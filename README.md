# Overview of SQL

```mysql
-- 03 Selecting Rows

USE world;
SELECT 'Hello, World';
SELECT 1 + 2;
SELECT * FROM Country;
SELECT * FROM Country ORDER BY Name;
SELECT Name, LifeExpectancy FROM Country ORDER BY Name;
SELECT Name, LifeExpectancy AS "Life Expectancy" FROM Country ORDER BY Name;
SELECT COUNT(*) FROM Country;
SELECT * FROM Country ORDER BY Name LIMIT 5;
SELECT * FROM Country ORDER BY Name LIMIT 5, 5;
SELECT * FROM Country ORDER BY Name LIMIT 10, 5;

-- 04 Selecting Columns

USE world;
SELECT * FROM Country ORDER BY Code;
SELECT Name, Code, Region, Population FROM Country ORDER BY Code;
SELECT Name AS Country, Code AS ISO, Region, Population AS Pop FROM Country ORDER BY Code;

-- 05 Counting Rows

USE world;
SELECT COUNT(*) FROM Country;
SELECT COUNT(*) FROM Country WHERE Population > 1000000;
SELECT COUNT(*) FROM Country WHERE Population > 100000000;
SELECT COUNT(*) FROM Country WHERE Population > 100000000 AND Continent = 'Europe' ;
SELECT COUNT(*) FROM Country;
SELECT COUNT(LifeExpectancy) FROM Country;

-- 06 Inserting Data

USE scratch;
SELECT * FROM customer;
INSERT INTO customer (name, address, city, state, zip) VALUES ('Fred Flintstone', '123 Cobblestone Way', 'Bedrock', 'CA', '91234');
INSERT INTO customer (name, city, state) VALUES ('Jimi Hendrix', 'Renton', 'WA');
SELECT * FROM customer;

-- 07 Updating Data

USE scratch;
SELECT * FROM customer;
SELECT * FROM customer WHERE name LIKE 'Jimi%';
UPDATE customer SET address = '123 Music Avenue', zip = '98056' WHERE name LIKE 'Jimi%';
UPDATE customer SET address = '987 Mockingbird Lane' WHERE name LIKE 'Jimi%';
UPDATE customer SET address = NULL, zip = NULL WHERE name LIKE 'Jimi%';

-- 08 Deleting Data

USE scratch;

CREATE TABLE test ( a INT, b VARCHAR(16), c VARCHAR(16) );
INSERT INTO test VALUES ( 1, 'this', 'right here!' );
INSERT INTO test VALUES ( 2, 'that', 'over there!' );
INSERT INTO test VALUES ( 3, 'another', 'nowhere.' );
INSERT INTO test VALUES ( 4, 'again', 'guess where?' );
INSERT INTO test VALUES ( 1, 'one more', 'everywhere!' );

SELECT * FROM test;
DELETE FROM test WHERE a = 2;
DELETE FROM test WHERE a = 1;
DELETE FROM test;
SELECT * FROM test;

DROP TABLE test;

SELECT * FROM customer;
SELECT * FROM customer WHERE name LIKE 'Jimi%' OR name LIKE 'Fred%';
DELETE FROM customer WHERE name LIKE 'Jimi%' OR name LIKE 'Fred%';
SELECT * FROM customer;

-- 09 Joining queries

USE album;
SELECT * FROM album;
SELECT * FROM track;

SELECT a.artist AS Artist, a.title AS Album, t.track_number AS 'Track Num',
    t.title AS Track, t.duration AS Seconds
  FROM album AS a
  JOIN track AS t ON a.id = t.album_id
  ORDER BY a.artist, a.title, t.track_number;

-- 10 Finding databases, tables, and columns

USE scratch;
SHOW databases;
SHOW tables;
DESCRIBE item;
```
# Data Types 

