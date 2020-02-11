# Overview of SQL

```mysql
-- http://webdev.slides.com/coltsteele/mysql-99-101#/134
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
SELECT title,author_fname,author_lname FROM `books` ORDER BY author_fname,author_lname DESC;
SELECT title,author_fname,author_lname FROM `books` ORDER BY 2;
SELECT title,author_fname,author_lname FROM `books` ORDER BY 2 DESC;
SELECT client_id,SUM(invoice_total) total FROM invoices GROUP BY client_id WITH ROLLUP; 



-- CASTING
SELECT Cast(countycode AS CHAR) 
FROM   pop 
ORDER  BY Cast(countycode AS CHAR) DESC; 

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

SELECT id, 
       t2.id AS s 
FROM   t1 
       INNER JOIN t2 using (id) 

SELECT * 
FROM   t1, 
       t2 
WHERE  t1.id = t2.id; 


SELECT * FROM orders o NATURAL JOIN customers c;

-- composite primary key

SELECT * FROM order_items oi
 JOIN order_item_notes oin
 ON oi.order_id = oin.order_id
 AND oi.product_id = oin.product_id;

-- INTERSECT
SELECT DISTINCT 
   id 
FROM t1
   INNER JOIN t2 USING(id);
   
-- UNION & UNION ALL
SELECT id 
FROM   t1 
UNION 
SELECT id 
FROM   t2; 

SELECT id 
FROM   t1 
UNION ALL 
SELECT id 
FROM   t2; 

-- if you use the UNION ALL explicitly, the duplicate rows, if available, remain in the result. Because UNION ALL does not need to handle duplicates, it performs faster than UNION DISTINCT .

-- MINUS
SELECT 
    id
FROM
    t1
LEFT JOIN
    t2 USING (id)
WHERE
    t2.id IS NULL;

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

# Indexes

```mysql
-- 02 Creating an index

USE scratch;
DROP TABLE IF EXISTS test;
CREATE TABLE test (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    string1 VARCHAR(128),
    string2 VARCHAR(128)
);
SHOW INDEX FROM test;

CREATE INDEX i_str2 ON test(string2);
SHOW INDEX FROM test;

DROP TABLE IF EXISTS test;

-- 03 Showing indexes

USE scratch;
SHOW INDEX FROM customer;

SELECT DISTINCT table_name, index_name 
    FROM information_schema.statistics 
    WHERE table_schema = 'scratch';

-- 04 Dropping indexes

USE scratch;
DESCRIBE customer;
SHOW INDEX FROM customer;
CREATE INDEX custzip ON customer(zip);
SHOW INDEX FROM customer;
DROP INDEX custzip ON customer;
SHOW INDEX FROM customer;

-- 05 multi-column indexes

USE scratch;
DROP TABLE IF EXISTS test;
CREATE TABLE test (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    string1 VARCHAR(128),
    string2 VARCHAR(128),
    INDEX twostrs (string1,string2)
);
INSERT INTO test (string1, string2) VALUES ('foo', 'bar'), ('this', 'that'), ('another', 'row'), ('foo', 'alpha');
SELECT string1, string2 FROM test ORDER BY string1, string2;

SHOW INDEX FROM test;

SELECT string1, string2 FROM test ORDER BY string1, string2;
EXPLAIN SELECT string1, string2 FROM test ORDER BY string1, string2;
DROP TABLE test;
```

# Views and Subselects
```mysql
-- 01 Creating a subselect

USE world;
DROP TABLE IF EXISTS t;
CREATE TABLE t ( a TEXT, b TEXT );
INSERT INTO t VALUES ( 'NY0123', 'US4567' );
INSERT INTO t VALUES ( 'AZ9437', 'GB1234' );
INSERT INTO t VALUES ( 'CA1279', 'FR5678' );
SELECT * FROM t;

SELECT SUBSTR(a, 1, 2) AS State, SUBSTR(a, 3) AS SCode, 
  SUBSTR(b, 1, 2) AS Country, SUBSTR(b, 3) AS CCode FROM t;

SELECT ss.CCode FROM (
  SELECT SUBSTR(a, 1, 2) AS State, SUBSTR(a, 3) AS SCode, 
    SUBSTR(b, 1, 2) AS Country, SUBSTR(b, 3) AS CCode FROM t
) AS ss;

