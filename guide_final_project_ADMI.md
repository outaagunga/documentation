
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
('Outa Agunga', 'outa.agunga@mail.admi.ac.ke', '0724907275', 'Nairobi'),
('Typing Pool', 'typingpool.astu@gmail.com', '0703645678', 'Machakos'),
('Alice Mwangi', 'alice@example.com', '0712345678', 'Turkana'),
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

Instead of manually inserting into `customers`, you can use a trigger to automatically create a customer entry when an `app_user` first places an order.  
```sql
CREATE OR REPLACE FUNCTION ensure_customer_exists()
RETURNS TRIGGER AS $$
DECLARE
  new_customer_id INT;
BEGIN
  -- check if customer already exists for this user_id
  SELECT customer_id INTO new_customer_id
  FROM customers
  WHERE user_id = NEW.user_id;

  -- if not found, create new customer profile from app_users
  IF new_customer_id IS NULL THEN
    INSERT INTO customers (user_id, full_name, email)
    SELECT id, split_part(email, '@', 1), email
    FROM app_users
    WHERE id = NEW.user_id
    RETURNING customer_id INTO new_customer_id;
  END IF;

  -- set customer_id on the order
  NEW.customer_id := new_customer_id;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_ensure_customer
BEFORE INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION ensure_customer_exists();

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
--Customers table
UPDATE customers AS c
SET user_id = au.id
FROM app_users AS au
WHERE LOWER(TRIM(c.email)) = LOWER(TRIM(au.email))
  AND c.user_id IS NULL;

--Orders table
UPDATE orders AS o
SET user_id = c.user_id
FROM customers AS c
WHERE o.customer_id = c.customer_id
  AND o.user_id IS NULL;

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
INSERT INTO app_users (email, role)	
VALUES
  ('alice@example.com', 'user'),
  ('brian@example.com', 'user'),
  ('cynthia@example.com', 'user'),
  ('david@example.com', 'user'),
  ('eva@example.com', 'user');

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

Excellent — this is a **very strong foundation** for professional-grade RLS implementation in Supabase 👏.
Your structure is already clear and accurate — we just need to **refine the tone, flow, and order of steps** to sound like a **polished technical guide** (production-oriented, not just lab notes).

Here’s your improved and *professionally fine-tuned* version 👇

---

# 🧾 **1.3 Implementing Row Level Security (RLS) in Supabase**

Row Level Security (RLS) ensures each user only accesses rows they are authorized to see or modify.
It’s a **core security feature** in Supabase and should always be enabled for production databases.

---

## **Step 1 — Enable RLS on All Application Tables**

You can enable RLS via the Supabase dashboard:

1. In the sidebar, navigate to **Database → Table Editor**.
2. Select a table (e.g., `customers`).
3. Open the **“RLS”** tab.
4. Click **“Enable RLS”**.

Or enable it directly using SQL:

```sql
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
```

✅ **Verify RLS Status:**

```sql
SELECT schemaname, tablename, rowsecurity
FROM pg_tables
WHERE schemaname = 'public';
```

**Expected Output Example:**

| schemaname | tablename | rowsecurity |
| ---------- | --------- | ----------- |
| public     | customers | true        |
| public     | products  | true        |
| public     | orders    | true        |

If `rowsecurity` is `true`, RLS is active.

> 💡 **Note:**
> If you are using a custom `app_users` table, ensure its roles (e.g., `admin`, `user`) match the roles in your JWT claims for consistent policy behavior.

---

## **Step 2 — Revoke Default Public Access**

Supabase allows “public” access by default. To ensure RLS rules apply correctly, revoke all default privileges from `PUBLIC` and explicitly grant them to the `authenticated` role:

```sql
-- Customers
REVOKE ALL ON customers FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON customers TO authenticated;

-- Products
REVOKE ALL ON products FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON products TO authenticated;

-- Orders
REVOKE ALL ON orders FROM PUBLIC;
GRANT SELECT, INSERT, UPDATE, DELETE ON orders TO authenticated;
```

> ✅ This ensures that only logged-in users (those with the `authenticated` role) can access data — subject to your RLS policies.

---

## **Step 3 — Define Policies**

Policies define **who can access which rows** and **under what conditions**.
The following examples assume JWT claims include a `role` (e.g., `'admin'` or `'user'`), and `auth.uid()` identifies the current user.

---

### 🧍‍♂️ **Customers Table Policies**

```sql
-- 1️⃣ Admin: full visibility and control
CREATE POLICY customers_admin_all ON customers
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- 2️⃣ Users: view only their own customer profile
CREATE POLICY customers_user_select_own ON customers
FOR SELECT
USING (user_id = auth.uid());

-- 3️⃣ Users: insert only their own record
CREATE POLICY customers_user_insert_own ON customers
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (user_id = auth.uid());

-- 4️⃣ Users: update their own record
CREATE POLICY customers_user_update_own ON customers
FOR UPDATE
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());

