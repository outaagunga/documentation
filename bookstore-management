# SQL Code-Along: Build a Professional PostgreSQL **Bookstore** Database

Goal: learn and practice core SQL concepts by building a real, end-to-end PostgreSQL schema for a Bookstore.
Flow: start simple → progressively add constraints, indexes, roles, triggers, monitoring, backup, and performance tuning. Each section contains: short explanation, SQL you can run, and practical notes.

> Assumes PostgreSQL (12+) and `psql` client. Run commands in `psql` or a GUI that speaks PostgreSQL. SQL blocks are commented so you can copy-paste and run step-by-step.

---

# Part 1 — Schema Design (Logical progression & normalization)

**Why schema design matters:** organizes data, enforces consistency, improves queries and maintainability. We’ll keep good normalization: separate `authors` and reference them from `books`.

### Step 1 — Create the database

```sql
-- Run as a superuser or a user who can create DBs
CREATE DATABASE bookstore_db
  WITH ENCODING 'UTF8'
       LC_COLLATE = 'en_US.UTF-8'
       LC_CTYPE = 'en_US.UTF-8'
       TEMPLATE = template0;
-- Connect to the DB:
\c bookstore_db
```

### Step 2 — Create tables (normalized)

Brief explanation: we'll create `authors`, `books`, `members`, `borrow_records`. Use proper types, constraints, defaults, and indexes later.

```sql
-- Authors table: store canonical author info
CREATE TABLE authors (
  id SERIAL PRIMARY KEY,
  full_name VARCHAR(150) NOT NULL,
  biography TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Books table: reference authors by id (normalized)
CREATE TABLE books (
  id SERIAL PRIMARY KEY,
  title VARCHAR(200) NOT NULL,
  author_id INTEGER NOT NULL REFERENCES authors(id) ON DELETE RESTRICT,
  published_year INTEGER,
  available BOOLEAN NOT NULL DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Members table
CREATE TABLE members (
  id SERIAL PRIMARY KEY,
  full_name VARCHAR(150) NOT NULL,
  email VARCHAR(100) NOT NULL UNIQUE,
  phone VARCHAR(20),
  join_date DATE NOT NULL DEFAULT CURRENT_DATE,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Borrow records table (one row per borrow transaction)
CREATE TABLE borrow_records (
  id SERIAL PRIMARY KEY,
  member_id INTEGER NOT NULL REFERENCES members(id) ON DELETE RESTRICT,
  book_id INTEGER NOT NULL REFERENCES books(id) ON DELETE RESTRICT,
  borrow_date DATE NOT NULL DEFAULT CURRENT_DATE,
  return_date DATE,
  created_at TIMESTAMPTZ DEFAULT now()
);
```

**Question: Why enforce foreign key constraints?**

* **Referential integrity**: ensures `borrow_records.member_id` points to a real member and `book_id` points to an actual book.
* **Prevents orphan rows**: you can't have borrow record for a deleted member unless you explicitly allow it.
* **Database-level safety**: application bugs won’t silently corrupt data.
* **Enables cascading rules**: you can set ON DELETE behaviors intentionally.

> Note: I used `ON DELETE RESTRICT` for safety. Alternatives: `SET NULL`, `CASCADE` depending on business rules.

---

# Part 2 — Sample Data (inserts + demonstration of errors)

### Step 3 — Insert sample authors, books, members

```sql
-- Add authors
INSERT INTO authors (full_name) VALUES
  ('George Orwell'),
  ('Jane Austen'),
  ('Chinua Achebe');

-- Add books (use author ids from authors)
INSERT INTO books (title, author_id, published_year) VALUES
  ('1984', 1, 1949),
  ('Animal Farm', 1, 1945),
  ('Pride and Prejudice', 2, 1813),
  ('Things Fall Apart', 3, 1958);

-- Add members
INSERT INTO members (full_name, email, phone) VALUES
  ('Asha Mwangi', 'asha@example.com', '+254700000001'),
  ('Peter Okoye', 'peter@example.com', '+254700000002');
```

