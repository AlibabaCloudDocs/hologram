---
keyword: [创建视图, Hologres]
---

# VIEW

Hologres支持基于内部表及外部表创建视图VIEW，您可以使用单张表、多张表或者其他视图来创建视图。本文为您介绍在Hologres中如何创建视图。

## 创建视图

创建视图的语法格式如下。

```
CREATE [TEMP | TEMPORARY] VIEW view_name AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

如果TEMPORARY或者TEMP被指定，视图会被创建为一个临时视图。当前会话结束时将会自动删掉临时视图。

## 创建内部表视图

1.  创建内部表，示例语句如下。

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

2.  基于内部表创建视图并查询表数据，示例语句如下。

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


## 创建外部表视图

1.  创建外部表，示例语句如下。

    ```
    create foreign table if not exists t1_foreign (
      amount decimal(10, 2), 
      rate decimal(10, 2)) 
      server odps_server options(project_name '<projectname>', table_name '<odps_name>')
      );
      
    select * from t1_foreign limit 2;
    ```

2.  基于外部表创建视图并查询表数据，示例语句如下。

    ```
    create view view1 as select * from t1_foreign;
    
    select * from view1 limit 2;
     amount | rate
    --------+-------
      12.12 | 13.13
      14.14 | 15.15
    ```


## 创建内部表及外部表的联合视图

1.  基于内部表和外部表创建联合视图并查询表数据，示例语句如下。

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


## 删除视图

删除视图的语法格式如下。

```
drop view <view_name>;
```

