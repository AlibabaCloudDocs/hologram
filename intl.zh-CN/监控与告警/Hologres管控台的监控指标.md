---
keyword: [Hologres, 监控指标]
---

# Hologres管控台的监控指标

Hologres管理控制台的监控告警页面，为您提供了实例的资源管理服务。您可以在该页面查看当前实例的资源使用及SQL语句的执行情况，及时识别系统报错并处理实例异常。本文为您介绍相关指标的含义。

Hologres管理控制台提供的监控指标如下：

-   [CPU使用率（%）](#section_i9a_t9b_jvs)
-   [内存使用率（%）](#section_nql_nm4_nzz)
-   [实例存储用量（字节）](#section_qsi_n05_rgr)
-   [连接数（个）](#section_qbs_kr4_jhf)
-   [QPS（个/秒）](#section_4d4_8y3_c1s)
-   [Query延迟（毫秒）](#section_8um_xe1_ddb)
-   [实时导入RPS（记录/秒）](#section_y7x_930_cre)

## CPU使用率（%）

**CPU使用率**指实例的CPU综合使用率。

Hologres的计算资源采用预留模式，即使没有执行查询操作，后台也会存在运行的进程，此时CPU使用率不为零属于正常现象。

Hologres可以充分发挥CPU多核并行计算的能力。通常，单个查询可以迅速将CPU使用率提高至100%，这表明计算资源得到了充分利用。

如果CPU使用率长期接近100%，表明实例的负载非常高，此时，CPU资源可能会影响系统的运行。如果您通过分析具体的业务场景，确定是资源不足影响了系统的运行，则可以对实例资源进行扩容，以便承载更大数据量的复杂查询。

## 内存使用率（%）

**内存使用率**指实例的内存综合使用率。

Hologres的内存资源采用预留模式，即使没有执行查询操作，也会有部分Meta或Index元数据加载到内存中，该类元数据用于提升计算速度。此时，内存使用率可能会达到30%~40%左右。

如果内存使用率长期接近100%，通常会影响系统的运行，您需要对实例的内存资源进行扩容，否则可能会影响实例的稳定性或性能。

## 实例存储用量（字节）

**实例存储用量**指实例存储的数据占用逻辑磁盘的大小。

如果您是使用**包年包月**付费模式购买实例，则存储资源的额度使用完后，超出部分会自动转为**按量计费**。请您合理设置生命周期并及时清理无用数据，避免产生不必要的存储费用。设置表的生命周期详情请参见[CREATE TABLE](/intl.zh-CN/SQL参考/DDL/TABLE/CREATE TABLE.md)。

## 连接数（个）

**连接数**指实例中总的SQL连接数，包括active及idle状态的JDBC或PSQL连接。

实例的连接数通常与实例的规格有关，具体的规格说明，请参见[实例规格概述]()。

如果您遇到如下情况，则说明系统连接数已经达到上限，需要检查您的应用是否存在连接数泄漏的情况并进行连接的释放，具体操作请参见[连接和Query管理]()：

-   连接数达到甚至超出`max_connections`的取值，您可以执行`show max_connections;`语句查看当前实例默认的最大连接数。
-   产生`FATAL: sorry, too many clients already connection limit exceeded for superusers`报错。
-   产生`FATAL: remaining connection slots are reserved for non-replication superuser connections`报错。

## QPS（个/秒）

**QPS**指平均每秒执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的次数。该指标每20秒上报一次，如果20秒内仅执行了一条SQL语句，则SQL语句的QPS将在该20秒的数据点显示1/20=0.05。

## Query延迟（毫秒）

**Query延迟**指执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的平均延迟（即响应时间）。

## 实时导入RPS（记录/秒）

**实时导入RPS**指每秒通过SQL语句或SDK方式导入或更新的数据记录条数。

Insert RPS表示使用外部表批量导入、使用COPY语句批量导入或Hologres表间插入数据的导入速率。

Update RPS表示通过执行更新或删除SQL语句，每秒更新或删除的记录条数。

