---
id: "megawise_basic_operation"
lang: "en"
title: "Basic Data Operations"
---

# Basic Data Operations

<!-- TOC -->

- [Conventions](#Conventions)
- [Basic Operations](#Basic-operations)
    - [Connect the psql client to MegaWise](#Connect-the-psql-client-to-MegaWise)
    - [Creating a database](#Creating-a-database)
    - [Switching to another database](#Switching-to-another-database)
    - [Removing a database](#Removing-a-database)
    - [Accessing a database](#Accessing-a-database)
    - [Creating a new table](#Creating-a-new-table)
    - [Removing a table](#Removing-a-table)
    - [Adding Rows to a table](#Adding-rows-to-a-table)
    - [Querying a table](#Querying-a-table)
    - [Joins between tables](#Joins-between-tables)
    - [Aggregate functions](#Aggregate-functions)

<!-- /TOC -->

## Conventions

The following conventions are used in the synopsis of a command: brackets ([ and ]) indicate optional parts. (In the synopsis of a Tcl command, question marks (?) are used instead, as is usual in Tcl.) Braces ({ and }) and vertical lines (|) indicate that you must choose one alternative. Dots (...) mean
that the preceding element can be repeated. Where it enhances the clarity, SQL commands are preceded by the prompt =>, and shell commands are preceded by the prompt $. Normally, prompts are not shown, though.


## Basic Operations


### Connect the psql client to MegaWise

Use the following command to connect to MegaWise:

```bash
$ psql -U zilliz -p 5433 -h $IP_ADDR -d postgres 
``` 

> `-U` specifies the username to connect to MegaWise.

> `-p` specifies the server port of the MegaWise.

> `-h` specifies the IP address of the MegaWise server.

> `-d` specifies the name of the database to connect to.

MegaWise Docker creates a built-in database `postgres` after launch. A default user `zilliz` is created in the database. You will then be prompted to enter the password. The default password is `zilliz` .

    If the terminal displays the following information, you can assume that the connection to MegaWise is successful.

    ```bash
    psql (11.1)
    Type "help" for help.

    postgres=>
    ```

    >Note：If the connection timeouts, check whether the firewall settings are correct.



### Creating a database

To create a new database, in this example named mydb, you use the following command:

```bash
$ create database mydb
```

If this produces no response then this step was successful and you can skip over the remainder of this section.

If you got the following information when creating a database, please log in as an administrator and grant the current user permission to create databases.

```bash
ERROR:  permission denied to create database
```


### Switching to another database

To switch to another created database, in this example named mydb, you use the following command:

```bash
\c mydb
```


### Removing a database

To remove a database, in this example named mydb, you use the following command:

```bash
drop database mydb
```

You must declare the database name if it is not a default name. This command physically removes all files related to the database and cannot be undone. Think twice before running this command.



### Accessing a database

In psql, you will be greeted with the following message after connecting:

```bash
psql (11.1)
Type "help" for help.

mydb=>
```

The last line could also be:

```bash
mydb=#
```

That would mean you are a database superuser, which is most likely the case if you installed the MegaWise instance yourself. Being a superuser means that you are not subject to access controls.

The last line printed out by psql is the prompt, and it indicates that psql is listening to you and that you can type SQL queries into a work space maintained by psql. Try out these commands:

```bash
mydb=> SELECT version();
                                         version
------------------------------------------------------------------------------------------
 MegaWise 11.1 on x86_64-pc-linux-gnu, compiled by gcc （Ubuntu 5.4.0-6ubuntu1~16.04.10）5.4.0 20160609 4.9.2, 64-bit
(1 row)

mydb=> SELECT current_date;
    date
------------
 2019-09-15
(1 row)

mydb=> SELECT 2 + 2;
 ?column?
----------
        4
(1 row)
```
The psql program has a number of internal commands that are not SQL commands. They begin with the backslash character, “\”. For example, you can get help on the syntax of various MegaWise SQL commands by typing:

```bash
mydb=> \h
```

To get out of psql, type:

```bash
mydb=> \q
```


### Creating a new table

You can create a new table by specifying the table name, along with all column names and their types:

```sql
CREATE TABLE weather (
    city            varchar(80),
    temp_lo         int,           -- low temperature
    temp_hi         int,           -- high temperature
    prcp            real,          -- precipitation
    date            date
);
```

Use two dashes (“--”) to introduce comments. Whatever follows them is ignored up to the end of the line. SQL is case insensitive about key words and identifiers, except when identifiers are double-quoted to preserve the case.

varchar(80) specifies a data type that can store arbitrary character strings up to 80 characters in length. int is the normal integer type. real is a type for storing single precision floating-point numbers. date specifies a data type about dates.

MegaWise supports standard SQL data types, including bool, int, smallint, bigint, float, real, double precision, decimal, numericchar(N)、varchar(N)、text、name、date、time、timestamp、interval, among other general data types.

The seond example stores cities and their associated geographical location:

```sql
CREATE TABLE cities (
   name            varchar(80),
   area            real
);
```



### Removing a table

Use the following command to remove a table:

```sql
DROP TABLE tablename;
```


### Adding Rows to a table

The INSERT statement is used to add rows to a table:

```sql
INSERT INTO weather VALUES ('San Francisco', 46, 50, 0.25, '1994-11-27');　　　　　　　　　　　　　
```

```sql
INSERT INTO cities VALUES ('San Francisco', 88.65);　　　　　　
```

Note that the data sequence must be consistent with the column sequence in the `CREATE TABLE` command. Constants that are not simple numeric values usually must be surrounded by single quotes ('), as in the previous example.

Alternatively, you can list the columns explicitly. The data must be consistent with the column names. The data sequence does not necessarily match the column sequence in the `CREATE TABLE` command.

```sql
INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');
```

You can also use `COPY` to load large amounts of data from text files.

```sql
COPY weather FROM '/home/user/weather.txt';
```

>Note: This command works only when the data file is in the server that runs MegaWise instead of the client.


### Querying a table

Use `SELECT` to query data in each table. The statement is divided into a select list (the part that lists the columns to be returned), a table list (the part that lists the tables from which to retrieve the data), and an optional qualification (the part that specifies any restrictions). For example, to retrieve all the rows of table weather, use the following command:

```sql
SELECT * FROM weather;
```

Here * is a shorthand for “all columns”. So the same result would be had with:
```sql
SELECT city, temp_lo, temp_hi, prcp, date FROM weather;
```
The output should be:

```sql
     city      | temp_lo | temp_hi | prcp |    date    
---------------+---------+---------+------+------------
 San Francisco |      46 |      50 | 0.25 | 11-27-1994
 San Francisco |      43 |      57 |    0 | 11-29-1994
 Hayward       |      37 |      54 |    0 | 11-29-1994
(3 rows)

```

You can write expressions, not just simple column references, in the select list. For example, if you run the following command:

```sql
SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
```

The output should be:

```sql
     city      | temp_avg |    date    
---------------+----------+------------
 San Francisco |       48 | 11-27-1994
 San Francisco |       50 | 11-29-1994
 Hayward       |       45 | 11-29-1994
(3 rows)

```

The `AS` clause is used to relabel the output column.

A query can be “qualified” by adding a `WHERE` clause that specifies which rows are wanted. The WHERE clause contains a Boolean (truth value) expression, and only rows for which the Boolean expression is true are returned. The usual Boolean operators (AND, OR, and NOT) are allowed in the qualification. For example, run the following command:

```sql
SELECT * FROM weather
    WHERE city = 'San Francisco' AND prcp > 0.0;
```

Result:

```sql
     city      | temp_lo | temp_hi | prcp |    date
---------------+---------+---------+------+------------
 San Francisco |      46 |      50 | 0.25 | 1994-11-27
(1 row)
```

You can request that the results of a query be returned in sorted order:

```sql
SELECT * FROM weather
    ORDER BY city;
```

```sql

     city      | temp_lo | temp_hi | prcp |    date    
---------------+---------+---------+------+------------
 Hayward       |      37 |      54 |    0 | 11-29-1994
 San Francisco |      46 |      50 | 0.25 | 11-27-1994
 San Francisco |      43 |      57 |    0 | 11-29-1994
(3 rows)

```

The previous example sorts data by city name. You can also sort the result per values in multiple columns.

```sql
SELECT * FROM weather
    ORDER BY city, temp_lo;
```

You can remove duplicate rows from the result of a query:

```sql
SELECT DISTINCT city
    FROM weather;
```

```sql
     city
---------------
 Hayward
 San Francisco
(2 rows)
```

You can ensure consistent results by using `DISTINCT` and `ORDER BY` together:

```sql
SELECT DISTINCT city
    FROM weather
    ORDER BY city;
```



### Joins between tables

Queries can access multiple tables at once, or access the same table in such a way that multiple rows of the table are being processed at the same time. A query that accesses multiple rows of the same or different tables at one time is called a join query. As an example, if you wish to list all the weather records together with the location of the associated city. To do that, we need to compare the city column of each row of the weather table with the name column of all rows in the cities table, and select the pairs of rows where these values match. The following command performs a query across tables:

```sql
SELECT *
    FROM weather, cities
    WHERE city = name;
```

```sql
     city      | temp_lo | temp_hi | prcp |    date    |     name      | area  
---------------+---------+---------+------+------------+---------------+-------
 San Francisco |      46 |      50 | 0.25 | 11-27-1994 | San Francisco | 88.65
 San Francisco |      43 |      57 |    0 | 11-29-1994 | San Francisco | 88.65
(2 rows)

```

There are two columns containing the city name. This is correct because the lists of columns from the weather and cities tables are concatenated. You can list the output columns explicitly rather than using *:

```sql
SELECT city, temp_lo, temp_hi, prcp, date, area
    FROM weather, cities
    WHERE city = name;
```

If there were duplicate column names in the two tables you'd need to qualify the column names to show which one you meant, as in:

```sql
SELECT weather.city, weather.temp_lo, weather.temp_hi,
       weather.prcp, weather.date, cities.area
    FROM weather, cities
    WHERE cities.name = weather.city;
```

Join queries can also be written in the following form:

```sql
SELECT *
    FROM weather INNER JOIN cities ON (weather.city = cities.name);
```

Use the following comman to perform `OUTER` JOIN to scan the weather table and for each row to find the matching cities row(s). If no matching row is fouund, empty values are substituted for the cities table's columns.


```sql
SELECT *
    FROM weather LEFT OUTER JOIN cities ON (weather.city = cities.name);
```

```sql
     city      | temp_lo | temp_hi | prcp |    date    |     name      |    area     
---------------+---------+---------+------+------------+---------------+-------------
 San Francisco |      46 |      50 | 0.25 | 11-27-1994 | San Francisco |       88.65
 San Francisco |      43 |      57 |    0 | 11-29-1994 | San Francisco |       88.65
 Hayward       |      37 |      54 |    0 | 11-29-1994 |               | 
(3 rows)
```

This query is called a left outer join because the table mentioned on the left of the join operator will have each of its rows in the output at least once, whereas the table on the right will only have those rows output that match some row of the left table. When outputting a left-table row for which there is no right-table match, empty (null) values are substituted for the right-table columns.


A `SELF JOIN` joins a table against itself. For example:

```sql
SELECT W1.city, W1.temp_lo AS low, W1.temp_hi AS high,
    W2.city, W2.temp_lo AS low, W2.temp_hi AS high
    FROM weather W1, weather W2
    WHERE W1.temp_lo < W2.temp_lo and w1.temp_hi < w2.temp_hi and w1.prcp = w2.prcp;
```

```sql
  city   | low | high |     city      | low | high 
---------+-----+------+---------------+-----+------
 Hayward |  37 |   54 | San Francisco |  43 |   57
(1 row)
```

The previous command relabeled the weather table as W1 and W2 to be able to distinguish the left and right side of the join.

You can also use aliases in queries to represent table names:

```sql
SELECT *
    FROM weather w, cities c
    WHERE w.city = c.name;
```


*******

**Note**

In all table join operations, the join conditions in the `WHERE` clause must include `=`.

****




### Aggregate functions

An aggregate function computes a single result from multiple input rows. For example, there are aggregates to compute the count, sum, avg (average), max (maximum) and min (minimum) over a set of rows.

As an example, we can find the highest low-temperature reading anywhere with:

```sql
SELECT max(temp_lo) FROM weather;
```

```sql
 max
-----
  46
(1 row)
```

The aggregate max cannot be used in the `WHERE` clause. For example, the following command does not match SQL grammar.

```sql
SELECT city FROM weather WHERE temp_lo = max(temp_lo);  
```

You can use a subquery instead:

```sql
SELECT city FROM weather
    WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
```

```sql
     city
---------------
 San Francisco
(1 row)
```

A subquery is an independent computation that computes its own aggregate separately.

Aggregates are also very useful in combination with `GROUP BY` clauses. For example, we can get the maximum low temperature observed in each city with the following command:

```sql
SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city;
```

```sql
     city      | max
---------------+-----
 Hayward       |  37
 San Francisco |  46
(2 rows)
```

The result contains one output row per city. Each aggregate result is computed over the table rows matching that city. We can filter these grouped rows using HAVING:

```sql
SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city
    HAVING max(temp_lo) < 40;
```

```sql
  city   | max
---------+-----
 Hayward |  37
(1 row)
```

The result contains the same results for only the cities that have all temp_lo values below 40.

It is important to understand the interaction between aggregates and SQL's `WHERE` and `HAVING` clauses. The fundamental difference between `WHERE` and `HAVING` is this: `WHERE` selects input rows before groups and aggregates are computed, whereas `HAVING` selects group rows after groups and aggregates are computed. Thus, the `WHERE` clause must not contain aggregate functions. On the other hand, the `HAVING` clause always contains aggregate functions. (Strictly speaking, you are allowed to write a `HAVING` clause that doesn't use aggregates. The same condition could be used more efficiently at the `WHERE` stage.)

In the previous example, `WHERE` is more efficient in filtering city names because unmatched rows cannot be involved in grouping and aggregate calculations.
