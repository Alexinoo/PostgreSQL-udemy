# PostgreSQL Bootcamp : Go From Beginner To Advanced,60+ hours

## Section 1: Introduction To PostgreSQL

- Installing PostgreSQL on Windows

  - [Download the installer](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)
  - Run the installer and follow the default settings on installation wizard
  - Enter root password for user 'postgres'

- Configure pgAdmin 4 client

  - Enter same password for user 'postgres' entered during installation

- Create a Database User
  - **Login/Group Roles - Create - Login/Group Role**
  - **General - Name**
  - **Definition - Password**
  - **Privileges**
    - Can Login
    - Create Databases

```
CREATE ROLE "Alex" WITH
  LOGIN
  NOSUPERUSER
  INHERIT
  CREATEDB
  NOCREATEROLE
  NOREPLICATION
  ENCRYPTED PASSWORD 'md5d48d58d5f0ccf167313bb402faa0f79a';
```

- Drop a Database
  - [IF EXISTS] _is optional_
  - Only super users & database owner can execute the DROP DATABASE statement
  - Careful whenever you try to use anything starting with 'DROP'

```
DROP DATABASE [IF EXISTS] database_name;
```

- Create a Database

```
CREATE DATABASE database_name;
```

## Section 2: Creating And Modifying Tables

- Create Table (QUERY EDITOR) - _Option 1_

  - function : CREATE TABLE

  - create actors/directors table

  - create movies table with **foreign key**

    - _columnname_ _datatype_ REFERENCES directors (director_id)

  - create movies_revenues

  - create movies_actors junction table
    - contains foreign keys of different table

```
CREATE TABLE actors(
    actor_id SERIAL PRIMARY KEY,
    first_name VARCHAR(150),
    last_name VARCHAR(150) NOT NULL,
    gender CHAR(1),
    date_of_birth DATE,
    add_date DATE,
    update_date DATE
);
```

```
CREATE TABLE directors(
    director_id SERIAL PRIMARY KEY,
    first_name VARCHAR(150),
    last_name VARCHAR(150),
    date_of_birth DATE,
    nationality VARCHAR(20),
    add_date DATE,
    update_date DATE
);
```

```
CREATE TABLE movies(
    movie_id SERIAL PRIMARY KEY,
    movie_name VARCHAR(100) NOT NULL,
    movie_length INT,
    movie_lang VARCHAR(20),
    age_certificate VARCHAR(10),
    release_date DATE,
    director_id INT REFERENCES directors (director_id)
);
```

```
CREATE TABLE movies_revenues(
    revenue_id SERIAL PRIMARY KEY,
    movie_id INT REFERENCES movies (movie_id),
    revenues_domestic NUMERIC (10,2),
    revenues_international NUMERIC (10,2)
);
```

```
CREATE TABLE movies_actors(
    movie_id INT REFERENCES movies (movie_id),
	  actor_id INT REFERENCES actors (actor_id),
	  PRIMARY KEY (movie_id,actor_id)
);
```

- Install Sample Data For _movies_ Database

  - **IMPORTANT** - Follow the sequence of inserting data because of foreign keys constraint

  - Download .sql files scripts

    - directors.sql , actors.sql ,movies.sql ,movies_actors.sql,movies_revenues.sql

  - Import these .sql scripts to insert data into tables from pgAdmin 4 client in the following sequence

    - Insert data into table : 'directors'
    - Insert data into table : 'actors'
    - Insert data into table : 'movies'
    - Insert data into table : 'movies_actors'
    - Insert data into table : 'movies_revenues'

- Create Table (pgAdmin 4 GUI) - _Option 2_

  - database_name - Schemas - Tables - Create - Table

    - **General**
      - Name **_tablename_**
      - Owner **_owner_**
      - Schema **_public_**
      - partition side **_optional_ (advanced concept)**
    - **Columns**

      - Name **_columnname_**
      - Datatype **_type_**
      - Length **_applies to character varying_**
      - Precision **_optional_**
      - Not NULL **_optional_**
      - Primary Key **_Mandatory for first column_**

    - **Constraints**

- pgAdmin 4 GUI - View Table Structure and columns

  - View the structure of an existing table

    - Use select query
      ```
      SELECT * FROM _tablename_
      ```
    - Right Click on Table name - Properties
      - General
      - Columns
        - _columns definitions_
      - Constraints
        - _Primary Key_
        - _Foreign Key_
        - _Check_
        - _Unique_
      - Parameters
      - Security
      - SQL

  - Add a column to an existing table

    - Right Click on Column - Create - Column
    - _column definitions_

  - Rename a column

    - Right click on Column - Properties
    - Edit & save

  - drop/delete a column

    - Right click on Column - Delete/Drop

  - change datatype of a column
    - Right Click on Column - Properties
    - _definitions_

- Delete table from database

  - Create table and then delete

  ```
  CREATE TABLE ROLES(
  	role_id SERIAL PRIMARY KEY ,
  	role_name VARCHAR(50)
  );
  ```

  - pgAdmin (GUI)

    - _Right click_ tablename - Delete/Drop

  - Editor

    ```
    DROP TABLE roles;
    ```

## Section 3: Modifying Data in the Tables

- Insert Data into a table

  - Create 'customers' table in Movies database

        - customer_id,first_name,last_name,email,age

        ```
        CREATE TABLE customer (
          customer_id SERIAL PRIMARY KEY,
          first_name VARCHAR(50),
          last_name VARCHAR(50),
          email VARCHAR(50),
          age INT
        );
        ```

  - View the table data with SELECT

    ```
      SELECT * FROM CUSTOMER;
    ```

  - Insert data in the table

    ```
    INSERT INTO table_name(columnname1,columnname2)
    VALUES('value1','value2');
    ```

    ```
    INSERT INTO customer(first_name,last_name,email,age)
    VALUES('Alex','Mwangi','mwangialex26@gmail.com',32);
    ```

- Insert Multiple Records into a table

  - We separate data with (,) to add multiple records

    ```
    INSERT INTO table_name(columnname1,columnname2)
    VALUES
    ('value1','value2',value3...),
    ('value1','value2',value3...),
    ('value1','value2',value3...),
    ('value1','value2',value3...);
    ```

    ```
    INSERT INTO customer(first_name,last_name,email,age)
    VALUES
    ('Anthony','Karibe','karibetoni@gmail.com',42),
    ('Nancy','Mwangi','unknown',62),
    ('Lonah','Wambui','unknown',80);
    ```

- Insert Data with Quote

  - Add say last_name ' Ng'ang'a '

  - Won't work

  ```
  INSERT INTO customer(first_name,last_name)
  VALUES('Peter','Ng'ang'a');
  ```

  - Solution : Add extra quote as an escape character

  ```
  INSERT INTO customer(first_name,last_name)
  VALUES('Peter','Ng''ang''a');
  ```

- Use RETURNING to get info on added

  - Default behavior when adding a record into a table

    ```
    INSERT INTO customer(first_name,last_name)
    VALUES('Adnan','Waheed');

    Query returned successfully in 100 msec.
    ```

  - After the insert , lets return all inserted/added rows (RETURNING \*)

    - returns a table of rows added

    ```
    INSERT INTO customer(first_name)
    VALUES('Jane')
    RETURNING *;
    ```

    | customer_id | first_name | last_name | email | age  |
    | :---------: | :--------: | :-------: | :---: | :--- |
    |    jane     |    null    |   null    | null  | null |

  - After the insert , lets return a single column value (RETURNING _customerid_)

    - return just a single column customer_id for example

    ```
    INSERT INTO customer(first_name)
    VALUES('James')
    RETURNING customer_id;
    ```

    | customer_id |
    | :---------: |
    |      8      |

- Update data in the table

  - Update single column _WHERE_ Condition

    ```
    UPDATE table_name
    SET column_name = 'new value'
    WHERE column_name = 'value';
    ```

  - Update multiple column _WHERE_ Condition

    ```
    UPDATE table_name
    SET column_name1 = 'new value' , column_name2 = 'new value'
    WHERE column_name = 'value';
    ```

- Updating a row and return the updated

  - Use RETURNING to get updated rows

  ```
   UPDATE customer
   SET last_name = 'Doe' , email = 'janedoe@email.com'
   WHERE customer_id = 7
   RETURNING *;
  ```

  | customer_id | first_name | last_name |       email       | age  |
  | :---------: | :--------: | :-------: | :---------------: | :--- |
  |      7      |    jane    |    doe    | janedoe@email.com | null |

- Updating all the records

  - update with no _WHERE_ _clause_

  ```
   UPDATE customer
   SET email = 'janedoe@email.com'
   RETURNING *;
  ```

- Delete data from a table

  - Delete with condition _WHERE_ _clause_

  ```
   DELETE FROM customer
   WHERE customer_id = 8
   RETURNING *;
  ```

  - Delete with no condition

  ```
   DELETE FROM customer;
  ```

- Using UPSERT

  - General idea

    - is that when you insert a new row into the table, PostgreSQL will update the row if it exists, otherwise , it will insert the new row.

    - That is why we call the action as upsert (update or insert)

    - Similar to INSERT INTO .... _IF NOT EXIST_

  - Syntax

    ```
    INSERT INTO table_name(column_list)
    VALUES(value_list)
    ON CONFLICT target action;
    ```

  - _where_ 'target' is the _column_

  - _where_ 'action' means

    DO NOTHING : _means do nothing if the row already exists_ <br>
    DO UPDATE SET _column1_ = _value1_ _WHERE_ condition : _update some fields_ <br>

  - EXAMPLE

    - Create 't_tags' table for Demo purposes

      ```
      CREATE TABLE t_tags(
        id SERIAL PRIMARY KEY,
        tag text UNIQUE,
        update_date TIMESTAMP DEFAULT NOW()
      );
      ```

    - Insert sample data

      ```
      INSERT INTO t_tags(tag)
      VALUES
      ('Pen'),
      ('Pencil');
      ```

    - Insert a record , ON CONFLICT DO NOTHING

      ```
      INSERT INTO t_tags(tag)
      VALUES('Pen')
      ON CONFLICT (tag)
      DO
      NOTHING;
      ```

    - Insert a record , ON CONFLICT UPDATE( _set new values_ ) (same value tag = tag)

    ```
     INSERT INTO t_tags(tag)
     VALUES('Pen')
     ON CONFLICT (tag)
     DO
     UPDATE SET
      tag = EXCLUDED.tag,
      update_date = NOW();
    ```

    - We can do more interesting things with EXCLUDED.tag like (_updates tag to_ 'Pen1') as following

    ```
     INSERT INTO t_tags(tag)
     VALUES('Pen')
     ON CONFLICT (tag)
     DO
     UPDATE SET
      tag = EXCLUDED.tag || '1',
      update_date = NOW();
    ```

## Section 4: Querying Data

- **Important Points to Note**

  - SQL Keywords are not case sensitive

    - SELECT is equivalent to (Select or select)

  - By convention, we will use all SQL keywords in uppercase to make the queries easier to read

  - SELECT \* FROM _tablename_ (Convention is to use lowercase for _tablename_)

  - PostgreSQL evaluates the FROM clause before the SELECT clause in the SELECT statement

- **Select All Data From A Table**

  - Syntax

    ```
    SELECT * FROM table_name
    ```

  - Get all records from movies table

    ```
    SELECT * FROM movies;
    ```

  - Get all records from actors table

    ```
    SELECT * FROM actors;
    ```

- **Select Specific columns From A Table**

  - Use explicit column names in SELECT statement

    - Syntax

      ```
      SELECT column1,column2 FROM table_name;
      ```

  - Get first_name from actors table

    ```
     SELECT first_name FROM actors;
    ```

  - Get first_name,last_name from actors table

    ```
     SELECT first_name,last_name FROM actors;
    ```

  - Get movie_name,movie_lang from movies table

    ```
     SELECT movie_name,movie_lang FROM movies;
    ```

- **Adding Aliases To Column name From A Table**

  - Rules for Naming aliases

    - All aliases are by default lowercase

    - Use double quotes for camelNotation e.g.

      - SELECT first_name AS "firstName" FROM actors;

    - Use double quotes for aliases that contains spaces e.g.

      - SELECT first_name AS "First Name" FROM actors;

    - AS keyword is optional

      - SELECT first_name "firstName", FROM actors;

    - Cannot use single quote for aliases ..

  - Examples

    - Make an alias for first_name as firstName on actors table

    ```
    SELECT first_name AS firstName FROM actors;
    ```

    - Make an alias for movie_name as "Movie Name" and movie_lang as "Language" on movies table

    ```
    SELECT movie_name AS "Movie Name",movie_lang AS "Language"  FROM movies;
    ```

    - Make alias for first_name AS 'First Name' from actors (use single quotes) - DOES NOT WORK !!

      ```
      SELECT movie_name AS 'Movie Name'  FROM movies;
      ```

- **Using SELECT statement for expressions**

  - Can we combine first_name,last_name together to make 'Full name' ?

    - Yes , Using Expressions

    - || is used as a concatenating operator

    ```
    SELECT first_name || last_name AS "Full Names"  FROM actors;
    ```

    - Add a gap between the first_name and the last_name

    ```
    SELECT first_name || ' ' || last_name AS "Full Names"  FROM actors;
    ```

- Can we use an expression to get output

  - Addition/Multiplication/Divison works - Returns sum/product/quotient
    ```
    SELECT 10 / 0;
    ```
  - Boolean expressions - Returns true/false
    ```
    SELECT 10 < 20;
    ```

