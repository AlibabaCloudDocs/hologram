---
keyword: [modify data in a table, Hologres, DDL, ALTER TABLE]
---

# ALTER TABLE

You can execute the ALTER TABLE statement to modify data in a table. This topic describes how to execute the ALTER TABLE statement.

## Usage notes

When you modify data in a Hologres table, take note of the following items:

-   You can rename the table or create fields in the table.
-   Operations on a foreign table are subjected to no limits.
-   You can also execute the ATTACH PARTITION and DETACH PARTITION statements to modify data in partitioned Hologres tables.

## Rename a table

You can execute the ALTER TABLE statement to rename a specified table. If the specified table does not exist or the new table name is the same as an existing table, an error is thrown.

-   Syntax

    ```
    -- Rename an internal table.
    ALTER TABLE <table_name> RENAME TO <new_table_name>;
    -- Rename a foreign table.
    ALTER FOREIGN TABLE <foreign_table_name> TO <new_foreign_table_name>;
    ```

-   Examples

    ```
    -- Rename the internal table holo_test as holo_test_1.
    ALTER TABLE holo_test RENAME TO holo_test_1 ;
    -- Rename the foreign table foreign_holo_test as foreign_holo_test_1.
    ALTER FOREIGN TABLE foreign_holo_test RENAME TO foreign_holo_test_1;
    ```


## Create fields

You can execute the ALTER TABLE statement to create fields in a table.

-   Syntax

    ```
    -- Create a field.
    ALTER TABLE IF EXISTS <table_name> ADD COLUMN <new_column> <data_type>;
    -- Create multiple fields at a time.
    ALTER TABLE IF EXISTS <table_name> ADD COLUMN <new_column_1> <data_type>, ADD COLUMN <new_column_2> <data_type>; 
    ```

-   Examples

    ```
    -- Create a field named id in the table named holo_test.
    ALTER TABLE IF EXISTS holo_test ADD COLUMN id int;
    ```


## Manage the default values of fields

You can execute the ALTER TABLE statement to manage the default values of fields in a table.

-   Syntax

    ```
    -- Modify the default value of a field in a table.
    ALTER TABLE <table> ALTER COLUMN <column> SET DEFAULT <expression>;
    -- Delete the default value of a field in a table.
    ALTER TABLE <table> ALTER COLUMN <column> DROP DEFAULT;
    ```

-   Examples

    ```
    -- Modify the default value of the id field in the table named holo_test and set the default value to 0.
    ALTER TABLE holo_test ALTER COLUMN id SET DEFAULT 0;
    -- Delete the default value of the id field in the table named holo_test.
    ALTER TABLE holo_test ALTER COLUMN id DROP DEFAULT;
    ```


## Modify the properties of a table

Hologres allows you to modify table properties by executing SQL statements to modify the relevant parameters. Specifically, you can modify the following properties:

-   You can modify the dictionary encoding property of fields in a table.
    -   Syntax

        ```
        -- Full modification: In addition to modifying the dictionary encoding property of the fields that you specify in the CALL statement, Hologres automatically enables dictionary encoding for fields of the TEXT data type.
        CALL SET_TABLE_PROPERTY('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]') ;
        
        -- Incremental modification: The dictionary encoding property of only fields that you specify in the CALL statement is modified. The dictionary encoding property of other fields remains unchanged.
        CALL UPDATE_TABLE_PROPERTY('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]') ;
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table to be modified. Pay attention to the case sensitivity. The table name can contain the schema information about the table.|
        |on|Enables dictionary encoding for the current field.|
        |off|Disables dictionary encoding for the current field.|
        |auto|Specifies that Hologres determines whether to enable or disable dictionary encoding for the current field. If you set this parameter for a field, Hologres determines whether to enable dictionary encoding for the field based on the recurrences of the values of this field. Higher recurrences of field values indicate that dictionary encoding is more suitable for the field. By default, Hologres V0.8 and earlier automatically enable dictionary encoding for all fields of the TEXT data type. Hologres V0.9 and later determine whether to enable dictionary encoding for a field based on the characteristics of the values of the field.|

    -   Examples
        -   In the following sample statements, dictionary encoding is enabled for the a field. Whether to enable dictionary encoding for the b field is determined by Hologres. Dictionary encoding is disabled for the c and d fields.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL UPDATE_TABLE_PROPERTY('holo_test','dictionary_encoding_columns','a:on,b:auto');
            ```

        -   In the following sample statements, dictionary encoding is disabled for the a field. Whether to enable dictionary encoding for the b, c, and d fields is determined by Hologres.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL SET_TABLE_PROPERTY('holo_test','dictionary_encoding_columns','a:off');
            ```

-   You can modify the bitmap index property of fields in a table.
    -   Syntax

        ```
        -- Full modification: In addition to modifying the bitmap index property of the fields that you specify in the CALL statement, Hologres automatically enables dictionary encoding for fields of the TEXT data type.
        CALL SET_TABLE_PROPERTY('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]') ;
        
        -- Incremental modification: The bitmap index property of only fields that you specify in the CALL statement is modified. The bitmap index property of other fields remains unchanged.
        CALL UPDATE_TABLE_PROPERTY('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]') ;
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table to be modified. Pay attention to the case sensitivity. The table name can contain the schema information about the table.|
        |on|Creates a bitmap index for the current field.|
        |off|Does not create a bitmap index for the current field.|

    -   Examples
        -   In the following sample statements, a bitmap index is created for the a field. No bitmap index is created for the b, c, or d field.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL UPDATE_TABLE_PROPERTY('holo_test','bitmap_columns','a:on');
            ```

        -   In the following sample statements, no bitmap index is created for the b field. Hologres automatically enables dictionary encoding for the a, c, and d fields.

            ```
            CREATE TABLE holo_test_1 (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL SET_TABLE_PROPERTY('holo_test_1','bitmap_columns','b:off');
            ```

-   You can modify the time to live \(TTL\) of a table.
    -   Syntax

        ```
        call set_table_property('<table_name>', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |time\_to\_live\_in\_seconds|The TTL that you want to set for the table. Unit: seconds. The value must be a non-negative integer or floating-point number.|

        **Note:** The TTL of a Hologres table starts from the time when data is written to the table. If no operation is performed on the table within the specified TTL, the table is deleted sometime after it expires.

    -   Examples

        ```
        call set_table_property('holo_test', 'time_to_live_in_seconds', '600');
        ```


