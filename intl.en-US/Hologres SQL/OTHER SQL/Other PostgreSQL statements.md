---
keyword: [Hologres, SQL statements]
---

# Other PostgreSQL statements

Hologres supports the PostgreSQL statements. These statements can be used to create, query, and update tables and schemas. It also includes further support for other PostgreSQL statements. This topic describes these PostgreSQL statements.

The following table lists the PostgreSQL statements supported by Hologres. For more information, see [PostgreSQL 11.9 Documentation](https://www.postgresql.org/docs/11/index.html).

|PostgreSQL statement|Description|
|--------------------|-----------|
|ALTER TABLE|Hologres supports only the following commands of the `ALTER TABLE` statement:-   TABLE RENAME
-   SET STATISTICS
-   CHANGE OWNER
-   ATTACH PARTITION
-   DETACH PARTITION |
|ALTER ROLE|N/A|
|ANALYZE|N/A|
|BEGIN|The `BEGIN` statement requires a Data Definition Language \(DDL\) statement.|
|COMMIT|The `COMMIT` statement requires a DDL statement.|
|CREATE DATABASE|N/A|
|CREATE EXTENSION|N/A|
|CREATE FOREIGN DATA WRAPPER|N/A|
|CREATE FOREIGN TABLE|Hologres supports only foreign tables of MaxCompute.|
|CREATE GROUP|N/A|
|CREATE SERVER|N/A|
|CREATE TABLE|Hologres does not support the following commands of the `CREATE TABLE` statement: -   UNLOGGED
-   TEMP
-   IF NOT EXISTS
-   LIKE
-   CHECK
-   DEFAULT
-   GENERATED
-   UNIQUE
-   EXCLUDE
-   FOREIGN KEY
-   DEFERRABLE
-   WITH OIDS
-   GLOBAL
-   LOCAL

Hologres supports only list partitions. You can specify only a list of discrete values for the partitioning key in the description for each partition. |
|CREATE VIEW|N/A|
|CREATE USER|N/A|
|CREATE USER MAPPING|N/A|
|DROP DATABASE|N/A|
|DROP FOREIGN DATA WRAPPER|N/A|
|DROP FOREIGN TABLE|N/A|
|DROP GROUP|N/A|
|DROP OWNED|N/A|
|DROP POLICY|N/A|
|DROP ROLE|N/A|
|DROP SERVER|N/A|
|DROP TABLE|N/A|
|DROP USER|N/A|
|DROP USER MAPPING|N/A|
|END|The `END` statement requires a DDL statement.|
|EXPLAIN|N/A|
|INSERT|N/A|
|ROLLBACK|N/A|
|SELECT|Hologres does not support all the commands of the `SELECT` statement: The following commands are unsupported:-   CUBE SET UP
-   GROUPING SET UP
-   CUBE ROLL UP
-   GROUPING ROLL UP
-   WITH RECURSIVE
-   NULL FIRST and NULL LAST
-   INTERSECT and EXCEPT
-   TABLESAMPLE
-   LOCKING
-   ONLY |
|SET|Hologres supports SET commands. However, these commands do not apply and may be canceled.|
|SET ROLE|N/A|
|START TRANSACTION|N/A|

