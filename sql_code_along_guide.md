Here’s a **comprehensive, structured SQL code-along guide** designed to take you step-by-step through every major SQL concept using a **realistic project-based approach**. The guide uses **Supabase** (PostgreSQL-based) and builds a **School Management System** project, while progressively introducing SQL concepts from basics to advanced topics.

---

# **SQL Code-Along: School Management System**

> **Goal:** Build a database for a school to manage students, courses, teachers, and enrollments.

This guide assumes you are using **Supabase** as your SQL environment, but it will also work in any PostgreSQL-compatible system.

---

## **1. Project Setup & Understanding Schemas**

### **What is a Schema?**

* A **schema** is a logical container that groups database objects such as **tables, views, functions, and sequences**.
* Think of it like **folders in a file system** — it helps organize your database.

In Supabase, schemas help you:

* Keep related tables grouped.
* Manage permissions at the schema level.
* Avoid naming conflicts between tables.

---

### **Task 1.1 – Create a New Schema**

```sql
-- Create a schema called 'school'
CREATE SCHEMA school;

-- Verify schema creation
SELECT schema_name FROM information_schema.schemata;
```

> ✅ **Explanation:**
>
> * `CREATE SCHEMA` creates a new schema.
> * Supabase provides a default schema called `public`, but creating custom schemas is best practice for organization.

---

## **2. Creating Tables (DDL Basics)**

### **Understanding DDL**

* **Data Definition Language (DDL)** commands define the **structure of the database**.
* Core DDL commands: `CREATE`, `ALTER`, `DROP`.

---

### **Task 2.1 – Create a Students Table**

We'll create a table `students` inside the `school` schema.

```sql
CREATE TABLE school.students (
    id SERIAL PRIMARY KEY,       -- Auto-incrementing student ID
    name TEXT NOT NULL,          -- Student's full name, required
    age INTEGER,                 -- Student's age
    grade TEXT,                   -- Example: "A", "B", "C"
    enrolled_at TIMESTAMP DEFAULT now()  -- Auto-set current timestamp
);
```

> ✅ **Explanation:**
>
> * `SERIAL` automatically generates unique IDs.
> * `NOT NULL` ensures the name field cannot be empty.
> * `DEFAULT now()` sets a default timestamp when a record is created.

---

### **Task 2.2 – Create Additional Tables**

We'll add two more tables: `teachers` and `courses`.

```sql
-- Table for teachers
CREATE TABLE school.teachers (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    subject TEXT NOT NULL,
    hired_at TIMESTAMP DEFAULT now()
);

-- Table for courses
CREATE TABLE school.courses (
    id SERIAL PRIMARY KEY,
    course_name TEXT NOT NULL,
    description TEXT,
    teacher_id INTEGER REFERENCES school.teachers(id)
);
```

> ✅ **New Concepts:**
>
> * `REFERENCES` creates a **foreign key** relationship.
> * Each course is linked to a teacher by `teacher_id`.

---

## **3. Inserting Data (DML Basics)**

### **Understanding DML**

* **Data Manipulation Language (DML)** commands modify data.
* Core DML commands: `INSERT`, `UPDATE`, `DELETE`.

---

### **Task 3.1 – Insert Sample Records**

```sql
-- Insert sample students
INSERT INTO school.students (name, age, grade)
VALUES 
  ('Alice Johnson', 19, 'A'),
  ('Bob Smith', 17, 'B'),
  ('Charlie Brown', 20, 'C');

-- Insert sample teachers
INSERT INTO school.teachers (name, subject)
VALUES
  ('Mr. Thomas', 'Mathematics'),
  ('Mrs. Davis', 'History');

-- Insert sample courses
INSERT INTO school.courses (course_name, description, teacher_id)
VALUES
  ('Algebra I', 'Introductory algebra course', 1),
  ('World History', 'History of the modern world', 2);
```

> ✅ **Tip:** Always check your insertions with:

```sql
SELECT * FROM school.students;
SELECT * FROM school.teachers;
SELECT * FROM school.courses;
```

---

## **4. Querying Data (DQL Basics)**

### **Understanding SELECT**

* **Data Query Language (DQL)** is used to **retrieve data**.
* `SELECT` is the main statement used for queries.

