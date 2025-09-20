
Got it ✅ — I'll prepare sample **East Africa–inspired** insert statements (Kenya, Tanzania, Uganda, Rwanda, Ethiopia). I’ll fix a couple of SQL issues first so the schema works properly, then populate with **10 rows per table**.

### Schema Fixes

1. `FOREIGN KEY` must reference a parent table explicitly.
2. `death_year` should allow `NULL` for living artists.

Here’s the refined schema + inserts:

```sql
CREATE SCHEMA museum_collection;

-- Table: Artists
CREATE TABLE museum_collection.artists (
    artist_id      SERIAL PRIMARY KEY,
    artist_name    VARCHAR(255) NOT NULL,
    nationality    VARCHAR(100),
    birth_year     DATE NOT NULL,
    death_year     DATE
);

-- Table: Artworks
CREATE TABLE museum_collection.artworks (
    artwork_id     SERIAL PRIMARY KEY,
    title          VARCHAR(255) NOT NULL,
    artist_id      INT NOT NULL,
    year_created   DATE NOT NULL,
    CONSTRAINT fk_artist FOREIGN KEY (artist_id) REFERENCES museum_collection.artists(artist_id)
);

-- Table: Museums
CREATE TABLE museum_collection.museums (
    museum_id      SERIAL PRIMARY KEY,
    museum_name    VARCHAR(255) NOT NULL UNIQUE,
    city           VARCHAR(100) NOT NULL,
    country        VARCHAR(100) NOT NULL
);

-- Table: Exhibitions
CREATE TABLE museum_collection.exhibitions (
    exhibition_id    SERIAL PRIMARY KEY,
    exhibition_name  VARCHAR(255) NOT NULL,
    museum_id        INT NOT NULL,
    start_date       DATE NOT NULL,
    end_date         DATE NOT NULL,
    CONSTRAINT fk_museum FOREIGN KEY (museum_id) REFERENCES museum_collection.museums(museum_id)
);

---------------------------------------------------
-- Insert Data
---------------------------------------------------

-- Artists (10 rows)
INSERT INTO museum_collection.artists (artist_name, nationality, birth_year, death_year) VALUES
('Elimo Njau', 'Kenyan', '1932-05-15', NULL),
('Jak Katarikawe', 'Ugandan', '1940-06-10', '2019-04-15'),
('Tinga Tinga', 'Tanzanian', '1932-07-15', '1972-01-01'),
('Skunder Boghossian', 'Ethiopian', '1937-07-22', '2003-05-04'),
('Theresa Musoke', 'Ugandan', '1944-09-11', NULL),
('Moses Nyawanda', 'Kenyan', '1950-02-20', NULL),
('Elias Jengo', 'Tanzanian', '1943-08-05', NULL),
('Aida Muluneh', 'Ethiopian', '1974-12-25', NULL),
('Ismael Kateregga', 'Ugandan', '1980-03-13', NULL),
('Michael Soi', 'Kenyan', '1972-04-29', NULL);

-- Artworks (10 rows)
INSERT INTO museum_collection.artworks (title, artist_id, year_created) VALUES
('The African Dancers', 1, '1965-01-01'),
('Cattle Herds in Kampala', 2, '1975-01-01'),
('Colorful Savannah', 3, '1968-01-01'),
('Night Journey', 4, '1985-01-01'),
('The Hornbill', 5, '1978-01-01'),
('Lake Victoria Sunrise', 6, '1989-01-01'),
('Village Life in Tanzania', 7, '1992-01-01'),
('The World is 9', 8, '2016-01-01'),
('Market Day in Kampala', 9, '2015-01-01'),
('Political Circus', 10, '2018-01-01');

-- Museums (10 rows)
INSERT INTO museum_collection.museums (museum_name, city, country) VALUES
('Nairobi National Museum', 'Nairobi', 'Kenya'),
('Uganda Museum', 'Kampala', 'Uganda'),
('National Museum of Tanzania', 'Dar es Salaam', 'Tanzania'),
('National Museum of Ethiopia', 'Addis Ababa', 'Ethiopia'),
('Kigali Genocide Memorial Museum', 'Kigali', 'Rwanda'),
('Bomas of Kenya Cultural Centre', 'Nairobi', 'Kenya'),
('Natural History Museum Arusha', 'Arusha', 'Tanzania'),
('Ethiopian National Gallery', 'Addis Ababa', 'Ethiopia'),
('Kabale Museum', 'Kabale', 'Uganda'),
('Rwenzori Art Centre', 'Kasese', 'Uganda');

-- Exhibitions (10 rows)
INSERT INTO museum_collection.exhibitions (exhibition_name, museum_id, start_date, end_date) VALUES
('East African Contemporary Art', 1, '2023-01-10', '2023-03-10'),
('Ugandan Heritage Showcase', 2, '2023-04-01', '2023-05-15'),
('Tinga Tinga Retrospective', 3, '2023-06-01', '2023-08-01'),
('Ancient Ethiopia: Axum & Beyond', 4, '2023-09-01', '2023-11-30'),
('Rwandan Cultural Memory', 5, '2024-01-15', '2024-04-15'),
('Kenya Contemporary Painters', 6, '2024-05-01', '2024-07-01'),
('Wildlife and Heritage of Arusha', 7, '2024-07-10', '2024-09-10'),
('Modern Ethiopian Photography', 8, '2024-09-15', '2024-11-15'),
('Western Uganda Traditions', 9, '2024-12-01', '2025-02-01'),
('Rwenzori Sculpture Biennale', 10, '2025-03-01', '2025-05-01');
```