```mysql
-- 01 create table

USE scratch;

CREATE TABLE test (
    id INT,
    name VARCHAR(255),
    address VARCHAR(255),
    city VARCHAR(255),
    state CHAR(2),
    zip CHAR(10)
);

DESCRIBE test;
SHOW TABLE STATUS;
SHOW CREATE TABLE test;

DROP TABLE IF EXISTS test;

-- 03 numeric types

USE scratch;
DESCRIBE numerics;
SELECT * FROM numerics;
SELECT da + db, fa + fb FROM numerics;
SELECT da + db = 0.3 FROM numerics;
SELECT fa + fb = 0.3 FROM numerics;
SELECT fa + fb FROM numerics;

-- 04 date and time

USE scratch;
SELECT NOW();
SHOW VARIABLES LIKE '%time_zone%';
SET time_zone = '+00:00';
SET time_zone = "SYSTEM";
SELECT UTC_TIMESTAMP();

# obsolete TIMESTAMP
DROP TABLE IF EXISTS temp;
CREATE TABLE temp (
  id INT UNSIGNED UNIQUE AUTO_INCREMENT PRIMARY KEY,
  stamp TIMESTAMP,
  name VARCHAR(64)
);
INSERT INTO temp (name) VALUES ('this');
INSERT INTO temp (name) VALUES ('that');
INSERT INTO temp (name) VALUES ('other');
SELECT * FROM temp;

# with DATETIME
CREATE TABLE temp (
  id INT UNSIGNED UNIQUE AUTO_INCREMENT PRIMARY KEY,
  stamp DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  name VARCHAR(64)
);

UPDATE temp SET name = 'Jackson Pollack' WHERE id = 2;

DROP TABLE IF EXISTS temp;

-- 06 enum

USE scratch;

DROP TABLE IF EXISTS test;
CREATE TABLE test (
  id INT UNSIGNED UNIQUE AUTO_INCREMENT PRIMARY KEY,
  a ENUM( 'Pablo', 'Henri', 'Jackson' )
);
INSERT INTO test ( a ) VALUES ( 'Pablo' );
INSERT INTO test ( a ) VALUES ( 'Henri' );
INSERT INTO test ( a ) VALUES ( 'Jackson' );
INSERT INTO test ( a ) VALUES ( 1 );
INSERT INTO test ( a ) VALUES ( 2 );
INSERT INTO test ( a ) VALUES ( 3 );
SELECT * FROM test;

DROP TABLE IF EXISTS test;
CREATE TABLE test (
  id INT UNSIGNED UNIQUE AUTO_INCREMENT PRIMARY KEY,
  a SET( 'Pablo', 'Henri', 'Jackson' )
);
INSERT INTO test ( a ) VALUES ( 'Pablo' );
INSERT INTO test ( a ) VALUES ( 'Henri' );
INSERT INTO test ( a ) VALUES ( 'Jackson' );
INSERT INTO test ( a ) VALUES ( 'Pablo,Jackson,Henri,Henri,Henri' );
INSERT INTO test ( a ) VALUES ( 1 );
INSERT INTO test ( a ) VALUES ( 2 );
INSERT INTO test ( a ) VALUES ( 3 );
INSERT INTO test ( a ) VALUES ( 4 );
INSERT INTO test ( a ) VALUES ( 5 );
INSERT INTO test ( a ) VALUES ( 6 );
INSERT INTO test ( a ) VALUES ( 7 );
SELECT * FROM test;

DROP TABLE IF EXISTS test;

-- 07 serial

USE scratch;

DROP TABLE IF EXISTS test;
CREATE TABLE test (
  id INT UNSIGNED UNIQUE AUTO_INCREMENT PRIMARY KEY,
  a VARCHAR(32)
);
INSERT INTO test ( a ) VALUES ( 'Pablo' );
INSERT INTO test ( a ) VALUES ( 'Henri' );
INSERT INTO test ( a ) VALUES ( 'Jackson' );
SELECT * FROM test;
DESCRIBE test;
SHOW CREATE TABLE test;
DROP TABLE IF EXISTS test;
```

