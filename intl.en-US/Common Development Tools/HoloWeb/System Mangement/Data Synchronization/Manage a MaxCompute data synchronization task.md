---
keyword: [HoloWeb, synchronize MaxCompute data, view the information about a MaxCompute data synchronization task]
---

# Manage a MaxCompute data synchronization task

HoloWeb allows you to synchronize MaxCompute data to Hologres with a few clicks in a visualized manner. This helps you query data in MaxCompute tables. This topic describes how to use HoloWeb to create a MaxCompute data synchronization task and view the status information about the task in the HoloWeb console.

-   An Alibaba Cloud account is created.
-   The real-name verification is complete.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create a MaxCompute data synchronization task

Create a MaxCompute data synchronization task. For more information, see [Synchronize MaxCompute data with a few clicks](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Synchronize MaxCompute data with a few clicks.md).

## View the information about the MaxCompute data synchronization task

1.  In the HoloWeb console, click **System Management** in the top navigation bar.

2.  In the left-side navigation pane, choose **Data Import** \> **Import MaxCompute Data**.

3.  On the **Import MaxCompute Data** page, set the parameters as required and click **Query**.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4377993261/p275132.png)

    If you do not set the **Instance Name**, **Database**, and **Status** parameters, all MaxCompute data synchronization tasks that are created within the current account are displayed.

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Instance Name|The name of the connected instance.|
    |Database|The name of the database where the MaxCompute data synchronization task resides.|
    |Status|The status of the MaxCompute data synchronization task. Valid values:    -   **Running**: indicates that the synchronization task is running.
    -   **Successful**: indicates that the synchronization task succeeds.
    -   **Failed**: indicates that the synchronization task failed.
    -   **Interrupted**: indicates that the synchronization task is interrupted. |

    You can perform the following operations on the tasks:

    -   View detailed information about a synchronization task.

        Find the required synchronization task and click **Details** in the **Operation** column to go to the **MaxCompute data synchronization details** page. All detailed information about the task is displayed.

    -   Rerun a synchronization task.

        Find the required synchronization task and click **Rerun** in the **Operation** column to rerun the task.

        If you want to interrupt the task that is rerun, click **Stop** in the **Operation** column.

    -   Delete a synchronization task.

        Find the required synchronization task and click **Delete** in the **Operation** column to delete the task.

    -   View the execution history of a synchronization task.

        Find the required synchronization task and click **Execution history** in the **Operation** column to view the execution information about each SQL statement in the task. For example, you can view the execution result and execution duration of an SQL statement, as shown in the following figure.

        ![Execution history](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7087004261/p186943.png)

        You can directly click an SQL statement to copy it.