---

### **Task 4.1 – Basic SELECT Queries**

```sql
-- Retrieve all students
SELECT * FROM school.students;

-- Retrieve only name and grade
SELECT name, grade FROM school.students;

-- Retrieve students older than 18
SELECT * FROM school.students
WHERE age > 18;
```

---

### **Task 4.2 – Filtering & Sorting**

```sql
-- Get students with grade 'A'
SELECT * FROM school.students
WHERE grade = 'A';

-- Sort students by age (oldest first)
SELECT * FROM school.students
ORDER BY age DESC;

-- Limit results to first 2 records
SELECT * FROM school.students
LIMIT 2;
```

---

### **Task 4.3 – Aggregations & Grouping**

```sql
-- Count number of students by grade
SELECT grade, COUNT(*) AS total_students
FROM school.students
GROUP BY grade;

-- Show grades with more than 1 student
SELECT grade, COUNT(*)
FROM school.students
GROUP BY grade
HAVING COUNT(*) > 1;
```

---

## **5. Working with Joins**

### **Understanding Joins**

Joins allow combining data from multiple tables.

---

### **Task 5.1 – INNER JOIN**

```sql
-- List courses with teacher names
SELECT c.course_name, t.name AS teacher
FROM school.courses c
INNER JOIN school.teachers t ON c.teacher_id = t.id;
```

---

### **Task 5.2 – LEFT JOIN**

```sql
-- Show all teachers, including those without assigned courses
SELECT t.name, c.course_name
FROM school.teachers t
LEFT JOIN school.courses c ON t.id = c.teacher_id;
```

---

### **Task 5.3 – RIGHT & FULL OUTER JOIN**

```sql
-- RIGHT JOIN example
SELECT t.name, c.course_name
FROM school.teachers t
RIGHT JOIN school.courses c ON t.id = c.teacher_id;

-- FULL OUTER JOIN example
SELECT t.name, c.course_name
FROM school.teachers t
FULL OUTER JOIN school.courses c ON t.id = c.teacher_id;
```

---

## **6. Advanced Queries**

### **Subqueries**

```sql
-- Find students older than the average age
SELECT * FROM school.students
WHERE age > (SELECT AVG(age) FROM school.students);
```

---

### **Common Table Expressions (CTEs)**

```sql
-- Use CTE to calculate average age
WITH avg_age AS (
    SELECT AVG(age) AS average_age FROM school.students
)
SELECT s.name, s.age
FROM school.students s, avg_age
WHERE s.age > avg_age.average_age;
```

---

### **Window Functions**

```sql
-- Rank students by age
SELECT name, age,
       RANK() OVER (ORDER BY age DESC) AS age_rank
FROM school.students;
```

---

## **7. Indexes & Performance**

```sql
-- Create an index on the 'name' column
CREATE INDEX idx_students_name ON school.students(name);

-- Check all indexes
SELECT * FROM pg_indexes WHERE schemaname = 'school';
```

> ✅ **Note:** Indexes speed up search queries but slow down inserts/updates.

---

## **8. Transactions**

Transactions ensure **data integrity** by grouping operations.

```sql
BEGIN;

UPDATE school.students
SET grade = 'B'
WHERE id = 1;

DELETE FROM school.students
WHERE id = 3;

-- Commit if all is good
COMMIT;

-- Or rollback if there's an issue
ROLLBACK;
```

---

## **9. Altering & Dropping**

```sql
-- Add a new column to students
ALTER TABLE school.students ADD COLUMN email TEXT;

-- Remove a column
ALTER TABLE school.students DROP COLUMN email;

-- Drop table completely
DROP TABLE school.students;

-- Drop schema (and everything inside it!)
DROP SCHEMA school CASCADE;
```

---

## **10. Final Practice Challenge**

**Scenario:**
Create a table `enrollments` to track which students are enrolled in which courses.

**Requirements:**

1. Columns:

   * `id` (primary key)
   * `student_id` (foreign key to students)
   * `course_id` (foreign key to courses)
   * `enrolled_on` (default now)
2. Insert sample data.
3. Query to show each student with their enrolled courses.

---

## **Next Steps**

