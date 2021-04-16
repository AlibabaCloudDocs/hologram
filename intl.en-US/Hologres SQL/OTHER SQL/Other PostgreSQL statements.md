---
keyword: [Hologres, PostgreSQL statements]
---

# Other PostgreSQL statements

Hologres is compatible with PostgreSQL. Apart from the PostgreSQL statements that are used to create, query, and update tables and schemas, Hologres also includes further support for other PostgreSQL statements. This topic describes these PostgreSQL statements.

The following table describes the PostgreSQL statements that are supported by Hologres. For more information, see [PostgreSQL official documentation](https://www.postgresql.org/docs/11/index.html).

|PostgreSQL statement|Description|
|--------------------|-----------|
|ALTER TABLE|Hologres supports only the following commands of the `ALTER TABLE` statement:-   TABLE RENAME
-   SET STATISTICS
-   CHANGE OWNER
-   ATTACH PARTITION
-   DETACH PARTITION |
|ALTER ROLE|N/A|
|ANALYZE|N/A|
|BEGIN|The `BEGIN` statement requires a data definition language \(DDL\) statement.|
|COMMIT|The `COMMIT` statement requires a DDL statement.|
|CREATE DATABASE|N/A|
|CREATE EXTENSION|N/A|
|CREATE FOREIGN DATA WRAPPER|N/A|
|CREATE FOREIGN TABLE|Hologres supports only foreign tables that are sourced from MaxCompute.|
|CREATE GROUP|N/A|
|CREATE SERVER|N/A|
|CREATE TABLE|Hologres does not support all the commands of the `CREATE TABLE` statement. The following commands are not supported:-   UNLOGGED
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

Hologres supports only list partitions. You can specify only a list of discrete values for the partition key in the description for each partition. |
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
|SELECT|Hologres does not support all the commands of the `SELECT` statement. The following commands are not supported:-   WITH RECURSIVE
-   TABLESAMPLE
-   LOCKING
-   ONLY |
|SET|Hologres supports SET commands. However, these commands do not apply and may be canceled.|
|SET ROLE|N/A|
|START TRANSACTION|N/A|

