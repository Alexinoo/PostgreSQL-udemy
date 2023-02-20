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
