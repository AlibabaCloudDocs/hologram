---
keyword: [DELETE, delete table data, Hologres]
---

# DELETE

You can execute the DELETE statement to delete rows that meet the specified condition from the specified table. This topic describes how to use the DELETE statement in Hologres.

## Limits

You cannot execute the DELETE statement to delete data from a parent partitioned table if its child partitioned tables contain data. To delete data from a parent partitioned table, you must delete data from relevant child partitioned tables first.

## Syntax

The DELETE statement uses the following syntax:

```
DELETE FROM table_name [ * ] [ [ AS ] alias ]
    [ WHERE condition  ]
```

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|alias|The substitute name for the table to delete rows from.|
|condition|The condition that rows to be deleted must meet.|

## Examples

The following example shows you how to use the DELETE statement in Hologres:

```
CREATE TABLE delete_test (
    id INT PRIMARY KEY,
    a INT,
    b text 
);

INSERT INTO delete_test VALUES 
(1, 10, 'a'),
(2, 30, 'b'),
(3, 50,  ''),
(4, 70, null);

DELETE FROM delete_test AS dt WHERE dt.a = 10;
DELETE FROM delete_test AS dt WHERE dt.b is null;
DELETE FROM delete_test AS dt WHERE dt.b='';
```

For more information about the DELETE statement, see [PostgreSQL Documentation](https://www.postgresql.org/docs/10/sql-delete.html).

