---
keyword: [create a view, Hologres]
---

# VIEW

Hologres allows you to create a view based on an internal table or a foreign table. You can create a view by using a single table, multiple tables, or other views. This topic describes how to create a view in Hologres.

## Create a view

Create a view. Syntax:

```
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

## Create a view by using an internal table

1.  Create an internal table. Sample statements:

    ```
    create table t2_holo (
      amount decimal(10, 2), 
      rate decimal(10, 2)
    );
    insert into t2_holo values 
    (12.12,13.13),
    (14.14,15.15),
    (16.16,17.17),
    (17.1,17),
    (18.01,19);
    ```

2.  Create a view based on the internal table and query data in the table. Sample statements:

    ```
    create view view2 as select * from t2_holo;
    
    select * from view2;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
      17.10 | 17.00
      18.01 | 19.00
    (5 rows)
    ```


## Create a view by using a foreign table

1.  Create a foreign table. Sample statements:

    ```
    create foreign table if not exists t1_foreign (
      amount decimal(10, 2), 
      rate decimal(10, 2)) 
      server odps_server options(project_name '<projectname>', table_name '<odps_name>')
      );
      
    select * from t1_foreign limit 2;
    ```

2.  Create a view based on the foreign table and query data in the table. Sample statements:

    ```
    create view view1 as select * from t1_foreign;
    
    select * from view1 limit 2;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
    ```


## Create a federated view by using both internal and foreign tables

1.  Create a federated view based on both internal and external tables and query data in the tables. Sample statements:

    ```
    create view view3 as select * from t2_holo union all  select * from t1_foreign;
    
    select * from view3;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
      17.10 | 17.00
      18.01 | 19.00
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
      12.12 | 13.13
      14.14 | 15.15
      16.16 | 17.17
       17.1 |    17
      18.01 |    19
    (20 rows)
    ```


## Delete a view

Delete a view. Syntax:

```
drop view <view_name>;
```

