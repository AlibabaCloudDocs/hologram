---
keyword: [CREATE TABLE LIKE, Hologres, ]
---

# CREATE TABLE LIKE

CREATE TABLE LIKE语句用于创建一个同Select Query结果相同的表。本文为您介绍CREATE TABLE LIKE的用法。

## 使用限制

-   Hologres V0.9及以下版本，`CREATE TABLE LIKE`语句仅支持复制表结构，不支持复制表属性（主键、索引等）。请在Hologres管理控制台的实例详情页查看当前版本。
-   Hologres V0.10版本开始，支持`CREATE TABLE LIKE`语句复制表结构和表属性（主键、索引等）。但仅限于`SELECT * FROM TABLE`语法。通过执行以下命令可以复制表属性：

    ```
    set hg_experimental_enable_create_table_like_properties=true;
    ```

-   使用`CREATE TABLE LIKE`创建的表不会自动同步源表的数据。
-   查询语句中的每一个目标列都要有一个不重复的别名，否则建表语句就会生成同列名的语句导致执行报错。示例如下：

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


## 普通表

1.  **命令格式**

    在Hologres中，普通表`CREATE TABLE LIKE`的命令格式如下：

    ```
    --复制一张普通表，但不能复制表属性
    CALL hg_create_table_like('new_table_name', 'select_query');
    
    --复制一张表并复制表属性
    set hg_experimental_enable_create_table_like_properties=true;--开关参数，session级别
    CALL hg_create_table_like('new_table_name', 'select * from old_table_name');
    ```

    **说明：** 通过调用hg\_create\_table\_like，系统会根据selec\_query结果的schema创建一个表名为table\_name的表，但不会插入任何数据。

2.  **参数说明**
    -   new\_table\_name：要创建表的表名（不支持创建外部表），只支持固定字符串，不支持字符拼接或函数生成等。
    -   select\_query：查询的SQL语句串。当SQL中的内容完全为`select * from tablename`时，`CREATE TABLE LIKE`会自动同步创建原表的所有属性，包括pk、索引等。如果SQL语句中有较多单引号，可以将$$符号置于SQL语句前后，通过`$$query_sql$$`改写（推荐使用该用法，操作更简便）自动实现单引号转义，用法如下：

        ```
        CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
        ```

        **说明：** 该种用法Holoweb中的query查询窗口暂不支持，请使用终端或者JDBC等开发工具。

    -   old\_table\_name：需要复制的原表名。
3.  **使用示例**

    如在Hologres中存在如下源表：

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

    在Hologres中`CREATE TABLE LIKE`的示例用法如下：

    -   创建一个同源表结构和属性相同的表

        ```
        --复制表及属性
        set hg_experimental_enable_create_table_like_properties=true;
        CALL hg_create_table_like('new_table', 'select * from src_table');
        ```

    -   创建一个在源表的基础上再增加字段的表

        ```
        --新增一个同b一样的字段c
        CALL hg_create_table_like('holo_table_1', $$select *, "b" as c from src_table$$);
        ```


## 分区表

1.  **命令格式**

    在Hologres中，分区表的`CREATE TABLE LIKE`的命令格式如下：

    ```
    --复制一张分区表，但不能复制表属性
    CALL hg_create_table_like('new_table_name', 'select_query', 'partition_clause');
    ```

2.  **参数说明**
    -   new\_table\_name：要创建表的表名（不支持创建外部表），只支持固定字符串，不支持字符拼接或函数生成等。
    -   select\_query：查询的SQL语句串。当SQL中的内容完全为`select * from tablename`时，`CREATE TABLE LIKE`会自动同步创建原表的所有属性，包括pk、索引等。如果SQL语句中有较多单引号，可以将$$符号置于SQL语句前后，通过`$$query_sql$$`改写（推荐使用该用法，操作更简便）自动实现单引号转义，用法如下：

        ```
        CALL HG_CREATE_TABLE_LIKE ('table_name', $$query_sql$$ [, 'partition_clause'])
        ```

        **说明：** 该种用法Holoweb中的query查询窗口暂不支持，请使用终端或者JDBC等开发工具。

    -   partition\_clause：分区相关的语法定义。用于指定分区键，不会自动创建分区子表，需要手动建分区子表。
3.  **使用示例**

    如在Hologres中存在如下源表：

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

    在Hologres中使用`CREATE TABLE LIKE`创建一张分区表示例如下：

    -   创建一个分区表

        ```
        --新增一个字段ds，并将表设置为以ds为分区的分区表
        CALL hg_create_table_like('new_table', $$select *, ''b'' as ds from src_table$$, 'partition by list(ds)');
        ```

    -   给对应的分区表创建分区子表

        ```
        create table new_table_child_20201213 partition of new_table for values in('20201213');--以20201213为分区值
        create table new_table_child_20201214 partition of new_table for values in('20201214');--以20201214为分区值
        ```


