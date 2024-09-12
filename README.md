# Doraemon Gadgets Database - Quickstart Guide



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

4. Create the table:
   ```sql
   CREATE TABLE doraemon_gadgets (
       gadget_id SERIAL PRIMARY KEY,
       gadget_name VARCHAR(100),
       description TEXT,
       introduced_year INTEGER,
       times_used INTEGER,
       power_level INTEGER,
       is_favorite BOOLEAN
   );
   ```

5. Insert sample data:
   ```sql
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

## Questions and answers
Certainly! I'll create a Doraemon-themed table and provide some SQL questions at basic and medium levels for a PostgreSQL event, along with sample data and answers. Let's start with the table structure and sample data.



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

Now, let's proceed with the SQL questions at basic and medium levels, along with their answers.

Basic Level Questions:

1. List all gadgets that Doraemon has used more than 100 times.

```sql
SELECT gadget_name, times_used
FROM doraemon_gadgets
WHERE times_used > 100
ORDER BY times_used DESC;
```

2. Find the gadget with the highest power level.

```sql
SELECT gadget_name, power_level
FROM doraemon_gadgets
ORDER BY power_level DESC
LIMIT 1;
```

3. How many gadgets were introduced in the 1970s?

```sql
SELECT COUNT(*) AS gadgets_from_70s
FROM doraemon_gadgets
WHERE introduced_year BETWEEN 1970 AND 1979;
```

Medium Level Questions:

4. Calculate the average power level of Doraemon's favorite gadgets compared to non-favorite gadgets.

```sql
SELECT 
    is_favorite,
    AVG(power_level) AS avg_power_level
FROM doraemon_gadgets
GROUP BY is_favorite;
```

5. List the top 3 most used gadgets along with their usage percentage compared to the total usage of all gadgets.

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

6. Find pairs of gadgets introduced in the same year.

```sql
SELECT 
    a.gadget_name AS gadget1,
    b.gadget_name AS gadget2,
    a.introduced_year
FROM doraemon_gadgets a
JOIN doraemon_gadgets b ON a.introduced_year = b.introduced_year AND a.gadget_id < b.gadget_id
ORDER BY a.introduced_year;
```

These questions cover a range of SQL concepts including basic SELECT statements, aggregations, common table expressions (CTEs), and self-joins. Would you like me to explain any of these queries or provide more questions?
