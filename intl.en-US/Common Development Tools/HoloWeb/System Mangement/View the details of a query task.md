---
keyword: [view the details of a query task, HoloWeb]
---

# View the details of a query task

This topic describes how to view the details of a query task in the HoloWeb console.

-   An Alibaba Cloud account is created.
-   The real-name verification is complete.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

1.  In the HoloWeb console, click **System Management** in the top navigation bar.

2.  In the left-side navigation pane, choose **Performance Optimization** \> **Active Query**.

3.  On the **Active Query** page, set the parameters as required.

    ![Active Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8377993261/p275086.png)

    |Parameter|Description|
    |---------|-----------|
    |Instance Name|The name of the connected instance.|
    |Database|The name of the database where the query task resides.|
    |Status|The status of the query task in the database.     -   **idle**: indicates that the query task is waiting for new commands from the client.
    -   **active**: indicates that the query task is running.
    -   **idle in transaction**: indicates that the query task is in a transaction but the task is not running.
    -   **idle in transaction \(aborted\)**: indicates that the query task is in a transaction but the task is not running because the specified SQL statement is invalid.
    -   **fastpath function call**: indicates that the query task is executing a `fast-path` function call.
    -   **disabled**: indicates that the feature of tracking active SQL statements is disabled for the query task. |

4.  Click **Query**.

    In this example, you can set the Status parameter to **idle** to view the query tasks that are in the idle state, as shown in the following figure.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9969182061/p141659.png)

    To view the details of a specific query task, click **Details** in the **Operation** column of the task.

    ![View Details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9969182061/p141697.png)

    In the **Details** dialog box, you can click **Copy** to copy the SQL statement that is specified for the query task.


