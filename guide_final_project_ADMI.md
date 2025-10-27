
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

Or you can force it, e.g  
```sql
ALTER TABLE customers FORCE ROW LEVEL SECURITY;
ALTER TABLE orders FORCE ROW LEVEL SECURITY;
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
USING (
  EXISTS (
    SELECT 1 FROM app_users au
    WHERE au.id = current_app_user_id()
      AND au.role = 'admin'
  )
)
WITH CHECK (
  EXISTS (
    SELECT 1 FROM app_users au
    WHERE au.id = current_app_user_id()
      AND au.role = 'admin'
  )
);

-- 2️⃣ Users: view only their own profile
CREATE POLICY customers_user_select_own ON customers
FOR SELECT
USING (
  user_id = current_app_user_id()
);

-- 3️⃣ Users: insert only their own record
CREATE POLICY customers_user_insert_own ON customers
FOR INSERT
WITH CHECK (
  user_id = current_app_user_id()
);

-- 4️⃣ Users: update their own record
CREATE POLICY customers_user_update_own ON customers
FOR UPDATE
USING (
  user_id = current_app_user_id()
)
WITH CHECK (
  user_id = current_app_user_id()
);

-- 5️⃣ Users: delete their own profile
CREATE POLICY customers_user_delete_own ON customers
FOR DELETE
USING (
  user_id = current_app_user_id()
);



```

**Explanation:**

* `USING` defines which rows are visible for SELECT, UPDATE, DELETE.
* Admins override all restrictions based on their role.

---

### 📦 **Products Table Policies**

#### **Pattern A: Shared Product Catalog (most common)**

```sql
-- Admin can manage all products
CREATE POLICY products_admin_all ON products
FOR ALL
USING (
  EXISTS (SELECT 1 FROM app_users au WHERE au.role = 'admin')
)
WITH CHECK (
  EXISTS (SELECT 1 FROM app_users au WHERE au.role = 'admin')
);

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
USING (
  EXISTS (
    SELECT 1 FROM app_users au
    WHERE au.id = current_app_user_id()
      AND au.role = 'admin'
  )
)
WITH CHECK (
  EXISTS (
    SELECT 1 FROM app_users au
    WHERE au.id = current_app_user_id()
      AND au.role = 'admin'
  )
);

-- 2️⃣ Users: view only their own orders
CREATE POLICY orders_user_select_own ON orders
FOR SELECT
USING (
  user_id = current_app_user_id()
);

-- 3️⃣ Users: insert orders only for themselves
CREATE POLICY orders_user_insert_own ON orders
FOR INSERT
WITH CHECK (
  user_id = current_app_user_id()
);

-- 4️⃣ Users: update their own orders
CREATE POLICY orders_user_update_own ON orders
FOR UPDATE
USING (
  user_id = current_app_user_id()
)
WITH CHECK (
  user_id = current_app_user_id()
);

-- 5️⃣ Users: delete their own orders
CREATE POLICY orders_user_delete_own ON orders
FOR DELETE
USING (
  user_id = current_app_user_id()
);


```

> 💡 You can later restrict updates based on order status (e.g., prevent editing after shipment) using additional conditions.

## NB//:
**OPTIONAL — ROLE-BASED SHORTCUT**
If you want to centralize the admin check and make your policies cleaner,
you can define a view or a helper function:

```sql
CREATE OR REPLACE FUNCTION is_admin(uid uuid)
RETURNS boolean AS $$
BEGIN
  RETURN EXISTS (SELECT 1 FROM app_users WHERE id = uid AND role = 'admin');
END;
$$ LANGUAGE plpgsql STABLE;
```

Then your policies become shorter and more readable:

```sql
CREATE POLICY orders_admin_all ON orders
FOR ALL
USING (is_admin(orders.user_id))
WITH CHECK (is_admin(orders.user_id));
```
---

**List all policies you've created**  
```sql
SELECT tablename, policyname, permissive, roles, cmd
FROM pg_policies
WHERE schemaname = 'public';
```
To check if the current user is bypassing the the session roles on RLS policy 
```sql
--replace customers, orders with your actaul table names 
SELECT tablename, policyname, permissive, roles, cmd, qual
FROM pg_policies
WHERE tablename IN ('customers', 'orders');
```