### Step 4 — Insert borrow records

```sql
-- Member 1 borrows book 1
INSERT INTO borrow_records (member_id, book_id) VALUES (1, 1);

-- Member 2 borrows book 4
INSERT INTO borrow_records (member_id, book_id, borrow_date) VALUES (2, 4, '2025-09-01');
```

**Bonus: insert a borrow record for a non-existent member**

```sql
-- This will fail if member 999 doesn't exist
INSERT INTO borrow_records (member_id, book_id) VALUES (999, 1);
```

**What happens & why:** PostgreSQL will return an error like `ERROR: insert or update on table "borrow_records" violates foreign key constraint "borrow_records_member_id_fkey"`. The FK prevents inserting a `member_id` that doesn't exist.

---

# Part 3 — Indexing for Performance

**Explanation:** Indexes speed reads for particular query patterns but add overhead for writes and storage.

### Task 5: Create useful indexes

Use `btree` (default) on searchable columns. Also consider `GIN`/`trigram` for fast text search.

```sql
-- Search books by title (simple prefix using btree or full-text / trigram for fuzzy)
CREATE INDEX idx_books_title ON books (title);

-- For case-insensitive / fuzzy searches consider pg_trgm:
-- (requires extension)
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE INDEX idx_books_title_trgm ON books USING gin (title gin_trgm_ops);

-- Search members by email (unique already has implicit index)
-- But for name/phone:
CREATE INDEX idx_members_full_name ON members (full_name);
CREATE INDEX idx_members_phone ON members (phone);
```

**Trade-offs of adding too many indexes:**

* **Slower writes (INSERT/UPDATE/DELETE):** each index must be updated.
* **Disk space increase.**
* **Complexity:** query planner may choose suboptimal index if many exist — you must `ANALYZE` and monitor.
* **Maintenance cost:** indexes must be vacuumed and rebuilt occasionally.

**When to add indexes:** after profiling with `EXPLAIN ANALYZE` and seeing slow queries.

---

# Part 4 — User Roles & Permissions

**Explanation:** principle of least privilege — give users only needed rights.

### Task 6–7: Create roles and assign privileges

```sql
-- Create roles (no-login roles are ideal as role groups)
CREATE ROLE librarian NOINHERIT;
CREATE ROLE assistant NOINHERIT;
CREATE ROLE borrower NOINHERIT;

-- Create login users and make them members of roles
CREATE USER librarian_user WITH PASSWORD 'lib_pass';
GRANT librarian TO librarian_user;

CREATE USER assistant_user WITH PASSWORD 'assist_pass';
GRANT assistant TO assistant_user;

CREATE USER borrower_user WITH PASSWORD 'borrow_pass';
GRANT borrower TO borrower_user;

-- Privileges:
-- Librarian: full access to all tables in schema public
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO librarian;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO librarian;

-- Assistant: can SELECT and INSERT into books & members, but not UPDATE or DELETE
GRANT SELECT, INSERT ON books, members TO assistant;
-- No grant for DELETE or UPDATE

-- Borrower: can SELECT books, members (maybe only own record), and INSERT on borrow_records to borrow
GRANT SELECT ON books TO borrower;
GRANT SELECT ON members TO borrower;
GRANT INSERT ON borrow_records TO borrower;
-- restrict borrower from modifying other members or books
```

**Testing access:** you can switch user in `psql` (superuser) to test:

```sql
-- as superuser:
SET ROLE assistant;
-- attempt update (should fail)
UPDATE books SET title = 'X' WHERE id = 1;
RESET ROLE;

-- or connect using psql:
-- psql -U assistant_user -d bookstore_db
```

**Notes:**

* For finer control, use row-level security (RLS) if users should only affect their own rows.
* For role passwords, use secure secrets management in production.