* Explore **views** and **stored procedures** in PostgreSQL.
* Learn **Supabase-specific features**, like **Row Level Security (RLS)**.
* Build a **Supabase dashboard** and connect to a frontend like **React**.

---

## **Summary**

This guide covered:

* **DDL** (Schemas, Tables, Constraints)
* **DML** (Insert, Update, Delete)
* **DQL** (Select, Filters, Grouping)
* **Joins**
* **Subqueries, CTEs, Window Functions**
* **Indexes & Performance**
* **Transactions**
* **Schema Management**

By following along, you now have a strong foundation for building **real-world database projects** using SQL and Supabase.




# SQL Code-Along: School Management System

**Goal:** Build a database for a school to manage **students**, **courses**, **teachers**, and **enrollments**.
This guide uses **Supabase (PostgreSQL)** but is compatible with any PostgreSQL environment. Every section is **code-along**: runnable SQL with short comments and clear explanations. Run blocks in Supabase SQL Editor or `psql`.

---

## 1. Project Setup & Understanding Schemas

### What is a Schema?

A **schema** is a logical container for database objects (tables, views, functions, sequences). Think of it as folders in a file system. In Supabase (Postgres) schemas help you:

* Keep related tables grouped.
* Manage permissions at schema level.
* Avoid naming conflicts.

---

### 1.1 Quick history & SQL dialects (context)

* **SQL** = Structured Query Language (ANSI standard).
* Major dialects and common differences:

  * **PostgreSQL** (rich features, used by Supabase): `LIMIT`, `RETURNING`, `plpgsql`.
  * **MySQL / MariaDB**: `LIMIT`, different procedural extensions.
  * **SQL Server (T-SQL)**: uses `TOP` for row limits, different system functions.
  * **Oracle (PL/SQL)**: different DDL/DML quirks, sequences usage.
* When following examples: these are written for **PostgreSQL**. Dialect notes are added where needed.

---

### 1.2 Setting up environment (Supabase / PostgreSQL)

* **Supabase**: create a project → use SQL editor or connect via `psql` / DBeaver / pgAdmin.
* **Local Postgres** (example):

```bash
# install postgresql (platform dependent), then:
createdb school_db
psql -d school_db
```

* **Connect with psql**:

```bash
psql -h <host> -U <user> -d <database>
```

* **Recommended SQL clients:** DBeaver, pgAdmin, TablePlus, Supabase SQL Editor.

---

### Task 1.1 – Create a New Schema

```sql
-- Create a schema called 'school'
CREATE SCHEMA IF NOT EXISTS school;

-- Verify schema creation (lists schemas)
SELECT schema_name
FROM information_schema.schemata
WHERE schema_name = 'school';
```

✅ **Explanation:** `CREATE SCHEMA` creates the logical container. Supabase has `public` by default; `school` keeps project objects isolated.

---

## 2. Creating Tables (DDL Basics)

### Understanding DDL

**DDL** (Data Definition Language) defines structure: `CREATE`, `ALTER`, `DROP`. We’ll create tables inside the `school` schema.

---

### Task 2.1 – Create a Students Table

```sql
-- Create students table
CREATE TABLE IF NOT EXISTS school.students (
    id SERIAL PRIMARY KEY,                 -- Auto-incrementing ID
    name TEXT NOT NULL,                    -- Required
    age INTEGER,                           -- Age in years
    grade TEXT,                            -- e.g., 'A', 'B'
    enrolled_at TIMESTAMP DEFAULT now()    -- Default to current time
);
```

✅ **Explanation:** `SERIAL` = auto-increment (Postgres). `NOT NULL` forces a value. `DEFAULT now()` sets timestamp if none provided.

---

### Task 2.2 – Create Additional Tables (teachers, courses)

```sql
-- Teachers
CREATE TABLE IF NOT EXISTS school.teachers (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    subject TEXT NOT NULL,
    hired_at TIMESTAMP DEFAULT now()
);

-- Courses
CREATE TABLE IF NOT EXISTS school.courses (
    id SERIAL PRIMARY KEY,
    course_name TEXT NOT NULL,
    description TEXT,
    teacher_id INTEGER REFERENCES school.teachers(id)  -- foreign key
);
```

