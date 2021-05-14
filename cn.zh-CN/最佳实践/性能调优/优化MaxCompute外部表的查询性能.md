---
keyword: [优化性能, MaxCompute外部表, 最佳实践]
---

# 优化MaxCompute外部表的查询性能

本文为您介绍在Hologres中如何优化查询MaxCompute外部表数据的性能。

Hologres与MaxCompute在底层资源无缝打通，您可以通过以下方式加速查询MaxCompute的数据：

-   **新建外部表直接加速查询**

    在Hologres中新建外部表，即可直接加速查询外部表数据。无需数据导入导出、无冗余存储。该方式适用于单次查询的数据量小于100 GB的表（与查询字段的大小无关）。

-   **导入数据至Hologres进行加速查询**

    当需要大量分析计算外部表数据并建立与内部表的连接时，您可以在Hologres中新建内部表并导入外部表数据。根据业务需求，为内部表指定合适的Distribute Key索引属性，加快查询速度。

    导入外部表数据相比新建外部表方式查询速度更快。该方式适用于单次查询的数据量大于等于100 GB的表，以及使用复杂查询、包含索引查询、更新数据或插入数据的场景。

    导入MaxCompute外部表数据至Hologres的操作请参见[使用SQL导入MaxCompute的数据至Hologres](/cn.zh-CN/数据接入/离线同步/MaxCompute/使用SQL导入MaxCompute的数据至Hologres.md)。


您还可以根据实际业务需求，通过优化查询语句、修改MaxCompute数据源表、合理配置资源和参数，优化查询外部表数据的性能。

## 优化查询语句

使用如下方式优化查询语句，避免查询外部表数据时扫描全表：

-   查询数据时使用`select a from xx`语句查询指定内容，不推荐使用`select * from xx`。
-   增加过滤分区的条件或减少扫描的分区数，实现减少扫描的数据量。

## 修改MaxCompute数据源表

通过修改MaxCompute数据源表优化查询数据的性能，方式如下：

-   **转换MaxCompute表为Hash Clustering表**

    Hash Clustering表可以优化Bucket Pruning、Aggregation以及存储。

    创建MaxCompute表时，如果使用`Clustered By`指定了Hash Key，则MaxCompute对指定列进行Hash运算，并分散Hash值至各个Bucket中。请选择重复键值少的列作为Hash Key。

    如果没有指定Hash Key，则使用如下语句指定。

    ```
    ALTER TABLE table_name [CLUSTERED BY (col_name [, col_name, ...]) [SORTED BY (col_name [ASC | DESC] [, col_name [ASC | DESC] ...])] INTO number_of_buckets BUCKETS]
    ```

    `ALTER TABLE`语句适用于指定存量表的Hash Key。

    新增聚集属性后，新的分区数据写入MaxCompute时直接执行Hash Clustering计算。同时，您可以使用`INSERT OVERWRITE`语句对原有的源表数据执行Hash Clustering计算。

    **说明：**

    -   Hash Clustering表不支持`INSERT INTO`语句，您需要使用`INSERT OVERWRITE`语句添加数据。
    -   Hash Clustering表不支持通过Tunnel方式上传数据至Range Cluster表。
-   **合并小文件**

    当MaxCompute中的小文件数量较多时，会降低查询表数据的速度。

    您可以在Hologres中执行如下语句，查看Query命中的文件数量：

    ```
    explain analyze <query>;
    ```

    查询结果中的file\_count表示MaxCompute中的文件数。如果当前小文件数量较多，影响查询速度，您可以在MaxCompute中对小文件进行合并，具体操作请参见[合并小文件](/cn.zh-CN/常见问题/优化诊断.md)。


## 合理配置资源

成功购买Hologres实例后，系统会按照实例规格分配资源。您可以联系技术支持或[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)，根据业务需求合理配置资源。

## 合理配置参数

通过合理配置如下参数，优化查询外部表数据的性能（一般情况下，如下参数无需调整）。

```
--调整每次读取MaxCompute表batch的大小，默认8192。
set hg_experimental_query_batch_size = xx

--设置MaxCompute表访问切分spilit的数目，可以调节并发数目，默认64MB，当表很大时需要调大，避免过多的split影响性能。
set hg_experimental_foreign_table_split_size = xx 

--设置MaxCompute执行的最大并发度，默认为128，建议数值设置小一些，避免一个Query影响其它Query，导致系统繁忙导致报错。
set hg_experimental_foreign_table_executor_max_dop = 64;
```