WHEN YOU LATER ADD REAL SUPABASE AUTH users, you will simply change:  

```
EXISTS (SELECT 1 FROM app_users au WHERE au.id = customers.user_id)
```
⬅️ to:
```
user_id = auth.uid()
```
And:
```
EXISTS (SELECT 1 FROM app_users au WHERE au.role = 'admin')
```
⬅️ to:
```
request.jwt.claims.role = 'admin'
```

## **Step 4 — Test and Validate Policies**
Test script** you can run in your **Supabase SQL editor** to verify your **RLS policies** for `customers`, `orders`, and `products`.

It includes setup, simulated users, and expected behavior explanations so you can confirm everything works correctly — even when using your **`app_users`** table instead of real Supabase Auth.

---

# 🧪 **RLS POLICY TEST SCRIPT**

> ✅ Use this only after you’ve enabled RLS and created all the policies from your current setup.

---

## **1️⃣ — Check that RLS is Enabled**

```sql
SELECT tablename, rowsecurity
FROM pg_tables
WHERE schemaname = 'public'
  AND tablename IN ('customers', 'orders', 'products');
```

**Expected:**
`rowsecurity` = `true` for all three tables.

---

## **2️⃣ — Prepare Mock Data**

We’ll simulate a few users and sample data to test policies.

```sql
-- 👥 Create 2 fake app users (acting as Supabase Auth substitutes)
INSERT INTO app_users (id, email, role)
VALUES
  ('11111111-1111-1111-1111-111111111111', 'admin@example.com', 'admin'),
  ('22222222-2222-2222-2222-222222222222', 'user1@example.com', 'user'),
  ('33333333-3333-3333-3333-333333333333', 'user2@example.com', 'user')
ON CONFLICT (id) DO NOTHING;

-- 🧍‍♂️ Customers (linked to app_users)
INSERT INTO customers (full_name, email, user_id)
VALUES
  ('Admin Tester', 'admin@example.com', '11111111-1111-1111-1111-111111111111'),
  ('John User', 'user1@example.com', '22222222-2222-2222-2222-222222222222'),
  ('Mary User', 'user2@example.com', '33333333-3333-3333-3333-333333333333')
ON CONFLICT DO NOTHING;

-- 📦 Products
INSERT INTO products (product_name, category, price)
VALUES
  ('Laptop', 'Electronics', 85000),
  ('Shoes', 'Fashion', 2500),
  ('Book', 'Education', 1200)
ON CONFLICT DO NOTHING;

-- 🧾 Orders
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES
  (1, 1, 1, '22222222-2222-2222-2222-222222222222'),
  (2, 2, 2, '33333333-3333-3333-3333-333333333333');
```

---

## **3️⃣ — Simulate Different Users (Test RLS Behavior)**

### ⚙️ Setup Helper Function (to test as specific users)

```sql
-- Create a helper to simulate logged-in user
CREATE OR REPLACE FUNCTION current_app_user_id()
RETURNS uuid AS $$
  SELECT current_setting('app.current_user', true)::uuid;
$$ LANGUAGE sql STABLE;

-- Helper to set the simulated user id
CREATE OR REPLACE FUNCTION set_app_user(uid uuid)
RETURNS void AS $$
BEGIN
  PERFORM set_config('app.current_user', uid::text, false);
END;
$$ LANGUAGE plpgsql;

```

---

## **4️⃣ — Test as Admin**

```sql
-- 🧑‍💼 Simulate admin login
SELECT set_app_user('11111111-1111-1111-1111-111111111111');

-- ✅ Admin should see all customers
SELECT * FROM customers;

-- ✅ Admin should see all orders
SELECT * FROM orders;

-- ✅ Admin can update anyone’s order
UPDATE orders
SET quantity = 10
WHERE order_id = 1
RETURNING *;

-- ✅ Admin can insert new products
INSERT INTO products (product_name, category, price)
VALUES ('Tablet', 'Electronics', 40000)
RETURNING *;


```