✅ **New Concepts:** `REFERENCES` creates a foreign key linking `courses.teacher_id` → `teachers.id`.

---

## 3. Inserting Data (DML Basics)

### Understanding DML

**DML** (Data Manipulation Language) modifies data: `INSERT`, `UPDATE`, `DELETE`.

---

### Task 3.1 – Insert Sample Records

```sql
-- Insert sample students
INSERT INTO school.students (name, age, grade)
VALUES 
  ('Alice Johnson', 19, 'A'),
  ('Bob Smith', 17, 'B'),
  ('Charlie Brown', 20, 'C');

-- Insert sample teachers
INSERT INTO school.teachers (name, subject)
VALUES
  ('Mr. Thomas', 'Mathematics'),
  ('Mrs. Davis', 'History');

-- Insert sample courses (teacher_id uses teachers inserted above)
INSERT INTO school.courses (course_name, description, teacher_id)
VALUES
  ('Algebra I', 'Introductory algebra course', 1),
  ('World History', 'History of the modern world', 2);
```

✅ **Tip:** verify inserts:

```sql
SELECT * FROM school.students;
SELECT * FROM school.teachers;
SELECT * FROM school.courses;
```

---

## 4. Querying Data (DQL Basics)

### Understanding SELECT

**DQL** (Data Query Language): `SELECT` retrieves data. Below we cover basics, filtering, sorting, groups, aggregation.

---

### Task 4.1 – Basic SELECT Queries

```sql
-- Retrieve all students
SELECT * FROM school.students;

-- Retrieve only name and grade
SELECT name, grade FROM school.students;

-- Retrieve students older than 18
SELECT * FROM school.students WHERE age > 18;
```

---

### Task 4.2 – Filtering & Sorting (full coverage)

```sql
-- Comparison operators: =, !=, >, <, >=, <=
SELECT * FROM school.students WHERE age >= 18;

-- Logical operators: AND, OR, NOT
SELECT * FROM school.students WHERE grade = 'A' AND age >= 18;
SELECT * FROM school.students WHERE grade = 'A' OR grade = 'B';
SELECT * FROM school.students WHERE NOT (grade = 'C');

-- Pattern matching: LIKE, wildcards % and _
SELECT * FROM school.students WHERE name LIKE 'A%';   -- starts with A
SELECT * FROM school.students WHERE name LIKE '%son';  -- ends with 'son'
SELECT * FROM school.students WHERE name LIKE '_ob%';  -- second char 'o'

-- Ranges and lists: BETWEEN, IN
SELECT * FROM school.students WHERE age BETWEEN 16 AND 19;
SELECT * FROM school.students WHERE grade IN ('A','B');

-- NULL checks
SELECT * FROM school.students WHERE age IS NULL;
SELECT * FROM school.students WHERE age IS NOT NULL;

-- Sorting: ORDER BY
SELECT * FROM school.students ORDER BY age DESC, name ASC;

-- Limit & Offset
SELECT * FROM school.students ORDER BY id LIMIT 2 OFFSET 1;  -- pagination
```

---

### Task 4.3 – Aggregations & Grouping

```sql
-- Aggregates: COUNT, SUM (example on hypothetical fees), AVG, MIN, MAX
SELECT COUNT(*) AS total_students, AVG(age) AS avg_age FROM school.students;

-- Count by grade
SELECT grade, COUNT(*) AS total_students
FROM school.students
GROUP BY grade;

-- HAVING filters groups
SELECT grade, COUNT(*) AS ct
FROM school.students
GROUP BY grade
HAVING COUNT(*) > 1;
```

✅ **Note:** `WHERE` filters rows before grouping; `HAVING` filters groups after grouping.

---

## 5. Working with Joins

### Understanding Joins

Joins combine rows from multiple tables based on related columns.

---

### Task 5.1 – INNER JOIN

```sql
-- List courses with teacher names
SELECT c.course_name, t.name AS teacher
FROM school.courses c
INNER JOIN school.teachers t ON c.teacher_id = t.id;
```

---

### Task 5.2 – LEFT JOIN

```sql
-- Show all teachers, including those without assigned courses
SELECT t.name, c.course_name
FROM school.teachers t
LEFT JOIN school.courses c ON t.id = c.teacher_id;
```

