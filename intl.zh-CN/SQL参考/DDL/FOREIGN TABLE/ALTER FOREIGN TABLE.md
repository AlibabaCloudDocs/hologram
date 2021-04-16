---
keyword: [修改外部表, DDL, Hologres]
---

# ALTER FOREIGN TABLE

ALTER FOREIGN TABLE语句用于修改外部表。本文为您介绍如何使用ALTER FOREIGN TABLE，为外部表重命名、增加列及删除列。

## 使用限制

Hologres仅支持为外部表重命名、增加列及删除列等修改外部表操作。

## 重命名

语法如下。

```
ALTER FOREIGN TABLE [ IF EXISTS ] name RENAME TO new_name;    
```

示例SQL语句如下。

```
ALTER FOREIGN TABLE test RENAME TO new_test_table; 
```

## 增加列

使用Hologres创建外部表查询数据时，当MaxCompute表增加字段后，Hologres不会自动更新Schema，需要您在Hologres中手动增加列。

**说明：** 大集群暂不支持使用如下语法增加列，关于大集群增加列的操作请参见[IMPORT FOREIGN SCHEMA](/intl.zh-CN/SQL参考/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。

语法如下。

```
ALTER FOREIGN TABLE IF EXISTS table_name ADD COLUMN new_column_name data_type;
```

示例SQL语句如下。

```
ALTER FOREIGN TABLE bank
 ADD COLUMN  cons_conf_idx float8,
 ADD COLUMN  euribor3m float8;
```

## 删除列

语法如下。

```
ALTER FOREIGN TABLE IF EXISTS table_name DROP COLUMN column_name;
```

示例SQL语句如下。

```
ALTER FOREIGN TABLE bank
 DROP COLUMN  cons_conf_idx,
 DROP COLUMN  euribor3m;
```

