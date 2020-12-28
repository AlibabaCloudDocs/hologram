---
keyword: [view the details of a query task, HoloWeb]
---

# View the details of a query task

This topic describes how to use HoloWeb to view the details of a query task.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  On the HoloWeb page, click **Tools** in the top navigation bar.

3.  In the left-navigation pane, choose **Performance Optimization** \> **Active Query**.

4.  On the **Active Query** tab, set the parameters as required.

    ![Active Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9969182061/p141399.png)

    |Parameter|Description|
    |---------|-----------|
    |Connection name|The name of the required connection.|
    |Database|The name of the database that is created in the selected connection.|
    |Status|The status of the query task in the database.    -   **idle**: indicates that the query task is waiting for new commands from the client.
    -   **active**: indicates that the query task is running.
    -   **idle in transaction**: indicates that the query task is in a transaction but the task is not running.
    -   **idle in transaction \(aborted\)**: indicates that the query task is in a transaction but the task is not running because the specified SQL statement is invalid.
    -   **fastpath function call**: indicates that the query task is executing a `fast-path` function call.
    -   **disabled**: indicates that the **track\_activities** function is disabled. |

5.  Click **Query**.

    In this example, you can set the Status parameter to **idle** to view the query tasks that are in the idle state, as shown in the following figure.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9969182061/p141659.png)

    To view the details of a specific query task, click **Details** in the **Operation** column of the task.

    ![Details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9969182061/p141697.png)

    In the **Details** dialog box, you can click **Replication** to copy the SQL statement that is specified for the query task.


