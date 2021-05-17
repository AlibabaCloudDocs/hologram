# 慢Query日志查看与分析（Beta）

慢Query的查询与分析可以帮助您对系统中发生的慢Query或失败Query进行诊断、分析和采取优化措施。本文将介绍在Hologres中如何查看慢Query日志并分析。

## 使用限制

在Hologres中查询慢Query日志，具体限制如下：

-   该功能仅Hologres V0.10及以上版本支持，请在Hologres管理控制台的实例详情页查看当前实例版本，如果您的实例是V0.10以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)升级实例。
-   为了保证系统稳定性，避免流量超载，查询最多返回10000条慢Query日志。
-   目前慢Query日志支持记录和查询INSERT、SELECT、UPDATE、DELETE等4种SQL语句，暂不支持记录和查看DDL和UTILITY语句。
-   查询返回的memory\_bytes、shuffle\_bytes和cpu\_time\_ms三个字段不是精准值，只有部分相对意义，没有绝对意义。
-   Hologres V0.10版本的FAILED Query，不显示内存、读盘、读取量、CPU时间、query\_stats等运行时的统计信息。

## 查看query\_log表

存放慢Query查询日志的系统表为hologres.hg\_query\_log，将实例升级到V0.10版本后，将会默认采集大于100ms的慢Query。其主要包含的字段信息如下：

```
   Column           |           Type           |               Descrption                
--------------------+--------------------------+-----------------------------------------
 usename            | text                     | 查询的用户名
 status             | text                     | 查询的最终状态，成功或失败
 query_id           | text                     | 查询ID
 datname            | text                     | 查询的数据库名
 command_tag        | text                     | 查询的类型(INSERT/SELECT/UPDATE/DELETE)
 duration           | integer                  | 查询的耗时(ms)
 message            | text                     | 报错的信息
 query_start        | timestamp with time zone | 查询开始时间
 query_date         | text                     | 查询开始日期
 query              | text                     | 查询的文本内容
 result_rows        | bigint                   | 查询返回的行数
 result_bytes       | bigint                   | 查询返回的字节数
 read_rows          | bigint                   | 查询读取的行数
 read_bytes         | bigint                   | 查询读取的字节数
 affected_rows      | bigint                   | DML影响的行数
 affected_bytes     | bigint                   | DML影响的字节数
 memory_bytes       | bigint                   | 单节点峰值内存消耗（非精确）
 shuffle_bytes      | bigint                   | 数据Shuffle估计字节数（非精确）
 cpu_time_ms        | bigint                   | 总的CPU时间(毫秒，非精确)
 physical_reads     | bigint                   | 物理读的次数
 pid                | integer                  | 查询服务进程ID
 application_name   | text                     | 查询应用类型
 engine_type        | text[]                   | 查询用到的引擎
 client_addr        | text                     | 查询的来源地址
 table_write        | text                     | SQL改写的表
 table_read         | text[]                   | SQL读取的表
 session_id         | text                     | 查询Session ID
 session_start      | timestamp with time zone | 查询Session开启时间
 command_id         | text                     | 命令/语句编号ID
 optimization_cost  | integer                  | 查询执行计划的耗时
 start_query_cost   | integer                  | 查询启动耗时
 get_next_cost      | integer                  | 查询执行耗时
 extended_cost      | text                     | 查询的其他详细耗时
 plan               | text                     | 查询对应的plan
 statistics         | text                     | 查询对应的执行时统计信息
 visualization_info | text                     | 查询Plan可视化信息
 query_detail       | text                     | 查询的其他扩展信息(JSON格式存储)
 query_extinfo      | text[]                   | 查询的其他扩展信息(ARRAY格式存储)
```

## 授予查看权限

慢Query日志需要有一定的权限才能查看，其权限规则和授权方式说明如下：

