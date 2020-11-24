---
keyword: [Hologres, data types]
---

# Data types

The data types of Hologres and PostgreSQL are compatible. This topic describes the data types and array types supported by Hologres.

## Data types

The data types of Hologres are a subset of the data types of PostgreSQL. The following table lists the data types supported by Hologres.

|Data type|Alias|Supported version|Length|Description|Valid value|Example|
|---------|-----|-----------------|------|-----------|-----------|-------|
|INTEGER|-   INT
-   INT4

|Hologres version 0.8 and earlier|4 bytes.|Common integers.|-2147483648 to 2147483647|2147483647|
|BIGINT|INT8|Hologres version 0.8 and earlier|8 bytes.|Integers in a larger range.|-9223372036854775808 to +9223372036854775807|9223372036854775807|
|BOOLEAN|BOOL|Hologres version 0.8 and earlier|1 byte.|The Boolean data type.|-   True
-   False

|True|
|FLOAT|FLOAT4|Hologres version 0.8 and earlier|4 bytes.|The precision is a variable, which is not accurate.|15 decimal digits precision.|123.3333|
|DOUBLE PRECISION|FLOAT8|Hologres version 0.8 and earlier|8 bytes.|The precision is a variable, which is not accurate.|15 decimal digits precision.|123.3333|
|TEXT|VARCHAR|Hologres version 0.8 and earlier|Variable length|A character string with a variable length.|N/A|abcdefg|
|TIMESTAMP WITH TIME ZONE|TIMESTAMPTZ|Hologres version 0.8 and earlier|8 bytes.|A timestamp with a time zone. Unit: milliseconds.**Note:** PostgreSQL stores the value of `TIMESTAMPTZ` in UTC by using a + or - symbol and time zone offset after the time.`` If no time zone is specified in the input string, the time zone indicated by the timezone parameter is used. Then, the time zone is converted into a UTC time zone by using the offset of the timezone parameter.

|4713 BC to 294276 AD|2004-10-19 10:23:54+02|
|DECIMAL|NUMERIC|Hologres version 0.8 and earlier|Variable length|The Precision and Scale parameters must be specified. -   Precision: the number of digits in a number.
-   Scale: the number of digits to the right of the decimal point in a number.

|Maximum precision and scale: 38.|DECIMAL\(38, 10\)|
|DATE|DATE|Hologres version 0.8|4 bytes.|4713 BC ~ 5874897 AD|Unit: Day|2004-10-19|
|TIMESTAMP|TIMESTAMP|Hologres version 0.8|8 bytes.|4713 BC ~ 5874897 AD|A timestamp without a time zone. Unit: microseconds.|2020-01-01 01:01:01.123456|
|CHAR\(n\)|CHAR\(n\)|Hologres version 0.8|Fixed number of characters in length. Maximum value: n.|The data size must be less than or equal to 1 GB.|A string that consists of a fixed number of characters in length.|-   abcd
-    |
|VARCHAR\(n\)|VARCHAR\(n\)|Hologres version 0.8|The value is variable in length and cannot exceed n characters.|The data size must be less than or equal to 1 GB.|A mutable string that contains a limited number of characters in length.|abcdefg|
|SERIAL|AUTO-INCREMENT COLUMN|Hologres version 0.8|For more information, see PostgreSQL SERIAL.|N/A|N/A|N/A|

The following sample SQL statements shows how to use the TIMESTAMPTZ, DATE, and DECIMAL data types:

```
CREATE TABLE test_data_type (
    tswtz_column timestamp WITH TIME ZONE,
    date_column date,
    decimal_column decimal(38, 10)
);

INSERT INTO test_data_type
VALUES ('2004-10-19 08:08:08', '2004-10-19', 123.456);

SELECT * FROM test_data_type;
```

## Array types

Hologres supports only one-dimensional arrays of the following types:

-   int4\[\]
-   int8\[\]
-   float4\[\]
-   float8\[\]
-   boolean\[\]
-   text\[\]

The following sample SQL statements shows how to use the preceding array types:

-   Declare an array.

    ```
    CREATE TABLE array_example(
    int4_array int4[],
    int8_array int8[],
    float4_array float4[],
    float8_array float8[],
    boolean_array boolean[],
    text_array text[]);
    ```

