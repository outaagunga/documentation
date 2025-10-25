
# PROJECT 2:- SECURITY  
> Data Fundamentals Project: Admin Roles & Security in Supabase  

---

# 🧩 **Part 1 — Database Setup & Enabling Row-Level Security**  

Set up your existing **E-commerce Database** (The database we created earlier in Data Tools project) in Supabase for role-based access and data security which is a PostgreSQL feature that restricts access to specific rows based on user identity.

---

## ⚙️ **1.2 Open Your Supabase Project**

1. Go to [https://app.supabase.com](https://app.supabase.com).
2. Open your **E-commerce** project.
3. In the **left sidebar**, click **Table Editor** to confirm your data is present.
4. You should see the same tables and sample rows from your previous project.

---

## 🧑‍💻 **1.5 Prepare for Roles and Authentication**

Before we can test “admin” and “user” access, we need **authenticated users**.

### Step-by-Step:

1. Go to **Authentication → Users** in your Supabase dashboard.
2. Click **Add User** → choose **“Invite user”** or **“Add manually”**.
3. Create two accounts:

   * **Admin User:** `admin@shop.com`
   * **Regular User:** `user@shop.com`

You can use your own email if needed, just make sure you note the roles.

---

## 🧩 **1.6 Simulate real user on supabase  
since supabase requires users to signup through the supabase platform, we are going to simulate real users who have signed up through supabase platform by:  

Creating a app_users table to store users and their roles  
Using gen_random_uuid() makes it behave just like Supabase’s Auth system, where every user gets a UUID automatically.  

```sql
CREATE TABLE app_users (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),  -- auto-generate UUIDs automatically for development use
  email text NOT NULL UNIQUE,
  role text NOT NULL DEFAULT 'user'
    CHECK (role IN ('admin', 'user', 'readonly')),
  created_at timestamptz DEFAULT now()
);


-- inserting sample data to the created app_users table  
INSERT INTO app_users (email, role)
VALUES
('outa.agunga@mail.admi.ac.ke', 'admin'),
('typingpool.astu@gmail.com', 'user'),
('alice@example.com', 'user'),
('brian@example.com', 'user'),
('cynthia@example.com', 'user'),
('david@example.com', 'user'),
('eva@example.com', 'user');

```
This is for development and testing purposes. If you want to deploy the project to realworld scenario- then change `REFERENCES app_users(id)` on the app_users to `REFERENCES auth.users(id)`.  


> 💡 To find your `auth.uid`, go to **Authentication → Users → Click a user → Copy UUID-> Paste It**.
> -- Example:
```sql
insert into users (id, email, role)
values
  ('<paste_admin_auth_uid>', 'admin@shop.com', 'admin'),
  ('<paste_user_auth_uid>', 'user@shop.com', 'user');
```


---
### 1. Create tables

```sql
-- 1️⃣ Create customers table
CREATE TABLE customers (
  customer_id SERIAL PRIMARY KEY,
  user_id uuid REFERENCES app_users(id) ON DELETE CASCADE,
  full_name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  phone VARCHAR(20),
  city VARCHAR(50),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);


-- 2️⃣ Create products table
CREATE TABLE IF NOT EXISTS products (
  product_id SERIAL PRIMARY KEY,
  product_name VARCHAR(100) NOT NULL,
  category VARCHAR(50),
  price NUMERIC(12,2) NOT NULL CHECK (price >= 0),
  stock_quantity INT DEFAULT 0 CHECK (stock_quantity >= 0),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- 3️⃣ Create orders table
CREATE TABLE orders (
  order_id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers(customer_id) ON DELETE CASCADE,
  product_id INT NOT NULL REFERENCES products(product_id) ON DELETE CASCADE,
  user_id uuid REFERENCES app_users(id) ON DELETE CASCADE,
  quantity INT NOT NULL CHECK (quantity > 0),
  total_amount NUMERIC(12,2),
  order_date TIMESTAMP WITH TIME ZONE DEFAULT now()
);

```
### Update total amount on orders table incase it didn't auto calculate  or
```sql
UPDATE orders AS o
SET total_amount = p.price * o.quantity
FROM products AS p
WHERE o.product_id = p.product_id
  AND o.total_amount IS NULL;
```

### 2. Trigger to auto-calc total_amount (best practice)

```sql
-- helper function
CREATE OR REPLACE FUNCTION calculate_order_total()
RETURNS TRIGGER AS $$
BEGIN
  -- calculate total using product price
  SELECT p.price * NEW.quantity INTO NEW.total_amount
  FROM products p
  WHERE p.product_id = NEW.product_id;

  -- set user_id to current authenticated uid if not provided
  IF NEW.user_id IS NULL THEN
    NEW.user_id := auth.uid();  -- supabase helper
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- trigger
CREATE TRIGGER trg_calc_order_total
BEFORE INSERT OR UPDATE ON orders
FOR EACH ROW
EXECUTE FUNCTION calculate_order_total();
```

### 3. Indexes & constraints (small optimizations)

```sql
-- indexes to speed lookups
CREATE INDEX IF NOT EXISTS idx_customers_user_id ON customers(user_id);
CREATE INDEX IF NOT EXISTS idx_orders_user_id ON orders(user_id);
CREATE INDEX IF NOT EXISTS idx_orders_customer_id ON orders(customer_id);
CREATE INDEX IF NOT EXISTS idx_orders_product_id ON orders(product_id);
```

### 4. Insert sample data

```sql
-- Customers
INSERT INTO customers (full_name, email, phone, city)
VALUES
('Alice Mwangi', 'alice@example.com', '0712345678', 'Nairobi'),
('Brian Otieno', 'brian@example.com', '0723456789', 'Mombasa'),
('Cynthia Njeri', 'cynthia@example.com', '0734567890', 'Kisumu'),
('David Kimani', 'david@example.com', '0745678901', 'Nakuru'),
('Eva Wambui', 'eva@example.com', '0756789012', 'Eldoret');

-- Products
INSERT INTO products (product_name, category, price, stock_quantity)
VALUES
('Wireless Mouse', 'Electronics', 1200.00, 50),
('Laptop Backpack', 'Accessories', 2500.00, 30),
('USB Flash Drive 64GB', 'Storage', 1500.00, 40),
('Bluetooth Speaker', 'Electronics', 4500.00, 20),
('Laptop Stand', 'Accessories', 3000.00, 25);

-- Orders (note total_amount will be auto-calculated by trigger)
INSERT INTO orders (customer_id, product_id, quantity)
VALUES
(1, 2, 1),
(2, 1, 2),
(3, 4, 1),
(4, 5, 1),
(5, 3, 3);
```
If you want to delete rows with a table, you can use:  
```sql
--e.g delete all rows on orders column  
TRUNCATE TABLE orders RESTART IDENTITY CASCADE;

```

> Note: Supabase also allows role in JWT claims. Keep both in sync if you use `app_users`.

### 6. Link customers table to auth.users
we are going to use `where email matches customer and user`  

```sql
UPDATE customers AS c
SET user_id = au.id
FROM app_users AS au
WHERE LOWER(TRIM(c.email)) = LOWER(TRIM(au.email))
  AND c.user_id IS NULL;
```
Then run this code to check if the `user_id` if filled or is null.  
```sql
SELECT *
FROM customers;
```

If null, run this code to check if there is matching data between the users table and custumers table  
```sql
-- Find which customers have matching auth.users
SELECT c.email AS customer_email,
       u.email AS auth_email,
       u.id AS auth_user_id
FROM customers c
LEFT JOIN auth.users u ON c.email = u.email;
```
or   
this to show any customers whose email isn’t in app_users.
```sql
SELECT c.email AS customer_email
FROM customers c
LEFT JOIN app_users au ON LOWER(TRIM(c.email)) = LOWER(TRIM(au.email))
WHERE au.id IS NULL;
```

If you get NULL under `auth_email`, then it means no there is no entry that matches `auth.users`.  
To fix this: create those users manually via `INSERT INTO auth.users (...)` e.g.:  

```sql
-- Create users to simulate as if they signed up on the supabase
INSERT INTO app_users (id, email, role)	
VALUES
  ('24f29f59-89c1-4f1d-97e9-554029e6a3f3', 'alice@example.com', 'user'),
  ('b5fa48df-c568-4d73-b42c-e19896d9cfa8', 'brian@example.com', 'user'),
  ('c7ca3a2a-d5de-43b2-95f5-5529b5c9cfe1', 'cynthia@example.com', 'user'),
  ('d9b5a214-12c8-4f52-8835-05f05e5b95af', 'david@example.com', 'user'),
  ('e1d2c897-5678-4eaa-94ad-77dcd97a7e2b', 'eva@example.com', 'user');

```
or  
insert from the customers table to the app_users  
```sql
INSERT INTO app_users (id, email, role)
SELECT u.id, u.email, 'user'
FROM auth.users u
WHERE u.email IN (
  SELECT email FROM customers
)
ON CONFLICT (id) DO NOTHING;
```

Try updating again to see if it works now  
```sql
UPDATE customers AS c
SET user_id = u.id
FROM auth.users AS u
WHERE LOWER(TRIM(c.email)) = LOWER(TRIM(u.email))
  AND c.user_id IS NULL;

-- LOWER(TRIM(...)) ensures that even if your customers.email has upper/lowercase or spaces, it still matches correctly.
```

Add a trigger so that next time if insert customers by email, the `user_id` does not display null  
```sql
CREATE OR REPLACE FUNCTION link_customer_to_user()
RETURNS TRIGGER AS $$
BEGIN
  UPDATE customers
  SET user_id = u.id
  FROM auth.users u
  WHERE LOWER(TRIM(customers.email)) = LOWER(TRIM(u.email))
  AND customers.customer_id = NEW.customer_id;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_link_customer_user
AFTER INSERT ON customers
FOR EACH ROW
EXECUTE FUNCTION link_customer_to_user();
```

---


## 🧾 **1.3 Enable Row Level Security (RLS)**

1. In the Supabase sidebar, go to **Database → Table Editor**.
2. Click on your first table, e.g. **customers**.
3. Go to the **“RLS” (Row Level Security)** tab.
4. Click **“Enable RLS”** for the table.

Repeat this for all your main tables  
or  
Use code:  
```sql
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
```
Check if RLS are successfully enabled using this code  

```sql
-- Check if RLS is enabled for all your tables
select schemaname, tablename, rowsecurity
from pg_tables
where schemaname = 'public';
```

✅ Output Example:

| schemaname | tablename | rowsecurity |
| ---------- | --------- | ----------- |
| public     | customers | true        |
| public     | products  | true        |
| public     | orders    | true        |

This means RLS is active for those tables.

---

```

> Note: Supabase also allows role in JWT claims. Keep both in sync if you use `app_users`.

### 6. Link customers table to auth.users
we are going to use `where email matches customer and user`  

```sql
UPDATE customers
SET user_id = u.id
FROM auth.users u
WHERE customers.email = u.email
  AND customers.user_id IS NULL;
```
Then run this code to check if the `user_id` if filled or is null.  
```sql
SELECT *
FROM customers;
```

If null, run this code to check if there is matching data between the users table and custumers table  
```sql
-- Find which customers have matching auth.users
SELECT c.email AS customer_email,
       u.email AS auth_email,
       u.id AS auth_user_id
FROM customers c
LEFT JOIN auth.users u ON c.email = u.email;
```
If you get NULL under `auth_email`, then it means no there is no entry that matches `auth.users`.  
To fix this: create those users manually via `INSERT INTO auth.users (...)` e.g.:  

```sql
-- Create users to simulate as if they signed up on the supabase
INSERT INTO users (id, email, role)	
VALUES
  ('24f29f59-89c1-4f1d-97e9-554029e6a3f3', 'alice@example.com', 'user'),
  ('b5fa48df-c568-4d73-b42c-e19896d9cfa8', 'brian@example.com', 'user'),
  ('c7ca3a2a-d5de-43b2-95f5-5529b5c9cfe1', 'cynthia@example.com', 'user'),
  ('d9b5a214-12c8-4f52-8835-05f05e5b95af', 'david@example.com', 'user'),
  ('e1d2c897-5678-4eaa-94ad-77dcd97a7e2b', 'eva@example.com', 'user'),

```
Try updating again to see if it works now  
```sql
UPDATE customers AS c
SET user_id = u.id
FROM auth.users AS u
WHERE LOWER(TRIM(c.email)) = LOWER(TRIM(u.email))
  AND c.user_id IS NULL;

-- LOWER(TRIM(...)) ensures that even if your customers.email has upper/lowercase or spaces, it still matches correctly.
```

Add a trigger so that next time if insert customers by email, the `user_id` does not display null  
```sql
CREATE OR REPLACE FUNCTION link_customer_to_user()
RETURNS TRIGGER AS $$
BEGIN
  UPDATE customers
  SET user_id = u.id
  FROM auth.users u
  WHERE LOWER(TRIM(customers.email)) = LOWER(TRIM(u.email))
  AND customers.customer_id = NEW.customer_id;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_link_customer_user
AFTER INSERT ON customers
FOR EACH ROW
EXECUTE FUNCTION link_customer_to_user();
```

---

# Part 2 — Security: enable RLS, grants, policy templates

> Run these steps **after** schema & sample data.

### 1. Revoke default public access & grant to `authenticated` (so RLS is enforced)

```sql
-- Example for one table; repeat for customers, products, orders
REVOKE ALL ON customers FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON customers TO authenticated;

REVOKE ALL ON products FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON products TO authenticated;

REVOKE ALL ON orders FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON orders TO authenticated;
```

### 2. Enable RLS on all tables

```sql
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
```

---

# Part 3 — Row Level Security Policies (complete set)

> Policies use `request.jwt.claims.role` to detect `admin`. Non-admin users are constrained to `auth.uid()`.

---

### Customers table policies

```sql
-- 1) Admin can view all customers
CREATE POLICY customers_admin_select ON customers
FOR SELECT
USING (request.jwt.claims.role = 'admin');