-   **查看实例所有DB的慢Query日志。**
    -   授予用户Superuser权限。

        Superuser账号可以查看实例所有DB的全部慢Query日志，给用户授予Superuser用户的权限，使用户有权限查看实例所有DB的慢Query日志。

        ```
        ALTER USER "云账号ID" SUPERUSER;--将“云账号ID”替换为实际用户名。如果是RAM用户，账号ID前需要添加“p4_”。
        ```

    -   将用户添加到pg\_read\_all\_stats用户组。

        除Superuser外，Hologres还支持pg\_read\_all\_stats用户组查看所有DB慢Query日志，普通用户如果需要查看所有日志，可以联系Superuser授权加入该用户组。授权方式如下：

        ```
        GRANT pg_read_all_stats TO "云账号ID";--专家权限模型授权
        CALL spm_grant('pg_read_all_stats', '云账号ID');  -- SPM权限模型
        CALL slpm_grant('pg_read_all_stats', '云账号ID'); -- SLPM权限模型
        ```

-   **查看本DB的慢Query日志。**

    开启简单权限模型（SPM）或基于Schema级别的简单权限模型（SLPM），将用户加入db\_admin用户组，db\_admin角色可以查看本DB的query日志。

    ```
    CALL spm_grant('<db_name>_admin', '云账号ID');  -- SPM权限模型
    CALL slpm_grant('<db_name>.admin', '云账号ID'); -- SLPM权限模型
    ```

-   **普通用户只能查询当前账号对应DB下自己执行的慢Query日志。**

## 诊断Query

您可以通过查询hologres.hg\_query\_log表，获取慢Query日志。如下将为您介绍进行Query日志诊断常用的固定SQL语句。主要包括：