**Expected:**
Admin can **see and modify everything**.

---

## **5️⃣ — Test as Regular User (User1)**

```sql
-- 👤 Simulate user1 login
SELECT set_app_user('22222222-2222-2222-2222-222222222222');

-- ✅ Should only see their own customer record
SELECT * FROM customers;

-- ✅ Should only see their own orders
SELECT * FROM orders;

-- ✅ Can create their own order
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES (1, 3, 1, '22222222-2222-2222-2222-222222222222')
RETURNING *;

-- ❌ Should NOT see other users' orders
SELECT * FROM orders
WHERE user_id = '33333333-3333-3333-3333-333333333333';

-- ❌ Should NOT update another user’s order
UPDATE orders
SET quantity = 5
WHERE user_id = '33333333-3333-3333-3333-333333333333';


```

**Expected:**

* User1 only sees and edits their own data.
* Other users’ rows are invisible (RLS hides them completely).
* Unauthorized updates will **fail silently** with a permissions error.

---

## **6️⃣ — Test as Second User (User2)**

```sql
-- 👤 Simulate user2 login
SELECT set_app_user('33333333-3333-3333-3333-333333333333');

-- ✅ Should only see their own record in customers
SELECT * FROM customers;

-- ✅ Should see only their own order
SELECT * FROM orders;

-- ❌ Should not see or update User1’s order
UPDATE orders
SET quantity = 99
WHERE user_id = '22222222-2222-2222-2222-222222222222';



```

---

## **7️⃣ — Check that Products Are Shared (Viewable by All)**

```sql
-- 👤 Still as User2
SELECT * FROM products;

-- ❌ Try to modify a product (should fail)
UPDATE products SET price = 99999 WHERE product_id = 1;

-- ❌ Try to insert a new product (should fail)
INSERT INTO products (product_name, category, price)
VALUES ('Fake Product', 'Test', 100);

```

**Expected:**
All products are visible, but **only admin** can modify or insert them.

---

## **8️⃣ — Check which policies are being enforced**

```sql
SELECT tablename, policyname, cmd, roles, permissive
FROM pg_policies
WHERE schemaname = 'public'
ORDER BY tablename;
```

**Expected:**
All `customers`, `orders`, and `products` policies appear.

---

## **9️⃣ — Clean Up (Optional)**

Check the currently active simulated user
```sql
SELECT current_app_user_id();
```

When you’re done testing:

```sql
DROP FUNCTION IF EXISTS set_local_user;
DROP FUNCTION IF EXISTS set_local_user(uuid);
DROP FUNCTION IF EXISTS current_local_user_id();

```

If you don't know which role this user has (admin/user/etc.), you can also:
```sql
-- Create a debug view for developers/testers
CREATE OR REPLACE VIEW debug_current_user AS
SELECT
  current_app_user_id() AS simulated_user_id,
  au.full_name AS simulated_user_name,
  au.role AS simulated_user_role,
  au.email AS simulated_user_email
FROM app_users au
WHERE au.id = current_app_user_id();
```
Now anyone can simply type:
```
SELECT * FROM debug_current_user;
```
This shows the full record for the currently active simulated user — including their role and email.

---

# ✅ **Expected Outcomes Summary**

| Test Case                                   | Expected Result   |
| ------------------------------------------- | ----------------- |
| Admin selects all tables                    | ✅ Sees everything |
| Admin inserts/updates/deletes               | ✅ Works           |
| User1 sees only their data                  | ✅ Works           |
| User2 sees only their data                  | ✅ Works           |
| Regular users see all products              | ✅ Works           |
| Regular users cannot insert/update products | ✅ Denied          |
| Invalid updates across users                | ❌ Blocked by RLS  |

---
## When test script fails  

When you run SQL queries directly in the **Supabase SQL Editor** or **psql**, you are using the **service role**, which **bypasses all RLS policies** automatically.

That means:
* All policies are **ignored** in this mode.
* You’ll never see any “denied” behavior, because the service role has superuser-level permissions.


### 🧠 2️⃣ How to Test RLS Properly
**Create a temporary debug helper** `(app_users mode)`

