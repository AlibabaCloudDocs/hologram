---
keyword: [HoloWeb, execution history of SQL statements]
---

# View the execution history of SQL statements in the HoloWeb console

HoloWeb allows you to view the execution history of SQL statements that are executed within the current account in the HoloWeb console. This way, you can view the SQL statements that are executed for specified tasks and relevant information such as the status and execution duration of these statements. You can also determine whether the SQL statements are appropriate based on the information. This helps you optimize inappropriate statements. This topic describes how to query the execution history of SQL statements in the HoloWeb console.

-   An Alibaba Cloud account is created.
-   The real-name verification is complete.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).


## Usage notes

You can query the execution history of only the SQL statements that are executed within the current account in the HoloWeb console, but not those that are executed within other accounts or by using other tools.

## View the execution history of SQL statements

1.  In the HoloWeb console, click **System Management** in the top navigation bar.

2.  In the left-side navigation pane, click **Execution History**.

3.  On the **Execution History** page, set the **Instance Name** and **Database** parameters.

4.  Click **Refresh**. All the SQL statements that are executed in the specified database and relevant information such as the status and execution duration are displayed.

    ![Execution History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2377993261/p275136.png)

    The following table describes the parameters that indicate the queried information.

    |Parameter|Description|
    |---------|-----------|
    |Started At|The time when the SQL statement is executed.|
    |Instance Name|The name of the connected instance.|
    |Database|The name of the database where the SQL statement resides.|
    |SQL Statements \(Click to Copy\)|The content of the SQL statement.|
    |Status|The status of the SQL statement. Valid values:    -   Successful
    -   Failed You can click the ![Info icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8096412161/p187715.png) icon to view the cause of the failure. |
    |SQL Lines|The number of rows of query results that are returned after the SQL statement is executed. By default, HoloWeb includes the `Limit 200` clause in SQL statements. If you want an SQL statement to return more than 200 rows of query results, manually add the `Limit N` clause to the SQL statement and set the N variable to a value that is greater than 200. |
    |Elapsed Time \(ms\)|The execution duration of the SQL statement. If the response time of the SQL statement has a latency, you can use the metrics of Hologres to analyze the cause and optimize the SQL statement. For more information, see [Metrics of Hologres](/intl.en-US/Monitoring And Alerting/Metrics of Hologres.md) and [Optimize SQL statements](/intl.en-US/Best Practices/Performance optimization/Optimize the performance of querying internal tables.md). |

    If you do not set the **Instance Name** and **Database** parameters, the execution history of all the SQL statements that are executed within the current account in the HoloWeb console is displayed.

    You can also enter an SQL statement that you want to query in the SQL search box and click the ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8096412161/p186998.png) icon to query the execution history of the specified statement.

    **Note:** You can use `%` in the SQL statement to represent all table names.


