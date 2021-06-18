---
keyword: [HoloWeb, SQL query, SQL query task]
---

# Manage an SQL query task

This topic describes how to create, edit, delete, copy, and rename an SQL query task in the HoloWeb console.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create an SQL query task

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the HoloWeb console, choose **SQL Editor** \> **Ad-hoc Query** in the top navigation bar.

    You can also right-click **My SQL query** in the left-side navigation pane and select **New SQL query**.

    In the **New SQL query** dialog box, set the parameters as required. The system automatically specifies the instance name and database name.

    ![New SQL query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1518993261/p275010.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the SQL query task to be created.|
    |Directory|The directory where the SQL query task is to reside. The default directory is **/My SQL query**. You can also specify a folder that you create.|
    |Instance Name|The name of the Hologres instance where the SQL query task is to run. You can select an existing instance from the drop-down list. **Note:** Both connected and disconnected instances are displayed in the drop-down list. To view an SQL query task created for a disconnected instance, you must connect to the instance after you execute and save the SQL statements. |
    |Database Name|The name of the database in the Hologres instance where the SQL query task is to run.|

5.  The new SQL query task is displayed in the left-side **My SQL query** list. You can click a table in the **Table Directory** section to view the names and data types of the fields in the table. You can also use standard PostgreSQL statements to analyze data in the SQL editor.

    **Note:** The names of tables and fields in SQL statements are not case-sensitive. To reference a table with an exactly matched name, you must enclose the name of the table in double quotation marks \("\) in SQL statements.

    ![Ad-hoc Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1518993261/p275030.png)

6.  Click **Run** to execute the SQL statements that you enter.

7.  Click **Save** to save the SQL statements in the SQL editor.


## View a table

After you create and edit an SQL query task, you can view a table in the Table Directory section to have a better understanding of its schema.

1.  In the HoloWeb console, choose **SQL Editor** \> **Ad-hoc Query** in the top navigation bar.

    You can also double-click an SQL query task in **My SQL query** list.

    ![Ad-hoc Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1518993261/p275030.png)

2.  You can view all internal tables in the **Table Directory** section. Click a table to view the names and data types of the fields in the table.

    You can also use standard PostgreSQL statements to analyze data in the SQL editor. For example, after you execute the CREATE TABLE statement in the SQL editor and click the ![Refresh icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610148951/p117260.png) icon, you can view the new table in the Table Directory section. You can click the table to view its schema.

    **Note:** The names of tables and fields in SQL statements are not case-sensitive. To reference a table with an exactly matched name, you must enclose the name of the table in double quotation marks \("\) in SQL statements.


## Edit an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the SQL query task that you want to manage.

    If the SQL query task is in a folder, enter the name of the SQL query task in the search box below **Query**.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7267993261/p274941.png)

2.  Double-click the SQL query task. Then, edit the SQL query task.

3.  You can enter the SQL statement that you want to execute in the SQL editor and click **Run**.

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

    You can view the execution result of the sample statement by checking the table schema in the [Table Directory](#step_3rq_2i2_7zf) section.

4.  Click **Save**. After you click Save, the entered SQL statement is saved for the SQL query task.


## Delete an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the SQL query task that you want to manage.

    If the SQL query task is in a folder, enter the name of the SQL query task in the search box below **Query**.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7267993261/p274941.png)

2.  Right-click the SQL query task and select **Delete SQL query**.

3.  Click **OK**.


## Copy an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the SQL query task that you want to manage.

    If the SQL query task is in a folder, enter the name of the SQL query task in the search box below **Query**.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7267993261/p274941.png)

2.  Right-click the SQL query task and select **Copy SQL query**.

    After you perform this operation, the SQL statement in the selected SQL query task is copied to the clipboard.

3.  Then, paste the copied statement to the SQL editor of the destination SQL query task.


## Rename an SQL query task

1.  In the left-side navigation pane, click **My SQL query**. Then, find the SQL query task that you want to manage.

    If the SQL query task is in a folder, enter the name of the SQL query task in the search box below **Query**.

    ![Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7267993261/p274941.png)

2.  Right-click the SQL query task and select **Rename**.

3.  Enter an updated name in the **Rename name** field as required.

4.  Click **OK**.