---

# Part 5 — Data Integrity (constraints + triggers + business rules)

### Task 8.1 — Prevent duplicate borrow records for same member/book on same day

Use a unique constraint on `(member_id, book_id, borrow_date)`.

```sql
ALTER TABLE borrow_records
  ADD CONSTRAINT uq_member_book_borrowdate UNIQUE (member_id, book_id, borrow_date);
```

### Task 8.2 — Prevent borrowing when the book is not available

We want a business rule:

* When inserting a borrow record: check `books.available = true` and then set it to `false`.
* When returning a book: set `books.available = true` and set `return_date`.

We implement this with a trigger + function.

```sql
-- function to prevent borrow if not available and mark unavailable when borrowed
CREATE OR REPLACE FUNCTION fn_before_insert_borrow()
RETURNS TRIGGER AS $$
BEGIN
  -- check book availability
  IF (SELECT available FROM books WHERE id = NEW.book_id) IS NOT TRUE THEN
    RAISE EXCEPTION 'Book id % is not available for borrowing', NEW.book_id;
  END IF;

  -- mark the book unavailable
  UPDATE books SET available = FALSE WHERE id = NEW.book_id;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_before_insert_borrow
  BEFORE INSERT ON borrow_records
  FOR EACH ROW
  EXECUTE FUNCTION fn_before_insert_borrow();
```

### Task 8.3 — Implement return logic

When borrower returns a book (i.e., `return_date` set), mark `books.available = true`. We'll do an `AFTER UPDATE` trigger that fires when `return_date` transitions from `NULL` to non-NULL.

```sql
CREATE OR REPLACE FUNCTION fn_after_update_borrow()
RETURNS TRIGGER AS $$
BEGIN
  IF (OLD.return_date IS NULL) AND (NEW.return_date IS NOT NULL) THEN
    -- set book available again
    UPDATE books SET available = TRUE WHERE id = NEW.book_id;
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_after_update_borrow
  AFTER UPDATE OF return_date ON borrow_records
  FOR EACH ROW
  EXECUTE FUNCTION fn_after_update_borrow();
```

### Task 8.4 — Additional scenarios & recommended rules

* **What if multiple physical copies exist?** Add `copies_total` and `copies_available` columns in `books` and decrement/increment accordingly (use CHECK to avoid negative copies).
* **When someone tries to borrow a book that doesn't exist:** FK prevents it. The app should show friendly message.
* **Returning a book before borrow\_date or invalid dates:** Add a CHECK constraint on `return_date >= borrow_date`:

```sql
ALTER TABLE borrow_records
  ADD CONSTRAINT chk_return_after_borrow CHECK (return_date IS NULL OR return_date >= borrow_date);
```

* **Soft deletes for members/books**: add `is_active boolean default true` instead of deleting rows. This preserves history.

---

# Part 6 — Backup & Monitoring

### Task 9 — Backup & restore from command line

**Backup (logical dump):**

```bash
# Dump the whole database to a compressed file
pg_dump -U your_pg_user -h your_host -F c -b -v -f bookstore_db.dump bookstore_db
# -F c => custom format, good for pg_restore
```

**Restore:**

```bash
# create empty DB first (if needed)
createdb -U your_pg_user bookstore_db_restore

# restore dump
pg_restore -U your_pg_user -d bookstore_db_restore -v bookstore_db.dump
```

**Alternative (plain SQL):**

```bash
pg_dump -U user -h host -F p -f bookstore_db.sql bookstore_db
psql -U user -d bookstore_db_restore -f bookstore_db.sql
```

### Task 10 — Monitor active connections & activity

Query system views:

```sql
-- See active connections and their queries
SELECT pid, usename, datname, client_addr, backend_start, state, query
FROM pg_stat_activity
ORDER BY backend_start DESC;

-- To see blocking/waiting queries
SELECT blocked.pid     AS blocked_pid,
       blocked.query   AS blocked_query,
       blocking.pid    AS blocking_pid,
       blocking.query  AS blocking_query
FROM pg_stat_activity blocked
JOIN pg_locks bl ON (bl.pid = blocked.pid)
JOIN pg_locks l  ON (l.locktype = bl.locktype AND l.database IS NOT DISTINCT FROM bl.database AND l.relation IS NOT DISTINCT FROM bl.relation AND l.page IS NOT DISTINCT FROM bl.page AND l.tuple IS NOT DISTINCT FROM bl.tuple AND l.transactionid IS NOT DISTINCT FROM bl.transactionid AND l.classid IS NOT DISTINCT FROM bl.classid AND l.objid IS NOT DISTINCT FROM bl.objid)
JOIN pg_stat_activity blocking ON blocking.pid = l.pid
WHERE NOT blocked.pid = blocking.pid
  AND NOT blocked.wait_event IS NULL;
```

Monitoring tools: `pg_stat_statements` (extension), `pg_top`, `pgAdmin`, Prometheus + Grafana for long term metrics.

---

# Other tasks & best practices

### 1. What happens if you update or delete a member that has borrow records?

* With `ON DELETE RESTRICT` the DB will **prevent deleting** the member while borrow records reference them.
* Alternatives:

  * **ON DELETE SET NULL**: borrow\_records.member\_id becomes `NULL` — not ideal for audit trail.
  * **ON DELETE CASCADE**: deletes borrow\_records — usually wrong because history lost.
  * **Soft delete**: set `members.is_active = false` — safe, preserves history. Recommended for production auditability.

**Suggested approach:** Use soft deletes (add `is_active boolean DEFAULT true`) and keep `ON DELETE RESTRICT` to avoid accidental data loss.

### 2. How to schedule regular backups using PostgreSQL tools or cron jobs?

Example cron job (daily at 02:00) saving compressed dumps with timestamp:

```bash
# /etc/cron.d/bookstore_backup
0 2 * * * postgres /usr/local/bin/pg_backup_bookstore.sh
```

`pg_backup_bookstore.sh`:

```bash
#!/bin/bash
OUTDIR=/var/backups/bookstore
mkdir -p "$OUTDIR"
DATE=$(date +'%Y-%m-%d_%H%M')
pg_dump -U backup_user -F c bookstore_db -f "$OUTDIR/bookstore_db_${DATE}.dump"
# Optional: rotate/remove older than 30 days
find "$OUTDIR" -type f -mtime +30 -name '*.dump' -delete
```

Use secure credentials (PGPASSFILE or environment variables), and test restores periodically.

### 3. If the Bookstore grows to millions of records, how to optimize?

**Schema & indexing:**

* Use **partitioning** (e.g., partition `borrow_records` by range on `borrow_date` or by `member_id` hash).
* Create appropriate indexes (composite indexes for common multi-column WHERE).
* Use partial indexes for low-selectivity columns (e.g., `WHERE available = true`).

**Query optimization:**

* Use `EXPLAIN ANALYZE` to inspect query plans. Add indexes or rewrite queries as needed.
* Avoid `SELECT *` — fetch only required columns.
* Use connection pooling (PgBouncer) to manage DB connections.

**Operations & maintenance:**

* Tune `shared_buffers`, `work_mem`, `maintenance_work_mem`, `effective_cache_size`.
* Regular `VACUUM` & `ANALYZE` (autovacuum tuning).
* Use read replicas for read scale-out.
* Use caching layer (Redis) for hot lookups (e.g., book availability).
* Consider materialized views for expensive aggregates (refresh periodically).

**Hardware & architecture:**

* Increase IOPS (fast disks / NVMe), RAM, CPU.
* Use partition-wise parallel queries on large datasets.
* Use monitoring dashboards and set alerts for slow queries, long transactions, or autovacuum backlog.

---

