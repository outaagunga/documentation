
# 🧩 Comprehensive Project Outline: *E-Commerce Database (Supabase Project — Data Tools)*

---

## **1. Project Overview**

### **1.1 Project Description**

1.1.1 Define purpose and scope of the e-commerce database.
1.1.2 Explain how the database supports typical online store operations.
1.1.3 Identify the main features (user registration, product management, orders, payments).
1.1.4 Specify stakeholders (customers, store admins, developers).
1.1.5 Describe limitations or exclusions of the project (e.g., no real payment gateway).

### **1.2 Project Objectives**

1.2.1 Create a normalized PostgreSQL database hosted on Supabase.
1.2.2 Implement relationships among key entities (customers, products, orders).
1.2.3 Demonstrate SQL querying, data integrity, and foreign key constraints.
1.2.4 Practice documentation and version control via GitHub.
1.2.5 Present a functional demo showing data retrieval and management.

### **1.3 Tools and Technologies**

1.3.1 Supabase (PostgreSQL + dashboard)
1.3.2 dbdiagram.io or Supabase ERD tool
1.3.3 GitHub for version control
1.3.4 SQL (DDL/DML statements)
1.3.5 Markdown for documentation (`README.md`, `data_dictionary.md`)

---

## **2. Database Design Phase**

### **2.1 Requirements Analysis**

2.1.1 Identify entities (e.g., Customers, Products, Orders).
2.1.2 Define relationships (e.g., one customer can make many orders).
2.1.3 Determine key attributes for each entity.
2.1.4 Specify user interactions (view products, make orders, track delivery).
2.1.5 Define data volume expectations (for scalability awareness).

### **2.2 Conceptual Design**

2.2.1 Draw the Entity Relationship Diagram (ERD).
2.2.2 Label all primary keys (PK), foreign keys (FK), and data types.
2.2.3 Define relationship types (1-to-many, many-to-many).
2.2.4 Validate relationships for data consistency.

### **2.3 Logical Design**

2.3.1 Normalize database tables (up to 3NF).
2.3.2 Create detailed schema design with table and column specifications.
2.3.3 Define constraints (UNIQUE, NOT NULL, CHECK).
2.3.4 Map ERD entities to SQL tables.
2.3.5 Review naming conventions for clarity and standardization.

### **2.4 Physical Design**

2.4.1 Configure Supabase database project.
2.4.2 Create schema using SQL commands in Supabase SQL editor.
2.4.3 Insert minimum 5 sample rows per table.
2.4.4 Verify foreign key integrity and cascading rules.
2.4.5 Test CRUD (Create, Read, Update, Delete) operations.

---

## **3. Database Implementation**

### **3.1 SQL Schema Creation**

3.1.1 Write DDL commands (`CREATE TABLE`, `ALTER TABLE`).
3.1.2 Add indexes for performance optimization.
3.1.3 Ensure foreign key constraints work correctly.
3.1.4 Export schema to `schema.sql`.
3.1.5 Back up SQL file in `/sql/` directory.

### **3.2 Data Population**

3.2.1 Insert sample data using `INSERT INTO`.
3.2.2 Populate at least 5 rows per table.
3.2.3 Use realistic e-commerce data (products, users, orders).
3.2.4 Validate referential integrity.
3.2.5 Test SELECT queries to confirm correctness.

### **3.3 Query Testing**

3.3.1 Test SELECT statements with joins (INNER JOIN, LEFT JOIN).
3.3.2 Write aggregate queries (SUM, COUNT, AVG).
3.3.3 Write filtering queries using `WHERE`, `ORDER BY`, `LIMIT`.
3.3.4 Perform subqueries and nested queries.
3.3.5 Save test queries in `/queries/test_queries.sql`.

---

## **4. Documentation Development**

### **4.1 README.md**

4.1.1 Write a short introduction to the project.
4.1.2 Include setup steps (Supabase + GitHub).
4.1.3 Document database schema overview.
4.1.4 Show example queries.
4.1.5 Include author info and acknowledgments.

### **4.2 Data Dictionary**

4.2.1 Create `data_dictionary.md` file.
4.2.2 Include table name, column name, data type, description, and constraints.
4.2.3 List relationships between tables.
4.2.4 Include key definitions and data sources.

### **4.3 Entity Relationship Diagram (ERD)**

4.3.1 Generate ERD using Supabase or dbdiagram.io.
4.3.2 Label all entities, keys, and connections clearly.
4.3.3 Export ERD as PNG/SVG.
4.3.4 Save ERD in `/docs/` folder.

### **4.4 Security and Access Notes**

4.4.1 Document how data access is handled.
4.4.2 Describe security rules or row-level policies (if used).
4.4.3 Create `security_notes.md` (optional but recommended).

---

## **5. GitHub Workflow**

### **5.1 Repository Setup**

5.1.1 Create new GitHub repository named `ecommerce-database`.
5.1.2 Initialize repo with `README.md`.
5.1.3 Create folder structure:

```
/docs
/sql
/queries
```

5.1.4 Push schema and documentation.
5.1.5 Commit regularly with descriptive messages.

### **5.2 Pull Request & Collaboration**

5.2.1 Fork class repository.
5.2.2 Create a branch for your submission.
5.2.3 Submit pull request (PR) to class repository.
5.2.4 Review one peer’s PR and leave feedback.
5.2.5 Merge approved PRs.

---

## **6. Presentation & Demonstration**

### **6.1 Demo Preparation**

6.1.1 Prepare a short 5-minute demo script.
6.1.2 Include overview of the project purpose.
6.1.3 Show ERD and explain relationships.
6.1.4 Run a few example queries live in Supabase.
6.1.5 Show GitHub repo and documentation.

### **6.2 Presentation Delivery**

6.2.1 Introduce project goals.
6.2.2 Walk through the schema design.
6.2.3 Demonstrate data retrieval and updates.
6.2.4 Explain future improvement possibilities.
6.2.5 Answer questions from peers/instructor.

---

## **7. Evaluation Criteria**

### **7.1 Week 1 — Database Setup**

7.1.1 Supabase project created successfully.
7.1.2 Schema designed and implemented.
7.1.3 Sample data inserted.
7.1.4 Schema exported to `schema.sql`.

### **7.2 Week 2 — Documentation**

7.2.1 `README.md` and `data_dictionary.md` completed.
7.2.2 ERD accurate and saved in `/docs/`.
7.2.3 Files properly structured in repository.

### **7.3 Week 3 — GitHub Workflow**

7.3.1 Repository properly set up and organized.
7.3.2 PR created and reviewed by peers.
7.3.3 Feedback implemented if applicable.

### **7.4 Week 4 — Presentation**

7.4.1 Clear demo and explanation.
7.4.2 GitHub and documentation walkthrough.
7.4.3 Confident Q&A session.

---

## **8. Optional Extensions (For Extra Credit or Practice)**

### **8.1 Advanced SQL**

8.1.1 Add stored procedures or functions.
8.1.2 Implement triggers (e.g., auto-update stock after order).
8.1.3 Create materialized views for reporting.

### **8.2 Security and Policies**

8.2.1 Add Supabase Row-Level Security (RLS).
8.2.2 Define roles (admin, customer).
8.2.3 Restrict access to specific operations.

### **8.3 Analytics**

8.3.1 Generate sales summary queries.
8.3.2 Track most purchased products.
8.3.3 Create reports for revenue per month.
---  

# STEP BY STEP GUIDE  

# 🧩 **Part 1 — Project Overview & Database Setup (Week 1)**

*Goal: Set up your Supabase project, design the database schema, and insert sample data.*

---

## 🗂️ **1.1 Understand the Project**

### 🎯 Objective

You are building an **E-commerce Database** for an online store using **Supabase (PostgreSQL)**.
It will store information about:

* **Customers** (who buy products)
* **Products** (available in the store)
* **Orders** (what customers purchase)

### 🧠 What You’ll Learn

* How to design database tables
* How to create and link them in Supabase
* How to write SQL scripts (`schema.sql`)
* How to populate your tables with sample data

---

## ⚙️ **1.2 Create Your Supabase Project**

### ✅ Step 1: Go to Supabase

