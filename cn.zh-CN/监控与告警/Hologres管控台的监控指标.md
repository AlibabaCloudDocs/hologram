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
-   [QPS（次/秒）](#section_4d4_8y3_c1s)
-   [Query延迟（ms）](#section_8um_xe1_ddb)
-   [实时写入RPS（记录/秒）](#section_y7x_930_cre)

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

如果您是使用**包年包月**付费模式购买实例，则存储资源的额度使用完后，超出部分会自动转为**按量计费**。请您合理设置生命周期并及时清理无用数据，避免产生不必要的存储费用。设置表的生命周期详情请参见[CREATE TABLE](/cn.zh-CN/Hologres SQL/DDL/TABLE/CREATE TABLE.md)。

## 连接数（个）

**连接数**指实例中总的SQL连接数，包括active及idle状态的JDBC或PSQL连接。

实例的连接数通常与实例的规格有关，您可以执行`show max_connections;`语句查看当前实例默认的最大连接数。当SQL连接数长期接近或达到最大值时，您需要检查您的应用是否存在连接数泄漏的情况，并合理设置连接池大小。

Holoweb及Holostudio等Hologres的周边组件，会通过JDBC的方式占用一定的连接数，当连接数充足时，您不用关注此类连接数的占用。除此之外，系统的自身运维也会占用一定的连接数。该类连接通常用户名为holo\_admin，应用标志为PSQL，当连接数充足时，您也无需考虑优化该类连接。

如果您遇到如下情况，则说明系统连接数已经达到上限：

-   连接数达到甚至超出`max_connections`的取值。
-   产生`FATAL: sorry, too many clients already connection limit exceeded for superusers`报错。
-   产生`FATAL: remaining connection slots are reserved for non-replication superuser connections`报错。

当连接数达到上限时，您可以先执行如下语句，查询pg\_stat\_activity中客户端后台idle状态的连接数。

```
select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
 datid | datname  |   pid    | usesysid |  usename   | application_name | client_addr | client_hostname | client_port |         backend_start         | xact_start | query_start |         state_change          | wait_event_type | wait_event | state | backend_xid | backend_xmin | query |  backend_type
-------+----------+----------+----------+------------+------------------+-------------+-----------------+-------------+-------------------------------+------------+-------------+-------------------------------+-----------------+------------+-------+-------------+--------------+-------+----------------
 24781 | postgres | 19882501 |       10 | holo_admin | psql             | 127.0.0.1   |                 |       46018 | 2020-09-27 20:30:51.161642+08 |            |             | 2020-09-27 20:30:51.172178+08 | Client          | ClientRead | idle  |             |              |       | client backend
(1 row)
```

参数说明如下表所示。

|参数|描述|
|--|--|
|datid|表示所连接的数据库的oid。|
|datname|表示所连接的数据库名称。|
|pid|表示后台为当前连接创建的服务进程的ID。查询结果显示的idle连接的总条数则为idle连接的总数。|
|usesysid|表示当前连接的用户的oid。|
|usename|表示当前连接的用户名。|
|application\_name|表示客户端的应用类型。|
|client\_addr|表示客户端的IP地址。|
|client\_hostname|表示客户端的主机名。|
|client\_port|表示客户端的端口。|
|state|表示连接的状态。常见的状态如下：-   active，活跃。
-   idle，空闲。
-   idle in transaction，长事务中的空闲状态。
-   idle in transaction（Aborted），已失败事务中的空闲状态。 |
|query|表示该连接最近执行的query操作。|

更多字段的含义请参见[Postgresql VIEW](https://www.postgresql.org/docs/11/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW)。

当存在较多无用的idle连接时，您可以从上述查询结果中，获取无用连接进程的pid，并使用如下语句杀死无用进程或取消目标进程的query操作。

```
select pg_cancel_backend(<pid>);     //取消目标连接上的query操作。
select pg_terminate_backend(<pid>);  //杀死目标后台连接进程。
```

## QPS（次/秒）

**QPS**指平均每秒执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的次数。该指标每20秒上报一次，如果20秒内仅执行了一条SQL语句，则SQL语句的QPS将在该20秒的数据点显示1/20=0.05。

## Query延迟（ms）

**Query延迟**指执行SELECT、INSERT、UPDATE或DELETE 4种SQL语句的平均延迟（即响应时间）。

## 实时写入RPS（记录/秒）

**实时写入RPS**指每秒通过SQL语句或SDK方式导入或更新的数据记录条数。

Insert RPS表示使用外部表批量导入、使用COPY语句批量导入或Hologres表间插入数据的导入速率。

Update RPS表示通过执行更新或删除SQL语句，每秒更新或删除的记录条数。

