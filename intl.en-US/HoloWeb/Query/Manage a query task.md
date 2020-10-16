---
keyword: [HoloWeb, SQL window, SQL query]
---

# Manage a query task

This topic describes how to use HoloWeb to create, edit, delete, copy, and rename a query task.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/HoloWeb/Connection Management/Manage a connection.md).

## Create a query task

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  In the top navigation bar, click **Query**. On the Query page, click the **SQL window** tab. The New SQL query dialog box appears.

    You can also open the New SQL query dialog box by performing the following steps: In the left-side navigation pane of the Query page, right-click **My SQL query** and select **New SQL query**.

5.  In the **New SQL query** dialog box, set the parameters as required.

    ![New SQL query](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7479438951/p132716.png)

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the query task to be created.|
    |Directory|The directory where the query task is to reside.|
    |Connection name|The name of the connection based on which you want to create the query task.|
    |Database name|The name of the database from which data is to be queried.|

    **Note:** You can place a query task in the **My SQL query** directory or in a created folder.

6.  Click **OK**.


## Edit a query task

1.  In the left-side navigation pane of the Query page, click **My SQL query**. Then, find the query task that you want to edit.

    You can click a folder to show all the query tasks that reside in the folder.

2.  Right-click the query task and select **Edit SQL query**.

3.  On the tab that appears, reset the **Connection name** and **Database** parameters as required. If you need to modify the SQL statement, enter the updated statement in the SQL editor and click the ![Run](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7479438951/p132774.png) icon.

    ![SQL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7479438951/p132784.png)

    For example, you can execute the following SQL statement to create a table from which data is to be queried:

    ```
    CREATE TABLE test (
     "id" bigint NOT NULL,
     "name" text NOT NULL,
     "age" bigint,
     "class" text NOT NULL,
    PRIMARY KEY (id)
    );
    ```

    To view the execution result of the preceding SQL statement, perform the following steps:

    1.  In the top navigation bar, click **Connection Management**.

    2.  On the **Connection Management** page, click the ![Refresh](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7479438951/p132799.png) icon in the left-side navigation pane.

    3.  In the left-side navigation pane, click **My connection**. Then, find the database from which data is to be queried.

    4.  Click the database and then choose Schema \> **public** \> **Table**. Double-click the created **test** table to view the data.

        ![New table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7479438951/p132804.png)

4.  Return to the query task editing tab and click **Save** in the upper-right corner.

    After you click **Save**, the entered SQL statement is saved for the query task.


## Delete a query task

1.  In the left-side navigation pane of the Query page, click **My SQL query**. Then, find the query task that you want to delete.

    You can click a folder to show all the query tasks that reside in the folder.

2.  Right-click the query task and select **Delete SQL query**.

3.  In the message that appears, click **OK**.


## Copy a query task

1.  In the left-side navigation pane of the Query page, click **My SQL query**. Then, find the query task that you want to copy.

    You can click a folder to show all the query tasks that reside in the folder.

2.  Right-click the query task and select **Copy SQL query**.

    After you perform this operation, the SQL statement in the selected query task is copied to the clipboard. Then, paste the copied statement to the SQL editor of the destination query task.

3.  On the editing tab of the query task, click **Save** in the upper-right corner.


## Rename a query task

1.  In the left-side navigation pane of the Query page, click **My SQL query**. Then, find the query task that you want to rename.

    You can click a folder to show all the query tasks that reside in the folder.

2.  Right-click the query task and select **Rename**.

3.  In the Rename SQL file dialog box, enter an updated name in the **Rename name** field.

4.  Click **OK**.