# Operators and Functions
```mysql
-- 01 comparison operators

SELECT 0 = 0;
SELECT 0 = 1;
SELECT 0 = '0';
SELECT '0.1' > 0;
SELECT '0.1' < 0;
SELECT 0.1 < 0;
SELECT 9 < 7;
SELECT 9 > 7;
SELECT 9 = 7;
SELECT 9 != 7;
SELECT 9 <= 7;
SELECT 9 >= 7;
SELECT (9 > 7) AND (12 < 27);
SELECT (9 > 7) OR (12 < 27);
SELECT (9 > 7) IS TRUE;
SELECT (9 > 7) IS NOT TRUE;

-- 02 logical operators

SELECT 1 AND 1;
SELECT 1 OR 1;
SELECT 1 OR 0;
SELECT 1 XOR 0;
SELECT 1 XOR 1;

SELECT 1 IS TRUE;
SELECT 1 IS NOT TRUE;

SELECT 1 IS NULL;
SELECT 1 IS NOT NULL;
SELECT 0 IS NULL;
SELECT 0 IS NULL;
SELECT NULL IS NULL;
SELECT '' IS NULL;

SELECT 7 IN (1, 5, 9);
SELECT 9 IN (1, 5, 9);

USE world;
SELECT Name AS 'Country', ROUND(Population / 1000000) AS 'PopMM' 
  FROM Country 
  WHERE Population > 50000000 AND Continent IN ('Asia', 'Europe')
  ORDER BY Population DESC;

SELECT t.title AS 'Track', t.track_number AS 'Track No', a.title AS 'Album', 
    a.artist AS 'Artist', t.duration AS 'Seconds'
  FROM Album AS a
  JOIN Track AS t ON t.album_id = a.id
  WHERE t.duration > 120 AND t.track_number > 3
  ORDER BY t.duration DESC;

-- 03 assignment operators

SELECT 5 + 3;
SELECT 5 - 3;
SELECT 5 * 3;
SELECT 5 / 3;
SELECT 5 DIV 3;
SELECT 5 MOD 3;
SELECT 5 / 0;

-- 04 operator precedence

SELECT 5 + 6 * 7 - 8;
SELECT (5 + 6) * (7 - 8);
SELECT 5 + (6 * 7) - 8;

-- 05 The CASE statement

USE scratch;

DROP TABLE IF EXISTS booltest;
CREATE TABLE booltest (a INTEGER, b INTEGER);
INSERT INTO booltest VALUES (1, 0);
SELECT * FROM booltest;

SELECT
  CASE WHEN a < 5 THEN 'true' ELSE 'false' END AS boolA,
  CASE WHEN b > 0 THEN 'true' ELSE 'false' END AS boolB
  FROM booltest
;

SELECT
  CASE a WHEN 1 THEN 'true' ELSE 'false' END AS boolA,
  CASE b WHEN 1 THEN 'true' ELSE 'false' END AS boolB 
  FROM booltest
;

DROP TABLE IF EXISTS booltest;

-- 06 the IF statement

USE scratch;

DROP TABLE IF EXISTS booltest;
CREATE TABLE booltest (a INTEGER, b INTEGER);
INSERT INTO booltest VALUES (1, 0);
SELECT * FROM booltest;

SELECT IF(a < 5, 'TRUE', 'FALSE') FROM booltest;
SELECT IF(b > 0, 'TRUE', 'FALSE') FROM booltest;

DROP TABLE IF EXISTS booltest;
```

# String Functions