---

### Task 5.3 – RIGHT & FULL OUTER JOIN

```sql
-- RIGHT JOIN: show all courses, even if teacher missing
SELECT t.name, c.course_name
FROM school.teachers t
RIGHT JOIN school.courses c ON t.id = c.teacher_id;

-- FULL OUTER JOIN: rows from both sides
SELECT t.name AS teacher, c.course_name
FROM school.teachers t
FULL OUTER JOIN school.courses c ON t.id = c.teacher_id;
```

---

### Additional Join Types & Techniques

```sql
-- CROSS JOIN (cartesian product) - combine every student with every course
SELECT s.name AS student, c.course_name
FROM school.students s
CROSS JOIN school.courses c
LIMIT 20;

-- SELF JOIN (compare rows within same table) - students in same grade pairs
SELECT a.name AS student_a, b.name AS student_b, a.grade
FROM school.students a
JOIN school.students b ON a.grade = b.grade AND a.id < b.id;

-- Joining multiple tables (enrollments will be created later)
-- Example: student -> enrollment -> course -> teacher (see Module 10 final query)
```

---

## 6. Advanced Queries

### Subqueries

```sql
-- Find students older than the average age
SELECT * FROM school.students
WHERE age > (SELECT AVG(age) FROM school.students);

-- Subquery in FROM (derived table)
SELECT s.name, avg_age.average_age
FROM school.students s
JOIN (
  SELECT AVG(age) AS average_age FROM school.students
) avg_age ON TRUE
WHERE s.age > avg_age.average_age;
```

---

### Common Table Expressions (CTEs)

```sql
-- Simple CTE example
WITH avg_age AS (
  SELECT AVG(age)::NUMERIC AS average_age FROM school.students
)
SELECT s.name, s.age
FROM school.students s, avg_age
WHERE s.age > avg_age.average_age;

-- Recursive CTE example (organizational units)
CREATE TABLE IF NOT EXISTS school.org_units (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  parent_id INTEGER REFERENCES school.org_units(id)
);

-- Insert sample hierarchy (run once)
INSERT INTO school.org_units (name, parent_id) VALUES
  ('School', NULL)
ON CONFLICT DO NOTHING;

INSERT INTO school.org_units (name, parent_id)
SELECT 'Science Dept', id FROM school.org_units WHERE name = 'School' LIMIT 1
ON CONFLICT DO NOTHING;

-- Recursive CTE to walk the tree
WITH RECURSIVE unit_tree AS (
  SELECT id, name, parent_id, 1 AS depth FROM school.org_units WHERE parent_id IS NULL
  UNION ALL
  SELECT u.id, u.name, u.parent_id, ut.depth + 1
  FROM school.org_units u
  JOIN unit_tree ut ON u.parent_id = ut.id
)
SELECT * FROM unit_tree ORDER BY depth, id;
```

---

### Window Functions

```sql
-- Ranking and analytic functions
SELECT name, age,
       ROW_NUMBER() OVER (ORDER BY age DESC)     AS row_num,
       RANK()       OVER (ORDER BY age DESC)     AS rank_age,
       DENSE_RANK() OVER (ORDER BY age DESC)     AS dense_rank_age,
       LAG(age, 1)  OVER (ORDER BY age DESC)     AS previous_age,
       LEAD(age, 1) OVER (ORDER BY age DESC)     AS next_age,
       NTILE(4)     OVER (ORDER BY age)          AS quartile,
       CUME_DIST()  OVER (ORDER BY age)          AS cumulative_dist
FROM school.students;
```

✅ **Use case:** window functions compute values across rows related to current row without collapsing rows.

---

## 7. Indexes & Performance

### Create & Inspect Indexes

```sql
-- Create an index on students.name to speed text searches
CREATE INDEX IF NOT EXISTS idx_students_name ON school.students(name);

-- Create index on students.age
CREATE INDEX IF NOT EXISTS idx_students_age ON school.students(age);

-- View indexes for the schema
SELECT * FROM pg_indexes WHERE schemaname = 'school';
```

✅ **Note:** Indexes speed reads but slow writes; create them where queries filter/join frequently.

