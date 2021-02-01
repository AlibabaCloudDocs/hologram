---
keyword: [HoloWeb, execution history of SQL statements]
---

# View the execution history of SQL statements in the HoloWeb console

HoloWeb allows you to view the execution history of SQL statements that are executed under the current account in the HoloWeb console. This way, you can view the SQL statements that are executed for specified tasks and relevant information such as the execution status and execution duration of these statements. You can also determine whether the SQL statements are appropriate based on the information. This helps you optimize inappropriate statements. This topic describes how to query the execution history of SQL statements in the HoloWeb console.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Usage notes

You can query the execution history of only the SQL statements that are executed under the current account in the HoloWeb console, but not those that are executed under other accounts or by using other tools.

## View the execution history of SQL statements

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  On the HoloWeb page, click **System Management** in the top navigation bar.

3.  In the left-side navigation pane, click **Execution History**.

4.  On the **Execution History** page, set the **Connection name** and **Database** parameters.

5.  Click **Refresh**. All the SQL statements that are executed in the required database and relevant information such as the execution status and execution duration are displayed.

    The following table describes the parameters that indicate the queried information.

    |Parameter|Description|
    |---------|-----------|
    |Start time|The time when the SQL statement is executed.|
    |Connection name|The name of the connection that is used to connect to the Hologres instance.|
    |Database|The name of the Hologres database.|
    |SQL|The details of the SQL statement.|
    |Status|Valid values:    -   Succeeded
    -   Failed You can click the ![ERROR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8096412161/p187715.png) icon to view the cause of the failure. |
    |Number of rows|The number of rows of query results that are returned after the SQL statement is executed.By default, HoloWeb adds the `Limit 200` clause to the end of SQL statements. If you want an SQL statement to return more than 200 rows of query results, add the `Limit N` clause to the end of the SQL statement and set the N variable to a value that is greater than 200. |
    |Duration|The execution duration of the SQL statement.If the response time of the SQL statement has a latency, you can use the metrics of Hologres to analyze the cause and optimize the SQL statement. For more information, see [Metrics of Hologres](/intl.en-US/Monitoring and alerting/Metrics of Hologres.md) and [Optimize SQL statements](/intl.en-US/Best Practices/Performance optimization/Optimize the performance of querying internal tables.md). |

    If you do not set the **Connection name** and **Database** parameters, the execution history of all the SQL statements that are executed under the current account in the HoloWeb console is displayed.

    You can also enter an SQL statement that you want to query in the SQL search box and click the ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8096412161/p186998.png) icon to query the execution history of the specified statement.

    For example, you can query all `SELECT` statements that are executed in the database that you specify, as shown in the following figure.

    **Note:** You can use `%` in the SQL statement to replace the table name.


