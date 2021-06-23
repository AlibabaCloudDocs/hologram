---
keyword: [实例连接, Query管理]
---

# Query管理

本文将为您介绍如何对实例中的Query进行诊断和管理。

## Query管理概述

Hologres兼容PostgreSQL，可以通过[查询pg\_stat\_activity视图信息](#section_gnd_zky_4q7)来查看实例Query的运行信息，以达到分析和诊断运行SQL的目的。具体涉及的操作内容如下所示：

-   [查看SQL运行信息](#section_hne_4no_4cb)：查看SQL运行信息，更好的管理SQL语句。
-   [查看耗时较长的SQL](#section_mc3_bcb_4dj)：查看当前实例耗时较长的SQL。
-   [终止Query](#section_ny7_vf5_lpe)：终止当前不符合预期的Query。
-   [修改Query超时时间](#section_8js_89g_oon)：修改Query运行超时时间，防止引发死锁。
-   [查询慢Query日志](#section_l6w_e1d_b7m)：通过慢Query的查询可以对慢Query或失败Query进行诊断、分析和采取优化措施。
-   [解决Query锁死](#section_2w9_nsx_fbg)：通过SQL语句解决用户Query锁死的问题。

## 查询pg\_stat\_activity视图信息

pg\_stat\_activity是一个非常有用的视图，可以分析排查当前运行的SQL任务以及一些异常问题。您可以执行如下命令查看Query的运行信息。

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
|usename|当前连接的用户名。`holo_admin`是Hologres内置的服务账号，运行结果显示为PSQL，该连接是必要的。如果您的连接数未超出当前实例的最大限度，不需要针对此连接进行优化。连接数查询方式，请参见[\#d7e214](#d7e214)。 |
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

    您可以在HoloWeb中通过可视化的方式一键终止活跃Query，具体操作请参见[查看活跃Query](/intl.zh-CN/连接开发工具/HoloWeb/系统管理/查看活跃Query.md)。

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


## 修改Query超时时间

Hologres支持您通过如下方式修改Query运行超时时间。

-   语法示例

    ```
    set statement_timeout = <time>  
    ```

-   参数说明

    time：超时时间取值范围为0~2147483647ms，单位默认为ms（当time后加单位时需要使用单引号，否则会报错）。当前默认超时时间为10小时，该设置针对session级别生效。

    **说明：** set statement\_timeout = <time\> 和要其他SQL语句一起执行方可生效。

-   使用示例
    -   设置超时时间为5000min，其中具体时间带单位，5000min需要整体添加单引号。

        ```
        set statement_timeout = '5000min' ; 
        select * from tablename;
        ```

    -   设置超时时间为5000ms。

        ```
        set statement_timeout = 5000 ; 
        select * from tablename;
        ```


## 查询慢Query日志

从Hologres V0.10版本开始，支持进行慢query日志查询，详请参见[慢Query日志查看与分析（Beta）](/intl.zh-CN/监控与告警/慢Query日志查看与分析（Beta）.md)。

## 解决Query锁死

当您执行`begin`后忘记提交事务，会造成事务泄漏，进而引发DB级别的死锁，影响服务正常使用。例如，您执行`begin ; select * from tfrom;`命令开启了一个事务，但没有执行`commit`进行提交，就会造成锁死。

Hologres支持您通过设置事务的超时时间的方式自动回滚事务，解决锁死问题。即，当一个带事务的连接空闲超过`idle_in_transaction_session_timeout`设置的时间还未提交或者回滚事务时，系统将自动回滚事务并关闭连接。

**说明：** 超时时间不建议设置过短，如果时间过短容易错误回滚正在使用中的事务。

具体设置方法如下。其中，超时时间单位为ms，默认取值为0，不会自动清理事务。

-   设置DB级事务的超时时间。

    ```
    alter database db_name set idle_in_transaction_session_timeout=300000;
    ```

-   设置单个连接事务的超时时间。

    ```
    set idle_in_transaction_session_timeout=300000;
    ```


