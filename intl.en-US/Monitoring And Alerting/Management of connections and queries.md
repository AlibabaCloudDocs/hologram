---
keyword: [instance connection, query management]
---

# Management of connections and queries

This topic describes how to diagnose and manage the connections to and SQL queries in a Hologres instance.

## Overview

Compatible with PostgreSQL, Hologres allows you to query the information about connections to a Hologres instance and the execution information about SQL queries in the instance by [querying the pg\_stat\_activity view](#section_gnd_zky_4q7). This helps you analyze the status of the connections to the instance and diagnose SQL execution. This feature involves the following operations:

-   [Query the information about connections](#section_1vp_j4t_t8w): You can query the number of connections to an instance or a database and the status of each connection. This helps you better manage your Hologres instance.
-   [Reserve connections for the superuser](#section_y7v_7vh_mut): Hologres automatically reserves connections for instance superusers. When the number of connections to an instance reaches the upper limit, the superuser can use a reserved connection to connect to the instance and manage the connections to the instance.
-   [Terminate connections](#section_0zu_02u_kie): When the number of connections to an instance reaches the upper limit, you can terminate idle connections.
-   [Set the maximum number of connections for a single user](#section_kvj_5uv_cfu): You can set the maximum number of connections for a single user. This prevents a user from occupying unnecessary resources due to excessive connections.
-   [Query the execution information about SQL statements](#section_hne_4no_4cb): You can query the execution information about SQL statements to better manage SQL statements.
-   [Query the information about time-consuming SQL statements](#section_mc3_bcb_4dj): You can query the information about time-consuming SQL statements that are being executed in an instance.
-   [Cancel queries](#section_ny7_vf5_lpe): You can cancel queries that do not meet your expectations.

## Query the pg\_stat\_activity view

pg\_stat\_activity is a helpful PostgreSQL system view. You can use pg\_stat\_activity to analyze and diagnose running PostgreSQL tasks and troubleshoot problems. To query the information about connections to a Hologres instance and the execution information about SQL queries in the instance, execute the following statement:

```
select * from pg_stat_activity ;
```

The following table describes the parameters in the query results of the pg\_stat\_activity view.

|Parameter|Description|
|---------|-----------|
|datid|The object identifier \(OID\) of the connected database at the Hologres backend.|
|datname|The name of the connected database at the Hologres backend.|
|pid|The ID of a process at the Hologres backend.|
|usesysid|The OID of a user who has logged on to the Hologres backend.|
|usename|The username of the current connection. `holo_admin` is the username of a built-in service account in Hologres. The connection that is automatically created by using this username is a PostgreSQL connection. This connection is required. If the number of connections to your Hologres instance has not reached the upper limit, you do not need to optimize this connection. For information about how to query the number of connections, see [Query the information about connections](#section_1vp_j4t_t8w). |
|application\_name|The type of the application on the client.|
|client\_addr|The IP address of the client. The displayed IP address may have been resolved and may not be the actual IP address of the source. |
|client\_hostname|The hostname of the client.|
|client\_port|The port number of the client.|
|backend\_start|The start time of the background process.|
|xact\_start|The start time of the current active transaction of the process. -   If no transaction is active, the value of this parameter is empty.
-   If the current query is the first transaction of the process, the value of this parameter is the same as the value of the query\_start parameter. |
|query\_start|The start time of the current active query. If the current connection is not active, the value of this parameter is the start time of the last query.|
|state\_change|The point in time when the status of the connection was last changed.|
|wait\_event\_type|The type of event that the backend is waiting for. If the backend is not waiting for an event, the value of this parameter is NULL. Valid values:-   LWLock: The backend is waiting for a lightweight lock.
-   Lock: The backend is waiting for a heavyweight lock. The type of the lock that the backend is waiting for is indicated by the `wait_event` parameter.
-   BufferPin: The server process is waiting to access a data buffer while no other process is checking the data buffer.
-   Activity: The server process is idle. This may be a system process that is waiting to be run in the main processing loop.
-   Extension: The server process is in an extension module and is waiting to be run.
-   Client: The server process is waiting for a query from a user application. In addition, the server is expecting an activity that is unrelated to its internal processing to happen.
-   PC: The server process is waiting for an activity of another process on the server.
-   Timeout: The server process is waiting for a timeout.
-   IO: The server process is waiting for the completion of an I/O. |
|wait\_event|The name of the event that the backend is waiting for. If the backend is not waiting for an event, the value of this parameter is NULL.|
|state|The status of the connection. Valid values:-   active: The connection is active.
-   idle: The connection is idle.
-   idle in transaction: The connection is idle in a long-running transaction.
-   idle in transaction \(aborted\): The connection is idle in a failed transaction. |
|backend\_xid|The identifier of the top-level transaction at the Hologres backend.|
|backend\_xmin|The xmin scope of the current backend.|
|query|The last query that was run at the backend. If the value of the state parameter is `active`, the value of this parameter is the query that is being run. Otherwise, the value of this parameter is the query that was last run.|
|backend\_type|The type of the current backend. Supported types include autovacuum launcher, autovacuum worker, logical replication launcher, logical replication worker, parallel worker, background writer, client backend, checkpointer, startup, walreceiver, walsender, and walwriter. In addition, backend execution components such as Panel Quality Engineering \(PQE\) are also supported.|

## Query the information about connections

1.  Query the default maximum number of connections to a Hologres instance.

    The default maximum number of connections to a Hologres instance varies based on the instance type. You can execute the following statement to query the default maximum number of connections to your Hologres instance. The return value is the default maximum number of connections from a single frontend host. For information about the maximum number of frontend hosts for each instance type, see [Instance types](/intl.en-US/Manage Instances/Instance types.md).

    ```
    show max_connections;
    ```

2.  Query the number of connections to the current database.

    You can execute the following statement to query the number of connections to the current database. For more information, see the [parameters](#table_0qg_o57_hp7) in the query results of the pg\_stat\_activity view.

    ```
    SELECT  datname
            ,COUNT(1) AS COUNT
    FROM    pg_stat_activity
    WHERE   backend_type = 'client backend'
    AND     application_name != 'hologres'
    AND     usename != 'holo_admin'
    GROUP BY datname;
    ```

3.  Query the status of each connection.

    You can query the status of each connection to an instance by using HoloWeb in the [Hologres console](https://hologram.console.aliyun.com/#/instance). You can also execute the following statement to query all the Java Database Connectivity \(JDBC\) or PostgreSQL connections in a specified state by querying the pg\_stat\_activity view:

    ```
    select * from pg_stat_activity where backend_type = 'client backend' and state = '<statename>';
    ```

    Replace the statename parameter in the statement with a state. You can use one of the following states:

    -   idle: The connection is idle, which indicates that the process is waiting for a command from the client.
    -   active: The connection is active, which indicates that the process is executing a query statement.
    -   idle in transaction: This state indicates that the process is in a transaction but is not executing a query statement.
    -   idle in transaction \(aborted\): This state indicates that the process is in a transaction where a syntax error exists and is not executing a query statement.
    For example, you can execute the following statement to query the idle connections to the current instance:

    ```
    select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
    ```

    Hologres components such as HoloWeb use JDBC connections. If the maximum number of connections to your Hologres instance can meet your requirements, you do not need to worry about these connections that are used by Hologres components. If the number of SQL connections to your Hologres instance constantly nears or reaches the upper limit, check whether a connection leak has occurred in your application. If so, set a reasonable limit on the connection pool of your application or release the idle connections. For more information, see [Terminate connections](#section_0zu_02u_kie).


## Reserve connections for the superuser

Hologres automatically reserves connections for the superuser of an instance. The number of reserved connections varies based on the instance type. For more information, see [Instance types](/intl.en-US/Manage Instances/Instance types.md). Reserved connections are used by superusers to manage connections when the number of connections to an instance reaches the upper limit. For example, superusers can terminate idle connections. The maximum number of connections that regular users can use equals the maximum number of connections minus the number of reserved connections. We recommend that regular users do not use superuser accounts to manage databases. Otherwise, no reserved connection is available for the superusers and the superusers cannot release connections.

## Terminate connections

If you are in one of the following scenarios, the number of connections to the current Hologres instance has reached the upper limit:

-   The number of connections reaches or exceeds the value of the `max_connections` parameter that you queried. You can view the number of connections on the Alerts tab of the instance details page in the Hologres console.
-   The `FATAL: sorry, too many clients already connection limit exceeded for superusers` error occurs.
-   The `FATAL: remaining connection slots are reserved for non-replication superuser connections` error occurs.

In these scenarios, you can log on to the Hologres instance as the superuser and execute the following statement to check whether a number of idle connections exist:

```
select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
```

If the query results show a number of idle processes and you confirm that they are idle and useless connections, you can execute the following sample statements to release the idle connections. Use the values of the pid parameter in the preceding query results to specify the connections to be released in the statements. For more information about the parameters in the following statements, see the [parameters](#table_0qg_o57_hp7) in the query results of the pg\_stat\_activity view.

```
-- Cancel the queries that use a specific connection.
select pg_cancel_backend(<pid>); 
-- Terminate the background process that corresponds to the connection.    
select pg_terminate_backend(<pid>); 

-- Terminate multiple background processes of idle connections and release the connections.
SELECT pg_terminate_backend(pid)
        ,query
        ,datname
        ,usename
        ,application_name
        ,client_addr
        ,client_port
        ,backend_start
        ,state
FROM    pg_stat_activity
WHERE   length(query) > 0
AND     pid != pg_backend_pid()
AND     backend_type = 'client backend'
AND     state = 'idle'
AND     application_name != 'hologres'
AND     usename != 'holo_admin';
```

## Set the maximum number of connections for a single user

Compatible with PostgreSQL, Hologres allows you to set the maximum number of connections for a single user. This prevents a user from occupying unnecessary resources due to excessive connections.

1.  Limit the number of connections for a single user.
    -   Sample code

        ```
        ALTER ROLE "Account ID" CONNECTION LIMIT <number>; 
        ```

    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |Account ID|The UID of the Alibaba Cloud account or RAM user to be used by the user that you want to manage. If the user is a RAM user, add p4\_ before the UID. For more information, see [Account overview](/intl.en-US/User Authorization/Account overview.md).|
        |number|The maximum number of connections.|

    -   Examples

        Set the maximum number of connections to 1 for the RAM user whose UID is 283813xxxx.

        ```
        ALTER ROLE "p4_283813xxxx" CONNECTION LIMIT 1; 
        ```

2.  To query the maximum number of connections for instance users, execute the following statement:

    ```
    SELECT rolname, rolconnlimit
    FROM pg_roles
    WHERE rolconnlimit <> -1;
    ```

    The following results are returned:

    ```
           rolname | rolconnlimit 
    ---------------+--------------
     p4_283813xxxx |      1
    (1 row)
    ```


## Query the execution information about SQL statements

The superuser of a Hologres instance can query the execution information about SQL statements that are submitted by all the users who are connected to the Hologres instance. RAM users can query the execution information about only SQL statements that are submitted by themselves. For more information about the parameters in the following statements, see the [parameters](#table_0qg_o57_hp7) in the query results of the pg\_stat\_activity view.

1.  To query the execution information about SQL statements that are submitted by a user who is connected to the current Hologres instance, execute the following statement:

    ```
    SELECT datname,usename,query,pid ,state FROM pg_stat_activity ;
    ```

2.  To query the execution information about SQL statements that are submitted by a user and are being executed, execute the following statement:

    ```
    SELECT datname,usename,query,pid,state
       FROM pg_stat_activity
       WHERE state != 'idle' ;
    ```


## Query the information about time-consuming SQL statements

To query the information about time-consuming SQL statements, execute the following statement. For more information about the parameters in the following statement, see the [parameters](#table_0qg_o57_hp7) in the query results of the pg\_stat\_activity view.

```
select current_timestamp - query_start as runtime, datname, usename, query,pid
    from pg_stat_activity
    where state != 'idle'
    order by 1 desc;
```

The following code shows sample results. In this example, the time-consuming statement is an UPDATE statement.

```
runtime     |    datname     | usename  | pid    |      current_query
-----------------+----------------+----------+------------------------------------
 00:00:24.258388 | holotest  | 123xxx   | 1267xx | UPDATE holo_order 
                                                   : set gmt = now(),
                                                   : trade_id = $1,
                                                   : trade_create_time = $2;
 00:00:1.186394  | testdb    | 156xx    | 1783xx | select * from oder;
(2 rows)
```

## Cancel queries

If queries do not meet your expectations, you can cancel them as needed by using one of the following methods:

-   Cancel queries in a visualized way.

    You can cancel active queries with a few clicks in the HoloWeb console. For more information, see [View the details of a query task](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/View the details of a query task.md).

-   Cancel queries by using SQL statements.

    For more information about the parameters in the following statements, see the [parameters](#table_0qg_o57_hp7) in the query results of the pg\_stat\_activity view.

    -   Cancel the queries that use a specific connection.

        ```
        select pg_cancel_backend(<pid>);
        ```

    -   Cancel multiple queries at a time.

        ```
        SELECT pg_cancel_backend(pid)
                ,query
                ,datname
                ,usename
                ,application_name
                ,client_addr
                ,client_port
                ,backend_start
                ,state
        FROM    pg_stat_activity
        WHERE   length(query) > 0
        AND     pid != pg_backend_pid()
        AND     backend_type = 'client backend'
        AND     application_name != 'hologres'
        AND     usename != 'holo_admin';
        ```


## Query slow query logs

In Hologres V0.10 and later, you can query slow query logs. For more information, see [t2069482.md\#]().