- **Using ORDER BY to sort records**

  - SELECT statement returns rows in an unspecified order

  - To sort the rows of the result set , we use ORDER BY clause in the SELECT statement

  - ORDER BY is ASC by default (_ASC is optional_)

  - Syntax
    ```
    SELECT column FROM tablename
    ORDER BY column ASC | DESC;
    ```
  - Sort based on single column in ASC / DESC

    ```
    SELECT * FROM movies ORDER BY release_date ASC;
    ```

    ```
    SELECT * FROM movies ORDER BY release_date DESC;
    ```

  - Sort based on multiple columns

    - ORDER BY multiple columns sorts data based on ASC and DESC direction with the combinations of columns

    - sort all movie records by their release_date in DESC and movie_name in ASC

      ```
      SELECT * FROM movies ORDER BY movie_name ASC, release_date DESC;
      ```

  - **PostgreSQL evaluates the clauses in the SELECT statement in the following order**

    - **FROM , SELECT , ORDER BY**

- **Using ORDER BY with alias column name**

  - Make an alias for last_name as surname and sort rows by last_name from actors table

    ```
    SELECT first_name , last_name AS surname FROM actors ORDER BY last_name;
    ```

  - sort rows by surname instead

    ```
    SELECT first_name , last_name AS surname FROM actors ORDER BY surname;
    ```

  - Both WORKS !!

- **Using ORDER BY to sort rows by expression**

  - Get all records of actors from _actors_ table
    ```
    SELECT * from actors;
    ```
  - Calculate the length of the actor first_name with LENGTH function
    ```
    SELECT first_name,LENGTH(first_name) as len from actors;
    ```
  - sort rows by length in DESC
    ```
    SELECT first_name,LENGTH(first_name) as len from actors ORDER BY len DESC;
    ```

- **Using ORDER BY with column name or column number**

  - Sort all records from actors table by first_name ASC and DOB DESC - use column_names

  ```
   SELECT * from actors ORDER BY first_name ,date_of_birth DESC;
  ```

  - Sort specific columns from actors -ORDER BY - use column_numbers instead

  ```
   SELECT first_name,last_name,date_of_birth from actors ORDER BY 1 ,3 DESC;
  ```

- **Using ORDER BY with with NULL values**

  - NULL is a marker that indicates either missing data / data is unknown

  - When sorting rows that contains NULL values ,we can specify the order of null with other non-null values by using

    - NULLS FIRST or NULLS LAST in the ORDER BY clause

  - Syntax

    ```
    SELECT column_list from tablename
    ORDER BY
    sort_expression [ASC | DESC] [NULLS FIRST | NULLS LAST];
    ```

  - Example

    - Create demo_sorting table with 1 column (num) and insert 1,2,3,null as values

    - Default behavior is that null values comes last/at the end after the non-values

      ```
      SELECT num from demo_sorting ORDER BY num NULLS LAST;
      ```

    - Start with Null Values

      ```
      SELECT num from demo_sorting ORDER BY num NULLS FIRST;
      ```

    - ORDER BY DESC places null values first unless you specify with NULLS LAST

- **Using DISTINCT for selecting distinct values**

  - Syntax

    ```
    SELECT DISTINCT columnname FROM tablename;
    ```

  - EXAMPLES

    - Single column

      ```
      SELECT DISTINCT movie_lang FROM movies
      ORDER BY 1;
      ```

      ```
      SELECT DISTINCT director_id FROM movies
      ORDER BY 1;
      ```

    - Multiple distinct values e.g. get unique movie_lang,director_id

      ```
      SELECT DISTINCT movie_lang,director_id FROM movies
      ORDER BY 1;
      ```

    - Get all unique values

      ```
      SELECT DISTINCT * FROM movies;
      ```

## Section 5: FILTERING Data

- **Comparison, Logical and Arithmetic Operators**

  - Operators - special keywords in SQL used in conjuction with SQL clauses to :

    - Compare the value of the fields
    - Select subset of fields
    - Perform arithmetic operations

  - Types of Operators

    - COMPARISON OPERATORS

      - Equal To _=_
      - Greater Than _>_
      - Less Than _<_
      - Greater Than or Equal To _>=_
      - Less Than or Equal To _<=_
      - Not Equal To _<>_

    - LOGICAL OPERATORS

      - AND
      - OR
      - LIKE
      - IN
      - BETWEEN

    - ARITHMETIC
      - Addition _+_
      - Subtraction _-_
      - Multiplication _\*_
      - Division _/_
      - Modulus _%_

  - Operators can be combined to make more complex queries

- **AND Operator**

  - Using WHERE clause

    - Add specific conditions to our queries
    - Limits the results for only data that satisfies our condition
    - Can be used in conjuction with operators (Comparison,Logical and Arithmetic)

  - Syntax

    ```
    SELECT columnlist FROM tablename
    WHERE
    conditions;
    ```

  - With Operators (AND | OR)

  - Use Single condition

    - Get all English Languages
      ```
      SELECT * FROM movies
      WHERE movie_lang = 'English';
      ```
    - Get all Japanese Languages
      ```
      SELECT * FROM movies
      WHERE movie_lang = 'Japanese';
      ```

  - Use Multiple conditions

    - Use AND and OR OPERATORS with 2 separate fields

    - Get all English Languages and age_certificate to 18

      ```
      SELECT * FROM movies
      WHERE movie_lang = 'English'
      AND age_certification = '18';
      ```

- **OR Operator**

  - Allows you to find the records that match ANY of the criteria you asked for.

    - Get all English language or Chinese movies

      ```
      SELECT * FROM movies
      WHERE movie_lang = 'English'
      OR movie_lang = 'Chinese';
      ```

    - Get all English language and director id is equal to 8

      ```
      SELECT * FROM movies
      WHERE movie_lang = 'English'
      AND director_id = 8;
      ```

- **Combining AND,OR Operators**

  - Get all English OR Chinese movies with age certificate equal to 12

    - Without parentheses (Does not give correct results)

      ```
      SELECT * FROM movies
      WHERE movie_lang = 'English'
      OR movie_lang = 'Chinese'
      AND age_certificate = '12'
      ORDER BY movie_lang;
      ```

    - With parentheses (correct results)

      ```
      SELECT * FROM movies
      WHERE (movie_lang = 'English' OR movie_lang = 'Chinese')
      AND age_certificate = '12'
      ORDER BY movie_lang;
      ```

- **Order of execution with AND,OR Operators**

  - AND operator is processed first and the OR second

  - Uses BODMAS approach

    - For example Multiplication comes before addition

    - Without parentheses 3\*2+1 = 7
    - With parentheses 3\*(2+1) = 9

  - Use paretheses when using multiple operators

- **Column aliases with WHERE clause**

  - Can we use column aliases with WHERE clause ??

    ```
    SELECT first_name,last_name AS surname
    FROM actors
    WHERE surname = 'Allen';
    ```

  - Therefore this throws an error

    - ERROR: column "surname" does not exist

- **Order Of execution of WHERE clause**

  - What is the order of execution of WHERE clause

    - FROM | WHERE | SELECT | ORDER BY

    ```
     SELECT * FROM movies
     WHERE movie_lang = 'English'
     ORDER BY movie_length DESC;
    ```

- **Using COMPARISON Operators**

  - Largely used with numeric data types (_can also be used with other data types as well_)

    - all movies where movie length is greater/less than 100 (_100 is not included_)

      ```
      SELECT * FROM movies WHERE movie_length > 100 ORDER BY movie_length;
      ```

      ```
      SELECT * FROM movies WHERE movie_length < 100 ORDER BY movie_length;
      ```

    - all movies where movie length is greater/less than and equal to 100 (_100 is included_)

      ```
      SELECT * FROM movies WHERE movie_length >= 100 ORDER BY movie_length;
      ```

      ```
      SELECT * FROM movies WHERE movie_length <= 100 ORDER BY movie_length;
      ```

    - all movies which are not in English language (_!=_ is the same as _<>_)

      ```
      SELECT * FROM movies WHERE movie_lang != 'English' ORDER BY movie_lang;
      ```

      ```
      SELECT * FROM movies WHERE movie_lang <> 'English' ORDER BY movie_lang;
      ```

  - Can we work with Dates data types

    - Working with dates requires consideration of FORMAT in which the date is stored

      - i.e. YYYY-MM-DD (default storage format)
        or
        DD-MM-YYYY

    - all movies where release date is greater than 2000 (_YYYY-MM-DD_) format in the db

      ```
      SELECT * FROM movies WHERE release_date > '2000-12-31' ORDER BY release_date;
      ```

- **Using LIMIT and OFFSET**

  - Used to limit output records

    - Syntax

      ```
      SELECT column_list FROM table_name
      ORDER BY column_name
      LIMIT number
      ```

    - Get the top 5 biggest movies by movie length

      ```
      SELECT * FROM movies ORDER BY movie_length DESC LIMIT 5;
      ```

    - Get the top 5 oldest American directors
      ```
      SELECT * FROM directors WHERE nationality = 'American' ORDER BY date_of_birth
      LIMIT 5 ;
      ```
    - Get the top 10 youngest female actors
      ```
      SELECT * FROM actors WHERE gender = 'F' ORDER BY date_of_birth DESC LIMIT 10 ;
      ```
    - Get the top 10 most domestic profitable movies
      ```
      SELECT * FROM movies_revenues ORDER BY revenues_domestic DESC
      NULLS LAST
      LIMIT 10;
      ```
    - Get the top 10 least domestic profitable movies
      ```
      SELECT * FROM movies_revenues ORDER BY revenues_domestic
      LIMIT 10;
      ```

  - Using OFFSET

    - LIMIT number OFFSET from_rownumber (from_rownumber is not included)

      - List 5 films starting from the fourth one ordered by movie_id

        ```
        SELECT * FROM movies
        ORDERED BY movie_id
        LIMIT 5 OFFSET 4;
        ```

      - List all top 5 movies after the top 5 highest domestic profits movies

        ```
        SELECT * FROM movies_revenues
        ORDER BY revenues_domestic DESC
        LIMIT 5 OFFSET 5;
        ```

- **Using FETCH clause**

  - Fetch Clause

    - FETCH clause retrieves a portion of rows returned by a query
    - Introduced in SQL 2008
    - Is functionally equivalent to the LIMIT clause
    - Highly recommended since it follows the standard SQL

  - Syntax

    ```
    OFFSET start { ROW | ROWS }
    FETCH {FIRST | NEXT } [row_ count] {ROW | ROWS} ONLY
    ```

    - Where
      - OFFSET start is an integer that is >=0 (default start is 0)
      - In case start is greater than the number of rows in the result set, no rows are returned
      - row_count (default value is 1)

  - Examples

    - Get first row of movies table

      ```
      SELECT * FROM movies
      FETCH FIRST 1 ROW ONLY;
      ```

      ```
      SELECT * FROM movies
      FETCH FIRST ROW ONLY;
      ```

    - Get top 5 biggest movies by movie length

      ```
      SELECT * FROM movies
      ORDER BY movie_length DESC
      FETCH FIRST 5 ROW ONLY;
      ```

    - Get top 5 oldest american directors

      ```
      SELECT * FROM directors
      ORDER BY date_of_birth
      FETCH FIRST 5 ROW ONLY;
      ```

    - Get top 10 youngest female actors

      ```
      SELECT * FROM actors
      WHERE gender = 'F'
      ORDER BY date_of_birth DESC
      FETCH FIRST 10 ROW ONLY;
      ```

    - Get first 5 movies from the 5th record onwards by long movie length

      ```
      SELECT * FROM movies
      ORDER BY movie_length DESC
      OFFSET 5
      FETCH FIRST 5 ROW ONLY;
      ```

- **Using IN and NOT IN**

  - Checks if a value matches /does not match the ones in a list

    - value IN (value1,value2,value...)
    - value NOT IN (value1,value2,value...)

    - IN operator returns true if value matches any value in the list
    - NOT IN operator returns true if the value does not match any value in the list

  - Examples :-

    - Get all movies in English,Chinese and Japanese languages

      ```
      SELECT * FROM movies
      WHERE movie_lang IN ('English','Chinese','Japanese')
      ORDER BY movie_lang;
      ```

    - Get all movies where age certificate is 12 and PG

      ```
      SELECT * FROM movies
      WHERE age_certificate IN ('12','PG')
      ORDER BY age_certificate;
      ```

    - Get all movies where director id is not 13 or 10

      ```
      SELECT * FROM movies
      WHERE director_id NOT IN (13,10)
      ORDER BY director_id;
      ```

    - Get all actors where actor id is not 1,2,3,4

      ```
      SELECT * FROM actors
      WHERE actor_id NOT IN (1,2,3,4)
      ORDER BY actor_id;
      ```