Since you’re not using real Supabase Auth yet, you can **simulate `auth.uid()`** by creating a custom SQL function that makes RLS policies session-aware.

```sql
-- Create a helper to simulate logged-in user
CREATE OR REPLACE FUNCTION current_app_user_id()
RETURNS uuid AS $$
  SELECT current_setting('app.current_user', true)::uuid;
$$ LANGUAGE sql STABLE;

-- Helper to set the simulated user id
CREATE OR REPLACE FUNCTION set_app_user(uid uuid)
RETURNS void AS $$
BEGIN
  PERFORM set_config('app.current_user', uid::text, false);
END;
$$ LANGUAGE plpgsql;
```

Then update your policies to use that:

```sql
EXISTS (
  SELECT 1 FROM app_users au
  WHERE au.id = current_app_user_id()
  AND au.id = orders.user_id
)
```

Now you can run tests like this:

```sql
-- Simulate user login
SELECT set_app_user('22222222-2222-2222-2222-222222222222');

-- Try RLS-protected operations
SELECT * FROM orders;
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES (1, 2, 1, '33333333-3333-3333-3333-333333333333');  -- should fail
```

✅ **This works!** Because `current_app_user_id()` changes per session — RLS now enforces ownership rules even in direct SQL.


---
# Incase of real supabase users - No simulation - Real world project 

You want your custom `app_users.id` to **match** the real Supabase Auth user’s `id` (UUID).  

```sql
CREATE TABLE app_users (
  id uuid PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email text NOT NULL UNIQUE,
  role text NOT NULL DEFAULT 'user'
    CHECK (role IN ('admin', 'user', 'readonly')),
  created_at timestamptz DEFAULT now()
);
```

🔹 `REFERENCES auth.users(id)` — links your app user record to the real Supabase Auth user.
🔹 `ON DELETE CASCADE` — ensures if a user is deleted from Auth, their record here is also removed.

---
If you want to auto-create `app_users` entries when someone signs up via Supabase Auth, use this trigger:

```sql
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS trigger AS $$
BEGIN
  INSERT INTO app_users (id, email)
  VALUES (NEW.id, NEW.email);
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
AFTER INSERT ON auth.users
FOR EACH ROW
EXECUTE FUNCTION handle_new_user();
```

Now every new signup will automatically create an `app_users` record.  


---
**Manually insert users directly via SQL for testing**
Step 1- Disable verification temporarily  

1️⃣ Go to your **Supabase Dashboard → Authentication → Providers → Email**
2️⃣ Toggle **“Enable Email Confirmations”** → **OFF**

> This means new signups via API or dashboard don’t need to confirm their email before being active.

3️⃣ You can now use the Supabase SQL Editor or Auth API to create users manually.

Example via SQL:

```sql
INSERT INTO auth.users (id, email, encrypted_password)
VALUES (
  '22222222-2222-2222-2222-222222222222',
  'typingpool.astu@gmail.com',
  crypt('test1234', gen_salt('bf'))
);
```

## 🔁 **Re-enable email verification after testing**

Once you finish your RLS or data setup tests:

1️⃣ Go back to `Authentication → Providers → Email`
2️⃣ Re-enable **“Email Confirmations”**

That ensures your production signup flow is secure again.
---

### **2️⃣ Get real UUIDs from Supabase Auth**

Run this query to see your real Supabase user IDs (the ones used by `auth.uid()`):

```sql
SELECT id, email FROM auth.users;
```

You’ll see output like:

| id                                   | email                                                             |
| ------------------------------------ | ----------------------------------------------------------------- |
| 11111111-1111-1111-1111-111111111111 | [outa.agunga@mail.admi.ac.ke](mailto:outa.agunga@mail.admi.ac.ke) |
| 22222222-2222-2222-2222-222222222222 | [typingpool.astu@gmail.com](mailto:typingpool.astu@gmail.com)     |
| ...                                  | ...                                                               |

---

### **3️⃣ The trigger you created previosly above inserts the users into `app_users`. if it fails, then Insert them into `app_users` manually**


