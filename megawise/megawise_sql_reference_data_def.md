---
id: "megawise_sql_datadef"
lang: "en"
title: "Data Definition"
---
# Data Definition


## Table basics

A table in a relational database consists of rows and columns. The number and order of the columns is fixed, and each column has a name. The number of rows is variable and reflects how much data is stored at a given moment. SQL does not make any guarantees about the order of the rows in a table. When a table is read, the rows will appear in an unspecified order, unless sorting is explicitly requested. Furthermore, SQL does not assign unique identifiers to rows, so it is possible to have several completely identical rows in a table.

MegaWise includes a sizable set of built-in data types that fit many applications. Users can also define their own data types. Some of the frequently used data types are integer for whole numbers, numeric for possibly fractional numbers, text for character strings, date for dates, time for time-of-day values, and timestamp for values containing both date and time.

To create a table, you use the aptly named `CREATE TABLE` command. In this command you specify at least a name for the new table, the names of the columns and the data type of each column. For example:

```sql
CREATE TABLE my_first_table (
    first_column text,
    second_column integer
);
```

This creates a table named my\_first\_table with two columns. The first column is named first\_column and has a data type of text; the second column has the name second\_column and the type integer. Note that the column list is comma-separated and surrounded by parentheses.

Of course, the previous example was heavily contrived. Normally, you would give names to your tables and columns that convey what kind of data they store. So let's look at a more realistic example:

```sql
CREATE TABLE products (
    product_no integer,
    name text,
    price numeric
);
```
(The numeric type can store fractional components, as would be typical of monetary amounts.)

There is a limit on how many columns a table can contain. Depending on the column types, it is between 250 and 1600. 

If you no longer need a table, you can remove it using the `DROP TABLE` command. For example:

```sql
DROP TABLE my_first_table;
DROP TABLE products;
```

Attempting to drop a table that does not exist is an error. Nevertheless, it is common in SQL script files to unconditionally try to drop each table before creating it, ignoring any error messages, so that the script works whether or not the table exists. (If you like, you can use the `DROP TABLE IF EXISTS` variant to avoid the error messages, but this is not standard SQL.)

## Default values

A column can be assigned a default value. When a new row is created and no values are specified for some of the columns, those columns will be filled with their respective default values. 

If no default value is declared explicitly, the default value is the null value. This usually makes sense because a null value can be considered to represent unknown data.

In a table definition, default values are listed after the column data type. For example:

```sql
CREATE TABLE products (
    product_no integer,
    name text,
    price numeric DEFAULT 9.99
);
```
