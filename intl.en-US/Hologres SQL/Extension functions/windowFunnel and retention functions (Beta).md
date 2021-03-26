---
keyword: [Hologres, the windowFunnel function, the retention function]
---

# windowFunnel and retention functions \(Beta\)

This topic describes how to use the windowFunnel function and the retention function in Hologres.

## Background information

Funnel analysis is a common type of conversion analysis and a famous theoretical model in growth hacking. It is often used in industries such as e-commerce and the Internet. Funnel analysis is used to reflect the conversion rates of user behavior in each stage from the start event to the end event. Funnel analysis is widely used in routine work related to data operations and data analysis, such as the analyses of user behavior, application data traffic, and product goal conversion.

## Limits

Only Hologres V0.9 and later allow you to use the windowFunnel function and the retention function. If the version of your Hologres instance is earlier than V0.9, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.

## windowFunnel function

-   Function description

    You can use the windowFunnel function to query the event list in a sliding time window. The function calculates the maximum number of events in the event list that match the events that you specify as the query condition.

    The function returns the maximum number of ordered and matched events from the first event based on the event list. If a match fails, the entire match ends.

    Assume that the length of the time window is large enough, you want to query events c1, c2, and c3, and the user data is c1, c2, c3, and c4. The matched events are c1, c2, and c3. Therefore, the function returns 3. Assume that you want to query events c1, c2, and c3, and the user data is c4, c3, c2, and c1. The event c1 is matched. Therefore, the function returns 1. Assume that you want to query events c1, c2, and c3, and the user data is c4 and c3. No events are matched. Therefore, the function returns 0.

-   Syntax

    ```
    windowFunnel(window, mode, timestamp, cond1, cond2, ..., condN)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |window|The length of the time window. The windowFunnel function uses the first event in the specified query condition as the start point of the time window. Then, the function determines the event list based on the start point and the length of the time window.|
    |mode|The working mode of the windowFunnel function. Two modes are supported: default and strict. Default value: default. In strict mode, the windowFunnel function applies the matching condition only to unique values.|
    |timestamp|The column that contains time values. Supported data types are TIMESTAMP, INT, and BIGINT.|
    |cond|An event in the query condition.|

-   Examples

    Before you proceed to the usage example, you must execute the following statement to enable extension. After that, you can call the windowFunnel function by using a statement. Extension is a database-level feature. For each database, you need to enable the feature only once.

    ```
    create extension flow_analysis;
    ```

    -   Create a table. Use the following SQL statement:

        ```
        CREATE TABLE IF NOT EXISTS funnel_test
        (
            user_id INT ,
            event_type text,
            event_time TIMESTAMP 
        );
        ```

    -   Import data. In this example, the data shows the online shopping process of users. The main events in the process are in the following order: browse a page, click a product, add to the shopping cart, and complete the payment. The following table and SQL statement show the details.

        |user\_id|event\_type|event\_time|
        |--------|-----------|-----------|
        |1|Browse a page|2021-01-31 11:00:00|
        |1|Click a product|2021-01-31 11:10:00|
        |1|Add to the shopping cart|2021-01-31 11:20:00|
        |1|Complete the payment|2021-01-31 11:30:00|
        |2|Add to the shopping cart|2021-01-31 11:00:00|
        |2|Complete the payment|2021-01-31 11:10:00|
        |1|Browse a page|2021-01-31 11:00:00|
        |3|Browse a page|2021-01-31 11:20:00|
        |3|Click a product|2021-01-31 12:00:00|
        |4|Browse a page|2021-01-31 11:50:00|
        |4|Click a product|2021-01-31 12:00:00|
        |5|Browse a page|2021-01-31 11:50:00|
        |5|Click a product|2021-01-31 12:00:00|
        |5|Add to the shopping cart|2021-01-31 11:10:00|
        |6|Browse a page|2021-01-31 11:50:00|
        |6|Click a product|2021-01-31 12:00:00|
        |6|Add to the shopping cart|2021-01-31 12:10:00|

        ```
        INSERT INTO funnel_test VALUES
        (1,'Browse a page','2021-01-31 11:00:00'),
        (1,'Click a product','2021-01-31 11:10:00'),
        (1,'Add to the shopping cart','2021-01-31 11:20:00'),
        (1,'Complete the payment','2021-01-31 11:30:00'),
        (2,'Add to the shopping cart','2021-01-31 11:00:00'),
        (2,'Complete the payment','2021-01-31 11:10:00'),
        (1,'Browse a page','2021-01-31 11:00:00'),
        (3,'Browse a page','2021-01-31 11:20:00'),
        (3,'Click a product','2021-01-31 12:00:00'),
        (4,'Browse a page','2021-01-31 11:50:00'),
        (4,'Click a product','2021-01-31 12:00:00'),
        (5,'Browse a page','2021-01-31 11:50:00'),
        (5,'Click a product','2021-01-31 12:00:00'),
        (5,'Add to the shopping cart','2021-01-31 11:10:00'),
        (6,'Browse a page','2021-01-31 11:50:00'),
        (6,'Click a product','2021-01-31 12:00:00'),
        (6,'Add to the shopping cart','2021-01-31 12:10:00');
        ```

        -   Assume that the length of the time window is 30 minutes, which equals 1,800 seconds. The events in the query condition are in the following order: browse a page, click a product, add to the shopping cart, and complete the payment. Use the following SQL statement to query the events of users that match the order in the query condition:

            ```
            SELECT
                user_id,
              windowFunnel(
                1800
                ,'default'
                ,event_time
                ,event_type = 'Browse a page'
                ,event_type = 'Click a product'
                ,event_type = 'Add to the shopping cart'
                ,event_type = 'Complete the payment'
              ) AS level
            FROM funnel_test
            GROUP BY user_id
            ORDER BY user_id ASC
            ;
            ```

            The following results are returned:

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

        -   To analyze the data from 11:00:00 to 12:00:00 on January 31, 2021, execute the following SQL statement:

            ```
            SELECT
                user_id,
              windowFunnel(
                1800
                ,'default'
                ,event_time
                ,event_type = 'Browse a page'
                ,event_type = 'Click a product'
                ,event_type = 'Add to the shopping cart'
                ,event_type = 'Complete the payment'
              ) AS level
            FROM funnel_test
            WHERE (event_time >= '2021-01-31 11:00:00') 
                AND (event_time <= '2021-01-31 12:00:00')
            GROUP BY user_id
            ORDER BY user_id ASC
            ;
            ```

            The following results are returned:

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

        -   Generally, the results of the windowFunnel function need to be sorted by level. Use the following SQL statement:

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
                  event_type = 'Browse a page',
                  event_type = 'Click a product',
                  event_type = 'Add to the shopping cart',
                  event_type = 'Complete the payment'
                  ) AS level
                FROM    funnel_test
                GROUP BY user_id
              ) AS basic_table
            GROUP BY level
            ORDER BY level ASC
            ;
            ```

            The following results are returned:

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

        -   Generally, the results of funnel analysis need to be cumulative data. Use the following SQL statement:

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
                      event_type = 'Browse a page', 
                      event_type = 'Click a product', 
                      event_type = 'Add to the shopping cart', 
                      event_type = 'Complete the payment' 
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

            The following results are returned:

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

    -   You can use a business intelligence \(BI\) tool to generate a funnel chart based on the return results.

        ![Funnel chart](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0688394161/p229480.png)


