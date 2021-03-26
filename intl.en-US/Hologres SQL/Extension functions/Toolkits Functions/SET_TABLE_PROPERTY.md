---
keyword: [SET\_TABLE\_PROPERTY, set table properties, Hologres]
---

# SET\_TABLE\_PROPERTY

This topic describes how to use the SET\_TABLE\_PROPERTY function in Hologres.

## Overview

The SET\_TABLE\_PROPERTY function is used to set table properties. For example, you can use this function to create indexes, set distribution keys, specify whether a table is row-oriented or column-oriented, or set the time to live \(TTL\) of a table. To modify or update data in a table or delete a table, execute the [ALTER TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/ALTER TABLE.md) or [DROP TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/DROP TABLE.md) statement.

## Syntax

```
CALL SET_TABLE_PROPERTY ( table_name, property, value )
WHERE PROPERTY IN
    orientation
  clustering_key
  segment_key
  bitmap_columns
  dictionary_encoding_columns
  time_to_live_in_seconds
  distribution_key
```

## Parameters

The following table describes the parameters in the SET\_TABLE\_PROPERTY function.

|Parameter|Description|
|---------|-----------|
|table\_name|The name of the table. The table name can be schema-qualified.The name can contain only lowercase letters, uppercase letters, digits, and underscores \(\_\). It must start with a letter.

If the name contains special characters, enclose the name in double quotation marks \(`" "`\). Uppercase letters are treated as lowercase letters because the value is not case-sensitive. |
|property|The name of the property.|
|orientation|The storage type of tables in the specified database. Two storage types are supported: column-oriented storage and row-oriented storage.Specify this parameter only in the same transaction as the CREATE TABLE statement. |
|clustering\_key|A clustered index that you want to create for a specified field.Specify this parameter only in the same transaction as the CREATE TABLE statement. |
|segment\_key|A segment key that you want to create based on one or more specified fields. For example, you can specify the field that contains time data as the segment key.If the segment key is involved in query conditions, Hologres can find the storage location of data based on the segment key.

Specify this parameter only in the same transaction as the CREATE TABLE statement. |
|bitmap\_columns|A bitmap index that you want to create for a specified field. The bitmap index can be used to filter data in a segment. This parameter can be specified separately.|
|dictionary\_encoding\_columns|A dictionary mapping that you want to create for the values of a specified field.Dictionary encoding can convert string comparisons into numeric comparisons. This can accelerate queries, such as queries that involve GROUP BY and FILTER statements. This parameter can be specified separately. |
|distribution\_key|The distribution strategy of tables in the specified database.Specify this parameter only in the same transaction as the CREATE TABLE statement. |
|time\_to\_live\_in\_seconds|The TTL of the table. Unit: seconds.The value of this parameter must be a non-negative integer or floating-point number. This parameter can be specified separately. |
|value|The value of the property. If the value of this parameter contains a field name and the field name contains uppercase letters, enclose the value in double quotation marks \(`" "`\).|

## Examples

```
BEGIN;
CREATE TABLE ORDERS ( 
  O_ORDERKEY              INTEGER NOT NULL,
  O_CUSTKEY        INTEGER NOT NULL,
  O_ORDERSTATUS    TEXT NOT NULL,
  O_TOTALPRICE     DECIMAL(15,2) NOT NULL,
  O_ORDERDATE      DATE NOT NULL,
  O_ORDERPRIORITY  TEXT NOT NULL,  
  O_CLERK          TEXT NOT NULL, 
  O_SHIPPRIORITY   INTEGER NOT NULL,
  O_COMMENT        TEXT NOT NULL);
CALL SET_TABLE_PROPERTY ('ORDERS', 'clustering_key', 'O_ORDERKEY:asc,O_CUSTKEY:asc');
CALL SET_TABLE_PROPERTY ('ORDERS', 'segment_key', 'O_ORDERDATE');
CALL SET_TABLE_PROPERTY ('ORDERS', 'bitmap_columns', 'O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY');
CALL SET_TABLE_PROPERTY ('ORDERS', 'dictionary_encoding_columns', 'O_ORDERSTATUS,O_ORDERPRIORITY,O_CLERK,O_SHIPPRIORITY');
CALL SET_TABLE_PROPERTY ('ORDERS', 'time_to_live_in_seconds', '172800');
COMMIT;
```

