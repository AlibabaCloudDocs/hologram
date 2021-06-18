---
keyword: [HoloWeb, schema, manage a schema]
---

# Manage a schema

This topic describes how to use HoloWeb to create, edit, and delete a schema in Hologres.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create a schema

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the HoloWeb console, find the left-side **Instances Connected** list on the **Metadata Management** tab. Click the instance that you want to manage. Log on to the database that you want to manage, right-click the database, and then select **Create Schema**.

    ![Manage a schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5429393261/p273740.png)

5.  In the New Schema dialog box, set the **Schema name** parameter and click **OK**. By default, the names of the current instance and database are entered in the **Instance Name** and **Database Name** fields. You do not need to modify the settings.

    ![Create a schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5429393261/p273741.png)

    The created schema is displayed under the current database in the left-side Instances Connected list.


## Manage a schema

To manage a schema, you can refresh information, edit or delete the schema, create an internal table, create a foreign table, or create multiple foreign tables at a time.

1.  Go to the HoloWeb console. On the **Metadata Management** tab, find the left-side **Instances Connected** list. All the connected instances are displayed.

2.  Click the instance that you want to manage and click the database that you want to manage. All the created schemas are displayed.

    **Note:** After you log on to a database, a schema named **public** is automatically created.

3.  Right-click the schema that you want to manage and manage the schema based on your business requirements.

    ![Manage a schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5429393261/p273747.png)

    You can perform the following operations:

    -   **Refresh information**

        After you edit the schema, refresh the information about the schema.

    -   **Edit a schema**

        Right-click the schema and select **Edit Schema**. You can modify only the **Schema name** parameter.

    -   **Create an internal table, create a foreign table, or create multiple foreign tables at a time**

        Right-click the schema and select the type of table that you want to create. On the tab that appears, set the parameters as required. For more information about how to configure each type of table, see the topics of [MaxCompute acceleration](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Manage a foreign table.md).

    -   **Delete a schema**

        Right-click the schema and select **Delete Schema**. In the Delete mode message, click **OK**.