### Cluster (Postgres-specific)

```sql
-- Cluster table by index (reorders table physically)
CLUSTER school.students USING idx_students_age;
-- Note: CLUSTER is one-time; maintenance needed to keep ordering.
```

---

## 8. Transactions

### Basics: BEGIN / COMMIT / ROLLBACK

```sql
-- Start transaction
BEGIN;

-- Multiple steps
UPDATE school.students SET grade = 'B' WHERE id = 1;
DELETE FROM school.students WHERE id = 3;

-- If everything OK
COMMIT;

-- If error or change of mind
ROLLBACK;
```

### ACID & Isolation Levels

* **ACID**: Atomicity, Consistency, Isolation, Durability.
* Postgres isolation levels: `READ UNCOMMITTED` (treated as `READ COMMITTED`), `READ COMMITTED` (default), `REPEATABLE READ`, `SERIALIZABLE`.

```sql
-- Set isolation level for a transaction
BEGIN;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
-- run statements...
COMMIT;
```

✅ **When to use:** use `SERIALIZABLE` for strict consistency when concurrency issues matter.

---

## 9. Altering & Dropping (DDL continued)

### Add / Modify / Drop Columns & Constraints

```sql
-- Add a new column (email)
ALTER TABLE school.students ADD COLUMN email TEXT;

-- Add UNIQUE constraint on email
ALTER TABLE school.students ADD CONSTRAINT unique_students_email UNIQUE (email);

-- Change column type
ALTER TABLE school.students ALTER COLUMN age TYPE SMALLINT;

-- Drop a column
ALTER TABLE school.students DROP COLUMN IF EXISTS email;

-- Drop a constraint
ALTER TABLE school.students DROP CONSTRAINT IF EXISTS unique_students_email;
```

### Drop tables and schema

```sql
-- Drop table completely
DROP TABLE IF EXISTS school.students CASCADE;

-- Drop schema and everything inside (use carefully)
DROP SCHEMA IF EXISTS school CASCADE;
```

---

## 10. Final Practice Challenge — Enrollments (end-to-end)

### 10.1 Create `enrollments` table

```sql
CREATE TABLE IF NOT EXISTS school.enrollments (
  id SERIAL PRIMARY KEY,
  student_id INTEGER NOT NULL REFERENCES school.students(id) ON DELETE CASCADE,
  course_id INTEGER NOT NULL REFERENCES school.courses(id) ON DELETE CASCADE,
  enrolled_on TIMESTAMP DEFAULT now(),
  UNIQUE (student_id, course_id)  -- prevent duplicate enrollments
);
```

✅ **Explanation:** `ON DELETE CASCADE` removes enrollments when student or course is deleted. `UNIQUE(student_id, course_id)` prevents duplicate enrollments.

---

### 10.2 Insert sample enrollments

```sql
-- Enroll Alice (id 1) in Algebra I (id 1) and Bob (id 2) in World History (id 2)
INSERT INTO school.enrollments (student_id, course_id)
VALUES
  (1, 1),
  (2, 2),
  (1, 2);  -- Alice also enrolled in World History
```

---

### 10.3 Query: show each student with their enrolled courses

```sql
SELECT s.id AS student_id, s.name AS student_name,
       c.course_name, c.id AS course_id, t.name AS teacher_name,
       e.enrolled_on
FROM school.enrollments e
JOIN school.students s ON e.student_id = s.id
JOIN school.courses c ON e.course_id = c.id
LEFT JOIN school.teachers t ON c.teacher_id = t.id
ORDER BY s.name, c.course_name;
```

---

## Extra: Subtopics explicitly required by checklist (quick runnable examples)

### INSERT ... SELECT (insert from query)

```sql
-- Create alumni table from students older than 21
CREATE TABLE IF NOT EXISTS school.alumni (
  id INTEGER PRIMARY KEY,
  name TEXT,
  age INTEGER,
  grade TEXT,
  enrolled_at TIMESTAMP
);

INSERT INTO school.alumni (id, name, age, grade, enrolled_at)
SELECT id, name, age, grade, enrolled_at FROM school.students WHERE age > 21;
```

### TRUNCATE vs DELETE vs DROP (examples)

