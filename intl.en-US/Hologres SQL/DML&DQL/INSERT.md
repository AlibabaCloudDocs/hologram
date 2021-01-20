# INSERT

This topic describes the syntax of the INSERT statement in Hologres.

## Description

INSERT: inserts new rows to a table. You can insert one or more rows specified by value expressions, or zero or more rows resulting from a query.

## Synopsis

```
INSERT INTO table [( column [, ...] )]
   VALUES ( {expression}  [, ...] ) 
   [, ...] | query}
```

The target column names can be listed in any order. If no list of column names is given at all, the default is all the columns of the table in their declared order, or the first N column names if only N columns are supplied by the VALUES clause or query. The values supplied by the VALUES clause or query are associated with the explicit or implicit column list left-to-right.

Each column not present in the explicit or implicit column list will be filled with a default value, either its declared default value or null if no default value is declared. If the expression for any column is not of the correct data type, automatic type conversion will be attempted.

## Parameters

table\_name: the name \(optionally schema-qualified\) of an existing table.

alias: a substitute name for table\_name. When an alias is provided, it completely hides the actual name of the table.

column\_name: the name of a column in the table named by table\_name. The column name can be qualified with a subfield name or array subscript, if needed. Inserting to only some fields of a composite column leaves the other fields null.

expression: an expression or value to assign to the corresponding column.

query: a query \(SELECT statement\) that supplies the rows to be inserted.

## Examples

Currently, Hologres allows you to execute an INSERT statement in the following ways:

1.  INSERT INTO VALUES

    ```
    INSERT INTO rh_holo2mysqltest (cate_id, cate_name) VALUES
        (3, 'true'),
        (3, 'fale'),
        (3, 'trxxue'),
        (3, 'x'),
        (4, 'The Dinner Game');
    ```

2.  INSERT INTO SELECT

    ```
    insert into test2
    select 2,'two';
    ```


