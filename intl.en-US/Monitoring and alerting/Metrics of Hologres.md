---
keyword: [Hologres, metrics]
---

# Metrics of Hologres

Hologres allows you to view the resource usage of instances, view the execution status of SQL statements, and resolve errors based on the metrics. This topic describes the metrics of Hologres.

Hologres provides the following metrics:

-   [CPU Utilization \(%\)](#section_i9a_t9b_jvs)
-   [Memory Usage \(%\)](#section_nql_nm4_nzz)
-   [Storage Usage](#section_qsi_n05_rgr)
-   [Connections](#section_qbs_kr4_jhf)
-   [QPS](#section_4d4_8y3_c1s)
-   [Query Latency \(ms\)](#section_8um_xe1_ddb)
-   [Real-time Import \(RPS\)](#section_y7x_930_cre)

## CPU Utilization \(%\)

**CPU Utilization** indicates the CPU usage of the instance.

In Hologres, compute resources are reserved by backend processes. If no query operations are performed, the CPU Utilization is still greater than zero.

Hologres supports multi-core parallel computing. In most cases, the CPU utilization during a single query increases to 100%. This indicates that computing resources are being fully utilized.

However, if the CPU utilization is close to 100% all the time, it indicates that the instance is heavily loaded. In this case, the performance of the instance may be affected. If your instance cannot run as normal due to insufficient resources, you can scale up the instance. This allows you to handle a higher number of data queries.

## Memory Usage \(%\)

**Memory Usage** indicates the memory usage of the instance.

In Hologres, memory resources are reserved by backend processes. If no query operations are performed, some metadata or index data is still loaded into the memory. In this case, the metadata or index data is used to improve the computing speed. The memory usage in these scenarios can reach 30% to 40%.

If the memory usage is close to 100% all the time, you must scale up the memory resources of the instance. Otherwise, the performance of the instance may be affected.

## Storage Usage

**Storage Usage** indicates the size of the logical disk that is used to store the data of the instance.

If you are using the **subscription** billing method and the quota of the storage resources is reached, the billing method is changed to **pay-as-you-go**. You need to configure a lifecycle for the instance based on your needs and delete redundant data in real time. Otherwise, you may incur extra storage costs. For information about how to configure a lifecycle, see [CREATE TABLE](/intl.en-US/Hologres SQL/DDL/TABLE/CREATE TABLE.md).

## Connections

**Connections** indicates the total number of SQL connections in the instance. This includes Java Database Connectivity \(JDBC\) connections to PostgreSQL databases in various states, such as, active and idle.

The number of SQL connections in an instance depends on the instance type. You can execute the `show max_connections;` statement to view the default maximum number of connections for the instance. If the number of SQL connections is close to the upper limit all the time, check whether a connection leak has occurred. In this case, you can configure a more suitable size for the connection pool.

HoloWeb, HoloStudio, and other peripheral applications of Hologres consume a specific number of connections by using JDBC. However, these connections have no impact on the instance. The operation and maintenance \(O&M\) of the instance also requires a specific number of connections. These connections are often named holo\_admin and tagged PSQL.

The number of SQL connections reaches the upper limit. This applies if one of the following conditions are met:

-   The number of SQL connections is greater than or equal to the value of `max_connections`.
-   The `FATAL: sorry, too many clients already connection limit exceeded for superusers` error occurs.
-   The `FATAL:remaining connection slots are reserved for non-replication superuser connections` error occurs.

If the number of SQL connections reaches the upper limit, you can query the number of idle client connections. Execute the following statement:

```
select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
 datid | datname  |   pid    | usesysid |  usename   | application_name | client_addr | client_hostname | client_port |         backend_start         | xact_start | query_start |         state_change          | wait_event_type | wait_event | state | backend_xid | backend_xmin | query |  backend_type
-------+----------+----------+----------+------------+------------------+-------------+-----------------+-------------+-------------------------------+------------+-------------+-------------------------------+-----------------+------------+-------+-------------+--------------+-------+----------------
 24781 | postgres | 19882501 |       10 | holo_admin | psql             | 127.0.0.1   |                 |       46018 | 2020-09-27 20:30:51.161642+08 |            |             | 2020-09-27 20:30:51.172178+08 | Client          | ClientRead | idle  |             |              |       | client backend
(1 row)
```

The following table describes the parameters in the syntax.

|Parameter|Description|
|---------|-----------|
|datid|The OID of the database to which the backend is connected.|
|datname|The name of the database to which the backend is connected.|
|pid|The process ID of the backend.|
|usesysid|The OID of the user who logged into the backend.|
|usename|The name of the user who logged into the backend.|
|application\_name|The name of the application that is connected to the backend.|
|client\_addr|The IP address of the client that is connected to the backend.|
|client\_hostname|The host name of the connected client.|
|client\_port|The TCP port number that the client uses to communicate with the backend.|
|state|The current state of the backend. Potential values-   active: The backend is executing a query statement.
-   idle: The backend is waiting for a new command from the client.
-   idle in transaction: The backend is in a transaction, but is not executing a query statement.
-   idle in transaction \(aborted\): The backend is in a transaction, but is not executing a query statement. This applies if one of the statements in the transaction triggers an error. |
|query|The most recent query of the backend.|

For more information, see [PostgreSQL 9.2.24 Documentation](https://www.postgresql.org/docs/11/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW).

If a large number of SQL connections are in the idle state, you can obtain the PIDs of the processes and execute the following statements. This allows you to terminate the backend or cancel the current queries on the backend.

```
select pg_cancel_backend (); // Cancel the current query on the backend.
select pg_terminate_backend (); // Terminate the backend.
```

## QPS

**QPS** indicates the average number of SQL statements that are executed per second. This includes statements, such as, SELECT, INSERT, UPDATE, and DELETE . This metric is updated every 20 seconds. If a single SQL statement is executed within 20 seconds, the QPS of the SQL statement is 0.05 \(1/20 = 0.05\).

## Query Latency \(ms\)

**Query Latency \(ms\)** indicates the average response time of SQL statements. This includes statements, such as, SELECT, INSERT, UPDATE, and DELETE.

## Real-time Import \(RPS\)

**Real-time Import \(RPS\)** indicates the number of entries that are imported or updated per second by using SQL statements or SDKs.

Insert RPS indicates the number of entries that are imported per second by using foreign tables, COPY statements, or Hologres tables.

Update RPS indicates the number of entries that are imported per second by executing the UPDATE or DELETE SQL statement.

