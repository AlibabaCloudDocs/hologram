# SET\_TABLE\_PROPERTY

This topic describes the syntax of the SET\_TABLE\_PROPERTY function in Hologres.

## Introduction

`set_table_property`: sets a property for a table, such as the index, distribution column, storage model, and time-to-live \(TTL\) of the table.

## Synopsis

```
CALL SET_TABLE_PROPERTY ( table_name, property, value )

where property in
    orientation
  clustering_key
  segment_key
  bitmap_columns
  dictionary_encoding_columns
  time_to_live_in_seconds
  distribution_key
```

## Parameters

-   ***table\_name***: the name of the table for which you want to set a property. The table name can be schema-qualified. The name can contain only case-insensitive letters, digits, and underscores `(_)`, and must start with a letter. If the name contains any special characters, enclose the name in double quotation marks `(" ")`.``````
-   ***property***: the name of the property to be set.
-   ***orientation property***: Indicates columnar or row store. Should be specified in the same transaction of creating table.
-   ***clustering\_key property***: Creating clustering index on specified columns. Should be specified in the same transaction of creating table .
-   ***segment\_key property***:Segment key is used to split the data into files. Specifying a column\(Ex: event\_time\) as segment key could benefit the queries which has a where clause on the segment key. Should be specified in the same transaction of creating table..
-   ***bitmap\_columns property***: Create bitmap index on specified columns. It benefits the filtering on the columns on which have it. Can be a stand alone command.
-   ***dictionary\_encoding\_columns property***: Create a mapping dictionary for specified columns. It benefits filtering comparison, aggregation, joins. Can be a stand alone command.
-   ***distribution\_key property*** :Declares the distribution policy. Should be specified in the same transaction of creating table.
-   ***time\_to\_live\_in\_seconds property*** :specifies the TTL of data in a table, which is represented in seconds. It must be a non-negative number, which can be an integer or a floating-point number. This property can be set in a single transaction.
-   ***value***: the value of the property. If the value contains a column name and the column name contains any uppercase letters, enclose the value in double quotation marks \(" "\).

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

