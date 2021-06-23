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

需要您注意的是：

-   目前Hologres V0.8版本，无[连接数（个）](#section_qbs_kr4_jhf)指标。
-   目前Hologres V0.9.27以下版本，如果当前实例中表数量超过1000时，由于指标量太大，[实时导入RPS（记录/秒）](#section_y7x_930_cre)指标可能会存在丢失指标或者指标值过低的情况，影响准确率。

## CPU使用率（%）

**CPU使用率**指实例的CPU综合使用率。

Hologres的计算资源采用预留模式，即使没有执行查询操作，后台也会存在运行的进程，此时CPU使用率不为零属于正常现象。

Hologres可以充分发挥CPU多核并行计算的能力。通常，单个查询可以迅速将CPU使用率提高至100%，这表明计算资源得到了充分利用。

如果CPU使用率长期接近100%，表明实例的负载非常高，此时，CPU资源可能会影响系统的运行。您可以通过分析具体的业务场景，判断资源占用情况：

-   是否存在较大的离线数据导入情况（INSERT），且数据规模还在逐渐增长。
-   是否存在高QPS的查询或写入。
-   是否存在上述场景或场景以外的混合负载。

如果确定是资源不足影响了系统的运行，则可以对实例资源进行扩容，以便承载更大数据量的复杂查询。

## 内存使用率（%）

**内存使用率**指实例的内存综合使用率。

Hologres的内存资源采用预留模式，即使没有执行查询操作，也会有部分Meta或Index元数据加载到内存中，该类元数据用于提升计算速度。此时，在不存在查询的情况下，内存使用率可能会达到30%~40%左右，属于正常现象。

如果内存使用率持续升高，甚至接近100%，通常会影响系统的运行，可能会影响实例的稳定性或性能。关于该问题产生的原因、主要影响和解决方法具体如下：

-   **产生原因**
    -   表数量增加，数据总量也随之增加，以至于数据规模远大于当前计算规格。由于内存使用率和元数据、索引量为正相关关系，因此，表数量越多、数据量越大、索引越多，都会导致内存使用率升高。
    -   索引设置不合理。例如，设置了过多的Bitmap或Dictionary索引。
-   **主要影响**
    -   影响稳定性。当元数据等过大时，会超额占据正常Query可用的内存空间，导致在查询过程中，可能会偶发`SERVER_INTERNAL_ERROR`、`ERPC_ERROR_CONNECTION_CLOSED`、`Total memory used by all existing queries exceeded memory limitation`等报错。
    -   影响性能。当元数据等过大时，会超额占据正常Query可用的缓存空间，从而导致缓存命中减少，Query延迟增加。
-   **解决方法**
    -   删除不再查询的数据，以释放占用的内存。
    -   设置合理的索引，可以根据业务场景具体分析，删除不涉及的Bitmap和Dictionary。
    -   扩容，对实例的计算和存储资源进行升配。升配的具体建议如下：
        -   普通场景：允许读磁盘数据的延迟，响应时间RT（return time）要求不严格，1 CU即1core 4G内存，可以支持50 GB~100 GB的数据存储。这种场景下，每32 CU对应的数据存储最高不应超过3 TB。
        -   RT要求低的Serving场景：建议查询热点数据全部在内存的缓存中。内存中缓存的比例默认占总内存的30%，即1 CU=1core+4 GB，那么在业务场景中，例如，1.3 GB用于数据缓存，表的元数据也会使用部分数据缓存。例如，低RT要求的场景，热点数据为100 GB，那么缓存至少需要100 GB可用（实际上读取数据并解压后，占用内存不止100 GB），因此需要约320 GB内存以上，从而推算计算资源至少需要96 CU左右。

## 实例存储用量（字节）

**实例存储用量**指实例存储的数据占用逻辑磁盘的大小，是所有DB存储用量的总和。

如果您是使用**包年包月**付费模式购买实例，则存储资源的额度使用完后，超出部分会自动转为**按量计费**。请您在超出存储资源后及时对存储进行升级配置，或者合理设置生命周期并及时清理无用数据，避免产生不必要的存储费用。设置表的生命周期详情请参见[CREATE TABLE](/cn.zh-CN/Hologres SQL/DDL/TABLE/CREATE TABLE.md)。您也可以通过SQL语句分析各个DB和DB表占用存储的大小，更多内容请参见[查看表和DB的存储大小](/cn.zh-CN/Hologres SQL/PostgreSQL兼容函数/查看表和DB的存储大小.md)。

## 连接数（个）

**连接数**指实例中总的SQL连接数，包括active及idle状态的JDBC或PSQL连接。

实例的连接数通常与实例的规格有关，具体的规格说明，请参见[实例规格概述](/cn.zh-CN/实例管理/实例规格概述.md)。

如果您遇到如下情况，则说明系统连接数已经达到上限，需要检查您的应用是否存在连接数泄漏的情况并进行连接的释放，具体操作请参见[连接数管理](/cn.zh-CN/监控与运维/连接数管理.md)：

-   连接数达到甚至超出`max_connections`的取值，您可以执行`show max_connections;`语句查看当前实例默认的最大连接数。
-   产生`FATAL: sorry, too many clients already connection limit exceeded for superusers`报错。
-   产生`FATAL: remaining connection slots are reserved for non-replication superuser connections`报错。

## QPS（个/秒）

**QPS**指平均每秒执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的次数。该指标每20秒上报一次，如果20秒内仅执行了一条SQL语句，则SQL语句的QPS将在该20秒的数据点显示1/20=0.05。

## Query延迟（毫秒）

**Query延迟**指执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的平均延迟（即响应时间）。您可以通过查看慢Query日志分析Query延迟，更多内容请参见[慢Query日志查看与分析（Beta）](/cn.zh-CN/监控与运维/慢Query日志查看与分析（Beta）.md)。

## 实时导入RPS（记录/秒）

**实时导入RPS**指每秒通过SQL语句或SDK方式导入或更新的数据记录条数。

Insert RPS表示使用外部表批量导入、使用COPY语句批量导入或Hologres表间插入数据的导入速率。

Update RPS表示通过执行更新或删除SQL语句，每秒更新或删除的记录条数。

SDK RPS表示通过SDK方式写入Hologres的每秒数据写入或更新的条数。SDK方式包括：

-   [通过Flink写入](/cn.zh-CN/数据接入/实时写入/Flink/概览.md)。
-   [通过Holo Client读写数据](/cn.zh-CN/连接开发工具/通过Holo Client读写数据.md)。
-   [导入Spark的数据至Hologres](/cn.zh-CN/数据接入/实时写入/Spark/导入Spark的数据至Hologres.md)。
-   [实时同步MySQL Binlog的数据至Hologres](/cn.zh-CN/数据接入/实时写入/RDS for MySQL/实时同步MySQL Binlog的数据至Hologres.md)。
-   [实时同步DataHub的数据至Hologres](/cn.zh-CN/数据接入/实时写入/DataHub/实时同步DataHub的数据至Hologres.md)。
-   通过数据集成DataX离线写入。
-   SQL或JDBC 通过`insert into values ()`语句写入。