SELECT co.Name, ss.CCode FROM (
    SELECT SUBSTR(a, 1, 2) AS State, SUBSTR(a, 3) AS SCode,
      SUBSTR(b, 1, 2) AS Country, SUBSTR(b, 3) AS CCode FROM t
  ) AS ss
  JOIN Country AS co
    ON co.Code2 = ss.Country
;

DROP TABLE t;

-- 02 searching within a result set

USE album;
SELECT DISTINCT album_id FROM track WHERE duration <= 90;

SELECT * FROM album
  WHERE id IN (SELECT DISTINCT album_id FROM track WHERE duration <= 90)
;

SELECT a.title AS album, a.artist, t.track_number AS seq, t.title, t.duration AS secs
  FROM album AS a
  JOIN track AS t
    ON t.album_id = a.id
  WHERE a.id IN (SELECT DISTINCT album_id FROM track WHERE duration <= 90)
  ORDER BY a.title, t.track_number
;

SELECT a.title AS album, a.artist, t.track_number AS seq, t.title, t.duration AS secs
  FROM album AS a
  JOIN (
    SELECT DISTINCT album_id, track_number, duration, title
    FROM track
    WHERE duration <= 90
  ) AS t
    on t.album_id = a.id
  ORDER BY a.title, t.track_number
;

-- 03 Creating a view

USE album;
SELECT id, album_id, title, track_number, duration DIV 60 AS m, duration MOD 60 AS s FROM track;

CREATE VIEW trackView AS
  SELECT id, album_id, title, track_number, duration DIV 60 AS m, duration MOD 60 AS s FROM track;
SELECT * FROM trackView;

SELECT a.title AS album, a.artist, t.track_number AS seq, t.title, t.m, t.s
  FROM album AS a
  JOIN trackView AS t
    ON t.album_id = a.id
  ORDER BY a.title, t.track_number
;

SELECT a.title AS album, a.artist, t.track_number AS seq, t.title, 
    CONCAT(t.m, ':', SUBSTR(CONCAT('00', t.s), -2, 2)) AS duration
  FROM album AS a
  JOIN trackView AS t
    ON t.album_id = a.id
  ORDER BY a.title, t.track_number
;

DROP VIEW IF EXISTS trackView;

-- 04 Joined view

USE album;
SELECT a.artist AS artist,
    a.title AS album,
    t.title AS track,
    t.track_number AS trackno,
    t.duration DIV 60 AS m,
    t.duration MOD 60 AS s
  FROM track AS t
    JOIN album AS a
      ON a.id = t.album_id
  ORDER BY a.artist, t.track_number
;

CREATE VIEW joinedAlbum AS
    SELECT a.artist AS artist,
        a.title AS album,
        t.title AS track,
        t.track_number AS trackno,
        t.duration DIV 60 AS m,
        t.duration MOD 60 AS s
      FROM track AS t
          JOIN album AS a
            ON a.id = t.album_id
    ORDER BY a.artist, t.track_number
    ;

SELECT * FROM joinedAlbum;
SELECT * FROM joinedAlbum WHERE artist = 'Jimi Hendrix';

SELECT artist, album, track, trackno, 
    CONCAT(m, ':', SUBSTR(CONCAT('00', s), -2, 2)) AS duration
    FROM joinedAlbum;

DROP VIEW IF EXISTS joinedAlbum;

```
# Stored Routines
```mysql
-- 02 Creating a stored function

USE album;
DROP FUNCTION IF EXISTS track_len;

DELIMITER //
CREATE FUNCTION track_len(seconds INT)
RETURNS VARCHAR(16)
DETERMINISTIC
BEGIN
    RETURN CONCAT_WS(':', seconds DIV 60, LPAD(seconds MOD 60, 2, '0' ));
END //
DELIMITER ;

SELECT title, duration AS secs, track_len(duration) AS len
  FROM track ORDER BY duration DESC;

SELECT a.artist AS artist,
    a.title AS album,
    t.title AS track,
    t.track_number AS trackno,
    track_len(t.duration) AS length
  FROM track AS t
  JOIN album AS a
    ON a.id = t.album_id
  ORDER BY artist, album, trackno