```sql
INSERT INTO app_users (id, email, role)
VALUES
('11111111-1111-1111-1111-111111111111', 'outa.agunga@mail.admi.ac.ke', 'admin'),
('22222222-2222-2222-2222-222222222222', 'typingpool.astu@gmail.com', 'user'),
('33333333-3333-3333-3333-333333333333', 'alice@example.com', 'user'),
('44444444-4444-4444-4444-444444444444', 'brian@example.com', 'user');
```

---

### **4️⃣ Optional — automated sync trigger**

If you want to auto-create `app_users` entries when someone signs up via Supabase Auth, use this trigger:

```sql
CREATE OR REPLACE FUNCTION handle_new_user()
RETURNS trigger AS $$
BEGIN
  INSERT INTO app_users (id, email)
  VALUES (NEW.id, NEW.email);
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
AFTER INSERT ON auth.users
FOR EACH ROW
EXECUTE FUNCTION handle_new_user();
```

Now every new signup will automatically create an `app_users` record.  

## Create other tables
> paste your tables schema here   

---
###👍 — since you manually inserted that test user directly into the **`auth.users`** table

You can delete them after running your tests.   
if any **foreign key constraints** exist (for example, `app_users.id → auth.users.id`), you’ll need to delete related rows first.

```sql
-- 1️⃣ Delete linked record in app_users first (if exists)
DELETE FROM app_users
WHERE id = '22222222-2222-2222-2222-222222222222';

-- 2️⃣ Then delete from auth.users
DELETE FROM auth.users
WHERE id = '22222222-2222-2222-2222-222222222222';
```
---


---
## 🔒 **2️⃣ Enable RLS on your data tables**

You’ll usually have tables like `customers`, `orders`, etc.
Turn RLS on for each one:

```sql
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

```

## 🧩 **3️⃣ Create helper function to check user role**

This makes your policies cleaner:

```sql
CREATE OR REPLACE FUNCTION auth_role()
RETURNS text AS $$
  SELECT role FROM app_users WHERE id = auth.uid();
$$ LANGUAGE sql SECURITY DEFINER;
```

✅ This lets you call `auth_role()` anywhere inside RLS policies.

---

## 🧠 **4️⃣ Define RLS policies**

**Policy on customers table**
```sql
-- ✅ 1️⃣ Admin: full visibility and control
CREATE POLICY customers_admin_all
ON customers
FOR ALL
USING (
  auth_role() = 'admin'
)
WITH CHECK (
  auth_role() = 'admin'
);

-- ✅ 2️⃣ Users: view only their own records
CREATE POLICY customers_user_select_own
ON customers
FOR SELECT
USING (
  user_id = auth.uid()
);

-- ✅ 3️⃣ Users: insert only their own records
CREATE POLICY customers_user_insert_own
ON customers
FOR INSERT
WITH CHECK (
  user_id = auth.uid()
);

-- ✅ 4️⃣ Users: update only their own records
CREATE POLICY customers_user_update_own
ON customers
FOR UPDATE
USING (
  user_id = auth.uid()
)
WITH CHECK (
  user_id = auth.uid()
);

-- ✅ 5️⃣ Users: delete only their own records
CREATE POLICY customers_user_delete_own
ON customers
FOR DELETE
USING (
  user_id = auth.uid()
);
```

**Policy on orders tables**
```sql
-- ✅ 1️⃣ Admin: full access to all orders
CREATE POLICY orders_admin_all
ON orders
FOR ALL
USING (
  auth_role() = 'admin'
)
WITH CHECK (
  auth_role() = 'admin'
);

-- ✅ 2️⃣ Users: view only their own orders
CREATE POLICY orders_user_select_own
ON orders
FOR SELECT
USING (
  user_id = auth.uid()
);

-- ✅ 3️⃣ Users: insert orders only for themselves
CREATE POLICY orders_user_insert_own
ON orders
FOR INSERT
WITH CHECK (
  user_id = auth.uid()
);

-- ✅ 4️⃣ Users: update only their own orders
CREATE POLICY orders_user_update_own
ON orders
FOR UPDATE
USING (
  user_id = auth.uid()
)
WITH CHECK (
  user_id = auth.uid()
);

-- ✅ 5️⃣ Users: delete only their own orders
CREATE POLICY orders_user_delete_own
ON orders
FOR DELETE
USING (
  user_id = auth.uid()
);
```

