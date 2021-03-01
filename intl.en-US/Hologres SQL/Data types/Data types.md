---
keyword: [Hologres, data type]
---

# Data types

The data types of Hologres and PostgreSQL are compatible. This topic describes the data types and array types that are supported by Hologres.

## Data types

The data types of Hologres are a subset of the data types of PostgreSQL. The following table describes the data types that are supported by Hologres.

|Data type|Supported version|Length|Description|Valid value|Example|
|---------|-----------------|------|-----------|-----------|-------|
|INTEGER \(alias: INT or INT4\)|All Hologres versions|4 bytes.|Common integers.|-2147483648 to +2147483647|2147483647|
|BIGINT \(alias: INT8\)|All Hologres versions|8 bytes.|Integers in a larger range.|-9223372036854775808 to +9223372036854775807|9223372036854775807|
|BOOLEAN \(alias: BOOL\)|All Hologres versions|1 byte.|The Boolean data type.|-   True
-   False

|True|
|FLOAT \(alias: FLOAT4\)|All Hologres versions|4 bytes.|The precision is a variable, which is not accurate.|15 decimal digits precision.|123.3333|
|DOUBLE PRECISION \(alias: FLOAT8\)|All Hologres versions|8 bytes.|The precision is a variable, which is not accurate.|15 decimal digits precision.|123.3333|
|TEXT \(alias: VARCHAR\)|All Hologres versions|Variable length.|A character string with a variable length.|N/A|abcdefg|
|TIMESTAMP WITH TIME ZONE \(alias: TIMESTAMPTZ\)|All Hologres versions|8 bytes.|A timestamp with a time zone. Unit: milliseconds.**Note:** PostgreSQL identifies the time zone in UTC by using a plus sign \(+\) or minus sign \(-\) and a time zone offset after the time in a value of `TIMESTAMPTZ`. If no time zone is specified in the input string, the time zone indicated by the timezone parameter is used. Then, the time zone is converted to a UTC time zone by using the offset of the timezone parameter.

|4713 BC to 294276 AD|2004-10-19 10:23:54+02|
|DECIMAL \(alias: NUMERIC\)|All Hologres versions|Variable length.|The Precision and Scale parameters must be specified.-   Precision: the number of digits in a number.
-   Scale: the number of digits to the right of the decimal point in a number.

