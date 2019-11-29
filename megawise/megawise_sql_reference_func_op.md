---
id: "megawise_func_op"
lang: "en"
title: "Functions and Operators"
---
# Functions and Operators


## Logical operators

Usual logical operators include `AND`, `OR`, and `NOT`. SQL uses a three-valued logic system with true, false, and null, which represents “unknown”, as in the following truth tables:

| *a*   | *b*   | *a* AND *b* | *a* OR *b* |
| ----- | ----- | ----------- | ---------- |
| TRUE  | TRUE  | TRUE        | TRUE       |
| TRUE  | FALSE | FALSE       | TRUE       |
| TRUE  | NULL  | NULL        | TRUE       |
| FALSE | FALSE | FALSE       | FALSE      |
| FALSE | NULL  | FALSE       | NULL       |
| NULL  | NULL  | NULL        | NULL       |



| *a*   | NOT *a* |
| ----- | ------- |
| TRUE  | FALSE   |
| FALSE | TRUE    |
| NULL  | NULL    |

The operators `AND` and `OR` are commutative, that is, you can switch the left and right operand without affecting the result. 

## Comparison functions and operators

The usual comparison operators are available, as shown in the following table.


| Operator       | Description     |
| ------------ | -------- |
| `<`          | less than     |
| `>`          | greater than     |
| `<=`         | less than or equal to |
| `>=`         | greater than or equal to |
| `=`          | equal     |
| `<>` or `!=` | not equal   |

All comparison operators are binary operators that return values of type `boolean`; expressions like `1 < 2 < 3` are not valid (because there is no `<` operator to compare a Boolean value with 3).

As shown in the following table, MegaWise also supports comparison predicates, which are similar to operators but have special syntax required by the SQL standard.


| Predicate                                 | Description                           |
| ----------------------------------------- | ------------------------------ |
| *a* `BETWEEN` *x* `AND` *y*               | between x and y                    |
| *a* `NOT BETWEEN` *x* `AND` *y*           | not between x and y                    |
| *a* `BETWEEN SYMMETRIC` *x* `AND` *y*     | between, after sorting the comparison values   |
| *a* `NOT BETWEEN SYMMETRIC` *x* `AND` *y* | not between, after sorting the comparison values|
| *expression* `IS NULL`                    | is null                         |
| *expression* `IS NOT NULL`                | is not null                       |
| *expression* `ISNULL`                     | is null (nonstandard syntax)           |
| *expression* `NOTNULL`                    | is not null (nonstandard syntax)         |

```sql
a BETWEEN x AND y
```

is equivalent to

```sql
a >= x AND a <= y
```

Notice that `BETWEEN` treats the endpoint values as included in the range. `NOT BETWEEN` does the opposite comparison:

```sql
a NOT BETWEEN x AND y
```

is equivalent to

```sql
a < x OR a > y
```

`BETWEEN SYMMETRIC` is like `BETWEEN` except there is no requirement that the argument to the left of `AND` be less than or equal to the argument on the right. If it is not, those two arguments are automatically swapped, so that a nonempty range is always implied.

Ordinary comparison operators yield null (signifying “unknown”), not true or false, when either input is null. For example, `7 = NULL` yields null, as does `7 <> NULL`. 

To check whether a value is or is not null, use the predicates:

```sql
expression IS NULL
expression IS NOT NULL
```

or the equivalent, but nonstandard, predicates:

```sql
expression ISNULL
expression NOTNULL
```

Do not write `expression = NULL` because `NULL` is not “equal to” `NULL`. (The null value represents an unknown value, and it is not known whether two unknown values are equal.)


## Mathematical functions and operators

MegaWise provides mathematical operators for multiple numeric types. The following table shows the available mathematical operators.


| Operator | Description                   | Example        | Result  |
| ------ | ---------------------- | ----------- | ----- |
| `+`    | addition                     | `2 + 3`     | `5`   |
| `-`    | subtraction                     | `2 - 3`     | `-1`  |
| `*`    | multiplication                     | `2 * 3`     | `6`   |
| `/`    | division (integer division truncates the result)	 | `4 / 2`     | `2`   |
| `%`    | modulo (remainder)	             | `5 % 4`     | `1`   |

The following table shows the available mathematical functions. Except where noted, any given form of a function returns the same data type as its argument.


