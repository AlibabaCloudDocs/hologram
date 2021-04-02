---
keyword: [实例连接, Query管理]
---

# 连接和Query管理

本文将为您介绍如何对实例中的连接和Query进行诊断和管理。

## 连接和Query管理概述

Hologres兼容PostgreSQL，可以通过[查询pg\_stat\_activity视图信息](#section_gnd_zky_4q7)来查看实例连接和Query的运行信息，以达到分析实例连接状态和诊断运行SQL的目的。具体涉及的操作内容如下所示：

-   [查询连接信息](#section_1vp_j4t_t8w)：通过查询实例、DB的连接数以及每个连接状态，更好的管理实例。
-   [管理员预留连接](#section_y7v_7vh_mut)：用于在连接数达到最大时对连接进行管理操作。
-   [终止连接](#section_0zu_02u_kie)：当系统连接数达到上限时，您可以终止空闲连接。
-   [查看SQL运行信息](#section_hne_4no_4cb)：查看SQL运行信息，更好的管理SQL语句。
-   [查看耗时较长的SQL](#section_mc3_bcb_4dj)：查看当前实例耗时较长的SQL。
-   [终止Query](#section_ny7_vf5_lpe)：终止当前不符合预期的Query。

## 查询pg\_stat\_activity视图信息

pg\_stat\_activity是一个非常有用的视图，可以分析排查当前运行的SQL任务以及一些异常问题。您可以执行如下命令查看实例连接和Query的运行信息。

```
select * from pg_stat_activity ;
```

pg\_stat\_activity视图的参数说明如下所示：

|字段|描述|
|--|--|
|datid|Hologres后端连接到的数据库的OID。|
|datname|Hologres后端连接到的数据库的名称。|
|pid|Hologres后端的进程ID。|
|usesysid|登录到Hologres后端的用户OID。|
|usename|当前连接的用户名。`holo_admin`是Hologres内置的服务账号，运行结果显示为PSQL，该连接是必要的。如果您的连接数未超出当前实例的最大限度，不需要针对此连接进行优化。连接数查询方式，请参见[查询连接信息](#section_1vp_j4t_t8w)。 |
|application\_name|客户端的应用类型。|
|client\_addr|客户端的IP地址。显示的IP地址可能是被解析过的，不保证一定是源端IP。 |
|client\_hostname|客户端的主机名。|
|client\_port|客户端的端口。|
|backend\_start|后台进程开始的时间。|
|xact\_start|该进程的当前事务被启动的时间。-   如果没有活动事务，则为空。
-   如果当前查询是该进程的第一个事务，这一列等于query\_start。 |
|query\_start|当前活动查询开始的时间，如果当前连接状态不是active，取值为上一个查询开始的时间。|
|state\_change|连接的状态（state）上一次被改变的时间。|
|wait\_event\_type|后端正在等待的事件类型，如果不存在则为NULL。可能的取值有：-   LWLock：后端正在等待一个轻量级锁。
-   Lock：后端正在等待一个重量级锁。`wait_event`将标识等待的锁的类型。
-   BufferPin：服务器进程正在等待访问一个数据缓冲区，而此时没有其他进程正在检查该缓冲区。
-   Activity：服务器进程处于闲置状态。被用于在其主处理循环中等待活动的系统进程。
-   Extension：服务器进程正在一个扩展模块中等待活动。
-   Client：服务器进程正在等待来自用户应用的某种查询，并且该服务器预期某种与其内部处理无关的事情发生。
-   PC：服务器进程正在等待来自服务器中另一个进程的某种活动。
-   Timeout：服务器进程正在等待一次超时发生。
-   IO：服务器进程正在等待一次IO完成。 |
|wait\_event|如果后端当前正在等待，则是等待事件的名称，否则为 NULL。|
|state|表示连接的状态。常见的状态如下：-   active：活跃。
-   idle：空闲。
-   idle in transaction：长事务中的空闲状态。
-   idle in transaction（Aborted）：已失败事务中的空闲状态。 |
|backend\_xid|Hologres后端的顶层事务标识符。|
|backend\_xmin|当前后端的xmin范围。|
|query|后端最近执行的查询。如果state为`active`，将会显示当前正在执行的查询。在所有其他状态下，显示上一个被执行的查询。|
|backend\_type|当前后端的类型。可能的类型为autovacuum launcher、autovacuum worker、logical replication launcher、logical replication worker、parallel worker、background writer、client backend、checkpointer、startup、walreceiver、walsender以及 walwriter。除此之外还包括后端的执行组件，例如PQE等。|

## 查询连接信息

1.  查询实例的默认最大连接数。

    不同的实例规格对应不同的默认连接数，您可以通过以下命令查询当前实例规格的最大连接数。命令执行完成后显示结果为单个FrontEnd节点的规格，不同实例的Frontend节点数请参见[实例规格概述](/cn.zh-CN/实例管理/实例规格概述.md)。

    ```
    show max_connections;
    ```

2.  查询当前DB的连接数。

    您可以通过如下命令查看当前DB的连接数，更多关于参数的说明，请参见[表 参数说明](#table_0qg_o57_hp7)。

    ```
    SELECT  datname
            ,COUNT(1) AS COUNT
    FROM    pg_stat_activity
    WHERE   backend_type = 'client backend'
    AND     application_name != 'hologres'
    AND     usename != 'holo_admin'
    GROUP BY datname;
    ```

3.  查看每个连接的状态。

    您可以在[Hologres管控台](https://hologram.console.aliyun.com/#/instance)通过HoloWeb查看每个实例的连接状态，也可以执行如下语句，通过查询pg\_stat\_activity视图来获取所有JDBC或PSQL连接的状态。

    ```
    select * from pg_stat_activity where backend_type = 'client backend' and state = '<statename>';
    ```

    其中statename是需要填写的状态参数名，包括以下几种：

    -   idle：空闲连接，表示进程在等待新的客户端命令。
    -   active：活跃连接，表示进程正在执行查询操作。
    -   idle in transaction：表示进程处于一个事务中，但是当前没有执行查询操作。
    -   idle in transaction \(aborted\)：表示进程处于一个事务中，该事务存在语句错误，并且进程当前没有执行查询操作。
    例如，您可以执行如下命令查询当前实例的空闲连接。

    ```
    select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
    ```

    Holoweb等Hologres周边组件会通过JDBC的方式占用一定的连接数，如果您的连接数满足需求，无需关心此类连接数的占用。当SQL连接数长期接近或达到`max_connections`时，意味着您需要检查您的应用是否存在连接数泄漏情况，需要在应用端合理设置连接池大小，或者您也可以释放空闲连接，具体操作请参见[终止连接](#section_0zu_02u_kie)。


## 管理员预留连接

Hologres会为Superuser预留连接，不同的实例规格预留的连接数不同，详情请参见[实例规格概述](/cn.zh-CN/实例管理/实例规格概述.md)。Superuser预留连接用于在连接数达到最大时对连接进行管理操作（如终止idle连接），普通用户的连接数最大为max\_connections预留连接。在实践中，不建议普通用户使用Superuser账号操作数据库，否则会导致连接全部占满，且无法通过管理渠道释放连接。

## 终止连接

如果您遇到如下情况，则说明系统连接数已经达到上限：

-   连接数达到甚至超出`max_connections`的取值，您可以在Hologres管控台的监控告警页查看连接数。
-   产生`FATAL: sorry, too many clients already connection limit exceeded for superusers`报错。
-   产生`FATAL: remaining connection slots are reserved for non-replication superuser connections`报错。

当您有上述情况产生，可以通过Superuser账号连接实例，执行如下语句查看空闲连接是否过多。

```
select * from pg_stat_activity where backend_type = 'client backend' and state = 'idle';
```

如果查询结果显示空闲进程过多，并且确定是无用的空闲连接时，可以找到上述语句结果中的pid字段，并执行如下语句释放空闲连接。更多关于参数的说明，请参见[表 参数说明](#table_0qg_o57_hp7)。

```
-- cancel该连接上的query
select pg_cancel_backend(<pid>); 
--结束对应的后台连接进程    
select pg_terminate_backend(<pid>); 

--批量终止后台idle连接进程，释放连接
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

## 查看SQL运行信息

Superuser可以查看所有用户的SQL运行信息，RAM用户只能查看自己的SQL运行信息。更多关于参数的说明，请参见[表 参数说明](#table_0qg_o57_hp7)。

1.  您可以通过如下语句查看当前实例内用户的SQL运行信息。

    ```
    SELECT datname,usename,query,pid ,state FROM pg_stat_activity ;
    ```

2.  您可以执行如下语句查看当前正在运行的SQL信息。

    ```
    SELECT datname,usename,query,pid,state
       FROM pg_stat_activity
       WHERE state != 'idle' ;
    ```


## 查看耗时较长的SQL

您可以通过如下语句查看当前实例耗时较长的SQL。更多关于参数的说明，请参见[表 参数说明](#table_0qg_o57_hp7)。

```
select current_timestamp - query_start as runtime, datname, usename, query,pid
    from pg_stat_activity
    where state != 'idle'
    order by 1 desc;
```

示例执行结果如下所示，该示例中可以看到耗时较久的语句为UPDATE。

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

## 终止Query

如果当前存在不符合预期的Query，您可以根据实际情况通过如下命令进行终止。

-   使用可视化方式终止Query。

    您可以在HoloWeb中通过可视化的方式一键终止活跃Query，具体操作请参见[查看活跃Query](/cn.zh-CN/连接开发工具/HoloWeb/系统管理/查看活跃Query.md)。

-   使用SQL语句终止Query。

    更多关于参数的说明，请参见[表 参数说明](#table_0qg_o57_hp7)。

    -   取消当前连接上的Query。

        ```
        select pg_cancel_backend(<pid>);
        ```

    -   批量取消Query。

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


