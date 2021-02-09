---
keyword: [Hologres, Clustering key]
---

# Hologres Clustering key最佳实践

本文将为您介绍在Hologres中，Clustering key的概念以及实现快速查询的设置方法。

## Clustering key概述

Clustering key即文件内聚簇布局，表示文件内按照指定key的顺序来排序数据。与传统数据库（MySQL、SQLServer）中的聚簇索引不同，Hologres的排序仅做到了文件内的排序，并非是全表数据的排序，因此在Clustering key上做排序操作仍然有一定的代价。每个表只能设置一次Clustering key。如果修改了Clustering key，需要重新进行数据导入。

-   对于行存表，如果设置了主键（PK），则PK默认就是Clustering key，如果不设置PK，则默认不设置Clustering key。
-   对于列存表，默认不设置Clustering key。

## 逻辑布局

Clustering key查询具备左匹配原则，不匹配则无法使用Clustering key查询加速。如下场景示例将为您说明Hologres中Clustering key的逻辑布局。

准备一张表，其字段分别包括Name、Date、Class。设置不同的字段为Clustering key，其最终的呈现结果也不同，具体如下图所示。

![逻辑布局](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8630872161/p239431.png)

-   设置Date为Clustering key，会将表内的数据按照Date进行排序。
-   设置Class和Date为Clustering key，会对表先按照Class排序后再按照Date进行排序。由于Clustering key查询具备左匹配原则，所以a、b、c的Clustering可以匹配a、b，但不能匹配b、c。

## 物理存储布局

Clustering key的物理存储布局如下图所示。

![物理布局](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0353152161/p239433.png)

## Clustering key应用

Clustering key主要适用于点查以及范围查询，对于filter操作有比较好的性能提升，即对于`where a = 1`或者`where a > 1 and a < 5`的场景加速效果比较好。需要特别注意的是，Clustering key和Bitmap可以同时设置，以达到最佳的点查性能。

同Bitmap相比，Hologres每个文件中按照一个一个的block来存储，Clustering key可以帮助快速定位到对应的block上，而Bitmap则用于block内部数据的快速定位。因此，Bitmap和Clustering key可以结合起来使用，以达到最佳的文件过滤效果。其异同点具体如下。

-   相同：都能对等值过滤查询有加速效果，具体示例如下所示。

    ```
    select a,b,c from test_b where b = 10;
    ```

-   不同：Clustering key每个表只能设置一个，对于range filter也有加速效果，而Bitmap每个表可以设置多个，对于range filter目前没有加速效果。因此，当用户有多种等值过滤查询场景时，可以考虑将使用次数最多的场景的key设置为Clustering key，剩余的过滤条件设置为Bitmap。具体示例如下所示：

    ```
    select a,b,c from test_b where a = 10;
    select a,b,c from test_b where a = 20;
    select a,b,c from test_b where b = 20;
    select a,b,c from test_b where c = 20;
    --建议设置a为clustering_key,a,b,c都设置为bitmap_column
    --call set_table_property('test_b', 'clustering_key', 'a');
    --call set_table_property('test_b', 'bitmap_columns', 'a,b,c');
    ```


## 使用示例

Bitmap和Clustering key可以结合起来使用，以达到最佳的文件过滤效果。具体示例如下：

```
select a,b,c from test_a where a > 10 and a < 100;
--建议设置a为clustering_key
--call set_table_property('test_a', 'clustering_key', 'a');

select a,b,c from test_b where b = 10;
--建议同时设置b为clustering_key和bitmap_column
--call set_table_property('test_b', 'clustering_key', 'b');
--call set_table_property('test_b', 'bitmap_columns', 'b');
```

