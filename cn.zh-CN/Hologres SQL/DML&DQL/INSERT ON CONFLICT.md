---
keyword: [INSERT ON CONFLICT, 插入重复数据的处理策略, Hologres]
---

# INSERT ON CONFLICT

INSERT ON CONFLICT语句用于在指定列插入某行数据时，如果主键存在重复的行数据，则对该数据执行更新或跳过操作。本文为您介绍在Hologres中INSERT ON CONFLICT语句的用法。

## 使用限制

-   `INSERT ON CONFLICT`语句的条件必须包含所有主键。
-   如果系统提示实例版本过低不支持该功能。您可以执行如下命令或[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例至最新版本。

    ```
    set hg_experimental_enable_insert_on_conflict = on; 
    ```


## 应用场景

`INSERT ON CONFLICT`命令适用于通过SQL方式导入数据的场景。

使用数据集成或Flink写入数据时，如果需要对主键重复的行数据执行更新或跳过操作，则需进行如下配置：

-   通过DataWorks的数据集成导入数据。

    数据集成已内置`INSERT ON CONFLICT`功能，该功能的实现原理请参见[Hologres Writer]()。同时，您需要进行如下配置：

    -   离线同步数据时选择**SDK（极速写入）**写入模式，并根据业务逻辑选择**写入冲突策略**为**忽略（Ignore）**或者**更新（Replace）**。
    -   实时同步数据时，**写入冲突策略**选择**忽略（Ignore）**或者**更新（Replace）**。
    **说明：** 同步数据时，Hologres的表均需要设置主键，才能更新数据。

-   通过Flink写入数据。

    通过Flink写入数据默认**写入冲突策略**使用**更新（Replace）**，但是需要您在Hologres建表时设置主键。详情请参见[t1997004.dita\#concept\_2481852](/cn.zh-CN/数据接入/实时写入/Flink/Flink全托管/Hologres结果表.md)。


## 命令格式

`INSERT ON CONFLICT`的语法格式如下。

```
INSERT INTO table_name [ AS alias ] [ ( column_name [, ...] ) ]
    {  VALUES ( { expression } [, ...] ) [, ...] | query }
    [ ON CONFLICT [ conflict_target ] conflict_action ]

where conflict_target is pk

    ON CONSTRAINT constraint_name

and conflict_action is one of:

    DO NOTHING
    DO UPDATE SET { column_name = { expression } |
                    ( column_name [, ...] ) = ( { expression } [, ...] ) |
                    ( column_name [, ...] ) = ( sub-SELECT )
                  } [, ...]
              [ WHERE condition ]
```

参数说明如下表所示。

|参数|描述|
|--|--|
|DO NOTHING|在指定列插入某行数据时，如果主键存在重复的行数据，则对该数据执行跳过操作。|
|DO UPDATE|在指定列插入某行数据时，如果主键存在重复的行数据，则对该数据执行更新操作。|
|expression|对应列执行的相关表达式，您可以参考Postgres来设置表达式，详情请参见[INSERT ON CONFLICT](https://www.postgresql.org/docs/11/sql-insert.html)。

例如，表达式为`excluded.column_name`。其中，excluded为插入的源数据表的别名，column\_name为插入数据至目标表指定列的列名称。假设column\_name为源数据插入至目标表的第N列，则excluded.column\_name为相应源数据表的第N列，当使用excluded.\*时，表示择所有列，需要保证插入目标列的顺序与建表的DDL顺序一致。 |

## 使用示例

-   `INSERT ON CONFLICT`语句的示例用法：

    ```
    create table conflict_1(
      a int not null primary key, 
      b int ,
      c int);
    insert into conflict_1 values
    (1,1,1),
    (2,3,4);
    
    create table conflict_2(
      a int not null primary key, 
      b int ,
      c int);
    insert into conflict_2 values(1,5,6);
    --主键相同时，将表conflict_2的某列数据更新到表conflict_1中。
    insert into conflict_1（a,b) select a,b from conflict_2 on conflict(a) do update set b = excluded.b; 
    
    --主键相同时，将表conflict_2的某一行数据全部插入至表conflict_1中。
    insert into conflict_1 values(2,7,8) on conflict(a) do update set b = excluded.b, c = excluded.c where conflict_1.c = 4; 
    
    --主键相同时，向表conflict_1插入表conflict_2的数据，系统直接跳过表conflict_2的数据（即插入数据失败）。
    insert into conflict_1 select * from conflict_2 on conflict(a) do nothing; 
    
    --do nothing不指定冲突列时，默认冲突列为主键。
    insert into conflict_1 select * from conflict_2 on conflict do nothing; 
    
    --指定主键constrain的名称。
    insert into conflict_1 select * from conflict_2 on conflict on constraint conflict_1_pkey do update set a = excluded.a;
    
    ---更新整行数据。
    insert into tmp1_on_conflict values(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*); 
    ```

    ![conflict](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2771863061/p174954.png)

-   行存表`INSERT ON CONFLICT`语句的优化：

    Hologres对行存表的更新场景实行了优化，建议您在使用时将UPDATE列的顺序与INSERT的顺序保持一致，并且更新为整行更新。

    ```
    insert into conflict_1(a,b,c) select a,b,c from conflict_2 on conflict (a) do update set a = exculded.a, b = excluded.b, c= excluded.c;
    
    insert into fonflict_1(a,b,c) select a,b,c from conflict_2 on conflict (a) do update (a,b,c) = Row(excluded.*)
    ```


## 常见报错

-   问题现象

    对数据源执行`INSERT ON CONFLICT`语句时出现如下报错。

    ![insert on conflict 常见报错](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5905108061/p200644.png)

-   问题原因

    Hologres兼容PostgreSQL，使用的也是标准PostgreSQL语法。在标准的PostgreSQL语义中，对数据源执行`INSERT ON CONFLICT`语句时，数据源不能包含重复数据，如果包含重复数据则会产生上述报错。

    **说明：** 数据源重复是指待插入的数据中包含重复数据，不是指待插入的数据与表里的数据重复。

    使用`INSERT ON CONFLICT`语句插入数据时包含重复数据，示例语句如下。

    ```
    insert into tmp1_on_conflict values(1,2,3),(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*);
    ```

-   解决方法

    如果数据源包含重复数据，可以配置`set hg_experimental_affect_row_multiple_times_keep_first = on;`，保留重复数据的第一条数据。