;

SELECT a.artist AS artist,
    a.title AS album,
    track_len(SUM(duration)) AS length
  FROM track AS t
  JOIN album AS a
    ON a.id = t.album_id
  GROUP BY a.id
  ORDER BY artist, album
;

SHOW FUNCTION STATUS WHERE DEFINER LIKE 'admin%';

-- to drop function: DROP FUNCTION IF EXISTS track_len;

-- 03 Creating a stored procedure

USE album;

-- simple procedure
DROP PROCEDURE IF EXISTS list_albums;
DELIMITER //
CREATE PROCEDURE list_albums ()
BEGIN
    SELECT * FROM album;
    SELECT * FROM track;
END //
DELIMITER ;

CALL list_albums();


DROP PROCEDURE IF EXISTS list_albums;
DELIMITER //
CREATE PROCEDURE list_albums (param VARCHAR(255))
  BEGIN
    SELECT a.artist AS artist,
        a.title AS album,
        t.title AS track,
        t.track_number AS trackno,
        track_len(t.duration) AS length
      FROM track AS t
      JOIN album AS a
        ON a.id = t.album_id
      WHERE a.artist LIKE param
      ORDER BY artist, album, trackno
    ;
  END //
DELIMITER ;

CALL list_albums('%hendrix%');

-- with output parameter
DROP PROCEDURE IF EXISTS total_duration;

DELIMITER //
CREATE PROCEDURE total_duration (param VARCHAR(255), OUT outp VARCHAR(255))
  BEGIN
    SELECT track_len(SUM(duration)) INTO outp
      FROM track
      WHERE album_id IN (SELECT id FROM album WHERE artist LIKE param)
    ;
  END //
DELIMITER ;

CALL total_duration('%hendrix%', @dur);
SELECT @dur;

SHOW FUNCTION STATUS WHERE DEFINER LIKE 'admin%';
SHOW PROCEDURE STATUS WHERE DEFINER LIKE 'admin%';

DROP FUNCTION IF EXISTS track_len;
DROP PROCEDURE IF EXISTS total_duration;

-- 04 Loops in stored procedures

USE scratch;
DROP PROCEDURE IF EXISTS str_count;

-- STR_COUNT()
-- count 1 to 5
-- concatenate in string

DELIMITER //
CREATE PROCEDURE str_count()
BEGIN
  DECLARE max_value INT UNSIGNED DEFAULT 5;
  DECLARE int_value INT UNSIGNED DEFAULT 0;
  DECLARE str_value VARCHAR(255) DEFAULT '';
  
  WHILE int_value < max_value DO
    SET int_value = int_value + 1;
    SET str_value = CONCAT(str_value, int_value, ' ');
  END WHILE;
  SELECT str_value;
END //
DELIMITER ;

CALL str_count();

DROP PROCEDURE IF EXISTS str_count;
```

# Transactions
```mysql
-- 02 Data integrity

USE scratch;
DROP TABLE IF EXISTS widgetInventory;
DROP TABLE IF EXISTS widgetSales;

CREATE TABLE widgetInventory (
  id INTEGER AUTO_INCREMENT PRIMARY KEY,
  description TEXT,
  onhand INTEGER NOT NULL
);

CREATE TABLE widgetSales (
  id INTEGER AUTO_INCREMENT PRIMARY KEY,
  inv_id INTEGER,
  quan INTEGER,
  price INTEGER
);

INSERT INTO widgetInventory ( description, onhand ) VALUES ( 'rock', 25 );
INSERT INTO widgetInventory ( description, onhand ) VALUES ( 'paper', 25 );
INSERT INTO widgetInventory ( description, onhand ) VALUES ( 'scissors', 25 );

SELECT * FROM widgetInventory;


START TRANSACTION;
INSERT INTO widgetSales ( inv_id, quan, price ) VALUES ( 1, 5, 500 );
UPDATE widgetInventory SET onhand = ( onhand - 5 ) WHERE id = 1;
COMMIT;

SELECT * FROM widgetInventory;
SELECT * FROM widgetSales;


START TRANSACTION;
INSERT INTO widgetInventory ( description, onhand ) VALUES ( 'toy', 25 );
ROLLBACK;
SELECT * FROM widgetInventory;

