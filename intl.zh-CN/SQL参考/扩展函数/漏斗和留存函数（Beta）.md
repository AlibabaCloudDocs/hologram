---
keyword: [Hologres, 漏斗函数, 留存函数]
---

# 漏斗和留存函数（Beta）

本文将会为您介绍在Hologres中漏斗（windowFunnel）和留存（retention）函数的使用。

## 背景信息

漏斗分析是常见的转化分析的方法，是黑客增长中著名的理论模型。它常被用于电商，互联网等行业。它用于反映用户从起点到终点的各个阶段的行为转化率。漏斗分析被广泛应用于用户行为分析和APP数据分析的流量分析、产品目标转化等日常数据运营与数据分析的工作中。

## 使用限制

仅Hologres V0.9及以上版本支持使用漏斗函数和留存函数，如果您的实例是V0.9以下版本，请您[提交工单](https://workorder-intl.console.aliyun.com/)或加入在线钉钉群升级实例。

## 漏斗函数

-   函数说明

    漏斗函数（windowFunnel）可以搜索滑动时间窗口中的事件列表，并计算条件匹配的事件列表的最大长度。

    搜索事件列表，从第一个事件开始匹配，依次做最长、有序匹配，返回匹配的最大长度。一旦匹配失败，结束整个匹配。

    假设在窗口足够大的条件下：条件事件为c1，c2，c3，而用户数据为c1，c2，c3，c4，最终匹配到c1，c2，c3，函数返回值为3。条件事件为c1，c2，c3，而用户数据为c4，c3，c2，c1，最终匹配到c1，函数返回值为1。条件事件为c1，c2，c3，而用户数据为c4，c3，最终没有匹配到事件，函数返回值为0。

-   函数语法

    ```
    windowFunnel(window, mode, timestamp, cond1, cond2, ..., condN)
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |window|窗口大小，即从指定的第一个事件开始，往后推移一个窗口来提取相关事件数据。|
    |mode|模式。支持 default和strict两种模式 ，默认为default。当设置strict时，windowFunnel\(\)仅对唯一值应用匹配条件。|
    |timestamp|包含时间的列，支持timestamp、int、bigint类型。|
    |cond|事件的每个步骤。|

-   使用示例

    在开始操作示例之前，您需要执行以下语句开启extension，开启之后可以通过语句调用函数。extension是DB级别的，一个DB只需要开启一次即可。

    ```
    create extension flow_analysis;
    ```

    -   创建一张数据表，SQL 语句如下。

        ```
        CREATE TABLE IF NOT EXISTS funnel_test
        (
            user_id INT ,
            event_type text,
            event_time TIMESTAMP 
        );
        ```

    -   导入数据。该数据为一个用户购买商品的全过程数据，主要路径为浏览页面、点击商品、加入购物车、支付货款。具体表格和SQL语句如下所示。

        |user\_id|event\_type|event\_time|
        |--------|-----------|-----------|
        |1|浏览页面|2021-01-31 11:00:00|
        |1|点击商品|2021-01-31 11:10:00|
        |1|加入购物车|2021-01-31 11:20:00|
        |1|支付货款|2021-01-31 11:30:00|
        |2|加入购物车|2021-01-31 11:00:00|
        |2|支付货款|2021-01-31 11:10:00|
        |1|浏览页面|2021-01-31 11:00:00|
        |3|浏览页面|2021-01-31 11:20:00|
        |3|点击商品|2021-01-31 12:00:00|
        |4|浏览页面|2021-01-31 11:50:00|
        |4|点击商品|2021-01-31 12:00:00|
        |5|浏览页面|2021-01-31 11:50:00|
        |5|点击商品|2021-01-31 12:00:00|
        |5|加入购物车|2021-01-31 11:10:00|
        |6|浏览页面|2021-01-31 11:50:00|
        |6|点击商品|2021-01-31 12:00:00|
        |6|加入购物车|2021-01-31 12:10:00|

        ```
        INSERT INTO funnel_test VALUES
        (1,'浏览页面','2021-01-31 11:00:00'),
        (1,'点击商品','2021-01-31 11:10:00'),
        (1,'加入购物车','2021-01-31 11:20:00'),
        (1,'支付货款','2021-01-31 11:30:00'),
        (2,'加入购物车','2021-01-31 11:00:00'),
        (2,'支付货款','2021-01-31 11:10:00'),
        (1,'浏览页面','2021-01-31 11:00:00'),
        (3,'浏览页面','2021-01-31 11:20:00'),
        (3,'点击商品','2021-01-31 12:00:00'),
        (4,'浏览页面','2021-01-31 11:50:00'),
        (4,'点击商品','2021-01-31 12:00:00'),
        (5,'浏览页面','2021-01-31 11:50:00'),
        (5,'点击商品','2021-01-31 12:00:00'),
        (5,'加入购物车','2021-01-31 11:10:00'),
        (6,'浏览页面','2021-01-31 11:50:00'),
        (6,'点击商品','2021-01-31 12:00:00'),
        (6,'加入购物车','2021-01-31 12:10:00');
        ```

        -   以30分钟（即1800秒）间隔为例，主要路径为浏览页面、点击商品、加入购物车、支付货款，统计各个用户的漏斗情况。

            ```
            SELECT
                user_id,
              windowFunnel(
                1800
                ,'default'
                ,event_time
                ,event_type = '浏览页面'
                ,event_type = '点击商品'
                ,event_type = '加入购物车'
                ,event_type = '支付货款'
              ) AS level
            FROM funnel_test
            GROUP BY user_id
            ORDER BY user_id ASC
            ;
            ```

            显示结果：

            ```
             user_id | level 
            ---------+-------
                   1 |     4
                   2 |     0
                   3 |     1
                   4 |     2
                   5 |     2
                   6 |     3
            (6 rows)
            ```

        -   如果需分析2021-01-31 11:00:00至2021-01-31 12:00:00的数据，对应的SQL语句如下：

            ```
            SELECT
                user_id,
              windowFunnel(
                1800
                ,'default'
                ,event_time
                ,event_type = '浏览页面'
                ,event_type = '点击商品'
                ,event_type = '加入购物车'
                ,event_type = '支付货款'
              ) AS level
            FROM funnel_test
            WHERE (event_time >= '2021-01-31 11:00:00') 
                AND (event_time <= '2021-01-31 12:00:00')
            GROUP BY user_id
            ORDER BY user_id ASC
            ;
            ```

            显示结果：

            ```
             user_id | level 
            ---------+-------
                   1 |     4
                   2 |     0
                   3 |     1
                   4 |     2
                   5 |     2
                   6 |     2
            (6 rows)
            ```

        -   一般漏斗图会按照level进行统计，对应的SQL语句如下：

            ```
            SELECT
                level,
              COUNT(1) AS count_user
            FROM
            (
                SELECT
                  user_id,
                windowFunnel(
                    1800,
                  'default',
                  event_time,
                  event_type = '浏览页面',
                  event_type = '点击商品',
                  event_type = '加入购物车',
                  event_type = '支付货款'
                  ) AS level
                FROM    funnel_test
                GROUP BY user_id
              ) AS basic_table
            GROUP BY level
            ORDER BY level ASC
            ;
            ```

            显示结果：

            ```
             level | count_user 
            -------+------------
                 0 |          1
                 1 |          1
                 2 |          2
                 3 |          1
                 4 |          1
            (5 rows)
            ```

        -   一般漏斗分析均为累计结果，对应的SQL语句如下：

            ```
            WITH
                test AS ( 
                  SELECT
                      level, 
                      COUNT(1) AS count_user 
                  FROM ( 
                    SELECT 
                    user_id, 
                    windowFunnel( 
                      1800, 
                      'default', 
                      event_time, 
                      event_type = '浏览页面', 
                      event_type = '点击商品', 
                      event_type = '加入购物车', 
                      event_type = '支付货款' 
                    ) AS level 
                    FROM funnel_test 
                    GROUP BY user_id 
                  ) AS basic_table 
                  GROUP BY level 
                  ORDER BY level ASC 
                )
            SELECT
                level,
              SUM(count_user) over( ORDER BY level DESC )
            FROM test
            GROUP BY level,
                             count_user
            ORDER BY level ASC
            ;
            ```

            显示结果：

            ```
             level | sum 
            -------+-----
                 0 |   6
                 1 |   5
                 2 |   4
                 3 |   2
                 4 |   1
            (5 rows)
            ```

    -   使用BI即可得到一张漏斗图。

        ![漏斗图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7928273161/p229480.png)


## 留存函数

-   函数说明

    该函数将一组条件作为参数，类型为1到32个UInt8类型的参数，用来表示事件是否满足特定条件。

-   函数语法

    ```
    retention(cond1, cond2, ..., cond32);
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |cond|返回结果的表达式。返回值包括：    -   1，条件满足。
    -   0，条件不满足。 |

-   使用示例
    -   创建一张数据表，以用户访问APP为例，SQL 语句如下：

        ```
        CREATE TABLE IF NOT EXISTS login_log
        (
            user_id INT ,
            log_time TIMESTAMP 
        );
        ```

    -   导入相关数据，具体表格和示例语句如下：

        |user\_id|log\_time|
        |--------|---------|
        |1101|2019-06-21 22:00:00|
        |1101|2019-06-20 22:00:00|
        |1101|2019-06-19 22:00:00|
        |1101|2019-06-17 22:00:00|
        |1101|2019-06-16 22:00:00|
        |1101|2019-06-21 22:00:00|
        |1101|2019-06-21 23:00:00|
        |1101|2019-06-20 23:00:00|
        |1101|2019-06-19 23:00:00|
        |1101|2019-06-17 23:00:00|
        |1101|2019-06-16 23:00:00|
        |1101|2019-06-21 23:00:00|
        |4101|2019-06-20 22:00:00|
        |4101|2019-06-19 22:00:00|
        |4101|2019-06-17 22:00:00|
        |4101|2019-06-16 22:00:00|
        |2201|2019-06-21 22:00:00|
        |2201|2019-06-20 22:00:00|
        |2201|2019-06-21 23:00:00|
        |2201|2019-06-20 23:00:00|
        |3301|2019-06-21 22:00:00|
        |3301|2019-06-19 22:00:00|
        |3301|2019-06-18 23:00:00|
        |3301|2019-06-17 23:00:00|
        |3301|2019-06-16 23:00:00|
        |3301|2019-06-15 23:00:00|

        ```
        INSERT INTO login_log VALUES
        (1101,'2019-06-21 22:00:00'),
        (1101,'2019-06-20 22:00:00'),
        (1101,'2019-06-19 22:00:00'),
        (1101,'2019-06-17 22:00:00'),
        (1101,'2019-06-16 22:00:00'),
        (1101,'2019-06-21 22:00:00'),
        (1101,'2019-06-21 23:00:00'),
        (1101,'2019-06-20 23:00:00'),
        (1101,'2019-06-19 23:00:00'),
        (1101,'2019-06-17 23:00:00'),
        (1101,'2019-06-16 23:00:00'),
        (1101,'2019-06-21 23:00:00'),
        (4101,'2019-06-20 22:00:00'),
        (4101,'2019-06-19 22:00:00'),
        (4101,'2019-06-17 22:00:00'),
        (4101,'2019-06-16 22:00:00'),
        (2201,'2019-06-21 22:00:00'),
        (2201,'2019-06-20 22:00:00'),
        (2201,'2019-06-21 23:00:00'),
        (2201,'2019-06-20 23:00:00'),
        (3301,'2019-06-21 22:00:00'),
        (3301,'2019-06-19 22:00:00'),
        (3301,'2019-06-18 23:00:00'),
        (3301,'2019-06-17 23:00:00'),
        (3301,'2019-06-16 23:00:00'),
        (3301,'2019-06-15 23:00:00');
        ```

    -   -   如需查询用户在2019-06-17至2019-06-21登录流失情况，则SQL语句如下：

    ```
    SELECT
        user_id,
      retention(
        date(log_time) = '2019-06-21',
        date(log_time) = '2019-06-20',
        date(log_time) = '2019-06-19',
        date(log_time) = '2019-06-18',
        date(log_time) = '2019-06-17'
        ) AS r
    FROM login_log 
    GROUP BY user_id
    ORDER BY user_id ASC;
    ```

-   具体结果如下，其中数组r表示用户每天登录情况，1和0表示该用户登录或未登录，user\_id为4101的用户，在2019-06-17 至 2019-06-21期间有登录记录，但数据全是0。这是因为在retention\(cond1, cond2, …\) 函数中，第一个表达 cond1 如果不满足，那么后面所有的结果就都是0。由于user\_id为4101的用户2019-06-21当天没登录，所有后面的数据全部为0。

    ```
     user_id |      r      
    ---------+-------------
        1101 | {1,1,1,0,1}
        2201 | {1,1,0,0,0}
        3301 | {1,0,1,1,1}
        4101 | {0,0,0,0,0}
    (4 rows)
    ```

-   若不希望出现如上情况，仅需将cond1变为true即可。

    ```
    SELECT
        user_id,
      retention(
        true,
        date(log_time) = '2019-06-21',
        date(log_time) = '2019-06-20',
        date(log_time) = '2019-06-19',
        date(log_time) = '2019-06-18',
        date(log_time) = '2019-06-17'
        ) AS r
    FROM login_log 
    GROUP BY user_id
    ORDER BY user_id ASC;
    ```

    查询结果如下：

    ```
     user_id |       r       
    ---------+---------------
        1101 | {1,1,1,1,0,1}
        2201 | {1,1,1,0,0,0}
        3301 | {1,1,0,1,1,1}
        4101 | {1,0,1,1,0,1}
    (4 rows)
    ```

    -   使用留存函数能便捷地计算用户的留存率。例如我们想计算用户的次日留存、3日留存、7日留存，样例SQL如下：

        ```
        -- 根据计算数组中SUM(r[index])获取2019-06-15活跃用户在第2、3、7日的留存数，以此计算留存率
        SELECT
            DATE(TIMESTAMP '2019-06-15 00:00:00') AS first_date,
            CAST(SUM(r[1]) AS NUMERIC) AS "第一天活跃用户",
            CAST(SUM(r[2]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "次日留存",
            CAST(SUM(r[3]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "3日留存",
            CAST(SUM(r[4]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "7日留存"
        FROM
        -- 计算2019-06-15活跃用户在第2、3、7日的登录情况，1/0 => 登录/未登录
            (
                WITH 
                    first_day_table AS ( SELECT TIMESTAMP '2019-06-15 00:00:00' AS first_day)
                SELECT
                    user_id,
                    retention(
                        DATE(log_time) = (SELECT DATE(first_day) FROM first_day_table),
                        DATE(log_time) = (SELECT DATE(first_day + INTERVAL '1 day') FROM first_day_table),
                        DATE(log_time) = (SELECT DATE(first_day + INTERVAL '2 day') FROM first_day_table),
                        DATE(log_time) = (SELECT DATE(first_day + INTERVAL '6 day') FROM first_day_table)
                        ) AS r
        -- 过滤2019-06-15活跃用户在后续 1～7 日登录数据
                    FROM    login_log
                    WHERE   (log_time >= TIMESTAMP '2019-06-15 00:00:00')
                    AND     (log_time <= TIMESTAMP '2019-06-15 00:00:00' + INTERVAL '6 day')
                    GROUP BY user_id
                ) AS basic_table
        GROUP BY first_date
        ;
        ```


