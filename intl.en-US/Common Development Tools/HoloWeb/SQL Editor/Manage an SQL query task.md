---
keyword: [HoloWeb, SQL window, SQL query]
---

# Manage an SQL query task

This topic describes how to use HoloWeb to create, edit, delete, copy, and rename an SQL query task.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create an SQL query task

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  In the top navigation bar of the HoloWeb console, choose **SQL Editor** \> **Ad-hoc Query**.

    You can also right-click **My SQL query** in the left-side navigation pane and select **New SQL query**.

    In the **New SQL query** dialog box, set the parameters as required.

    ![New SQL query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7479438951/p132716.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the SQL query task to be created.|
    |Directory|The directory where the SQL query task is to reside.|
    |Connection name|The name of the configured connection.|
    |Database name|The name of the Hologres database.|

    **Note:** You can place an SQL query task in the **My SQL query** directory or in a created folder.

3.  On the **Ad-hoc query** tab, set the **Connection name** and **Database** parameters. Then, execute standard PostgreSQL statements in the SQL editor to analyze data.

    **Note:**

    -   The names of tables and fields in SQL statements are not case-sensitive. If you need to query a table with an exactly matched name, the name of this table must be enclosed in double quotation marks \("\) in the SQL statement.
    -   By default, you can run only one SQL query task at a time. If you run another one, the system automatically pauses the previous SQL query task.
4.  Click **Run** to execute the SQL statement that you enter.

5.  Click **Save** to save the SQL statement that you enter in the SQL editor.


## Edit an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the required SQL query task.

    You can click a folder to show all the SQL query tasks that reside in the folder.

2.  Right-click the SQL query task and select **Edit SQL query**.

3.  Reset the **Connection name** and **Database** parameters as required. If you need to modify the SQL statement, enter the updated statement in the SQL editor and click **Run**.

    ![SQL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7479438951/p132784.png)

    For example, you can execute the following SQL statement:

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

    2.  On the **Connection Management** page, click the ![Refresh](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7479438951/p132799.png) icon.

    3.  In the left-side navigation pane, click **My connection**. Then, find the database from which data is queried.

    4.  Click the required database and choose Schema \> **public** \> **Table**. Double-click the created **test** table to view the data.

        ![Create a table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7479438951/p132804.png)

4.  Click **Save**.

    After you click **Save**, the entered SQL statement is saved for the SQL query task.


## Delete an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the required SQL query task.

    You can click a folder to show all the SQL query tasks that reside in the folder.

2.  Right-click the SQL query task and select **Delete SQL query**.

3.  Click **OK**.


## Copy an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the required SQL query task.

    You can click a folder to show all the SQL query tasks that reside in the folder.

2.  Right-click the SQL query task and select **Copy SQL query**.

    After you perform this operation, the SQL statement in the selected SQL query task is copied to the clipboard.

3.  Then, paste the copied statement to the SQL editor of the destination SQL query task.


## Rename an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the required SQL query task.

    You can click a folder to show all the SQL query tasks that reside in the folder.

2.  Right-click the SQL query task and select **Rename**.

3.  Enter an updated name in the **Rename name** field as required.

4.  Click **OK**.


