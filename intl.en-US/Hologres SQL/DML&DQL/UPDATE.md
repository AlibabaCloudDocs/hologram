---
keyword: [UPDATE, update table data, Hologres]
---

# UPDATE

You can execute the UPDATE statement to update the values of the specified columns in all rows that meet the specified condition in the specified table. This topic describes how to use the UPDATE statement in Hologres.

## Limits

-   You cannot execute the UPDATE statement to update shard keys.
-   You can execute the UPDATE statement to update child partitioned tables but not parent partitioned tables.

## Syntax

The UPDATE statement uses the following syntax:

```
UPDATE table [ * ] [ [ AS ] alias ]
    SET { column = { expression } |
          ( column [, ...] ) = ( { expression } [, ...] ) } [, ...]
    [ FROM from_list ]
    [ WHERE condition ]
```

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|alias|The substitute name for the table to update.|
|expression|The expression to assign an updated value to a column.|
|condition|The condition that rows to be updated must meet.|

## Examples

The following example shows you how to use the UPDATE statement in Hologres:

```
CREATE TABLE update_test (
  a text primary key, 
  b int not null, 
  c text not null, 
  d text);  

INSERT INTO update_test VALUES ('b1', 10, '', '');

UPDATE update_test SET b = b + 10 where a = 'b1';
UPDATE update_test SET c = 'new_' || a, d = null where b = 20;
UPDATE update_test SET (b,c,d) = (1, 'test_c', 'test_d'); 

CREATE TABLE tmp(a int);
INSERT INTO tmp VALUES (2);
UPDATE update_test SET b = tmp.a FROM tmp;
```

For more information about the UPDATE statement, see [PostgreSQL Documentation](https://www.postgresql.org/docs/9.1/sql-update.html).