-- restore database
DROP TABLE widgetInventory;
DROP TABLE widgetSales;

-- 03 Performance 

USE scratch;
DROP TABLE IF EXISTS test;
DROP PROCEDURE IF EXISTS insert_loop;
CREATE TABLE test ( id INTEGER AUTO_INCREMENT PRIMARY KEY, data TEXT );

DELIMITER //
CREATE PROCEDURE insert_loop( IN count INT UNSIGNED )
BEGIN
    DECLARE accum INT UNSIGNED DEFAULT 0;
    DECLARE start_time VARCHAR(32);
    DECLARE end_time VARCHAR(32);
    SET start_time = SYSDATE(6);
    WHILE accum < count DO
        SET accum = accum + 1;
        INSERT INTO test ( data ) VALUES ( 'this is a good sized line of text.' );
    END WHILE;
    SET end_time = SYSDATE(6);
    SELECT TIME_FORMAT(start_time, '%T.%f') AS `Start`,
        TIME_FORMAT(end_time, '%T.%f') AS `End`,
        TIME_FORMAT(TIMEDIFF(end_time, start_time), '%s.%f') AS `Elapsed Secs`;
END //
DELIMITER ;

-- START TRANSACTION;
CALL insert_loop(10000);
-- START TRANSACTION;

SELECT * FROM test ORDER BY id DESC LIMIT 10;

DROP TABLE IF EXISTS test;
DROP PROCEDURE IF EXISTS insert_loop;

```

# Triggers
```mysql
-- 01 update triggers

USE scratch;
DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;

CREATE TABLE widgetCustomer ( id INTEGER AUTO_INCREMENT PRIMARY KEY, name VARCHAR(64), last_order_id INT );
CREATE TABLE widgetSale ( id INTEGER AUTO_INCREMENT PRIMARY KEY, item_id INT, customer_id INT, quan INT, price INT );

INSERT INTO widgetCustomer (name) VALUES ('Bob');
INSERT INTO widgetCustomer (name) VALUES ('Sally');
INSERT INTO widgetCustomer (name) VALUES ('Fred');

SELECT * FROM widgetCustomer;

DROP TRIGGER IF EXISTS newWidgetSale;
DELIMITER //
CREATE TRIGGER newWidgetSale AFTER INSERT ON widgetSale 
    FOR EACH ROW
    BEGIN
         UPDATE widgetCustomer SET last_order_id = NEW.id WHERE widgetCustomer.id = NEW.customer_id;
    END //
DELIMITER ;


INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (1, 3, 5, 1995);
INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (2, 2, 3, 1495);
INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (3, 1, 1, 2995);
SELECT * FROM widgetSale;
SELECT * FROM widgetCustomer;

DROP TRIGGER IF EXISTS newWidgetSale;
DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;


-- 02 preventing updates
-- test.db

USE scratch;
DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;

CREATE TABLE widgetCustomer ( id INTEGER AUTO_INCREMENT PRIMARY KEY, name VARCHAR(64), last_order_id INT );
CREATE TABLE widgetSale ( id INTEGER AUTO_INCREMENT PRIMARY KEY, item_id INT, customer_id INTEGER, quan INT, price INT,
    reconciled INT );
INSERT INTO widgetSale (item_id, customer_id, quan, price, reconciled) VALUES (1, 3, 5, 1995, 0);
INSERT INTO widgetSale (item_id, customer_id, quan, price, reconciled) VALUES (2, 2, 3, 1495, 1);
INSERT INTO widgetSale (item_id, customer_id, quan, price, reconciled) VALUES (3, 1, 1, 2995, 0);
SELECT * FROM widgetSale;

DROP TRIGGER IF EXISTS updateWidgetSale;
DELIMITER //
CREATE TRIGGER updateWidgetSale BEFORE UPDATE ON widgetSale
    FOR EACH ROW
    BEGIN
        IF OLD.id = NEW.id AND OLD.reconciled = 1 THEN
            SIGNAL SQLSTATE '45000' set message_text = 'cannot update reconciled row: "widgetSale"';
        END IF;
    END //
DELIMITER ;