-- 2) Admin can insert/update/delete all customer rows
CREATE POLICY customers_admin_fullaccess ON customers
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- 3) Users can view only their profiles (customers.user_id == auth.uid())
CREATE POLICY customers_user_select_own ON customers
FOR SELECT
USING (user_id = auth.uid());

-- 4) Users can update their own profile
CREATE POLICY customers_user_update_own ON customers
FOR UPDATE
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());

-- 5) Users can insert a customer profile but only for themselves
CREATE POLICY customers_user_insert_own ON customers
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (user_id = auth.uid());

-- 6) Users can delete only their own profile (optionally allow or deny)
CREATE POLICY customers_user_delete_own ON customers
FOR DELETE
USING (user_id = auth.uid());
```

Notes:

* `USING` controls which rows are visible for SELECT/UPDATE/DELETE.
* `WITH CHECK` controls the conditions for INSERT or UPDATE values (prevents a user inserting a row for someone else).
* You can disallow DELETE for users if you prefer (omit the DELETE policy for users).

---

### Products table policies

For products you said users can view all products but only admins can edit all products; you also added "Users can edit their own profiles" — I’ll interpret that as "users cannot edit arbitrary products unless they are product owners" — but product ownership may not exist. Below are two common patterns; choose the one you want.

**Pattern A — Products are global resources (admins can edit; users can only view):**

```sql
-- Admin can select and modify products
CREATE POLICY products_admin_all ON products
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- Users can view all products
CREATE POLICY products_user_select_all ON products
FOR SELECT
USING (true);