```mysql
-- 01 string comparisons

USE world;
SELECT Name FROM country WHERE Name LIKE '_a%' ORDER BY Name;
SELECT Name FROM country WHERE STRCMP(Name, 'France') <= 0 ORDER BY Name;

-- 02 regular expressions

USE world;
SELECT Name FROM country WHERE Name RLIKE 'y$' ORDER BY Name;
SELECT Name FROM country WHERE Name RLIKE '[xz][ai]' ORDER BY Name;

-- 03 string concatenation

SELECT 'This ' || 'and ' || 'that';
SELECT CONCAT('This ', 'and ', 'that');
SELECT CONCAT('Love', ' ', 'is', ' ', 'all', ' ', 'you', ' ', 'need');
SELECT CONCAT('one', 'two');
SELECT CONCAT('string', 42);
SELECT CONCAT(42, 42);

-- 04 numeric conversions

SELECT 32742;
SELECT HEX(32742);
SELECT OCT(32742);
SELECT BIN(32742);

SELECT CONV('32742',10,16);
SELECT CONV('7FE6',16,10);
SELECT CONV('28K6',24,10);

-- 05 trimming and padding

USE scratch;
SELECT * FROM customer WHERE name LIKE '  Bill Smith  ';
SELECT * FROM customer WHERE name LIKE TRIM('  Bill Smith  ');
SELECT CONCAT(':', RTRIM('  Bill Smith  '), ':');
SELECT CONCAT(':', LTRIM('  Bill Smith  '), ':');

SELECT CONCAT(':', TRIM('x' FROM 'xxxBill Smithxxx'), ':');

SELECT LPAD('Price', 20, '.');
SELECT LPAD('Price', 20, '. ');
SELECT RPAD('Price', 20, '. ');

-- 06 case conversion

USE scratch;
SELECT UPPER(name) FROM customer;
SELECT LOWER(name) FROM customer;
SELECT CONCAT(UPPER(SUBSTRING(name, 1, 1)),LOWER(SUBSTRING(name, 2))) FROM customer;

-- 07 substring

SELECT SUBSTRING('this is a string', 6);
SELECT SUBSTR('this is a string', 6);
SELECT SUBSTR('this is a string', 6, 4);
SELECT SUBSTR('this is a string', -6);
SELECT SUBSTR('this is a string', -6, 4);

SELECT SUBSTRING_INDEX('this is a string', ' ', 1);
SELECT SUBSTRING_INDEX('this is a string', ' ', 2);
SELECT SUBSTRING_INDEX('this is a string', ' ', -2);

-- 08 soundex

SELECT SOUNDEX('bill'), SOUNDEX('bell');
SELECT SOUNDEX('acton'), SOUNDEX('action');
SELECT SOUNDEX('acting'), SOUNDEX('action');

SELECT 'bill' SOUNDS LIKE 'boil', 'bill' SOUNDS LIKE 'phil';

```

# Mathematical Functions

```mysql
-- 01 value functions

SELECT ABS(-47);
SELECT ABS(-47.73);

SELECT CEILING(12.2);
SELECT CEIL(12.2);

SELECT ROUND(17.5);
SELECT ROUND(17.4);

SELECT TRUNCATE(42.973, 1);
SELECT TRUNCATE(42.973, 2);
SELECT TRUNCATE(99942.973, -2);

-- 02 math funcitons

SELECT PI();
SELECT PI() + 0.000000000000000;

SELECT POWER(8, 2);
SELECT POW(8, 2);

SELECT SQRT(64);
SELECT POWER(4096, 1/4);

SELECT RAND();
SELECT RAND(42);

-- 03 trigonometry

SELECT SIN(2);
SELECT ASIN(.2);
SELECT COS(PI());
SELECT ACOS(.5);
SELECT TAN(PI());
SELECT ATAN(2);
SELECT COT(12);

-- 04 logarithms

SELECT LN(2);
SELECT LOG(2);
SELECT LOG(10, 100);
SELECT LOG(2, 65535);
SELECT LOG2(65535);
SELECT LOG10(100);
SELECT EXP(1);

-- 05 degrees and radians

SELECT DEGREES(PI());
SELECT RADIANS(180);

```

# Differences from Standard SQL

```mysql
-- 01 string concatenation

SELECT 'string1' || 'string2'; -- standard not mysql
SELECT TRUE || FALSE;          -- non-standard mysql
SELECT TRUE OR FALSE;
SELECT CONCAT('string1', 'string2');

-- 02 quote marks
SELECT 'this is a string';
SELECT "this is a string";

USE scratch;
SELECT "name", "address" FROM customer;
SELECT `name`, `address` FROM customer;

-- 03 the modulo operator

SELECT 47 / 3;
SELECT DIV(47,3);       -- standard not mysql
SELECT MOD(47,3);
SELECT 47 MOD 3;        -- non-standard operator
SELECT 47 % 3;          -- non-standard operator

-- 04 comments

--SQL standard comment
-- SQL standard comment

# non-standard MySQL comment
#non-standard MySQL comment

/*
    SQL standard 
    multi-line comment
*/

/*!
    SELECT 'MySQL executable comment' AS `Say what?!`
*/ ;

SELECT 1 /*! + 2 */ ;

```



