---
keyword: [修改分区表, Hologres, DDL]
---

# ALTER PARTITION TABLE

ALTER PARTITION TABLE语句用于修改分区表。本文为您介绍ALTER PARTITION TABLE的用法。

## 命令格式

Hologres支持以下3种修改分区表的操作。

```
ALTER TABLE [IF EXISTS] table_name RENAME to new_table_name;
ALTER TABLE [IF EXISTS] table_name ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>);
ALTER TABLE [IF EXISTS] table_name DETACH PARTITION paritition_name;
```

## 参数说明

修改分区表的示参数说明如下表所示。

|参数|描述|
|--|--|
|`RENAME`|重命名分区表。|
|`ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>)`|用于将当前表绑定为目标表的分区。说明如下：-   绑定分区的规范必须与目标表的分区策略和分区键相对应。
-   需要绑定的表与目标表的列数量必须相同，并且列类型必须匹配。
-   需要绑定的表必须具有目标表的所有NOT NULL约束。

如果您添加了一个不接受NULL值的列表分区，除非它是一个表达式， 否则您需要添加NOT NULL约束至分区键列。 |
|`DETACH PARTITION partition_name`|分离目标表的指定分区。被分离的分区作为独立表继续存在，但不再与目标表相关联。 |

## 使用示例

修改分区表的示例语句如下。

```
--修改分区表的名称
alter table holo_test rename to my_holo_test;

--添加my_table为holo_table的分区表
alter table holo_table attach partition my_table for values in ('2015');

--将holo_test从all_test分区表中解除绑定，分离为独立表
alter table all_test detach partition holo_test; 
            
```

您也可以参见如下完整示例进行分区表替换。

```
---创建新的分区表
begin; 
drop table if exists "table_20210101_new";
create table "table_20210101_new" (
  "colA" integer not null,
  "colB" text not null,
  "colC" numeric not null,
  "ds" text not null,
  "process_time" timestamptz not null default now()
);
call_set_table_property('table_20210101_new', 'orientation','column');
call_set_table_property('table_20210101_new', 'distribution_key','colA');
call_set_table_property('table_20210101_new', 'event_time_column','process_time');
commit;

---导入数据
insert into "table_20210101_new" select * from ...;

---替换子表
begin;
--解除绑定子分区为独立的表
alter table table_parent DETACH PARTITION table_20210101;
--重命名解绑定后独立的表
alter table table_20210101 RENAME to table_20210101_backup;
--把临时表更名为分区表名
alter table table_20210101_new RENAME to table_20210101;
--绑定新的分区表
alter table table_parent ATTACH PARTITION table_20210101 FOR VALUES in ("20210101");
commit;
```