-- Users cannot INSERT/UPDATE/DELETE products (unless admin)
-- (No policy for insert/update/delete for 'authenticated' users)
```

**Pattern B — Products can be owned by a user (products table must have owner_user_id)**
If you want user-owned products, add `owner_user_id uuid` to `products` and use similar policies to customers:

```sql
-- Example if products.owner_user_id exists:
ALTER TABLE products ADD COLUMN IF NOT EXISTS owner_user_id uuid REFERENCES auth.users(id);

CREATE POLICY products_user_update_own ON products
FOR UPDATE
USING (owner_user_id = auth.uid())
WITH CHECK (owner_user_id = auth.uid());

CREATE POLICY products_user_insert ON products
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (owner_user_id = auth.uid());

CREATE POLICY products_user_select_all ON products
FOR SELECT
USING (true); -- everyone can view products
```

(Use A if products are global/inventory items; use B if users manage their own product listings.)

---

### Orders table policies

```sql
-- 1) Admin can view & modify all orders
CREATE POLICY orders_admin_all ON orders
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- 2) Users can view only their orders (user_id == auth.uid())
CREATE POLICY orders_user_select_own ON orders
FOR SELECT
USING (user_id = auth.uid());

-- 3) Users can insert orders but only for themselves
CREATE POLICY orders_user_insert_own ON orders
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (user_id = auth.uid());

