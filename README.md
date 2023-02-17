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
