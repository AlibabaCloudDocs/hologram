---
keyword: [Hologres, metrics]
---

# Metrics of Hologres

You can manage resources of instances based on the metrics on the Alerts tab of the instance details page in the Hologres console. Hologres allows you to view the resource usage of instances, view the execution status of SQL statements, and resolve errors based on the metrics. This topic describes the metrics of Hologres.

Hologres provides the following metrics:

-   [CPU Utilization \(%\)](#section_i9a_t9b_jvs)
-   [Memory Usage \(%\)](#section_nql_nm4_nzz)
-   [Storage Usage \(bytes\)](#section_qsi_n05_rgr)
-   [Connections](#section_qbs_kr4_jhf)
-   [QPS](#section_4d4_8y3_c1s)
-   [Query Latency \(ms\)](#section_8um_xe1_ddb)
-   [Real-time Import \(RPS\)](#section_y7x_930_cre)

## CPU Utilization \(%\)

The **CPU Utilization \(%\)** metric indicates the CPU utilization of the instance.

In Hologres, computing resources are reserved by backend processes. If no query operations are performed, the CPU utilization is still greater than zero.

Hologres supports multi-core parallel computing. In most cases, the CPU utilization during a single query increases to 100%. This indicates that computing resources are being fully utilized.

If the CPU utilization is close to 100% all the time, it indicates that the instance is heavily loaded. In this case, the performance of the instance may be affected. If your instance cannot run as normal due to insufficient resources, you can scale up the instance. This allows you to handle a larger number of complex data queries.

## Memory Usage \(%\)

The **Memory Usage \(%\)** metric indicates the memory usage of the instance.

In Hologres, memory resources are reserved by backend processes. If no query operations are performed, some metadata or index data is still loaded into the memory. In this case, the metadata or index data is used to improve the computing speed. The memory usage can reach about 30% to 40% in these scenarios.

If the memory usage is close to 100% all the time, you must scale up the memory resources of the instance. Otherwise, the performance of the instance may be affected.

## Storage Usage \(bytes\)

The **Storage Usage \(bytes\)** metric indicates the size of the logical disk that is used to store the data of the instance.

If you are using the **subscription** billing method and the quota of the storage resources is reached, the billing method is changed to **pay-as-you-go**. You must configure a lifecycle for the data of the instance based on your needs and delete redundant data in real time. Otherwise, you may be charged for extra storage. For information about how to configure a lifecycle, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).

## Connections

The **Connections** metric indicates the total number of SQL connections in the instance. This includes Java Database Connectivity \(JDBC\) connections to PostgreSQL databases in various states, such as, active and idle.

The number of SQL connections reaches the upper limit if one of the following conditions is met. In this case, you must check whether a connection leak has occurred and release connections. For more information, see [Management of connections and queries](/intl.en-US/Monitoring And Alerting/Management of connections and queries.md).

-   The number of SQL connections reaches or even exceeds the value of the `max_connections` parameter. In this case, you can execute the `show max_connections;` statement to view the default maximum number of connections for the instance.
-   The `FATAL: sorry, too many clients already connection limit exceeded for superusers` error occurs.
-   The `FATAL: remaining connection slots are reserved for non-replication superuser connections` error occurs.

## QPS

The **QPS** metric indicates the average number of SQL statements that are executed per second. This includes the SELECT, INSERT, UPDATE, and DELETE statements. This metric is updated every 20 seconds. If a single SQL statement is executed within 20 seconds, the QPS of SQL statements is 0.05 \(1/20 = 0.05\).

## Query Latency \(ms\)

The **Query Latency \(ms\)** metric indicates the average response time of SQL statements. This includes the SELECT, INSERT, UPDATE, and DELETE statements.

## Real-time Import \(RPS\)

The **Real-time Import \(RPS\)** metric indicates the number of entries that are imported or updated per second by using SQL statements or SDKs.

The insert RPS indicates the number of entries that are imported per second by using foreign tables, COPY statements, or Hologres tables.

The update RPS indicates the number of entries that are imported per second by executing the UPDATE or DELETE statement.

