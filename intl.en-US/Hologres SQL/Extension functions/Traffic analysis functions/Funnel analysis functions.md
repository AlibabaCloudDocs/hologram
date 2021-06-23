# Funnel analysis functions

Funnel analysis is a common type of conversion analysis. It is used to reflect the conversion rates of user behavior in various stages of a process. Funnel analysis is widely used for data operations and data analysis, such as the analyses of user behavior, application data traffic, and product goal conversion. This topic describes how to use funnel analysis functions in Hologres.

## Limits

Hologres provides the [windowFunnel](#section_ji2_4gu_6za), [retention](#section_onu_nio_q4t), [range\_retention\_count](#section_6j3_5t2_00n), and [range\_retention\_sum](#section_6j3_5t2_00n) functions to help you perform funnel analysis in various scenarios. When you use the functions, take note of the following limits:

-   Only Hologres V0.9 and later allow you to use the windowFunnel and retention functions. You can view the version of your Hologres instance on the instance details page in the Hologres console.
-   Only Hologres V0.10 and later allow you to use the range\_retention\_count and range\_retention\_sum functions. If the version of your Hologres instance is V0.9 or earlier, [submit a ticket](https://workorder-intl.console.aliyun.com/) to upgrade your instance.
-   Before you use the functions, you must execute the following statement to install an extension as the superuser. Then, you can use statements to call the functions. An extension is installed at the database level. For each database, you need to install an extension only once.

    ```
    create extension flow_analysis; -- Install an extension.
    ```


## Scenario

This topic shows you how to use funnel analysis functions in a simplified online shopping process. The process contains the following events:

-   Browse a product
-   Add to favorites
-   Add to the shopping cart
-   Purchase the product

The following table lists the sample data.

|user\_id|event\_type|event\_time|
|--------|-----------|-----------|
|4913|Browse a product|2014-11-18 00:00:00|
|501286|Browse a product|2014-11-18 00:00:00|
|501286|Add to the shopping cart|2014-11-18 00:00:00|
|632347|Browse a product|2014-11-18 00:00:00|
|814199|Browse a product|2014-11-18 00:00:00|
|814199|Add to favorites|2014-11-18 00:00:00|
|1259845|Browse a product|2014-11-18 00:00:00|
|1259845|Purchase the product|2014-11-18 00:00:00|
|1498131|Browse a product|2014-11-18 00:00:00|
|1498131|Add to favorites|2014-11-18 00:00:00|
|1857066|Browse a product|2014-11-18 00:00:00|
|1926899|Browse a product|2014-11-18 00:00:00|
|3318666|Browse a product|2014-11-18 00:00:00|
|3324323|Browse a product|2014-11-18 00:00:00|
|3442818|Browse a product|2014-11-18 00:00:00|

You can execute the following statement to create a table:

```
BEGIN;
DROP TABLE IF EXISTS public.user_analysis;
CREATE TABLE IF NOT EXISTS public.user_analysis
(
    user_id INT NOT NULL ,
      event_type    TEXT ,
    event_time TIMESTAMP NOT NULL 
);
call set_table_property('user_analysis', 'distribution_key', 'user_id');
call set_table_property('user_analysis', 'segment_key', 'event_time');
call set_table_property('user_analysis', 'clustering_key', 'event_time');
call set_table_property('user_analysis', 'bitmap_columns', 'event_type');
COMMIT;
```

## windowFunnel

-   **Description**

    You can use the windowFunnel function to query the event list in a sliding time window. The function calculates the maximum number of events in the event list that match the events you specify as query conditions.

    The function returns the maximum number of ordered and matched events from the first event based on the event list. If a match fails, the entire match ends.

    Assume that the time window is long enough:

    -   You want to query events c1, c2, and c3, and the user data is c1, c2, c3, and c4. The matched events are c1, c2, and c3. Therefore, the function returns 3.
    -   You want to query events c1, c2, and c3, and the user data is c4, c3, c2, and c1. The event c1 is matched. Therefore, the function returns 1.
    -   You want to query events c1, c2, and c3, and the user data is c4 and c3. No events are matched. Therefore, the function returns 0.
-   **Syntax**

    ```
    windowFunnel(window, mode, timestamp, cond1, cond2, ..., condN)
    ```

-   **Parameters**

    |Parameter|Description|
    |---------|-----------|
    |window|The length of the time window. The windowFunnel function uses the first event in the specified query conditions as the start point of the time window. Then, the function determines the event list based on the start point and the length of the time window.|
    |mode|The working mode of the function. Two modes are supported: default and strict. Default value: default. In strict mode, the windowFunnel function applies the query conditions only to unique values.|
    |timestamp|The column that contains time values. Supported data types are TIMESTAMP, INT, and BIGINT.|
    |cond|An event in the query conditions.|

-   **Examples**

    If you want to analyze the funnel in which the events of users match the order of the specified events within a period of time, you can specify the following query conditions in SQL statements:

    -   Time window: 30 minutes, which equals 1,800 seconds
    -   Time period: 2014-11-25 00:00:00 to 2014-11-26 00:00:00
    -   Events in the following order: browse a product, add to favorites, add to the shopping cart, and purchase the product
    ```
    WITH
        level_detail AS ( 
            SELECT 
                level
                ,COUNT(1) AS count_user 
            FROM ( 
                SELECT 
                    user_id 
                    ,windowFunnel( 
                        1800
                        ,'default'
                        ,event_time
                        ,event_type = 'Browse a product'
                        ,event_type = 'Add to favorites'
                        ,event_type = 'Add to the shopping cart'
                        ,event_type = 'Purchase the product' 
                        ) AS level 
                FROM public.user_analysis
                WHERE event_time >= TIMESTAMP '2014-11-25 00:00:00'
                    AND event_time < TIMESTAMP '2014-11-26 00:00:00'
                GROUP BY user_id 
                ) AS basic_table 
            GROUP BY level 
            ORDER BY level ASC )
    SELECT  CASE level    WHEN 0 THEN 'Total number of users'
                          WHEN 1 THEN 'Browse a product'
                          WHEN 2 THEN 'Add to favorites'
                          WHEN 3 THEN 'Add to the shopping cart'
                          WHEN 4 THEN 'Purchase the product' 
                  END
            ,SUM(count_user) over ( ORDER BY level DESC )
    FROM    level_detail
    GROUP BY level
             ,count_user
    ORDER BY level ASC
    ;
    ```

    The following results are returned:

    ```
        case    | sum  
    ------------+------
     Total number of users   | 6351
     Browse a product   | 6346
     Add to favorites   |  997
     Add to the shopping cart |   88
     Purchase the product   |    9
    (5 rows)
    ```


## retention

-   **Description**

    The retention function takes a set of conditions as parameters to determine whether an event meets the specific conditions. The parameters specify 1 to 32 conditions whose return values are of the UINT8 type.

-   **Syntax**

    ```
    retention(cond1, cond2, ..., cond32);
    ```

-   **Parameters**

    |Parameter|Description|
    |---------|-----------|
    |cond|A conditional expression that returns a result. Valid values:    -   1: The condition is met.
    -   0: The condition is not met. |

-   **Examples**

    If you want to analyze user retention from a specific start date, you can specify the following query conditions in SQL statements:

    -   Start date: November 25, 2014
    -   Metrics: active users on the first day, the second-day user retention rate, the third-day user retention rate, and the seventh-day user retention rate
    ```
    -- Use SUM(r[index]) to obtain the logon records of active users on November 25, 2014 and their logon records on the second, third, and seventh days. Then, calculate user retention rates.
    SELECT
        DATE '2014-11-25 00:00:00' AS "Start date",
        SUM(r[1])::NUMERIC AS "Active users on the first day",
        SUM(r[2])::NUMERIC/SUM(r[1])::NUMERIC AS "Second-day retention rate",
        SUM(r[3])::NUMERIC/SUM(r[1])::NUMERIC AS "Third-day retention rate",
        SUM(r[4])::NUMERIC/SUM(r[1])::NUMERIC AS "Seventh-day retention rate"
    FROM
    -- Use the retention function to obtain arrays that show the second-day, third-day, and seventh-day logon records of active users on November 25, 2014. Each value of the array r indicates the logon records of a user. The element 1 indicates that the user logged on that day. The element 0 indicates that the user did not log on that day. 
        (
            WITH 
                first_day_table AS ( SELECT TIMESTAMP '2014-11-25 00:00:00' AS first_day)
            SELECT
                user_id,
                retention(
                    DATE(event_time) = (SELECT DATE(first_day) FROM first_day_table),
                    DATE(event_time) = (SELECT DATE(first_day + INTERVAL '1 day') FROM first_day_table),
                    DATE(event_time) = (SELECT DATE(first_day + INTERVAL '2 day') FROM first_day_table),
                    DATE(event_time) = (SELECT DATE(first_day + INTERVAL '6 day') FROM first_day_table)
                    ) AS r
    -- Query the logon records of active users on November 25, 2014 in the next six days.
                FROM    public.user_analysis
                WHERE   (event_time >= TIMESTAMP '2014-11-25 00:00:00')
                AND     (event_time <= TIMESTAMP '2014-11-25 00:00:00' + INTERVAL '6 day')
                GROUP BY user_id
            ) AS basic_table
    GROUP BY "Start date"
    ;
    ```

    The following results are returned:

    ```
      Start date  | Active users on the first day | Second-day retention rate | Third-day retention rate  | Seventh-day retention rate  
    ------------+---------------+----------+----------+----------
     2014-11-25 |          6351 | 0.796410 | 0.769013 | 0.088647
    (1 row)
    ```


## range\_retention\_count and range\_retention\_sum

-   **Description**

    Retention analysis is the most common and typical scenario where user growth is analyzed. In most cases, you can use charts to analyze user retention.

    ![Chart](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8464344261/p262441.png)

    Hologres provides the range\_retention\_count and range\_retention\_sum functions for retention analysis. The return values of the range\_retention\_count function are arrays of the BIGINT type. The return values cannot be read but can be used as the input of the range\_retention\_sum function. The return values of the range\_retention\_sum function are arrays of the TEXT type. Examples:

    ```
     {
      "20210306,562574,413024,343126,291178,215240,0,0,0,0,0,0,0,0,0,0,0", 
      "20210309,502617,376019,332303,273917,208421,0,0,0,0,0,0,0,0,0,0,0",
       "20210305,550118,412264,357542,297458,216549,0,0,0,0,0,0,0,0,0,0,0",
       "20210307,560659,402810,344581,291851,214705,0,0,0,0,0,0,0,0,0,0,0",
       "20210308,527392,383356,346084,282764,212309,0,0,0,0,0,0,0,0,0,0,0"
     }
    ```

-   **Syntax**

    ```
    range_retention_count(is_first,is_active,dt,retention_interval,retention_granularity)
    ```

-   **Parameters**

    |Parameter|Type|Description|
    |---------|----|-----------|
    |is\_first|bool|A Boolean value that indicates whether the first event is matched. Valid values:     -   true: The first event is matched.
    -   false: The first event is not matched. |
    |is\_active|bool|A Boolean value that indicates whether the retention condition is met. Valid values:     -   true: The retention condition is met.
    -   false: The retention condition is not met. |
    |dt|date|The date on which the event occurs. Example: 2020-12-12.|
    |retention\_interval|int\[\]|The retention interval. A maximum of 15 retention intervals are supported. Example: ARRAY\[1,3,5,7,15,30\].|
    |retention\_granularity|text|The retention granularity. Valid values:    -   day
    -   week
    -   month |

-   **Examples**

    If you want to analyze user retention within a period of time and use the Browse a product event as the first event of users, you can use the following SQL statements:

    ```
    WITH tbl_detail AS (
            SELECT range_retention_count(is_first, is_active, dt, ARRAY[1, 3, 7],'day' ) AS detail
            FROM (
                SELECT user_id, event_time::DATE AS dt
                    , CASE 
                        WHEN 
                        event_time >= timestamp '2014-11-25 00:00:00'
                        AND event_time < timestamp '2014-11-25 00:00:00' + INTERVAL '7' day
                        and event_type = 'Browse a product' -- Use the Browse a product event as the first event.
                        THEN true
                        ELSE false
                    END AS is_first
                    , CASE 
                        WHEN event_time >= timestamp '2014-11-25 00:00:00' + INTERVAL '1' day
                        AND event_time < timestamp '2014-11-25 00:00:00' + INTERVAL '7' day + INTERVAL '7' day
                        THEN true
                        ELSE false
                    END AS is_active
                FROM public.user_analysis
            ) tbl
            GROUP BY user_id
        ),
        tbl_sum AS (
            SELECT regexp_split_to_array(unnest(range_retention_sum(detail)), ',') AS s
            FROM tbl_detail
        )
    SELECT s[1] AS Start date
            ,s[3]::numeric / s[2]::numeric AS First day
            ,s[4]::numeric / s[2]::numeric AS Third day
            ,s[5]::numeric / s[2]::numeric AS Seventh day
    FROM tbl_sum
    ORDER BY s[1];
    ```

    The following results are returned:

    ```
      Start date|    First day     |    Third day     |    Seventh day     
    ----------+--------------+--------------+--------------
     20141125 | 0.7962496060 | 0.7533879609 | 0.7666246454
     20141126 | 0.7904926806 | 0.7547615299 | 0.7684558476
     20141127 | 0.7711184521 | 0.7651407896 | 0.7564889098
     20141128 | 0.7799514955 | 0.7801131770 | 0.7414713015
     20141129 | 0.7946945337 | 0.7770096463 | 0.7500000000
     20141130 | 0.7944496707 | 0.7754782063 | 0.7483537158
     20141201 | 0.8017728870 | 0.7780834479 | 0.7545468439
    (7 rows)
    ```


