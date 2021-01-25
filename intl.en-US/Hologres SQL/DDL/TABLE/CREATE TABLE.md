# CREATE TABLE

## Create table

1.  **Synopsis**

    The CREATE TABLE statement in Hologres only supports certain features of the CREATE TABLE statement in PostgreSQL. The CREATE TABLE statement in Hologres uses the following syntax:

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
    1.  The column\_type parameter specifies the data type of the column to be created in the new table. For more information about the data types supported by Hologres, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).
    2.  The following table lists the six `column constraints` that are supported and whether they are supported or not as `table constraints`.

        |Column constraint|Supported as table constraint|
        |-----------------|-----------------------------|
        |PRIMARY KEY constraint|Yes|
        |NOT NULL constraint|Yes|
        |NULL constraint|Yes|
        |UNIQUE constraint|No|
        |CHECK constraint|No|
        |DEFAULT constraint|No|

        **Note:** If a `column constraint` is used, you can only define one column as the `primary key` of a table. If the `primary key` of a table consists of multiple columns, use a `table constraint`.

    3.  You can call the set\_table\_property function to set properties for the table. For more information, see the following text.
3.  **Limitation**
    1.  Table names and column names are both case insensitive. To define an uppercase table or column name or a table or column name with special characters, you can enclose the name in double quotation marks \(`" "`\) to escape it. Examples are as follows:

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

    2.  `IF NOT EXISTS`: When you create a table, if no table with the same name exists and the semantics is correct, the table can be created. If the `IF NOT EXISTS` option is not specified and a table with the same name exists, an error is thrown. If the `IF NOT EXISTS` option is specified, Hologres displays a notice, skips the table creation step, and returns a success response. The following table lists the rules.

        |Situation with IF NOT EXISTS specified|Response|
        |--------------------------------------|--------|
        |A table with the same name exists.|`NOTICE:relation “xx“already exists, skippingSUCCEED`|
        |No table with the same name exists.|`SUCCEED`|

    3.  Table names and column names can contain only letters, digits, and underscores \(\_\), and must start with a letter. If you want to include a special character in a table or column name, escape the table or column name by using double quotation marks \(" "\). Uppercase letters are treated as lowercase letters because table names and column names are case-insensitive. An example is as follows:

        ```
        begin;
        create table TABLE_one (a int not null);
        call set_table_property('table_one', 'clustering_key', 'a');
        commit;
        insert into table_one values (12);
        ```

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

Hologres allows you to call the set\_table\_property function to set table properties. Proper settings of table properties help Hologres efficiently sort and query data.

1.  **Synopsis**

    ```
    call set_table_property('<table_name>', property, value)
    ```

    **Note:** Generally, you must call the `set_table_property` function in the same transaction as `CREATE TABLE`.

    In the current Hologres version, you can execute one of the following SQL statements to set table properties:

    ```
    call set_table_property('table_name', 'orientation', '[column | row]'); 
    call set_table_property('table_name', 'clustering_key', '[columnName{:[desc|asc]} [,...]]'); 
    call set_table_property('table_name', 'segment_key', '[columnName [,...]]');
    call set_table_property('table_name', 'bitmap_columns', '[columnName [,...]]');
    call set_table_property('table_name', 'dictionary_encoding_columns', '[columnName [,...]]');
    call set_table_property('table_name', 'time_to_live_in_seconds', '<non_negative_literal>');
    call set_table_property('table_name', 'distribution_key', '[columnName[,...]]') ;
    ```