# Practical query optimization example: EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE
SELECT b.title, a.full_name
FROM books b
JOIN authors a ON a.id = b.author_id
WHERE b.title ILIKE '%Pride%';
```

* Run this; look for `Seq Scan` vs `Index Scan`. If `Seq Scan` on `books` is used and table is big, ensure you have trigram index or full-text index for such `ILIKE '%...%'` patterns.

---

# Additional Professional touches

### Audit trail / created\_by / updated\_by

Add `created_by` and `updated_by` columns (FK to an `users` table) for auditing.

### Row-level security (RLS)

If borrowers must only see their own borrow records, enable RLS on `borrow_records` and policy that allows owners to access their rows.

### Example: copies\_total & copies\_available (multi-copy support)

```sql
ALTER TABLE books ADD COLUMN copies_total INTEGER NOT NULL DEFAULT 1;
ALTER TABLE books ADD COLUMN copies_available INTEGER NOT NULL DEFAULT 1;

-- Add check constraints
ALTER TABLE books ADD CONSTRAINT chk_copies_nonneg CHECK (copies_total >= 0 AND copies_available >= 0);
ALTER TABLE books ADD CONSTRAINT chk_copies_le_total CHECK (copies_available <= copies_total);

-- Modify triggers: decrement/increment copies_available instead of toggling boolean
-- (left as exercise to adapt earlier triggers)
```

---

# Full quick checklist (what we covered)

* [x] Create DB and connect.
* [x] Tables: authors, books, members, borrow\_records.
* [x] Constraints: PKs, FKs, UNIQUE, CHECK.
* [x] Sample inserts and FK error behavior.
* [x] Indexes and tradeoffs (btree, gin/trgm).
* [x] Roles & privileges (librarian, assistant, borrower).
* [x] Business rules enforced via triggers (prevent borrow when unavailable, update availability on return).
* [x] Unique constraint to avoid duplicate borrow in same day.
* [x] Backup & restore commands, scheduling with cron.
* [x] Monitoring with `pg_stat_activity`, `pg_stat_statements`.
* [x] Handling deletes/updates (soft delete recommended).
* [x] Scaling & optimization strategies.

---

# Quick Appendix — Useful SQL snippets

```sql
-- 1. Show tables
\dt

-- 2. List constraints on a table
SELECT conname, pg_get_constraintdef(c.oid) as definition
FROM pg_constraint c
JOIN pg_class t ON c.conrelid = t.oid
WHERE t.relname = 'borrow_records';

-- 3. Enable pg_stat_statements (in postgresql.conf add shared_preload_libraries = 'pg_stat_statements')
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
SELECT * FROM pg_stat_statements ORDER BY total_time DESC LIMIT 10;

-- 4. Create materialized view for heavy reporting
CREATE MATERIALIZED VIEW mv_book_popularity AS
SELECT b.id, b.title, COUNT(br.id) AS times_borrowed
FROM books b
LEFT JOIN borrow_records br ON br.book_id = b.id
GROUP BY b.id, b.title
WITH NO DATA;

-- To populate:
REFRESH MATERIALIZED VIEW mv_book_popularity;
```

---

# Final notes — How to use this guide

1. Run the SQL sections in order.
2. After you add sample data, deliberately try the error cases (e.g., insert borrow for non-existent member) to see FK enforcement.
3. Use `EXPLAIN ANALYZE` before/after creating indexes to observe performance differences.
4. Evolve the schema: add `copies` if physical inventory expands; add RLS for multi-tenant setups; add audit columns for compliance.

If you want, I can:

* Convert this guide into a runnable SQL script file with comments and conditional guards, or
* Provide a version that includes `copies_total/copies_available` logic and adapted triggers, or
* Produce step-by-step exercises with expected outputs and `EXPLAIN ANALYZE` snapshots for you to run and compare.

Which of those would you like next? (I can produce the SQL script now.)

