---
keyword: [Hologres, query storage size]
---

# Query the storage sizes of tables and databases

Hologres is compatible with PostgreSQL. You can use PostgreSQL functions to query the storage size of a table or database in Hologres. This topic shows you how to query the storage sizes of tables and databases by executing SQL statements.

## Usage notes

When you query the storage sizes of tables and databases in Hologres, take note of the following items:

-   Before you query the storage sizes of tables and databases, you must create a Hologres instance and connect the instance to a development tool. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).
-   Only Hologres V0.9 and later allow you to query the storage sizes of objects. If the version of your Hologres instance is earlier than V0.9, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.

## Query the storage size of a table

-   Description

    You can query the storage sizes of only internal tables. You cannot directly query the storage size of a parent partitioned table. If you do so, the returned value is 0. Instead, you can query the storage size of a child partitioned table. If the storage size of a table is zero, the returned value is empty.

-   Syntax

    ```
    select pg_relation_size('table_name'); // The unit of the returned value is byte.
    ```

-   Parameter description

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table whose storage size you want to query in the current database.|

-   Returned value: The unit of the returned value is byte. The returned value indicates the total size of the memory and physical disk space that the current table occupies at the moment.

    If you need higher readability, use the pg\_size\_pretty function in the query statement. The following code snippets shows the syntax:

    ```
    --Query the storage size of a single table in the current database.
    SELECT pg_size_pretty(pg_relation_size('table_name'));
    
    --Query the storage sizes of all the tables in the current database.
    SELECT table_schema || '.' || table_name AS table_full_name, pg_size_pretty(pg_relation_size('"' || table_schema || '"."' || table_name || '"')) AS table_size
    FROM information_schema.tables
    WHERE table_schema NOT IN ('pg_catalog','information_schema','hologres')
    ORDER BY table_size DESC;
    ```


## Query the storage size of a schema

-   Description

    You can query the total size of all the tables in a schema by executing an SQL statement.

-   Syntax

    ```
    SELECT table_schema, pg_size_pretty(SUM(pg_relation_size( table_schema  || '.' || table_name)::decimal)) AS schema_size
    FROM information_schema.tables 
    WHERE table_schema = 'schema_name'
    GROUP BY table_schema;
    ```

-   Parameter description

    |Parameter|Description|
    |---------|-----------|
    |schema\_name|The name of the schema where the current table resides.|

-   Returned value: The unit of the returned value is byte.

## Query the storage size of a database

-   Description

    You can query the size of the internal tables in the current database or a connected database.

-   Syntax

    ```
    select pg_database_size(current_database()); // The unit of the returned value is byte.
    ```

-   Parameter description

    |Parameter|Description|
    |---------|-----------|
    |current\_database|The current database. You can directly execute the preceding statement to query the storage size of the current database without the need to replace parameters.|

-   Returned value: The unit of the returned value is byte. The returned value indicates the total size of all the Hologres tables and write-ahead logs in the current database.

    If you need higher readability, use the pg\_size\_pretty function in the query statement. The following code snippet shows the syntax:

    ```
    select pg_size_pretty(pg_database_size(current_database())); // The unit of the returned value may be KB or MB.
    ```


