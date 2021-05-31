---
keyword: [modify data in a table, Hologres, DDL, ALTER TABLE]
---

# ALTER TABLE

You can execute the ALTER TABLE statement to modify data in a table. This topic describes how to execute the ALTER TABLE statement.

## Limits

When you execute the ALTER TABLE statement to modify data in a Hologres table, take note of the following items:

-   You can rename a table, create fields in the table, or modify the time-to-live \(TTL\) of the table.
-   You can configure the DEFAULT constraint for a field. You can also modify the dictionary\_encoding\_columns and bitmap\_columns properties for a field.
-   You can add the ATTACH PARTITION and DETACH PARTITION clauses to the ALTER TABLE statement to modify data in partitioned Hologres tables.

## Rename a table

You can execute the ALTER TABLE statement to rename a specified table. If the specified table does not exist or the new table name is the same as an existing table name, an error is thrown.

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


## Configure the DEFAULT constraint for a field

You can execute the ALTER TABLE statement to configure the DEFAULT constraint for a field in a table that is created in Hologres V0.9.23 or later. In this scenario, the ALTER TABLE statement uses the following syntax:

-   Syntax

    ```
    -- Specify a DEFAULT constraint for a field in a table.
    ALTER TABLE <table> ALTER COLUMN <column> SET DEFAULT <expression>;
    -- Remove the DEFAULT constraint for a field in a table.
    ALTER TABLE <table> ALTER COLUMN <column> DROP DEFAULT;
    ```

-   Examples

    ```
    -- Specify a DEFAULT constraint for the id field in the table named holo_test to set the default value to 0.
    ALTER TABLE holo_test ALTER COLUMN id SET DEFAULT 0;
    -- Remove the DEFAULT constraint for the id field in the table named holo_test.
    ALTER TABLE holo_test ALTER COLUMN id DROP DEFAULT;
    ```


## Modify the properties of a table

Hologres allows you to modify table properties by modifying relevant function parameters. You can modify the following properties:

-   **dictionary\_encoding\_columns**
    -   Syntax

        ```
        -- Set the dictionary encoding property for all fields.
        CALL SET_TABLE_PROPERTY('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]');
        
        -- Modify the dictionary encoding property only for the fields that you specify in the CALL statement. The dictionary encoding property remains unchanged for other fields.
        CALL UPDATE_TABLE_PROPERTY('<table_name>', 'dictionary_encoding_columns', '[columnName{:[on|off|auto]}[,...]]');
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table to be modified. Pay attention to the case sensitivity. The table name can contain the schema information about the table.|
        |on|Enables dictionary encoding for the field.|
        |off|Disables dictionary encoding for the field.|
        |auto|Specifies that Hologres determines whether to enable or disable dictionary encoding for the field. If you use this keyword for a field, Hologres determines whether to enable dictionary encoding for the field based on the recurrences of the values of this field. Dictionary encoding is more suitable for the fields that have higher recurrences of field values. By default, Hologres V0.8 and earlier enable dictionary encoding for all fields of the TEXT data type. Hologres V0.9 and later determine whether to enable dictionary encoding for a field based on the characteristics of the values of the field.|

    -   Examples
        -   In the following sample statements, dictionary encoding is enabled for the a field. Hologres determines whether to enable dictionary encoding for the b field. The dictionary encoding property remains unchanged for the c and d fields.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL UPDATE_TABLE_PROPERTY('holo_test','dictionary_encoding_columns','a:on,b:auto');
            ```

        -   In the following sample statements, dictionary encoding is disabled for the a field. Hologres automatically enables dictionary encoding for the b, c, and d fields.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL SET_TABLE_PROPERTY('holo_test','dictionary_encoding_columns','a:off');
            ```

-   **bitmap\_columns**
    -   Syntax

        ```
        -- Set the bitmap index property for all fields.
        CALL SET_TABLE_PROPERTY('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]');
        
        -- Modify the bitmap index property only for the fields that you specify in the CALL statement. The bitmap index property remains unchanged for other fields.
        CALL UPDATE_TABLE_PROPERTY('<table_name>', 'bitmap_columns', '[columnName{:[on|off]}[,...]]');
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |table\_name|The name of the table to be modified. Pay attention to the case sensitivity. The table name can contain the schema information about the table.|
        |on|Creates a bitmap index for the field.|
        |off|Does not create a bitmap index for the field.|

    -   Examples
        -   In the following sample statements, a bitmap index is created for the a field. The bitmap index property remains unchanged for the b, c, and d fields.

            ```
            CREATE TABLE holo_test (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL UPDATE_TABLE_PROPERTY('holo_test','bitmap_columns','a:on');
            ```

        -   In the following sample statements, no bitmap index is created for the b field. Hologres automatically creates bitmap indexes for the a, c, and d fields.

            ```
            CREATE TABLE holo_test_1 (
             a text NOT NULL,
             b text NOT NULL,
             c text NOT NULL,
             d text
            );
            CALL SET_TABLE_PROPERTY('holo_test_1','bitmap_columns','b:off');
            ```

-   time\_to\_live\_in\_seconds
    -   Syntax

        ```
        call set_table_property('<table_name>', 'time_to_live_in_seconds', '<non_negative_literal>');
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |time\_to\_live\_in\_seconds|The property to be modified. This property specifies the TTL of the table, in seconds. The value must be a non-negative integer or floating-point number.|

        **Note:** The TTL of a Hologres table starts from the time when data is written to the table. If no operation is performed on the table within the specified TTL, the table is deleted sometime after it expires.

    -   Examples

        ```
        call set_table_property('holo_test', 'time_to_live_in_seconds', '600');
        ```


