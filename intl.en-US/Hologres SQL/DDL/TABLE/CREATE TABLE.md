---
keyword: [create a table, Hologres, CREATE TABLE statement]
---

# CREATE TABLE

You can execute the CREATE TABLE statement to create a table. This topic describes how to execute the CREATE TABLE statement in Hologres.

## Create a table

1.  **Syntax**

    The CREATE TABLE statement in Hologres only supports specific features of the CREATE TABLE statement in PostgreSQL. The CREATE TABLE statement in Hologres uses the following syntax:

    ```
    begin;
    create table [if not exists] [schema_name.]table_name ([
      {
       column_name column_type [column_constraints, [...]]
       | table_constraints
       [, ...]
      }
    ]);
    call set_table_property('<table_name>', property, value);
    commit;
    ```

2.  **Parameters**
    1.  The column\_type parameter specifies the data type of a column to be created in the new table. For more information about the data types that are supported by Hologres, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).
    2.  The following table describes whether the specific parameters are supported as `column constraints` or `table constraints`.

        |Parameter|Supported as a column constraint|Supported as a table constraint|
        |---------|--------------------------------|-------------------------------|
        |primary key|Yes|Yes|
        |not null|Yes|-|
        |null|Yes|-|
        |unique|No|No|
        |check|No|No|
        |default|No|No|

    3.  You can call the set\_table\_property function to set properties for the table. For more information, see [Set table properties](#section_l9q_k83_z01).
3.  **Limits**
    1.  When you configure the primary key of a table, you can specify multiple columns to constitute a composite primary key. The columns that constitute the primary key must be unique and cannot be null. In addition, you must specify all these columns in one CREATE TABLE statement. The primary key does not support columns of the following data types: FLOAT, DOUBLE, NUMERIC, ARRAY, JSON, and specific complex data types. The following sample code shows you how to specify the id and ds columns to constitute the primary key of a table:

        ```
        BEGIN;
        CREATE TABLE public.test (
         "id" text NOT NULL,
         "ds" text NOT NULL,
        PRIMARY KEY (id,ds)
        );
        CALL SET_TABLE_PROPERTY('public.test', 'orientation', 'column');
        COMMIT;
        ```

    2.  Table names and column names are not case-sensitive. To define an uppercase table or column name or a table or column name with special characters, you can enclose the name in double quotation marks \(" "\) to escape it. Sample code:

        ```
        create table "TBL"(a int);
        select relname from pg_class where relname = 'TBL';
        insert into "TBL" values (-1977);
        select * from "TBL";
        ------------------------------------------------------------------
        begin;
        create table tbl ("C1" int not null);
        call set_table_property('tbl', 'clustering_key', '"C1"');
        commit;
        ------------------------------------------------------------------
        begin;
        create table tbl ("C1" int not null, c2 text);
        call set_table_property('tbl', 'clustering_key', '"C1,c2:desc"');  -- For more information about the set_table_property function, see the following text.
        call set_table_property('tbl', 'segment_key', '"C1",c2:desc');
        commit;
        ------------------------------------------------------------------
        create table "Tab_$A%*" (a int);
        select relname from pg_class where relname = 'Tab_$A%*';
        insert into "Tab_$A%*" values (-1977);
        select * from "Tab_$A%*";
        ```

    3.  `IF NOT EXISTS`: When you create a table, if no table with the same name exists and the semantics is correct, the table can be created. If the `IF NOT EXISTS` option is not specified and a table with the same name exists, an error is returned. If the `IF NOT EXISTS` option is specified, Hologres displays a notice, skips the table creation step, and returns a success response. The following table describes the rules.

        |Situation|Response of the situation with IF NOT EXISTS specified|Response of the situation without IF NOT EXISTS specified|
        |---------|------------------------------------------------------|---------------------------------------------------------|
        |A table with the same name exists.|`NOTICE: relation "xx"already exists, skippingSUCCEED`|`ERROR: relation is already exists.`|
        |No table with the same name exists.|`SUCCEED`|`SUCCEED`|

    4.  A table name cannot exceed 64 bytes in length. Otherwise, the table name is truncated.
4.  **Examples**

    To create a standard table that contains a primary key, execute the following SQL statement:

    ```
    CREATE TABLE test (
     "id" bigint NOT NULL,
     "name" text NOT NULL,
     "age" bigint,
     "class" text NOT NULL,
    PRIMARY KEY (id)
    );
    ```


## Set table properties

Hologres allows you to call the set\_table\_property function to set table properties. Proper settings of table properties help Hologres efficiently sort and query data. When you execute the CREATE TABLE statement to create a table, the parameters related to the data storage layout of the table must be executed at the same time. In the current Hologres version, the orientation, distribution\_key, clustering\_key, and event\_time\_column parameters cannot be modified after a table is created.

1.  **Syntax**

    ```
    call set_table_property('<table_name>', property, value)
    ```

    **Note:** Generally, you must call the `set_table_property` function in the same transaction as the `CREATE TABLE` statement.

    In the current Hologres version, you can execute one of the following SQL statements to set table properties:

    ```
    call set_table_property('table_name', 'orientation', '[column | row]'); 
    call set_table_property('table_name', 'distribution_key', '[columnName[,...]]');
    call set_table_property('table_name', 'clustering_key', '[columnName{:[desc|asc]} [,...]]'); 
    call set_table_property('table_name', 'event_time_column', '[columnName [,...]]');
    call set_table_property('table_name', 'bitmap_columns', '[columnName [,...]]');
    call set_table_property('table_name', 'dictionary_encoding_columns', '[columnName [,...]]');
    call set_table_property('table_name', 'time_to_live_in_seconds', '<non_negative_literal>');
    ```

2.  **Parameters**

    The following table describes the parameters and their settings in column-oriented tables and row-oriented tables.

    |Parameter|Column-oriented table|Row-oriented table|
    |---------|---------------------|------------------|
    |orientation|Default value: column.|row|
    |distribution\_key|By default, the primary key is used as the distribution key. We recommend that you use the primary key.|By default, the primary key is used as the distribution key. We recommend that you use the primary key.|
    |clustering\_key|By default, this parameter is empty.|By default, the primary key is used as the clustering key. We recommend that you use the primary key.|
    |event\_time\_column|By default, the first non-null timestamp field is used as the event time column.|You cannot specify the primary key as the event time column.|
    |bitmap\_columns|Set this parameter based as needed.|Not supported|
    |dictionary\_encoding\_columns|Set this parameter based as needed.|Not supported|
    |time\_to\_live\_in\_seconds|Set this parameter based as needed.|Set this parameter based as needed.|

    1.  **orientation**

        ```
        call set_table_property('<table_name>', 'orientation', '[column | row]');
        ```

        -   The orientation property specifies whether the database table uses the column-oriented or row-oriented storage model in Hologres. The current Hologres version supports only one data storage model.
        -   By default, database tables use the column-oriented storage model in Hologres. **The column-oriented storage model is more applicable to online analytical processing \(OLAP\) scenarios and supports complex queries. The row-oriented storage model is more applicable to scenarios where key-value pairs are used for queries and supports point queries and scans based on primary keys.**
        -   Examples

            ```
            // Create a row-oriented table.
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'orientation', 'row');
            commit;
            
            // Create a column-oriented table.
            begin;
            create table tb2 (a int not null, b text not null);
            call set_table_property('tb2', 'orientation', 'column');
            commit;
            ```

    2.  **distribution\_key**

        ```
        call set_table_property('<table_name>', 'distribution_key', '[columnName[,...]]') ;
        ```

        -   The distribution\_key property specifies one or more columns that constitute a distribution key, which is used to distribute data in a table. Hologres distributes data to each shard based on the distribution key. Entries with the same distribution key value are distributed to the same shard.
        -   If you specify a single value for columnName, no extra spaces are allowed. If you specify multiple values for columnName, separate them with commas \(,\) and do not include extra spaces.
        -   The distribution key does not support columns of the following data types: FLOAT, DOUBLE, NUMERIC, ARRAY, JSON, and specific complex data types.
        -   If a table has a primary key, the primary key is used as the distribution key by default. The distribution key must consist of one or more columns that constitute the primary key and cannot be null. Entries with the same distribution key value must be distributed to the same shard. If no primary key is specified for the table, the distribution key can be null. In other words, you do not need to specify a column as the distribution key. If the distribution key is null, data can be randomly distributed to different shards. If a distribution key value is null, the value is regarded as an empty string.
        -   By default, Hologres randomly distributes data in a table to each shard. If you specify a column as the distribution key, data is distributed to each shard based on the column. Entries with the same distribution key value are distributed to the same shard. If you use the column as a filter condition, Hologres directly scans data in the shards that meet the filter condition. If you use the column as a join condition, Hologres directly joins data on the current node, without the need to distribute data to other compute nodes. This significantly improves the join efficiency.
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'distribution_key', 'a');
            commit;
            
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'distribution_key', 'a,b');
            commit;
            
            begin;
            create table tbl1(a int not null, b text not null);
            call set_table_property('tbl1', 'distribution_key', 'a');
            create table tbl2(c int not null, d text not null);
            call set_table_property('tbl2', 'distribution_key', 'c');
            commit;
            
            select b, count(*) from tbl1 join tbl2 on tbl1.a = tbl2.c group by b;
            ```

    3.  **clustering\_key**

        ```
        call set_table_property('<table_name>', 'clustering_key', '[columnName{:[desc|asc]} [,...]]') ;
        ```

        -   The clustering\_key property specifies the columns for Hologres to create clustered indexes. Hologres sorts data based on clustered indexes. Hologres allows you to use clustered indexes to accelerate range and filter queries on indexed columns.
        -   The columns that constitute the clustering key must meet the NOT NULL constraint. The clustering key does not support columns of the following data types: FLOAT, DOUBLE, ARRAY, JSON, and specific complex data types.
        -   When you use the clustering\_key property to specify a column, you can append `desc` or `asc` to the column name to specify a sorting order for the indexes to be built. By default, the ascending order `asc` is used.
        -   By default, the clustering key of a column-oriented table is null. By default, the clustering key of a row-oriented table is the primary key. No clustering key is specified in the versions earlier than Hologres version 0.9. If the clustering key is not the primary key of a table, Hologres generates two sorting orders for this table: sorting based on the primary key and sorting based on the clustering key. This causes redundant data.
        -   The clustering key is used for sorting. In this case, the first column among the columns that constitute the clustering key has the highest priority. We recommend that you retain only one or two columns to constitute the clustering key.
        -   The clustering key allows you to accelerate range and filter queries on the first few columns in a clustered index. **Queries must follow the leftmost matching principle. Otherwise, you cannot use the clustering key to accelerate the queries**.

            Assume that the clustering\_key property of Table table1 specifies Column col1 and Column col2 as indexed columns. The following examples are some queries that can be accelerated and some that cannot:

            ```
            // The query can be accelerated.
            select * from table1 where col1='abc'; 
            // The query can be accelerated.
            select * from table1 where col1>'xxx' and col1<'abc';
            // The query can be accelerated.
            select * from table1 where col1 in ('abc','def');
            // The query can be accelerated.
            select * from table1 where col1='abc' and col='def'; 
            // The query cannot be accelerated.
            select col1,col4 from table1 where col2='def';
            ```

        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'clustering_key', 'a,b');
            commit;
            -------------------------------------------------------------
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'clustering_key', 'a:desc,b:asc');
            commit;
            ```

    4.  **event\_time\_column**

        ```
        call set_table_property('<table_name>', 'event_time_column', '[columnName{:[desc|asc]} [,...]]') ;
        ```

        -   The segment\_key property is renamed to the event\_time\_column property in Hologres version 0.9. However, this property can still be used in Hologres version 0.9. The event\_time\_column property allows you to specify whether to use specific columns as event time columns. For example, you can specify a column of the TIME data type as an event time column. Hologres can quickly find the storage location of the corresponding data based on an event time column.
        -   Before you set the event\_time\_column property, make sure that orientation is set to column. This way, the table uses the column-oriented storage model.
        -   The columns that are specified as event time columns must meet the NOT NULL constraint. You cannot specify a column of one of the following data types as an event time column: FLOAT, DOUBLE, ARRAY, JSON, and specific complex data types.
        -   By default, the first non-null TIMESTAMP or TIMESTAMPTZ field in the schema of a column-oriented table is used as an event time column. If no such field exists, the first non-null DATE field is used as an event time column. By default, no event time column is specified for a table in the versions earlier than Hologres version 0.9.
        Examples

        ```
        begin;
        create table tbl (a int not null, b text not null);
        call set_table_property('tbl', 'event_time_column', 'a,b');
        commit;
        -------------------------------------------------------------
        begin;
        create table tbl (a int not null, b time not null);
        call set_table_property('tbl', 'event_time_column', 'b:asc');
        commit;
        ```

    5.  **bitmap\_columns**

        ```
        call set_table_property('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]') ;
        ```

        The following table describes the required parameters.

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table. The case of the table name must be same as that of a table name to be modified. The table name can contain the schema information of the table.|
        |on|Indicates that a bitmap index is built for the current field.|
        |off|Indicates that a bitmap index is not built for the current field.|

        -   The bitmap\_columns property allows you to specify whether to build bitmap indexes for specific columns. Bitmaps can help quickly filter data in a stored file. Therefore, we recommend that you convert filter conditions to bitmaps.
        -   Before you set the bitmap\_columns property, make sure that orientation is set to column. This way, the table uses the column-oriented storage model.
        -   We recommend that you build bitmap indexes only for columns with a few values. This way, a binary string is constructed for each value to indicate the bitmap where the value is located.
        -   The columns specified by the bitmap\_columns property can be null.
        -   By default, the bitmap\_columns property implicitly builds bitmap indexes for all TEXT columns in both Hologres version 0.8 and Hologres version 0.9.
        -   **You can call the set\_table\_property function for the bitmap\_columns property outside the transaction of CREATE TABLE to modify the property. The modified columns do not take effect immediately and bitmap indexes are asynchronously built and deleted in the background.** For more information, see [ALTER TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/ALTER TABLE.md).
        -   Examples

            ```
            // Create a table named tbl and configure bitmap indexes.
            begin;
            create table tbl (
              a int not null, 
              b text not null);
            call set_table_property('tbl', 'bitmap_columns', 'a:on,b:off');
            commit;
            
            // Modify the bitmap indexes for all fields. Bitmap indexes are automatically built for all TEXT fields, excluding the fields specified in the CALL statement.
            call set_table_property('tbl', 'bitmap_columns', 'a:off');
            // Modify the bitmap indexes in an incremental manner. Specify only whether to build bitmap indexes for specific fields.
            call update_table_property('tbl', 'bitmap_columns', 'b:off');
            ```

    6.  **dictionary\_encoding\_columns**

        ```
        call set_table_property('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off]}[,...]]') ;
        ```

        The following table describes the required parameters.

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table. The case of the table name must be same as that of a table name to be modified. The table name can contain the schema information of the table.|
        |on|Indicates that dictionary mappings are built for the current field.|
        |off|Indicates that dictionary mappings are not built for the current field.|
        |auto|Indicates that Hologres automatically determines whether to build dictionary mappings for the current field. If you select the auto keyword for a field, Hologres automatically determines whether to build dictionary mappings for the field based on the duplication degree of values. More duplicate values ensure a higher efficiency of dictionary mappings. By default, dictionary mappings are built for all TEXT columns in Hologres version 0.8 and earlier. In Hologres version 0.9 and later, Hologres can automatically determine whether to build dictionary mappings for a field based on the characteristics of data.|

        -   The dictionary\_encoding\_columns property allows you to specify whether to build dictionary mappings for specific columns. If you select the on keyword for a column, Hologres builds dictionary mappings for the values in the column. Dictionary mappings can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.
        -   Before you set the dictionary\_encoding\_columns property, make sure that orientation is set to column. This way, the table uses the column-oriented storage model.
        -   The columns specified by the dictionary\_encoding\_columns property can be null.
        -   We recommend that you build dictionary mappings only for columns with a few values. This can help compress storage.
        -   By default, the dictionary\_encoding\_columns property implicitly builds dictionary mappings for all TEXT columns in Hologres version 0.8 and earlier. In Hologres version 0.9 and later, Hologres can automatically determine whether to build dictionary mappings for a field based on the characteristics of data.
        -   **You can call the set\_table\_property function for the dictionary\_encoding\_columns property outside the transaction of CREATE TABLE to modify the property. The modified columns do not take effect immediately and dictionary mappings are asynchronously built and deleted in the background.** For more information, see [ALTER TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/ALTER TABLE.md).
        -   Examples

            ```
            // Create a table named tbl and configure dictionary mappings.
            begin;
            create table tbl (
              a int not null, 
              b text not null,
              c text not null
            );
            call set_table_property('tbl', 'dictionary_encoding_columns', 'a:on,b:off,c:auto');
            commit;
            
            // Modify the dictionary mappings for all fields. Dictionary mappings are automatically built for all TEXT fields, excluding the fields specified in the CALL statement.
            call set_table_property('tbl', 'dictionary_encoding_columns', 'a:off');
            // Modify the dictionary mappings in an incremental manner. Specify only whether to build dictionary mappings for specific fields.
            call update_table_property('tbl', 'dictionary_encoding_columns', 'b:off');
            ```

    7.  **time\_to\_live\_in\_seconds**

        ```
        call set_table_property('<table_name>', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

        -   The time\_to\_live\_in\_seconds property specifies the time to live \(TTL\) of data in a table, which is represented in seconds. The time must be a non-negative number, which can be an integer or a floating-point number.
        -   You can call the set\_table\_property function for the time\_to\_live\_in\_seconds property outside the transaction of CREATE TABLE to modify the property.
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'time_to_live_in_seconds', '3.14159');
            commit;
            
            // Modify the TTL.
            call set_table_property('tbl', 'time_to_live_in_seconds', '86400');
            ```

            **Note:** Hologres does not delete data from a table exactly based on the TTL specified for the table but at specific time after the data exceeds the TTL. Therefore, the business logic cannot highly depend on the TTL. If you want Hologres to delete table data at a specified point in time, create a node in the Data Analytics module of HoloStudio and schedule the node to delete data.


## Add comments

Hologres allows you to add comments to internal tables, foreign tables, and columns.

The following examples show you how to add comments:

```
-- Add a comment to an internal table.
COMMENT ON TABLE table_name IS 'my comments on table table_name.' ;

-- Add a comment to a column.
COMMENT ON COLUMN table_name.col1 IS 'This my first col1';

-- Add a comment to a foreign table.
COMMENT ON FOREIGN TABLE foreign_table IS ' comments on my foreign table';
```

For more information, see [PostgreSQL comment](https://www.postgresql.org/docs/11/sql-comment.html).

