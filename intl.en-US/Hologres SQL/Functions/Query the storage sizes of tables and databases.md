---
keyword: [Hologres, query storage size]
---

# Query the storage sizes of tables and databases

Hologres is compatible with PostgreSQL. You can use PostgreSQL to query the storage size of a table or database in Hologres. This topic describes how to use SQL statements to query the storage sizes of tables and databases.

## Usage notes

When you query the storage sizes of tables and databases in Hologres, take note of the following items:

-   Before you query the storage sizes of tables and databases, you must create a Hologres instance and connect to a development tool. For more information, see [Use HoloWeb to develop data in a Hologres instance]().
-   Only Hologres V0.9 and later allow you to query the storage sizes of objects. If the version of your Hologres instance is earlier than V0.9, [submit a ticket](https://workorder-intl.console.aliyun.com/) to update your instance.

## Query the storage size of a table

-   Usage notes

    When you query the storage size of a table, the table must be an internal table. You cannot directly query the storage size of a parent partitioned table. If you do so, the returned value is 0. Instead, you can query the storage size of a child partitioned table. If the storage size of the child partitioned table is zero, the returned value is empty.

-   Syntax

    ```
    select pg_relation_size('table_name'); // The unit of the returned value is byte.
    ```

-   Parameter description

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table whose storage size you want to query in the current database.|

-   Returned value: The unit of the returned value is byte. The returned value indicates the total size of the memory table and physical disk of the current table at the moment.

    If you need higher readability, use the pg\_size\_pretty function in the query statement. The following code snippet shows the syntax:

    ```
    select pg_size_pretty( pg_relation_size('table_name')); // The unit of the returned value may be KB or MB.
    ```


## Query the storage size of a database

-   Usage notes

    You can query the sizes of only the currently connected database and the internal tables in the database.

-   Syntax

    ```
    select pg_database_size(current_database()); // The unit of the returned value is byte.
    ```

-   Parameter description

    |Parameter|Description|
    |---------|-----------|
    |current\_database|The current database. You can directly execute the preceding statement to query the storage size of the current database without replacing parameters.|

-   Returned value: The unit of the returned value is byte. The returned value indicates the total size of all the Hologres tables and write-ahead logs in the current database.

    If you need higher readability, use the pg\_size\_pretty function in the query statement. The following code snippet shows the syntax:

    ```
    select pg_size_pretty(pg_database_size(current_database())); // The unit of the returned value may be KB or MB.
    ```


