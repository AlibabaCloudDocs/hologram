---
keyword: [修改分区表, Hologres, DDL]
---

# ALTER PARTITION TABLE

ALTER PARTITION TABLE语句用于修改分区表。本文为您介绍ALTER PARTITION TABLE的用法。

## 语法

Hologres支持以下3种修改分区表的操作。

```
ALTER TABLE [IF EXISTS] table_name RENAME to new_table_name;
ALTER TABLE [IF EXISTS] table_name ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>);
ALTER TABLE [IF EXISTS] table_name DETACH PARTITION paritition_name;
```

参数说明如下表所示。

|参数|描述|
|--|--|
|`RENAME`|重命名分区表。|
|`ATTACH PARTITION new_partition_name FOR VALUES in (<string_literal>)`|用于将当前表绑定为目标表的分区。说明如下：-   绑定分区的规范必须与目标表的分区策略和分区键相对应。
-   需要绑定的表与目标表的列数量必须相同，并且列类型必须匹配。
-   需要绑定的表必须具有目标表的所有NOT NULL约束。

如果您添加了一个不接受NULL值的列表分区，除非它是一个表达式， 否则您需要添加NOT NULL约束至分区键列。 |
|`DETACH PARTITION partition_name`|分离目标表的指定分区。被分离的分区作为独立表继续存在，但不再与目标表相关联。 |

## 示例

修改分区表的示例语句如下。

```
ALTER TABLE holo_test RENAME to my_holo_test;//修改分区表的名称。

ALTER TABLE holo_table ATTACH PARTITION my_table FOR VALUES in ('2015');//添加my_table为holo_table的分区表。

ALTER TABLE all_test DETACH PARTITION holo_test; //将holo_test从all_test分区表中解除绑定，分离为独立表。
            
```