-   [查询query\_log总查询数](#li_tm1_fm2_vja)
-   [查询每个用户的慢Query情况](#li_xan_3rn_r6g)
-   [查询某个慢Query的具体信息](#li_r9l_74a_gu1)
-   [查询最近10分钟消耗比较高的Query](#li_akg_cfs_gzu)
-   [查询最近10分钟Query各阶段耗时比较高的Query](#li_pf3_lq9_849)
-   [查询最先失败的Query](#li_4fm_59h_gn0)

-   查询query\_log总查询数（默认为近1个月内的数据）。

    ```
    select count(*) from hologres.hg_query_log;
    ```

    执行结果具体如下，表示近1个月内总共执行了44个耗时大于指定阈值的Query。

    ```
    count
    -------
        44
    (1 row)
    ```

-   查询每个用户的慢Query个数。

    ```
    select usename as "用户",
           count(1) as "Query个数"
    from hologres.hg_query_log
    group by usename
    order by count(1) desc;
    ```

    执行结果具体如下，其中count\(1\)的单位是个数。

    ```
    用户                   | Query个数
    -----------------------+-----
     1111111111111111      |  27
     2222222222222222      |  11
     3333333333333333      |   4
     4444444444444444      |   2
    (4 rows)
    ```

-   查询某个慢Query的具体信息。

    ```
    select * from hologres.hg_query_log where query_id = '13001450118416xxxx';
    ```

    执行结果具体如下，更多关于各参数的说明，请参见[查看query\_log表](#section_4g5_hg4_dhj)。

    ```
           usename      | status  |      query_id      | datname| command_tag | duration | message |      query_start       | query_date |                                             query                                                  | result_rows | result_bytes | read_rows |read_bytes | affected_rows | affected_bytes | memory_bytes | shuffle_bytes | cpu_time_ms | physical_reads |   pid   | application_name | engine_type |  client_addr  | table_write | table_read |   session_id   |     session_start      | command_id | optimization_cost | start_query_cost | get_next_cost | extended_cost | plan | statistics | visualization_info | query_detail | query_extinfo
    -----------------------+---------+--------------------+---------+-------------+----------+---------+------------------------+------------+---------------------------------------------------------------------------------------------------------+-------------+--------------+-----------+------------+---------------+----------------+--------------+---------------+-------------+----------------+---------+------------------+-------------+---------------+-------------+------------+-----------------+------------------------+------------+-------------------+------------------+---------------+---------------+------+------------+--------------------+--------------+---------------
     p4_11111111111xxxx | SUCCESS | 13001450118416xxxx | dbname | SELECT      |      149 |         | 2021-03-30 23:45:01+08 | 20210330   | explain analyze select  * from tablename where user_id = '20210330010xxxx' limit 1000;             |        1000 |       417172 |         0 |         0 |            -1 |             -1 |     26731376 |     476603616 |      321626 |              0 | 1984913 | psql             | {HQE}      | 33.41.xxx.xxx |             |            | 6063475a.1e4991 | 2021-03-30 23:44:26+08 | 0          |                58 |               22 |            67 |               |      |            |                    |              |
    (1 row)
    ```

-   查询最近某个时间段（如10分钟）消耗比较高的Query。您也可以根据业务需求修改具体时间，查询目标时间段消耗比较高的Query。

    ```
    select status as "状态",
           duration as "耗时(ms)",
           query_start as "开始时间",
           (read_bytes/1048576)::text || ' MB' as "读取量",
           (memory_bytes/1048576)::text || ' MB' as "内存",
           (shuffle_bytes/1048576)::text || ' MB' as "Shuffle",
           (cpu_time_ms/1000)::text || ' s' as "CPU时间",
           physical_reads as "读盘",
           query_id as "QueryID",
           query::char(30)
     from hologres.hg_query_log
     where query_start >= now() - interval '10 min'
     order by duration desc,
              read_bytes desc,
              shuffle_bytes desc,
              memory_bytes desc,
              cpu_time_ms desc,
              physical_reads desc
     limit 100;
    ```

    执行结果具体如下：

    ```
    状态    | 耗时(ms) |    开始时间            | 读取量 | 内存  | Shuffle | CPU时间 | 读盘 |      QueryID       |             query
    ---------+---------+------------------------+--------+-------+---------+---------+------+--------------------+--------------------------------
     SUCCESS |  149    | 2021-03-30 23:45:01+08 | 0 MB   | 25 MB | 454 MB  | 321 s   |    0 | 13001450118416xxxx | explain analyze select  * from
     SUCCESS |  137    | 2021-03-30 23:49:18+08 | 247 MB | 21 MB | 213 MB  | 803 s   | 7771 | 13001491818416xxxx | explain analyze select  * from
     FAILED  |   53    | 2021-03-30 23:48:43+08 | 0 MB   | 0 MB  | 0 MB    | 0 s     |    0 | 13001484318416xxxx | select ds::bigint / 0 from pub
    (3 rows)
    ```

-   查询最近时间段（如最近10分钟）Query各阶段耗时比较高的Query。您也可以根据业务需求修改具体时间，查询目标时间段Query各阶段耗时比较高的Query。

    ```
    select status as "状态",
           duration as "耗时(ms)",
           optimization_cost as "优化耗时(ms)",
           start_query_cost as "启动耗时(ms)",
           get_next_cost as "执行耗时(ms)",
           duration-optimization_cost-start_query_cost-get_next_cost as "其他耗时(ms)",
           query_id as "QueryID",
           query::char(30)
     from hologres.hg_query_log
     where query_start >= now() - interval '10 min'
     order by duration desc,
              start_query_cost desc,
              optimization_cost,
              get_next_cost desc,
              duration-optimization_cost-start_query_cost-get_next_cost desc
     limit 100;
    ```

    执行结果具体如下：

    ```
    状态     | 耗时(ms) | 优化耗时(ms) | 启动耗时(ms) | 执行耗时(ms) | 其他耗时(ms) |      QueryID       |             query
    ---------+----------+--------------+--------------+--------------+--------------+--------------------+--------------------------------
     SUCCESS |     4572 |          521 |          320 |         3726 |            5 | 6000260625679xxxx  | -- /* user: wang ip: xxx.xx.x
     SUCCESS |     1490 |          538 |           98 |          846 |            8 | 12000250867886xxxx | -- /* user: lisa ip: xxx.xx.x
     SUCCESS |     1230 |          502 |           95 |          625 |            8 | 26000512070295xxxx | -- /* user: zhang ip: xxx.xx.
    (3 rows)
    ```

-   查询最先失败的Query。

    ```
    select status as "状态",
           regexp_replace(message, '\n', ' ')::char(150) as "报错信息",
           duration as "耗时(ms)",
           query_start as "开始时间",
           query_id as "QueryID",
           query::char(100) as "Query"
     from hologres.hg_query_log
     where query_start between '2021-03-25 17:00:00'::timestamptz
                       and '2021-03-25 17:42:00'::timestamptz + interval '2 min'
       and status = 'FAILED'
     order by query_start asc
     limit 100;
    ```

    执行结果具体如下：

    ```
    状态    |                                                                     报错信息                                                                           | 耗时(ms)  |        开始时间        |     QueryID       | Query
    --------+--------------------------------------------------------------------------------------------------------------------------------------------------------+-------+------------------------+-------------------+-------
     FAILED | Query:[1070285448673xxxx] code: kActorInvokeError msg: "[holo_query_executor.cc:330 operator()] HGERR_code XX000 HGERR_msge internal error: status { c |  1460 | 2021-03-25 17:28:54+08 | 1070285448673xxxx | S...
     FAILED | Query:[1016285560553xxxx] code: kActorInvokeError msg: "[holo_query_executor.cc:330 operator()] HGERR_code XX000 HGERR_msge internal error: status { c |   131 | 2021-03-25 17:28:55+08 | 1016285560553xxxx | S...
    (2 rows)
    ```


## 配置项

为了实现针对性地记录目标Query，Hologres有多个配置项可供选择。主要包括：

-   [log\_min\_duration\_statement](#li_j4u_fg5_uao)
-   [log\_min\_duration\_query\_stats](#li_aw8_i5w_a0s)
-   [log\_min\_duration\_query\_plan](#li_x2x_1y8_eq6)

-   **log\_min\_duration\_statement**
    -   函数说明

        该配置项记录Query耗时，系统默认记录大于100ms的Query，打开该配置项可以方便地跟踪需要优化的查询。如果某个语句的执行时间大于或者等于该毫秒数，慢Query日志会记录该语句、执行时间以及其它信息。该参数取值设置为-1则表示不记录任何查询。

        **说明：** 只支持超级用户（Superuser）修改该配置项。

    -   使用示例
        -   设置当前DB记录大于10s的Query，需要Superuser设置。

            ```
            ALTER DATABASE dbname SET log_min_duration_statement = '10s';
            ```

        -   设置当前Session记录大于10s的Query，普通用户可以执行。

            ```
            SET log_min_duration_statement = '10s';
            ```

-   **log\_min\_duration\_query\_stats**
    -   函数说明

        该配置项记录Query执行时统计信息，系统默认将记录大于10s的Query执行时的统计信息，打开这个选项主要用来跟踪需要优化的查询。如果某个语句的执行时间大于或者等于该数值，慢Query日志会记录该语句执行时的统计信息，包括Query在各个执行环节的执行信息。该参数取值设置为-1则表示关闭对query stats信息的记录。该信息存储量较高，记录较多时会降低查询分析慢Query日志的速度。因此，可以在排查具体问题时设置小于10s，否则不建议调整为更小的值。

        **说明：** 支持普通用户根据业务情况修改该配置项。

    -   使用示例
        -   设置当前DB记录大于10s的Query执行时的统计信息，需要Superuser设置。

            ```
            ALTER DATABASE dbname SET log_min_duration_query_stats = '10s';
            ```

        -   设置当前Session记录大于10s的Query执行时的统计信息，普通用户可以执行。

            ```
            SET log_min_duration_query_stats = '10s';
            ```

-   **log\_min\_duration\_query\_plan**
    -   函数说明

        该配置项记录Query的执行计划信息，系统将会默认展示大于等于10s的慢Query日志的执行计划。如果某个语句的执行时间大于或者等于该数值，慢Query日志会记录该语句的执行计划。执行计划一般情况下可以即时地通过explain得到，无需记录。该参数取值设置为-1则表示关闭对Query执行计划的记录。

        **说明：** 支持普通用户根据业务情况修改该配置项。

    -   使用示例
        -   设置当前DB记录大于10s的Query日志的执行计划，需要Superuser设置。

            ```
            ALTER DATABASE dbname SET log_min_duration_query_plan = '10s';
            ```

        -   设置当前Session记录大于10s的Query日志的执行计划，普通用户可以执行。

            ```
            SET log_min_duration_query_plan = '10s';
            ```


