---
keyword: [INSERT ON CONFLICT, 插入重复数据的处理策略, Hologres]
---

# INSERT ON CONFLICT

INSERT ON CONFLICT语句用于在指定列插入某行数据时，如果主键存在重复的行数据，则对该数据执行更新或跳过操作。

## 使用限制

-   `INSERT ON CONFLICT`语句的条件必须包含所有主键。
-   如果系统提示不支持该功能，是因为实例版本太低。您可以执行如下命令或[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例至0.8版本。

    ```
    set hg_experimental_enable_insert_on_conflict = on; 
    ```


## 应用场景

`INSERT ON CONFLICT`命令适用于通过SQL方式导入数据的场景。

使用数据集成或Flink写入数据时，如果需要对主键重复的行数据执行更新或跳过操作，则需进行如下配置：

-   通过DataWorks的数据集成导入数据

    数据集成已内置`INSERT ON CONFLICT`功能，该功能的实现原理请参见[HoloWrtier](https://help.aliyun.com/knowledge_detail/158321.html?spm=a2c4g.11186631.2.11.17e6f853Ju0Tz0)。同时，您需要进行如下配置：

    -   离线同步数据时选择SDK（极速写入）写入模式，并根据业务逻辑选择写入冲突策略为忽略或者更新。
    -   实时同步数据时写入冲突策略选择忽略或者覆盖。
    **说明：** 同步数据时，Hologres的表均需要设置主键，才能更新数据。

-   通过Flink写入数据

    通过Flink写入数据默认使用更新策略，但是需要您在Hologres建表时设置主键。详情请参见[Flink](https://help.aliyun.com/document_detail/152662.html?spm=a2c4g.11186623.6.580.682478ceInXNJ1)。


## 语法

`INSERT ON CONFLICT`语句的语法格式如下。

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
|AS alias|插入的源数据表的别名。|
|column\_name|插入数据至目标表指定列对应的列名称。|

## 示例

`INSERT ON CONFLICT`语句的示例用法如下。

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

insert into conflict_1 select * from conflict_2 on conflict(a) do update set b = excluded.b; //主键相同时，将表conflict_2的某列数据更新到表conflict_1中。

insert into conflict_1 values(2,7,8) on conflict(a) do update set b = excluded.b, c = excluded.c where conflict_1.c = 4; //主键相同时，将表conflict_2的某一行数据全部插入至表conflict_1中。

insert into conflict_1 select * from conflict_2 on conflict(a) do nothing; //主键相同时，向表conflict_1插入表conflict_2的数据，系统直接跳过表conflict_2的数据（即插入数据不成功）。

insert into conflict_1 select * from conflict_2 on conflict do nothing; //do nothing不指定冲突列时，默认冲突列为主键。

insert into conflict_1 select * from conflict_2 on conflict on constraint conflict_1_pkey do update set a = excluded.a; //指定主键constrain的名称。

insert into tmp1_on_conflict values(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*); //更新整行数据。
```

更多用法请参见PG文档[Insert on conflict](https://www.postgresql.org/docs/11/sql-insert.html)。

**说明：** PostgresSQL中，执行`DO UPDATE`操作时，数据源不能包含重复数据。

如果数据源包含重复数据，您需要配置`set hg_experimental_affect_row_multiple_times_keep_first = on;`，保留数据源重复数据的第一条。如果不配置上述语句，则会报错`insert into tmp1_on_conflict values(1,2,3),(1,2,3) on conflict(a) do update set (a, b ,c )= ROW(excluded.*);`。

![conflict](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/2771863061/p174954.png)

