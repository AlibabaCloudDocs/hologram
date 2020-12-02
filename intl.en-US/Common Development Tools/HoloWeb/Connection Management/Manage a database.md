---
keyword: [HoloWeb, database]
---

# Manage a database

This topic describes how to use HoloWeb to create, edit, and delete a database in Hologres.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

## Create a database

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  On the **Connection Management** page of the HoloWeb console, click the **Database** tab. The New database dialog box appears.

    You can also open the New database dialog box by performing the following steps: In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed. Right-click the connection based on which you want to create a database and select **New database**.

5.  In the New database dialog box, set the **Connection name**, **Database name**, and **Permissions policy** parameters, and click **OK**.

    ![DB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0999438951/p118018.png)

    **Note:** The specified database name must be unique.


## Edit a database

1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

2.  Click the connection for which you want to edit a database and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Right-click the database that you want to edit and select **Edit database**.

4.  Change the value of the **Permissions policy** parameter from **Simple** to **Expert** based on your business requirements.

    ![Edit database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1999438951/p132033.png)

    **Note:** When you modify the settings of a database in the **Edit database** dialog box, you can only change the value of **Permissions policy** from Simple to Expert.


## Delete a database

1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

2.  Click the connection from which you want to delete a database and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Right-click the database that you want to delete and select **Delete database**.