- **Using BETWEEN and NOT BETWEEN**

  - Mostly used with Dates data types

  - Matches a value against a range of values

    - value BETWEEN low and high

    - If the value is >= low value and <= high value, the expression returns true , otherwise , it returns false

  - Examples

    - Get all actors where birth_date between 1991 and 1995

      ```
      SELECT * FROM actors
      WHERE date_of_birth BETWEEN '1991-01-01' AND '1995-12-31'
      ORDER BY date_of_birth;
      ```

    - Get all movies released between 1998 and 2002

      ```
      SELECT * FROM movies
      WHERE release_date BETWEEN '1998-01-01' AND '2002-12-31'
      ORDER BY release_date;
      ```

    - Get all movies where domestic revenues are between 100 and 300

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic BETWEEN 100 AND 300
      ORDER BY revenues_domestic;
      ```

    - Can also be written as :

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic >= 100 AND revenues_domestic <= 300
      ORDER BY revenues_domestic;
      ```

    - Get all English movies where movie length is not between 100 and 200

      ```
      SELECT * FROM movies
      WHERE movie_length NOT BETWEEN 100 AND 300
      AND movie_lang = 'English'
      ORDER BY movie_length;
      ```

- **Using LIKE and ILIKE**

  - Queries data based on the matching pattern

    - Returns true if the pattern matches , otherwise false

    - Let's you search for patterns in strings by using 2 special characters

      - % Matches any sequence of zero or more characters
      - \_ matches any single character

  - Syntax

    - value LIKE pattern
    - value ILIKE pattern

  - Examples

    - **Full character Search**

      ```
      SELECT 'hello' LIKE 'hello'; --true
      ```

      - returns true

    - **Partial character Search using %**

      ```
      SELECT 'hello' LIKE 'h%'; --true
      ```

      ```
      SELECT 'hello' LIKE '%e%'; --true
      ```

      ```
      SELECT 'hello' LIKE 'hell%'; --true
      ```

      ```
      SELECT 'hello' LIKE '%ll'; --false o is missing at the end
      ```

    - **Single character search using \_**

      ```
      SELECT 'hello' LIKE '_ello'; --true
      ```

    - **Checking occurence of search using \_**

      ```
      SELECT 'hello' LIKE '__ll__'; --false
      ```

    - **Using % and \_ together**

      ```
      SELECT 'hello' LIKE '%ll_'; --true
      ```

    - **Get all actor names where first name is starting with 'A'**

      ```
      SELECT * FROM actors
      WHERE first_name LIKE 'A%'
      ORDER BY first_name;
      ```

    - **Get all actor names where last name ending with 'a'**

      ```
      SELECT * FROM actors
      WHERE last_name LIKE '%a'
      ORDER BY last_name;
      ```

    - **Get all actors names where first name has 5 characters only**

      ```
      SELECT * FROM actors
      WHERE first_name LIKE '_____'
      ORDER BY first_name;
      ```

    - **Get all actors names where first name contains 'l' on the second place**

      ```
      SELECT * FROM actors
      WHERE first_name LIKE '_l%'
      ORDER BY first_name;
      ```

    - **Is LIKE case-sensitive..? - OOh Yes**

      - Get record for actors where actor name is 'Tim'

        ```
        SELECT * FROM actors
        WHERE first_name LIKE '%Tim%'
        ORDER BY first_name; --returns 1 record
        ```

      - Get record for actors where actor name is 'tim'

        ```
        SELECT * FROM actors
        WHERE first_name LIKE '%tim%'
        ORDER BY first_name; -- returns none
        ```

    - **How about ILIKE, is this case-sensitive too..? Actually Not**

      - Get record for actors where actor name is 'Tim'

        ```
        SELECT * FROM actors
        WHERE first_name ILIKE '%Tim%'
        ORDER BY first_name; --returns 1 record
        ```

      - Get record for actors where actor name is 'tim'

        ```
        SELECT * FROM actors
        WHERE first_name ILIKE '%tim%'
        ORDER BY first_name; --returns 1 record
        ```