-   Add an element to an array.
    -   Use the ARRAY keyword.

        ```
        INSERT INTO array_example(
        int4_array,
        int8_array,
        float4_array,
        float8_array,
        boolean_array,
        text_array)
        VALUES (ARRAY[1, 2, 3, 4],
        ARRAY[1, 2, 3, 4],
        ARRAY[1.0, 2.0],
        ARRAY[1.0, 2.0, 3.0],
        ARRAY[true, true, false],
        ARRAY['foo1', 'foo2', 'foo3']);
        ```

    -   Use the `{}` expression.

        ```
        INSERT INTO array_example(
        int4_array,
        int8_array,
        float4_array,
        float8_array,
        boolean_array,
        text_array)
        VALUES ('{1, 2, 3, 4}',
        '{1, 2, 3, 4}',
        '{1.0, 2.0}',
        '{1.0, 2.0, 3.0}',
        '{true, true, false}',
        '{"foo1", "foo2", "foo3"}');
        ```

-   Query an array.
    -   Query an element in an array.

        ```
        SELECT int4_array[3] FROM array_example;
        ```

    -   Query multiple elements in an array.

        ```
        SELECT int4_array[1:2] FROM array_example;
        ```


## Data type mappings between MaxCompute and Hologres when you create a foreign MaxCompute table

The following table lists the data type mappings between MaxCompute and Hologres when you create a foreign MaxCompute table.

|MaxCompute data type|Hologres data type|Supported version|Description|
|--------------------|------------------|-----------------|-----------|
|-   STRING
-   VARCHAR

|TEXT|Hologres version 0.8 and earlier|N/A|
|BIGINT|INT8|Hologres version 0.8 and earlier|N/A|
|INT|-   INT4
-   INT

|Hologres version 0.8 and earlier|N/A|
|FLOAT|-   FLOAT4
-   REAL

|Hologres version 0.8 and earlier|N/A|
|DOUBLE|-   FLOAT
-   FLOAT8

|Hologres version 0.8 and earlier|N/A|
|BOOLEAN|BOOL|Hologres version 0.8 and earlier|N/A|
|DATETIME|TIMESTAMPTZ|Hologres version 0.8 and earlier|The DATETIME data type of MaxCompute specifies a date time in UTC+8. The time ranges from 0000-01-01 to 9999-12-31, which is accurate to milliseconds.|
|DECIMAL|NUMERIC|Hologres version 0.8 and earlier|If no precision or scale is specified for the DECIMAL in MaxCompute, the default value is DECIMAL \(38,18\). The precision and scale are automatically converted when you create a foreign table by executing the [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md) statement.|
|TIMESTAMP|TIMESTAMPTZ|Hologres version 0.8|-   The TIMESTAMP in MaxCompute. Valid values: 0000-01-01 00:00:00.000000000 to 9999-12-31 23.59:59.999999999. This is accurate to nanoseconds.
-   The TIMESTAMPTZ of Hologres is accurate to milliseconds.

Precision and scale are automatically converted.|
|CHAR\(n\)|Hologres allows you to map CHAR\(n\) to TXET in MaxCompute. You must set the `set hg_enable_convert_type_for_foreign_table = true` parameter, and set the field type to TEXT when you create a foreign table.

|Hologres version 0.8|CHAR\(n\) of MaxCompute is a fixed-length character, and n indicates the length. Maximum value of n: 255. If you insert a string that is shorter than the length of the column, PostgreSQL increases the length by using spaces.|
|VARCHAR\(n\)|Hologres allows you to map CHAR\(n\) to TXET in MaxCompute. You need to set the `set hg_enable_convert_type_for_foreign_table = true` parameter, and set the field type to TEXT when you create a foreign table.

|Hologres version 0.8|VARCHAR\(n\) is a variable-length character string, and n indicates the length. Valid values of n: 1 to 65535.|
|DATE|DATE|Hologres version 0.8|N/A|
|SMALLINT|Default value: INT4.Hologres allows you to map SMALLINT to INT8 in MaxCompute. You need to set the `set hg_enable_convert_type_for_foreign_table = true` parameter, and set the field type to INT8 when you create a foreign table.

|Hologres version 0.8 and earlier|N/A|
|TINYINT|Default value: INT4.Hologres allows you to map SMALLINT to INT8 in MaxCompute. You need to set the `set hg_enable_convert_type_for_foreign_table = true` parameter, and set the field type to INT8 when you create a foreign table.

