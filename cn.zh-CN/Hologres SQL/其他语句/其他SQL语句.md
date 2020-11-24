---
keyword: [Hologres, SQL语句]
---

# 其他SQL语句

交互式分析Hologres兼容PostgreSQL，除了支持创建、查询及更新数据库的表和Schema等对象的SQL语句外，同时还支持其他SQL语句。本文为您介绍Hologres支持的其他SQL语句。

Hologres已支持的其他PostgreSQL功能语句如下表所示，您可以参考[PostgreSQL官网文档](https://www.postgresql.org/docs/11/index.html)的用法示例进行使用。

|命令|说明|
|--|--|
|ALTER TABLE|Hologres仅支持Postgre中`ALTER TABLE`语句的如下功能：-   TABLE RENAME
-   SET STATISTICS
-   CHANGE OWNER
-   ATTACH PARTITION
-   DETACH PARTITION |
|ALTER ROLE|无|
|ANALYZE|无|
|BEGIN|Hologres的`BEGIN`仅对DDL语句生效。|
|COMMIT|Hologres的`COMMIT`仅对DDL语句生效。|
|CREATE DATABASE|无|
|CREATE EXTENSION|无|
|CREATE FOREIGN DATA WRAPPER|无|
|CREATE FOREIGN TABLE|Hologres仅支持创建MaxCompute外部表。|
|CREATE GROUP|无|
|CREATE SERVER|无|
|CREATE TABLE|Hologres仅支持PostgreSQL中`CREATE TABLE`语句的部分功能。其中，不支持的功能如下：-   UNLOGGED
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

Hologres的PARTITION仅支持LIST类型，并且PARTITION LIST只能取类型为STRING的唯一值。 |
|CREATE VIEW|无|
|CREATE USER|无|
|CREATE USER MAPPING|无|
|DROP DATABASE|无|
|DROP FOREIGN DATA WRAPPER|无|
|DROP FOREIGN TABLE|无|
|DROP GROUP|无|
|DROP OWNED|无|
|DROP POLICY|无|
|DROP ROLE|无|
|DROP SERVER|无|
|DROP TABLE|无|
|DROP USER|无|
|DROP USER MAPPING|无|
|END|Hologres的`END`仅支持与DDL语句配合使用。|
|EXPLAIN|无|
|INSERT|无|
|ROLLBACK|无|
|SELECT|Hologres仅支持PostgreSQL中`SELECT`语句的部分语句功能。其中，不支持的功能如下：-   CUBE SET UP
-   GROUPING SET UP
-   CUBE ROLL UP
-   GROUPING ROLL UP
-   递归查询
-   NULL FIRST或NULL LAST
-   INTERSECT或EXCEPT
-   TABLESAMPLE
-   LOCKING
-   ONLY |
|SET|Hologres中可能会出现部分Postgres的属性设置了，但是实际没有效果的现象。|
|SET ROLE|无|
|START TRANSACTION|无|