- **Using IS NULL and IS NOT NULL keywords**

  - IS NULL operator checks if a value is NULL

    - Returns only null values

  - IS NOT NULL operator checks if a value is NOT NULL

    - Returns only null values

  - **Syntax**

    ```
    SELECT columnlist FROM tablename WHERE columnname IS NULL
    ```

    ```
    SELECT columnlist FROM tablename WHERE columnname IS NOT NULL
    ```

  - **Examples**

    - Get a list of actors with missing date_of_birth

      ```
      SELECT * FROM actors
      WHERE date_of_birth IS NULL;
      ```

    - Get a list of actors with missing birth date or missing first name

      ```
      SELECT * FROM actors
      WHERE date_of_birth IS NULL
      OR first_name IS NULL
      ORDER BY first_name;
      ```

    - Get a list of movies where domestic revenues is null

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic IS NULL;
      ```

    - Get a list of movies where either domestic revenues or international revenues is null

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic IS NULL
      OR revenues_international IS NULL;
      ```

    - Get a list of movies where either domestic revenues and international revenues is null

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic IS NULL
      AND revenues_international IS NULL;
      ```

    - Get a list of movies where domestic revenues are not null

      ```
      SELECT * FROM movies_revenues
      WHERE revenues_domestic IS NOT NULL;
      ```

  - **How about using the following instead of NULL**

    - = NULL

      ```
      SELECT * FROM actors
      WHERE date_of_birth = NULL; -- returns 0 records
      ```

    - = 'NULL'

      ```
      SELECT * FROM actors
      WHERE date_of_birth = 'NULL'; --ERROR:  invalid input syntax for type date: "NULL"
      ```

    - = ""

      ```
      SELECT * FROM actors
      WHERE date_of_birth = ""; --ERROR:  zero-length delimited identifier at or near """"
      ```

    - = ' '

      ```
      SELECT * FROM actors
      WHERE date_of_birth = ' ';--ERROR:  invalid input syntax for type date: " "
      ```

- **Concatenation Techniques**

  - To concatenate 2 or more strings into one , we use concatenation operator ||

    - Combining Strings together

      ```
      SELECT 'string1' || 'string2' AS new_string;
      ```

    - Combining columns together

      ```
      SELECT CONCAT(column1,column2) AS new_string;
      ```

    - Combining columns together using CONCAT_WS

      - CONCAT_WS - concatenates strings into one separated by a particular operator

        ```
        SELECT CONCAT_WS('|',column1,column2) AS new_string;
        ```

  - **Examples**

    - Combine string 'Hello' and 'World'

      ```
      SELECT 'Hello' || 'World' AS new_string;
      ```

      - Adding some space between

        ```
        SELECT 'Hello' ||' '|| 'World' AS new_string;
        ```

    - Combine actor first name and last name into 'Actor Name'

      ```
      SELECT CONCAT(first_name,last_name) AS "Actor Name" FROM actors
      ORDER BY first_name;
      ```

    - Add a separator between first name and last name

      ```
      SELECT CONCAT(first_name,' ',last_name) AS "Actor Name" FROM actors
      ORDER BY first_name;
      ```

    - Print first name , last name and dob separated by a comma

      ```
      SELECT CONCAT_WS(', ',first_name,last_name,date_of_birth) AS "Actor Name" FROM actors
      ORDER BY first_name;
      ```

      ```
      SELECT CONCAT_WS(' | ',first_name,last_name,date_of_birth) AS "Actor Name" FROM actors
      ORDER BY first_name;
      ```

- **Concatenation Techniques - How NULLS are handled**

  - Are NULL values ignored ?

    - Using ||

      ```
      SELECT 'Hello' || NULL || 'World'; --null
      ```

      ```
      SELECT 'Hello' || 'NULL' || 'World'; --HelloNULLWorld
      ```

    - Using CONCAT

      ```
      SELECT CONCAT(revenues_domestic,'|',revenues_international) AS profits from movies_revenues;
      ```

    - Using CONCAT_WS (smart - ignores null values)

      ```
      SELECT CONCAT('|',revenues_domestic,revenues_international) AS profits from movies_revenues;
      ```

## Section 6: PostgreSQL Data types

- **Boolean Data type**

  - PostgreSQL supports a single boolean data type.

  - Boolean can have 3 values :

    - True
    - False
    - Null

  - Valid literals for boolean values in PostgreSQL

    - must be enclosed in single quotes except true and false

      |  TRUE  | FALSE   |
      | :----: | :------ |
      |  TRUE  | FALSE   |
      | 'true' | 'false' |
      |  't'   | 'f'     |
      |  'y'   | 'n'     |
      | 'yes'  | 'no'    |
      |  '1'   | '0'     |

    - create sample table_boolean and insert some sample data

      ```
      CREATE TABLE table_boolean(
        product_id SERIAL PRIMARY KEY,
        is_available boolean NOT NULL
      );
      ```

      ```
      INSERT INTO table_boolean(is_available)VALUES(TRUE);
      INSERT INTO table_boolean(is_available)VALUES(FALSE);
      INSERT INTO table_boolean(is_available)VALUES('true');
      INSERT INTO table_boolean(is_available)VALUES('false');
      INSERT INTO table_boolean(is_available)VALUES('t');
      INSERT INTO table_boolean(is_available)VALUES('f');
      INSERT INTO table_boolean(is_available)VALUES('y');
      INSERT INTO table_boolean(is_available)VALUES('n');
      INSERT INTO table_boolean(is_available)VALUES('yes');
      INSERT INTO table_boolean(is_available)VALUES('no');
      INSERT INTO table_boolean(is_available)VALUES('1');
      INSERT INTO table_boolean(is_available)VALUES('0');
      ```

    - Querying using conditional search

      - Valid literals for boolean values can be used in the _WHERE_ condition as well

        ```
        SELECT * FROM table_boolean WHERE is_available = TRUE; --works
        SELECT * FROM table_boolean WHERE is_available = 1; ERROR:
        SELECT * FROM table_boolean WHERE is_available = '1'; --works
        SELECT * FROM table_boolean WHERE is_available = 0; ERROR:
        SELECT * FROM table_boolean WHERE is_available = '0'; --works
        SELECT * FROM table_boolean WHERE is_available = 'false'; --works
        SELECT * FROM table_boolean WHERE is_available = 'true'; --works
        SELECT * FROM table_boolean WHERE is_available = 't'; --works
        SELECT * FROM table_boolean WHERE is_available = 'f'; --works
        ```

      - Using NOT condition
        ```
        SELECT * FROM table_boolean WHERE NOT is_available; --return false values
        SELECT * FROM table_boolean WHERE is_available; --return true values
        ```

    - Set default values for boolean columns

      ```
      ALTER TABLE table_boolean
      ALTER COLUMN is_available
      SET DEFAULT FALSE;
      ```

      ```
      INSERT INTO table_boolean(product_id)VALUES(13);
      ```

- **Characters Data type**

  - Character strings are general-purpose types and can be used to represent

    - text
    - numbers
    - symbols
    - OR a combination of the above

  - There are 3 main types of character data

    - CHARACTER(n) - CHAR(n) - _fixed-length (blank padded)_
    - CHARACTER VARYING(n) VARCHAR(n) - _variable-length with length limit_
    - TEXT , VARCHAR - _variable unlimited length_

      - _WHERE_

        - n is the number of characters that the column holds (,f not provide,default is 1)

      - _if the excessive characters are all spaces ,PostgreSQL truncates the spaces to the maximum length (n) and stores the characters_

  - CHARACTER(n) , CHAR(n)

    - char(10) will store 10 character length
    - if you insert less than 10 ,PostgreSQL will pad the rest of the column with spaces
    - if you insert more than 10 ,PostgreSQL will truncate to the max that it can store
    - _if n is not specified, default is 1_

    - Example:

      ```
      SELECT CAST('Alex' AS character(10)) AS "Name"; --"Alex      " --CAST - datatype conversion fn

      Similar to:-

      SELECT 'Alex'::char(10) AS "Name";--"Alex      "

      Combining both:-

      SELECT
         CAST('Alex' AS character(10)) AS "Name",
         'Alex'::char(10) AS "Name2";

      Output is exactly the same:
        "Alex      "
        "Alex      "
      ```

    - Use cases :
      - country codes (US,UK,JP...) - char(2)

  - CHARACTER VARYING(n) , VARCHAR(n) - variable-length with length limit

    - useful if entries in a column can vary but you don't want PostgreSQL to pad the fields with blanks
    - stores the number of characters provided. _Save space! :)_
    - if more characters are provided than the max, the rest from maximum are truncated
    - no default value exist for this type
    - _n rep max no of characters the col can hold_

    - Examples :

      ```
      SELECT 'Alex'::varchar(10) AS Name; --"Alex"
      SELECT 'THIS IS A TEST FROM THE SYSTEM'::varchar(10) AS Random; --""THIS IS A ""
      SELECT 'ABCD 123 $%#@!'::varchar(20); --"ABCD 123 $%#@!"
      ```

  - TEXT - variable-length column , any size

    - variable-length column type
    - unlimited length (per PostgreSQL says max is 1GB)
    - Not part of SQL standard , but similar type available in Microsoft SQL server and MySQL

    - Examples :

      ```
      SELECT 'Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.'::text AS "Lorem Ipsum";
      ```

  - Create a Table for all character types & insert sample data

    ```
    CREATE TABLE table_characters(
      col_char CHAR(10),
      col_varchar VARCHAR(10),
      col_text TEXT
    );
    ```

    ```
    INSERT INTO table_characters (col_char,col_varchar,col_text)
    VALUES
    ('ABC','ABC','ABC'),
    ('xyz','xyz','xyz');
    ```

- **NUMERIC Data type**

  - Number columns can hold number values but not null values

  - Math operations (Add,Multiply,Divide) can be perfomed on numbers data type

  - Two main types of Numbers data are :-

    - Integers - Whole Numbers (+ve and -ve)

    - Fixed-point , Floating point - (Two format of fractions of whole numbers)

  - Integers

    - Three main types of integers

      - tinyint 1 byte _(0 to 255)_
      - smallint 2 bytes _(-32768 to +32767 )_
      - integer 4 bytes _(-2147483648 to +2147483647)_
      - largeint 8 bytes _(-9223372036854775808 to +9223372036854775807)_

    - bigint is good enough for most of the situation if not all: Numbers larger than 2.1 billion

    - Db throws an error if a number is outside of it's data type range as per above stats

    - Auto-increment integer data type : SERIAL - AN ANSI standard for identity columns

      - For serial data type :

        - smallserial - 2 bytes _(1 to 32767)_
        - serial - 2 bytes _(1 to 2147483647)_
        - bigserial - 8 bytes _(1 to 9223372036854775807)_

  - Example :-

    - Create table_serial and insert some sample data

      ```
      CREATE TABLE table_serial(
       product_id SERIAL ,
       product_name VARCHAR(100)
      );
      ```

      ```
      INSERT INTO table_serial(product_name)
      VALUES
      ('Pen'),
      ('Pencil');
      ('Book');
      ```

- **DECIMALS NUMBERS Data type**

  - Decimals rep a whole number plus a fraction of the whole number

  - The fraction is rep by digits following a decimal point

  - Fixed-point numbers

    - numeric(precision,scale)

      - _WHERE_

        - _precision_ is the MAximum number of digits to the left and right of the decimal
        - _scale_ is the number of digits allowable on the right of the decimal

      - numeric(10,2) _returns 2 digits to the right of the decimal point_

    - decimal(precison,scale)

  - Floating-point numbers

    - There are 2 type of floating-point numbers

      - real - _allows precision to 6 decimal digits_

      - double - _allows precision to 15 decimal points of precision_

  - In Summary

    | Data type        | Storage Size | Storage Type   | Range                                       |
    | :--------------- | :----------- | :------------- | :------------------------------------------ |
    | numeric,decimal  | variable     | fixed-point    | Upto 131072 digits before the decimal point |
    |                  |              |                | Upto 16383 digits after the decimal point   |
    | real             | 4 bytes      | floating point | 6 decimal digits precision                  |
    | double precision | 8 bytes      | floating point | 15 decimal digits precision                 |

  - Example

    - Create table_numbers and insert some sample data

      ```
      CREATE TABLE table_numbers(
       col_numeric numeric(20,5),
       col_real real,
       col_double double precision
      );
      ```

      ```
      INSERT INTO table_numbers(col_numeric,col_real,col_double)
      VALUES
      (.9,.9,.9),
      (3.13579,3.13579,3.13579),
      (4.1357976854,4.1357976854,4.1357976854);
      ```

- **Selecting Numbers Data types**

  - Use integers whenever possible

  - Decimal data and calculations needs to be exact, then use numeric or decimal

  - Float will save space, but be careful about inexactness

  - Choose a big enough number type by looking at your data

  - With big whole numbers, use bigint only if column values constrained to fit into either smaller integer or smallint types

- **Date/Time data types**

  - One of the most important types

  - Assigned to the variable that is supposed to store only the time value

  - Below is the date /time

    | Type        | Stores                  | Low value | High value |
    | :---------- | :---------------------- | :-------- | :--------- |
    | Date        | date only               | 4713 BC   | 294276 AD  |
    | Time        | time only               | 4713 BC   | 5874897 AD |
    | Timestamp   | date and time           | 4713 BC   | 294276 AD  |
    | Timestamptz | date,time and timestamp | 4713 BC   | 294276 AD  |
    |             |                         |           |            |
    | interval    | store values            |           |            |

- **DATE data type**

  - stores date values

  - Uses 4 bytes to store date value

  - By default , uses the format YYYY-MM-DD

  - Some good useful keywords

    - CURRENT_DATE (stores current date)

  - Syntax

    column_name DATE

  - Examples :-

    - Create table_dates and add sample data

      ```
      CREATE TABLE table_dates(
        id SERIAL PRIMARY KEY,
        employee_name VARCHAR(100) NOT NULL,
        hire_date DATE NOT NULL,
        add_date DATE DEFAULT CURRENT_DATE
      );
      ```

      ```
      INSERT INTO table_dates(employee_name,hire_date)
      VALUES
      ('ADAM','2020-01-01'),
      ('LINDA','2020-02-01');
      ```

    - **Some useful functions**

      - **current date**

        ```
        SELECT CURRENT_DATE;
        ```

      - **current date and time**

        ```
        SELECT NOW();
        ```

- **TIME data type**

  - Stores time of the day values

  - Takes 8 bytes to store time

  - Syntax

    - columnname TIME (precision) - time without time zone

      - _WHERE_

        - _precision is the number of fractional digits placed in the 2nd field precision up to 6 digits_

  - **Common Formats**

    - HH:MM - _10:30_ (Hours:Min)
    - HH:MM:SS - _10:30:25_ , 23:59:59 (Hours:Min:Sec)
    - HHMMSS - _103030_ (HoursMinSec)

    - MM:SS.pppppp - 03:59.999999
    - HH:MM:SS.pppppp - 03:05:08.777777
    - HHMMSS.pppppp - 030508.777777

      - _where pppppp is the precision can be upto 6 digits_

  - Example :-

    - Create table_time and insert sample data

      ```
      CREATE TABLE table_time(
        id serial PRIMARY KEY,
        class_name VARCHAR(100) NOT NULL,
        start_time TIME NOT NULL,
        end_time TIME NOT NULL
      );
      ```

      ```
      INSERT INTO table_time(class_name,start_time,end_time)
      VALUES
      ('MATH','08:00:00','09:00:00'),
      ('CHEM','09:01:00','10:00:00');
      ```

    - **Some useful functions**

      - **current time**

        ```
        SELECT CURRENT_TIME; --returns time with time zone
        ```

      - **current time with precision**

        ```
        SELECT CURRENT_TIME(4); --20:45:45.222400+03:00
        SELECT CURRENT_TIME(2); --20:45:45.220000+03:00
        SELECT CURRENT_TIME(6); --20:45:45.222489+03:00
        ```

      - **local time**

        ```
        SELECT LOCALTIME; --20:48:11.248216
        SELECT LOCALTIME(4); --20:48:11.248216 (with precision)
        ```

      - **Perfom Arithmetic operations**

        ```
        SELECT time '12:00' - time '04:00' AS RESULT; --08:00:00
        ```

      - **Using Interval**

        - interval 'n type'

          - _WHERE_ n rep a number and type could be (second,min ,hrs,day,month,year etc)

          ```
          SELECT
          CURRENT_TIME,
          CURRENT_TIME + interval '2 hours' AS result; --Adds 2 hrs to the current time
          ```

          ```
          SELECT
          CURRENT_TIME,
          CURRENT_TIME + interval '-2 hours' AS result; --Subtract 2 hrs from the current time
          ```

- **TIMESTAMP AND TIMESTAMPTZ data type**

  - Allows to store date and time together

    - Timestamp - time without timezone

    - Timestamptz - time with timezone

      - **Timezone Handling**

        - Internally stored value is always in UTC (Universal Coordinated Time) traditionally known as Greenwich Mean Time (GMT)

      - **Adding a Timestamptz**

        - An INPUT value that has explicit time zone specified is converted to UTC using the appropriate offset for that time zone

        - If no time zone is stated in the input string , then it is assumed to be in the time zone indicated by the system's TimeZone parameter , and is converted to UTC using the offset for the timezone zone

      - **Output a Timestamptz**

        - When a timestamptz value is output, it is always converted from UTC to the current timezone zone, and displayed as local time in that zone

  - Examples :

    - Create table_time_tz and add sample data

      ```
      CREATE TABLE table_time_tz (
        ts TIMESTAMP,
        tstz TIMESTAMPTZ
      );

      INSERT INTO table_time_tz(ts,tstz)
      VALUES ('2020-02-22 10:10:10-03:00','2020-02-22 10:10:10-03:00');
      ```

      - Show current timezone

        ```
        SHOW TIMEZONE; --Africa/Nairobi
        ```

      - Current timestamp

        ```
        SELECT CURRENT_TIMESTAMP; --2023-02-21 22:20:28.758737+03
        ```

      - Current time of the day

        ```
        SELECT TIMEOFDAY(); --Tue Feb 21 22:22:02.265429 2023 EAT
        ```

      - Using timezone() to convert time based on a timezone

        - show timezone variations

          ```
          SELECT timezone('Asia/Singapore','2023-02-21 10:29:00'); --2023-02-21 15:29:00
          SELECT timezone('America/Los_angeles','2023-02-21 10:26:00'); -- 2023-02-20 23:28:00
          SELECT timezone('America/New_York','2023-02-21 10:26:00'); -- 2023-02-21 02:26:00
          SELECT timezone('Africa/Nairobi','2023-02-21 10:26:00'); --2023-02-21 10:27:00
          ```

- **UUID (Universal Unique Identifier) data type**

  - A 128-bit quantity generated by an algorithm that makes it unique in the universe using the same algorithm

    - 40e6215d-b5c6-4896-987c-f30f3678f608

      - 32 digits
      - hexadecimal digits
      - separated by hyphen/dashes

  - Benefits

    - UUID much better than SERIAL when it comes to 'uniqueness' across systems as SERIAL data type generates only unique values within a single database

  - Usage

    - To create a UUID datatype in PostgreSQL, you need a third party UUID algorithms to generate UUID e.g uuid-ossp

      - Enable third party UUID extensions first e.g uuid-ossp

        ```
        CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
        ```

      - Generate a sample UUID value

        ```
        SELECT uuid_generate_v1();
        --generates UUD version 1 "c01a258a-be45-11ed-a37e-40b03449fa25"
        ```

        ```
        SELECT uuid_generate_v4();
        --generates UUD version 1 "c01a258a-be45-11ed-a37e-40b03449fa25"
        ```

        - uuid_generate_v1()

          - generates uuid using the computer MAC address
          - Uses the current timestamp and some random value to generate the code
          - Very powerful at the same time

        - uuid_generate_v4()

          - purely random generated number

  - Example

    - Create table_uuid and add sample data

      ```
      CREATE TABLE table_uuid(
        product_id UUID DEFAULT uuid_generate_v1(),
        product_name VARCHAR(100) NOT NULL
      );

      INSERT INTO table_uuid(product_name) VALUES('ABC');
      INSERT INTO table_uuid(product_name) VALUES('ABCD');
      ```

    - Change column uuid DEFAULT uuid_generate_v1() to uuid_generate_v4()

      ```
      ALTER TABLE table_uuid
      ALTER COLUMN product_id
      SET DEFAULT uuid_generate_v4();


      INSERT INTO table_uuid(product_name) VALUES('ABCDE');
      INSERT INTO table_uuid(product_name) VALUES('ABCDEF');
      ```

- **Array Data type**

  - allows columns of a table to be defined as variable-length multidimensional arrays.

  - Every data type has its own companion array type

    - e.g.

      - integer has an integer[] array type
      - character has character[] array type

  - An array datatype is named by appending square brackets [] to the data type name of the array elements

  - Syntax

    - variable_name DATATYPE [];

    - phone TEXT []

  - Example

        - Create table_array and add sample data

          ```
          CREATE TABLE table_array(
            id SERIAL PRIMARY KEY ,
            name VARCHAR(100),
            phone TEXT [] --our array
          );

          INSERT INTO table_array(name,phone)
          VALUES('John Doe',ARRAY [ '(801)-589-4567','(819)-555-2222' ]);

          INSERT INTO table_array(name,phone)
          VALUES('John Doe',ARRAY [ '(201)-123-5846','(214)-222-3333' ]);
          ```

        - You can also use curly braces as follows:

          ```
          INSERT INTO contacts (name, phones)
           VALUES
           ('Lily Bush','{"(408)-589-5841"}'),
           ('William Gate','{"(408)-589-5842","(408)-589-58423"}');
          ```

        - Query data

          ```
          SELECT name,phone[1] FROM table_array; --returns the first array element (index-1 based)
          ```

          ```
          SELECT name FROM table_array
          WHERE phone[1] = '(214)-222-3333'; --returns John Doe
          ```

- **hstore Data type**

  - Definition

    - hstore is a datatype that stores data into key-value pairs (close to how we store data in json)

    - hstore module implements the hstore data type

    - the keys and values are just text strings only

  - Usage

    - Install/enable hstore extension first

      ```
      CREATE EXTENSION IF NOT EXISTS hstore;
      ```

  - Example

    - Create table_hstore and add sample data

      ```
      CREATE TABLE table_hstore(
        book_id SERIAL PRIMARY KEY,
        title VARCHAR(100) NOT NULL,
        book_info hstore
      );

      INSERT INTO table_hstore(title,book_info)
      VALUES
      ('Atomic Habits',
      '
        "author" => "James Clear",
        "publisher" => "Warner group",
        "paper_cost" => "$9.99",
        "e_cost" => "$5.99"
      ');

      INSERT INTO table_hstore(title,book_info)
      VALUES
      ('Rich Dad Poor Dad',
      '
        "author" => "Robert T. Kiyosaki",
        "publisher" => "Warner group",
        "paper_cost" => "$9.99",
        "e_cost" => "$5.99"
      ');
      ```

    - Query with a specific store value (-> operator)

      ```
      SELECT title,book_info -> 'author' AS Author FROM table_hstore;

      SELECT title,book_info -> 'e_cost' AS "Electronic Cost" FROM table_hstore;
      ```

- **json Data type**

  - PostgreSQL has two native data types to store JSON documents:

    - JSON

      - is a blob (Binary Large Object) that stores JSON data in raw format, preserving even insignificant things such as whitespace, the order of keys in objects, or even duplicate keys in objects.

      - offers limited querying capabilities, and it's slow because it needs to load and parse the entire JSON blob each time.

    - JSONB
      - stores data in binary format
      - More powerful - it allows the contents to be indexed and queried with ease.

  - Example

    - Create table_json and add sample data

      ```
      CREATE TABLE table_json(
        id SERIAL PRIMARY KEY,
        docs json
      );

      INSERT INTO table_json(docs)
      VALUES
      ('[1,2,3,4,5,6]'), --option-1 []
      ('{"name":"Alex"}'); --option-2 {}

      SELECT docs FROM table_json;
      ```

    - Search specific data in JSON column (Containment operator @>)

      - The containment operator @> tests whether one document contains another.

        ```
        SELECT * FROM table_json
        WHERE docs @> '2';
        ```

        - WON'T Work!! - Need to convert into jsonb first

          ```
          ALTER TABLE table_json
          ALTER COLUMN docs
          TYPE JSONB;
          ```

        - WORKS NOW !!!

          ```
          SELECT * FROM table_json
          WHERE docs @> '2';
          ```

    - Indexing JSONB

      ```
      CREATE INDEX ON table_json USING GIN (docs jsonb_path_ops);
      ```

  - JSONB Functions

        - jsonb_each()

          - Expands the top-level JSON document into a set of key-value pairs.

            ```
            select jsonb_each( '{"name": "Alice", "agent": {"bot": true} }'::jsonb );

            --returns

              (name,"Alice")
              (agent,"{"bot": true}")
            ```

        - jsonb_object_keys()

          - Returns the keys of the top-level JSON document.

            ```
            select jsonb_object_keys( '{"name": "Alice", "agent": {"bot": true} }'::jsonb );

            --returns name,agent
            ```

        - jsonb_extract_path()

          - Returns a JSON object that is traversed by a "path".

            ```
            select jsonb_extract_path( '{"name": "Alice", "agent": {"bot": true} }'::jsonb, 'agent', 'bot');

            -returns true
            ```

        - jsonb_pretty()

          - PostgreSQL returns a compact representation which works for machine consumption

          - If you want your JSON documents pretty printed for human consumption, use jsonb_pretty()

            ```
            select jsonb_pretty( '{"name": "Alice", "agent": {"bot": true} }'::jsonb );

            -- returns the following
                {
                  "name": "Alice",
                  "agent": {
                           "bot": true
                        }
            }
            ```

- **Network Addresses Data type**

  - Network Address types

    - PostgreSQL offers data types to store IPv4, IPv6, and MAC addresses

      | Name     | Storage Size  | Description                      |
      | :------- | :------------ | :------------------------------- |
      | cidr     | 7 or 19 bytes | IPv4 and IPv6 networks           |
      | inet     | 7 or 19 bytes | IPv4 and IPv6 hosts and networks |
      | macaddr  | 6 bytes       | MAC addresses                    |
      | macaddr8 | 8 bytes       | MAC addresses (EUI-64 format)    |

    - It is better to use these types instead of plain text types to store network addresses, because these types offer input error checking and specialized operators and functions

    - cidr and inet mostly common

  - Special sorting mechanisms

    - When sorting inet or cidr data types, IPv4 addresses will always sort before IPv6 addresses, including IPv4 addresses encapsulated or mapped to IPv6 addresses, such as ::10.2.3.4 or ::ffff:10.4.3.2.

  - Example

    - Let's build a sample table with an IP address for IPv4 and IPv6 network address type .i.e inet

      ```
      CREATE TABLE table_netaddr(
        id SERIAL PRIMARY KEY,
        ip INET
      );

      INSERT INTO table_netaddr(ip)
      VALUES
      ('4.35.221.243'),
      ('4.152.207.126'),
      ('4.152.207.238'),
      ('4.249.111.162'),
      ('12.1.223.132'),
      ('12.8.192.60');
      ```

    - Functions that we can use on ip addresses entries

      - set_masklen() - set netmask length for inet value

        ```
        SELECT ip, set_masklen(ip,24) AS inet_24 FROM table_netaddr;
        ```

    - Convert inet to cidr type

      ```
       SELECT
        ip,
        set_masklen(ip,24) AS inet_24,
        set_masklen(ip::cidr,24) AS cidr_24
       FROM table_netaddr;
      ```

    - Analyze other network like /27 , /28

      ```
      SELECT
       ip,
       set_masklen(ip,24) AS inet_24,
       set_masklen(ip::cidr,24) AS cidr_24,
       set_masklen(ip::cidr,27) AS cidr_27,
       set_masklen(ip::cidr,28) AS cidr_28
      FROM table_netaddr;
      ```

## Section 7: Modifying Table Structures - Add Constraints

- **Create Sample Database MYDATA - Add Columns**

  ```
  CREATE DATABASE MYDATA;
  ```

  - Create Persons table

    ```
      CREATE TABLE persons(
        person_id SERIAL PRIMARY KEY,
        first_name VARCHAR(20) NOT NULL,
        last_name VARCHAR(20) NOT NULL
      );
    ```

  - **Add 2 columns - age , nationality , email-unique**

    ```
    ALTER TABLE persons
    ADD COLUMN age INT NOT NULL;
    ```

    ```
    ALTER TABLE persons
    ADD COLUMN nationality VARCHAR(20) NOT NULL,
    ADD COLUMN email VARCHAR(100) UNIQUE
    ```

- **Modify Table Structures - Add/Modify Columns**

  - **Rename a table**

    ```
    ALTER TABLE persons
    RENAME TO users;
    ```

  - **Rename a column**

    ```
    ALTER TABLE persons
    RENAME COLUMN age TO person_age;
    ```

  - **Drop a column**

    ```
    ALTER TABLE persons
    DROP COLUMN person_age;
    ```

  - **Change Data type of a column**

        ```
        ALTER TABLE persons
        ALTER COLUMN age TYPE int;
        ```

        - **WON'T WORK !!!**
        - **ERROR: column "age" cannot be cast automatically to type integer**
        - **HINT: You might need to specify "USING age::integer".SQL state: 42804**
        - **Means you cannot varchar(10) directly to int**

          ```
          ALTER TABLE persons
          ALTER COLUMN age TYPE int
          USING age::integer;
          ```

        - The above works now

  - **Set a default value of a Column**

    - **Add a new column to persons table**

      ```
      ALTER TABLE persons
      ADD COLUMN is_enable VARCHAR(1);
      ```

    - **Set a default value**

      ```
      ALTER TABLE persons
      ALTER COLUMN is_enable SET DEFAULT 'Y';
      ```

      - **Add sample data**

      ```
      INSERT INTO persons(first_name,last_name,nationality,age)
      VALUES
      ('Jane','Doe','Kenyan',32);
      ```

- **Add constraints to columns**

  - Create table web_links and add sample data

    ```
    CREATE TABLE web_links(
      link_id SERIAL PRIMARY KEY,
      link_url VARCHAR(255) NOT NULL,
      link_target VARCHAR(20)
    );

    INSERT INTO web_links(link_url,link_target)
    VALUES
    ('https://www.google.com','_blank');
    ```

  - **Add UNIQUE CONSTRAINT to link_url Column**

    ```
    ALTER TABLE web_links
    ADD CONSTRAINT unique_web_url UNIQUE(link_url);
    ```

  - **Set a column to accept only defined allowed/acceptable values**

    - **Add is_enable column**

      ```
      ALTER TABLE web_links
      ADD COLUMN is_enable VARCHAR(2);

      INSERT INTO web_links(link_url,link_target,is_enable)
      VALUES
      ('https://www.amazon.com','_blank','Y');
      ```

    - **Set is_enable to allow Y/N only**

      ```
      ALTER TABLE web_links
      ADD CHECK (is_enable IN ('Y','N'));

      INSERT INTO web_links(link_url,link_target,is_enable)
      VALUES
      ('https://www.citi.com','_blank','Q');


      --ERROR:  new row for relation "web_links" violates check constraint "web_links_is_enable_check"

      UPDATE web_links
      SET is_enable = 'Y'
      WHERE link_id = 1; --Success

      UPDATE web_links
      SET is_enable = 'Q'
      WHERE link_id = 2; --ERROR:  new row for relation "web_links" violates check constraint "web_links_is_enable_check"
      ```

## Section 8: Data type Conversions

- **What is a data type conversion**

  - Type Conversion is the conversion of ORIGINAL data type to ANOTHER data type

  - There are 2 types of conversions

    - **Implicit**

      - Data conversion is done AUTOMATICALLY by PostgreSQL engine

    - **Explicit**

      - done via 'conversion functions' e.g. CAST or ::

  - Examples

    - integer = integer

      ```
      SELECT * FROM movies
      WHERE movie_id = 1; --same data type, so NO conversion
      ```

    - integer = string

      ```
      SELECT * FROM movies
      WHERE movie_id = '1'; --PostgreSQL does the conversion here .i.e. Implicit
      ```

    - Explicit conversion

      ```
      SELECT * FROM movies
      WHERE movie_id = integer '1'; --Explicit conversion
      ```

- **Using CAST for data conversions**

  - PostgreSQL CAST operator is used to covert a value of one type to another data type

  - Syntax : CAST (expression AS target_data_type)

    - WHERE

      - _expression_ - represents a constant , a table column or an expression

      - _target data type_ - rep

        - Boolean
        - Character (char , varchar, text)
        - Numeric (integer, floating point number)
        - array
        - JSON
        - UUID
        - hstore (stores as key/value pairs)
        - Temporal type (date , time,timestamp,interval)
        - Special type (network address , geometric data)

  - Example

    - **String to Integer conversion**

      ```
      SELECT CAST('10' AS INTEGER); --SUCCESS
      SELECT CAST('10n' AS INTEGER); --ERROR:  invalid input syntax for integer: "10n"
      ```

    - **String to Date conversion**

      ```
      SELECT
        CAST('2020-01-01' AS DATE),
        CAST('01-MAY-2020' AS DATE);
      ```

    - **String to Boolean conversion**

      ```
      SELECT
       CAST('true' AS BOOLEAN),
       CAST('false' AS BOOLEAN),
       CAST('T' AS BOOLEAN),
       CAST('F' AS BOOLEAN);
      ```

      ```
      SELECT
        CAST('0' AS BOOLEAN),
        CAST('1' AS BOOLEAN);
      ```

    - **String to Double conversion**

      ```
      SELECT
        CAST('14.7888' AS DOUBLE PRECISION );
      ```

    - **You can also use the folllowing syntax for conversion directly too**

      ```
      expression::type
      ```

      ```
      SELECT
        '10'::INTEGER,
        '2020-01-01'::DATE,
        '01-01-2020'::DATE;
      ```

    - **String to Timestamp conversion**

      ```
      SELECT
        '2020-02-20 10:30:25.467'::TIMESTAMP; --Without timezone "2020-02-20 10:30:25.467"
      ```

      ```
      SELECT
        '2020-02-20 10:30:25.467'::TIMESTAMPTZ; --With timezone "2020-02-20 10:30:25.467+03"
      ```

    - **String to interval conversion**

      ```
      SELECT
        '10 minute'::interval,
        '4 hour'::interval,
        '1 day'::interval,
        '2 week'::interval,
        '5 month'::interval;
      ```

      | interval | interval | interval | interval | interval |
      | :------- | :------- | :------- | :------- | :------- |
      | 00:10:00 | 04:00:00 | 1 day    | 14 days  | 5 mons   |

- **Implicit to Explicit conversions**

  - **Using integer and factorial**

    ```
    SELECT 20 !;
    ```

    | ?column?            |
    | :------------------ |
    | 2432902008176640000 |

    ```
    SELECT 20 ! AS Result;
    ```

    | Result              |
    | :------------------ |
    | 2432902008176640000 |

  - **Round with numeric**

    ```
    SELECT ROUND(10,4) AS result;
    ```

    | Result  |
    | :------ |
    | 10.0000 |

    ```
    SELECT ROUND(CAST(10 AS NUMERIC),4) AS result; --more explicit
    ```

    | Result  |
    | :------ |
    | 10.0000 |

  - **Using CAST with Text**

    - SUBSTR - Takes some part of the string starting from the specified position

      ```
      SELECT SUBSTR('123456',2) AS Result;
      ```

      | Result |
      | :----- |
      | 23456  |

      ```
      SELECT
        SUBSTR('123456',2) AS Implicit,
        SUBSTR(CAST('123456' AS TEXT),4) AS Explicit;
      ```

      | Implicit | Explicit |
      | :------- | :------- |
      | 23456    | 456      |

- **Table data conversion**

  - Create table 'ratings' with initial data as characters and insert some sample data

    ```
    CREATE TABLE ratings(
      rating_id SERIAL PRIMARY KEY,
      rating VARCHAR(1) NOT NULL
    );

    INSERT INTO ratings(rating)
    VALUES
    ('A'),
    ('B'),
    ('C'),
    ('D');

    ```

  - Now say the business requirement changes and they want all ratings to be numeric

    - so let's add some integer data

      ```
      INSERT INTO ratings(rating)
      VALUES
      (1),
      (2),
      (3),
      (4);
      ```

    - Then convert all values in the rating column into integers

    - Use CAST to change all non numeric data into integers

      ```
      SELECT
        rating_id,
        CASE
          WHEN rating~E'^\\d+$'THEN
            CAST(rating AS INTEGER)
          ELSE
            0
        END AS rating
      FROM ratings;
      ```

## Section 9: Conversion Functions

- **TO_CHAR()**

  - PostgreSQL TO_CHAR() converts

    - a timestamp
    - an interval
    - an integer
    - a double precision or
    - a numeric value

      - **to a string**

  - Syntax TO_CHAR(expression, format )

  - TO_CHAR() function requires two arguments:

    - WHERE

      - _expression_ can be a timestamp, an interval, an integer, a double precision, or a numeric value that is converted to a string according to a specific format.

      - _format_ rep

        - valid numeric format strings:

          | Format   | Description                                                              |
          | :------- | :----------------------------------------------------------------------- |
          | 9        | Numeric value with the specified number of digits                        |
          | 0        | Numeric value with leading zeros                                         |
          | .        | (period) decimal point                                                   |
          | D        | decimal point that uses locale                                           |
          | ,        | (comma) group (thousand) separator                                       |
          | FM       | Fill mode, which suppresses padding blanks and leading zeroes.           |
          | PR       | Negative value in angle brackets.                                        |
          | S        | Sign anchored to a number that uses locale                               |
          | L        | Currency symbol that uses locale                                         |
          | G        | Group separator that uses locale                                         |
          | MI       | Minus sign in the specified position for numbers that are less than 0.   |
          | PL       | Plus sign in the specified position for numbers that are greater than 0. |
          | SG       | Plus / minus sign in the specified position                              |
          | RN       | Roman numeral that ranges from 1 to 3999                                 |
          | TH or th | Upper case or lower case ordinal number suffix                           |

        - valid numeric format strings:

          | Pattern                  | Description                                                                                      |
          | :----------------------- | :----------------------------------------------------------------------------------------------- |
          | Y,YYY                    | year in 4 digits with comma                                                                      |
          | YYYY                     | year in 4 digits                                                                                 |
          | YYY                      | last 3 digits of year                                                                            |
          | YY                       | last 2 digits of year                                                                            |
          | Y                        | The last digit of year                                                                           |
          | IYYY                     | ISO 8601 week-numbering year (4 or more digits)                                                  |
          | IYY                      | Last 3 digits of ISO 8601 week-numbering year                                                    |
          | IY                       | Last 2 digits of ISO 8601 week-numbering year                                                    |
          | I                        | Last digit of ISO 8601 week-numbering year                                                       |
          | BC, bc, AD or ad         | Era indicator without periods                                                                    |
          | B.C., b.c., A.D. ora.d.  | Era indicator with periods                                                                       |
          | MONTH                    | English month name in uppercase                                                                  |
          | Month                    | Full capitalized English month name                                                              |
          | month                    | Full lowercase English month name                                                                |
          | MON                      | Abbreviated uppercase month name e.g., JAN, FEB, etc.                                            |
          | Mon                      | Abbreviated capitalized month name e.g, Jan, Feb, etc.                                           |
          | mon                      | Abbreviated lowercase month name e.g., jan, feb, etc.                                            |
          | MM                       | month number from 01 to 12                                                                       |
          | DAY                      | Full uppercase day name                                                                          |
          | Day                      | Full capitalized day name                                                                        |
          | day                      | Full lowercase day name                                                                          |
          | DY                       | Abbreviated uppercase day name                                                                   |
          | Dy                       | Abbreviated capitalized day name                                                                 |
          | dy                       | Abbreviated lowercase day name                                                                   |
          | DDD                      | Day of year (001-366)                                                                            |
          | IDDD                     | Day of ISO 8601 week-numbering year (001-371; day 1 of the year is Monday of the first ISO week) |
          | DD                       | Day of month (01-31)                                                                             |
          | D                        | Day of the week, Sunday (1) to Saturday (7)                                                      |
          | ID                       | ISO 8601 day of the week, Monday (1) to Sunday (7)                                               |
          | W                        | Week of month (1-5) (the first week starts on the first day of the month)                        |
          | WW                       | Week number of year (1-53) (the first week starts on the first day of the year)                  |
          | IW                       | Week number of ISO 8601 week-numbering year (01-53; the first Thursday of the year is in week 1) |
          | CC                       | Century e.g, 21, 22, etc.                                                                        |
          | J                        | Julian Day (integer days since November 24, 4714 BC at midnight UTC)                             |
          | RM                       | Month in upper case Roman numerals (I-XII; >                                                     |
          | rm                       | Month in lowercase Roman numerals (i-xii; >                                                      |
          | HH                       | Hour of day (0-12)                                                                               |
          | HH12                     | Hour of day (0-12)                                                                               |
          | HH24                     | Hour of day (0-23)                                                                               |
          | MI                       | Minute (0-59)                                                                                    |
          | SS                       | Second (0-59)                                                                                    |
          | MS                       | Millisecond (000-999)                                                                            |
          | US                       | Microsecond (000000-999999)                                                                      |
          | SSSS                     | Seconds past midnight (0-86399)                                                                  |
          | AM, am, PM or pm         | Meridiem indicator (without periods)                                                             |
          | A.M., a.m., P.M. or p.m. | Meridiem indicator (with periods)                                                                |

  - Return Value

    - TO_CHAR() function returns a string in TEXT data type that represents the first argument formatted according to the specified format.

  - Examples

        - **Convert an integer to a string**

          ```
          SELECT
            TO_CHAR(10070,9,9999);
          ```

          | to_char |
          | :------ |
          | 1,0070  |

        - **Convert date into DD-MM-YYYY format**

          ```
          SELECT
            release_date,
            TO_CHAR(release_date,'DD-MM-YYYY'),
            TO_CHAR(release_date,'Dy ,MM,YYYY')
          FROM movies
          ```

          |to_char|to_char|to_char|
          |:------|:------|:------|
          |1972-02-02 | 02-02-1972 | Wed ,02,1972|
          |1979-08-15 | 15-08-1979 | Wed ,08,1979|

        - **Convert timestamp literal to a string**

          ```
          SELECT
            TO_CHAR(
            TIMESTAMP '2020-01-01 10:30:45',
            'HH24:MI:SS'
            );
          ```
          |to_char|
          |:------|
          |10:30:45|

        - **Adding Currency symbol to movies revenues (prices)**

          ```
          SELECT
            movie_id,
            revenues_domestic,
            TO_CHAR(revenues_domestic,'$999D99') -- $ currency ndigits 2 decimal places
          FROM movies_revenues;
          ```

          |movie_id|revenues_domestic|to_char|
          |:-------|:----------------|:------|
          |45      |22.20            |$    22.20|
          |13      |199.40           |$   199.40|

- **TO_NUMBER()**

  - TO_NUMBER() function converts a character string to a numeric value.

  - Syntax TO_NUMBER(string, format)

    - _WHERE_

      - _string_ - String to be converted to a number

      - _format_ - see valid numeric formats above

  - Return Value

    - TO_NUMBER() function returns a value whose data type is numeric.

  - Example

    - **Convert a string to a number**

      ```
      SELECT
        TO_NUMBER('1420.89','9999.9');
      ```

      | to_number |
      | :-------- |
      | 1420.8    |

      ```
      SELECT
        TO_NUMBER('12,345.6-','99G999D9S');
      ```

      | to_number |
      | :-------- |
      | -12345.6  |

    - **Convert a money amount to a number**

      ```
      SELECT
        TO_NUMBER('$1,234,567.89','L9G999g999.99');
      ```

      | to_number  |
      | :--------- |
      | 1234567.89 |

    - Format control

      ```
      SELECT
        TO_NUMBER('1,234,567.89','9G999g999');
      ```

      | to_number |
      | :-------- |
      | 1234567   |

- **TO_DATE()**

  - TO_DATE() function converts a string literal to a date value.

  - Syntax TO_DATE(text,format)

    - _WHERE_

      - _text_ - The first argument is the string that you want to convert to a date.

      - _format_ - see valid date formats above.

  - Return Value

    - TO_DATE() function returns a date value.

  - Examples

    - **String to date**

      ```
      SELECT
        TO_DATE('2023/03/11','YYYY/MM/DD');
      ```

      | to_date    |
      | :--------- |
      | 2023-03-11 |

      ```
      SELECT
        TO_DATE('11032023','MMDDYYYY');
      ```

      | to_date    |
      | :--------- |
      | 2023-03-11 |

      ```
      SELECT
        TO_DATE('March 11, 2023','Month DD, YYYY');
      ```

      | to_date    |
      | :--------- |
      | 2023-03-11 |

  - PostgreSQL TO_DATE gotchas

    - If you pass an invalid date string, the TO_DATE() function will try to convert it to a valid date and issue an error if it cannot. For example:

      ```
      SELECT TO_DATE('2017/02/30', 'YYYY/MM/DD');

      ERROR:  date/time field value out of range: "2017/02/30"
      LINE 1: SELECT '2017/02/30'::date;
      ```

      ```
      SELECT TO_DATE('2023/02/28', 'YYYY/MM/DD');

      | to_date    |
      | :--------- |
      | 2023-02-28 |
      ```

- **TO_TIMESTAMP()**

  - TO_TIMESTAMP() function converts a string to a timestamp according to the specified format.

  - Syntax - TO_TIMESTAMP(timestamp, format)

    - _WHERE_

      - _timestamp_ is a string that represents a timestamp value in the format specified by format.

      - _format_ rep valid date and time values formats (_see valid datetime formats_)

  - Return Value

    - TO_TIMESTAMP() function returns a timestamp with time zone.

  - Example

    - Convert a string to a timestamp

      ```
      SELECT
        TO_TIMESTAMP('2023-03-11 11:39:20','YYYY-MM-DD HH:MI:SS');
      ```

      | to_timestamp           |
      | :--------------------- |
      | 2017-03-31 09:30:20+03 |

  - TO_TIMESTAMP() function skips spaces in the input string unless the fixed format global option (FX prefix) is used.

    ```
    SELECT
     TO_TIMESTAMP('2017     Aug','YYYY MON');
    ```

    | to_timestamp           |
    | :--------------------- |
    | 2023-03-01 00:00:00+03 |

## Section 10: User-defined types

- PostgreSQL allows you to create user-defined data types through the following statements:

  - **CREATE DOMAIN** creates a user-defined data type with constraints such as NOT NULL, CHECK, etc.

  - **CREATE TYPE** creates a composite type used in stored procedures as the data types of returned values.

        # PostgreSQL CREATE DOMAIN statement

        - In PostgreSQL, a domain is a data type with optional constraints e.g. NOT NULL and CHECK

        - A domain has a unique name within the schema scope - cannot be re-used outside of scope where they are defined.

        - Domains are useful for centralizing the management of fields with common constraints.

        - For example, some tables may have the same column that do not accept NULL and spaces.

        - SYNTAX

          ```
          CREATE DOMAIN name AS datatype CONSTRAINT;
          ```

        - Examples

          - **Create user-defined datatype that checks range and not null**

            ```
            CREATE DOMAIN addr AS VARCHAR(20) NOT NULL;
            ```

            - **Usage Create 'locations' table and insert sample data**

              ```
              CREATE TABLE locations(
                --address VARCHAR(20) NOT NULL
                address addr
              );

              INSERT INTO locations(address)VALUES('Nairobi KE');
              ```

            - **Test it out**

              ```
              INSERT INTO locations(address)VALUES('Nairobi KE Nairobi KE Nairobi KE Nairobi KE');

              ERROR:  value too long for type character varying(20)
              SQL state: 22001
              ```

          - **Create user-defined datatype (positive_numeric) that checks for positive number > 0**

            ```
            CREATE DOMAIN positive_numeric AS INT NOT NULL CHECK(VALUE > 0);
            ```

            - **Usage Create 'sample' table and insert sample data**

              ```
              CREATE TABLE sample(
                sample_id SERIAL PRIMARY KEY,
                value_number positive_numeric
              );

              INSERT INTO sample(value_number)VALUES(10);
              ```

            - **Test it out**

              ```
              INSERT INTO sample(value_number)VALUES(-10);

              ERROR:  value for domain positive_numeric violates check constraint "positive_numeric_check"
              ```

          - **Create user-defined datatype (postal_code) that validates postal_code**

            ```
            CREATE DOMAIN
             us_postal_code TEXT
             CHECK (
              VALUE ~'^\d{5}$'
              OR
              VALUE ~'^\D{5} - \d{4}$'
              );
            ```

            - **Usage Create 'addresses' table and insert sample data**

              ```
              CREATE TABLE addresses(
                address_id SERIAL PRIMARY KEY,
                postal_code us_postal_code
              );

              INSERT INTO addresses(postal_code)VALUES('10000');
              ```

            - **Test it out**

              ```
              INSERT INTO addresses(postal_code)VALUES('10000-1000-10000');
              ERROR:  value for domain us_postal_code violates check constraint "us_postal_code_check"
              ```

          - **Create user-defined datatype (proper_email) that validates user/employee/ email**

            ```
            CREATE DOMAIN
              proper_email VARCHAR(255)
              CHECK(VALUE ~ '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$');
            ```

            - **Usage Create 'client_names' table and insert sample data**

              ```
              CREATE TABLE client_names(
                id SERIAL PRIMARY KEY,
                email proper_email
              );

              INSERT INTO client_names(email)VALUES('testt@test.com');
              ```

            - **Test it out**

              ```
              INSERT INTO client_names(email)VALUES('somerandomemailhere');
              ERROR:  value for domain proper_email violates check constraint "proper_email_check"
              ```

          - **Create user-defined Enum datatype (valid_colors) that checks for defined set of color values**

            ```
            CREATE DOMAIN
              valid_color VARCHAR(10)
              CHECK(VALUE IN ('Red','Green','Blue'));
            ```

            ```
            CREATE DOMAIN
              user_status VARCHAR(10)
              CHECK(VALUE IN ('ACTIVE','INACTIVE'));
            ```

            - **Usage Create 'colors' table and insert sample data**

              ```
              CREATE TABLE colors(
                id SERIAL PRIMARY KEY,
                color valid_color
              );

              INSERT INTO colors(color)VALUES('Red');
              ```

              ```
              CREATE TABLE users(
                id SERIAL PRIMARY KEY,
                status user_status
              );

              INSERT INTO users(status)VALUES('Active');
              ```

            - **Test it out**

              ```
              INSERT INTO colors(color)VALUES('Yellow');
              ERROR:  value for domain valid_color violates check constraint "valid_color_check"

              INSERT INTO users(status)VALUES('TEMP');
              ERROR:  value for domain user_status violates check constraint "user_status_check"
              ```

    - List all Domain data types

      - To get all domains in a specific schema, you use the following query

        ```
        SELECT typname
        FROM pg_catalog.pg_type
        JOIN pg_catalog.pg_namespace
        ON pg_namespace.oid = pg_type.typnamespace
        WHERE typtype = 'd' and nspname = '<schema_name>';
        ```

      - To get all domains in the public schema of the current database:

        ```
        SELECT typname
        FROM pg_catalog.pg_type
        JOIN pg_catalog.pg_namespace
        ON pg_namespace.oid = pg_type.typnamespace
        WHERE
        typtype = 'd' and nspname = 'MYDATA';
        ```

    - Drop a DOMAIN datatype

      - Be careful !! - WONT WORK!! - If there are any objects/tables that depend on the DOMAIN

      - Syntax

        ```
        DROP DOMAIN positive_numeric;
        ```

      - Add CASCADE if you want to drop dependent objects too (col using the DOMAIN will be dropped)

        ```
        DROP DOMAIN positive_numeric CASCADE;
        ```

      - **Change the col to another type first - then DROP DOMAIN (IF YOU DONT WANT THE COL TO BE DROPPED)**

      # PostgreSQL Composite data type

      - A composite data type is a user defined data type consisting of several fields with their corresponding data types

      - Characteristics of a composite Data type:

        - List of field names with corresponding data types
        - Can be used in a table as a 'column'
        - Can be used in Functions or Procedures
        - Can return multiple values, it's a composite data type

      - Syntax

        ```
        CREATE TYPE typename AS (fields column_properties);
        ```

      - Examples :

        - **Create user-defined composite datatype (address) with (city,country) fields**

          ```
          CREATE TYPE address AS(
            city VARCHAR(50),
            country VARCHAR(20)
          );
          ```

          - **Usage Create 'companies' table and insert sample data**

            ```
            CREATE TABLE companies (
              comp_id SERIAL PRIMARY KEY,
              address address
            );

            INSERT INTO companies(address)
            VALUES(ROW('LONDON','UK'));

            INSERT INTO companies(address)
            VALUES(ROW('NEW YORK','US'));
            ```

          - Querying data

            - Suppose we want to filter data based on country only from 'companies'

            - Syntax

              ```
              SELECT (composite_type).field_name FROM table_name;


              SELECT (address).country FROM companies;
              SELECT (address).city FROM companies;
              ```

            - Suppose you are joining multiple tables

            - Syntax

              ```
              SELECT (tablename.composite_column).field_name FROM table_name;
              ```

        - **Create user-defined composite datatype (inventory_item) with (product_name,supplier_id,price) fields**

          ```
          CREATE TYPE inventory_item AS (
            product_name VARCHAR(20),
            supplier_id INT,
            price NUMERIC
          );
          ```

          - **Usage Create 'inventory' table and insert sample data**

            ```
            CREATE TABLE inventory (
              inventory_id SERIAL PRIMARY KEY,
              item inventory_item
            );

            INSERT INTO inventory(item)
            VALUES(ROW('PEN',10,4.99));

            INSERT INTO inventory(item)
            VALUES(ROW('PAPER',20,10.99));
            ```

          - Querying data

            - Suppose we want to get all products whose prices are less than 5 bucks

              ```
              SELECT (item).product_name
              FROM inventory
              WHERE (item).price < 5.99;
              ```

        - **Create user-defined composite ENUM datatype (currency)**

          ```
          CREATE TYPE currency AS ENUM('USD','EURO','GBP');
          ```

          - **Access data directly from ENUM datatype**

            ```
            SELECT 'USD'::currency;
            ```

          - **Add another Value to ENUM datatype(currency)**

            ```
            ALTER TYPE currency ADD VALUE 'CHF' AFTER 'EURO';
            ```

          - **Usage Create 'stocks' table and insert sample data**

            ```
            CREATE TABLE stocks (
            stock_id SERIAL PRIMARY KEY,
            stock_currency currency
            );

            INSERT INTO stocks(stock_currency)
            VALUES('USD');

            INSERT INTO stocks(stock_currency)
            VALUES('KES'); -- ERROR:  invalid input value for enum currency: "KSH"
            ```

      - **DROP TYPE**

        - **Create user-defined composite ENUM datatype (sample_type) and Drop**

          ```
          CREATE TYPE sample_type AS ENUM ('ABC','123');
          ```

        - Drop Type 'sample_type'

          ```
          DROP TYPE sample_type;
          ```

      - **ALTER TYPE**

        - **Alter Composite Data type**

          - **Create a Sample composite type**

            ```
            CREATE TYPE myaddress AS(
              city VARCHAR(50),
              country VARCHAR(20)
            );
            ```

          - **Add a New Attribute to our composite type**

          ```
          ALTER TYPE my_address
          ADD ATTRIBUTE street VARCHAR(150);
          ```

          - **Rename a composite data type**

            ```
            ALTER TYPE myaddress RENAME TO my_address;
            ```

          - **Change the OWNERSHIP of composite types**

            ```
            ALTER TYPE my_address OWNER TO alex; --ERROR:  role "alex" does not exist
            ```

          - **Change the Schema of the current type**

            ```
            ALTER TYPE my_address SET SCHEMA test_schema; --ERROR:  schema "test_schema" does not exist
            ```

        - **Alter ENUM Data type**

          - **Create a sample ENUM datatype**

            ```
            CREATE TYPE mycolors AS ENUM('green','red','blue');
            ```

          - **Add a new value to ENUM - [BEFORE / AFTER] VALUE**

            ```
            ALTER TYPE mycolors
            ADD VALUE 'purple' BEFORE 'green'; --BEFORE green;

            ALTER TYPE mycolors
            ADD VALUE 'magenta' AFTER 'blue'; --AFTER blue;
            ```

          - **Update an existing value in ENUM**

            ```
            ALTER TYPE name RENAME VALUE old_value TO newvalue;

            ALTER TYPE mycolors RENAME VALUE 'red' TO 'orange'; --ERROR:  syntax error at or near "VALUE"
            ```

          - **List all ENUM values**

            ```
            SELECT enum_range(NULL::mycolors); --{green,red,blue}
            ```

        - **UPDATE an ENUM data in PRODUCTION server**

          - **Create a sample ENUM datatype ad jobs table**

            ```
            CREATE TYPE status_enum AS ENUM('queued','waiting','running','done');

            CREATE TABLE jobs(
              job_id SERIAL PRIMARY KEY,
              job_status status_enum
            );
            ```

          - **INSERT some data**

            ```
            INSERT INTO jobs(job_status)
            VALUES
            ('queued'),
            ('waiting'),
            ('running'),
            ('done');
            ```

          - **UPDATE 'waiting' to 'running'**

            ```
            UPDATE jobs
            SET job_status = 'running'
            WHERE job_status = 'waiting' ;
            ```

          - **RENAME status_enum TO status_enum_old**

            ```
            ALTER TYPE status_enum RENAME TO status_enum_old;
            ```

          - **CREATE a new one with the 3 values**

            ```
            CREATE TYPE status_enum AS ENUM('queued','running','done');
            ```

          - Alter job_status COLUMN to use the status_enum TYPE

            ```
            ALTER TABLE jobs
            ALTER COLUMN job_status TYPE status_enum;

            --ERROR:  column "job_status" cannot be cast automatically to type status_enum
            --HINT:  You might need to specify "USING job_status::status_enum"

            ALTER TABLE jobs
            ALTER COLUMN job_status TYPE status_enum
            USING job_status::text::status_enum; --CASTING our enum values to text
            ```

          - DROP ENUM TYPE

            - Drop 'status_enum_old'

              ```
              DROP TYPE status_enum_old;
              ```

        - **An ENUM with a DEFAULT value in a table**

          - CREATE a sample ENUM datatype 'status' and assign to a table

            ```
            CREATE TYPE status AS ENUM('pending','approved','rejected');
            ```

          - CREATE a table 'cron_jobs' with a DEFAULT value from the 'status' enum type

            ```
            CREATE TABLE cron_jobs(
             cron_job_id SERIAL PRIMARY KEY,
             status status DEFAULT 'pending'
            );
            ```

          - INSERT some sample data (Test)

            ```
            INSERT INTO cron_jobs(cron_job_id)VALUES(1);
            INSERT INTO cron_jobs(cron_job_id,status)
            VALUES
            (2,'approved'),
            (3,'rejected');
            ```

        - **CREATE a TYPE IF NOT EXISTS using PL/PGSQL**

          - Create a composite data type called 'ai'

          - Handy tool that checks if type exists else creates it

            ```
             DO
             $$
              BEGIN
                IF NOT EXISTS (
                    SELECT *
                    FROM pg_catalog.pg_type
                    JOIN pg_catalog.pg_namespace
                    ON pg_namespace.oid = pg_type.typnamespace
                    WHERE
                    typtype = 'ai' and nspname = 'MYDATA'
                  )THEN
                CREATE TYPE ai AS (a text,i integer);
                END IF;
              END;
              $$
              LANGUAGE plpgsql;
            ``
            ```

## Section 11: Explore PostgreSQL Constraints

- **Introduction to Constraints**

  - Constraints are like 'gate keepers'

  - Controls the kind of data that goes into the db

  - Constraints are the rules enforced on data COLUMNS on table

  - Used to prevent INVALID data from being entered into the db

  - Ensure ACCURACY and RELIABILITY of the data in the db

  - Constraints can be added on 2 levels :-

    - Table (constraints only applied to the whole table)

    - Column (constraints only applied to one column)

  - Types of Constraints

    - NOT NULL - Field MUST have values

    - UNIQUE - Only unique fields are allowed

    - DEFAULT - Ability to set DEFAULT values

    - PRIMARY KEY - Uniquely identifies each row/record in a table

    - FOREIGN KEY - Constraints data based on columns in other tables

    - CHECK - Checks all values meet specific criteria

- **NOT NULL Constraint**

  - NULL represents UNKNOWN or MISSING information

  - NULL is not the same as an EMPTY STRING or Zero

  - To check if a value is NULL or NOT , use [IS NULL / IS NOT NULL] boolean operator

  - You can create NOT NULL constraint on a table column as follows:

    ```
    CREATE TABLE table_name(
      column_name data_type NOT NULL
    );
    ```

  - Example

    - CREATE table a sample table 'table_nn'

      ```
      CREATE TABLE table_nn(
        id SERIAL PRIMARY KEY,
        tag TEXT NOT NULL
      );
      ```

    - INSERT some Sample Data

      ```
      INSERT INTO table_nn(tag)VALUES('John');
      ```

    - TRY TO INSERT NUll values

      ```
      INSERT INTO table_nn(tag)
      VALUES(Null); --ERROR:  null value in column "tag" violates not-null constraint

      INSERT INTO table_nn(tag)
      VALUES(''); --Empty string is treated as data

      INSERT INTO table_nn(tag)
      VALUES(0); --0 is treated as data
      ```

    - Adding NOT NULL to an Existing table (let's say you forgot)

      - Create a table without NOT NULL constraint

        ```
        CREATE TABLE table_nn2(
          id SERIAL PRIMARY KEY,
          tag TEXT
        );
        ```

      - Set to NOT NULL

        ```
        ALTER TABLE table_nn2
        ALTER COLUMN tag SET NOT NULL;
        ```

- **UNIQUE Constraint**

  - Ensures that the values stored in a column or a group of columns are unique

  - INSERT - Checks if the value already exists before INSERTING new value

  - UPDATE - Checks if the value already exists before UPDATING EXISTING value

  - Syntax

    ```
    CREATE TABLE table_name(
      column_name data_type UNIQUE
    );
    ```

  - Example

    ```
    CREATE TABLE table_emails(
      id SERIAL PRIMARY KEY,
      email text UNIQUE
    );

    INSERT INTO table_emails(email)
    VALUES('mwangialex26@gmail.com'); - WORKS!!

    INSERT INTO table_emails(email)
    VALUES('mwangialex26@gmail.com'); --ERROR:  duplicate key value violates unique constraint "table_emails_email_key"
    ```

  - Create UNIQUE key on Multiple Columns

    ```
    CREATE TABLE table_products(
      id SERIAL PRIMARY KEY,
      product_code VARCHAR(10),
      product_name text
      --UNIQUE(product_code,product_name) -- on definition
    );
    ```

  - Now add UNIQUE constraint on table_products

    ```
    ALTER TABLE table_products
    ADD CONSTRAINT constraint_name UNIQUE(col1,col2,...);

    ALTER TABLE table_products
    ADD CONSTRAINT unique_product_code UNIQUE(product_code,product_name);
    ```

  - Insert some sample data

    ```
    INSERT INTO table_products(product_code,product_name)
    VALUES('A','apple');

    INSERT INTO table_products(product_code,product_name)
    VALUES('A','apple'); --ERROR:  duplicate key value violates unique constraint "unique_product_code"
    ```

  - Order of columns matters !!

- **DEFAULT Constraint**

  - Sets a DEFAULT value of a column in a table - IF default value is NOT set, then the default value is NULL

    ```
      <column> DEFAULT <value>
    ```

    ```
    CREATE TABLE employees (
      employee_id SERIAL PRIMARY KEY,
      first_name VARCHAR(50),
      last_name VARCHAR(50),
      is_active VARCHAR(2) DEFAULT 'Y'
    );
    ```

  - INSERT sample data

    ```
    INSERT INTO employees(first_name,last_name)
    VALUES('john','doe');
    ```

  - Set a default value to an existing table

    ```
    ALTER TABLE employees
    ALTER COLUMN is_active SET DEFAULT 'N';

    INSERT INTO employees(first_name,last_name)
    VALUES('jane','doe');
    ```

  - Drop a default value

    ```
    ALTER TABLE employees
    ALTER COLUMN is_active DROP DEFAULT;

    INSERT INTO employees(first_name,last_name)
    VALUES('susan','doe');
    ```

- **PRIMARY KEY Constraint**

  - A primary key is a field in a table which uniquely identifies each row/record in a database table

  - Primary keys MUST contain UNIQUE values

  - A table can have only one primary key, which may consist of single or multiple fields

  - When multiple fields are used as a primary key, they are called a composite key

  - They are same as UNIQUE NOT NULL

  - Syntax

    ```
    CREATE TABLE table_name(
      column_name data_type PRIMARY KEY,
      ...
    );
    ```

  - Example

    ```
    CREATE TABLE table_items(
      item_id INT PRIMARY KEY,
      item_name VARCHAR(50) NOT NULL
    );

    INSERT INTO table_items(item_id,item_name)
    VALUES(1,'Orange');
    INSERT INTO table_items(item_id,item_name)
    VALUES(1,'Orange');--ERROR:  duplicate key value violates unique constraint "table_items_pkey"
    ```

  - Viewing constraint naming convention

    ```
    tablename_pkey
    ```

  - ADD PRIMARY KEY to an Existing table

    - Drop Existing first

      ```
      ALTER TABLE table_name
      DROP CONSTRAINT constraint_name;

      ALTER TABLE table_items
      DROP CONSTRAINT table_items_pkey;
      ```

    - Add Primary Key

      ```
      ALTER TABLE table_name
      ADD PRIMARY KEY(col1,col2,..);

      ALTER TABLE table_items
      ADD PRIMARY KEY(item_id);
      ```

- **PRIMARY KEY Constraint on Multiple Columns**

  - Also known as Composite PRIMARY KEY

  - Create sample table 'table_grades' for student grades

    ```
    CREATE TABLE table_grades(
      course_id VARCHAR(100) NOT NULL,
      student_id VARCHAR(100) NOT NULL,
      grade INT NOT NULL
    );

    INSERT INTO table_grades(course_id,student_id,grade)
    VALUES
    ('Math','S1',50),
    ('CHEMISTRY','S1',70),
    ('ENGLISH','S2',80),
    ('PHYSICS','S1',80);

    course_id + student_id = composite key
    ```

  - DROP table 'table_grades' and re-create again with composite key

    ```
    DROP TABLE table_grades;
    ```

  - Re-create again with composite key

    ```
     CREATE TABLE table_grades(
      course_id VARCHAR(100) NOT NULL,
      student_id VARCHAR(100) NOT NULL,
      grade INT NOT NULL,
      PRIMARY KEY (course_id,student_id)
    );

    INSERT INTO table_grades(course_id,student_id,grade)
    VALUES
    ('Math','S1',50),
    ('CHEMISTRY','S1',70),
    ('ENGLISH','S2',80),
    ('PHYSICS','S1',80);

    INSERT INTO table_grades(course_id,student_id,grade)
    VALUES
    ('Math','S1',50); --ERROR:  duplicate key value violates unique constraint "table_grades_pkey"

    INSERT INTO table_grades(course_id,student_id,grade)
    VALUES
    ('Math','S3',50); --Works

    ```

  - Order of columns above matters --PRIMARY KEY (course_id,student_id)

  - Drop PRIMARY KEY

    ```
    ALTER TABLE table_name
    DROP CONSTRAINT constraint_name;

    ALTER TABLE table_grades
    DROP CONSTRAINT table_grades_pkey;
    ```

  - Add PRIMARY KEY TO an existing table

    ```
    ALTER TABLE table_name
    ADD CONSTRAINT constraint_name
    PRIMARY KEY (col1,col2,...);
    ```

- **FOREIGN KEY Constraint**

  - **Introduction to Foreign Keys**

    - A foreign key is a column or a group of columns in a table that reference the primary key of another table.

    - The table that contains the foreign key is called the referencing table or child table.

    - And the table referenced by the foreign key is called the referenced table or parent table.

    - Syntax

      ```
      [CONSTRAINT fk_name]
      FOREIGN KEY(fk_columns)
      REFERENCES parent_table(parent_key_columns)
      [ON DELETE delete_action]
      [ON UPDATE update_action]
      ```

      - _WHERE_

        - _Optional_ Specify the name for the foreign key constraint after the CONSTRAINT keyword which is optional. PostgreSQL will assign an auto-generated name if ommitted.

        - Specify one or more foreign key columns in parentheses after the FOREIGN KEY keywords.

        - Specify the parent table and parent key columns referenced by the foreign key columns in the REFERENCES clause.

        - Specify the delete and update actions in the ON DELETE and ON UPDATE clauses.

      - The delete and update actions determine the behaviors when the primary key in the parent table is deleted and updated.

      - Since the primary key is rarely updated, the ON UPDATE action is not often used in practice.

    - Example

      ```
      CREATE TABLE table_name(
      column_name datatype PRIMARY KEY,
      ....
      ....
      FOREIGN KEY(columnname) REFERENCES child_table_name(columnname)
      );
      ```

  - **Tables Without Foreign key constraints**

    - Create 2 tables independent of each other

      ```
      CREATE TABLE table_product(
        product_id INT PRIMARY KEY,
        product_name VARCHAR(100) NOT NULL,
        supplier_id INT NOT NULL
      );

      CREATE TABLE table_supplier(
        supplier_id INT PRIMARY KEY,
        supplier_name VARCHAR(100) NOT NULL
      );
      ```

    - Insert sample data

      ```
      INSERT INTO table_supplier(supplier_id,supplier_name)
      VALUES
      (1,'SUPPLIER 1'),
      (2,'SUPPLIER 2');

      INSERT INTO table_product(product_id,product_name,supplier_id)
      VALUES
      (1,'PEN',1),
      (2,'PAPER',2);

      INSERT INTO table_product(product_id,product_name,supplier_id)
      VALUES
      (3,'COMPUTER',10); --INSERTS supplier_id-10 even when the id does not exist

      ```

  - **RE_CREATE Tables With Foreign key constraints**

    - Drop and re-create 'table_supplier' and 'table_product'

      ```
      DROP TABLE table_product;
      DROP TABLE table_supplier;
      ```

    - Run the 'CREATE TABLE table_supplier' above ..ORDER Matters !!!

    - Create 'table_product' and define 'supplier_id' as foreign key

      ```
      CREATE TABLE table_product(
        product_id INT PRIMARY KEY,
        product_name VARCHAR(100) NOT NULL,
        supplier_id INT NOT NULL,
        FOREIGN KEY(supplier_id) REFERENCES table_supplier(supplier_id)
      );
      ```

  - **Foreign key maintains Referential data integrity**

    - Foreign key states that values in the column MUST match with values with some other row from another table

      ```
      INSERT INTO table_supplier(supplier_id,supplier_name)
      VALUES
      (1,'BIC'),
      (2,'COCACOLA');

      INSERT INTO table_product(product_id,product_name,supplier_id)
      VALUES
      (1,'PEN',1),
      (2,'FANTA ORANGE',2);

      INSERT INTO table_product(product_id,product_name,supplier_id)
      VALUES
      (3,'COMPUTER',3); ERROR:  insert or update on table "table_product" violates foreign key constraint "table_product_supplier_id_fkey"
      ```

    - Let's try to delete 'BIC' from the parent table (supplier table) - ERROR!!

      ```
      DELETE FROM table_supplier WHERE supplier_id = 1;

      --ERROR:  update or delete on table "table_supplier" violates foreign key constraint "table_product_supplier_id_fkey" on table "table_product"
      ```

    - Let's try to delete product with the id 1 from the child table (product table) - WORKS!!

    - Let's try again to delete 'BIC' from the parent table (supplier table) - WORKS - no child records!!

    - Same CONCEPT applies to both Update/Delete actions

  - **Drop a constraint**

    - Naming convention of a foreign key constraint ;--tablename_columnname_fkey

    - Syntax

      ```
      ALTER TABLE table_name
      DROP CONSTRAINT constraint_name;

      ALTER TABLE table_product
      DROP CONSTRAINT table_product_supplier_id_fkey;
      ```

  - **Add OR Update Foreign Key Constraint on existing table**

    - Syntax

      ```
      ALTER TABLE table_product
      ADD CONSTRAINT constraint_name FOREIGN KEY(columnname) REFERENCES tablename(columnname);

      ALTER TABLE table_product
      ADD CONSTRAINT table_product_supplier_id_fkey
      FOREIGN KEY(supplier_id) REFERENCES table_supplier(supplier_id);
      ```

- **CHECK Constraint**

  - Naming convention {tablename}\_{column}\_check

  - **Introduction To CHECK Constraint**

    - Allows you to specify if values in a column MUST meet a specific requirement

      e.g. Salary > 0

    - Uses a Boolean expression to evaluate the values before they are are inserted/updated to the column

    - If the values pass the check, PostgreSQL will insert/update these values to the column, Otherwise, PostgreSQL will reject the changes and issue a constraint violation error

  - **Add CHECK Constraint to New Table**

    - Create sample 'staff' table

      ```
      CREATE TABLE staff(
        staff_id SERIAL PRIMARY KEY,
        first_name VARCHAR(50) NOT NULL,
        last_name VARCHAR(50) NOT NULL,
        date_of_birth DATE CHECK(date_of_birth > '1900-01-01'),
        join_date DATE CHECK(join_date > date_of_birth),
        salary NUMERIC CHECK(salary > 0)
      );
      ```

    - Add some sample test-data

      ```
      INSERT INTO staff(first_name,last_name,date_of_birth,join_date,salary)
      VALUES
      ('Jane','Doe','1999-01-01','2023-01-01',100);

      INSERT INTO staff(first_name,last_name,date_of_birth,join_date,salary)
      VALUES
      ('James','Doe','2020-01-01','2020-01-01',100); --ERROR: Violates "staff_check" constraint

      INSERT INTO staff(first_name,last_name,date_of_birth,join_date,salary)
      VALUES
      ('James','Doe','2010-01-01','2020-01-01',100); --WORKS

      INSERT INTO staff(first_name,last_name,date_of_birth,join_date,salary)
      VALUES
      ('James','Doe','2010-01-01','2020-01-01',-100); --ERROR : Violates "staff_salary_check" constraint
      ```

    - Update existing with wrong values

      ```
      UPDATE staff
      SET salary = 0
      WHERE staff_id = 1; --ERROR : Violates check constraint "staff_salary_check"
      ```

  - **ADD, RENAME , DROP on EXISTING table**

    - Create price table without CHECK constraint

      ```
      CREATE TABLE price(
        price_id SERIAL PRIMARY KEY,
        product_id INT NOT NULL,
        price NUMERIC NOT NULL,
        discount NUMERIC NOT NULL,
        valid_from DATE NOT NULL
      );
      ```

    - Add the following Check Constraint: price > 0 , discount > 0% and price > discount

      ```
      ALTER TABLE price
      ADD CONSTRAINT price_check
        CHECK(
          price > 0
            AND
          discount >= 0
            AND
          price > discount
          );
      ```

    - Test by Inserting some sample data

      ```
      INSERT INTO price(product_id,price,discount,valid_from)
      VALUES(1,100,20,'2020-01-01');

      INSERT INTO price(product_id,price,discount,valid_from)
      VALUES(2,100,120,'2020-01-01'); --ERROR: Violates check constraint "price_check"
      ```

    - Rename CHECK constraint

      ```
      ALTER TABLE price
      RENAME CONSTRAINT price_check TO price_discount_check;
      ```

    - Drop CHECK a constraint

      ```
      ALTER TABLE price
      DROP CONSTRAINT price_discount_check;
      ```