2.  **Parameters**
    1.  **orientation**

        ```
        call set_table_property('<table_name>', 'orientation', '[column | row]');
        ```

        -   The orientation property specifies whether the database table uses the column-oriented or row-oriented storage model in Hologres.
        -   By default, database tables use the column-oriented storage model in Hologres. **The column-oriented storage model is more applicable to online analytical processing \(OLAP\) scenarios and supports complex queries. The row-oriented storage model is more applicable to scenarios where key-value pairs are used for queries and supports point queries and scans based on primary keys.**
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'orientation', 'row');
            commit;
            ```

    2.  **clustering\_key**

        ```
        call set_table_property('<table_name>', 'clustering_key', '[columnName{:[desc|asc]} [,...]]') ;
        ```

        -   The clustering\_key property specifies some columns for Hologres to create clustered indexes. Hologres sorts data based on clustered indexes. By using clustered indexes, Hologres can accelerate range and filter queries on indexed columns.
        -   The columns specified by clustering\_key must meet the NOT NULL constraint.
        -   When using clustering\_key to specify a column, you can append `desc` or `asc` to the column name to specify a sorting order for building indexes. The ascending order `asc` is used by default.
        -   The `clustering_key` property cannot be set on columns whose data type is `FLOAT or DOUBLE`.
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

    3.  **segment\_key**

        ```
        call set_table_property('<table_name>', 'segment_key', '[columnName{:[desc|asc]} [,...]]') ;
        ```

        -   The segment\_key property specifies some columns, such as the column of time, as segment keys. Hologres can quickly find the storage location of the corresponding data based on the specified columns.
        -   Before you set the segment\_key property, make sure that orientation is set to column, that is, the table uses the column-oriented storage model.
        -   The columns specified by segment\_key must meet the NOT NULL constraint.
        -   The segment\_key property cannot be set on columns whose data type is a floating-point type, for example, FLOAT or DOUBLE.
        Examples

        ```
        begin;
        create table tbl (a int not null, b text not null);
        call set_table_property('tbl', 'segment_key', 'a,b');
        commit;
        -------------------------------------------------------------
        begin;
        create table tbl (a int not null, b time not null);
        call set_table_property('tbl', 'segment_key', 'b:asc');
        commit;
        ```

    4.  **bitmap\_columns**

        ```
        call set_table_property('<table_name>', 'bitmap_columns', '[columnName [,...]]') ;
        ```

        -   The bitmap\_columns property specifies the columns on which Hologres builds bit codes. The bitmap can be used to quickly filter data in a segment. Therefore, we recommend that you encode the data of filter conditions to bit codes.
        -   Before you set the bitmap\_columns property, make sure that orientation is set to column, that is, the table uses the column-oriented storage model.
        -   The bitmap\_columns property is suitable for unordered columns with a few values. It constructs a binary string for each value to indicate the bitmap where the value is located.
        -   The columns specified by bitmap\_columns can be null.
        -   By default, all text columns are implicitly set in bitmap\_columns.
        -   **You can call the set\_table\_property function for the bitmap\_columns property outside the transaction of CREATE TABLE to modify the property.**
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'bitmap_columns', 'a,b');
            commit;
            ```

    5.  **dictionary\_encoding\_columns**

        ```
        call set_table_property('<table_name>', 'dictionary_encoding_columns', '[columnName [,...]]') ;
        ```

        -   The dictionary\_encoding\_columns property specifies some columns as dictionary encoding columns. Hologres creates a dictionary mapping for the values of the specified columns. Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.
        -   Before you set the dictionary\_encoding\_columns property, make sure that orientation is set to column, that is, the table uses the column-oriented storage model.
        -   The columns specified by dictionary\_encoding\_columns can be null.
        -   The dictionary\_encoding\_columns property is suitable for unordered columns with a few values, which can be compressed for storage.
        -   By default, all text columns are implicitly set in dictionary\_external\_columns.
        -   You can call the set\_table\_property function for the dictionary\_encoding\_columns property outside the transaction of CREATE TABLE to modify the property.
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'dictionary_encoding_columns', 'a,b');
            commit;
            ```

    6.  **time\_to\_live\_in\_seconds**

        ```
        call set_table_property('<table_name>', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

        -   The time\_to\_live\_in\_seconds property specifies the time to live \(TTL\) of data in a table, which is represented in seconds. It must be a non-negative number, which can be an integer or a floating-point number.
        -   You can call the set\_table\_property function for the time\_to\_live\_in\_seconds property outside the transaction of CREATE TABLE to modify the property.
        -   Examples

            ```
            begin;
            create table tbl (a int not null, b text not null);
            call set_table_property('tbl', 'time_to_live_in_seconds', '3.14159');
            commit;
            -------------------------------------------------------------
            begin;
            create table tbl (a int not null, b time not null);
            call set_table_property('tbl', 'time_to_live_in_seconds', '86400');
            commit;
            ```

            **Note:** Hologres does not delete data from a table exactly based on the TTL specified for the table but at certain time after the data exceeds the TTL. Therefore, the business logic shall not highly depend on the TTL. If you want Hologres to delete table data at a specified time point, create a node in the Data Analytics module of HoloStudio and schedule the node to delete data.

    7.  **distribution\_key**

        ```
        call set_table_property('<table_name>', 'distribution_key', '[columnName[,...]]') ;
        ```

        -   The distribution\_key property specifies a column as the distribution column. Hologres distributes data in the table based on the specified column.
        -   If you specify a single value for columnName, no extra spaces are allowed. If you specify multiple values for columnName, separate them with commas \(,\) and do not include extra spaces.
        -   The column specified by distribution\_key can be null.
        -   By default, Hologres randomly distributes data in a table to each shard. If you specify a distribution column, data is shuffled to each shard based on the distribution column. Same values are distributed to the same shard. If you use the distribution column as a filter condition, Hologres directly scans data in the shards that meet the filter condition. If you use the distribution column as a join condition, Hologres directly joins data of a node on this node, without the need to shuffle data to other compute nodes. This significantly improves the join efficiency.
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
            ```


## Add comments

Hologres allows you to add comments to tables, foreign tables, and columns.

Examples are as follows:

```
-- Add a comment to a table.
comment on table table_name is 'my comments on table table_name.' ;

-- Add a comment to a column.
comment on column table_name.col1 is 'This my first col1';

-- Add a comment to a foreign table.
comment on foreign table foreign_table is ' comments on my foreign table';
```

For more information, see [PostgreSQL comment](https://www.postgresql.org/docs/11/sql-comment.html).