---

👉 Do you want me to also generate **joins & sample SELECT queries** (e.g., list all artworks with their artist and museum exhibition) so you can test the dataset immediately?
Perfect ✅ — here are some **sample SELECT queries** you can use to test the dataset. They’ll join across tables so you can see the relationships between **artists, artworks, museums, and exhibitions**.

---

### 1. List all artworks with their artist names

```sql
SELECT a.title AS artwork_title,
       ar.artist_name,
       ar.nationality,
       a.year_created
FROM museum_collection.artworks a
JOIN museum_collection.artists ar 
     ON a.artist_id = ar.artist_id
ORDER BY a.year_created;
```

---

### 2. Show all exhibitions with their hosting museum

```sql
SELECT e.exhibition_name,
       m.museum_name,
       m.city,
       m.country,
       e.start_date,
       e.end_date
FROM museum_collection.exhibitions e
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
ORDER BY e.start_date;
```

---

### 3. Find artworks and the exhibitions happening in the same country as the artist’s nationality

```sql
SELECT ar.artist_name,
       ar.nationality,
       aw.title AS artwork_title,
       m.museum_name,
       e.exhibition_name
FROM museum_collection.artworks aw
JOIN museum_collection.artists ar 
     ON aw.artist_id = ar.artist_id
JOIN museum_collection.exhibitions e 
     ON e.museum_id IN (
        SELECT museum_id FROM museum_collection.museums 
        WHERE country = ar.nationality
     )
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id;
```

---

### 4. List all living artists and their artworks

```sql
SELECT ar.artist_name,
       aw.title AS artwork_title,
       aw.year_created
FROM museum_collection.artists ar
LEFT JOIN museum_collection.artworks aw 
       ON ar.artist_id = aw.artist_id
WHERE ar.death_year IS NULL
ORDER BY ar.artist_name;
```

---

### 5. Count of artworks per artist

```sql
SELECT ar.artist_name,
       COUNT(aw.artwork_id) AS total_artworks
FROM museum_collection.artists ar
LEFT JOIN museum_collection.artworks aw 
       ON ar.artist_id = aw.artist_id
GROUP BY ar.artist_name
ORDER BY total_artworks DESC;
```

---

### 6. Which exhibitions are happening in 2024?

```sql
SELECT e.exhibition_name,
       m.museum_name,
       e.start_date,
       e.end_date
FROM museum_collection.exhibitions e
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
WHERE EXTRACT(YEAR FROM e.start_date) = 2024;
```

---

Would you like me to also create a **master query** that shows:

* artwork title
* artist name
* exhibition name
* museum name + country

…all in one table (like a full joined view)?

Perfect ✅ — let’s extend your schema with a **junction table** to explicitly record which artworks are shown in which exhibitions. That way, your master query will have a *direct link* between artworks and exhibitions.

---

## 🔹 Schema Update

```sql
-- Junction table: Artwork ↔ Exhibition
CREATE TABLE museum_collection.artwork_exhibitions (
    artwork_exhibition_id SERIAL PRIMARY KEY,
    artwork_id INT NOT NULL,
    exhibition_id INT NOT NULL,
    CONSTRAINT fk_artwork FOREIGN KEY (artwork_id) REFERENCES museum_collection.artworks(artwork_id),
    CONSTRAINT fk_exhibition FOREIGN KEY (exhibition_id) REFERENCES museum_collection.exhibitions(exhibition_id)
);
```

---

## 🔹 Sample Data (linking artworks to exhibitions)

We’ll assign each artwork to at least one exhibition across East Africa:

```sql
INSERT INTO museum_collection.artwork_exhibitions (artwork_id, exhibition_id) VALUES
(1, 1),  -- The African Dancers → East African Contemporary Art
(2, 2),  -- Cattle Herds in Kampala → Ugandan Heritage Showcase
(3, 3),  -- Colorful Savannah → Tinga Tinga Retrospective
(4, 4),  -- Night Journey → Ancient Ethiopia
(5, 2),  -- The Hornbill → Ugandan Heritage Showcase
(6, 1),  -- Lake Victoria Sunrise → East African Contemporary Art
(7, 7),  -- Village Life in Tanzania → Wildlife & Heritage of Arusha
(8, 8),  -- The World is 9 → Modern Ethiopian Photography
(9, 9),  -- Market Day in Kampala → Western Uganda Traditions
(10, 6); -- Political Circus → Kenya Contemporary Painters
```

---

## 🔹 Master Query (Now With Direct Links 🎉)

This query ties together **artwork → artist → exhibition → museum** properly:

```sql
SELECT 
    aw.title AS artwork_title,
    ar.artist_name,
    ar.nationality,
    e.exhibition_name,
    e.start_date,
    e.end_date,
    m.museum_name,
    m.city,
    m.country
FROM museum_collection.artworks aw
JOIN museum_collection.artists ar 
     ON aw.artist_id = ar.artist_id
JOIN museum_collection.artwork_exhibitions ae 
     ON aw.artwork_id = ae.artwork_id
JOIN museum_collection.exhibitions e 
     ON ae.exhibition_id = e.exhibition_id
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
ORDER BY e.start_date, ar.artist_name;
```

---

✅ This way you’ll get results like:

| artwork\_title       | artist\_name   | nationality | exhibition\_name              | museum\_name                | country  |
| -------------------- | -------------- | ----------- | ----------------------------- | --------------------------- | -------- |
| The African Dancers  | Elimo Njau     | Kenyan      | East African Contemporary Art | Nairobi National Museum     | Kenya    |
| Cattle Herds Kampala | Jak Katarikawe | Ugandan     | Ugandan Heritage Showcase     | Uganda Museum               | Uganda   |
| Colorful Savannah    | Tinga Tinga    | Tanzanian   | Tinga Tinga Retrospective     | National Museum of Tanzania | Tanzania |

---

👉 Do you want me to also add **sample queries** to check:

* which artworks each exhibition is displaying,
* and which exhibitions each artist has been part of?

Got it 👍 — if you want **DISTINCT** statements, they’ll help remove duplicates (especially when an artist has multiple artworks in the same exhibition, or exhibitions share the same museum).

Here are **DISTINCT-based queries** for your use cases:

---

## 🔹 1. Distinct list of exhibitions and their artworks

```sql
SELECT DISTINCT
    e.exhibition_name,
    m.museum_name,
    aw.title AS artwork_title,
    ar.artist_name
FROM museum_collection.exhibitions e
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
JOIN museum_collection.artwork_exhibitions ae 
     ON e.exhibition_id = ae.exhibition_id
JOIN museum_collection.artworks aw 
     ON ae.artwork_id = aw.artwork_id
JOIN museum_collection.artists ar 
     ON aw.artist_id = ar.artist_id
ORDER BY e.exhibition_name, ar.artist_name;
```

---

## 🔹 2. Distinct exhibitions an artist has participated in

```sql
SELECT DISTINCT
    ar.artist_name,
    e.exhibition_name,
    m.museum_name,
    m.city,
    m.country
FROM museum_collection.artists ar
JOIN museum_collection.artworks aw 
     ON ar.artist_id = aw.artist_id
JOIN museum_collection.artwork_exhibitions ae 
     ON aw.artwork_id = ae.artwork_id
JOIN museum_collection.exhibitions e 
     ON ae.exhibition_id = e.exhibition_id
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
ORDER BY ar.artist_name, e.exhibition_name;
```

---

## 🔹 3. Distinct exhibitions by country

```sql
SELECT DISTINCT
    m.country,
    e.exhibition_name,
    m.museum_name
FROM museum_collection.exhibitions e
JOIN museum_collection.museums m 
     ON e.museum_id = m.museum_id
ORDER BY m.country, e.exhibition_name;
```

---

## 🔹 4. Distinct list of artists shown in each exhibition

```sql
SELECT DISTINCT
    e.exhibition_name,
    ar.artist_name,
    ar.nationality
FROM museum_collection.exhibitions e
JOIN museum_collection.artwork_exhibitions ae 
     ON e.exhibition_id = ae.exhibition_id
JOIN museum_collection.artworks aw 
     ON ae.artwork_id = aw.artwork_id
JOIN museum_collection.artists ar 
     ON aw.artist_id = ar.artist_id
ORDER BY e.exhibition_name, ar.artist_name;
```

---

👉 Do you want me to **bundle these DISTINCT queries into a database view** (like `vw_exhibition_overview`) so you don’t have to keep writing long joins every time?

