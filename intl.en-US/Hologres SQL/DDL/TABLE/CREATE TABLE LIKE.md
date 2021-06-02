---
keyword: [CREATE TABLE LIKE, Hologres, ]
---

# CREATE TABLE LIKE

You can execute the CREATE TABLE LIKE statement to create a table with the same schema as the table obtained by executing the specified SELECT statement. This topic describes how to execute the CREATE TABLE LIKE statement.

## Limits

-   In Hologres V0.9 and earlier, you can execute the `CREATE TABLE LIKE` statement to copy only table schemas, but not table properties such as the primary key and index. You can view the version of a Hologres instance on the instance details page in the Hologres console.
-   In Hologres V0.10 and later, you can execute the `CREATE TABLE LIKE` statement to copy both table schemas and table properties such as the primary key and index. To copy table properties from another table when you create a table, you must use the CREATE TABLE LIKE statement together with the `SELECT * FROM TABLE` statement. In addition, you must execute the following statement to allow the operation of copying table properties:

    ```
    set hg_experimental_enable_create_table_like_properties=true;
    ```

-   The `CREATE TABLE LIKE` statement does not synchronize data from the source table to the created table.
-   If you specify columns in the SELECT statement, you must specify a unique alias for each column. If you specify the same alias for different columns, the CREATE TABLE LIKE statement creates multiple columns with the same name for the table. As a result, an error message is returned. For example, an error message is returned if you use the following code:

    ```
    CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as c from src_table');
    ERROR:  column "c" specified more than once
    CONTEXT:  SQL statement "create table new_table (
    "a"     integer,
    "b"     text,
    "c"     integer,
    "c"     text
    );"
    PL/pgSQL function hg_create_table_like(text,text) line 22 at EXECUTE
    ```


## Create a standard table

1.  **Syntax**

    The `CREATE TABLE LIKE` statement uses the following syntax to create a standard table in Hologres:

    ```
    -- Create a standard table by copying the schemas, but not the properties, of another table.
    CALL hg_create_table_like('new_table_name', 'select_query');
    
    -- Create a standard table by copying both the schemas and properties of another table.
    set hg_experimental_enable_create_table_like_properties=true;-- Specify whether to allow the operation of copying table properties. The parameter setting applies only to the current session.
    CALL hg_create_table_like('new_table_name', 'select * from old_table_name');
    ```

    **Note:** After you call the hg\_create\_table\_like\(\) function, Hologres creates a table with the same schema as the table that is obtained by executing the SQL statement specified by select\_query. The created table is named new\_table\_name.

