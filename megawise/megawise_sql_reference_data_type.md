---
id: "megawise_data_type"
lang: "en"
title: "Data Types"
---
# Data Types


## Numeric data types

Numeric types consist of two-, four-, and eight-byte integers, four- and eight-byte floating-point numbers, and selectable-precision decimals. The following table lists the available types.

| Name               | Storage size | Description               | Range                |
| ------------------ | -------- | ------------------ | -------------------------------------------- |
| `smallint`         | 2 bytes    | small-range integer	         | -32768 to +32767                             |
| `integer`          | 4 bytes    | typical choice for integer	     | -2147483648 to +2147483647                   |
| `bigint`           | 8 bytes    | large-range integer	         | -9223372036854775808 to +9223372036854775807 |
| `decimal`          | variable     | user-specified precision, exact	 | up to 131072 digits before the decimal point; up to 16383 digits after the decimal point|
| `numeric`          | variable     | user-specified precision, exact	 | up to 131072 digits before the decimal point; up to 16383 digits after the decimal point|
| `real`             | 4 bytes    | variable-precision, inexact	   | 6 decimal digits precision|
| `double precision` | 8 bytes    | variable-precision, inexact	   | 15 decimal digits precision|
  


### Integer types

SQL only specifies the integer types `integer` (or `int`), `smallint`, and `bigint`. The type names `int2`, `int4`, and `int8` are extensions, which are also used by some other SQL database systems.


### Floating-Point types

The data types real and double precision are inexact, variable-precision numeric types. In practice, these types are usually implementations of IEEE Standard 754 for Binary Floating-Point Arithmetic (single and double precision, respectively).

- If you require exact storage and calculations (such as for monetary amounts), use the numeric type instead.
- If you want to do complicated calculations with these types for anything important, especially if you rely on certain behavior in boundary cases (infinity, underflow), you should evaluate the implementation carefully.
- Comparing two floating-point values for equality might not always work as expected.

In addition to ordinary numeric values, the floating-point types have several special values:

```sql
`Infinity`
`-Infinity`
`NaN
```

These represent the IEEE 754 special values “infinity”, “negative infinity”, and “not-a-number”, respectively. (On a machine whose floating-point arithmetic does not follow IEEE 754, these values will probably not work as expected.) When writing these values as constants in an SQL command, you must put quotes around them, for example UPDATE table SET x = '-Infinity'. On input, these strings are recognized in a case-insensitive manner.

------

**Note**

IEEE 754 specifies that NaN should not compare equal to any other floating-point value (including NaN). 

------


## Character types

The following table displays available character types in MegaWise.


|                 Name                 |      Description      |
| :----------------------------------: | :------------: |
| `character varying(n)`, `varchar(n)` |  variable-length with limit|
|      `character(n)`, `char(n)`       | fixed-length, blank padded|
|                `text`                |    variable unlimited length|


SQL defines two primary character types: `character varying(n)` and `character(n)`, where n is a positive integer. Both of these types can store strings up to n characters in length. If the string to be stored is shorter than the declared length, values of type character will be space-padded; values of type character varying will simply store the shorter string.

If one explicitly casts a value to character `varying(n)` or `character(n)`, then an over-length value will be truncated to n characters without raising an error. (This is required by the SQL standard.)

If `character varying` is used without length specifier, the type accepts strings of any size. The latter is a MegaWise extension. In addition, MegaWise provides the text type, which stores strings of any length. 


```sql
CREATE TABLE test1 (a character(4));
INSERT INTO test1 VALUES ('ok');
SELECT a, FROM test1; -- (1)
  a   
------
 ok   

CREATE TABLE test2 (b varchar(5));
INSERT INTO test2 VALUES ('ok');
INSERT INTO test2 VALUES ('good      ');
INSERT INTO test2 VALUES ('too long');
ERROR:  value too long for type character varying(5)
INSERT INTO test2 VALUES ('too long'::varchar(5)); -- explicit truncation
SELECT b, FROM test2;
   b   
-------
 ok    
 good  
 too l 
