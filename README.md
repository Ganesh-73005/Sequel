# Doraemon Gadgets Database 



## Setup

1. Connect to your PostgreSQL server.

2. Create the database (optional):
   ```sql
   CREATE DATABASE doraemon_db;
   ```

3. Connect to the database:
   ```sql
   \c doraemon_db
   ```


```sql
-- Create the doraemon_gadgets table
CREATE TABLE doraemon_gadgets (
    gadget_id SERIAL PRIMARY KEY,
    gadget_name VARCHAR(100),
    description TEXT,
    introduced_year INTEGER,
    times_used INTEGER,
    power_level INTEGER,
    is_favorite BOOLEAN
);

-- Insert sample data
INSERT INTO doraemon_gadgets (gadget_name, description, introduced_year, times_used, power_level, is_favorite)
VALUES
    ('Anywhere Door', 'A door that allows teleportation to any place', 1970, 156, 9, true),
    ('Time Machine', 'A device that enables time travel', 1969, 42, 10, true),
    ('Translation Konjac', 'A konnyaku that can translate languages', 1974, 89, 7, false),
    ('Take-copter', 'A small propeller for flying', 1971, 203, 8, true),
    ('Invisible Cape', 'A cape that makes the wearer invisible', 1973, 67, 8, false),
    ('Memory Bread', 'Bread that helps memorize information', 1975, 31, 6, false),
    ('Big Light', 'A flashlight that enlarges objects', 1972, 54, 7, true),
    ('Small Light', 'A flashlight that shrinks objects', 1972, 48, 7, true),
    ('Time Furoshiki', 'A cloth that can slow down time', 1976, 22, 9, false),
    ('Air Cannon', 'A cannon that shoots air bullets', 1978, 36, 5, false);

```


## Basic Level Questions:

### 1. List all gadgets that Doraemon has used more than 100 times.

```sql
SELECT gadget_name, times_used
FROM doraemon_gadgets
WHERE times_used > 100
ORDER BY times_used DESC;
```

### 2. Find the gadget with the highest power level.

```sql
SELECT gadget_name, power_level
FROM doraemon_gadgets
ORDER BY power_level DESC
LIMIT 1;
```

### 3. How many gadgets were introduced in the 1970s?

```sql
SELECT COUNT(*) AS gadgets_from_70s
FROM doraemon_gadgets
WHERE introduced_year BETWEEN 1970 AND 1979;
```


### 4. Calculate the average power level of Doraemon's favorite gadgets compared to non-favorite gadgets.

```sql
SELECT 
    is_favorite,
    AVG(power_level) AS avg_power_level
FROM doraemon_gadgets
GROUP BY is_favorite;
```

### 5. List the top 3 most used gadgets along with their usage percentage compared to the total usage of all gadgets.

```sql
WITH total_usage AS (
    SELECT SUM(times_used) AS total
    FROM doraemon_gadgets
)
SELECT 
    gadget_name,
    times_used,
    ROUND(times_used * 100.0 / total, 2) AS usage_percentage
FROM doraemon_gadgets, total_usage
ORDER BY times_used DESC
LIMIT 3;
```

### 6. Find pairs of gadgets introduced in the same year.

```sql
SELECT 
    a.gadget_name AS gadget1,
    b.gadget_name AS gadget2,
    a.introduced_year
FROM doraemon_gadgets a
JOIN doraemon_gadgets b ON a.introduced_year = b.introduced_year AND a.gadget_id < b.gadget_id
ORDER BY a.introduced_year;
```


## Medium Level Queries

### 1. Which gadgets are used more often than average?

**Question:** Which gadgets have been used more times than the average usage across all gadgets?

```sql
SELECT gadget_name, times_used
FROM doraemon_gadgets
WHERE times_used > (SELECT AVG(times_used) FROM doraemon_gadgets)
ORDER BY times_used DESC;
```

This query finds gadgets that have been used more times than the average usage across all gadgets. It uses a subquery to calculate the average `times_used` and compares each gadget's usage against this average.

### 2. How has gadget usage accumulated over time?

**Question:** What is the cumulative sum of gadget usage as they were introduced over the years?

```sql
SELECT 
    gadget_name,
    introduced_year,
    times_used,
    SUM(times_used) OVER (ORDER BY introduced_year) AS cumulative_usage
FROM doraemon_gadgets
ORDER BY introduced_year;
```

This query calculates the cumulative sum of `times_used` for gadgets, ordered by `introduced_year`. It uses a window function (`SUM OVER`) to compute the running total of usage as gadgets are introduced over time.

### 3. How are gadgets distributed across power levels?

**Question:** How many gadgets fall into 'Low', 'Medium', and 'High' power level categories?