-- 5️⃣ Users: (optional) delete only their own profile
CREATE POLICY customers_user_delete_own ON customers
FOR DELETE
USING (user_id = auth.uid());
```

**Explanation:**

* `USING` defines which rows are visible for SELECT, UPDATE, DELETE.
* `WITH CHECK` controls which rows the user is allowed to create or modify.
* Admins override all restrictions based on their role.

---

### 📦 **Products Table Policies**

#### **Pattern A: Shared Product Catalog (most common)**

```sql
-- Admin can manage all products
CREATE POLICY products_admin_all ON products
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- Regular users can view all products
CREATE POLICY products_user_select_all ON products
FOR SELECT
USING (true);
```

Users can browse but cannot modify products.

---

#### **Pattern B: User-Owned Products (optional)**

If you want users to manage their own listings, first add ownership:

```sql
ALTER TABLE products ADD COLUMN IF NOT EXISTS owner_user_id uuid REFERENCES auth.users(id);
```

Then define:

```sql
CREATE POLICY products_user_insert ON products
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (owner_user_id = auth.uid());

CREATE POLICY products_user_update_own ON products
FOR UPDATE
USING (owner_user_id = auth.uid())
WITH CHECK (owner_user_id = auth.uid());

CREATE POLICY products_user_select_all ON products
FOR SELECT
USING (true);
```

---

### 🧾 **Orders Table Policies**

```sql
-- 1️⃣ Admin: full access
CREATE POLICY orders_admin_all ON orders
FOR ALL
USING (request.jwt.claims.role = 'admin')
WITH CHECK (request.jwt.claims.role = 'admin');

-- 2️⃣ Users: view only their own orders
CREATE POLICY orders_user_select_own ON orders
FOR SELECT
USING (user_id = auth.uid());

-- 3️⃣ Users: insert orders only for themselves
CREATE POLICY orders_user_insert_own ON orders
FOR INSERT
USING (auth.uid() IS NOT NULL)
WITH CHECK (user_id = auth.uid());

-- 4️⃣ Users: update their own orders
CREATE POLICY orders_user_update_own ON orders
FOR UPDATE
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());

-- 5️⃣ Users: (optional) delete their own orders
CREATE POLICY orders_user_delete_own ON orders
FOR DELETE
USING (user_id = auth.uid());
```

> 💡 You can later restrict updates based on order status (e.g., prevent editing after shipment) using additional conditions.

---

**List all policies you've created**  
```sql
SELECT tablename, policyname, permissive, roles, cmd
FROM pg_policies
WHERE schemaname = 'public';
```

## **Step 4 — Test and Validate Policies**

### 🧑‍💼 Test as Admin

```sql
-- Should see all orders
SELECT * FROM orders;

-- Should be able to modify any record
UPDATE orders SET quantity = 99 WHERE order_id = 1;
```

### 👤 Test as User

Simulate a logged-in user via the Supabase Policies UI or with the SDK:

```sql
-- Should only see their own orders
SELECT * FROM orders;

-- Should be allowed to insert orders for themselves
INSERT INTO orders (customer_id, product_id, quantity)
VALUES (1, 1, 1);

-- Should fail if trying to use another user's ID
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES (1, 1, 1, 'fake-uuid');  -- ❌ denied
```

---

## **Step 5 — Best Practices**

1. ✅ **Use CHECK constraints** for data integrity (`price >= 0`, `quantity > 0`).
2. 🕒 **Add audit columns** like `created_by`, `updated_at` for traceability.
3. 🔒 **Use service_role key only server-side** (it bypasses RLS).
4. 🎯 **Minimize JWT claims** — keep only `role`, `sub`, and necessary fields.
5. 🧪 **Test policies regularly** using Supabase’s Policy Simulator or `auth.uid()` queries.

---

## **Step 6 — Common Pitfalls**

| Mistake                      | Description                                   | Fix                                         |
| ---------------------------- | --------------------------------------------- | ------------------------------------------- |
| ❌ Forgot to enable RLS       | Policies won’t apply                          | `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` |
| ❌ Forgot to grant privileges | Authenticated users can’t access data         | Use `GRANT ... TO authenticated`            |
| ❌ Exposed service_role key   | Full bypass of RLS (critical risk)            | Never include in client apps                |
| ❌ Missing JWT role           | Policies using `request.jwt.claims.role` fail | Use fallback with `app_users` lookup        |

**Example Fallback Check:**

```sql
(request.jwt.claims.role = 'admin')
OR EXISTS (
  SELECT 1 FROM app_users au
  WHERE au.id = auth.uid() AND au.role = 'admin'
)
```

---

## ✅ **RLS Implementation Checklist**

| Task                           | Status |
| ------------------------------ | ------ |
| Create tables                  | ✅      |
| Add triggers & constraints     | ✅      |
| Insert seed data               | ✅      |
| Revoke public privileges       | ✅      |
| Enable RLS                     | ✅      |
| Create policies for all tables | ✅      |
| Test as admin and user         | ✅      |

---