-- 4) Users can update their own orders (depending on allowed fields)
-- Example: allow update only if they are owner and order is not shipped (advance: add a status column)
CREATE POLICY orders_user_update_own ON orders
FOR UPDATE
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());

-- 5) Users can delete their own orders (optional)
CREATE POLICY orders_user_delete_own ON orders
FOR DELETE
USING (user_id = auth.uid());
```

Notes:

* If you need to allow updates only to certain columns (e.g., quantity), enforce it at application layer or add triggers to block disallowed field changes.
* You can add order statuses and restrict updates when `status = 'completed'` etc.

---

# Part 4 — Additional best practices & automation

1. **Validation & CHECK constraints**: keep `CHECK (quantity > 0)` and `CHECK (price >= 0)`. Done above.
2. **Audit trail**: consider `created_by`, `updated_by`, and an `audit` table or `updated_at` column with trigger to auto set `updated_at`.
3. **Service role usage**: only use `service_role` key server-side for admin tasks (it bypasses RLS). Never embed service_role in client apps.
4. **Minimize claims in JWT**: only include `role` and necessary claims; avoid sensitive data in JWT.
5. **Testing policies**: in Supabase SQL editor you can run policy tests by using the `'auth.uid'` or by using the Supabase “Policies” UI which lets you simulate JWT claims. Example test queries below.

---

# Part 5 — Test scenarios (copy/paste)

### Test as admin (simulate JWT claim in Supabase UI or use service_role on server)

```sql
-- Admin: view all orders
SELECT * FROM orders; -- should return all rows if admin