|Maximum precision and scale: 38.|DECIMAL\(38, 10\)|
|DATE|Hologres version 0.8|4 bytes.|Unit: day.|4713 BC ~ 5874897 AD|2004-10-19|
|TIMESTAMP|Hologres version 0.8|8 bytes.|A timestamp without a time zone. Unit: microseconds.|4713 BC ~ 5874897 AD|2020-01-01 01:01:01.123456|
|CHAR\(n\)|Hologres version 0.8|A character string with a fixed length of n characters.|The data size must be less than or equal to 1 GB.|A character string with a fixed length.|-   abcd
-   efgh |
|VARCHAR\(n\)|Hologres version 0.8|A character string with a variable length that cannot exceed n characters.|The data size must be less than or equal to 1 GB.|A character string with a variable length of limited characters.|abcdefg|
|SERIAL \(auto-increment column\)|Hologres version 0.8|For more information, see PostgreSQL SERIAL.|N/A|N/A|N/A|
|SMALLINT|Hologres version 0.9|2 bytes.|Integers in a smaller range.|-32768~+32767|32767|
|JSON and JSONB|Hologres version 0.9|For more information, see JSON data type.|N/A|N/A|N/A|
|BYTEA|Hologres version 0.9|The value is variable in length. For more information, see [Binary Data Types](https://www.postgresql.org/docs/11/datatype-binary.html).|A binary string with a variable length.|The data size must be less than or equal to 1 GB.|N/A|
|BIT\(n\)|Hologres version 0.9|A binary string with a length of n bits.|A binary string with a fixed length.|The data size must be less than or equal to 1 GB.|N/A|
|VARBIT\(n\)|Hologres version 0.9|A binary string with a variable length that cannot exceed n bits.|A binary string with a length of limited bits.|The data size must be less than or equal to 1 GB.|N/A|
|INTERVAL|All Hologres versions|16 bytes.|N/A|-178000000 years~178000000 years|interval '1 year'|
|TIMETZ|Hologres version 0.9|12 bytes.|A timestamp with a time zone. Unit: microseconds.|00:00:00~24:00:00|12:00:00+08|
|TIME|Hologres version 0.9|8 bytes.|A timestamp without a time zone. Unit: microseconds.|00:00:00~24:00:00|12:00:00|
|INET|Hologres version 0.9|For more information, see [Endpoint type](http://www.postgres.cn/docs/11/datatype-net-types.html#DATATYPE-INET).|This data type allows you to save an IPv4 or IPv6 host IP address in a data domain.|N/A|192.168.100.128/25|
|MONEY|Hologres version 0.9|8 bytes. For more information, see [Currency type](http://www.postgres.cn/docs/11/datatype-money.html).|This data type allows you to store an amount of money in a currency with a fixed number of decimal digits.|-92233720368547758.08~+92233720368547758.07|$12.34|
|OID|Hologres version 0.9|4 bytes.|An object identifier in the numeric form.|N/A|1024|
|UUID|Hologres version 0.9|16 bytes.|A universally unique identifier with a fixed length of 128 bits.**Note:** Algorithms provided by the uuid-ossp module are not supported. For more information, see [UUID data type](http://postgres.cn/docs/11/datatype-uuid.html).

|00000000-0000-0000-0000-000000000000~ffffffff-ffff-ffff-ffff-ffffffffffff|a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11|

The following sample SQL statements show how to use the TIMESTAMP WITH TIME ZONE, DATE, and DECIMAL data types:

```
CREATE TABLE test_data_type (
  tswtz_column TIMESTAMP WITH TIME ZONE,
  date_column date,
  decimal_column decimal(38, 10),
  char_column char(20),
  varchar_volumn varchar(225)
);

INSERT INTO test_data_type
VALUES ('2004-10-19 08:08:08', '2004-10-19', 123.456, 'abcd', 'a');

SELECT * FROM test_data_type;
      tswtz_column      | date_column | decimal_column |     char_column      | varchar_volumn 
------------------------+-------------+----------------+----------------------+----------------
 2004-10-19 08:08:08+08 | 2004-10-19  | 123.4560000000 | abcd                 | a
(1 row)
```

The following sample SQL statements show how to use the BIT, VARBIT, and BYTEA data types:

```
// The BIT and VARBIT data types.

CREATE TABLE test (a BIT(3), b BIT VARYING(5));
INSERT INTO test VALUES (B'101', B'00');
INSERT INTO test VALUES (B'10', B'101');

ERROR:  bit string length 2 does not match type bit(3)

INSERT INTO test VALUES (B'10'::bit(3), B'101');
SELECT * FROM test;

  a  |  b
-----+-----
 101 | 00
 100 | 101

//BYTEA

SET bytea_output = 'escape';

SELECT 'abc \153\154\155 \052\251\124'::bytea;
     bytea
----------------
 abc klm *\251T
 
RESET bytea_output;  -- 'hex' by default

SELECT 'abc \153\154\155 \052\251\124'::bytea;
          bytea
--------------------------
 \x616263206b6c6d202aa954
(1 row)
```

## Array types

Hologres supports only one-dimensional arrays of the following types:

-   int4\[\]
-   int8\[\]
-   float4\[\]
-   float8\[\]
-   boolean\[\]
-   text\[\]

The following sample SQL statements show how to use the preceding array types:

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


## Data type mappings between MaxCompute and Hologres when you create a foreign table

The following table describes the data type mappings between MaxCompute and Hologres when you create a foreign table.

|MaxCompute data type|Hologres data type|Supported version|Description|
|--------------------|------------------|-----------------|-----------|
|-   STRING
-   VARCHAR

|TEXT|All Hologres versions|N/A|
|BIGINT|INT8|All Hologres versions|N/A|
|INT|-   INT4
-   INT

|All Hologres versions|N/A|
|FLOAT|-   FLOAT4
-   REAL

|All Hologres versions|N/A|
|DOUBLE|-   FLOAT
-   FLOAT8

|All Hologres versions|N/A|
|BOOLEAN|BOOL|All Hologres versions|N/A|
|DATETIME|TIMESTAMP WITH TIME ZONE|All Hologres versions|The DATETIME data type of MaxCompute specifies a date time in UTC+8. The time ranges from 0000-01-01 to 9999-12-31, which is accurate to milliseconds.|
|DECIMAL|NUMERIC|All Hologres versions|If no precision or scale is specified for the DECIMAL data type in MaxCompute, the default value is DECIMAL \(38,18\). The precision and scale are automatically converted when you create a foreign table by executing the [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md) statement.|
|TIMESTAMP|TIMESTAMP WITH TIME ZONE|Hologres version 0.8|-   The TIMESTAMP in MaxCompute. Valid values: 0000-01-01 00:00:00.000000000 to 9999-12-31 23.59:59.999999999. This is accurate to nanoseconds.
-   The TIMESTAMPTZ of Hologres is accurate to milliseconds.

Precision and scale are automatically converted.|
|CHAR\(n\)|By default, CHAR\(n\) is used.Hologres allows you to map CHAR\(n\) in MaxCompute to TEXT. You must set the `hg_enable_convert_type_for_foreign_table` parameter to true, and set the field type to TEXT when you create a foreign table.

|Hologres version 0.8|Entries of the CHAR\(n\) data type in MaxCompute are character strings with a fix length of n characters. Maximum value of n: 255. If you insert a character string that is shorter than the required length, Hologres increases the length by using spaces.|
|VARCHAR\(n\)|By default, VARCHAR\(n\) is used.Hologres allows you to map CHAR\(n\) in MaxCompute to TEXT. You must set the `hg_enable_convert_type_for_foreign_table` parameter to true, and set the field type to TEXT when you create a foreign table.

|Hologres version 0.8|Entries of the VARCHAR\(n\) data type in MaxCompute are character strings with a variable length that cannot exceed n characters. Valid values of n: 1 to 65535.|
|DATE|DATE|Hologres version 0.8|N/A|
|SMALLINT|By default, INT2 is used.Hologres allows you to map SMALLINT in MaxCompute to INT8. You must set the `hg_enable_convert_type_for_foreign_table` parameter to true, and set the field type to INT8 when you create a foreign table.

|All Hologres versions, in which the field type for Hologres version 0.8 is INT4 and the field type for Hologres version 0.9 is INT2.|N/A|
|TINYINT|By default, INT2 is used.Hologres allows you to map SMALLINT in MaxCompute to INT8. You must set the `hg_enable_convert_type_for_foreign_table` parameter to true, and set the field type to INT8 when you create a foreign table.

|All Hologres versions, in which the field type for Hologres version 0.8 is INT4 and the field type for Hologres version 0.9 is INT2.|N/A|
|CHAR|Not supported|Not supported|N/A|
|ARRAY<INT\>|INT4\[\]|Hologres version 0.8|N/A|
|ARRAY<BIGINT\>|INT8\[\]|Hologres version 0.8|N/A|
|ARRAY<FLOAT\>|FLOAT4\[\]|Hologres version 0.8|N/A|
|ARRAY<DOUBLE\>|FLOAT8\[\]|Hologres version 0.8|N/A|
|ARRAY<BOOLEAN\>|BOOLEAN\[\]|Hologres version 0.8|N/A|
|ARRAY<STRING\>|TEXT\[\]|Hologres version 0.8|N/A|
|BINARY|BYTEA|Hologres version 0.9|N/A|
|ARRAY<TINYINT\>|Not supported|Not supported|N/A|
|ARRAY<SMALLINT\>|Not supported|Not supported|N/A|

**Note:** If the MaxCompute table contains fields whose data types are not supported by Hologres, you can query other fields.

## Data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time

The following table describes the data type mappings between MaxCompute and Hologres when you create multiple foreign tables at a time.

|MaxCompute data type|Hologres data type|Supported version|
|--------------------|------------------|-----------------|
|-   STRING
-   VARCHAR

|TEXT|All Hologres versions|
|BIGINT|INT8|All Hologres versions|
|INT|-   INT4
-   INT

|All Hologres versions|
|FLOAT|-   FLOAT4
-   REAL

|All Hologres versions|
|DOUBLE|-   FLOAT
-   FLOAT8

|All Hologres versions|
|DATETIME|TIMESTAMP WITH TIME ZONE|All Hologres versions|
|DECIMAL|NUMERIC|All Hologres versions|
|BOOLEAN|BOOL|All Hologres versions|
|TIMESTAMP|TIMESTAMP WITH TIME ZONE|Hologres version 0.8|
|CHAR\(n\)|CHAR\(n\)|Hologres version 0.8|
|VARCHAR\(n\)|VARCHAR\(n\)|Hologres version 0.8|
|TINYINT|INT2|All Hologres versions, in which the field type for Hologres version 0.8 is INT4 and the field type for Hologres version 0.9 is INT2.|
|SMALLINT|INT2|All Hologres versions, in which the field type for Hologres version 0.8 is INT4 and the field type for Hologres version 0.9 is INT2.|
|ARRAY<INT\>|INT4\[\]|Hologres version 0.8|
|ARRAY<BIGINT\>|INT8\[\]|Hologres version 0.8|
|ARRAY<FLOAT\>|FLOAT4\[\]|Hologres version 0.8|
|ARRAY<DOUBLE\>|FLOAT8\[\]|Hologres version 0.8|
|ARRAY<BOOLEAN\>|BOOLEAN\[\]|Hologres version 0.8|
|ARRAY<STRING\>|TEXT\[\]|Hologres version 0.8|
|CHAR|Not supported|Hologres version 0.9|
|BINARY|Not supported|Not supported|
|ARRAY<TINYINT\>|Not supported|Not supported|
|ARRAY<SMALLINT\>|Not supported|Not supported|

## Data type mappings between Realtime Compute for Apache Flink and Hologres

The following table describes the data type mappings between Realtime Compute for Apache Flink and Hologres.

|Realtime Compute data type|Hologres data type|Supported version|
|--------------------------|------------------|-----------------|
|VARCHAR|TEXT|All Hologres versions|
|BIGINT|INT8|All Hologres versions|
|INT|-   INT4
-   INT

|All Hologres versions|
|SMALLINT|-   INT4
-   INT

|All Hologres versions|
|TINYINT|-   INT4
-   INT

|All Hologres versions|
|FLOAT|-   FLOAT4
-   REAL

|All Hologres versions|
|DOUBLE|-   FLOAT
-   FLOAT8

|All Hologres versions|
|BOOLEAN|BOOL|All Hologres versions|
|TIMESTAMP|TIMESTAMP WITH TIME ZONE|All Hologres versions|
|DATE|DATE|Hologres version 0.8|
|DECIMAL|NUMERIC|All Hologres versions|
|TIME|Not supported|Not supported|
|CHAR|Not supported|Not supported|
|BINARY|Not supported|Not supported|