## Retention function

-   Function description

    This function takes a set of conditions as parameters to determine whether an event meets the specific conditions. The parameters are 1 to 32 values of the UINT8 data type.

-   Syntax

    ```
    retention(cond1, cond2, ..., cond32);
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |cond|A conditional expression that returns a result. Valid values:    -   1: The condition is met.
    -   0: The condition is not met. |

-   Examples
    -   Create a table. In this example, the table contains the data of user logon to an application. Use the following SQL statement:

        ```
        CREATE TABLE IF NOT EXISTS login_log
        (
            user_id INT ,
            log_time TIMESTAMP 
        );
        ```

    -   Import data. The following table and sample statement show the details.

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

    -   -   To obtain information about user churn from June 17 to June 21, 2019, execute the following SQL statement:

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

-   In the following return results, each value of the array r indicates the logon records of a user. The element 1 indicates that the user logged on that day. The element 0 indicates that the user did not log on that day. However, User 4101 logged on in four days from June 17 to June 21, 2019, but the elements of the corresponding array are all 0. This is because in the function retention\(cond1, cond2, …\), if the condition in the first expression cond1 is not met, the results of the other expressions are all 0. User 4101 did not log on on June 21, 2019. The first condition is not met, so the results of the other expressions are also 0.

    ```
     user_id |      r      
    ---------+-------------
        1101 | {1,1,1,0,1}
        2201 | {1,1,0,0,0}
        3301 | {1,0,1,1,1}
        4101 | {0,0,0,0,0}
    (4 rows)
    ```

-   To avoid the preceding invalid results, you need only to set cond1 to true.

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

    The following results are returned:

    ```
     user_id |       r       
    ---------+---------------
        1101 | {1,1,1,1,0,1}
        2201 | {1,1,1,0,0,0}
        3301 | {1,1,0,1,1,1}
        4101 | {1,0,1,1,0,1}
    (4 rows)
    ```

    -   You can use the retention function to calculate user retention rates. For example, you can calculate the second-day user retention rate, third-day user retention rate, and seventh-day user retention rate. Use the following sample statements:

        ```
        -- Use SUM(r[index]) to obtain user logon records on the second, third, and seventh days, starting from June 15, 2019, and then calculate user retention rates.
        SELECT
            DATE(TIMESTAMP '2019-06-15 00:00:00') AS first_date,
            CAST(SUM(r[1]) AS NUMERIC) AS "Active users on the first day",
            CAST(SUM(r[2]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "Second-day retention rate",
            CAST(SUM(r[3]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "Third-day retention rate",
            CAST(SUM(r[4]) AS NUMERIC)/CAST(SUM(r[1]) AS NUMERIC) AS "Seventh-day retention rate"
        FROM
        -- Use the retention function to obtain arrays that show the second-day, third-day, and seventh-day logon records of users who logged on on June 15, 2019. The element 1 indicates that a user logged on. The element 0 indicates that a user did not log on.
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
        -- Query the logon records of users who logged on on June 15, 2019 from June 15 to June 21, 2019.
                    FROM    login_log
                    WHERE   (log_time >= TIMESTAMP '2019-06-15 00:00:00')
                    AND     (log_time <= TIMESTAMP '2019-06-15 00:00:00' + INTERVAL '6 day')
                    GROUP BY user_id
                ) AS basic_table
        GROUP BY first_date
        ;
        ```


