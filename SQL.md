Relational databases are perfectly equipped to work with relationships between multiple models. \
Using **S**tructured **Q**uery **L**anguage (SQL), we can craft complex queries across our strictly formatted relational databases.

*"Each table is a named collection of rows. Each row of a given table has the same set of named columns, and each column is of a specific data type. Whereas columns have a fixed order in each row, it is important to remember that SQL does not guarantee the order of the rows within the table in any way (although they can be explicitly sorted for display)."*  - [SQL Concepts](https://www.postgresql.org/docs/12/tutorial-concepts.html)

### Relational Database Options
There are various options for relational database systems including Oracle, PostgreSQL and SQLite. Each have their own slightly different flavour of SQL but the syntax differences are often small, which can cause confusion! Check the documentation of your chosen relational database for more details.

## PostgreSQL
*"PostgreSQL is a powerful, open source object-relational database system that uses and extends the SQL language combined with many features that safely store and scale the most complicated data workloads. The origins of PostgreSQL date back to 1986 as part of the POSTGRES project at the University of California at Berkeley and has more than 30 years of active development on the core platform."* - [About PostgreSQL](https://www.postgresql.org/about/)

## Local Usage
### Install
- On [MacOS](https://www.postgresql.org/download/macosx/) you can use the [installer](https://www.postgresql.org/download/macosx/) or the recommended [homebrew](https://formulae.brew.sh/formula/postgresql) installation
    - `brew update && brew install postgresql`
- On [Windows](https://www.postgresql.org/download/windows/), use the [installer](https://www.postgresql.org/download/windows/). For more install assistance, check [this tutorial](https://www.postgresqltutorial.com/install-postgresql/)
- On [Linux](https://www.postgresql.org/download/), find the instructions for your distro [here](https://www.postgresql.org/download/)

### Start and Stop the service
To run PostgreSQL as a background service:
- On MacOS
    - start: `brew services start postgresql`
    - stop: `brew services stop postgresql`
- On Windows use the Services console

**Optional** Install and connect a GUI editor
[PopSQL](https://popsql.com/) is one of [many options](https://alternativeto.net/software/popsql/) for GUI interaction with your relational database server. It is not required but can be a nice way to store and share your queries.

## Docker Usage
### Create Container
- `docker run --name <new-container-name> -e POSTGRES_PASSWORD=<a-password> -d postgres`
### Create container with volume (useful if you want to [run files from within psql shell](https://github.com/getfutureproof/fp_guides_wiki/wiki/SQL#run-a-sql-file))
- `docker run --name <new-container-name> --mount type=bind,source="$(pwd)",dst=<destination> -e POSTGRES_PASSWORD=<a-password> -d postgres`
- eg. `docker run --name vol-db --mount type=bind,source="$(pwd)",dst="/code" -e POSTGRES_PASSWORD=password -d postgres`
### Attach to container in bash shell (from where you can run the first set of commands below)
- `docker exec -it <container-name> /bin/bash`
- eg. `docker exec -it vol-db /bin/bash`
### Or attach to container in psql shell (from where you can run the second set of commands below)
- `docker exec -it vol-db psql -U postgres`

***

## Interact with the Postgres service via the terminal
To interact with your Postgres service you can access via the command line. Alternatively you can use a GUI such as PopSQL (see above).

### See all databases
- `psql -U postgres -l`

### Create a new database
- `createdb <db-name> -U <pg-username>` ( eg. `createdb shelter -U postgres`)

### Delete a database
- `dropdb <db-name> -U <pg-username>` ( eg. `dropdb shelter -U postgres`)

### Access database shell
- enter: `psql <db-name> -U <pg-username>` ( eg. `psql shelter -U postgres`)
- exit: `\q`

***

## SQL
The following commands can be run from within your database shell (see above). The ending semicolons are very important, don't forget them! [This cheatsheet](https://sp.postgresqltutorial.com/wp-content/uploads/2018/03/PostgreSQL-Cheat-Sheet.pdf) is a great resource for the commands below and much more!

### Help!
- `\h` to see options
- `\h <option>` to get help

### Create a new table
```sql
CREATE TABLE cats (
	id serial PRIMARY KEY,
	name VARCHAR ( 20 ) NOT NULL,
	age INT
);
```
This creates a table called 'cats' with 3 columns: a primary key which auto-increments, a compulsory name string with a maximum of 20 characters and an age which is an integer.

### Delete a table
- `DROP TABLE <table-name>;`
- eg. `DROP TABLE cats;`

### See all tables in a database
- `\dt`

### Run a .sql file
- `\i <file-path>`
***

## Table CRUD operations

### [**C**reate](https://www.postgresql.org/docs/12/tutorial-populate.html)
- Just one:
    -  `INSERT INTO cats (name, age) VALUES ('Zelda', 3);`
- Multiple:
```sql
INSERT INTO cats (name, age) 
VALUES ('Tigerlily', 9), ('Sam', 10), (null, 1);
```

### **R**ead
SQL has many options to put together queries. Check out the [documentation](https://www.tutorialspoint.com/postgresql/index.htm) for a rundown of all the possibilities! `SELECT` is the starting point, make sure to get familiar with `GROUP_BY` and `HAVING` as well as the others below.

Here are a few to get started:
- all rows in cats table
    - `SELECT * FROM cats;`
- all names in cats table
    - `SELECT name FROM cats;`
- all names of cats older than 7
    - `SELECT name FROM cats WHERE age > 7;`
- cats called Zelda
    - `SELECT * FROM cats WHERE name = 'Zelda';`
- cats with a name
    - `SELECT * FROM cats WHERE name IS NOT NULL;`
- unique ages in cats table in descending order
    - `SELECT DISTINCT age FROM cats ORDER BY age DESC;`
- highest age value in cats table
    - `SELECT max(age) FROM cats;`
- name of oldest cat in table
    - `SELECT name FROM cats WHERE age = (SELECT max(age) FROM cats);`
- cats older than 5 whose name starts with 'T'  
    - `SELECT * FROM cats WHERE (age > 5) AND (name LIKE 'T%');`


### **U**pdate
- Update the age of cats called 'Zelda' to be 4
    - `UPDATE cats SET age = 4 WHERE name = 'Zelda';`


### **D**elete
- Delete all rows in table
    - `DELETE FROM cats;`
- Delete rows in table matching query
    - `DELETE FROM cats WHERE name = 'Zelda';`


***

## Aggregation Functions
- `COUNT`
- `COUNT DISTINCT`
- `SUM`
- `AVG`
- `MAX`
- `MIN`

***

## Grouping
We can group results together which means that our results are ordered by a specific column.
```sql
CREATE TABLE owners (
	id serial PRIMARY KEY,
	name VARCHAR ( 50 ) NOT NULL,
	location VARCHAR (20)
);

INSERT INTO owners (name, location) VALUES ('Beth', 'London'), ('Christine', 'Cornwall'), ('Naz', 'London');


SELECT COUNT(id), location 
FROM owners
GROUP BY location;
```

***

## Joins
One of the great features of SQL is its power to craft complex queries across multiple tables. When planning your database, consider what relationships will be in place.

There are 4 different types of join:
- (INNER) JOIN: Returns records that have matching values in both tables
- LEFT (OUTER) JOIN: Returns all records from the left table, and the matched records from the right table
- RIGHT (OUTER) JOIN: Returns all records from the right table, and the matched records from the left table
- FULL (OUTER) JOIN: Returns all records when there is a match in either left or right table \
For more details, see the [W3 Schools Joins guide](https://www.w3schools.com/sql/sql_join.asp)

```sql
CREATE TABLE cats (
	id serial PRIMARY KEY,
	name VARCHAR ( 20 ),
	age INT
);

CREATE TABLE owners (
	id serial PRIMARY KEY,
	name VARCHAR ( 50 ) NOT NULL,
	location VARCHAR (20)
);

CREATE TABLE adoptions (
	id serial PRIMARY KEY,
    cat_id INT REFERENCES cats (id) NOT NULL,
    owner_id INT REFERENCES owners (id) NOT NULL,
    adoption_date TIMESTAMP DEFAULT NOW()
);

INSERT INTO cats (name, age) VALUES ('Zelda', 3), (null, 1), ('Tigerlily', 9);

INSERT INTO owners (name, location) VALUES ('Beth', 'London'), ('Christine', 'Cornwall'), ('Naz', 'London');

INSERT INTO adoptions (cat_id, owner_id) VALUES (1, 1), (3, 2);
```

Let's query for the names of cat and owners that have been joined together via our adoptions table!
```sql
SELECT owners.name AS owner, cats.name AS cat
FROM ((adoptions
INNER JOIN owners ON adoptions.owner_id = owners.id)
INNER JOIN cats ON adoptions.cat_id = cats.id);
```



## Resources
[PostgreSQL Documentation](https://www.postgresql.org/docs/12/index.html)
