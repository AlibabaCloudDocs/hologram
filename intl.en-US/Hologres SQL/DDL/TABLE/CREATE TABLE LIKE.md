---
keyword: [CREATE TABLE LIKE, Hologres, ]
---

# CREATE TABLE LIKE

You can execute the CREATE TABLE LIKE statement to create a table with the same schema as the table obtained by executing the specified SELECT statement. This topic describes how to execute the CREATE TABLE LIKE statement.

## Limits

The `CREATE TABLE LIKE` statement does not synchronize data from the source table to the created table.

## Create a standard table

1.  **Syntax**

    The following syntax applies when you execute the CREATE TABLE LIKE to create a standard table in Hologres:

    ```
    CALL hg_create_table_like('table_name', 'select_query');
    ```

    **Note:** After you execute the preceding SQL statement, Hologres creates a table with the same schema as the table that is obtained by executing the SQL statement specified by select\_query. The created table is named table\_name.

2.  **Parameters**
    -   table\_name: the name of the table to be created. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name.
    -   select\_query: the SQL statement that is used to query data. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name.
3.  **Examples**

    The following sample code shows you how to execute the CREATE TABLE LIKE statement to create a standard table in Hologres:

    1.  To create a table with the same schema as the table that is obtained by executing the specified SQL statement, execute the following SQL statements:

        ```
        CREATE TABLE src_table(a int, b text);
        CALL hg_create_table_like('new_table', 'select * from src_table');
        ```

        ![643](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5521194161/p91113.png)

    2.  To create a table that contains specific columns in addition to those in the table that is obtained by executing the specified SQL statement, execute the following SQL statements:

        ```
        CREATE TABLE test_table(a int, b text);
        
        CALL hg_create_table_like('holo_table', 'select *, 1 as c, ''a'' as d from test_table');
        ```

        ![644](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5521194161/p91114.png)


## Create a partitioned table

1.  **Syntax**

    The following syntax applies when you execute the CREATE TABLE LIKE statement to create a partitioned table in Hologres:

    ```
    CALL hg_create_table_like('table_name', 'select_query', 'partition_clause');
    ```

2.  **Parameters**
    -   table\_name: the name of the table to be created. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name.
    -   select\_query: the SQL statement that is used to query data. You must set this parameter to a fixed string but not a string of variables or a function that is used to generate a table name.
    -   partition\_clause: the clause that is used to partition the table.
3.  **Examples**

    The following sample code shows you how to execute the CREATE TABLE LIKE statement to create a partitioned table in Hologres:

    ```
    CREATE TABLE src_table(a int, b text);
    CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as d from src_table', 'partition by list(b)');
    
    CREATE TABLE new_table_child1 PARTITION OF new_table FOR VALUES IN(1);
    
    \d new_table
    
    \d new_table_child1 
    ```

    ![645](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5521194161/p91115.png)


## Set table properties

After you create a table by executing the CREATE TABLE LIKE statement, you can call the set\_table\_property function to set properties for the table in Hologres.

```
CREATE TABLE src_table(a int, b text);

BEGIN;
CALL hg_create_table_like('new_table', 'select *, 1 as c, ''a'' as d from src_table');
CALL set_table_property('new_table', 'distribution_key', 'b');
CALL set_table_property('new_table', 'orientation', 'column');
CALL set_table_property('new_table', 'bitmap_columns', 'b');
CALL set_table_property('new_table', 'dictionary_encoding_columns', 'b');
CALL set_table_property('new_table', 'time_to_live_in_seconds', '86400');
COMMIT;

\d new_table
```

![646](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5521194161/p91116.png)

## Limits

If you specify columns in the SELECT statement, you must specify a unique alias for each column. If you specify the same alias for different columns, the CREATE TABLE LIKE statement creates multiple columns with the same name for the table. As a result, an error message is returned. The following code provides an example:

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

![647](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5521194161/p91117.png)

