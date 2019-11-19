---
id: "megawise_sql_syntax"
lang: "en"
title: "SQL Syntax"
---
# SQL Syntax

<!-- TOC -->

- [Lexical structure](#Lexical-structure)
    - [Identifiers and key words](#Identifiers-and-key-words)
    - [Constants](#Constants)
        - [String constants](#String-constants)
        - [String constants with C-style escapes](#String-constants-with-C-style-escapes)
        - [Dollar-Quoted string constants](#Dollar-Quoted-string-constants)
        - [Bit-String constants](#Bit-String-constants)
        - [Numeric constants](#Numeric-constants)
    - [Special characters](#Special-characters)
    - [Comments](#Comments)
    - [Operator precedence](#Operator-precedence)
- [Value expressions](#Value-expressions)
    - [Column references](#Column-references)
    - [Positional parameters](#Positional-parameters)
    - [Operator invocations](#Operator-invocations)
    - [Function calls](#Function-calls)
    - [Type casts](#Type-casts)
    - [Scalar subqueries](#Scalar-subqueries)

<!-- /TOC -->

## Lexical structure

SQL input consists of a sequence of commands. A command is composed of a sequence of tokens, terminated by a semicolon (“;”). The end of the input stream also terminates a command. Which tokens are valid depends on the syntax of the particular command.

A token can be a key word, an identifier, a quoted identifier, a literal (or constant), or a special character symbol. Tokens are normally separated by whitespace (space, tab, newline), but special characters must be close to other tokens.

For example, the following is (syntactically) valid SQL input:

```sql
SELECT * FROM MY_TABLE;
INSERT INTO MY_TABLE VALUES (3, 'hi there');
```

This is a sequence of three commands, one per line (although this is not required; more than one command can be on a line, and commands can usefully be split across lines).

### Identifiers and key words

Tokens such as `SELECT` and `VALUES` in the example above are examples of key words, that is, words that have a fixed meaning in the SQL language. The tokens MY_TABLE is an example of identifiers. Therefore they are sometimes simply called “names”. The maximum identifier length is 63 bytes.

SQL identifiers and key words must begin with a letter (a-z, but also letters with diacritical marks and non-Latin letters) or an underscore (_). Subsequent characters in an identifier or key word can be letters, underscores, digits (0-9), or dollar signs ($). Note that dollar signs are not allowed in identifiers according to the letter of the SQL standard, so their use might render applications less portable. The SQL standard will not define a key word that contains digits or starts or ends with an underscore, so identifiers of this form are safe against possible conflict with future extensions of the standard.

Key words and unquoted identifiers are case insensitive. Therefore:

```sql
INSERT INTO MY_TABLE VALUES (3, 'hi there');
```

can equivalently be written as:

```sql
INSERT INTO MY_TABLE VaLuEs (3, 'hi there');
```

A convention often used is to write key words in upper case and names in lower case, for example:

```sql
INSERT INTO "my_table" VALUES (3, 'hi there');
```

There is a second kind of identifier: the delimited identifier or quoted identifier. It is formed by enclosing an arbitrary sequence of characters in double-quotes ("). A delimited identifier is always an identifier, never a key word. So "select" could be used to refer to a column or table named “select”, whereas an unquoted select would be taken as a key word and would therefore provoke a parse error when used where a table or column name is expected. The example can be written with quoted identifiers like this:

```sql
INSERT INTO "my_table" VALUES (3, 'hi there');
```

A variant of quoted identifiers allows including escaped Unicode characters identified by their code points. This variant starts with U& (upper or lower case U followed by ampersand) immediately before the opening double quote, without any spaces in between, for example U&"foo". (Note that this creates an ambiguity with the operator &. Use spaces around the operator to avoid this problem.) Inside the quotes, Unicode characters can be specified in escaped form by writing a backslash followed by the four-digit hexadecimal code point number or alternatively a backslash followed by a plus sign followed by a six-digit hexadecimal code point number. For example, the identifier "data" could be written as:

```sql
U&"d\0061t\+000061"
```

The following less trivial example writes the Russian word “slon” (elephant) in Cyrillic letters:

```sql
U&"\0441\043B\043E\043D"
```

If a different escape character than backslash is desired, it can be specified using the `UESCAPE` clause after the string, for example:


```sql
U&"d!0061t!+000061" UESCAPE '!'
```
The escape character can be any single character other than a hexadecimal digit, the plus sign, a single quote, a double quote, or a whitespace character. Note that the escape character is written in single quotes, not double quotes.

To include the escape character in the identifier literally, write it twice.

The Unicode escape syntax works only when the server encoding is UTF8. When other server encodings are used, only code points in the ASCII range (up to \007F) can be specified.

Quoting an identifier also makes it case-sensitive, whereas unquoted names are always folded to lower case. For example, the identifiers FOO, foo, and "foo" are considered the same by MegaWise, but "Foo" and "FOO" are different from these three and each other. (The folding of unquoted names to lower case in MegaWise is incompatible with the SQL standard, which says that unquoted names should be folded to upper case. Thus, foo should be equivalent to "FOO" not "foo" according to the standard. If you want to write portable applications you are advised to always quote a particular name or never quote it.)

### Constants 

There are three kinds of implicitly-typed constants in MegaWise: strings, bit strings, and numbers. Constants can also be specified with explicit types, which can enable more accurate representation and more efficient handling by the system. 

#### String constants

A string constant in SQL is an arbitrary sequence of characters bounded by single quotes ('), for example 'This is a string'. To include a single-quote character within a string constant, write two adjacent single quotes, e.g., 'Dianne''s horse'. Note that this is not the same as a double-quote character (").

Two string constants that are only separated by whitespace with at least one newline are concatenated and effectively treated as if the string had been written as one constant. For example:

```sql
SELECT 'foo'
'bar';
is equivalent to:
```

```sql
SELECT 'foobar';
but:
```

```sql
SELECT 'foo'      'bar';
```

is not valid syntax. \(This slightly bizarre behavior is specified by SQL; MegaWise is following the standard.\)

#### String constants with C-style escapes 

MegaWise also accepts “escape” string constants, which are an extension to the SQL standard. An escape string constant is specified by writing the letter E (upper or lower case) just before the opening single quote, e.g., E'foo'. (When continuing an escape string constant across lines, write E only before the first opening quote.) Within an escape string, a backslash character (\) begins a C-like backslash escape sequence, in which the combination of backslash and following character(s) represent a special byte value, as shown in the following table.

| Backslash Escape Sequence	                          | Interpretation
                               |
| ----------------------------------------------- | ---------------------------------- |
| `\b`                                            | backspace                               |
| `\f`                                            | form feed                               |
| `\n`                                            | newline                               |
| `\r`                                            | carriage return                               |
| `\t`                                            | tab                             |
| `\*o*`, `\*oo*`, `\*ooo*` (*o* = 0 - 7)         | octal byte value                      |
| `\x*h*`, `\x*hh*` (*h* = 0 - 9, A - F)          | hexadecimal byte value                     |
| `\u*xxxx*`, `\U*xxxxxxxx*` (*x* = 0 - 9, A - F) | 16 or 32-bit hexadecimal Unicode character value |

Any other character following a backslash is taken literally. Thus, to include a backslash character, write two backslashes (\\). Also, a single quote can be included in an escape string by writing \', in addition to the normal way of ''.

It is your responsibility that the byte sequences you create, especially when using the octal or hexadecimal escapes, compose valid characters in the server character set encoding. When the server encoding is UTF-8, then the Unicode escapes or the alternative Unicode escape syntax should be used instead. (The alternative would be doing the UTF-8 encoding by hand and writing out the bytes, which would be very cumbersome.)

#### Dollar-Quoted string constants

While the standard syntax for specifying string constants is usually convenient, it can be difficult to understand when the desired string contains many single quotes or backslashes, since each of those must be doubled. To allow more readable queries in such situations, MegaWise provides another way, called “dollar quoting”, to write string constants. A dollar-quoted string constant consists of a dollar sign ($), an optional “tag” of zero or more characters, another dollar sign, an arbitrary sequence of characters that makes up the string content, a dollar sign, the same tag that began this dollar quote, and a dollar sign. For example, here are two different ways to specify the string “Dianne's horse” using dollar quoting:

```sql
$$Dianne's horse$$
$SomeTag$Dianne's horse$SomeTag$
```

Notice that inside the dollar-quoted string, single quotes can be used without needing to be escaped. Indeed, no characters inside a dollar-quoted string are ever escaped: the string content is always written literally. Backslashes are not special, and neither are dollar signs, unless they are part of a sequence matching the opening tag.

It is possible to nest dollar-quoted string constants by choosing different tags at each nesting level. This is most commonly used in writing function definitions. For example:

```sql
$function$
BEGIN
    RETURN ($1 ~ $q$[\t\r\n\v\\]$q$);
END;
$function$
```

Here, the sequence $q$[\t\r\n\v\\]$q$ represents a dollar-quoted literal string [\t\r\n\v\\], which will be recognized when the function body is executed by MegaWise. But since the sequence does not match the outer dollar quoting delimiter $function$, it is just some more characters within the constant so far as the outer string is concerned.

The tag, if any, of a dollar-quoted string follows the same rules as an unquoted identifier, except that it cannot contain a dollar sign. Tags are case sensitive, so $tag$String content$tag$ is correct, but $TAG$String content$tag$ is not.

A dollar-quoted string that follows a keyword or identifier must be separated from it by whitespace; otherwise the dollar quoting delimiter would be taken as part of the preceding identifier.

Dollar quoting is not part of the SQL standard, but it is often a more convenient way to write complicated string literals than the standard-compliant single quote syntax. It is particularly useful when representing string constants inside other constants, as is often needed in procedural function definitions. With single-quote syntax, each backslash in the above example would have to be written as four backslashes, which would be reduced to two backslashes in parsing the original string constant, and then to one when the inner string constant is re-parsed during function execution.

#### Bit-String constants

Bit-string constants look like regular string constants with a B (upper or lower case) immediately before the opening quote (no intervening whitespace), e.g., B'1001'. The only characters allowed within bit-string constants are 0 and 1.

Alternatively, bit-string constants can be specified in hexadecimal notation, using a leading X (upper or lower case), e.g., X'1FF'.

Both forms of bit-string constant can be continued across lines in the same way as regular string constants. Dollar quoting cannot be used in a bit-string constant.

#### Numeric constants

Numeric constants are accepted in these general forms:

```sql
digits
digits.[digits][e[+-]digits]
[digits].digits[e[+-]digits]
digitse[+-]digits
```

where digits is one or more decimal digits (0 through 9). At least one digit must be before or after the decimal point, if one is used. At least one digit must follow the exponent marker (e), if one is present. There cannot be any spaces or other characters embedded in the constant. Note that any leading plus or minus sign is not actually considered part of the constant; it is an operator applied to the constant.

These are some examples of valid numeric constants:

42
3.5
4.
.001
5e2
1.925e-3

A numeric constant that contains neither a decimal point nor an exponent is initially presumed to be type integer if its value fits in type integer (32 bits); otherwise it is presumed to be type bigint if its value fits in type bigint (64 bits); otherwise it is taken to be type numeric. Constants that contain decimal points and/or exponents are always initially presumed to be type numeric.

When necessary, you can force a numeric value to be interpreted as a specific data type by casting it. For example, you can force a numeric value to be treated as type real (float4) by:

```sql
REAL '1.23'  -- string style
1.23::REAL   -- MegaWise (historical) style
```

### Special characters

- A dollar sign ($) followed by digits is used to represent a positional parameter in the body of a function definition or a prepared statement. In other contexts the dollar sign can be part of an identifier or a dollar-quoted string constant.

- Parentheses (()) have their usual meaning to group expressions and enforce precedence. In some cases parentheses are required as part of the fixed syntax of a particular SQL command.

- Commas (,) are used in some syntactical constructs to separate the elements of a list.

- The semicolon (;) terminates an SQL command. It cannot appear anywhere within a command, except within a string constant or quoted identifier.

- The asterisk (*) is used in some contexts to denote all the fields of a table row or composite value. It also has a special meaning when used as the argument of an aggregate function, namely that the aggregate does not require any explicit parameter.

- The period (.) is used in numeric constants, and to separate schema, table, and column names.

### Comments  

A comment is a sequence of characters beginning with double dashes and extending to the end of the line, for example:

```sql
-- This is a standard SQL comment
```
Alternatively, C-style block comments can be used:

```sql
/* multiline comment
 * with nesting: /* nested block comment */
 */
```

where the comment begins with /* and extends to the matching occurrence of */. These block comments nest, as specified in the SQL standard but unlike C, so that one can comment out larger blocks of code that might contain existing block comments.

### Operator precedence

Most operators have the same precedence and are left-associative. The precedence and associativity of the operators is hard-wired into the parser.

You will sometimes need to add parentheses when using combinations of binary and unary operators. For instance:

```sql
SELECT 5 ! - 6;
```
will be parsed as:

```sql
SELECT 5 ! (- 6);
```

To get the desired behavior in this case, you must write:

```sql
SELECT (5 !) - 6;
```
The following table shows the precedence (highest to lowest) and associativity of the operators in MegaWise.


| Operator/Element                              | Associativity | Description                           |
| ---------------------------------------- | ------ | ------------------------------ |
| `.`                                      | left     | table/column name separator                  |
| `::`                                     | left     | typecast         |
| `+` `-`                                  | right     | unary plus, unary minus                 |
| `^`                                      | left     | exponentiation                           |
| `*` `/` `%`                              | left     | multiplication, division, modulo                     |
| `+` `-`                                  | left     | addition, subtraction                         |
| `BETWEEN` `IN` `LIKE` `ILIKE`            |        | range containment, set membership, string matching |
| `<` `>` `=` `<origin/develop=` `>=` `<>` |        | comparison operators                     |
| `NOT`                                    | right     | logical negation                       |
| `AND`                                    | left     | logical conjunction                       |
| `OR`                                     | left     | logical disjunction                       |


## Value expressions

Value expressions are used in a variety of contexts, such as in the target list of the `SELECT` command, as new column values in `INSERT`, or in search conditions in a number of commands. The expression syntax allows the calculation of values from primitive parts using arithmetic, logical, set, and other operations.

A value expression is one of the following:

- A constant or literal value
- A column reference
- A positional parameter reference, in the body of a function definition or prepared statement
- An operator invocation
- A function call
- A type cast
- A scalar subquery

### Column references

A column can be referenced in the form:

```sql
correlation.columnname
```

*correlation* is the name of a table (possibly qualified with a schema name), or an alias for a table defined by means of a FROM clause. The correlation name and separating dot can be omitted if the column name is unique across all the tables being used in the current query.

### Positional parameters

A positional parameter reference is used to indicate a value that is supplied externally to an SQL statement. Parameters are used in SQL function definitions and in prepared queries. The form of a parameter reference is:

```sql
$number
```

For example, consider the definition of a function, `dept`, as:

```sql
CREATE FUNCTION dept(text) RETURNS dept
    AS $$ SELECT * FROM dept WHERE name = $1 $$
    LANGUAGE SQL;
```

Here the `$1` references the value of the first function argument whenever the function is invoked.

### Operator invocations

Here are three possible syntaxes for an operator invocation:

*expression* *operator* *expression* (binary infix operator)
*operator* *expression* (unary prefix operator)
*expression* *operator* (unary postfix operator)
where the operator token follows the syntax rules of operators, or is one of the key words `AND`, `OR`, and `NOT`, or is a qualified operator name in the form:

```sql
OPERATOR(schema.operatorname)
```

### Function calls

The syntax for a function call is the name of a function (possibly qualified with a schema name), followed by its argument list enclosed in parentheses:

```sql
function_name ([expression [, expression ... ]] )
```
For example, the following computes the square root of 2:

```sql
sqrt(2)
```

### Type casts

A type cast specifies a conversion from one data type to another. MegaWise accepts two equivalent syntaxes for type casts:

```sql
CAST ( expression AS type )
expression::type

```

The `CAST` syntax conforms to SQL; the syntax with `::` is special MegaWise usage.

When a cast is applied to a value expression of a known type, it represents a run-time type conversion. An explicit type cast can usually be omitted if there is no ambiguity as to the type that a value expression must produce (for example, when it is assigned to a table column); the system will automatically apply a type cast in such cases.

### Scalar subqueries

A scalar subquery is an ordinary `SELECT` query in parentheses that returns exactly one row with one column. It is an error to use a query that returns more than one row or more than one column as a scalar subquery. But if, during a particular execution, the subquery returns no rows, there is no error; the scalar result is taken to be null. The subquery can refer to variables from the surrounding query, which will act as constants during any one evaluation of the subquery. 

For example, the following finds the largest city population in each state:

```sql
SELECT name, (SELECT max(pop) FROM cities WHERE cities.state = states.name)
    FROM states;
```
