---
keyword: [Hologres, metrics]
---

# Metrics of Hologres

You can manage the resources of instances based on the metrics on the Alerts tab of the instance details page in the Hologres console. Hologres allows you to view the resource usage of instances, view the execution status of SQL statements, and resolve errors based on the metrics. This topic describes the metrics of Hologres.

Hologres provides the following metrics:

-   [CPU Utilization \(%\)](#section_i9a_t9b_jvs)
-   [Memory Usage \(%\)](#section_nql_nm4_nzz)
-   [Storage Usage \(bytes\)](#section_qsi_n05_rgr)
-   [Connections](#section_qbs_kr4_jhf)
-   [QPS](#section_4d4_8y3_c1s)
-   [Query Latency \(ms\)](#section_8um_xe1_ddb)
-   [Real-time Import \(RPS\)](#section_y7x_930_cre)

Take note of the following items:

-   The [Connections](#section_qbs_kr4_jhf) metric is not supported in Hologres V0.8.
-   In Hologres V0.9.27 or earlier, if an instance contains more than 1,000 tables, statistics are collected on a large amount of data. In this case, the [Real-time Import \(RPS\)](#section_y7x_930_cre) metric may be lost or the metric value is excessively low. This affects the accuracy.

## CPU Utilization \(%\)

The **CPU Utilization \(%\)** metric indicates the CPU utilization of the instance.

In Hologres, computing resources are reserved by backend processes. If no query operations are performed, the CPU utilization is still greater than zero.

Hologres supports multi-core parallel computing. In most cases, the CPU utilization during a single query increases to 100%. This indicates that computing resources are being fully utilized.

If the CPU utilization is close to 100% all the time, the instance is heavily loaded. In this case, the performance of the instance may be affected. You can analyze specific business scenarios to check the resource usage:

-   Check whether a large amount of offline data is imported by using INSERT operations, and whether the amount of data is still increasing.
-   Check whether data is queried or written with high queries per second \(QPS\).
-   Check whether hybrid loads exist in the preceding or other scenarios.

If your instance cannot run as normal due to insufficient resources, you can upgrade the specifications of the instance. This allows you to handle a larger number of complex data queries.

## Memory Usage \(%\)

The **Memory Usage \(%\)** metric indicates the memory usage of the instance.

In Hologres, memory resources are reserved by backend processes. If no query operations are performed, some metadata or index data is still loaded into the memory. In this case, the metadata or index data is used to improve the computing speed. In addition, the memory usage may be in the range of 30% to 40%. This is normal.

If the memory usage continues to increase or is close to 100%, the performance of the system is affected, and the stability or performance of the instance may also be affected. The following part describes the cause, impacts, and solution of this issue:

-   **Cause**
    -   As the number of tables increases, the total amount of data also grows. The size of data is much larger than that can be processed by the current computing resources. The memory usage is positively correlated with the amount of metadata and the number of indexes. This way, the memory usage is higher due to increased tables, data, and indexes.
    -   Indexes are inappropriately set. For example, bitmap indexes are created or dictionary encoding is enabled for excessive fields.
-   **Impacts**
    -   The stability of the instance is affected. A large amount of metadata occupies the memory space available to queries. As a result, errors occasionally occur during queries, such as the `SERVER_INTERNAL_ERROR`, `ERPC_ERROR_CONNECTION_CLOSED`, and `Total memory used by all existing queries exceeded memory limitation` errors.
    -   The performance of the instance is affected. A large amount of metadata also occupies the cache space available to queries. This decreases cache hits and increases the query latency.
-   **Solution**
    -   Delete the data that is no longer queried to release the occupied memory.
    -   Set indexes appropriately. You can delete unnecessary bitmap indexes and disable dictionary encoding in specific business scenarios.
    -   Upgrade the specifications of the instance to increase the computing and storage resources of the instance. We recommend that you upgrade an instance based on the scenario:
        -   In scenarios where disk data can be read at a specific latency and the response time \(RT\) is not strict, 1 compute unit \(CU\) can support data storage of 50 GB to 100 GB. Computing resources of 1 CU include 1 CPU core and 4 GB memory. In such scenarios, every 32 CUs can support data storage of at most 3 TB.
        -   In serving scenarios that require a short RT, we recommend that all hotspot data be cached in the memory. By default, the cache accounts for 30% of the total memory. In such scenarios, 1.3 GB memory among 1 CU is used for data cache, and table metadata also occupies part of the data cache. For example, in a scenario that requires a short RT, hotspot data of 100 GB needs to be cached in the memory. After the data is read and decompressed, it takes up more than 100 GB memory. In this case, at least 320 GB memory is required, which corresponds to computing resources of at least 96 CUs.

## Storage Usage \(bytes\)

The **Storage Usage \(bytes\)** metric indicates the size of the logical disk that is used to store the data of all databases in the instance.

If you are using the **subscription** billing method and the quota of the storage resources is used up, the billing method is changed to **pay-as-you-go**. In this case, you can upgrade the specifications of the instance to increase the storage resources of the instance. You can also configure a lifecycle for the data of the instance based on your needs and delete redundant data in real time. Otherwise, you may be charged for extra storage. For information about how to configure a lifecycle, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md). You can query the storage size of a table or database in Hologres by executing SQL statements. For more information, see [Query the storage sizes of tables and databases](/intl.en-US/Hologres SQL/Functions/Query the storage sizes of tables and databases.md).

## Connections

The **Connections** metric indicates the total number of SQL connections in the instance. This includes Java Database Connectivity \(JDBC\) connections to PostgreSQL databases in various states, such as the active and idle states.

The number of SQL connections in an instance depends on the instance type. For more information, see [Instance types](/intl.en-US/Manage Instances/Instance types.md).

The number of SQL connections reaches the upper limit if one of the following conditions is met. In this case, you must check whether a connection leak has occurred and release connections. For more information, see [Management of connections and queries](/intl.en-US/Monitoring And Alerting/Management of connections and queries.md).

-   The number of SQL connections reaches or even exceeds the value of the `max_connections` parameter. In this case, you can execute the `show max_connections;` statement to view the default maximum number of connections for the instance.
-   The `FATAL: sorry, too many clients already connection limit exceeded for superusers` error occurs.
-   The `FATAL: remaining connection slots are reserved for non-replication superuser connections` error occurs.

## QPS

The **QPS** metric indicates the average number of SQL statements that are executed per second. This includes the SELECT, INSERT, UPDATE, and DELETE statements. This metric is updated every 20 seconds. If a single SQL statement is executed within 20 seconds, the QPS of SQL statements is 0.05 \(1/20 = 0.05\).

## Query Latency \(ms\)

The **Query Latency \(ms\)** metric indicates the average RT of SQL statements. This includes the SELECT, INSERT, UPDATE, and DELETE statements. You can query slow query logs to analyze the query latency. For more information, see [t2069482.md\#]().

## Real-time Import \(RPS\)

The **Real-time Import \(RPS\)** metric indicates the number of entries that are imported or updated per second by using SQL statements or SDKs.

The insert records per second \(RPS\) indicates the number of entries that are imported per second by using foreign tables, COPY statements, or Hologres tables.

The update RPS indicates the number of entries that are imported per second by executing the UPDATE or DELETE statement.

The SDK RPS indicates the number of entries that are imported or updated per second by using SDKs. Data can be imported by using SDKs in the following ways:

-   [Use Flink to write data to Hologres](/intl.en-US/Data Access/Real-time data writing/Realtime Compute/Overview.md).
-   [Read and write data by using Holo Client](/intl.en-US/Common Development Tools/Read and write data by using Holo Client.md).
-   [Import Spark data to Hologres](/intl.en-US/Data Access/Real-time data writing/Spark/Import Spark data to Hologres.md).
-   [Synchronize MySQL binlogs to Hologres in real time](/intl.en-US/Data Access/Real-time data writing/RDS for MySQL/Synchronize MySQL binlogs to Hologres in real time.md).
-   [Synchronize data from DataHub to Hologres in real time](/intl.en-US/Data Access/Real-time data writing/DataHub/Synchronize data from DataHub to Hologres in real time.md).
-   Use the `INSERT INTO VALUES ()` statement to write data by using the PostgreSQL client or a JDBC client.

