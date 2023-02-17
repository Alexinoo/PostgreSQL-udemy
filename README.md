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
