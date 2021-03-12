---
keyword: [HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY, Hologres, 工具函数]
---

# HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY

HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY用于获取指定键或组合键的数据片ID（Shard ID）。本文为您介绍在交互式分析Hologres中HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY的用法。

## 命令语法

```
SELECT HG_SHARD_ID_FOR_DISTRIBUTION_KEY ( 'schemaname.tablename' , columnname [...] ] ) 
FROM schemaname.tablename
```

## 参数说明

参数说明如下表所示。

|参数|描述|
|--|--|
|schemaname|对应schema名称。|
|tablename|需要获取Shard ID的表名称。|
|columnname|单column或组合column的表名，作为计算所落到的数据分片ID（Shard ID）上。|

## 使用示例

您可以使用如下示例步骤获取指定键或组合键的数据片ID（Shard ID）。

1.  安装插件。您可以执行以下语句安装插件。该命令是DB级别的，在一个DB内执行一次即可。切换到新的DB需要再次执行。

    ```
    create extension hg_toolkit;
    ```

2.  获取lineitem表中l\_shipmode列的Shard ID。

    ```
    SELECT  HG_SHARD_ID_FOR_DISTRIBUTION_KEY ( 'public.lineitem', l_shipmode)
    FROM public.lineitem ;
    ```

3.  获取执行结果。

    ```
     hg_shard_id_for_distribution_key 
    ----------------------------------
                                   24
                                   24
                                   24
                                   24
                                   31
                                   24
                                   12
                                   25
                                   25
                                   25
    (10 rows)
    ```


