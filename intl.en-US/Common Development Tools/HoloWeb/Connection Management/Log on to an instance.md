---
keyword: [HoloWeb, connect to a Hologres instance, manage a Hologres instance, log on to a Hologres instance]
---

# Log on to an instance

HoloWeb is a development platform that is used to process data in Hologres instances. This topic describes how to use HoloWeb to log on to a Hologres instance.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Log on to an instance

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Log on to Hologres Database** to go to the HoloWeb console.

4.  Log on to a Hologres instance in one of the following ways:

    -   **Use the Connect to Instance dialog box**

        If you have not logged on to a Hologres instance, the Connect to Instance dialog box appears after you go to the HoloWeb console. To log on to an instance, set the parameters as required in the dialog box and click **OK**.

        ![Connect to Instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273388.png)

        |Parameter|Description|
        |---------|-----------|
        |Instance Name|Select a disconnected instance from the drop-down list. **Note:**

        -   The left-side Instances Disconnected list displays all the disconnected instances on which you have access permissions in the current region within your Alibaba Cloud account.
        -   After you purchase a Hologres instance, a disconnected instance that has a ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon is added to the left-side Instances Disconnected list. The icon indicates that the network type of the instance is VPC. This instance cannot be edited or removed. |
        |Select Database|Valid values:        -   Existing Database: Select the database to which you want to connect from the Database Name drop-down list.
        -   Create Database: Enter a database name as required and select a permission model for the database. |
        |Database Name|        -   If you set the Select Database parameter to Existing Database, select a database from the drop-down list.
        -   If you set the Select Database parameter to Create Database, enter a database name as required. |
        |Permission Model|This parameter is displayed only when you set the Select Database parameter to Create Database. Select a permission model for the database based on your business requirements. For more information about permission models, see:        -   [SPM](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md)
        -   [SLPM](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md)
        -   [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md) |

    -   **Log on to a disconnected instance**

        In the Instances Disconnected list, right-click a disconnected instance and select **Connect to Instance**. In the Connect to Instance dialog box, set the parameters as required and click **OK**. For more information about the parameters, see [the parameter description](#table_rb8_wh8_1xs).

        ![Connect to Instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273414.png)

    -   **Add and log on to an instance**

        On the Metadata Management tab, click **Add Instance** and add a Hologres instance that resides in other regions. When you set the parameters in the Add Instance dialog box, set the Create Instance and Log On parameter to **Yes**. For more information about how to add a Hologres instance, see [Create a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

5.  The connected instance is displayed in the **Instances Connected** list. To manage the instance, right-click the instance and select an action. For more information about instance management, see [t2079639.md\#]().