Visit **[https://supabase.com](https://supabase.com)** and log in with your GitHub or email account.

### ✅ Step 2: Create a New Project

1. Click **“New Project”**.
2. Give your project a name, e.g. **ecommerce-db**.
3. Set a strong password.
4. Choose your **database region** (closest to your location).
5. Click **Create Project**.

⏳ Wait for Supabase to finish setting up your PostgreSQL database.

---

## 🧩 **1.3 Design Your Database Schema**

We’ll use **three tables**:

1. `customers`
2. `products`
3. `orders`

### 🧱 Relationships

* One **customer** can make **many orders**.
* Each **order** refers to a **single product**.
* Each **order** links a **customer → product** relationship.

Let’s plan each table 👇

| Table       | Description                                             |
| ----------- | ------------------------------------------------------- |
| `customers` | Stores buyer details                                    |
| `products`  | Stores product information                              |
| `orders`    | Stores purchase details and links customers to products |

---

## 🧾 **1.4 Write the SQL Schema**

You’ll now create your database structure inside the **Supabase SQL Editor**.

### ✅ Step 1: Open SQL Editor

In your Supabase dashboard, go to **SQL Editor → New Query**.

### ✅ Step 2: Create Tables

#### 🧩 Table 1: `customers`

```sql
CREATE TABLE customers (
  customer_id SERIAL PRIMARY KEY,
  full_name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  phone VARCHAR(20),
  city VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### 🧩 Table 2: `products`

```sql
CREATE TABLE products (
  product_id SERIAL PRIMARY KEY,
  product_name VARCHAR(100) NOT NULL,
  category VARCHAR(50),
  price DECIMAL(10,2) NOT NULL,
  stock_quantity INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### 🧩 Table 3: `orders`

```sql
CREATE TABLE orders (
  order_id SERIAL PRIMARY KEY,
  customer_id INT REFERENCES customers(customer_id) ON DELETE CASCADE,
  product_id INT REFERENCES products(product_id) ON DELETE CASCADE,
  quantity INT NOT NULL,
  total_amount DECIMAL(10,2),
  order_date TIMESTAMP DEFAULT NOW()
);
```

## 🧩 **1.5 Insert Sample Data**

Each table needs at least **5 rows** of sample data.

### 🧩 Insert into `customers`

```sql
INSERT INTO customers (full_name, email, phone, city) VALUES
('Alice Mwangi', 'alice@example.com', '0712345678', 'Nairobi'),
('Brian Otieno', 'brian@example.com', '0723456789', 'Mombasa'),
('Cynthia Njeri', 'cynthia@example.com', '0734567890', 'Kisumu'),
('David Kimani', 'david@example.com', '0745678901', 'Nakuru'),
('Eva Wambui', 'eva@example.com', '0756789012', 'Eldoret');
```

### 🧩 Insert into `products`

```sql
INSERT INTO products (product_name, category, price, stock_quantity) VALUES
('Wireless Mouse', 'Electronics', 1200.00, 50),
('Laptop Backpack', 'Accessories', 2500.00, 30),
('USB Flash Drive 64GB', 'Storage', 1500.00, 40),
('Bluetooth Speaker', 'Electronics', 4500.00, 20),
('Laptop Stand', 'Accessories', 3000.00, 25);
```

### 🧩 Insert into `orders`

```sql
INSERT INTO orders (customer_id, product_id, quantity) VALUES
(1, 2, 1),
(2, 1, 2),
(3, 4, 1),
(4, 5, 1),
(5, 3, 3);
```

---

## 🔍 **1.6 Test Your Queries**

Now, verify that everything works properly.

### ✅ Select All Customers

```sql
SELECT * FROM customers;
```

### ✅ Join Orders and Products

```sql
SELECT o.order_id, c.full_name, p.product_name, o.quantity, o.total_amount, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id;
```

### ✅ Check Total Orders Per Customer

```sql
SELECT c.full_name, COUNT(o.order_id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.full_name;
```

---
### You will realized at times totals do not calcualte automatically. To solve that we use a triger  
### Create a trigger function to calculate `total_amount`

This function automatically sets `total_amount` based on the `quantity` × `price` from the `products` table.

```sql
CREATE OR REPLACE FUNCTION set_total_amount()
RETURNS TRIGGER AS $$
BEGIN
  -- Calculate total_amount from the product price
  SELECT price INTO NEW.total_amount
  FROM products
  WHERE product_id = NEW.product_id;

  NEW.total_amount := NEW.total_amount * NEW.quantity;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

### Create a trigger to call this function

Attach the function to the `orders` table so it runs **before every insert or update**.

```sql
CREATE TRIGGER trg_set_total_amount
BEFORE INSERT OR UPDATE ON orders
FOR EACH ROW
EXECUTE FUNCTION set_total_amount();
```

---

### Test it

```sql
-- Insert a sample order
INSERT INTO orders (customer_id, product_id, quantity)
VALUES (1, 2, 3);

-- Check that total_amount is auto-calculated
SELECT * FROM orders;
```

**Expected Output:**

| order_id | customer_id | product_id | quantity | total_amount | order_date          |
| -------- | ----------- | ---------- | -------- | ------------ | ------------------- |
| 1        | 1           | 2          | 3        | 149.97       | 2025-10-12 15:34:21 |


--- 
## Troubleshouting Triggers if there are not working  

### 1️⃣ **Check if the trigger function exists**

```sql
SELECT routine_name, routine_type, data_type
FROM information_schema.routines
WHERE routine_name = 'update_total_amount';
```

→ You should see one row returned if the function exists.

---

### 2️⃣ **Check if the trigger is attached to your orders table**

```sql
SELECT trigger_name, event_manipulation, action_timing, action_statement
FROM information_schema.triggers
WHERE event_object_table = 'orders';
```

You should see:

```
trigger_name    | event_manipulation | action_timing | action_statement
----------------+--------------------+----------------+---------------------------------------------
set_total_amount| INSERT             | BEFORE         | EXECUTE FUNCTION update_total_amount()
set_total_amount| UPDATE             | BEFORE         | EXECUTE FUNCTION update_total_amount()
```

✅ If it shows up, your trigger is **attached correctly**.

---

### 3️⃣ **Check if it fires**

After inserting a test order:

```sql
INSERT INTO orders (customer_id, product_id, quantity)
VALUES (1, 2, 2)
RETURNING *;
```

Then confirm:

```sql
SELECT order_id, product_id, quantity, total_amount
FROM orders
ORDER BY order_id DESC
LIMIT 1;
```

✅ If `total_amount` is **not-null**, your trigger is working.

---

### 4️⃣ **Optional: list all triggers in your database**

```sql
SELECT event_object_table AS table_name, trigger_name, action_timing, event_manipulation
FROM information_schema.triggers
ORDER BY table_name;
```

This will show all triggers on all tables.

---

You can also calculate `total_amount`** for all the orders that were inserted before the trigger was fixed (so they’re all filled in automatically)  

---

## 🧩 Step-by-Step Fix for Old `NULL` Totals

### 🧠 1️⃣ Check what’s currently missing

Run this to confirm which orders are missing totals:

```sql
SELECT order_id, customer_id, product_id, quantity, total_amount
FROM orders
WHERE total_amount IS NULL;
```

You’ll see rows where `total_amount` is `NULL`.

---

### ⚙️ 2️⃣ Update all missing totals

This command computes totals directly from product prices and updates all affected rows:

```sql
UPDATE orders AS o
SET total_amount = p.price * o.quantity
FROM products AS p
WHERE o.product_id = p.product_id
  AND o.total_amount IS NULL;
```

### 🔍 3️⃣ Verify results

After the update, check again:

```sql
SELECT order_id, customer_id, product_id, quantity, total_amount
FROM orders
ORDER BY order_id;
```

---

## 💾 **1.7 Export Schema**

After testing:

1. Go to **Supabase → Table Editor → SQL Editor**.
2. Click the **“Download SQL”** option to export schema.
3. Save it locally as:

   ```
   /sql/schema.sql
   ```
4. Keep your insert statements in a separate file:

   ```
   /sql/sample_data.sql
   ```

---

## 📁 **1.8 Folder Organization (Local Project Structure)**

Create a folder in your computer (you’ll later push this to GitHub):

```
ecommerce-database/
│
├── sql/
│   ├── schema.sql
│   ├── sample_data.sql
│
├── docs/
│
├── README.md
└── data_dictionary.md
```

---

## 🧠 **1.9 Verify Your Deliverables (End of Week 1)**

✅ You should now have:

* [x] Supabase project set up
* [x] 3 tables: `customers`, `products`, `orders`
* [x] At least 5 rows of sample data per table
* [x] Working foreign key relationships
* [x] Exported `schema.sql` and `sample_data.sql`

---

🎉 **Congratulations!**
You’ve completed **Part 1 — Database Setup**.
Your database is now live and functional.

---


# 🧾 **Part 2 — Documentation (Week 2)**

*Goal: Create professional documentation for your database project — including `README.md`, `data_dictionary.md`, and an ERD diagram.*

---

## 🧠 Why Documentation Matters

Your documentation tells others (and future you):

* What your project does
* How your database is structured
* How to run and test it
* How to contribute or build on top of it

By the end of this part, you’ll have:

* `README.md` — a full project overview and setup guide
* `data_dictionary.md` — a breakdown of every table and column
* ERD image in `/docs/` folder

---

## 📘 **2.1 Create the `README.md` File**

### 📁 Step 1: Create the File

In your project folder:

```
ecommerce-database/
│
├── README.md
```

### ✍️ Step 2: Write Project Overview

Below is a ready-to-use **template** with explanations and examples.

---

### 🧩 Example: `README.md`

````markdown
# 🛒 E-Commerce Database — Supabase Project

## 📖 Overview
This project is a simple **E-commerce database** built using **Supabase (PostgreSQL)**.  
It demonstrates key database design principles including normalization, relationships, and data integrity.

The database supports typical e-commerce operations:
- Managing **customers**
- Listing **products**
- Tracking **orders** and total sales

---

## 🧱 Database Schema Overview

### Tables
1. **customers** — stores customer details  
2. **products** — stores product listings  
3. **orders** — records customer purchases

### Relationships
- Each **customer** can place **many orders**  
- Each **order** links to a **product**  

![ERD Diagram](./docs/ecommerce_erd.png)

---

## ⚙️ Setup Instructions

### Prerequisites
- [Supabase Account](https://supabase.com)
- Basic knowledge of SQL
- [GitHub](https://github.com) account (for version control)

### Steps
1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/ecommerce-database.git
````

2. Open Supabase SQL Editor and paste the contents of:

   * `/sql/schema.sql`
   * `/sql/sample_data.sql`
3. Run the scripts to create and populate your database.
4. Test queries inside Supabase or pgAdmin.

---

## 🧮 Example Queries

### Get All Customers

```sql
SELECT * FROM customers;
```

### Get Order Details (with Customer & Product Info)

```sql
SELECT o.order_id, c.full_name, p.product_name, o.quantity, o.total_amount
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id;
```

### Calculate Total Revenue

```sql
SELECT SUM(total_amount) AS total_revenue FROM orders;
```

---

## 🧑‍💻 Author

**Your Name**
Student, Supabase Data Tools Project
📧 [youremail@example.com](mailto:youremail@example.com)

---

## 🪪 License

This project is for educational purposes only.

```

---

### 💡 Tips for README.md
- Keep sentences short and clear.  
- Always include one sample query.  
- Use simple Markdown formatting (### headers, lists, code blocks).  
- Link your ERD image once it’s ready.  

---

## 📊 **2.2 Create the Data Dictionary**

### 📁 Step 1: Create the File
```

ecommerce-database/
│
├── data_dictionary.md

````

### 📋 Step 2: Fill in Table Details

---

### 🧩 Example: `data_dictionary.md`

```markdown
# 📘 Data Dictionary — E-Commerce Database

This document describes all tables, columns, and relationships used in the database.

---

## 🧱 Table: customers

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| customer_id | SERIAL | PRIMARY KEY | Unique customer ID |
| full_name | VARCHAR(100) | NOT NULL | Customer’s full name |
| email | VARCHAR(100) | UNIQUE, NOT NULL | Customer email address |
| phone | VARCHAR(20) |  | Customer contact number |
| city | VARCHAR(50) |  | Customer city or location |
| created_at | TIMESTAMP | DEFAULT NOW() | Date the record was created |

---

## 🧱 Table: products

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| product_id | SERIAL | PRIMARY KEY | Unique product ID |
| product_name | VARCHAR(100) | NOT NULL | Name of the product |
| category | VARCHAR(50) |  | Type/category of product |
| price | DECIMAL(10,2) | NOT NULL | Price per unit |
| stock_quantity | INT | DEFAULT 0 | Available stock |
| created_at | TIMESTAMP | DEFAULT NOW() | Record creation date |

---

## 🧱 Table: orders

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| order_id | SERIAL | PRIMARY KEY | Unique order ID |
| customer_id | INT | FOREIGN KEY → customers(customer_id) | Customer placing the order |
| product_id | INT | FOREIGN KEY → products(product_id) | Product being ordered |
| quantity | INT | NOT NULL | Number of units purchased |
| total_amount | DECIMAL(10,2) | GENERATED ALWAYS | Total cost (quantity × product price) |
| order_date | TIMESTAMP | DEFAULT NOW() | When the order was made |

---

## 🔗 Relationships

| Relationship | Type | Description |
|---------------|------|-------------|
| customers → orders | One-to-Many | Each customer can make many orders |
| products → orders | One-to-Many | Each product can appear in many orders |

---

## 🧠 Notes
- All foreign key relationships use `ON DELETE CASCADE`.  
- Data follows normalization principles (3NF).  
- `total_amount` is a computed column (calculated automatically).  
````

---

## 🗺️ **2.3 Create the ERD (Entity Relationship Diagram)**

You can create your ERD using either **Supabase** or **[dbdiagram.io](https://dbdiagram.io)**.

### 🧩 Step 1: Use dbdiagram.io (recommended)

1. Visit **[https://dbdiagram.io](https://dbdiagram.io)**
2. Create a new diagram
3. Paste this schema in the editor:

```dbml
Table customers {
  customer_id int [pk, increment]
  full_name varchar
  email varchar [unique]
  phone varchar
  city varchar
  created_at timestamp
}

Table products {
  product_id int [pk, increment]
  product_name varchar
  category varchar
  price decimal
  stock_quantity int
  created_at timestamp
}

Table orders {
  order_id int [pk, increment]
  customer_id int [ref: > customers.customer_id]
  product_id int [ref: > products.product_id]
  quantity int
  total_amount decimal
  order_date timestamp
}
```

4. You’ll see a visual diagram showing relationships.
5. Export it as **PNG** or **SVG**.
6. Save it inside your project folder:

   ```
   /docs/ecommerce_erd.png
   ```

---

### 📸 Step 2: (Alternative) Generate from Supabase

If you prefer Supabase’s built-in ERD:

1. Go to **Supabase Dashboard → Table Editor → Relationships view**
2. Click **“Generate ERD”**
3. Take a screenshot and save it in `/docs/`

---

## 🧩 **2.4 Optional — Add Security Notes**

If you want to demonstrate awareness of data security, create an extra file:

### 🧩 `security_notes.md`

```markdown
# 🔒 Security Notes — E-Commerce Database

## Access Control
- Only authorized users (e.g., admin) can insert or delete product records.
- Customers can only view product data and their own order history.

## Data Protection
- Sensitive fields like emails are validated before insertion.
- Deletion cascades prevent orphaned data.

## Supabase Features
- (Optional) Enable Row Level Security (RLS) for controlled access.
```

Save it as:

```
/docs/security_notes.md
```

---

## ✅ **2.5 End-of-Week Checklist**

By the end of **Week 2**, you should have:

| Deliverable                      | File                      | Status |
| -------------------------------- | ------------------------- | ------ |
| Project overview and setup guide | `README.md`               | ✅      |
| Data Dictionary                  | `data_dictionary.md`      | ✅      |
| ERD diagram                      | `/docs/ecommerce_erd.png` | ✅      |
| (Optional) Security Notes        | `/docs/security_notes.md` | ✅      |

---

🎉 **Congratulations!**
You’ve completed **Part 2 — Documentation**.
Your database is now fully documented and ready for version control.

---


# 🧭 **Part 3 — GitHub Workflow (Week 3)**

*Goal: Upload your project to GitHub, organize it properly, and submit a Pull Request (PR) to your class repository.*

---

## 💡 Why This Step Matters

GitHub isn’t just a storage space — it shows your professionalism, lets others review your code, and acts as proof of your technical work.
This section will teach you how to:

* Initialize a Git repository
* Push files to GitHub
* Make a Pull Request (PR)
* Review another student’s PR

---

## 🧩 **3.1 Set Up Your Local Repository**

### ✅ Step 1: Open Your Project Folder

Locate your project folder from Part 2:

```
ecommerce-database/
│
├── sql/
│   ├── schema.sql
│   ├── sample_data.sql
│
├── docs/
│   ├── ecommerce_erd.png
│   ├── security_notes.md
│
├── README.md
└── data_dictionary.md
```

---

### ✅ Step 2: Initialize Git

Open your **terminal** or **VS Code terminal** inside this folder.

Run:

```bash
git init
```

This creates a hidden `.git/` folder that tracks your files.

---

### ✅ Step 3: Add Your Files

```bash
git add .
```

This stages all files for your first commit.

---

### ✅ Step 4: Commit Your Work

```bash
git commit -m "Initial commit: Added schema, docs, and sample data"
```

Your local Git history now includes your full project snapshot.

---

## 🌐 **3.2 Create Your GitHub Repository**

### ✅ Step 1: Log In

Go to [https://github.com](https://github.com).

### ✅ Step 2: Create New Repository

1. Click the **“+” → “New repository”**.

2. Repository name:

   ```
   ecommerce-database
   ```

3. Description (optional):
   “Supabase project for an E-commerce database design and demo.”

4. Choose:

   * Visibility: **Public** (recommended for this class)
   * Don’t initialize with README (you already have one)

5. Click **Create repository**.

---

### ✅ Step 3: Connect Local Folder to GitHub Repo

You’ll see GitHub give you connection commands like these 👇
Copy and paste them into your terminal:

```bash
git remote add origin https://github.com/your-username/ecommerce-database.git
git branch -M main
git push -u origin main
```

✅ You’ll now see your files appear on GitHub!

---

### 🧠 Tip

If you get a permission or authentication error, run:

```bash
git config --global user.name "Your GitHub Username"
git config --global user.email "youremail@example.com"
```

Then try pushing again.

---

## 🧾 **3.3 Verify Your Repository**

After pushing, open your GitHub repo and confirm it contains:

* `README.md`
* `data_dictionary.md`
* `/docs/` folder with ERD
* `/sql/` folder with schema and sample data

✅ Example:

```
📦 ecommerce-database
 ┣ 📂 docs
 ┃ ┣ 📄 ecommerce_erd.png
 ┃ ┗ 📄 security_notes.md
 ┣ 📂 sql
 ┃ ┣ 📄 schema.sql
 ┃ ┗ 📄 sample_data.sql
 ┣ 📄 README.md
 ┗ 📄 data_dictionary.md
```

---

## 🔁 **3.4 Make a Pull Request (PR) to the Class Repository**

This step simulates submitting your project for grading or team review.

### ✅ Step 1: Fork the Class Repository

* Go to your **class repository link** (provided by your instructor).
* Click **“Fork”** → This copies it to your GitHub account.

### ✅ Step 2: Clone Your Fork

Copy the repo’s URL (your forked version), then run:

```bash
git clone https://github.com/your-username/class-database-repo.git
cd class-database-repo
```

---

### ✅ Step 3: Add Your Project Folder

Inside your cloned repo, create a folder for your project:

```
/projects/yourname-ecommerce/
```

Copy your `ecommerce-database` files into that folder.

Example:

```
class-database-repo/
 ┣ 📂 projects/
 ┃ ┗ 📂 yourname-ecommerce/
 ┃   ┣ 📂 sql/
 ┃   ┣ 📂 docs/
 ┃   ┣ 📄 README.md
 ┃   ┗ 📄 data_dictionary.md
 ┣ 📄 README.md
 ┗ 📄 .gitignore
```

---

### ✅ Step 4: Commit and Push

```bash
git add .
git commit -m "Added E-commerce Database project files"
git push origin main
```

---

### ✅ Step 5: Create a Pull Request

1. Go to your **GitHub fork page**.
2. Click **“Compare & Pull Request.”**
3. Write a short title and description:

   ```
   Title: E-commerce Database Project Submission
   Description: Added schema, documentation, and ERD for the Supabase E-commerce Database project.
   ```
4. Submit the PR.

Your instructor or reviewer will now see your submission.

---

## 🧑‍🤝‍🧑 **3.5 Review a Peer’s Pull Request**

As part of collaboration, you’ll review someone else’s submission.

### ✅ Step 1: Visit Class Repository

Go to the main repo’s **Pull Requests** tab.

### ✅ Step 2: Choose a PR to Review

Open a fellow student’s PR and click **“Files Changed”**.

### ✅ Step 3: Add Feedback

Use inline comments like:

* “Consider using consistent naming for tables.”
* “Good ERD structure!”
* “Add one more example query to README.”

### ✅ Step 4: Approve or Request Changes

Click **Review Changes → Approve** or **Request Changes**.

---

## 📋 **3.6 (Optional) Organize GitHub with Branches**

If you want to look more professional or make changes safely:

```bash
git checkout -b documentation-update
# make changes to docs
git add .
git commit -m "Updated documentation for clarity"
git push origin documentation-update
```

Then, create a PR from that branch into your `main`.

---

## 🧠 **3.7 Common Git Commands Reference**

| Purpose            | Command                   |
| ------------------ | ------------------------- |
| Check repo status  | `git status`              |
| See commit history | `git log --oneline`       |
| Stage changes      | `git add filename`        |
| Commit             | `git commit -m "message"` |
| Push to GitHub     | `git push origin main`    |
| Pull updates       | `git pull origin main`    |

---

## ✅ **3.8 End-of-Week Checklist**

| Task                  | Deliverable                                           | Status |
| --------------------- | ----------------------------------------------------- | ------ |
| GitHub repo created   | `https://github.com/your-username/ecommerce-database` | ✅      |
| All files uploaded    | Schema, docs, ERD                                     | ✅      |
| Pull Request created  | PR link shared with instructor                        | ✅      |
| Peer review completed | At least one PR reviewed                              | ✅      |

---

🎉 **Congratulations!**
You’ve completed **Part 3 — GitHub Workflow**.
Your E-commerce Database project is now properly documented, versioned, and publicly available.

---

Would you like me to continue with **Part 4 — Presentation & Demo (Week 4)** next — including what to prepare, how to structure your slides, and live demo steps (with query examples)?

Excellent ✅ — let’s wrap up your project strong!

Now that your **database and GitHub repo** are ready, it’s time for the final stage: presenting your work and running a live demo.

---

# 🎤 **Part 4 — Presentation & Demo (Week 4)**

*Goal: Present your E-commerce Database project professionally and demonstrate how it works using real SQL queries.*

---

## 🧭 **Overview**

This week focuses on **communicating** your technical work clearly — both visually and verbally.
By the end of this section, you’ll have:

* A **clean project presentation (slides)**
* A **live demo** using Supabase or SQL editor
* A **self-contained project summary** ready for your portfolio or GitHub README

---

## 🧩 **4.1 Create Your Presentation Slides**

Your slides should be simple, visual, and structured like a story — not full of text.

Use **Google Slides**, **Canva**, or **PowerPoint**, and follow this 6-slide outline:

---

### 🖼️ Slide 1: Title & Team

**Example:**

```
E-Commerce Database Project  
Designed & Built by: [Your Name]
Bootcamp: [Cohort Name / Date]
```

Optionally, include:

* Your GitHub link
* Supabase project link (if public)
* A background photo or logo (optional)

---

### 🧠 Slide 2: Project Overview

Summarize your project in 2–3 sentences.

**Example:**

> The E-Commerce Database was designed to manage an online store’s data — including products, customers, orders, and reviews. The goal is to demonstrate a clean relational structure that supports real-world use cases.

---

### 🧱 Slide 3: Database Design

Include your **ERD diagram (Entity Relationship Diagram)**.

You can generate this in Supabase automatically or use [draw.io](https://app.diagrams.net/).

Label key relationships clearly:

* `1:N` for one-to-many (e.g., customers → orders)
* `N:M` if applicable (e.g., products ↔ categories)

---

### 📊 Slide 4: Key Tables & Relationships

Create a small summary table like this:

| Table Name    | Purpose                   | Key Columns               |
| ------------- | ------------------------- | ------------------------- |
| `customers`   | Stores customer details   | `customer_id`, `email`    |
| `products`    | Lists all items for sale  | `product_id`, `price`     |
| `orders`      | Tracks customer purchases | `order_id`, `customer_id` |
| `order_items` | Links orders to products  | `order_id`, `product_id`  |

Optional: include a screenshot of your Supabase table view.

---

### ⚙️ Slide 5: SQL Demo (Live or Screenshot)

Show 2–3 short SQL examples that prove your database *works*.

#### Example 1 — Get total number of customers:

```sql
SELECT COUNT(*) AS total_customers FROM customers;
```

#### Example 2 — Find top-selling product:

```sql
SELECT p.product_name, SUM(oi.quantity) AS total_sold
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_name
ORDER BY total_sold DESC
LIMIT 1;
```

#### Example 3 — Check recent orders:

```sql
SELECT o.order_id, c.customer_name, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
ORDER BY o.order_date DESC
LIMIT 5;
```

💡 *Tip:* You can show this in real time on [Supabase SQL Editor](https://supabase.com) or a local PostgreSQL client.

---

### 🧾 Slide 6: Challenges & Lessons Learned

Be honest and reflective.

**Example:**

* “I learned how to properly structure relationships in SQL.”
* “Understanding primary and foreign keys was tricky at first.”
* “Supabase’s GUI made it easier to test queries and generate ERDs.”

Optional: add your next step, e.g.

> “Next, I want to build a Django REST API using this database.”

---

## 💻 **4.2 Run a Live Demo**

During your presentation (or recorded video):

1. **Open Supabase SQL Editor.**
2. Run at least 2–3 queries from your `/sql/queries.sql` file.
3. Show:

   * Table contents (SELECT * FROM …)
   * A JOIN query that combines tables
   * A simple aggregation (COUNT, SUM, AVG)

💡 Example sequence:

```sql
-- 1. View products
SELECT * FROM products LIMIT 5;

-- 2. Show orders with customer names
SELECT o.order_id, c.customer_name, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;

-- 3. Calculate total sales per product
SELECT p.product_name, SUM(oi.quantity * p.price) AS total_revenue
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_name
ORDER BY total_revenue DESC;
```

✅ Screenshot your outputs or record a short screen video (if required).

---

## 🧾 **4.3 Update Your README for the Final Submission**

Your `README.md` should now include:

1. **Project overview**
2. **ERD diagram** (embed the image)
3. **Table list and description**
4. **Sample SQL queries**
5. **Installation or run instructions**
6. **Author info + GitHub link**

**Example README snippet:**

````markdown
# 🛍️ E-Commerce Database Project

This project demonstrates a relational database for an online store using PostgreSQL (via Supabase).

## 🧱 ERD Diagram
![E-Commerce ERD](./docs/ecommerce_erd.png)

## 🧩 Key Tables
- customers
- products
- orders
- order_items

## 📊 Example Query
```sql
SELECT p.product_name, SUM(oi.quantity) AS total_sold
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
GROUP BY p.product_name;
````

## 👩‍💻 Author

[Your Name]
[GitHub Profile Link]

```

---

## 🧑‍⚖️ **4.4 Evaluation Rubric (Example)**

| Category | Description | Points |
|-----------|--------------|--------|
| Database Design | Proper tables, relationships, ERD | 25 |
| SQL Queries | Working and meaningful queries | 25 |
| Documentation | README + data dictionary + comments | 20 |
| GitHub Organization | Repo structure and commit quality | 15 |
| Presentation & Demo | Clear, confident, well-structured | 15 |
| **Total** |  | **100** |

---

## 🏁 **4.5 End-of-Project Checklist**

| Task | Description | Status |
|------|--------------|--------|
| Presentation slides done | Visual overview with ERD & examples | ✅ |
| Live SQL demo tested | Queries run successfully | ✅ |
| README updated | Contains all info | ✅ |
| GitHub repo clean | Organized and public | ✅ |
| PR merged | Instructor approved | ✅ |

---

🎉 **Congratulations!**
You’ve completed the full **E-Commerce Database Project Workflow**:
1. Built your database schema in Supabase  
2. Created documentation (ERD, data dictionary, README)  
3. Pushed everything to GitHub  
4. Delivered a professional presentation and live demo  

You now have a **portfolio-ready project** you can proudly share on your CV, LinkedIn, or portfolio.

---

Would you like me to continue with a **Bonus Part 5 — Turning This Database into an API (using Django Rest Framework or FastAPI)** as an optional next learning step?
```

Perfect 👏 — great next step!

Let’s now move into **Part 5 — Turning Your E-Commerce Database into an API using Django Rest Framework (DRF)**.

This will transform your **Supabase PostgreSQL database** into a **fully functional backend API** — the kind used by real-world e-commerce apps or dashboards.

---

# 🧩 **Part 5 — Build an E-Commerce API with Django Rest Framework**

### 🎯 **Goal**

Convert your **E-Commerce database** (built in Supabase/PostgreSQL) into a **REST API** using Django Rest Framework (DRF).

You’ll be able to:

* View and manage data (products, customers, orders) via API endpoints
* Handle CRUD operations (Create, Read, Update, Delete)
* Test APIs in the browser or Postman
* Optionally, add authentication

---

## ⚙️ **5.1 Prerequisites**

Before starting, ensure you have:

* Python 3.10+ installed
* PostgreSQL database connection (Supabase provides this URL)
* GitHub repo from your previous project (you’ll add Django code to it)

---

## 🪜 **5.2 Step 1 — Setup Django Project**

### 5.2.1 Create a new folder for your backend

```bash
mkdir ecommerce_api
cd ecommerce_api
```

### 5.2.2 Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
```

### 5.2.3 Install Django and DRF

```bash
pip install django djangorestframework psycopg2-binary python-dotenv
```

### 5.2.4 Start a new Django project

```bash
django-admin startproject core .
```

✅ This creates:

```
ecommerce_api/
 ├── core/
 │   ├── settings.py
 │   ├── urls.py
 │   └── wsgi.py
 ├── manage.py
 └── venv/
```

---

## 🗄️ **5.3 Step 2 — Connect to Your Supabase Database**

### 5.3.1 Get connection details from Supabase:

In Supabase dashboard → *Settings → Database → Connection Info*

You’ll see something like:

```
Host: db.abcxyz.supabase.co
Database: postgres
User: postgres
Password: your_password
Port: 5432
```

### 5.3.2 Add to `.env` file

Create a `.env` file in your project root:

```bash
touch .env
```

**Add your credentials:**

```bash
DB_NAME=postgres
DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=db.abcxyz.supabase.co
DB_PORT=5432
```

### 5.3.3 Update `settings.py`

Install `python-dotenv` and load environment variables:

```python
# core/settings.py
import os
from dotenv import load_dotenv

load_dotenv()

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
        'HOST': os.getenv('DB_HOST'),
        'PORT': os.getenv('DB_PORT'),
    }
}
```

Also, add DRF to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'store',   # will create this app next
]
```

---

## 🏗️ **5.4 Step 3 — Create Your App and Models**

### 5.4.1 Create the app

```bash
python manage.py startapp store
```

### 5.4.2 Define models (same as your Supabase schema)

Example:

```python
# store/models.py
from django.db import models

class Customer(models.Model):
    customer_id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    address = models.TextField()
    phone = models.CharField(max_length=20)

    def __str__(self):
        return self.name


class Product(models.Model):
    product_id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=150)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()

    def __str__(self):
        return self.name


class Order(models.Model):
    order_id = models.AutoField(primary_key=True)
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)
    order_date = models.DateTimeField(auto_now_add=True)
    total_amount = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return f"Order {self.order_id} by {self.customer.name}"
```

### 5.4.3 Make migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🌐 **5.5 Step 4 — Create Serializers**

Serializers convert database data into JSON and vice versa.

```python
# store/serializers.py
from rest_framework import serializers
from .models import Customer, Product, Order

class CustomerSerializer(serializers.ModelSerializer):
    class Meta:
        model = Customer
        fields = '__all__'

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__'

class OrderSerializer(serializers.ModelSerializer):
    customer = CustomerSerializer(read_only=True)

    class Meta:
        model = Order
        fields = '__all__'
```

---

## 🧭 **5.6 Step 5 — Create Views and Endpoints**

### 5.6.1 Create API views

```python
# store/views.py
from rest_framework import viewsets
from .models import Customer, Product, Order
from .serializers import CustomerSerializer, ProductSerializer, OrderSerializer

class CustomerViewSet(viewsets.ModelViewSet):
    queryset = Customer.objects.all()
    serializer_class = CustomerSerializer

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

class OrderViewSet(viewsets.ModelViewSet):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer
```

---

## 🧩 **5.7 Step 6 — Register URLs**

```python
# store/urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import CustomerViewSet, ProductViewSet, OrderViewSet

router = DefaultRouter()
router.register(r'customers', CustomerViewSet)
router.register(r'products', ProductViewSet)
router.register(r'orders', OrderViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

And include it in your main `core/urls.py`:

```python
# core/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('store.urls')),
]
```

---

## 🚀 **5.8 Step 7 — Run the Server and Test**

```bash
python manage.py runserver
```

Now open your browser and visit:

* **Customers API:** [http://127.0.0.1:8000/api/customers/](http://127.0.0.1:8000/api/customers/)
* **Products API:** [http://127.0.0.1:8000/api/products/](http://127.0.0.1:8000/api/products/)
* **Orders API:** [http://127.0.0.1:8000/api/orders/](http://127.0.0.1:8000/api/orders/)

You’ll see JSON responses — and DRF gives you a built-in web interface too 🎉

---

## 🧾 **5.9 Step 8 — Add Sample Data**

You can:

1. Insert data directly in **Supabase dashboard**, or
2. Use Django shell:

```bash
python manage.py shell
```

```python
from store.models import Customer, Product, Order
c1 = Customer.objects.create(name="Jane Doe", email="jane@example.com", address="Mombasa", phone="0712345678")
p1 = Product.objects.create(name="Laptop", price=75000, stock=10)
Order.objects.create(customer=c1, total_amount=75000)
```

---

## 🔒 **5.10 (Optional) Step 9 — Add Authentication**

Add simple token authentication:

1. Add `'rest_framework.authtoken'` to `INSTALLED_APPS`
2. Migrate:

   ```bash
   python manage.py migrate
   ```
3. Update settings:

   ```python
   REST_FRAMEWORK = {
       'DEFAULT_AUTHENTICATION_CLASSES': [
           'rest_framework.authentication.TokenAuthentication',
       ],
   }
   ```
4. Generate tokens for users:

   ```bash
   python manage.py drf_create_token <username>
   ```

Then, include tokens in API headers when making requests.

---

## 📦 **5.11 Step 10 — Commit and Push to GitHub**

```bash
git add .
git commit -m "Added Django REST API for e-commerce database"
git push origin main
```

---

## ✅ **End Result**

You now have a fully functional **E-Commerce REST API** connected to your Supabase PostgreSQL database.

### Example Endpoints:

| Endpoint               | Method           | Description              |
| ---------------------- | ---------------- | ------------------------ |
| `/api/customers/`      | GET, POST        | List or add customers    |
| `/api/products/`       | GET, POST        | List or add products     |
| `/api/orders/`         | GET, POST        | Manage orders            |
| `/api/customers/{id}/` | GET, PUT, DELETE | Manage a single customer |

---

## 🧠 **Next Learning Steps (Optional)**

* Add **frontend UI** (React or Vue)
* Deploy API to **Render**, **Vercel**, or **Railway**
* Add **JWT authentication** using `djangorestframework-simplejwt`
* Connect to **payment gateway APIs** (like Stripe or Mpesa Sandbox)

---

# PROJECT 2:- SECURITY  

Perfect ✅ — we’ll now start your **follow-along guide** for the new project:

> **Data Fundamentals Project: Admin Roles & Security in Supabase**

This will build **directly on top of your previous e-commerce database** (the one you already created in Supabase).

We'll follow the same structured “Step-by-Step” style you liked before.
Let’s begin with **Part 1 — Database Setup & Row-Level Security (RLS)**.

---

# 🧩 **Part 1 — Database Setup & Enabling Row-Level Security**

### 🎯 **Goal**

Set up your existing **E-commerce Database** in Supabase for role-based access and data security.
You’ll enable **Row Level Security (RLS)**, which is a PostgreSQL feature that restricts access to specific rows based on user identity.

---

## 🗂️ **1.1 Use Your Existing Database**

You’ll use the **E-commerce database** you built in your previous “Data Tools Final Project.”

### ✅ Your Tables (example)

| Table       | Description                 | Key Columns                             |
| ----------- | --------------------------- | --------------------------------------- |
| `customers` | Stores customer information | `customer_id`, `name`, `email`          |
| `products`  | Stores product catalog      | `product_id`, `name`, `price`           |
| `orders`    | Tracks customer purchases   | `order_id`, `customer_id`, `order_date` |

---

## ⚙️ **1.2 Open Your Supabase Project**

1. Go to [https://app.supabase.com](https://app.supabase.com).
2. Open your **E-commerce** project.
3. In the **left sidebar**, click **Table Editor** to confirm your data is present.
4. You should see the same tables and sample rows from your previous project.

---

## 🧾 **1.3 Enable Row Level Security (RLS)**

RLS ensures that users can only view or edit rows that they are authorized to access.

### Step-by-Step:

1. In the Supabase sidebar, go to **Database → Table Editor**.
2. Click on your first table, e.g. **customers**.
3. Go to the **“RLS” (Row Level Security)** tab.
4. Click **“Enable RLS”** for the table.

Repeat this for all your main tables:

* `customers`
* `products`
* `orders`

✅ Once enabled, Supabase will restrict all access to the table **unless** you define explicit policies (which you’ll do in Part 2).

---

## 🧰 **1.4 Verify RLS Status**

You can also check this using SQL inside the **SQL Editor** in Supabase:

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

## 🧩 **1.6 Add a User Role Column**

We’ll store the user’s role (“admin” or “user”) inside a `users` table that maps to Supabase Auth IDs.

### SQL Example:

```sql
-- Create a users table linked to Supabase Auth users
create table if not exists users (
  id uuid references auth.users on delete cascade,
  email text not null,
  role text check (role in ('admin', 'user')) default 'user',
  primary key (id)
);
```

### Add Sample Data

After creating the table, insert your two users:

```sql
insert into users (id, email, role)
values
  ('<paste_admin_auth_uid>', 'admin@shop.com', 'admin'),
  ('<paste_user_auth_uid>', 'user@shop.com', 'user');
```

> 💡 To find your `auth.uid`, go to **Authentication → Users → Click a user → Copy UUID**.

---

## 🧮 **1.7 Link Orders and Customers to Authenticated Users**

Let’s add a reference column to track who created or owns data.

Example for `orders` table:

```sql
alter table orders
add column user_id uuid references auth.users (id);
```

Then backfill existing rows (optional):

```sql
update orders
set user_id = '<paste_admin_auth_uid>'
where order_id is not null;
```

This ensures future data entries can be filtered by the logged-in user.

---

## 🔐 **1.8 Test Basic Access (Pre-Policy)**

At this stage:

* RLS is enabled ✅
* No policies exist yet ❌
* This means all tables are **locked down** — no data is visible to anyone (even authenticated users)

To confirm:

1. Go to **SQL Editor** → Run:

   ```sql
   select * from customers;
   ```
2. You’ll see **“permission denied”** if RLS is properly active.

✅ That’s expected — in the next part, we’ll create policies to allow different access levels.

---

## 🗂️ **1.9 Document Your Setup**

Create a new markdown file in your GitHub repo:

```bash
touch security_notes.md
```

Write a summary:

```markdown
# Supabase Security Setup — Part 1

## Tables Secured with RLS
- customers
- products
- orders

## Description
Enabled Row Level Security (RLS) for all core e-commerce tables.
Created a `users` table linked to `auth.users` to manage roles.
Verified RLS via SQL query.

## Next Steps
- Define admin and user policies (Part 2)
- Implement Supabase Auth and functions
```

---

## ✅ **End of Part 1 Checklist**

| Task                                   | Description                                 | Status |
| -------------------------------------- | ------------------------------------------- | ------ |
| ✅ Used existing E-commerce Supabase DB | Confirmed schema and data                   | Done   |
| ✅ Enabled RLS                          | Turned on Row Level Security for all tables | Done   |
| ✅ Created `users` table                | Linked to Supabase Auth                     | Done   |
| ✅ Added user roles (admin/user)        | Inserted sample records                     | Done   |
| ✅ Verified RLS behavior                | Tested permission denied queries            | Done   |

---

✅ **Next Up:**
**Part 2 — Roles & Security Policies in Supabase**
We’ll write actual SQL policies that control what **admins** and **regular users** can do in your e-commerce system.

Would you like me to continue directly with **Part 2: Roles & Policies** now?


Absolutely! Let’s move to **Part 2** of your project — where you’ll actually create **roles and security policies** for your existing e-commerce Supabase database.

We’ll continue the **follow-along format** with clear explanations and SQL snippets.

---

# 🧩 Part 2 — Defining Roles & Security Policies in Supabase

---

## 🎯 Goal

In this part, you’ll:

* Create **Admin** and **User** roles.
* Enable **Row Level Security (RLS)** for your tables.
* Write **policies** that control what each role can do (read, insert, update, delete).

We’ll continue using your **E-commerce Database** with these 3 tables:

* `customers`
* `products`
* `orders`

---

## 🧱 Step 1: Enable Row Level Security (RLS)

RLS ensures users can **only access data they own or are permitted to see**.

### 🔧 Example:

For each table, turn on RLS:

```sql
-- Enable RLS for customers
alter table customers enable row level security;

-- Enable RLS for products
alter table products enable row level security;

-- Enable RLS for orders
alter table orders enable row level security;
```

✅ **Tip:**
In Supabase Dashboard:

* Go to **Table Editor → Policies → Enable RLS** for each table.

---

## 👥 Step 2: Add Role Column to `customers`

You need a way to identify **who is admin and who is a normal user**.

### 🧩 Example:

```sql
alter table customers
add column role text default 'user' check (role in ('admin', 'user'));
```

Now your `customers` table might look like this:

| id | name         | email                                   | role  |
| -- | ------------ | --------------------------------------- | ----- |
| 1  | Alice Kim    | [alice@mail.com](mailto:alice@mail.com) | admin |
| 2  | Brian Otieno | [brian@mail.com](mailto:brian@mail.com) | user  |

---

## 🛠️ Step 3: Create Policies for Each Table

We’ll make **two main policies**:

1. Admins → Full Access
2. Users → Limited Access (own data only)

---

### 🧾 3.1. Policies for `customers` table

#### a) Admins can view all customers

```sql
create policy "Admins can view all customers"
on customers
for select
using (exists (
  select 1 from customers where id = auth.uid() and role = 'admin'
));
```

#### b) Users can view only their own profile

```sql
create policy "Users can view their own profile"
on customers
for select
using (auth.uid() = id);
```

#### c) Users can update their own profile

```sql
create policy "Users can update their own profile"
on customers
for update
using (auth.uid() = id);
```

---

### 🧾 3.2. Policies for `products` table

#### a) Admins can manage all products

```sql
create policy "Admins can manage all products"
on products
for all
using (exists (
  select 1 from customers where id = auth.uid() and role = 'admin'
));
```

#### b) Users can view all products

```sql
create policy "Users can view products"
on products
for select
using (true);
```

---

### 🧾 3.3. Policies for `orders` table

#### a) Users can only view and insert their own orders

```sql
create policy "Users can view their own orders"
on orders
for select
using (auth.uid() = customer_id);

create policy "Users can create their own orders"
on orders
for insert
with check (auth.uid() = customer_id);
```

#### b) Admins can manage all orders

```sql
create policy "Admins can manage all orders"
on orders
for all
using (exists (
  select 1 from customers where id = auth.uid() and role = 'admin'
));
```

---

## 🧪 Step 4: Test Your Policies

1. Go to **SQL Editor** in Supabase.
2. Run a query as a test user (simulate user login with `auth.uid()` in SQL).
3. Try to:

   * Select all customers → should fail for normal user.
   * Select all products → should pass for any user.
   * Insert an order for another user → should fail.
   * Admin → should have full access.

✅ Example test query:

```sql
-- Test: Simulate current user
select * from customers where id = auth.uid();
```

---

## 🧾 Step 5: Document Policies

Create a file called `security_notes.md` in your GitHub repo.

Example:

```markdown
# Security Notes

## Tables with RLS
- customers
- products
- orders

## Policies Summary
- **Customers**:
  - Admins → Full Access
  - Users → View/Update Own Profile

- **Products**:
  - Admins → Manage All
  - Users → View All

- **Orders**:
  - Admins → Manage All
  - Users → Create/View Own Orders
```

---

## ✅ Deliverables for Part 2

* RLS enabled for all tables.
* Roles defined in `customers` table.
* Policies created for all 3 tables.
* `security_notes.md` added to GitHub.

---

Next up 👉 **Part 3 — Implementing Supabase Auth & Secure Functions**
Would you like me to continue with **Part 3** now?

Perfect 👌 Let’s move on to **Part 3** of your project.
This is where you’ll make your Supabase e-commerce database **secure and functional** by connecting **authentication (Auth)** and adding **custom SQL functions** that only admins can use.

---

# 🔐 Part 3 — Supabase Auth & Secure Functions

---

## 🎯 Goal

In this section, you’ll:

1. Set up **Supabase Authentication (Auth)** for users and admins.
2. Protect your data so only **logged-in users** can access it.
3. Create **secure functions** that require admin privileges (like deleting orders or products).
4. Test your setup with SQL and Auth users.

---

## 🧩 Step 1: Enable Supabase Auth

Supabase provides built-in **Auth** (email/password, magic links, etc.) that integrates directly with your PostgreSQL tables.

### 🪄 How to Enable Auth

1. Go to your **Supabase Dashboard → Authentication → Providers**
2. Enable **Email/Password** authentication.
3. Under **Users → Add User**, create two sample accounts:

   * **Admin account:** `admin@store.com`
   * **User account:** `user@store.com`

---

## ⚙️ Step 2: Link Auth Users to Your Customers Table

When a new user signs up, Supabase stores them in the `auth.users` system table.
You need to **sync** this with your `customers` table using a trigger function.

### 🧱 Create a Trigger to Sync Auth and Customers

```sql
-- Function to auto-create a customer after signup
create or replace function handle_new_user()
returns trigger
language plpgsql
security definer
as $$
begin
  insert into customers (id, name, email, role)
  values (new.id, new.email, new.email, 'user');
  return new;
end;
$$;

-- Trigger: runs whenever a new auth user is created
create trigger on_auth_user_created
after insert on auth.users
for each row
execute function handle_new_user();
```

✅ **What this does:**
Each time someone signs up, Supabase automatically adds them to your `customers` table with a default `role = 'user'`.

---

## 🔐 Step 3: Restrict Access to Authenticated Users Only

Now we’ll block all unauthenticated users from seeing or editing data.

### Example: Allow only logged-in users to access data

For every table:

```sql
create policy "Only logged-in users can access data"
on customers
for select
using (auth.role() = 'authenticated');
```

You can repeat the same for `orders` and `products`.

✅ **Tip:**

* This ensures that even if someone knows your table name, they **can’t query** it unless they are logged in.

---

## 🧠 Step 4: Create a Secure Admin-only Function

Sometimes you need to allow **admins** to perform special operations like deleting a product, resetting stock, or viewing analytics.

We’ll create an example admin-only SQL function.

### Example 1: Delete a Product (Admin only)

```sql
create or replace function admin_delete_product(prod_id uuid)
returns void
language plpgsql
security definer
as $$
begin
  if exists (
    select 1 from customers where id = auth.uid() and role = 'admin'
  ) then
    delete from products where id = prod_id;
  else
    raise exception 'Permission denied: admin access required.';
  end if;
end;
$$;
```

### Example 2: View All Orders (Admin only)

```sql
create or replace function admin_view_all_orders()
returns table (
  order_id uuid,
  customer_id uuid,
  total_amount numeric,
  order_date timestamp
)
language sql
security definer
as $$
  select id, customer_id, total_amount, created_at
  from orders
  where exists (
    select 1 from customers where id = auth.uid() and role = 'admin'
  );
$$;
```

✅ **Notes:**

* `security definer` runs the function with the privileges of the creator (usually admin).
* Each function checks if the logged-in user has `role = 'admin'`.

---

## 🧪 Step 5: Testing the Security Rules

You can simulate your users in the Supabase SQL Editor by temporarily replacing `auth.uid()` with a user UUID.

### Example: Testing Admin Access

```sql
-- Simulate admin user
select admin_delete_product('e7a19b7f-56dd-45b0-8a01-927f9cb9a123');
```

If the UUID belongs to a user with role = `'admin'`, the product will be deleted.
Otherwise, it should raise an exception.

---

## 🧾 Step 6: Document the Auth & Functions

Add this section to your `security_notes.md` or `README.md`:

```markdown
## Authentication & Secure Functions

### Auth Setup
- Enabled Supabase Email/Password Auth.
- Linked `auth.users` to `customers` via trigger `handle_new_user()`.
- Default role: `user`.

### Functions
1. `admin_delete_product(prod_id)` → Deletes a product (admin only)
2. `admin_view_all_orders()` → Lists all orders (admin only)

### Access Rules
- Only authenticated users can access data.
- Row Level Security (RLS) active on all tables.
- Admins can perform full CRUD; Users limited to own data.
```

---

## ✅ Deliverables for Part 3

By the end of this section, you should have:

* ✅ Supabase Auth enabled and tested
* ✅ Trigger to sync new users to `customers` table
* ✅ RLS policies allowing only authenticated users
* ✅ At least **one admin-only function**
* ✅ Updated documentation in your `README.md` and `security_notes.md`

---

Next up 👉 **Part 4 — GitHub Submission & Final Presentation**
Would you like me to continue with **Part 4** now (final stage)?

Excellent 👏 Let’s wrap up your Supabase security project with the **final phase** — documentation, GitHub submission, and presentation prep.

This is **Part 4** of your **Admin Roles & Security in Supabase** project — focusing on showcasing your work professionally, just like a real developer would when submitting a portfolio project.

---

# 🚀 Part 4 — GitHub Submission & Final Presentation

---

## 🎯 Goal

In this final stage, you’ll:

1. Organize your files and documentation.
2. Push your project to GitHub.
3. Create a professional `README.md` and `security_notes.md`.
4. Submit a Pull Request (PR) to the class repository.
5. Prepare and deliver a short 5-minute presentation demo.

---

## 🧩 Step 1: Organize Your Project Folder

Make sure your project folder has the correct structure before pushing to GitHub.

### 📁 Example Folder Structure:

```
supabase-ecommerce-security/
│
├── schema.sql                # SQL schema from your e-commerce database
├── sample_data.sql           # Optional sample insert statements
├── security_policies.sql     # All policies and role setup SQL
├── functions.sql             # Admin-only functions
│
├── docs/
│   ├── ERD.png               # Entity Relationship Diagram
│   └── roles_policies_chart.png (optional visual)
│
├── README.md                 # Main project documentation
├── data_dictionary.md        # Column definitions
├── security_notes.md         # RLS, roles, and functions explained
└── LICENSE (optional)
```

✅ **Tip:**
Keep your SQL files organized — it makes your repository easier for others (and future you) to understand.

---

## 🧾 Step 2: Write Your `README.md`

Your `README.md` acts as your **project report + setup guide**.

Here’s a clear structure you can follow 👇

### Example: `README.md`

````markdown
# 🛒 Supabase E-commerce Database — Admin Roles & Security

This project demonstrates how to implement **roles, security policies, and admin-only access** in a Supabase (PostgreSQL) database.  
It builds on an e-commerce schema with customers, products, and orders tables.

---

## ⚙️ Tech Stack
- Supabase (PostgreSQL)
- SQL (RLS, policies, functions)
- GitHub for documentation & submission

---

## 🧱 Database Schema
**Tables:**
- `customers` → user profiles (includes role: 'admin' or 'user')
- `products` → store products
- `orders` → records of user purchases

---

## 🔐 Security Features
- **Row Level Security (RLS)** enabled on all tables
- **Two roles:** Admin (full access), User (restricted access)
- **Policies:** Users can only view/insert their own data
- **Functions:** Admin-only operations (delete product, view all orders)
- **Supabase Auth:** Email/password signup; auto-linked to `customers` table

---

## 🧩 Example Admin Function
```sql
create or replace function admin_delete_product(prod_id uuid)
returns void
language plpgsql
security definer
as $$
begin
  if exists (
    select 1 from customers where id = auth.uid() and role = 'admin'
  ) then
    delete from products where id = prod_id;
  else
    raise exception 'Permission denied: admin access required.';
  end if;
end;
$$;
````

---

## 🧪 Testing

1. Log in as a **user** → Can view products, add own orders only.
2. Log in as **admin** → Can delete products, view all orders, manage everything.
3. Unauthenticated → Cannot access any table.

---

## 📚 Documentation

* `schema.sql` → Database structure
* `data_dictionary.md` → Table/column descriptions
* `security_notes.md` → Policies and RLS documentation
* `functions.sql` → Admin-only functions

---

## 📸 Screenshots

Add optional screenshots:

* Supabase Dashboard (RLS enabled)
* Policy editor
* Successful test query for admin/user

---

## 🧾 Author

**Your Name**
Data Tools Project — Admin Roles & Security
Lecturer: [Name]
Semester: 2025 Term 2

````

---

## 🔒 Step 3: Create `security_notes.md`

This file gives a technical summary of your security setup.

### Example: `security_notes.md`
```markdown
# Security Notes

## Row Level Security
RLS is enabled on:
- customers
- products
- orders

## Roles
- **Admin:** Full CRUD access on all tables.
- **User:** Can view all products, manage only their own profile and orders.

## Policies Summary
| Table | Action | Who | Description |
|--------|---------|------|-------------|
| customers | SELECT, UPDATE | User | Can only view/update own data |
| customers | ALL | Admin | Full access |
| products | SELECT | All users | View all |
| products | ALL | Admin | Manage all products |
| orders | INSERT, SELECT | User | Can create/view own orders |
| orders | ALL | Admin | Manage all orders |

## Functions
- `admin_delete_product(prod_id)` → Admin only  
- `admin_view_all_orders()` → Admin only

## Auth Integration
- Supabase Email/Password enabled  
- Trigger `handle_new_user()` automatically adds new signups to `customers`
````

---

## 🧰 Step 4: Push to GitHub

1. Create a new repository on GitHub:
   **`supabase-ecommerce-security`**

2. In your terminal or VS Code, run:

   ```bash
   git init
   git add .
   git commit -m "Initial commit: Supabase roles & security setup"
   git branch -M main
   git remote add origin https://github.com/<your-username>/supabase-ecommerce-security.git
   git push -u origin main
   ```

3. Go to your GitHub repo and verify all files are uploaded.

---

## 🔄 Step 5: Submit a Pull Request (PR)

1. Fork your lecturer’s **class repository** (as instructed).
2. Go to **Pull Requests → New Pull Request**.
3. Include:

   * A short project summary.
   * Link to your demo video (optional).
   * Your GitHub repo link.

**Example PR Message:**

```
## Project: Supabase E-commerce Security
Implemented RLS, Admin/User roles, and secure functions.
Includes documentation, SQL files, and test examples.
```

---

## 🗣️ Step 6: Presentation Demo (5 minutes)

### 🧭 Suggested Flow:

1. **Introduction (30s)**

   * “This project adds security and admin roles to my e-commerce database in Supabase.”

2. **Database Overview (1 min)**

   * Show tables and relationships briefly.

3. **Security Setup (2 mins)**

   * Show RLS enabled.
   * Demonstrate a user trying to access unauthorized data (and failing).
   * Show admin deleting a product successfully.

4. **Functions & Auth (1 min)**

   * Show `admin_delete_product()` or `admin_view_all_orders()` in action.

5. **Conclusion (30s)**

   * Highlight what you learned about roles, security, and SQL functions.

✅ **Tip:** Practice your demo once or twice — focus on clarity, not speed.

---

## 🏁 Final Deliverables Checklist

| # | Deliverable                                               | Status |
| - | --------------------------------------------------------- | ------ |
| 1 | RLS enabled on all tables                                 | ✅      |
| 2 | Admin/User roles implemented                              | ✅      |
| 3 | Security policies applied                                 | ✅      |
| 4 | Auth integration complete                                 | ✅      |
| 5 | Admin-only function added                                 | ✅      |
| 6 | Documentation complete (`README.md`, `security_notes.md`) | ✅      |
| 7 | GitHub repository created                                 | ✅      |
| 8 | Pull Request submitted                                    | ✅      |
| 9 | Presentation demo ready                                   | ✅      |

---

✅ **By the end of Part 4**, your project should look professional, secure, and well-documented — ready to present or include in your developer portfolio.

---

Would you like me to now give you a **bonus “Polish & Presentation Checklist”** (with visual examples and talking points) to help you present confidently and make your project stand out?

Perfect 🎯
Let’s finish strong with your **Bonus Section: “Polish & Presentation Checklist”** — this will help you make your **Supabase E-commerce Security Project** stand out both technically and visually when you present or submit it.

---

# 🏆 Bonus: Project Polish & Presentation Checklist

This checklist ensures your project looks **complete, professional, and portfolio-ready** — the kind that impresses instructors and potential employers.

---

## 🌟 SECTION 1 — Visual Polish & Organization

### ✅ 1.1 Folder & File Organization

Your repo should be clean, easy to navigate, and consistent.
Use short, descriptive filenames and group related files together.

**Good Example:**

```
supabase-ecommerce-security/
├── schema.sql
├── functions.sql
├── security_policies.sql
├── docs/
│   ├── ERD.png
│   ├── roles_policies_chart.png
│   └── demo_screenshots/
├── README.md
├── data_dictionary.md
├── security_notes.md
└── sample_data.sql
```

**Bad Example:**

```
project/
├── final.sql
├── mywork.sql
├── newtest.sql
├── randomfiles/
└── (no README)
```

🟢 **Tip:** Your `README.md` should be the *first file* someone sees — it acts like your project’s homepage.

---

### ✅ 1.2 Add a Simple ERD (Entity Relationship Diagram)

You can create one using:

* [dbdiagram.io](https://dbdiagram.io)
* [draw.io](https://app.diagrams.net)
* Supabase’s built-in ERD view (then take a screenshot)

Example ERD for your e-commerce project:

```
+-------------+        +-------------+        +-----------+
| customers   | 1 --- n| orders      | n --- 1| products  |
+-------------+        +-------------+        +-----------+
| id (uuid)   |        | id (uuid)   |        | id (uuid) |
| name        |        | customer_id |        | name      |
| email       |        | product_id  |        | price     |
| role        |        | quantity    |        | stock     |
+-------------+        +-------------+        +-----------+
```

Save this as `docs/ERD.png`.

---

### ✅ 1.3 Add Visuals to Your README

Visuals make your documentation easier to understand.

Include screenshots such as:

* Supabase dashboard (RLS enabled)
* Policy setup window
* Admin vs. user query result comparison

Example Markdown snippet:

```markdown
## 🔒 Row Level Security Example

| Role | Action | Result |
|------|---------|--------|
| User | SELECT * FROM orders | ✅ Sees only own orders |
| Admin | SELECT * FROM orders | ✅ Sees all orders |

![RLS Example](docs/demo_screenshots/rls_result.png)
```

---

## 🧠 SECTION 2 — Presentation Prep

### ✅ 2.1 Structure Your 5-Minute Demo

Keep it clear, fast, and focused. Use this structure 👇

| Minute    | Section       | What to Say / Show                                                                              |
| --------- | ------------- | ----------------------------------------------------------------------------------------------- |
| 0:00–0:30 | Intro         | “Hi, I’m [Name]. I built an e-commerce database on Supabase and added secure roles & policies.” |
| 0:30–1:30 | Overview      | Show ERD and explain 3 tables briefly.                                                          |
| 1:30–2:30 | Roles & RLS   | Show RLS enabled, explain Admin vs. User permissions.                                           |
| 2:30–3:30 | Demo          | Log in as a User → show restricted access; log in as Admin → show full access.                  |
| 3:30–4:30 | Function Demo | Show `admin_delete_product()` or `admin_view_all_orders()` running.                             |
| 4:30–5:00 | Wrap-up       | Summarize what you learned and next steps.                                                      |

🟢 **Pro Tip:** Time yourself once — if it takes longer, simplify your explanations.

---

### ✅ 2.2 Highlight What You Learned

When asked about your project, focus on key skills:

**Example talking points:**

* “I learned how to use Supabase’s Row Level Security (RLS) to restrict access to specific rows.”
* “I implemented role-based access using SQL policies.”
* “I created an admin-only SQL function for secure operations.”
* “I practiced documenting and deploying my project through GitHub.”

This shows you didn’t just build something — you *understand* how it works.

---

## 💬 SECTION 3 — Make Your Work Portfolio-Ready

### ✅ 3.1 Add a Project Summary to Your README

At the top, include a short summary paragraph like this:

> **Summary:**
> This project demonstrates secure database design in Supabase for an e-commerce platform.
> It includes user authentication, admin and customer roles, Row Level Security (RLS), and admin-only SQL functions.

### ✅ 3.2 Add a “Future Improvements” Section

Shows initiative and critical thinking.

**Example:**

```markdown
## 🚀 Future Improvements
- Add a front-end (Next.js or React) to connect to Supabase Auth.
- Implement payment tracking table.
- Create an admin dashboard to view total sales.
```

### ✅ 3.3 Add GitHub Topics/Tags

On GitHub, scroll to “About” → add:

```
#supabase #postgresql #database-security #rls #admin-roles
```

Makes your repo searchable and professional.

---

## 🎨 SECTION 4 — (Optional) Demo Video or GIF

If you want extra marks or to use this as a **portfolio project**, record a **short demo** (1–2 minutes):

### Tools:

* **Loom** (free screen recorder)
* **OBS Studio** (for higher quality)
* **Windows + G** (built-in Game Bar recording)

### What to show:

1. Dashboard → RLS enabled
2. Run SQL query as user (restricted)
3. Run query as admin (full access)
4. Call `admin_delete_product()` function
5. Show GitHub repo and documentation

Then embed the link in your `README.md`:

```markdown
🎥 [Watch Demo on Loom](https://loom.com/share/your-demo-link)
```

---

## 🧾 SECTION 5 — Instructor Submission Checklist

Before submitting your **Pull Request (PR)**, confirm all boxes below ✅:

| Task                                                                  | Status |
| --------------------------------------------------------------------- | ------ |
| Project uses **E-commerce schema** (customers, products, orders)      | ✅      |
| **Row Level Security (RLS)** enabled                                  | ✅      |
| **Admin/User** roles added                                            | ✅      |
| **Policies** created for each table                                   | ✅      |
| **Supabase Auth** configured                                          | ✅      |
| At least **1 admin-only function** created                            | ✅      |
| **README.md**, **data_dictionary.md**, **security_notes.md** complete | ✅      |
| **GitHub repo** pushed and organized                                  | ✅      |
| **PR submitted** with summary                                         | ✅      |
| **Presentation** ready and tested                                     | ✅      |

---

## 💡 Final Tips to Stand Out

* ✅ Use consistent formatting in SQL (uppercase keywords, lowercase table names).
* ✅ Add emoji headings in your README for clarity (`🔐`, `🧠`, `🛠️`).
* ✅ Show confidence and understanding — it’s better than speed during presentation.
* ✅ Mention real-world importance:

  > “RLS and role-based access control are essential in production databases to prevent unauthorized data exposure.”

---

## 🏁 Final Words

By completing this project, you’ve practiced **real-world database security design** — not just theory.
You’ve applied:

* Authentication
* Authorization
* Role-based policies
* SQL functions with privilege control
* Documentation and GitHub collaboration

This project alone is strong enough to feature in your **portfolio or CV** under *Database Security & Access Control with Supabase (PostgreSQL)*.

---

Would you like me to generate a **“Professional README Template (copy-paste ready)”** next — formatted with icons, sections, and placeholders you can customize before uploading to GitHub?
It will make your project look like a professional open-source repo.

Perfect 👏

Here’s your **Professional README Template** — clean, structured, and beginner-friendly — that makes your **Supabase E-commerce Security Project** look like a professional open-source portfolio project.

You can copy this directly into your `README.md` file and just fill in your details.

---

# 🛍️ Supabase E-commerce Security Project

*A beginner-friendly project demonstrating database design, authentication, and secure access control using Supabase (PostgreSQL).*

---

## 📖 Table of Contents

* [📘 Project Overview](#-project-overview)
* [🧩 Database Schema](#-database-schema)
* [🔐 Security & Role Management](#-security--role-management)
* [⚙️ SQL Functions](#️-sql-functions)
* [🧠 Learning Highlights](#-learning-highlights)
* [📊 Demo & Visuals](#-demo--visuals)
* [🚀 Future Improvements](#-future-improvements)
* [📂 Folder Structure](#-folder-structure)
* [🧾 Setup Instructions](#-setup-instructions)
* [👨‍💻 Author & Credits](#-author--credits)

---

## 📘 Project Overview

> This project demonstrates how to build a **secure e-commerce database** using **Supabase** and **PostgreSQL**, implementing **Row Level Security (RLS)**, **role-based access**, and **admin-only SQL functions**.

### 🎯 Objectives

* Design a normalized e-commerce database (customers, products, orders).
* Implement **Admin** and **User** roles.
* Apply **Row Level Security (RLS)** for data isolation.
* Create **SQL policies** for controlled access.
* Add **secure SQL functions** for admin-only actions.
* Document the design, schema, and security measures.

---

## 🧩 Database Schema

### 🧱 Core Tables

| Table       | Description                                             |
| ----------- | ------------------------------------------------------- |
| `customers` | Stores customer profiles, roles, and contact info       |
| `products`  | Contains product inventory details                      |
| `orders`    | Tracks customer orders linked to customers and products |

### Example ERD

![ERD Diagram](docs/ERD.png)

### Example Schema (SQL)

```sql
CREATE TABLE customers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  role TEXT CHECK (role IN ('admin', 'user')) DEFAULT 'user'
);

CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  price NUMERIC(10,2),
  stock INT DEFAULT 0
);

CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID REFERENCES customers(id),
  product_id UUID REFERENCES products(id),
  quantity INT NOT NULL,
  created_at TIMESTAMP DEFAULT now()
);
```

---

## 🔐 Security & Role Management

### 🔑 Roles

| Role      | Description                                            |
| --------- | ------------------------------------------------------ |
| **Admin** | Full access to all data and can manage products/orders |
| **User**  | Can only view and manage their own orders              |

### 🔒 Enable Row Level Security (RLS)

```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
```

### 🛡️ Policies

```sql
-- Allow users to view only their orders
CREATE POLICY user_select_own_orders
ON orders FOR SELECT
USING (auth.uid() = customer_id);

-- Allow admin full access
CREATE POLICY admin_full_access
ON orders
USING (EXISTS (
  SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
));
```

---

## ⚙️ SQL Functions

### 🧭 Example: Admin-Only Product Deletion

```sql
CREATE OR REPLACE FUNCTION admin_delete_product(p_id UUID)
RETURNS VOID AS $$
BEGIN
  IF EXISTS (
    SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
  ) THEN
    DELETE FROM products WHERE id = p_id;
  ELSE
    RAISE EXCEPTION 'Permission denied: only admins can delete products';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 🧠 Learning Highlights

Through this project, I learned:

* How to use **Supabase** for authentication and RLS.
* Setting up **secure SQL policies** for user roles.
* Writing **SQL functions** with privilege control.
* How to document and present a database security project.

---

## 📊 Demo & Visuals

### 🔹 RLS in Action

| Role  | Action                 | Result                 |
| ----- | ---------------------- | ---------------------- |
| User  | `SELECT * FROM orders` | ✅ Sees only own orders |
| Admin | `SELECT * FROM orders` | ✅ Sees all orders      |

![RLS Example](docs/demo_screenshots/rls_result.png)

### 🎥 Demo Video

[🎥 Watch Demo on Loom](https://loom.com/share/your-demo-link)

---

## 🚀 Future Improvements

* Build a front-end dashboard (React or Next.js).
* Add product categories and payment history tables.
* Use Supabase Auth UI for user signup/login.
* Add triggers for inventory updates on order creation.

---

## 📂 Folder Structure

```
supabase-ecommerce-security/
├── schema.sql
├── security_policies.sql
├── functions.sql
├── data_dictionary.md
├── security_notes.md
├── docs/
│   ├── ERD.png
│   ├── roles_policies_chart.png
│   └── demo_screenshots/
├── sample_data.sql
└── README.md
```

---

## 🧾 Setup Instructions

### Prerequisites

* Supabase account: [https://supabase.com](https://supabase.com)
* SQL client or Supabase dashboard access
* Basic SQL knowledge

### Steps

1. **Create a new Supabase project**
2. **Run** the SQL files (`schema.sql`, `security_policies.sql`, `functions.sql`)
3. **Enable RLS** on all main tables
4. **Insert sample data** using `sample_data.sql`
5. **Test** admin vs user behavior with Supabase Auth

---

## 👨‍💻 Author & Credits

**Author:** [Your Name]
**GitHub:** [your-username](https://github.com/your-username)
**Email:** [[your-email@example.com](mailto:your-email@example.com)]

Special thanks to **Supabase** for providing a free and beginner-friendly backend platform.

---

## 🏷️ Tags

`#supabase` `#postgresql` `#database-security` `#rls` `#admin-roles` `#ecommerce`

---

Would you like me to generate a **matching `schema.sql` + `security_policies.sql` + `functions.sql` file set** (with comments and formatted for upload) next — so you can quickly deploy this to your Supabase project?
It will match the README exactly.

Excellent 👌 — we’ll now build **a full Supabase project file set** that matches your README perfectly.

Below are three main SQL files you’ll upload or run in your Supabase SQL editor, step-by-step.
Each section includes **clear explanations and snippets** for easier learning and debugging.

---

# 🧱 Part 1 — `schema.sql`

*(Defines your e-commerce database structure and sample data)*

```sql
-- =========================================================
-- 📘 SCHEMA: E-COMMERCE DATABASE (Supabase)
-- Author: [Your Name]
-- Date: [Today's Date]
-- Description: Base schema for an e-commerce platform.
-- =========================================================

-- Enable UUID Extension (needed for generating unique IDs)
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

-- =============================
-- 1️⃣ TABLE: Customers
-- =============================
CREATE TABLE IF NOT EXISTS customers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  role TEXT CHECK (role IN ('admin', 'user')) DEFAULT 'user',
  created_at TIMESTAMP DEFAULT NOW()
);

-- =============================
-- 2️⃣ TABLE: Products
-- =============================
CREATE TABLE IF NOT EXISTS products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  price NUMERIC(10,2) NOT NULL CHECK (price >= 0),
  stock INT DEFAULT 0 CHECK (stock >= 0),
  created_at TIMESTAMP DEFAULT NOW()
);

-- =============================
-- 3️⃣ TABLE: Orders
-- =============================
CREATE TABLE IF NOT EXISTS orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID REFERENCES customers(id) ON DELETE CASCADE,
  product_id UUID REFERENCES products(id) ON DELETE SET NULL,
  quantity INT NOT NULL CHECK (quantity > 0),
  created_at TIMESTAMP DEFAULT NOW()
);

-- =============================
-- ✅ SAMPLE DATA
-- =============================

-- Insert customers (1 admin + 2 users)
INSERT INTO customers (name, email, role)
VALUES 
('Alice Admin', 'alice@shop.com', 'admin'),
('Ben Buyer', 'ben@shop.com', 'user'),
('Clara Customer', 'clara@shop.com', 'user');

-- Insert products
INSERT INTO products (name, price, stock)
VALUES
('Wireless Mouse', 15.99, 50),
('Laptop Stand', 39.50, 30),
('Mechanical Keyboard', 69.00, 20);

-- Insert orders
INSERT INTO orders (customer_id, product_id, quantity)
SELECT c.id, p.id, 2
FROM customers c, products p
WHERE c.name = 'Ben Buyer' AND p.name = 'Laptop Stand';

INSERT INTO orders (customer_id, product_id, quantity)
SELECT c.id, p.id, 1
FROM customers c, products p
WHERE c.name = 'Clara Customer' AND p.name = 'Wireless Mouse';
```

---

# 🛡️ Part 2 — `security_policies.sql`

*(Implements Row Level Security and access control policies)*

```sql
-- =========================================================
-- 🔐 SECURITY POLICIES: E-COMMERCE DATABASE
-- =========================================================

-- Enable Row Level Security (RLS) for all tables
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- =============================
-- 👤 CUSTOMERS TABLE POLICIES
-- =============================

-- Admin can view all customers
CREATE POLICY admin_select_all_customers
ON customers
FOR SELECT
USING (EXISTS (
  SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
));

-- User can view only their own profile
CREATE POLICY user_select_own_profile
ON customers
FOR SELECT
USING (auth.uid() = id);

-- =============================
-- 🧱 PRODUCTS TABLE POLICIES
-- =============================

-- Everyone (authenticated) can view products
CREATE POLICY public_view_products
ON products
FOR SELECT
USING (true);

-- Only admin can insert, update, delete products
CREATE POLICY admin_manage_products
ON products
FOR ALL
USING (EXISTS (
  SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
));

-- =============================
-- 🧾 ORDERS TABLE POLICIES
-- =============================

-- Users can view their own orders
CREATE POLICY user_view_own_orders
ON orders
FOR SELECT
USING (auth.uid() = customer_id);

-- Users can insert orders only for themselves
CREATE POLICY user_insert_own_order
ON orders
FOR INSERT
WITH CHECK (auth.uid() = customer_id);

-- Admin can access all orders
CREATE POLICY admin_full_access_orders
ON orders
FOR ALL
USING (EXISTS (
  SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
));
```

---

# ⚙️ Part 3 — `functions.sql`

*(Contains admin-only stored functions for advanced operations)*

```sql
-- =========================================================
-- ⚙️ FUNCTIONS: ADMIN PRIVILEGED ACTIONS
-- =========================================================

-- Delete a product (admin only)
CREATE OR REPLACE FUNCTION admin_delete_product(p_id UUID)
RETURNS VOID AS $$
BEGIN
  IF EXISTS (
    SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
  ) THEN
    DELETE FROM products WHERE id = p_id;
  ELSE
    RAISE EXCEPTION 'Permission denied: only admins can delete products';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Update stock quantity (admin only)
CREATE OR REPLACE FUNCTION admin_update_stock(p_id UUID, new_stock INT)
RETURNS VOID AS $$
BEGIN
  IF EXISTS (
    SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
  ) THEN
    UPDATE products SET stock = new_stock WHERE id = p_id;
  ELSE
    RAISE EXCEPTION 'Permission denied: only admins can update stock';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Calculate total revenue (admin only)
CREATE OR REPLACE FUNCTION admin_total_revenue()
RETURNS NUMERIC AS $$
DECLARE
  total NUMERIC;
BEGIN
  IF EXISTS (
    SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
  ) THEN
    SELECT SUM(p.price * o.quantity)
    INTO total
    FROM orders o
    JOIN products p ON p.id = o.product_id;
    RETURN COALESCE(total, 0);
  ELSE
    RAISE EXCEPTION 'Permission denied: only admins can view revenue';
  END IF;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

# ✅ Folder Summary

```
supabase-ecommerce-security/
├── schema.sql                # Database structure + sample data
├── security_policies.sql     # RLS and policy setup
├── functions.sql             # Admin-only SQL functions
├── README.md                 # Project overview and documentation
├── data_dictionary.md        # Column descriptions
├── security_notes.md         # Explanation of policies and roles
└── docs/
    ├── ERD.png
    ├── roles_policies_chart.png
    └── demo_screenshots/
```

---

# 🧾 Next Step

Would you like me to generate the **`data_dictionary.md`** and **`security_notes.md`** files next — fully filled and formatted — so your GitHub documentation set is 100% complete?
They’ll include:

* Detailed column definitions (for all 3 tables)
* Explanation of each policy and role in simple terms

Perfect 👍 — let’s complete your project documentation with **two professional, ready-to-upload Markdown files**:

✅ `data_dictionary.md`
✅ `security_notes.md`

These two files will make your Supabase E-commerce Security project fully documented and GitHub-ready.

---

# 📘 `data_dictionary.md`

```markdown
# 📗 Data Dictionary — Supabase E-commerce Database

This document describes all tables, columns, data types, and relationships used in the **E-commerce Security Project**.

---

## 🧱 Table: customers

| Column | Data Type | Constraints | Description |
|--------|------------|--------------|--------------|
| `id` | `UUID` | Primary Key, Default `gen_random_uuid()` | Unique ID for each customer |
| `name` | `TEXT` | NOT NULL | Customer’s full name |
| `email` | `TEXT` | UNIQUE, NOT NULL | Email address used for login |
| `role` | `TEXT` | CHECK (role IN ('admin', 'user')) | Defines access privileges |
| `created_at` | `TIMESTAMP` | DEFAULT `NOW()` | Record creation timestamp |

### Notes
- Only one **admin** account is recommended.
- Every user must have a unique email.
- Used in authentication and authorization policies.

---

## 🛍️ Table: products

| Column | Data Type | Constraints | Description |
|--------|------------|--------------|--------------|
| `id` | `UUID` | Primary Key, Default `gen_random_uuid()` | Unique ID for each product |
| `name` | `TEXT` | NOT NULL | Product name |
| `price` | `NUMERIC(10,2)` | NOT NULL, CHECK (price >= 0) | Product price |
| `stock` | `INT` | CHECK (stock >= 0), Default `0` | Available inventory |
| `created_at` | `TIMESTAMP` | DEFAULT `NOW()` | Time of product creation |

### Notes
- Linked to `orders` through `product_id`.
- Admin can manage stock and delete products.

---

## 🧾 Table: orders

| Column | Data Type | Constraints | Description |
|--------|------------|--------------|--------------|
| `id` | `UUID` | Primary Key, Default `gen_random_uuid()` | Unique order ID |
| `customer_id` | `UUID` | Foreign Key → `customers(id)` | Customer who placed the order |
| `product_id` | `UUID` | Foreign Key → `products(id)` | Product being ordered |
| `quantity` | `INT` | NOT NULL, CHECK (quantity > 0) | Quantity ordered |
| `created_at` | `TIMESTAMP` | DEFAULT `NOW()` | Date and time of order placement |

### Notes
- Enforces referential integrity with foreign keys.
- RLS ensures users only view or insert their own orders.

---

## 🔗 Relationships

| Relationship | Description |
|---------------|-------------|
| `customers.id → orders.customer_id` | One-to-many (each customer can have multiple orders) |
| `products.id → orders.product_id` | One-to-many (each product can appear in multiple orders) |

---

## 🧩 Summary

- **Total Tables:** 3  
- **Foreign Keys:** 2  
- **Primary Keys:** 3  
- **Relationships:** 2  
- **RLS Enabled:** ✅ Yes  
- **Custom Functions:** ✅ 3 Admin-only SQL functions  

```

---

# 🔐 `security_notes.md`

````markdown
# 🔐 Security Notes — Supabase E-commerce Security Project

This document explains the **security architecture**, **roles**, and **policies** applied in the project to protect data and enforce access control.

---

## 🧠 Overview

The goal is to implement **Role-Based Access Control (RBAC)** and **Row Level Security (RLS)** to ensure that:
- Admins have full control over all data.
- Regular users can only access or modify their own information.
- Sensitive operations (like deleting or updating products) are restricted.

---

## 👥 Roles and Permissions

| Role | Privileges | Description |
|------|-------------|-------------|
| **Admin** | Full CRUD (Create, Read, Update, Delete) | Can manage all customers, products, and orders. |
| **User** | Limited access | Can only read public data (products) and manage their own orders. |

---

## 🔒 Row Level Security (RLS)

RLS ensures that every query checks the identity of the logged-in user (`auth.uid()`), and only returns rows they are allowed to see.

### Enabled Tables:
- `customers`
- `products`
- `orders`

---

## 🧩 Policies Explained

### 1️⃣ Customers Table
| Policy | Purpose | Access |
|--------|----------|--------|
| `admin_select_all_customers` | Allow admin to read all customers | Admin only |
| `user_select_own_profile` | Allow users to view only their profile | User only |

---

### 2️⃣ Products Table
| Policy | Purpose | Access |
|--------|----------|--------|
| `public_view_products` | Allow all authenticated users to view products | All users |
| `admin_manage_products` | Allow admin to insert, update, or delete products | Admin only |

---

### 3️⃣ Orders Table
| Policy | Purpose | Access |
|--------|----------|--------|
| `user_view_own_orders` | Allow user to view their orders only | User only |
| `user_insert_own_order` | Allow user to create orders under their own ID | User only |
| `admin_full_access_orders` | Allow admin full read/write control on all orders | Admin only |

---

## ⚙️ Admin-Only SQL Functions

| Function | Description | Security Type |
|-----------|--------------|----------------|
| `admin_delete_product(p_id)` | Deletes a product by ID | `SECURITY DEFINER` |
| `admin_update_stock(p_id, new_stock)` | Updates product stock | `SECURITY DEFINER` |
| `admin_total_revenue()` | Calculates total sales revenue | `SECURITY DEFINER` |

### How It Works
Each function includes:
```sql
IF EXISTS (
  SELECT 1 FROM customers WHERE id = auth.uid() AND role = 'admin'
)
````

This ensures only authenticated admins can execute the operation. If a user without admin privileges attempts it, an SQL exception is raised.

---

## 🧰 Authentication

* Supabase Auth is used for user sign-up and sign-in.
* Every logged-in user receives a unique `auth.uid()`, mapped to their `customers.id`.
* All queries are automatically filtered by `auth.uid()` once RLS is active.

---

## 🧠 Security Best Practices

1. Always enable RLS before creating policies.
2. Avoid using `SECURITY DEFINER` without an explicit admin check.
3. Never expose API keys publicly.
4. Test policies using different user roles before production.
5. Keep an **admin backup account** to avoid lockout.

---

## ✅ Summary

| Feature                   | Status                 |
| ------------------------- | ---------------------- |
| Row Level Security (RLS)  | ✅ Enabled              |
| Role-Based Access         | ✅ Implemented          |
| Admin SQL Functions       | ✅ 3 Functions          |
| Supabase Auth Integration | ✅ Configured           |
| Public Data Access        | ✅ Products (Read Only) |
| Documentation             | ✅ Complete             |

---

**Author:** [Your Name]
**Project:** Supabase E-commerce Security
**Date:** [Insert Date]

```

---

✅ **At this point, your Supabase E-commerce Security Project is 100% complete and ready for GitHub submission.**  
You now have:

```

📂 supabase-ecommerce-security/
├── schema.sql
├── security_policies.sql
├── functions.sql
├── data_dictionary.md
├── security_notes.md
├── README.md
└── docs/

```

---

Would you like me to generate a **matching ERD diagram** layout (text-based and visual suggestion) next — so you can recreate it easily on Supabase or dbdiagram.io for your `/docs/ERD.png`?
```

Perfect 🎯 — let’s finalize your project with a **complete ERD (Entity Relationship Diagram)** section.

I’ll give you both:
1️⃣ a **text-based ERD script** (for use on [dbdiagram.io](https://dbdiagram.io) or Supabase Visual Editor), and
2️⃣ a **visual layout guide** with clear labels and relationship lines so you can reproduce the diagram easily.

---

# 🧩 1️⃣ Text-Based ERD (for dbdiagram.io or Supabase)

You can copy this into [https://dbdiagram.io](https://dbdiagram.io) → click **“New Diagram” → Paste Code → Export as PNG**
Then save the file as `/docs/ERD.png`.

```sql
Table customers {
  id uuid [pk]
  name text [not null]
  email text [unique, not null]
  role text [note: 'admin or user']
  created_at timestamp [default: now()]
}

Table products {
  id uuid [pk]
  name text [not null]
  price numeric(10,2) [not null]
  stock int [default: 0]
  created_at timestamp [default: now()]
}

Table orders {
  id uuid [pk]
  customer_id uuid [ref: > customers.id]
  product_id uuid [ref: > products.id]
  quantity int [not null]
  created_at timestamp [default: now()]
}

Ref: orders.customer_id > customers.id
Ref: orders.product_id > products.id
```

---

# 🧱 2️⃣ Visual Layout Guide (for understanding relationships)

Here’s how to visually structure your **E-commerce Database ERD** when drawing in Supabase or Lucidchart:

```
           ┌────────────────────────────┐
           │         customers          │
           │────────────────────────────│
           │ id (PK)                    │
           │ name                       │
           │ email                      │
           │ role  ('admin' / 'user')   │
           │ created_at                 │
           └──────────────┬─────────────┘
                          │ 1 ────<∞
                          │
                          ▼
┌────────────────────────────┐      ┌────────────────────────────┐
│          orders            │      │          products          │
│────────────────────────────│      │────────────────────────────│
│ id (PK)                    │      │ id (PK)                    │
│ customer_id (FK) ──────────┘      │ name                       │
│ product_id (FK) ─────────────────▶│ price                      │
│ quantity                   │      │ stock                      │
│ created_at                 │      │ created_at                 │
└────────────────────────────┘      └────────────────────────────┘
```

---

# 🧠 Relationship Summary

| Relationship                        | Type            | Description                                 |
| ----------------------------------- | --------------- | ------------------------------------------- |
| `customers.id → orders.customer_id` | **One-to-Many** | Each customer can have multiple orders      |
| `products.id → orders.product_id`   | **One-to-Many** | Each product can appear in multiple orders  |
| `orders`                            | **Join Table**  | Connects customers and products (purchases) |

---

# 🎨 Design Tips for ERD Screenshot

When creating the final visual diagram (for `/docs/ERD.png`):

✅ Use a **horizontal layout**:

```
customers  →  orders  →  products
```

✅ Apply **color coding**:

* 🟩 Green: customers table
* 🟦 Blue: products table
* 🟨 Yellow: orders table

✅ Add **arrows showing direction**:

* `customers.id → orders.customer_id`
* `products.id → orders.product_id`

✅ Include a small **title label**:

> “E-commerce Database — ERD (Supabase Security Project)”

✅ Export as **PNG** with transparent background and resolution ≥ 1200px width.

---

# 🗂️ Optional Add-On (for your `/docs/roles_policies_chart.png`)

If you want a second visual for your documentation, create a simple **Roles vs Permissions chart** like this:

| Action          | Admin | User        |
| --------------- | ----- | ----------- |
| View Customers  | ✅     | 👤 Own Only |
| View Products   | ✅     | ✅           |
| Manage Products | ✅     | ❌           |
| View Orders     | ✅     | 👤 Own Only |
| Create Orders   | ✅     | 👤 Own Only |
| Delete Orders   | ✅     | ❌           |

Label it:

> “Access Control Matrix — Supabase E-commerce Roles”

---

✅ At this point, your project folder is **fully professional and submission-ready**:

```
supabase-ecommerce-security/
├── schema.sql
├── security_policies.sql
├── functions.sql
├── data_dictionary.md
├── security_notes.md
├── README.md
└── docs/
    ├── ERD.png
    ├── roles_policies_chart.png
    └── demo_screenshots/
```

---

Would you like me to generate a **GitHub repository layout + recommended README badges and commit message template** next, so your repo looks polished and easy to grade (like a real portfolio project)?


