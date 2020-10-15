---
keyword: [Hologres Instances, Hologres console, create instances, modify instance configurations]
---

# Instances

In the Hologres console, the Hologres Instances page lists all instances purchased by using your Alibaba Cloud account. This topic describes the features on the Hologres Instances page and the related operations that you can perform.

## Features

On the **Hologres Instances** page, you can view all the Hologres instances purchased by using your Alibaba Cloud account and the status of the instances. You can also create instances or modify the configurations of the instances. You can click the name of an instance to go to the instance details page. On the page that appears, you can manage the instance objects such as databases and users in a refined manner.

![660](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5041572061/p94150.png)

The **Hologres Instances** page provides the following features:

-   **Create Instance**

    You can click the **Create Instance** button to go to the buy page of Hologres. On the page that appears, you can purchase instances. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

-   **Go to DataWorks - HoloStudio**

    HoloStudio is an end-to-end data development platform based on Hologres. It is deeply integrated into DataWorks.

    HoloStudio supports data development in a visualized manner and by using SQL statements. You can click **Go to DataWorks - HoloStudio** to use HoloStudio. Before you use HoloStudio, you must bind your Hologres instance to a DataWorks workspace. For more information, see [Bind a Hologres database to a workspace](/intl.en-US/HoloStudio/Bind a Hologres database to a workspace.md).

-   **Search for an instance**

    If you have purchased multiple instances in the current region, you can enter a keyword in the search box to search for your desired instance.

-   **Status**

    The **Status** column shows the running status of your Hologres instances. A Hologres instance has the following states:

    -   **Running**: The instance is running as expected.
    -   **Creating**: The payment is successful, and Hologres is creating an instance. You must wait for three to five minutes.
    -   **Stopped**: The instance stops running, and you cannot connect to it.
-   **Billing Method**

    The **Billing Method** column shows the billing methods of your Hologres instances.For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

-   **Actions**

    The **Actions** column shows the following operations that you can perform on the Hologres instances:

    -   **Manage**

        You can click **Manage** to go to the instance management page. On the instance management page, you can view and manage the instance objects such as databases and users in a refined manner.

    -   **Switch to Subscription**

        You can click **Switch to Subscription** to change the billing method from **Pay-as-you-go** to **Subscription**.

        **Note:** You cannot change the billing method of a Hologres instance from **Subscription** to **Pay-as-you-go**. For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

    -   **Upgrade**

        If your current instance cannot meet your business requirements, you can click **Upgrade** to scale up the instance. You can use your Alibaba Cloud account or a RAM user that has the required permissions to perform the Upgrade operation on Hologres instances.

        **Note:** During an upgrade, the Hologres instance stops working for a few minutes. We recommend that you perform the Upgrade operation during off-peak hours.

    -   **Downgrade**

        If your current instance has a large number of idle resources, you can click **Downgrade** to scale down the instance. You can use your Alibaba Cloud account or a RAM user that has the required permissions to perform the Downgrade operation on Hologres instances.

        **Note:** During a downgrade, the Hologres instance stops working for a few minutes. We recommend that you perform the Downgrade operation during off-peak hours.

    -   **Renew**

        If you have purchased a **Subscription** instance, you can click **Renew** to renew the instance.

    -   **Stop**

        If you have purchased a **Pay-as-you-go** instance, you can click **Stop** to stop using the instance. After you click **Stop**, the system no longer charges for your computing resources but still charges for your storage resources.

    -   **Delete**

        If you no longer require an instance, you can click **Delete** to delete it.


