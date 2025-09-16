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

