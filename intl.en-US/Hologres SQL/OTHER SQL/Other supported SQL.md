# Other supported SQL

This topic describes other PostgreSQL statements supported by the current Hologres version.

The statements supported by Hologres are a subset of PostgreSQL statements. The following table lists the PostgreSQL statements supported by Hologres currently.

|PostgreSQL statement|Description|
|--------------------|-----------|
|ALTER ROLE|None|
|ANALYZE|None|
|BEGIN|This statement can only be used together with DDL statements.|
|COMMIT|This statement can only be used together with DDL statements.|
|CREATE DATABASE|None|
|CREATE EXTENSION|None|
|CREATE FOREIGN DATA WRAPPER|None|
|CREATE FOREIGN TABLE|This statement is supported only for MaxCompute.|
|CREATE GROUP|None|
|CREATE SERVER|None|
|CREATE TABLE|Only certain features of this statement are supported. For example, only list partitioning is supported, and the partition list can have only one value of the string type. The following parameters are not supported: UNLOGGED

TEMP

IF NOT EXISTS

LIKE

CHECK

DEFAULT

GENERATED

UNIQUE

EXCLUDE

FOREIGN KEY

DEFERRABLE

WITH OIDS

GLOBAL and LOCAL |
|CREATE USER|None|
|CREATE USER MAPPING|None|
|DROP DATABASE|None|
|DROP FOREIGN DATA WRAPPER|None|
|DROP FOREIGN TABLE|None|
|DROP GROUP|None|
|DROP OWNED|None|
|DROP POLICY|None|
|DROP ROLE|None|
|DROP SERVER|None|
|DROP TABLE|None|
|DROP USER|None|
|DROP USER MAPPING|None|
|END|This statement can only be used together with DDL statements.|
|EXPLAIN|None|
|INSERT|None|
|ROLLBACK|None|
|SELECT|Only certain features of this statement are supported. The following parameters are not supported: CUBE, GROUPING SETS, and ROLLUP

WITH RECURSIVE

NULLS FIRST and NULLS LAST

INTERSECT and EXCEPT

TABLESAMPLE

Locking

ONLY |
|SET|None|
|SET ROLE|None|
|START TRANSACTION|None|