| Function    | Return Type           | Description       | Example      | Result               |
| ----------------------------- | ------------------ | ------------------------------------ | ----------------- | ------------------ |
| `abs(x)`                      | (same as input)	     | absolute value	        | `abs(-17.4)`      | `17.4`             |
| `cbrt(dp)`                    | `dp`               | cube root	         | `cbrt(27.0)`      | `3`                |
| `ceil(dp or numeric)`         | (same as input)	     | nearest integer greater than or equal to argument	      | `ceil(-42.8)`     | `-42`              |
| `ceiling(dp or numeric)`      | (same as input)	   | nearest integer greater than or equal to argument (same as `ceil`)	| `ceiling(-95.3)`  | `-95`              |
| `log(dp or numeric)`          | (same as input)	   | base 10 logarithm   | `log(100.0)`      | `2`                |
| `mod(y, x)`                   | (same as argument types) | remainder of *y*/*x*	      | `mod(9,4)`        | `1`                |
| `pi()`                        | `dp`               | “π” constant                           | `pi()`            | `3.14159265358979` |
| `power(a dp, b dp)`           | `dp`               | *a* raised to the power of *b*     | `power(9.0, 3.0)` | `729`              |
| `power(a numeric, b numeric)` | `numeric`          | *a* raised to the power of *b*       | `power(9.0, 3.0)` | `729`              |
| `round(dp or numeric)`        | (same as input)	    | round to nearest integer     | `round(42.4)`     | `42`               |
| `sqrt(dp or numeric)`         | (same as input)	    | square root                             | `sqrt(2.0)`       | `1.4142135623731`  |

The following table shows trigonometric functions.

| Function (radians) |  Description  |
| :---------: | :----: |
|  `acos(x)`  | inverse cosine |
|  `asin(x)`  | inverse sine |
|  `atan(x)`  | inverse tangent |
|  `cos(x)`   |  cosine  |
|  `cot(x)`   |  cotangent  |
|  `sin(x)`   |  sine  |
|  `tan(x)`   |  tangent  |


## String functions and operators

MegaWise supports the following string functions and operators:


| Function       | Return Type | Description   | Example               | Result  |
| ---------------------------------------- | -------- | -------- | -------------------------------- | ----- |
| `substring(string, int)`                 | text     | Extract substring | substring('Thomas', 2)           | homas |
| `substring(string, int, int)`            | text     | Extract substring | substring('Thomas', 2, 3)        | hom   |
| `substr(string, int)`                    | text     | Extract substring | substr('Thomas', 2)              | homas |
| `substr(string, int, int)`               | text     | Extract substring | substr('Thomas', 2, 3)           | hom   |
| `substring(string [from int] [for int])` | text     | Extract substring | substring('Thomas' from 2 for 3) | hom   |


## Pattern matching

MegaWise provides the `LIKE` operator for pattern matching.

```sql
string LIKE pattern [ESCAPE escape-character]
string NOT LIKE pattern [ESCAPE escape-character]
```

The `LIKE` expression returns true if the string matches the supplied pattern. (As expected, the `NOT LIKE` expression returns false if `LIKE` returns true, and vice versa. An equivalent expression is `NOT (*string* LIKE *pattern*)`.)

If pattern does not contain percent signs or underscores, then the pattern only represents the string itself; in that case `LIKE` acts like the equals operator. An underscore (_) in pattern stands for (matches) any single character; a percent sign (%) matches any sequence of zero or more characters.

Some examples:

```sql
'abc' LIKE 'abc'    true
'abc' LIKE 'a%'     true
'abc' LIKE '_b_'    true
'abc' LIKE 'c'      false
```

To match a literal underscore or percent sign without matching other characters, the respective character in *pattern* must be preceded by the escape character. The default escape character is the backslash but a different one can be selected by using the `ESCAPE` clause. To match the escape character itself, write two escape characters.

> **Note:** The backslash already has a special meaning in string literals, so to write a pattern constant that contains a backslash you must write two backslashes in an SQL statement. Thus, writing a pattern that actually matches a literal backslash means writing four backslashes in the statement. You can avoid this by selecting a different escape character with ESCAPE; then a backslash is not special to LIKE anymore. (But backslash is still special to the string literal parser, so you still need two of them to match a backslash.)


## Date/Time functions and operators

The following table illustrates the behaviors of the basic arithmetic operators (+, *, etc.). 


| Operator | Example                                                         | Result                              |
| ------ | ------------------------------------------------------------ | --------------------------------- |
| `+`    | `date '2001-09-28' + integer '7'`                            | `date '2001-10-05'`               |
| `+`    | `date '2001-09-28' + interval '1 hour'`                      | `timestamp '2001-09-28 01:00:00'` |
| `+`    | `date '2001-09-28' + time '03:00'`                           | `timestamp '2001-09-28 03:00:00'` |
| `+`    | `interval '1 day' + interval '1 hour'`                       | `interval '1 day 01:00:00'`       |
| `+`    | `timestamp '2001-09-28 01:00' + interval '23 hours'`         | `timestamp '2001-09-29 00:00:00'` |
| `+`    | `time '01:00' + interval '3 hours'`                          | `time '04:00:00'`                 |
| `-`    | `- interval '23 hours'`                                      | `interval '-23:00:00'`            |
| `-`    | `date '2001-10-01' - date '2001-09-28'`                      | `integer '3'` (days)              |
| `-`    | `date '2001-10-01' - integer '7'`                            | `date '2001-09-24'`               |
| `-`    | `date '2001-09-28' - interval '1 hour'`                      | `timestamp '2001-09-27 23:00:00'` |
| `-`    | `time '05:00' - time '03:00'`                                | `interval '02:00:00'`             |
| `-`    | `time '05:00' - interval '2 hours'`                          | `time '03:00:00'`                 |
| `-`    | `timestamp '2001-09-28 23:00' - interval '23 hours'`         | `timestamp '2001-09-28 00:00:00'` |
| `-`    | `interval '1 day' - interval '1 hour'`                       | `interval '1 day -01:00:00'`      |
| `-`    | `timestamp '2001-09-29 03:00' - timestamp '2001-09-27 12:00'` | `interval '1 day 15:00:00'`       |
| `*`    | `900 * interval '1 second'`                                  | `interval '00:15:00'`             |
| `*`    | `21 * interval '1 day'`                                      | `interval '21 days'`              |
| `*`    | `double precision '3.5' * interval '1 hour'`                 | `interval '03:30:00'`             |
| `/`    | `interval '1 hour' / double precision '1.5'`                 | `interval '00:40:00'`             |

The following table shows functions for Date/Time values.


| Function                    | Return Type         | Description       | Example                                               | Result |
| ------------------------------- | ---------------- | ---------- | -------------------------------------------------- | ---- |
| `extract`(field from source) | double precision | get subfield | extract(hour from timestamp '2001-02-16 20:38:40') | 20   |

The `extract` function retrieves subfields such as year or hour from date/time values.

*source* must be a value expression of type timestamp, time, or interval. (Expressions of type date are cast to timestamp and can therefore be used as well.) 

*field* is an identifier or string that selects what field to extract from the source value. The `extract` function returns values of type double precision. 

The following are valid field names:

- hour

    The hour field（0 - 23）
    ```sql
    SELECT EXTRACT(HOUR FROM TIMESTAMP '2001-02-16 20:38:40'); 
    ```

    Result：20

- minute

    The minute field（0 - 59）
    ```sql
    SELECT EXTRACT(MINUTE FROM TIMESTAMP '2001-02-16 20:38:40'); 
    ```

    Result：38

- second

    The seconds field, including fractional parts（0 - 59）

    ```sql
    SELECT EXTRACT(SECOND FROM TIMESTAMP '2001-02-16 20:38:40');
    ```

    Result：40

    ```sql
    SELECT EXTRACT(SECOND FROM TIME '17:12:28.5'); 
    ```

    Result：28.5

- day
 
    For timestamp values, the day (of the month) field (1 - 31) ; for interval values, the number of days

    ```sql
    SELECT EXTRACT(DAY FROM TIMESTAMP '2001-02-16 20:38:40');
    ```

    Result：16

    ```sql
    SELECT EXTRACT(DAY FROM INTERVAL '40 days 1 minute');
    ```

    Result：40

- month

    For timestamp values, the number of the month within the year (1 - 12) ; for interval values, the number of months, modulo 12 (0 - 11)

    ```sql
    SELECT EXTRACT(MONTH FROM TIMESTAMP '2001-02-16 20:38:40');
    ```

    Result：2

    ```sql
    SELECT EXTRACT(MONTH FROM INTERVAL '2 years 3 months'); 
    ```

    Result：3

    ```sql
    SELECT EXTRACT(MONTH FROM INTERVAL '2 years 13 months'); 
    ```

    Result：1

- year

    The year field.

    ```sql
    SELECT EXTRACT(YEAR FROM TIMESTAMP '2001-02-16 20:38:40'); 
    ```

    Result：2001

## Conditional expressions

The conditional expression `CASE` in MegaWise is similar to if/else statements in other programming languages:

```sql
CASE WHEN condition THEN result
     [WHEN ...]
     [ELSE result]
END
```

`CASE` clauses can be used wherever an expression is valid. Each *condition* is an expression that returns a boolean result. If the *condition*'s result is true, the value of the `CASE` expression is the result that follows the *condition*, and the remainder of the `CASE` expression is not processed. If the *condition*'s result is not true, any subsequent `WHEN` clauses are examined in the same manner. If no `WHEN` *condition* yields true, the value of the `CASE` expression is the result of the `ELSE` clause. If the `ELSE` clause is omitted and no *condition* is true, the result is null.

An example of the `CASE` expression:

```sql
SELECT * FROM test;

 a
---
 1
 2
 3


SELECT a,
       CASE WHEN a=1 THEN 'one'
            WHEN a=2 THEN 'two'
            ELSE 'other'
       END
    FROM test;

 a | case
---+-------
 1 | one
 2 | two
 3 | other
```

The data types of all the result expressions must be convertible to a single output type. 

There is a “simple” form of CASE expression that is a variant of the general form above:

```sql
CASE expression
    WHEN value THEN result
    [WHEN ...]
    [ELSE result]
END
```

The first *expression* is computed, then compared to each of the *value* expressions in the `WHEN` clauses until one is found that is equal to it. If no match is found, the *result* of the `ELSE` clause (or a null value) is returned.

The example above can be written using the simple `CASE` syntax:

```sql
SELECT a,
       CASE a WHEN 1 THEN 'one'
              WHEN 2 THEN 'two'
              ELSE 'other'
       END
    FROM test;

 a | case
---+-------
 1 | one
 2 | two
 3 | other
```

## Aggregate functions

Aggregate functions compute a single result from a set of input values. The following table shows supported functions in MegaWise:


| Function   | Parameter type                                      | Return Type                                                     | Partial Mode | Description           |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- | -------------------------------- |
| `avg(expression)`   | `smallint`、`int`、`bigint`、`real`、`double precision`、`numeric` or `interval` | `numeric` for any integer-type argument, `double precision` for a floating-point argument, otherwise the same as the argument data type | Yes      | the average (arithmetic mean) of all input values |
| `count(*)`          |                                                              | `bigint`                                                     | Yes      | number of input rows  |
| `count(expression)` | any                                                          | `bigint`                                                     | Yes      | number of input rows for which the value of *expression* is not null |
| `max(expression)`   | any numeric, string, date/time, network, or enum type, or arrays of these types | same as argument type   | Yes      | maximum value of *expression* across all input values |
| `min(expression)`   | any numeric, string, date/time, network, or enum type, or arrays of these types| same as argument type | Yes      | minimum value of *expression* across all input values |
| `sum(expression)`   | `smallint`、`int`、 `bigint`、`real`、`double precision`、`numeric` or `interval` | `bigint` for `smallint` or int arguments, `numeric` for `bigint` arguments, otherwise the same as the argument data type | Yes      | sum of *expression* across all input values  


## Subquery expressions

This section describes the SQL-compliant subquery expressions available in Megawise. All of the expression forms documented in this section return Boolean (true/false) results.

### EXISTS subquery

```sql
EXISTS (subquery)
```

The argument of `EXISTS` is an arbitrary `SELECT` statement, or subquery. The subquery is evaluated to determine whether it returns any rows. If it returns at least one row, the result of `EXISTS` is “true”; if the subquery returns no rows, the result of `EXISTS` is “false”.

The subquery can refer to variables from the surrounding query, which will act as constants during any one evaluation of the subquery.

This simple example is like an inner join on `col2`, but it produces at most one output row for each `tab1` row, even if there are several matching `tab2` rows:

```sql
SELECT col1
FROM tab1
WHERE EXISTS (SELECT 1 FROM tab2 WHERE col2 = tab1.col2);
```

### IN subquery

```sql
expression IN (subquery)
```

The parenthesized subquery must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result. The result of `IN` is “true” if any equal subquery row is found. The result is “false” if no equal row is found (including the case where the subquery returns no rows).

Note that if the left-hand expression yields null, or if there are no equal right-hand values and at least one right-hand row yields null, the result of the `IN` construct will be null, not false. 

### NOT IN subquery

```sql
expression NOT IN (subquery)
```

The parenthesized subquery must return exactly one column. The left-hand expression is evaluated and compared to each row of the subquery result. The result of NOT IN is “true” if only unequal subquery rows are found (including the case where the subquery returns no rows). The result is “false” if any equal row is found.

Note that if the left-hand expression yields null, or if there are no equal right-hand values and at least one right-hand row yields null, the result of the NOT IN construct will be null, not true.


## System administration functions

The following table shows the functions available to query and alter run-time configuration parameters.


| Name    | Return Type | Description         |
| ----------------------------------------------- | -------- | ---------------------- |
| `current_setting(setting_name [, missing_ok ])` | `text`   | get current value of setting|
| `set_config(setting_name, new_value, is_local)` | `text`   | set parameter and return new value |

The function `current_setting` yields the current value of the setting *setting_name*.

```sql
SELECT current_setting('datestyle');

 current_setting
-----------------
 ISO, MDY
(1 row)
```

If there is no setting named *setting_name*, `current_setting` throws an error unless *missing_ok* is supplied and is `true`.

`set_config` sets the parameter *setting_name* to *new_value*. If *is_local* is `true`, the new value will only apply to the current transaction. If you want the new value to apply for the current session, use `false` instead. An example:

```sql
SELECT set_config('log_statement_stats', 'off', false);

 set_config
------------
 off
(1 row)
```
