---
keyword: [Hologres, MaxCompute]
---

# 将Hologres作为MaxCompute的外部表进行访问

MaxCompute与Hologres在底层无缝打通，您可以在MaxCompute中，将Hologres的表定义为MaxCompute的外部表直接进行访问。通过MaxCompute的JDBC驱动方式查询Hologres数据源的数据，该方式无数据冗余，无需数据导入导出。

## 使用限制

在MaxCompute中创建Hologres外部表的使用限制如下：

-   MaxCompute不支持对创建的Hologres外部表进行更新（Update）、删除（Delete）操作。
-   Hologres的分区表和MaxCompute的分区表没有对应关系。MaxCompute的外部表不支持分区。

## 应用场景

在MaxCompute中创建Hologres外部表的应用场景如下：

-   Hologres作为加工结果表：在MaxCompute中进行数据加工ETL，将加工后的数据直接写入Hologres外部表，再通过Hologres对外提供分析查询、在线服务，减少数据集成的调度和配置工作，开发效率更高。
-   Hologres作为加工维度表：Hologres具备实时更新的能力，因此适合作为维度表的统一存储，提供实时动态更新能力，供MaxCompute、Flink等数据处理系统访问。只需要一份维度数据存储，无冗余，保证了维度数据的一致性。事实表保存在MaxCompute中，维度表保存在Hologres中，MaxCompute在ETL加工过程中关联维度表数据进行关联分析。

以上场景的具体使用操作请参见MaxCompute文档[Hologres外部表](/cn.zh-CN/开发/外部表/Hologres外部表.md)。