|Hologres version 0.8 and earlier|N/A|
|CHAR|N/A|N/A|N/A|
|INT4\[\]|INT4\[\]|Hologres version 0.8 and earlier|N/A|
|INT8\[\]|INT8\[\]|Hologres version 0.8 and earlier|N/A|
|FLOAT4\[\]|FLOAT4\[\]|Hologres version 0.8 and earlier|N/A|
|FLOAT8\[\]|FLOAT8\[\]|Hologres version 0.8 and earlier|N/A|
|BOOLEAN\[\]|BOOLEAN\[\]|Hologres version 0.8 and earlier|N/A|
|TEXT\[\]|TEXT\[\]|Hologres version 0.8 and earlier|N/A|
|BINARY|N/A|N/A|N/A|
|INT1\[\]|N/A|N/A|N/A|
|INT2\[\]|N/A|N/A|N/A|

**Note:** If the MaxCompute table contains fields whose data types are not supported by Hologres, you can query other fields.

## Data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time

The following table lists the data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time.

|MaxCompute data type|Hologres data type|Supported version|
|--------------------|------------------|-----------------|
|-   STRING
-   VARCHAR

|TEXT|Hologres version 0.8 and earlier|
|BIGINT|INT8|Hologres version 0.8 and earlier|
|INT|-   INT4
-   INT

|Hologres version 0.8 and earlier|
|FLOAT|-   FLOAT4
-   REAL

|Hologres version 0.8 and earlier|
|DOUBLE|-   FLOAT
-   FLOAT8

|Hologres version 0.8 and earlier|
|DATETIME|TIMESTAMPTZ|Hologres version 0.8 and earlier|
|DECIMAL|NUMERIC|Hologres version 0.8 and earlier|
|BOOLEAN|BOOL|Hologres version 0.8 and earlier|
|TIMESTAMP|TIMESTAMPTZ|Hologres version 0.8|
|CHAR\(n\)|CHAR\(n\)|Hologres version 0.8|
|VARCHAR\(n\)|VARCHAR\(n\)|Hologres version 0.8|
|TINYINT|INT4|Hologres version 0.8 and earlier|
|SMALLINT|INT4|Hologres version 0.8 and earlier|
|INT4\[\]|INT4\[\]|Hologres version 0.8 and earlier|
|INT8\[\]|INT8\[\]|Hologres version 0.8 and earlier|
|FLOAT4\[\]|FLOAT4\[\]|Hologres version 0.8 and earlier|
|FLOAT8\[\]|FLOAT8\[\]|Hologres version 0.8 and earlier|
|BOOLEAN\[\]|BOOLEAN\[\]|Hologres version 0.8 and earlier|
|TEXT\[\]|TEXT\[\]|Hologres version 0.8 and earlier|
|CHAR|N/A|N/A|
|BINARY|N/A|N/A|
|INT1\[\]|N/A|N/A|
|INT2\[\]|N/A|N/A|

## Data type mappings between Realtime Compute for Apache Flink and Hologres

The following table lists the data type mappings between Realtime Compute for Apache Flink and Hologres.

|Realtime Compute data type|Hologres data type|Supported version|
|--------------------------|------------------|-----------------|
|VARCHAR|TEXT|Hologres version 0.8 and earlier|
|BIGINT|INT8|Hologres version 0.8 and earlier|
|INT|-   INT4
-   INT

|Hologres version 0.8 and earlier|
|SMALLINT|-   INT4
-   INT

|Hologres version 0.8 and earlier|
|TINYINT|-   INT4
-   INT

|Hologres version 0.8 and earlier|
|FLOAT|-   FLOAT4
-   REAL

|Hologres version 0.8 and earlier|
|DOUBLE|-   FLOAT
-   FLOAT8

|Hologres version 0.8 and earlier|
|BOOLEAN|BOOL|Hologres version 0.8 and earlier|
|TIMESTAMP|TIMESTAMPTZ|Hologres version 0.8 and earlier|
|DATE|DATE|Hologres version 0.8 and earlier|
|DECIMAL|NUMERIC|Hologres version 0.8 and earlier|
|TIME|N/A|N/A|
|CHAR|N/A|N/A|
|BINARY|N/A|N/A|

