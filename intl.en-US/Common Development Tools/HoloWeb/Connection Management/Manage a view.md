---
keyword: [HoloWeb, view]
---

# Manage a view

A view is a virtual table whose data is queried from one or more tables. A view has the same schema as a regular table, but does not store data. The content of a view is defined by a query. You can create a view based on a single table or multiple tables. A single-table view is used to query and modify data. The data of the source table may change. A multi-table view is used to query data. The data of the source tables does not change. This topic describes how to create, rename, and delete a view.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create a view

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the HoloWeb console, click the **Metadata Management** tab. In the left-side **Instances Connected** list, click the instance, database, and schema that you want to manage in sequence. Right-click **Views** and select **Create View**.

5.  On the **Create View** tab, set the parameters as required.

    ![Create a view](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8165044261/p279037.png)

    |Parameter|Description|
    |---------|-----------|
    |Instance Name|The name of the current instance.|
    |Database|The name of the current database.|
    |View Name|The name of the Hologres view.|
    |Description|The description of the Hologres view.|
    |Schema|The name of the schema. You can select the default schema **public** or a custom schema. |
    |Query Statement|The SQL statements that are used to query data. Enter SQL statements in the SQL editor to query data based on a single table or multiple tables.     -   A single-table view is used to query and modify data. The data of the source table may change. If you create a view based on a single table and modify the data in the view, the data of the source table is updated accordingly. If you modify the data of the source table, the data in the view is also updated.
    -   A multi-table view is used to query data. The data of the source tables does not change. If you create a view based on multiple tables, you cannot modify the data in the view.
**Note:** When you use a single-table view, we recommend that you modify the data in the view with caution. This prevents the data of the source table from being modified and ensures that your business is not affected. |

6.  Click **Submit View** in the upper-right corner. The Run Log section in the lower part displays the execution result. The created view is displayed under the schema that you selected in the left-side navigation pane.

    After the view is created, you can view its query statements and DDL statements and preview the data on the configuration tab of the view.

    ![View the created view](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8165044261/p279049.png)


## Manage a view

1.  Go to the HoloWeb console. On the **Metadata Management** tab, find the left-side **Instances Connected** list. All the connected instances are displayed.

2.  In the left-side navigation pane, find the view that you want to manage. You can search for a view in the top search box or find a view in the Instances Connected list.

    ![Manage a view](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8165044261/p279051.png)

    You can perform the following operations:

    -   **Refresh information**

        After you modify the content of the view, right-click **Views** and select **Refresh** to refresh the information about the view.

    -   **Edit a view**

        Right-click the view and select **Edit View**. On the configuration tab of the view, edit the statements on the **Query Statement** tab and click **Submit View** in the upper-right corner.

    -   **Delete a view**

        Right-click the view and select **Delete View**. In the Delete View message, click **OK**.

    -   **Query a view**

        On the configuration tab of the view, click **Query View** in the upper-right corner. On the Ad-hoc Query tab, enter SQL statements in the SQL editor and click **Run**. For more information about how to query data, see [Manage an SQL query task](/intl.en-US/Common Development Tools/HoloWeb/SQL Editor/Manage an SQL query task.md).

        ![Query a view](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8165044261/p279050.png)


## Use SQL statements to manage a view

Hologres also allows you to create, view, and delete a view by using SQL statements. For more information, see [VIEW](/intl.en-US/Hologres SQL/DDL/VIEW/VIEW.md).

