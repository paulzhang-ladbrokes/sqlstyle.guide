# SQL style guide

## Overview

These are guidelines to help you write SQL queries that will be easier to read.

Remember that even if you hate a given style at first, generally speaking it is
far more important that we have _any_ agreed upon style than that we all like it,
and furthermore the odds are that you will eventually learn to like it once you
start to follow it.

Queries submitted to the Data teams should follow the style guide, and queries starkly
contrasting to, or ignorant of, these guidelines may be asked to be reformatted and
resubmitted. Feel free to ask about style rationale, or pose a question how you can make
your query (or often taking a step back, question) adhere.

Original SQL style guide by [Simon Holywell][simon] is licensed under a [Creative Commons
Attribution-ShareAlike 4.0 International License][licence].
Based on a work at [http://www.sqlstyle.guide][self].

## General

### Do

* Use consistent and descriptive identifiers and names.
* Make judicious use of white space and indentation to make code easier to read.
* Store [ISO-8601][iso-8601] compliant time and date information
  (`YYYY-MM-DD HH:MM:SS.SSSSS`).
* Try to use only standard SQL functions instead of vendor specific functions for
  reasons of portability.
* Keep code succinct and devoid of redundant SQL—such as unnecessary quoting or
  parentheses or `WHERE` clauses that can otherwise be derived.
* Include comments in SQL code where necessary. Use the C style opening `/*` and
  closing `*/` where possible otherwise preceed comments with `--` and finish
  them with a new line.

### Avoid

* (LB) ~~CamelCase—it is difficult to scan quickly~~.
* Descriptive prefixes or Hungarian notation such as `sp_` or `tbl`.
* Plurals—use the more natural collective term where possible instead. For example
  `staff` instead of `employees` or `people` instead of `individuals`.
* Quoted identifiers—if you must use them then stick to SQL92 double quotes for
  portability (you may need to configure your SQL server to support this depending
  on vendor).

## Naming conventions

### General

* Ensure the name is unique and does not exist as a [MySQLreserved keyword][reserved-keywords]
  or [Redshift reserved keyword](http://docs.aws.amazon.com/redshift/latest/dg/r_pg_keywords.html)
* Avoid abbreviations and if you have to use them make sure they are commonly
  understood.
* Keep the length to a maximum of 30 bytes—in practice this is 30 characters
  unless you are using multi-byte character set.
* Names must begin with a letter and may not end with an underscore.
* Only use letters, numbers and underscores in names.
* (LB) Use lowerCamelCase for column name and UpperCamelCase for table name.
* (LB) Avoid the use of underscore (`_`)
* ~~Avoid the use of multiple consecutive underscores—these can be hard to read.~~
* ~~Use underscores where you would naturally include a space in the name (first
  name becomes `first_name`).~~

```sql
SELECT firstName
FROM Staff;
```

### Tables

* Do not prefix with `tbl` or any other such descriptive prefix or Hungarian
  notation.
* Never give a table the same name as one of its columns and vice versa.

### Columns

* Always use the singular name.
* Where possible avoid simply using `id` as the primary identifier for the table.
* Do not add a column with the same name as its table and vice versa.
* Always use lowercase except where it may make sense not to such as proper nouns.

### Aliasing or correlations

* Should relate in some way to the object or expression they are aliasing.
* As a rule of thumb the correlation name should be the first letter of each word
  in the object's name.
* If there is already a correlation with the same name then append a number.
* Always include the `AS` keyword—makes it easier to read as it is explicit.
* For computed data (`SUM()` or `AVG()`) use the name you would give it were it
  a column defined in the schema.

```sql
SELECT firstName AS fn
FROM Staff AS s1
  JOIN Student AS s2
    ON s2.mentorId = s1.staffNum;
```
```sql
SELECT SUM(s.monitorTally) AS monitorTotal
FROM Staff AS s;
```

### Stored procedures

* The name must contain a verb.
* Do not prefix with `sp_` or any other such descriptive prefix or Hungarian
  notation.

### Uniform prefixes

* `is_` - denotes a boolean

### Uniform suffixes

The following suffixes have a universal meaning ensuring the columns can be read
and understood easily from SQL code. Use the correct suffix where appropriate.

* `_id`—a unique identifier such as a column that is a primary key.
* `_at`-denotes a column that contains the time of something.
* `_date`—denotes a column that contains the date of something.
* `_pct`—percent
* `_status`—flag value or some other status of any type such as
  `publication_status`.
* `_total`—the total or sum of a collection of values.
* `_num`—denotes the field contains any kind of number.
* `_name`—signifies a name such as `first_name`.
* `_seq`—contains a contiguous sequence of values.
* `_tally`—a count.
* `_size`—the size of something such as a file size or clothing.
* `_addr`—an address for the record could be physical or intangible such as
  `ip_addr`.

## Query syntax

### Reserved words

Always use uppercase for the [reserved keywords][reserved-keywords]
like `SELECT` and `WHERE`.

It is best to avoid the abbreviated keywords and use the full length ones where
available (prefer `ABSOLUTE` to `ABS`).

Do not use database server specific keywords where an ANSI SQL keyword already
exists performing the same function. This helps to make code more portable.

```sql
SELECT modelNum
FROM Phone AS p
WHERE p.releaseDate > '2016-09-30';
```

### White space

To make the code easier to read it is important that the correct amount of
spacing is used. Do not crowd code or remove natural language spaces.

### Indentation

To ensure that SQL is readable it is important that standards of indentation
are followed.

**ONLY** the fundamental keywords - `SELECT`, `FROM`, `WHERE`, `GROUP BY`, `HAVING`, `LIMIT`,
and `ORDER BY`should be fully left justified.

For single `SELECT`s, you can use the single line form:

```sql
SELECT firstName
FROM Rapper
```

If you are `SELECT`ing more than one column, place all selects on their own line indented 2
spaces in a block after the `SELECT` keyword.

```sql
SELECT
  firstName,
  lastName,
  isStillTippinOnFourFours,
  isStillWrappedInFourVogues
FROM Rapper
WHERE firstName = 'Mike'
  AND lastName = 'Jones'
```

This allows the reader to quickly scan for the important building blocks of the query.

#### Spaces

Always include spaces:

* before and after equals (`=`)
* after commas (`,`)
* surrounding apostrophes (`'`) where not within parentheses or with a trailing
  comma or semicolon.

```sql
SELECT
  a.title,
  a.releaseDate,
  a.recordingDate
FROM Album AS a
WHERE a.title = 'Charcoal Lane'
  OR a.title = 'The New Danger';
```

#### Line spacing

Always include newlines/vertical space:

* after `WITH` subqueries
* after a comma (e.g. between elements of a `SELECT`)
* before `AND` or `OR`
* after semicolons to separate queries for easier reading
* after each keyword definition
* to separate code into related sections, which helps to ease the readability of
  large chunks of code.

Keeping all the dependent keywords (e.g. `ON` depends on `JOIN`, `AND` depends on `WHERE`,
`SET` depends on `UPDATE` right aligned with the top level keywords helps make it clear
all the lines are part of the same clause.

```sql
INSERT INTO Album (title, releaseDate, recordingDate)
VALUES ('Charcoal Lane', '1990-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000'),
       ('The New Danger', '2008-01-01 01:01:01.00000', '1990-01-01 01:01:01.00000');
```

```sql
INSERT INTO Album
  (
    title,
    releaseDate,
    recordingDate
  )
VALUES
  (
    'Charcoal Lane',
    '1990-01-01 01:01:01.00000',
    '1990-01-01 01:01:01.00000'
  ),
  (
    'The New Danger',
    '2008-01-01 01:01:01.00000',
    '1990-01-01 01:01:01.00000'
  );
```

```sql
UPDATE Album
  SET releaseDate = '1990-01-01 01:01:01.00000'
WHERE title = 'The New Danger'
  AND recordingDate = '1990-01-01 01:01:01.00000';
```

```sql
SELECT
  a.title,
  a.releaseDate,
  a.recordingDate,
  a.productionDate
FROM Album AS a
WHERE a.title = 'Charcoal Lane'
  OR a.title = 'The New Danger';
```

#### JOINs

Joins should be indented 2 spaces right from the `FROM` keyword

Single line `JOIN`s are fine for simple situations

```sql
SELECT r.lastName
FROM Rider AS r
  JOIN Bike AS b ON r.bikeVinNum = b.vinNum
  INNER JOIN Crew AS c ON r.crewChiefLastName = c.lastName
```

Multi line JOINs should be indented as per the dependent keywords rule:

```sql
SELECT r.lastName
FROM Rider AS r
  INNER JOIN Bike AS b
    ON r.bikeVinNum = b.vinNum
      AND r.bikeLane = r.lane
  INNER JOIN Crew c ON r.crewChiefLastName = c.lastName
WHERE id = 5
```

#### Boolean Expressions

Complicated boolean expressions can be extremely hard to read.  To help with this, left justify boolean subclauses
such that the whitespace indicates the current block and the boolean `AND`/`OR` are justified the same as the
expressions they apply to.

```sql
SELECT firstName
FROM Rapper
WHERE
  (
    firstName = 'Mike'
    AND
    lastName = 'Jones'
  )
  OR
  (
    wasTipping = 1
    AND
    isStillTippinOnFourFours = 1
    AND
    (
      isStillWrappedInFourVogues = 1
      OR
      isWoodGrainGripping = 0
    )
  );
```

#### WITH statements (PostgreSQL only)

Indent them until the closing parentheses.

```sql
WITH MyTmpTable AS (
  SELECT r.lastName
  FROM Rider AS r
    INNER JOIN Bike AS b ON r.bikeVinNum = b.vinNum
  WHERE id = 10
),

MyOtherTmpTable AS (
  SELECT lastName
  FROM Staff
)

SELECT *
FROM MyTmpTable
  JOIN MyOtherTmpTable ON myOtherTmpTable.lastName = myTmpTable.lastName
```

#### Sub-queries

In PostgreSQL you should probably be writing subqueries with `WITH` clauses and mostly avoiding the
use of inline subqueries.

In MySQL or other query engines that do not support `WITH`, sub-queries should be left aligned 2
spaces to the right of the opening parentheses and then laid out using the same style as a `WITH`
statement w/r/t parentheses.

```sql
SELECT
  r.lastName,
  (
    SELECT MAX(YEAR(championshipDate))
    FROM Champion AS c
    WHERE c.lastName = r.lastName
      AND c.confirmed = 'Y'
  ) AS lastChampionshipYear
FROM Rider AS r
WHERE r.lastName IN
  (
    SELECT c.lastName
    FROM Champion AS c
    WHERE YEAR(championshipDate) > '2008'
      AND c.confirmed = 'Y'
  )
```

#### Case statements (PostreSQL)

`CASE` and `END` can either be inline if they are less than 80 characters:

```sql
SELECT CASE WHEN x > y THEN 1 ELSE 0 END
FROM Table
```

Or WHEN/END should have 2 space left justification, and `WHEN`/`THEN` should be indented the same as the `ELSE`/`value`.
Multiple `AND` or `OR` clauses can be inlined if < 80 characters, or should be right aligned with the whitespace river
after `WHEN` if they are longer

```sql
SELECT
  CASE
    WHEN x > y AND x < z
      THEN 'x more than y but less than z'
    WHEN x > y AND x > z
      THEN 'x more than y and more than z'
    WHEN someVeryLongThingHappened BETWEEN '2016-01-01' AND '2016-01-01'
     AND someEventVeryMuchLongerThingHappened BETWEEN '2016-01-01' AND '2016-01-01'
      THEN 'something happened later'
    WHEN someVeryLongThingHappened BETWEEN '2016-01-01' AND '2016-01-01'
      OR someEventVeryMuchLongerThingHappened BETWEEN '2016-01-01' AND '2016-01-01'
      THEN 'something else'
    ELSE
      'x and y not related'
    END AS city,
  streetAddress,
  phoneNumber
FROM OfficeLocation
```

#### Case statements (MySql)

```sql
SELECT
  CASE postcode
    WHEN 'BN1'
      THEN 'Brighton'
    WHEN 'EH1'
      THEN 'Edinburgh'
    END AS city,
  streetAddress,
  phoneNumber
FROM OfficeLocation
```

### Preferred formalisms

* Make use of `BETWEEN` where possible instead of combining multiple statements
  with `AND`.
* Similarly use `IN()` instead of multiple `OR` clauses - though note that MySQL is **really bad** at optimizing `IN` even with an obvious index, so use your discretion depending on what type of server you are querying and how big the table is.
* Where a value needs to be interpreted before leaving the database use the `CASE`
  expression. `CASE` statements can be nested to form more complex logical structures.
* Avoid the use of `UNION` clauses and temporary tables where possible. If the
  schema can be optimised to remove the reliance on these features then it most
  likely should be.

```sql
SELECT
  CASE postcode
    WHEN 'BN1'
      THEN 'Brighton'
    WHEN 'EH1'
      THEN 'Edinburgh'
    END AS city
FROM OfficeLocation
WHERE country = 'United Kingdom'
  AND openingTime BETWEEN 8 AND 9
  AND postcode IN ('EH1', 'BN1', 'NN1', 'KW1')
```

## Create syntax

When declaring schema information it is also important to maintain human
readable code. To facilitate this ensure the column definitions are ordered and
grouped where it makes sense to do so.

Indent column definitions by four (4) spaces within the `CREATE` definition.

### Choosing data types

* Use 6 decimal places on dollars (not cents!) for storing currency information, e.g. `DECIMAL(20,6)`.
* Where possible do not use vendor specific data types—these are not portable and
  may not be available in older versions of the same vendor's software.
* Only use `REAL` or `FLOAT` types where it is strictly necessary for floating
  point mathematics otherwise prefer `NUMERIC` and `DECIMAL` at all times. Floating
  point rounding errors are a nuisance!

### Specifying default values

* The default value must be the same type as the column—if a column is declared
  a `DECIMAL` do not provide an `INTEGER` default value.
* Default values must follow the data type declaration and come before any
  `NOT NULL` statement.

### Constraints and keys

Constraints and their subset, keys, are a very important component of any
database definition. They can quickly become very difficult to read and reason
about though so it is important that a standard set of guidelines are followed.

#### Choosing keys

Deciding the column(s) that will form the keys in the definition should be a
carefully considered activity as it will effect performance and data integrity.

1. The key should be unique to some degree.
2. Consistency in terms of data type for the value across the schema and a lower
   likelihood of this changing in the future.
3. Can the value be validated against a standard format (such as one published by
   ISO)? Encouraging conformity to point 2.
4. Keeping the key as simple as possible whilst not being scared to use compound
   keys where necessary.

It is a reasoned and considered balancing act to be performed at the definition
of a database. Should requirements evolve in the future it is possible to make
changes to the definitions to keep them up to date.

#### Defining constraints

Once the keys are decided it is possible to define them in the system using
constraints along with field value validation.

##### General

* Avoid explicit foreign keys; they are usually more trouble than they are worth.
* Tables must have at least one key to be complete and useful.
* Constraints should be given a custom name excepting `UNIQUE`, `PRIMARY KEY`
  and `FOREIGN KEY` where the database vendor will generally supply sufficiently
  intelligible names automatically.

##### Layout and order

* Specify the primary key first right after the `CREATE TABLE` statement.
* Constraints should be defined directly beneath the column they correspond to.
  Indent the constraint so that it aligns to the right of the column name.
* If it is a multi-column constraint then consider putting it as close to both
  column definitions as possible and where this is difficult as a last resort
  include them at the end of the `CREATE TABLE` definition.
* If it is a table level constraint that applies to the entire table then it
  should also appear at the end.
* Use alphabetical order where `ON DELETE` comes before `ON UPDATE`.
* If it make senses to do so align each aspect of the query on the same character
  position. For example all `NOT NULL` definitions could start at the same
  character position. This is not hard and fast, but it certainly makes the code
  much easier to scan and read.

##### Validation

* Use `LIKE` and `SIMILAR TO` constraints to ensure the integrity of strings
  where the format is known.
* Where the ultimate range of a numerical value is known it must be written as a
  range `CHECK()` to prevent incorrect values entering the database or the silent
  truncation of data too large to fit the column definition. In the least it
  should check that the value is greater than zero in most cases.
* `CHECK()` constraints should be kept in separate clauses to ease debugging.

##### Example

```sql
CREATE TABLE staff (
    PRIMARY KEY (staffNum),
    staffNum INT(5) NOT NULL,
    firstName VARCHAR(100) NOT NULL,
    pensInDrawer INT(2) NOT NULL,
      CONSTRAINT pensInDrawerRange
      CHECK(pensInDrawer >= 1 AND pensInDrawer < 100)
);
```

### Designs to avoid

* Object oriented design principles do not effectively translate to relational
  database designs—avoid this pitfall.
* Placing the value in one column and the units in another column. The column
  should make the units self evident to prevent the requirement to combine
  columns again later in the application. Use `CHECK()` to ensure valid data is
  inserted into the column.
* [EAV (Entity Attribute Value)][eav] tables—use a specialist product intended for
  handling such schema-less data instead.
* Splitting up data that should be in one table across many because of arbitrary
  concerns such as time-based archiving or location in a multi-national
  organisation. Later queries must then work across multiple tables with `UNION`
  rather than just simply querying one table.


## Appendix

### Reserved keyword reference

A list of ANSI SQL (92, 99 and 2003), MySQL 3 to 5.x, PostgreSQL 8.1, MS SQL Server 2000, MS ODBC and Oracle 10.2 reserved keywords.

```sql
A
ABORT
ABS
ABSOLUTE
ACCESS
ACTION
ADA
ADD
ADMIN
AFTER
AGGREGATE
ALIAS
ALL
ALLOCATE
ALSO
ALTER
ALWAYS
ANALYSE
ANALYZE
AND
ANY
ARE
ARRAY
AS
ASC
ASENSITIVE
ASSERTION
ASSIGNMENT
ASYMMETRIC
AT
ATOMIC
ATTRIBUTE
ATTRIBUTES
AUDIT
AUTHORIZATION
AUTO_INCREMENT
AVG
AVG_ROW_LENGTH
BACKUP
BACKWARD
BEFORE
BEGIN
BERNOULLI
BETWEEN
BIGINT
BINARY
BIT
BIT_LENGTH
BITVAR
BLOB
BOOL
BOOLEAN
BOTH
BREADTH
BREAK
BROWSE
BULK
BY
C
CACHE
CALL
CALLED
CARDINALITY
CASCADE
CASCADED
CASE
CAST
CATALOG
CATALOG_NAME
CEIL
CEILING
CHAIN
CHANGE
CHAR
CHAR_LENGTH
CHARACTER
CHARACTER_LENGTH
CHARACTER_SET_CATALOG
CHARACTER_SET_NAME
CHARACTER_SET_SCHEMA
CHARACTERISTICS
CHARACTERS
CHECK
CHECKED
CHECKPOINT
CHECKSUM
CLASS
CLASS_ORIGIN
CLOB
CLOSE
CLUSTER
CLUSTERED
COALESCE
COBOL
COLLATE
COLLATION
COLLATION_CATALOG
COLLATION_NAME
COLLATION_SCHEMA
COLLECT
COLUMN
COLUMN_NAME
COLUMNS
COMMAND_FUNCTION
COMMAND_FUNCTION_CODE
COMMENT
COMMIT
COMMITTED
COMPLETION
COMPRESS
COMPUTE
CONDITION
CONDITION_NUMBER
CONNECT
CONNECTION
CONNECTION_NAME
CONSTRAINT
CONSTRAINT_CATALOG
CONSTRAINT_NAME
CONSTRAINT_SCHEMA
CONSTRAINTS
CONSTRUCTOR
CONTAINS
CONTAINSTABLE
CONTINUE
CONVERSION
CONVERT
COPY
CORR
CORRESPONDING
COUNT
COVAR_POP
COVAR_SAMP
CREATE
CREATEDB
CREATEROLE
CREATEUSER
CROSS
CSV
CUBE
CUME_DIST
CURRENT
CURRENT_DATE
CURRENT_DEFAULT_TRANSFORM_GROUP
CURRENT_PATH
CURRENT_ROLE
CURRENT_TIME
CURRENT_TIMESTAMP
CURRENT_TRANSFORM_GROUP_FOR_TYPE
CURRENT_USER
CURSOR
CURSOR_NAME
CYCLE
DATA
DATABASE
DATABASES
DATE
DATETIME
DATETIME_INTERVAL_CODE
DATETIME_INTERVAL_PRECISION
DAY
DAY_HOUR
DAY_MICROSECOND
DAY_MINUTE
DAY_SECOND
DAYOFMONTH
DAYOFWEEK
DAYOFYEAR
DBCC
DEALLOCATE
DEC
DECIMAL
DECLARE
DEFAULT
DEFAULTS
DEFERRABLE
DEFERRED
DEFINED
DEFINER
DEGREE
DELAY_KEY_WRITE
DELAYED
DELETE
DELETED
DELIMITER
DELIMITERS
DENSE_RANK
DENY
DEPTH
DEREF
DERIVED
DESC
DESCRIBE
DESCRIPTOR
DESTROY
DESTRUCTOR
DETERMINISTIC
DIAGNOSTICS
DICTIONARY
DISABLE
DISCONNECT
DISK
DISPATCH
DISTINCT
DISTINCTROW
DISTRIBUTED
DIV
DO
DOMAIN
DOUBLE
DROP
DUAL
DUMMY
DUMP
DYNAMIC
DYNAMIC_FUNCTION
DYNAMIC_FUNCTION_CODE
EACH
ELEMENT
ELSE
ELSEIF
ENABLE
ENCLOSED
ENCODING
ENCRYPTED
END
END-EXEC
ENUM
EQUALS
ERRLVL
ESCAPE
ESCAPED
EVERY
EXCEPT
EXCEPTION
EXCLUDE
EXCLUDING
EXCLUSIVE
EXEC
EXECUTE
EXISTING
EXISTS
EXIT
EXP
EXPLAIN
EXTERNAL
EXTRACT
FALSE
FETCH
FIELDS
FILE
FILLFACTOR
FILTER
FINAL
FIRST
FLOAT
FLOAT4
FLOAT8
FLOOR
FLUSH
FOLLOWING
FOR
FORCE
FOREIGN
FORTRAN
FORWARD
FOUND
FREE
FREETEXT
FREETEXTTABLE
FREEZE
FROM
FULL
FULLTEXT
FUNCTION
FUSION
G
GENERAL
GENERATED
GET
GLOBAL
GO
GOTO
GRANT
GRANTED
GRANTS
GREATEST
GROUP
GROUPING
HANDLER
HAVING
HEADER
HEAP
HIERARCHY
HIGH_PRIORITY
HOLD
HOLDLOCK
HOST
HOSTS
HOUR
HOUR_MICROSECOND
HOUR_MINUTE
HOUR_SECOND
IDENTIFIED
IDENTITY
IDENTITY_INSERT
IDENTITYCOL
IF
IGNORE
ILIKE
IMMEDIATE
IMMUTABLE
IMPLEMENTATION
IMPLICIT
IN
INCLUDE
INCLUDING
INCREMENT
INDEX
INDICATOR
INFILE
INFIX
INHERIT
INHERITS
INITIAL
INITIALIZE
INITIALLY
INNER
INOUT
INPUT
INSENSITIVE
INSERT
INSERTED
INSERT_ID
INSTANCE
INSTANTIABLE
INSTEAD
INT
INT1
INT2
INT3
INT4
INT8
INTEGER
INTERSECT
INTERSECTION
INTERVAL
INTO
INVOKER
IS
ISAM
ISNULL
ISOLATION
ITERATE
JOIN
K
KEY
KEY_MEMBER
KEY_TYPE
KEYS
KILL
LANCOMPILER
LANGUAGE
LARGE
LAST
LAST_INSERT_ID
LATERAL
LEADING
LEAST
LEAVE
LEFT
LENGTH
LESS
LEVEL
LIKE
LIMIT
LINENO
LINES
LISTEN
LN
LOAD
LOCAL
LOCALTIME
LOCALTIMESTAMP
LOCATION
LOCATOR
LOCK
LOGIN
LOGS
LONG
LONGBLOB
LONGTEXT
LOOP
LOW_PRIORITY
LOWER
M
MAP
MATCH
MATCHED
MAX
MAX_ROWS
MAXEXTENTS
MAXVALUE
MEDIUMBLOB
MEDIUMINT
MEDIUMTEXT
MEMBER
MERGE
MESSAGE_LENGTH
MESSAGE_OCTET_LENGTH
MESSAGE_TEXT
METHOD
MIDDLEINT
MIN
MIN_ROWS
MINUS
MINUTE
MINUTE_MICROSECOND
MINUTE_SECOND
MINVALUE
MLSLABEL
MOD
MODE
MODIFIES
MODIFY
MODULE
MONTH
MONTHNAME
MORE
MOVE
MULTISET
MUMPS
MYISAM
NAME
NAMES
NATIONAL
NATURAL
NCHAR
NCLOB
NESTING
NEW
NEXT
NO
NO_WRITE_TO_BINLOG
NOAUDIT
NOCHECK
NOCOMPRESS
NOCREATEDB
NOCREATEROLE
NOCREATEUSER
NOINHERIT
NOLOGIN
NONCLUSTERED
NONE
NORMALIZE
NORMALIZED
NOSUPERUSER
NOT
NOTHING
NOTIFY
NOTNULL
NOWAIT
NULL
NULLABLE
NULLIF
NULLS
NUMBER
NUMERIC
OBJECT
OCTET_LENGTH
OCTETS
OF
OFF
OFFLINE
OFFSET
OFFSETS
OIDS
OLD
ON
ONLINE
ONLY
OPEN
OPENDATASOURCE
OPENQUERY
OPENROWSET
OPENXML
OPERATION
OPERATOR
OPTIMIZE
OPTION
OPTIONALLY
OPTIONS
OR
ORDER
ORDERING
ORDINALITY
OTHERS
OUT
OUTER
OUTFILE
OUTPUT
OVER
OVERLAPS
OVERLAY
OVERRIDING
OWNER
PACK_KEYS
PAD
PARAMETER
PARAMETER_MODE
PARAMETER_NAME
PARAMETER_ORDINAL_POSITION
PARAMETER_SPECIFIC_CATALOG
PARAMETER_SPECIFIC_NAME
PARAMETER_SPECIFIC_SCHEMA
PARAMETERS
PARTIAL
PARTITION
PASCAL
PASSWORD
PATH
PCTFREE
PERCENT
PERCENT_RANK
PERCENTILE_CONT
PERCENTILE_DISC
PLACING
PLAN
PLI
POSITION
POSTFIX
POWER
PRECEDING
PRECISION
PREFIX
PREORDER
PREPARE
PREPARED
PRESERVE
PRIMARY
PRINT
PRIOR
PRIVILEGES
PROC
PROCEDURAL
PROCEDURE
PROCESS
PROCESSLIST
PUBLIC
PURGE
QUOTE
RAID0
RAISERROR
RANGE
RANK
RAW
READ
READS
READTEXT
REAL
RECHECK
RECONFIGURE
RECURSIVE
REF
REFERENCES
REFERENCING
REGEXP
REGR_AVGX
REGR_AVGY
REGR_COUNT
REGR_INTERCEPT
REGR_R2
REGR_SLOPE
REGR_SXX
REGR_SXY
REGR_SYY
REINDEX
RELATIVE
RELEASE
RELOAD
RENAME
REPEAT
REPEATABLE
REPLACE
REPLICATION
REQUIRE
RESET
RESIGNAL
RESOURCE
RESTART
RESTORE
RESTRICT
RESULT
RETURN
RETURNED_CARDINALITY
RETURNED_LENGTH
RETURNED_OCTET_LENGTH
RETURNED_SQLSTATE
RETURNS
REVOKE
RIGHT
RLIKE
ROLE
ROLLBACK
ROLLUP
ROUTINE
ROUTINE_CATALOG
ROUTINE_NAME
ROUTINE_SCHEMA
ROW
ROW_COUNT
ROW_NUMBER
ROWCOUNT
ROWGUIDCOL
ROWID
ROWNUM
ROWS
RULE
SAVE
SAVEPOINT
SCALE
SCHEMA
SCHEMA_NAME
SCHEMAS
SCOPE
SCOPE_CATALOG
SCOPE_NAME
SCOPE_SCHEMA
SCROLL
SEARCH
SECOND
SECOND_MICROSECOND
SECTION
SECURITY
SELECT
SELF
SENSITIVE
SEPARATOR
SEQUENCE
SERIALIZABLE
SERVER_NAME
SESSION
SESSION_USER
SET
SETOF
SETS
SETUSER
SHARE
SHOW
SHUTDOWN
SIGNAL
SIMILAR
SIMPLE
SIZE
SMALLINT
SOME
SONAME
SOURCE
SPACE
SPATIAL
SPECIFIC
SPECIFIC_NAME
SPECIFICTYPE
SQL
SQL_BIG_RESULT
SQL_BIG_SELECTS
SQL_BIG_TABLES
SQL_CALC_FOUND_ROWS
SQL_LOG_OFF
SQL_LOG_UPDATE
SQL_LOW_PRIORITY_UPDATES
SQL_SELECT_LIMIT
SQL_SMALL_RESULT
SQL_WARNINGS
SQLCA
SQLCODE
SQLERROR
SQLEXCEPTION
SQLSTATE
SQLWARNING
SQRT
SSL
STABLE
START
STARTING
STATE
STATEMENT
STATIC
STATISTICS
STATUS
STDDEV_POP
STDDEV_SAMP
STDIN
STDOUT
STORAGE
STRAIGHT_JOIN
STRICT
STRING
STRUCTURE
STYLE
SUBCLASS_ORIGIN
SUBLIST
SUBMULTISET
SUBSTRING
SUCCESSFUL
SUM
SUPERUSER
SYMMETRIC
SYNONYM
SYSDATE
SYSID
SYSTEM
SYSTEM_USER
TABLE
TABLE_NAME
TABLES
TABLESAMPLE
TABLESPACE
TEMP
TEMPLATE
TEMPORARY
TERMINATE
TERMINATED
TEXT
TEXTSIZE
THAN
THEN
TIES
TIME
TIMESTAMP
TIMEZONE_HOUR
TIMEZONE_MINUTE
TINYBLOB
TINYINT
TINYTEXT
TO
TOAST
TOP
TOP_LEVEL_COUNT
TRAILING
TRAN
TRANSACTION
TRANSACTION_ACTIVE
TRANSACTIONS_COMMITTED
TRANSACTIONS_ROLLED_BACK
TRANSFORM
TRANSFORMS
TRANSLATE
TRANSLATION
TREAT
TRIGGER
TRIGGER_CATALOG
TRIGGER_NAME
TRIGGER_SCHEMA
TRIM
TRUE
TRUNCATE
TRUSTED
TSEQUAL
TYPE
UESCAPE
UID
UNBOUNDED
UNCOMMITTED
UNDER
UNDO
UNENCRYPTED
UNION
UNIQUE
UNKNOWN
UNLISTEN
UNLOCK
UNNAMED
UNNEST
UNSIGNED
UNTIL
UPDATE
UPDATETEXT
UPPER
USAGE
USE
USER
USER_DEFINED_TYPE_CATALOG
USER_DEFINED_TYPE_CODE
USER_DEFINED_TYPE_NAME
USER_DEFINED_TYPE_SCHEMA
USING
UTC_DATE
UTC_TIME
UTC_TIMESTAMP
VACUUM
VALID
VALIDATE
VALIDATOR
VALUE
VALUES
VAR_POP
VAR_SAMP
VARBINARY
VARCHAR
VARCHAR2
VARCHARACTER
VARIABLE
VARIABLES
VARYING
VERBOSE
VIEW
VOLATILE
WAITFOR
WHEN
WHENEVER
WHERE
WHILE
WIDTH_BUCKET
WINDOW
WITH
WITHIN
WITHOUT
WORK
WRITE
WRITETEXT
X509
XOR
YEAR
YEAR_MONTH
ZEROFILL
ZONE
```

[simon]: https://www.simonholywell.com/?utm_source=sqlstyle.guide&utm_medium=link&utm_campaign=md-document
    "SimonHolywell.com"
[issue]: https://github.com/treffynnon/sqlstyle.guide/issues
    "SQL style guide issues on GitHub"
[fork]: https://github.com/treffynnon/sqlstyle.guide/fork
    "Fork SQL style guide on GitHub"
[pull]: https://github.com/treffynnon/sqlstyle.guide/pulls/
    "SQL style guide pull requests on GitHub"
[celko]: http://www.amazon.com/gp/product/0120887975/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0120887975&linkCode=as2&tag=fuph-20&linkId=7PF4X6KIAMWYYT7Z
    "Joe Celko's SQL Programming Style (The Morgan Kaufmann Series in Data Management Systems)"
[dl-md]: https://raw.githubusercontent.com/treffynnon/sqlstyle.guide/gh-pages/_includes/sqlstyle.guide.md
    "Download the guide in Markdown format"
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
    "Wikipedia: ISO 8601"
[rivers]: http://practicaltypography.com/one-space-between-sentences.html
    "Practical Typography: one space between sentences"
[reserved-keywords]: #reserved-keyword-reference
    "Reserved keyword reference"
[eav]: https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model
    "Wikipedia: Entity–attribute–value model"
[self]: http://www.sqlstyle.guide
    "SQL style guide by Simon Holywell"
[licence]: http://creativecommons.org/licenses/by-sa/4.0/
    "Creative Commons Attribution-ShareAlike 4.0 International License"