UPDATE widgetSale SET quan = 9 WHERE id = 1;
UPDATE widgetSale SET quan = 9 WHERE id = 2;

SELECT * FROM widgetSale;

DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;

-- 03 timestamps
-- test.db

USE scratch;
DROP TABLE IF EXISTS widgetSale;
DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetLog;

CREATE TABLE widgetCustomer ( id INTEGER AUTO_INCREMENT PRIMARY KEY, name VARCHAR(64), last_order_id INT, stamp VARCHAR(24) );
CREATE TABLE widgetSale ( id INTEGER AUTO_INCREMENT PRIMARY KEY, item_id INT, customer_id INTEGER, quan INT, price INT, stamp VARCHAR(24) );
CREATE TABLE widgetLog ( id INTEGER AUTO_INCREMENT PRIMARY KEY, stamp VARCHAR(24), event VARCHAR(64), username VARCHAR(64), tablename VARCHAR(64), table_id INT);

INSERT INTO widgetCustomer (name) VALUES ('Bob');
INSERT INTO widgetCustomer (name) VALUES ('Sally');
INSERT INTO widgetCustomer (name) VALUES ('Fred');
SELECT * FROM widgetCustomer;

DROP TRIGGER IF EXISTS stampSale;
DROP TRIGGER IF EXISTS newWidgetSale;
DELIMITER //
CREATE TRIGGER stampSale BEFORE INSERT ON widgetSale
    FOR EACH ROW
    BEGIN
        DECLARE nowstamp VARCHAR(24) DEFAULT NOW();
        SET NEW.stamp = nowstamp;
    END //

CREATE TRIGGER newWidgetSale AFTER INSERT ON widgetSale 
    FOR EACH ROW
    BEGIN
        DECLARE nowstamp VARCHAR(24) DEFAULT NOW();
        INSERT INTO widgetLog (stamp, event, username, tablename, table_id)
            VALUES (nowstamp, 'INSERT', USER(), 'widgetSale', NEW.id);
        UPDATE widgetCustomer SET last_order_id = NEW.id, stamp = nowstamp
             WHERE widgetCustomer.id = NEW.customer_id;
        
    END //
DELIMITER ;

INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (1, 3, 5, 1995);
INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (2, 2, 3, 1495);
INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (3, 1, 1, 2995);

SELECT * FROM widgetSale;
SELECT * FROM widgetCustomer;
SELECT * FROM widgetLog;

-- restore database
DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;
DROP TABLE IF EXISTS widgetLog;

```

# Foreign Keys
```mysql
-- 01 Foreign keys

USE scratch;
DROP TABLE IF EXISTS widgetSale;
DROP TABLE IF EXISTS widgetCustomer;

CREATE TABLE widgetCustomer ( id INTEGER AUTO_INCREMENT PRIMARY KEY, name VARCHAR(64) );
CREATE TABLE widgetSale ( 
    id INTEGER AUTO_INCREMENT PRIMARY KEY, 
    item_id INT, 
    customer_id INT,
    quan INT,
    price INT,
    INDEX custid (customer_id),
    CONSTRAINT custid FOREIGN KEY custid(customer_id) REFERENCES widgetCustomer(id)
        ON UPDATE RESTRICT
        ON DELETE SET NULL
);

INSERT INTO widgetCustomer (name) VALUES ('Bob'), ('Sally'), ('Fred');
INSERT INTO widgetSale (item_id, customer_id, quan, price) VALUES (1, 3, 5, 1995), (2, 2, 3, 1495), (3, 1, 1, 2995);
SELECT * FROM widgetSale;
SELECT * FROM widgetCustomer;

UPDATE widgetCustomer SET id = 9 WHERE id = 2;



-- 02 Dropping and changing foreign keys

USE scratch;
SELECT * FROM widgetSale;
SELECT * FROM widgetCustomer;

ALTER TABLE widgetSale DROP FOREIGN KEY custid;
ALTER TABLE widgetSale ADD CONSTRAINT custid
  FOREIGN KEY (customer_id) REFERENCES widgetCustomer(id)
  ON UPDATE RESTRICT 
  ON DELETE SET NULL;

UPDATE widgetCustomer SET id = 2 WHERE id = 9;
UPDATE widgetCustomer SET id = 9 WHERE id = 2;

