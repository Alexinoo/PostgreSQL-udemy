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

- Create Table

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
