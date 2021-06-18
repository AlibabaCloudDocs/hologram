---
keyword: [HoloWeb, connect to a Hologres instance, manage a Hologres instance]
---

# Manage instances

This topic describes how to use HoloWeb to manage the connected and disconnected Hologres instances. For example, you can edit or remove an instance.

The following table describes the operations that you can perform to manage the connected and disconnected Hologres instances.

|Instance type|Operation|
|-------------|---------|
|[Manage the disconnected instances](#section_pfx_iko_4au)|You can perform the following operations on the disconnected instances:-   Log on to an instance
-   Edit an instance
-   Remove an instance |
|[Manage the connected instances](#section_a7n_ozi_17i)|You can perform the following operations on the connected instances:-   Refresh information
-   Edit an instance
-   Manage users
-   Manage databases
-   Switch the connected database
-   Create a database
-   Log off from an instance
-   Remove an instance |

## Manage the disconnected instances

To manage a disconnected instance, you can log on to, edit, or remove the instance.

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  On the Metadata Management tab, right-click the disconnected instance that you want to manage.

    ![Connect to Instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273414.png)

    **Note:**

    -   The left-side Instances Disconnected list displays all the disconnected instances on which you have access permissions in the current region within your Alibaba Cloud account.
    -   After you purchase a Hologres instance, a disconnected instance that has a ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon is added to the left-side Instances Disconnected list. The icon indicates that the network type of the instance is VPC. This instance cannot be edited or removed.
    You can perform the following operations:

    -   **Log on to an instance**

        Right-click the instance and select **Connect to Instance**. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

    -   **Edit an instance**

        Right-click the instance and select **Edit Instance**. For more information about the parameters, see [Add an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Add an instance.md).

        **Note:**

        -   The ![Public network](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273449.png) icon indicates that the network type of the instance is public network. You can modify all the parameters for this instance.
        -   The ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon indicates that the network type of the instance is VPC. You can modify only the description for this instance.
    -   **Remove an instance**

        Right-click the instance and select **Delete Instance**. In the Delete Instance message, click **OK**.

        **Note:** You can remove only the instances that have a ![Public network](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273449.png) icon, which means their network type is public network. You cannot remove the instances that have a ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon, which means their network type is VPC.


## Manage the connected instances

To manage a connected instance, you can refresh information, edit or remove the instance, manage users and permissions, switch the connected database, or log off from the instance.

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  On the Metadata Management tab, right-click the connected instance that you want to manage.

    ![Manage the connected instances](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8180493261/p273467.png)

    **Note:**

    -   The left-side Instances Disconnected list displays all the disconnected instances on which you have access permissions in the current region within your Alibaba Cloud account.
    -   After you purchase a Hologres instance, a disconnected instance that has a ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon is added to the left-side Instances Disconnected list. The icon indicates that the network type of the instance is VPC. This instance cannot be edited or removed.
    You can perform the following operations:

    -   **Refresh information**

        After you edit the instance, refresh the information about the instance.

    -   **Edit an instance**

        Right-click the instance and select **Edit Instance**. For more information about the parameters, see [Add an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Add an instance.md).

        **Note:**

        -   The ![Public network](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273449.png) icon indicates that the network type of the instance is public network. You can modify all the parameters for this instance.
        -   The ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon indicates that the network type of the instance is VPC. You can modify only the description for this instance.
    -   **Manage users**

        Right-click the instance and select **User Management**. On the **User Management** page, you can add or remove users for the instance. For more information, see [t1877467.md\#]().

    -   **Manage databases**

        Right-click the instance and select **Database Authorization**. On the **Database Authorization** page, you can create a database and manage permissions. You can also manage permissions for an existing database. For more information, see [Manage databases](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/Manage databases.md).

    -   **Switch the connected database**

        Right-click the instance and select **Switch Database**. In the **Connect to Instance** dialog box, you can switch the connected database in the instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

    -   **Create a database**

        Right-click the instance and select **New Database**. In the **New database** dialog box, you can create a database in the instance. The created database is displayed under the current instance in the left-side Instances Connected list. For more information, see [Create a database](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a database.md).

    -   **Log off from an instance**

        Right-click the instance and select **Log Off**. In the Note message, click **OK**. The disconnected instance is displayed in the left-side Instances Disconnected list.

    -   **Remove an instance**

        Right-click the instance and select **Delete Instance**. In the Delete Instance message, click **OK**.

        **Note:** You can remove only the instances that have a ![Public network](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273449.png) icon, which means their network type is public network. You cannot remove the instances that have a ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon, which means their network type is VPC.


