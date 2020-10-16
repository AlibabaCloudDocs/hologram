---
keyword: [HoloWeb, schema]
---

# Manage a schema

This topic describes how to use HoloWeb to create, edit, and delete a schema in Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/HoloWeb/Connection Management/Manage a connection.md).

## Create a schema

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  On the **Connection Management** page of the HoloWeb console, click the **Mode** tab. The New mode dialog box appears.

    You can also open the New mode dialog box by performing the following steps: In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed. Click the relevant connection and then click Database. Right-click the database for which you want to create a schema and select New mode, or click the database, right-click **Schema**, and then select **New mode**.

5.  In the New mode dialog box, set the **Connection name**, **Database name**, and **Pattern name** parameters, and click **OK**.

    ![New mode](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5699438951/p132147.png)


## Edit a schema

1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Click the database for which you want to edit a schema and then click **Schema**. All the available schemas are displayed.

    **Note:** After a database is created, a schema named **public** is automatically created.

4.  Right-click the schema that you want to edit and select **Edit mode**.

5.  In the Edit mode dialog box, change the value of the **Pattern name** parameter based on your business requirements.

    ![Edit mode](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5699438951/p132174.png)

    **Note:** When you modify the settings of a schema in the **Edit mode** dialog box, you can only change the value of the **Pattern name** parameter.


## Delete a schema

1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Click the database from which you want to delete a schema and then click **Schema**. All the available schemas are displayed.

4.  Right-click the schema that you want to delete and select **Delete mode**.