```sql
SELECT 
    CASE 
        WHEN power_level <= 3 THEN 'Low'
        WHEN power_level <= 7 THEN 'Medium'
        ELSE 'High'
    END AS power_category,
    COUNT(*) AS gadget_count
FROM doraemon_gadgets
GROUP BY power_category;
```

This query categorizes gadgets into 'Low', 'Medium', and 'High' power levels and counts gadgets in each category. It uses a `CASE` statement to assign categories based on `power_level` and then groups the results.

## Hard Level Queries (using JOINs)

First, we need to create additional tables and insert data:

```sql
CREATE TABLE doraemon_characters (
    character_id SERIAL PRIMARY KEY,
    character_name VARCHAR(50)
);

INSERT INTO doraemon_characters (character_name) VALUES
('Doraemon'), ('Nobita'), ('Shizuka'), ('Gian'), ('Suneo');

CREATE TABLE gadget_usage (
    usage_id SERIAL PRIMARY KEY,
    gadget_id INTEGER REFERENCES doraemon_gadgets(gadget_id),
    character_id INTEGER REFERENCES doraemon_characters(character_id),
    usage_count INTEGER
);

INSERT INTO gadget_usage (gadget_id, character_id, usage_count) VALUES
(1, 1, 50), (1, 2, 80), (1, 3, 20), (1, 4, 5), (1, 5, 1),
(2, 1, 30), (2, 2, 10), (2, 3, 2), (2, 4, 0), (2, 5, 0),
(3, 1, 40), (3, 2, 35), (3, 3, 10), (3, 4, 2), (3, 5, 2),
(4, 1, 100), (4, 2, 80), (4, 3, 15), (4, 4, 5), (4, 5, 3);
```

### 4. Who is the most frequent user of each gadget?

**Question:** For each gadget, which character has used it the most times?

```sql
SELECT 
    dg.gadget_name,
    dc.character_name,
    gu.usage_count
FROM doraemon_gadgets dg
JOIN gadget_usage gu ON dg.gadget_id = gu.gadget_id
JOIN doraemon_characters dc ON gu.character_id = dc.character_id
WHERE (dg.gadget_id, gu.usage_count) IN (
    SELECT gadget_id, MAX(usage_count)
    FROM gadget_usage
    GROUP BY gadget_id
)
ORDER BY gu.usage_count DESC;
```

This query joins three tables to find the character who used each gadget the most. It uses a subquery to find the maximum usage count for each gadget and then joins this result with the character information.

### 5. Which gadgets have never been used together?

**Question:** What pairs of gadgets have never been used together by the same character?

```sql
SELECT 
    dg1.gadget_name AS gadget1,
    dg2.gadget_name AS gadget2
FROM doraemon_gadgets dg1
CROSS JOIN doraemon_gadgets dg2
WHERE dg1.gadget_id < dg2.gadget_id
  AND NOT EXISTS (
    SELECT 1
    FROM gadget_usage gu1
    JOIN gadget_usage gu2 ON gu1.character_id = gu2.character_id
    WHERE gu1.gadget_id = dg1.gadget_id
      AND gu2.gadget_id = dg2.gadget_id
  )
ORDER BY dg1.gadget_name, dg2.gadget_name;
```

This complex query finds pairs of gadgets that have never been used together by the same character. It uses a `CROSS JOIN` to generate all possible gadget pairs and then uses `NOT EXISTS` with a subquery to filter out pairs that have been used together.

### 6. Is there a correlation between a gadget's power level and its usage?

**Question:** What is the correlation coefficient between a gadget's power level and its total usage across all characters?

```sql
WITH gadget_total_usage AS (
    SELECT gadget_id, SUM(usage_count) AS total_usage
    FROM gadget_usage
    GROUP BY gadget_id
)
SELECT 
    (COUNT(*) * SUM(dg.power_level * gtu.total_usage) - SUM(dg.power_level) * SUM(gtu.total_usage)) /
    (SQRT(COUNT(*) * SUM(dg.power_level * dg.power_level) - SUM(dg.power_level) * SUM(dg.power_level)) *
     SQRT(COUNT(*) * SUM(gtu.total_usage * gtu.total_usage) - SUM(gtu.total_usage) * SUM(gtu.total_usage))) AS correlation
FROM doraemon_gadgets dg
JOIN gadget_total_usage gtu ON dg.gadget_id = gtu.gadget_id;
```

This advanced query calculates the correlation coefficient between a gadget's power level and its total usage across all characters. It uses a Common Table Expression (CTE) to calculate total usage and then applies the correlation formula directly in SQL.

These queries demonstrate various advanced SQL techniques including subqueries, window functions, CASE statements, JOINs, EXISTS clauses, and complex mathematical calculations.
