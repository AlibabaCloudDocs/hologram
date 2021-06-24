# Query and analyze slow query logs \(beta\)

By querying and analyzing slow query logs, you can diagnose the causes of identified slow queries and failed queries and then take optimization measures. This topic describes how to query and analyze slow query logs in Hologres.

## Limits

Take note of the following rules when you query slow query logs in Hologres:

-   This feature is supported only by Hologres V0.10 and later. You can check the version of your Hologres instance on the instance details page in the Hologres console. If your instance version is earlier than V0.10, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   To ensure system stability and prevent traffic overloads, a query can return a maximum of 10,000 slow query logs.
-   Logs of slow queries by using the INSERT, SELECT, UPDATE, or DELETE statement can be collected and retrieved. Data definition language \(DDL\) statements and utility statements are not supported.
-   The return values of the memory\_bytes, shuffle\_bytes, and cpu\_time\_ms fields are not precise values. The values are only for reference.
-   Logs of failed queries within a Hologres instance of V0.10 do not contain specific runtime statistics, such as the memory size, disk reads, CPU time consumed, number of physical reads, and value of the query\_stats field.

## Query the hologres.hg\_query\_log table

Slow query logs are stored in the system table hologres.hg\_query\_log. If the version of your Hologres instance is V0.10, Hologres collects the logs of slow queries that consume more than 100 ms. The following table describes the fields that are contained in the hologres.hg\_query\_log table.

```
   Column           |           Type           |               Descrption                
--------------------+--------------------------+-----------------------------------------
 usename            | text                     | The name of the user that initiated the query.
 status             | text                     | The status of the query. The value indicates whether the query is successful.
 query_id           | text                     | The ID of the query.
 datname            | text                     | The name of the database whose data is queried.
 command_tag        | text                     | The type of the statement used in the query. Valid values: INSERT, SELECT, UPDATE, and DELETE.
 duration           | integer                  | The time consumed by the query, in milliseconds.
 message            | text                     | The returned error message.
 query_start        | timestamp with time zone | The beginning of the time range to query.
 query_date         | text                     | The beginning date of the time range to query.
 query              | text                     | The statement used in the query.
 result_rows        | bigint                   | The number of logs returned.
 result_bytes       | bigint                   | The number of bytes returned.
 read_rows          | bigint                   | The number of logs read by the query.
 read_bytes         | bigint                   | The number of bytes read by the query.
 affected_rows      | bigint                   | The number of logs affected by the data manipulation language (DML) statement.
 affected_bytes     | bigint                   | The number of bytes affected by the data manipulation language (DML) statement.
 memory_bytes       | bigint                   | The peak size of memory occupied by a single node. This value is not a precise value.
 shuffle_bytes      | bigint                   | The number of bytes that are shuffled. This value is not a precise value.
 cpu_time_ms        | bigint                   | The total CPU time, in milliseconds. This value is not a precise value.
 physical_reads     | bigint                   | The number of physical reads.
 pid                | integer                  | The process identifier (PID) of the query.
 application_name   | text                     | The type of the application involved in the query.
 engine_type        | text[]                   | The engine used by the query.
 client_addr        | text                     | The IP address from which the query was initiated.
 table_write        | text                     | The table to which data is written.
 table_read         | text[]                   | The table from which data is read.
 session_id         | text                     | The ID of the query session.
 session_start      | timestamp with time zone | The time when the query session started.
 command_id         | text                     | The ID of the command or statement.
 optimization_cost  | integer                  | The time consumed by the execution plan of the query.
 start_query_cost   | integer                  | The time consumed for the startup of the query.
 get_next_cost      | integer                  | The time consumed for the execution of the query.
 extended_cost      | text                     | The time consumed for other actions of the query.
 plan               | text                     | The execution plan of the query.
 statistics         | text                     | The runtime statistics of the query.
 visualization_info | text                     | The visualization information about the execution plan.
 query_detail       | text                     | The extended information about the query in JSON format.
 query_extinfo      | text[]                   | The extended information about the query in the form of an array.
```

## Grant query permissions to users

Only authorized users can query slow query logs. Take note of the following rules and use the following methods when you grant query permissions to users:

-   **A user that has the required permissions can query the slow query logs of all databases in a Hologres instance.**
    -   Grant the superuser permissions to the user.

        After the superuser permissions on a Hologres instance are granted to a user, the user can query the slow query logs of all databases in the Hologres instance.

        ```
        ALTER USER "Account ID" SUPERUSER;-- Replace Account ID with the Alibaba Cloud account ID of the user. If the user uses a RAM user, prefix the account ID with p4_. 
        ```

    -   Add the user to the pg\_read\_all\_stats user group.

        In addition to superusers, users in the pg\_read\_all\_stats user group also have the permissions to query the slow query logs of all databases in the relevant Hologres instance. If you are a superuser, you can add regular users to this user group. To add a user to this user group, execute one of the following statements:

        ```
        GRANT pg_read_all_stats TO "Account ID";-- Use the standard PostgreSQL authorization model to authorize the user.
        CALL spm_grant('pg_read_all_stats', 'Account ID');  -- Use the simple permission model (SPM) to authorize the user.
        CALL slpm_grant('pg_read_all_stats', 'Account ID'); -- Use the schema-level permission model (SLPM) to authorize the user.
        ```

-   **A user that has the required permissions can query the slow query logs of a database in a Hologres instance.**

    Users in the db\_admin user group have the permissions to query the slow query logs of the relevant database. If you are a superuser, you can add regular users to this user group by using the SPM or SLPM.

    ```
    CALL spm_grant('<db_name>_admin', 'Account ID');  -- Use the SPM to authorize the user.
    CALL slpm_grant('<db_name>.admin', 'Account ID'); -- Use the SLPM to authorize the user.
    ```

-   **A regular user has the permissions to query the logs of slow queries that were initiated by the user in the database within the relevant Alibaba Cloud account.**

## Analyze slow query logs

You can obtain slow query logs by querying the hologres.hg\_query\_log table. The following section describes the SQL statements that are commonly used to analyze slow query logs:

-   [Query the total number of slow queries](#li_tm1_fm2_vja)
-   [Query the number of slow queries initiated by each user](#li_xan_3rn_r6g)
-   [Query the details of a specified slow query](#li_r9l_74a_gu1)
-   [Query the slow queries that consume a large amount of time in the last 10 minutes](#li_akg_cfs_gzu)
-   [Query the slow queries that consume a large amount of time at different query stages in the last 10 minutes](#li_pf3_lq9_849)
-   [Query the first failed queries](#li_4fm_59h_gn0)

-   Execute the following statement to query the total number of slow queries. By default, the number of slows queries in the last 30 days is returned.

    ```
    select count(*) from hologres.hg_query_log;
    ```

    The following sample code shows the return result. The result indicates that 44 slow queries were initiated in the last 30 days. Slow queries are queries that consume more time than the specified threshold.

    ```
    count
    -------
        44
    (1 row)
    ```

-   Execute the following statements to query the number of slow queries initiated by each user:

    ```
    select username as "User",
           count(1) as "Number of slow queries"
    from hologres.hg_query_log
    group by usename
    order by count(1) desc;
    ```

    The following sample code shows the return result. The values of the count\(1\) field indicate the numbers of slow queries initiated by the users.

    ```
    User                   | Number of slow queries
    -----------------------+-----
     1111111111111111      |  27
     2222222222222222      |  11
     3333333333333333      |   4
     4444444444444444      |   2
    (4 rows)
    ```

-   Execute the following statement to query the details of a specified slow query:

    ```
    select * from hologres.hg_query_log where query_id = '13001450118416xxxx';
    ```

    The following sample code shows the return result. For more information about the fields, see [Query the hologres.hg\_query\_log table](#section_4g5_hg4_dhj).

    ```
           usename      | status  |      query_id      | datname| command_tag | duration | message |      query_start       | query_date |                                             query                                                  | result_rows | result_bytes | read_rows |read_bytes | affected_rows | affected_bytes | memory_bytes | shuffle_bytes | cpu_time_ms | physical_reads |   pid   | application_name | engine_type |  client_addr  | table_write | table_read |   session_id   |     session_start      | command_id | optimization_cost | start_query_cost | get_next_cost | extended_cost | plan | statistics | visualization_info | query_detail | query_extinfo
    -----------------------+---------+--------------------+---------+-------------+----------+---------+------------------------+------------+---------------------------------------------------------------------------------------------------------+-------------+--------------+-----------+------------+---------------+----------------+--------------+---------------+-------------+----------------+---------+------------------+-------------+---------------+-------------+------------+-----------------+------------------------+------------+-------------------+------------------+---------------+---------------+------+------------+--------------------+--------------+---------------
     p4_11111111111xxxx | SUCCESS | 13001450118416xxxx | dbname | SELECT      |      149 |         | 2021-03-30 23:45:01+08 | 20210330   | explain analyze select  * from tablename where user_id = '20210330010xxxx' limit 1000;             |        1000 |       417172 |         0 |         0 |            -1 |             -1 |     26731376 |     476603616 |      321626 |              0 | 1984913 | psql             | {HQE}      | 33.41.xxx.xxx |             |            | 6063475a.1e4991 | 2021-03-30 23:44:26+08 | 0          |                58 |               22 |            67 |               |      |            |                    |              |
    (1 row)
    ```

-   Execute the following statements to query the slow queries that consume a large amount of time in a recent time range, such as the last 10 minutes. You can specify a custom time range based on your business requirements.

    ```
    select status as "Status",
           duration as "Time consumed (ms)",
           query_start as "Started at",
           (read_bytes/1048576)::text || ' MB' as "Bytes",
           (memory_bytes/1048576)::text || ' MB' as "Memory",
           (shuffle_bytes/1048576)::text || ' MB' as "Shuffle",
           (cpu_time_ms/1000)::text || ' s' as "CPU time",
           physical_reads as "Physical reads",
           query_id as "Query ID",
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

    The following sample code shows the return result.

    ```
    Status    | Time consumed (ms) |    Started at            | Bytes | Memory  | Shuffle | CPU time | Physical reads |      Query ID       |             query
    ---------+---------+------------------------+--------+-------+---------+---------+------+--------------------+--------------------------------
     SUCCESS |  149    | 2021-03-30 23:45:01+08 | 0 MB   | 25 MB | 454 MB  | 321 s   |    0 | 13001450118416xxxx | explain analyze select  * from
     SUCCESS |  137    | 2021-03-30 23:49:18+08 | 247 MB | 21 MB | 213 MB  | 803 s   | 7771 | 13001491818416xxxx | explain analyze select  * from
     FAILED  |   53    | 2021-03-30 23:48:43+08 | 0 MB   | 0 MB  | 0 MB    | 0 s     |    0 | 13001484318416xxxx | select ds::bigint / 0 from pub
    (3 rows)
    ```

-   Execute the following statements to query the slow queries that consume a large amount of time at different query stages in a recent time range, such as the last 10 minutes. You can specify a custom time range based on your business requirements.

    ```
    select status as "Status",
           duration as "Total time (ms)",
           optimization_cost as "Optimization time (ms)",
           start_query_cost as "Startup time (ms)",
           get_next_cost as "Execution time (ms)",
           duration-optimization_cost-start_query_cost-get_next_cost as "Other (ms)",
           query_id as "Query ID",
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

    The following sample code shows the return result.

    ```
    Status     | Total time (ms) | Optimization time (ms) | Startup time (ms) | Execution time (ms) | Other (ms) |      Query ID       |             query
    ---------+----------+--------------+--------------+--------------+--------------+--------------------+--------------------------------
     SUCCESS |     4572 |          521 |          320 |         3726 |            5 | 6000260625679xxxx  | -- /* user: wang ip: xxx.xx.x
     SUCCESS |     1490 |          538 |           98 |          846 |            8 | 12000250867886xxxx | -- /* user: lisa ip: xxx.xx.x
     SUCCESS |     1230 |          502 |           95 |          625 |            8 | 26000512070295xxxx | -- /* user: zhang ip: xxx.xx.
    (3 rows)
    ```

-   Execute the following statements to query the first failed queries:

    ```
    select status as "Status",
           regexp_replace(message, '\n', ' ')::char(150) as "Error message",
           duration as "Time consumed (ms)",
           query_start as "Started at",
           query_id as "Query ID",
           query::char(100) as "Statement"
     from hologres.hg_query_log
     where query_start between '2021-03-25 17:00:00'::timestamptz
                       and '2021-03-25 17:42:00'::timestamptz + interval '2 min'
       and status = 'FAILED'
     order by query_start asc
     limit 100;
    ```

    The following sample code shows the return result.

    ```
    Status    |                                                                     Error message                                                                           | Time consumed (ms)  |        Started at        |     Query ID       | Statement
    --------+--------------------------------------------------------------------------------------------------------------------------------------------------------+-------+------------------------+-------------------+-------
     FAILED | Query:[1070285448673xxxx] code: kActorInvokeError msg: "[holo_query_executor.cc:330 operator()] HGERR_code XX000 HGERR_msge internal error: status { c |  1460 | 2021-03-25 17:28:54+08 | 1070285448673xxxx | S...
     FAILED | Query:[1016285560553xxxx] code: kActorInvokeError msg: "[holo_query_executor.cc:330 operator()] HGERR_code XX000 HGERR_msge internal error: status { c |   131 | 2021-03-25 17:28:55+08 | 1016285560553xxxx | S...
    (2 rows)
    ```


## Configuration items

Hologres provides the following configuration items to allow you to customize the collection configuration of slow query logs:

-   [log\_min\_duration\_statement](#li_j4u_fg5_uao)
-   [log\_min\_duration\_query\_stats](#li_aw8_i5w_a0s)
-   [log\_min\_duration\_query\_plan](#li_x2x_1y8_eq6)

-   **log\_min\_duration\_statement**
    -   Description

        This configuration item is used to specify a threshold of the time consumed by queries for log collection. By default, Hologres collects the logs of slow queries that consume more than 100 ms. You can use this configuration item to specify the range of slow query logs to be collected. If the time consumed by a slow query is equal to or larger than the specified value, its log is collected. The log contains information such as the SQL statement and time consumed. A value of -1 indicates that no slow query logs are collected.

        **Note:** Only superusers can modify this configuration item at the database level.

    -   Examples
        -   Execute the following statement to collect the logs of slow queries that consume more than 10s for the current database as a superuser:

            ```
            ALTER DATABASE dbname SET log_min_duration_statement = '10s';
            ```

        -   Execute the following statement to collect the logs of slow queries that consume more than 10s for the current session as a regular user:

            ```
            SET log_min_duration_statement = '10s';
            ```

-   **log\_min\_duration\_query\_stats**
    -   Description

        This configuration item is used to specify a threshold of the time consumed by queries for the collection of runtime statistics. By default, Hologres collects the runtime statistics of slow queries that consume more than 10s. You can use this configuration item to specify the range of slow queries whose runtime statistics are to be collected. If the time consumed by a slow query is equal to or larger than the specified value, its runtime statistics, including the statistics of all query stages, are collected. A value of -1 indicates that no runtime statistics of slow queries are collected. Such statistics require large storage space. The more runtime statistics Hologres collects, the slower the speed of querying and analyzing slow query logs is. Therefore, we recommend that you specify a value smaller than 10s only when you troubleshoot specific issues. In other scenarios, specify a value equal to or larger than 10s.

        **Note:** Regular users are allowed to modify this configuration item based on business requirements.

    -   Examples
        -   Execute the following statement to collect the runtime statistics of slow queries that consume more than 10s for the current database as a superuser:

            ```
            ALTER DATABASE dbname SET log_min_duration_query_stats = '10s';
            ```

        -   Execute the following statement to collect the runtime statistics of slow queries that consume more than 10s for the current session as a regular user:

            ```
            SET log_min_duration_query_stats = '10s';
            ```

-   **log\_min\_duration\_query\_plan**
    -   Description

        This configuration item is used to specify a threshold of the time consumed by queries for the collection of execution plans. By default, Hologres collects the execution plans of slow queries that consume more than 10s. If the time consumed by a slow query is equal to or larger than the specified value, its execution plan is collected. By default, you can execute the EXPLAIN statement to query execution plans in real time and you do not need to collect such data. A value of -1 indicates that no execution plans of slow queries are collected.

        **Note:** Regular users are allowed to modify this configuration item based on business requirements.

    -   Examples
        -   Execute the following statement to collect the execution plans of slow queries that consume more than 10s for the current database as a superuser:

            ```
            ALTER DATABASE dbname SET log_min_duration_query_plan = '10s';
            ```

        -   Execute the following statement to collect the execution plans of slow queries that consume more than 10s for the current session as a regular user:

            ```
            SET log_min_duration_query_plan = '10s';
            ```


