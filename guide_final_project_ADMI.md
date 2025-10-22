step-by-step **follow-along** workflow (best practices + ready-to-run SQL) for a Supabase / Postgres project with Row Level Security (RLS), Admin vs User roles, sample data, triggers, and explicit policies for read/insert/update/delete on each table.

Work through the numbered steps in order. Copy/paste each SQL block into Supabase SQL editor (or psql) and run it before moving to the next step.

---

# Part 0 — assumptions & best practices (quick)

1. Supabase will put `jwt` claims into `request.jwt.claims`. Use `request.jwt.claims.role` to detect `admin` vs `user`. Example values: `'admin'`, `'user'`.
2. Use `auth.uid()` to get the currently authenticated user's UUID. (Supabase exposes this in the DB environment.)
3. Keep client apps using the **anon** key (subject to RLS) and only use the **service_role** key on server backends (it bypasses RLS — don't use it in client code).
4. Enable RLS for all tables and use `USING` and `WITH CHECK` clauses in policies to control read vs insert/update/delete semantics.
5. Use triggers for derived/calculated values (e.g., `total_amount`) so clients don’t have to compute them.
6. Revoke broad public access and grant required privileges to the `authenticated` role so RLS policies can take effect.

---

# Part 1 — Schema + sample data + helper trigger

### 1. Create tables

```sql
-- 1️⃣ Create customers table
CREATE TABLE IF NOT EXISTS customers (
  customer_id SERIAL PRIMARY KEY,
  user_id uuid REFERENCES auth.users (id) ON DELETE CASCADE,
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
CREATE TABLE IF NOT EXISTS orders (
  order_id SERIAL PRIMARY KEY,
  customer_id INT NOT NULL REFERENCES customers (customer_id) ON DELETE CASCADE,
  product_id INT NOT NULL REFERENCES products (product_id) ON DELETE CASCADE,
  user_id uuid REFERENCES auth.users (id) ON DELETE CASCADE,
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
still trying this out --
```sql
TRUNCATE TABLE orders RESTART IDENTITY CASCADE;

INSERT INTO orders (customer_id, product_id, quantity, user_id)
VALUES
(1, 2, 1, 'b5fa48df-c568-4d73-b42c-e19896d9cfa8'),
(2, 1, 2, 'b5fa48df-c568-4d73-b42c-e19896d9cfa8'),
(3, 4, 1, 'b5fa48df-c568-4d73-b42c-e19896d9cfa8'),
(4, 5, 1, 'b5fa48df-c568-4d73-b42c-e19896d9cfa8'),
(5, 3, 3, 'b5fa48df-c568-4d73-b42c-e19896d9cfa8');
```

### 5. Create an *app users* table (optional but useful to store role)

If you prefer a separate table to mirror roles (in addition to auth.users claims):

```sql
CREATE TABLE IF NOT EXISTS app_users (
  id uuid PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  email text UNIQUE,
  role text NOT NULL CHECK (role IN ('admin','user','readonly')),
  created_at timestamptz DEFAULT now()
);

-- sample app_users rows (you can use the auth.users id values)
INSERT INTO app_users (id, email, role) VALUES
('24f29f59-89c1-4f1d-97e9-554029e6a3f3', 'outa.agunga@mail.admi.ac.ke', 'admin'),
('b5fa48df-c568-4d73-b42c-e19896d9cfa8', 'typingpool.astu@gmail.com', 'user');
```

> Note: Supabase also allows role in JWT claims. Keep both in sync if you use `app_users`.

### 6. Link customers to auth.users where email matches (your example)

```sql
UPDATE customers
SET user_id = u.id
FROM auth.users u
WHERE customers.email = u.email
  AND customers.user_id IS NULL;
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