```sql
-- DELETE specific rows (can rollback)
BEGIN;
DELETE FROM school.students WHERE id = 999;
ROLLBACK;  -- undone

-- TRUNCATE: remove ALL rows fast (resets identity in Postgres if specified)
TRUNCATE TABLE school.alumni RESTART IDENTITY;

-- DROP: remove table definition & data
DROP TABLE IF EXISTS school.alumni;
```

### Scalar functions: string / numeric / date examples

```sql
SELECT name,
       LOWER(name) AS lower_name,
       UPPER(name) AS upper_name,
       TRIM(name) AS trimmed_name,
       SUBSTRING(name FROM 1 FOR 4) AS first4,
       CONCAT(name, ' (', grade, ')') AS label,
       ROUND(AVG(age) OVER ()::numeric, 2) AS avg_age_rounded,
       AGE(now(), enrolled_at) AS time_since_enroll,
       EXTRACT(YEAR FROM enrolled_at) AS enroll_year
FROM school.students;
```

### Correlated subquery example

```sql
-- Students older than the average age in their grade
SELECT s1.*
FROM school.students s1
WHERE s1.age > (
  SELECT AVG(s2.age) FROM school.students s2 WHERE s2.grade = s1.grade
);
```

### Views

```sql
-- Create a view of students and their course counts
CREATE OR REPLACE VIEW school.student_course_counts AS
SELECT s.id, s.name, COUNT(e.course_id) AS course_count
FROM school.students s
LEFT JOIN school.enrollments e ON s.id = e.student_id
GROUP BY s.id, s.name;

-- Query the view
SELECT * FROM school.student_course_counts;
```

### Stored function (returns a value) and stored procedure (performs actions)

```sql
-- Function: return student's grade
CREATE OR REPLACE FUNCTION school.get_student_grade(p_student_id INTEGER)
RETURNS TEXT LANGUAGE plpgsql AS $$
DECLARE
  v_grade TEXT;
BEGIN
  SELECT grade INTO v_grade FROM school.students WHERE id = p_student_id;
  RETURN COALESCE(v_grade, 'U');
END;
$$;

-- Call function
SELECT school.get_student_grade(1);

-- Procedure: mark student as promoted (example action)
CREATE OR REPLACE PROCEDURE school.promote_student(p_student_id INTEGER)
LANGUAGE plpgsql AS $$
BEGIN
  UPDATE school.students SET grade = 'Promoted' WHERE id = p_student_id;
END;
$$;

-- Call procedure
CALL school.promote_student(2);
```

### EXPLAIN / EXPLAIN ANALYZE (performance)

```sql
-- Show estimated query plan (no execution)
EXPLAIN SELECT * FROM school.students WHERE age > 18;

-- Run and show actual timing and rows
EXPLAIN ANALYZE SELECT * FROM school.students WHERE age > 18;
```

---

## Best practices & summary notes

* **Security:** Always use parameterized queries from application code to prevent SQL injection. Supabase client libraries use prepared statements by default.
* **Naming:** Use consistent naming: `snake_case`, plural table names (`students`), columns descriptive and consistent.
* **Indexes:** Index columns used in `WHERE` and `JOIN` but avoid over-indexing.
* **Transactions:** Group related writes in transactions to maintain consistency.
* **Avoid `SELECT *`** in production: select only needed columns.
* **Supabase extras:** Learn Row Level Security (RLS) and `auth.uid()` for per-user data access in Supabase.

---

## Ready-to-run order (quick checklist)

1. Create schema: `CREATE SCHEMA school;`
2. Create `students`, `teachers`, `courses`.
3. Insert sample `students`, `teachers`, `courses`.
4. Create `enrollments` and `org_units` if following recursive CTE example.
5. Insert enrollments.
6. Run join/query examples and analytics (`EXPLAIN ANALYZE` to inspect).

---

If you want, I’ll now:

* Produce a **single consolidated SQL script** (one file) that runs these steps in order (with guards like `IF NOT EXISTS`) so you can paste and execute top-to-bottom in Supabase; **or**
* Export this as a **PDF** or **Anki flashcards** for study.

Tell me which output you prefer and I’ll generate it right here.