-- Admin: update any order total (works for admin)
UPDATE orders SET quantity = 99 WHERE order_id = 1;
```

### Test as normal user (client with anon key; JWT contains role='user' and auth.uid())

Simulate by running as a user via Supabase UI "Policies" simulator or through client SDK after logging in.

```sql
-- user should only see their own orders
SELECT * FROM orders WHERE user_id = auth.uid();  -- same as client SELECT * FROM orders;

-- user attempting to SELECT all rows will be blocked by RLS

-- user inserting an order (user_id will be set by trigger if not provided)
INSERT INTO orders (customer_id, product_id, quantity)
VALUES (1, 1, 1);

-- user attempting to insert order for another user_id will fail
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES (1, 1, 1, '24f29f59-89c1-4f1d-97e9-554029e6a3f3');  -- fails unless auth.uid() matches
```

---

# Part 6 — Common pitfalls & how to avoid them

1. **Policies but no grants**: If you forget to `GRANT ... TO authenticated`, RLS still applies and some operations may be denied. Always grant required privileges to `authenticated`.
2. **Forgetting to enable RLS**: enabling policies without `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` does nothing.
3. **Service Role leaks**: never expose your service_role key to clients — it bypasses RLS and is a huge security risk.
4. **JWT role not present**: ensure your authentication logic (e.g., custom claims) populates `role` claim or use `app_users` as a fallback check in policies (e.g., `EXISTS (SELECT 1 FROM app_users au WHERE au.id = auth.uid() AND au.role = 'admin')`). Example fallback:

```sql
-- admin check fallback if role claim missing
(request.jwt.claims.role = 'admin')
OR EXISTS (SELECT 1 FROM app_users au WHERE au.id = auth.uid() AND au.role = 'admin')
```

---

# Part 7 — Optional: example policy using app_users table (fallback)

```sql
CREATE POLICY customers_admin_select_with_appusers ON customers
FOR SELECT
USING (
  request.jwt.claims.role = 'admin'
  OR EXISTS (
    SELECT 1 FROM app_users au
    WHERE au.id = auth.uid() AND au.role = 'admin'
  )
);
```

---

# Part 8 — Summary checklist (copy/paste to run)

1. Create tables ✅
2. Create trigger for `total_amount` ✅
3. Insert sample data ✅
4. Revoke public & grant to `authenticated` ✅
5. Enable RLS on tables ✅
6. Add policies for customers/products/orders ✅
7. Test as admin and user ✅

---

If you want, I can:

* produce a single SQL file/SQL script that runs all of the above in order (safe to run once), or
* produce a version where `products` are user-owned (Pattern B) and include owner policies, or
* generate simple client SDK snippets (JavaScript / supabase-js) showing login and demo queries (simulate user vs admin).

Which of those would you like next?