---

### 🧰 **Test manually (simulate user in SQL Editor)**

**Test as Admin**
```sql
-- 🧑‍💼 Simulate admin login (sets the current authenticated user)
SELECT set_config('request.jwt.claim.sub', '11111111-1111-1111-1111-111111111111', true);

-- 🧾 Check current simulated user and role
SELECT auth.uid() AS current_user_id, auth_role() AS current_user_role;

-- ✅ Admin should see all customers
SELECT * FROM customers;

-- ✅ Admin should see all orders
SELECT * FROM orders;

-- ✅ Admin can update anyone’s order
UPDATE orders
SET quantity = 10
WHERE order_id = 1
RETURNING *;

-- ✅ Admin can insert new products
INSERT INTO products (product_name, category, price)
VALUES ('Tablet', 'Electronics', 40000)
RETURNING *;
```
**Test as regular user**
```sql
-- 👤 Simulate user1 login (real Supabase Auth UUID)
SELECT set_config('request.jwt.claim.sub', '22222222-2222-2222-2222-222222222222', true);

-- 🧾 Verify the simulated user context
SELECT auth.uid() AS current_user_id, auth_role() AS current_user_role;

-- ✅ Should only see their own customer record
SELECT * FROM customers;

-- ✅ Should only see their own orders
SELECT * FROM orders;

-- ✅ Can create their own order
INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES (
  1,               -- must belong to this user's customer_id
  3,               -- any valid product_id
  1,               -- quantity
  auth.uid()       -- ensures correct ownership under RLS
)
RETURNING *;

-- ❌ Should NOT see other users' orders (should return zero rows)
SELECT * FROM orders
WHERE user_id = '33333333-3333-3333-3333-333333333333';

-- ❌ Should NOT update another user’s order (should be denied)
UPDATE orders
SET quantity = 5
WHERE user_id = '33333333-3333-3333-3333-333333333333';

```

---

## 🧭 **6️⃣ Optional — make admin bypass RLS**

If you want admins to skip all restrictions, use a global policy check:

```sql
ALTER TABLE customers FORCE ROW LEVEL SECURITY;

CREATE POLICY "Admins bypass all restrictions"
ON customers
USING (auth_role() = 'admin');
```
Then place this first (Supabase evaluates policies in OR logic).

---

# Incase of real supabase - real world scenario option 3 mar adek

Goal → Mirror the Supabase Auth user ID in your own `app_users` table, auto-sync on signup, and apply secure Row-Level Security (RLS) policies.

---

## ⚙️ **1️⃣ Create `app_users` Table**

You’ll use the same UUIDs as in `auth.users`.
Skip the direct foreign-key constraint (since the `auth` schema is protected).

```sql
CREATE TABLE public.app_users (
  id uuid PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email text NOT NULL UNIQUE,
  role text NOT NULL DEFAULT 'user'
    CHECK (role IN ('admin', 'user', 'readonly')),
  created_at timestamptz DEFAULT now()
);
```

✅ **Why:** avoids `permission denied for schema auth` while keeping a perfect ID match with Auth.

---

## 🔁 **2️⃣ Auto-Create `app_users` When New User Signs Up**

Define a trigger function inside the `auth` schema that inserts a linked record in `public.app_users`.

```sql
CREATE OR REPLACE FUNCTION auth.handle_new_user()
RETURNS trigger AS $$
BEGIN
  INSERT INTO public.app_users (id, email)
  VALUES (NEW.id, NEW.email)
  ON CONFLICT (id) DO NOTHING;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER
SET search_path = public, auth;

CREATE TRIGGER on_auth_user_created
AFTER INSERT ON auth.users
FOR EACH ROW
EXECUTE FUNCTION auth.handle_new_user();
```

✅ **`SET search_path`:** ensures proper schema resolution.

---

## 🧪 **3️⃣ Create your test Users data**

Run as the database owner in SQL Editor:

```sql
SELECT auth.create_user(
  email := 'admin@example.com',
  password := 'Admin123!',
  email_confirmed := true
);

SELECT auth.create_user(
  email := 'user@example.com',
  password := 'User123!',
  email_confirmed := true
);
```

### Each call creates an `auth.users` record → triggers `app_users` auto-insert.

Confirm:

```sql
SELECT id, email FROM auth.users;
SELECT * FROM app_users;
```

---

## 🧩 **4️⃣ Create Helper Function `auth_role()`**

This returns the current user’s role (with fallback).

```sql
CREATE OR REPLACE FUNCTION public.auth_role()
RETURNS text AS $$
  SELECT COALESCE(
    (SELECT role FROM public.app_users WHERE id = auth.uid()),
    'readonly'
  );
$$ LANGUAGE sql SECURITY DEFINER;
```

✅ Returns `'readonly'` if no match in `app_users`.

---

## 🧱 **5️⃣ Create Your Data Tables**

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
---
### Run this trigger to populate `user_id` when inserting data into out created tables.

```sql
CREATE OR REPLACE FUNCTION set_user_id()
RETURNS trigger AS $$
BEGIN
  IF NEW.user_id IS NULL THEN
    NEW.user_id := auth.uid();
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER set_customer_user
BEFORE INSERT ON customers
FOR EACH ROW
EXECUTE FUNCTION set_user_id();

CREATE TRIGGER set_order_user
BEFORE INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION set_user_id();
```
---
### Trigger to autocalculate total amount on orders
```sql
CREATE OR REPLACE FUNCTION calculate_total_amount()
RETURNS trigger AS $$
BEGIN
  NEW.total_amount := (SELECT price FROM products WHERE product_id = NEW.product_id) * NEW.quantity;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER before_order_insert
BEFORE INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION calculate_total_amount();

```
---
### Insert your data now into the tables

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
---

## 🔒 **6️⃣ Enable RLS**

```sql
ALTER TABLE public.customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.orders ENABLE ROW LEVEL SECURITY;
```

---

## 📜 **7️⃣ Define Simplified RLS Policies**

### 🧍 For `customers` table

```sql
-- Admins: full access
CREATE POLICY customers_admin_all
ON public.customers
FOR ALL
USING (auth_role() = 'admin')
WITH CHECK (auth_role() = 'admin');

-- Regular users: manage only own records
CREATE POLICY customers_user_manage_own
ON public.customers
FOR ALL
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());
```

### 📦 For `orders` table

```sql
-- Admins: full access
CREATE POLICY orders_admin_all
ON public.orders
FOR ALL
USING (auth_role() = 'admin')
WITH CHECK (auth_role() = 'admin');

-- Regular users: manage only own orders
CREATE POLICY orders_user_manage_own
ON public.orders
FOR ALL
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());
```

✅ Cleaner and easier than multiple single-purpose policies.

---

## 🧰 **8️⃣ Test Manually in SQL Editor**

### 👑 Simulate Admin

```sql
-- Pretend to be an authenticated admin
SELECT set_config('request.jwt.claim.sub', '<ADMIN_UUID>', true);

-- Verify
SELECT auth.uid(), auth_role();

-- Test queries
SELECT * FROM public.customers;
SELECT * FROM public.orders;

UPDATE public.orders
SET quantity = 10
WHERE id = 1
RETURNING *;
```

### 👤 Simulate Regular User

```sql
SELECT set_config('request.jwt.claim.sub', '<USER_UUID>', true);

SELECT auth.uid(), auth_role();

-- User should only see their own data
SELECT * FROM public.customers;
SELECT * FROM public.orders;

-- Create a new order
INSERT INTO public.orders (customer_id, product_id, quantity)
VALUES (1, 2, 1)
RETURNING *;

```

---

## 🧹 **9️⃣ Clean Up Test Data**

```sql
DELETE FROM public.app_users WHERE email = 'user@example.com';
DELETE FROM auth.users WHERE email = 'user@example.com';
```

If you used foreign keys → delete in order: `orders` → `customers` → `app_users` → `auth.users`.


---