DROP TABLE IF EXISTS widgetCustomer;
DROP TABLE IF EXISTS widgetSale;

DROP TABLE IF EXISTS widgetSale;
DROP TABLE IF EXISTS widgetCustomer;


```

# Inserting & Updating Data Using Queries
```mysql
CREATE TABLE tempcustomer AS 
  SELECT first_name, 
         last_name 
  FROM   externalcustomer AS c 
  WHERE  c.create_date >= ‘8 / 1 / 2015
  

CREATE TABLE tempcustomer AS 
  SELECT c.first_name, 
         c.last_name, 
         a.address 
  FROM   externalcustomer AS c 
         inner join externaladdress AS a 
                 ON c.customer_id = a.customer_id 
  WHERE  c.create_date >= ‘8 / 1 / 2015
  
  
UPDATE customer AS c 
       LEFT JOIN tempcustomer AS tc 
              ON c.customer_id = tc.customer_id 
SET    c.first_name = tc.first_name, 
       c.last_name = tc.last_name, 
       c.address = tc.address 
WHERE  c.customer_id > 5; 


UPDATE customer 
SET    first_name = (SELECT first_name 
                     FROM   tempcustomer 
                     WHERE  tempcustomer.customer_id = customer.customer_id); 

```  

# Importing/Exporting Data from a CSV File

| customer_id | first_name | last_name | address           |
|-------------|------------|-----------|-------------------|
| 5           | Jennifer   | Smith     | 111 Comman        |
| 6           | Paul       | Smith     | Note Street       |
| 7           | Andrew     | Johnson   | Uncommon Location |


```mysql
LOAD data INFILE 'c:/customer/customer.csv' 
INTO TABLE customer 
fields TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
IGNORE 1 rows;

SELECT customer_id, 
       first_name, 
       last_name 
FROM   customer 
WHERE  create_date >= ‘8/1/2015' INTO OUTFILE 'C:/customers/customers.csv' FIELDS ENCLOSED BY '"' ESCAPED BY '"' LINES TERMINATED BY ';

```

# Pivot Table Query

```mysql
USE balances;
SET SESSION group_concat_max_len = 1000000;
SET @sql = null;
select GROUP_CONCAT(
        DISTINCT CONCAT('SUM(if(currency_code=','''',currency_code,''',available,',0,')) BOKA_', currency_code)
    )  
  INTO @sql from balances;


SET @sql =  CONCAT('SELECT account_id, ',@sql, ' from balances group by account_id limit 100');

PREPARE stmt FROM @sql;
EXECUTE stmt;
DEALLOCATE PREPARE stmt;
```


# Subqueries
```mysql
-- USING ALL KEYWORD
SELECT * FROM invoices
WHERE invoice_total > ALL (
	SELECT invoice_total 
	FROM invoices
	WHERE client_id = 3
)


SELECT * FROM invoices
WHERE invoice_total > (
	SELECT MAX(invoice_total) 
	FROM invoices
	WHERE client_id = 3
)


-- select clients with	at least two invoices
-- USING ANY KEYWORD
SELECT * FROM clients
WHERE client_id = ANY (
	SELECT client_id FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
)


SELECT * FROM clients
WHERE client_id IN (
	SELECT client_id FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
)
```

# Correlated Subqueries
```mysql
-- select employees whose salary is above the average in their office


-- for each employee
--		calculate the avg salary for employee.office
--      return the employee if salary > avg
SELECT * FROM employees e
WHERE salary > (
	SELECT AVG(salary)
	FROM employees
	WHERE office_id = e.office_id
)

-- select clients that have an invoice
SELECT * FROM clients
WHERE client_id IN (
	SELECT DISTINCT client_id
	FROM invoices
)

-- using EXISTS which is much fater than the IN clause
SELECT * FROM clients c
WHERE client_id EXISTS (
	SELECT client_id
	FROM invoices
	WHERE client_id = c.client_id
)

-- Find the products that have never been ordered (Optimized query than NOT IN)
SELECT * 
FROM products p
WHERE NOT EXISTS (
	SELECT product_id
	FROM order_items
	WHERE productd_id = p.product_id
)

```