```

There are two other fixed-length character types in MegaWise. The name type is 64 bytes (63 usable characters plus terminator) in length. The "char" (note the quotes) type is one byte in length.


| Name     | Storage Size | Description                 |
| -------- | -------- | -------------------- |
| `"char"` | 1 byte    | single-byte internal type
       |
| `name`   | 64 bytes   | internal type for object names
 |

## Date/Time types

MegaWise supports the full set of SQL date and time types, shown in the following table. Dates are counted according to the Gregorian calendar.

| Name                                      | Storage Size | Description                     | Low Value       | High Value      | Resolution       |
| ----------------------------------------- | -------- | ------------------------ | ------------ | ----------- | ------------ |
| `timestamp [ without time zone ]` | 8 bytes    | both date and time (no time zone)	 | 4713 BC      | 294276 AD   | 1 millisecond |
| `date`                                    | 4 bytes    | date (no time of day)	 | 4713 BC      | 5874897 AD  | 1 day          |
| `time [ without time zone ]`      | 8 bytes    | time of day (no date)	   | 00:00:00     | 24:00:00    | 1 millisecond |
| `interval [ fields ]`             | 16 bytes   | time interval	    | -178000000 years | 178000000 years | 1 millisecond  |

The `interval` type has an additional option, which is to restrict the set of stored fields by writing one of these phrases:

```
YEAR
MONTH
DAY
HOUR
MINUTE
SECOND
YEAR TO MONTH
DAY TO HOUR
DAY TO MINUTE
DAY TO SECOND
HOUR TO MINUTE
HOUR TO SECOND
MINUTE TO SECOND
```

### Date/Time input

Date and time input is accepted in almost any reasonable format, including ISO 8601, SQL-compatible, traditional POSTGRES, and others. For some formats, ordering of day, month, and year in date input is ambiguous and there is support for specifying the expected ordering of these fields. Set the DateStyle parameter to MDY to select month-day-year interpretation, DMY to select day-month-year interpretation, or YMD to select year-month-day interpretation.

MegaWise is more flexible in handling date/time input than the SQL standard requires.

Remember that any date or time literal input needs to be enclosed in single quotes, like text strings. SQL requires the following syntax:

```sql
type 'value'
```

### Date/Time output

The output format of the date/time types can be set to one of the four styles ISO 8601, SQL (Ingres), traditional POSTGRES (Unix date format), or German. The default is the ISO format. (The SQL standard requires the use of the ISO 8601 format. The name of the “SQL” output format is a historical accident.) Table 5.5 shows examples of each output style. The output of the date and time types is generally only the date or time part in accordance with the given examples. However, the POSTGRES style outputs date-only values in ISO format.

| Style Specification	   | Description              | Example|
| ---------- | ----------------- | ------------------------------ |
| `ISO`      | ISO 8601/SQL standard	 | `1997-12-17 07:37:16-08` |
| `SQL`      | traditional style	          | `12/17/1997 07:37:16.00 PST`   |
| `Postgres` | original style	          | `Wed Dec 17 07:37:16 1997 PST` |
| `German`   | regional style          | `17.12.1997 07:37:16.00 PST`   |

------

**Note**
ISO 8601 specifies the use of uppercase letter `T` to separate the date and time. MegaWise accepts that format on input, but on output it uses a space rather than `T`, as shown above. This is for readability and for consistency with RFC 3339 as well as some other database systems.

------

In the SQL and POSTGRES styles, day appears before month if DMY field ordering has been specified, otherwise month appears before day. The following table shows an example:


| `datestyle` Setting | Input Ordering       | Example Output                       |
| --------------- | -------------- | ------------------------------ |
| `SQL, DMY`      | *day*/*month*/*year* | `17/12/1997 15:37:16.00 CET`   |
| `SQL, MDY`      | *month*/*day*/*year* | `12/17/1997 07:37:16.00 PST`   |
| `Postgres, DMY` | *day*/*month*/*year* | `Wed 17 Dec 07:37:16 1997 PST` |

The date/time style can be selected by the user using the `SET datestyle` command, the `result_config.date_order` parameter in the `megawise_config.yaml` configuration file, or the `PGDATESTYLE` environment variable on the server or client.


## Boolean type

MegaWise provides the standard SQL type boolean, as is shown in the following table. The boolean type can have several states: “true”, “false”, and a third state, “unknown”, which is represented by the SQL null value.


| Name      | Storage Size | Description         |
| --------- | -------- | ------------ |
| `boolean` | 1 byte    | state of true or false |

The datatype input function for type boolean accepts these string representations for the “true” state:

- `TRUE`
- `true`
- `yes`
- `on`
- `1`

and these representations for the “false” state:

- `FALSE`
- `false`
- `no`
- `off`
- `0`

Leading or trailing whitespace is ignored, and case does not matter. The key words `TRUE` and `FALSE` are the preferred (SQL-compliant) usage.

The datatype output function for type boolean always emits either t or f, as shown in the following example:

```sql
CREATE TABLE test1 (a boolean, b text);
INSERT INTO test1 VALUES (TRUE, 'sic est');
INSERT INTO test1 VALUES (FALSE, 'non est');
SELECT * FROM test1;
 a |    b
---+---------
 t | sic est
 f | non est

SELECT * FROM test1 WHERE a;
 a |    b
---+---------
 t | sic est
```
