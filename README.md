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