2.  **Parameters**
    -   new\_table\_name: the name of the table to be created. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name. You cannot create a foreign table by executing the CREATE TABLE LIKE statement.
    -   select\_query: the SQL statement that is used to query data. If you set the select\_query parameter to `select * from tablename`, the `CREATE TABLE LIKE` statement automatically copies all properties of the source table, including the primary key and index. If the query statement contains a lot of single quotation marks \('\), we recommend that you escape the query statement in the format of `$$query_sql$$`. This method can help simplify operations. The following sample code is for your reference:

        ```
        CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
        ```

        **Note:** HoloWeb does not support query statements escaped in the format of $$query\_sql$$. We recommend that you execute such statements by using development tools such as clients and Java Database Connectivity \(JDBC\).

    -   old\_table\_name: the name of the table to be copied.
3.  **Examples**

    For example, the source table is created in Hologres by executing the following statements:

    ```
    BEGIN;
    CREATE TABLE public.src_table (
     "a" int8 NOT NULL,
     "b" text NOT NULL,
    PRIMARY KEY (a)
    );
    CALL SET_TABLE_PROPERTY('public.src_table', 'orientation', 'column');
    CALL SET_TABLE_PROPERTY('public.src_table', 'bitmap_columns', 'b');
    CALL SET_TABLE_PROPERTY('public.src_table', 'dictionary_encoding_columns', 'b:auto');
    CALL SET_TABLE_PROPERTY('public.src_table', 'time_to_live_in_seconds', '3153600000');
    CALL SET_TABLE_PROPERTY('public.src_table', 'distribution_key', 'a');
    CALL SET_TABLE_PROPERTY('public.src_table', 'storage_format', 'segment');
    COMMIT;
    ```

    You can execute the `CREATE TABLE LIKE` statement by using one of the following methods to create a standard table in Hologres based on the source table:

    -   To create a table by copying both the schemas and properties of the source table, execute the following statements:

        ```
        -- Create a table by copying both the schemas and properties of the source table.
        set hg_experimental_enable_create_table_like_properties=true;
        CALL hg_create_table_like('new_table', 'select * from src_table');
        ```

    -   To create a table by adding a column to the schema of the source table, execute the following statement:

        ```
        -- Create a table by adding Column c to the schema of the source table. Column c has the same properties as Column b.
        CALL hg_create_table_like('holo_table_1', $$select *, "b" as c from src_table$$);
        ```


## Create a partitioned table

1.  **Syntax**

    The `CREATE TABLE LIKE` statement uses the following syntax to create a partitioned table in Hologres:

    ```
    -- Create a partitioned table by copying the schemas, but not the properties, of another table.
    CALL hg_create_table_like('new_table_name', 'select_query', 'partition_clause');
    ```

2.  **Parameters**
    -   new\_table\_name: the name of the table to be created. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name. You cannot create a foreign table by executing the CREATE TABLE LIKE statement.
    -   select\_query: the SQL statement that is used to query data. If you set the select\_query parameter to `select * from tablename`, the `CREATE TABLE LIKE` statement automatically copies all properties of the source table, including the primary key and index. If the query statement contains a lot of single quotation marks \('\), we recommend that you escape the query statement in the format of `$$query_sql$$`. This method can help simplify operations. The following sample code is for your reference:

        ```
        CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
        ```

        **Note:** HoloWeb does not support query statements escaped in the format of $$query\_sql$$. We recommend that you execute such statements by using development tools such as clients and JDBC.

    -   partition\_clause: the clause that is used to partition the table. This clause specifies the partition key. Hologres does not automatically create child partitioned tables. Therefore, you must manually create them.
3.  **Examples**

    For example, the source table is created in Hologres by executing the following statements:

    ```
    BEGIN;
    CREATE TABLE public.src_table (
     "a" int8 NOT NULL,
     "b" text NOT NULL,
    PRIMARY KEY (a)
    );
    CALL SET_TABLE_PROPERTY('public.src_table', 'orientation', 'column');
    CALL SET_TABLE_PROPERTY('public.src_table', 'bitmap_columns', 'b');
    CALL SET_TABLE_PROPERTY('public.src_table', 'dictionary_encoding_columns', 'b:auto');
    CALL SET_TABLE_PROPERTY('public.src_table', 'time_to_live_in_seconds', '3153600000');
    CALL SET_TABLE_PROPERTY('public.src_table', 'distribution_key', 'a');
    CALL SET_TABLE_PROPERTY('public.src_table', 'storage_format', 'segment');
    COMMIT;
    ```

    You can execute the `CREATE TABLE LIKE` statement by using one of the following methods to create a partitioned table in Hologres based on the source table:

    -   To create a partitioned table, execute the following statement:

        ```
        -- Create a partitioned table by adding Column ds to the schema of the source table. Set Column ds as the partition key.
        CALL hg_create_table_like('new_table', $$select *, ''b'' as ds from src_table$$, 'partition by list(ds)');
        ```

    -   To create child partitioned tables for a partitioned table, execute the following statements:

        ```
        create table new_table_child_20201213 partition of new_table for values in('20201213');-- Specify 20201213 as a partition key value.
        create table new_table_child_20201214 partition of new_table for values in('20201214');-- Specify 20201214 as a partition key value.
        ```


