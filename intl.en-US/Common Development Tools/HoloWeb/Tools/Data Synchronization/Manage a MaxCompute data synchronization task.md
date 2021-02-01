---
keyword: [HoloWeb, synchronize MaxCompute data, view information about a MaxCompute data synchronization task]
---

# Manage a MaxCompute data synchronization task

HoloWeb allows you to synchronize MaxCompute data with a few clicks in a visualized manner. This helps you query data in MaxCompute tables. This topic describes how to use HoloWeb to create a MaxCompute data synchronization task and view the status information about the task in the HoloWeb console.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create a MaxCompute data synchronization task

Create a MaxCompute data synchronization task. For more information, see [Synchronize MaxCompute data with a few clicks](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Synchronize MaxCompute data with a few clicks.md).

## View the information about the MaxCompute data synchronization task

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  On the HoloWeb page, click **System Management** in the top navigation bar.

3.  In the left-side navigation pane, choose **Data Synchronization** \> **Synchronization MaxCompute data**.

4.  On the **Synchronization MaxCompute data** page, set the parameters as required and click **Query**.

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Connection name|The name of the required connection.|
    |Database|The name of the Hologres database.|
    |Status|Valid values:    -   **Running**: indicates that the synchronization task is running.
    -   **Succeeded**: indicates that the synchronization task has been executed.
    -   **Failed**: indicates that the synchronization task failed to be executed.
    -   **Interrupted**: indicates that the synchronization task is interrupted. |

    If you do not set the **Connection name**, **Database**, and **Status** parameters, all MaxCompute data synchronization tasks that are created under the current account are displayed.

    In this example, you can set the **Status** parameter to **Succeeded** to view the synchronization tasks that are in the Succeeded state, as shown in the following figure.You can perform the following operations on these tasks:

    -   View detailed information about a synchronization task.

        Find the required synchronization task and click **Details** in the **Operation** column to go to the **MaxCompute data synchronization details** page. All detailed information about the task is displayed, as shown in the following figure.

    -   Rerun a synchronization task.

        Find the required synchronization task and click **Rerun** in the **Operation** column to rerun the task.

        If you want to interrupt the task that is rerun, click **Stop** in the **Operation** column.

    -   Delete a synchronization task.

        Find the required synchronization task and click **Delete** in the **Operation** column to delete the task.

    -   View the execution history of a synchronization task.

        Find the required synchronization task and click **Execution history** in the **Operation** column to view the execution information about each SQL statement in the task. For example, you can view whether the SQL statement is successfully executed and the execution duration of the SQL statement, as shown in the following figure.You can directly click an SQL statement to copy it.


