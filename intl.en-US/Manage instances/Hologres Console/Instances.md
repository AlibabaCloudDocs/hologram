---
keyword: [Hologres Instances page, Hologres console, create a Hologres instance, modify instance configurations]
---

# Instances

In the Hologres console, the Hologres Instances page lists all the instances that are purchased by using your Alibaba Cloud account. This topic describes the features on the Hologres Instances page and related operations that you can perform.

## Features

On the **Hologres Instances** page, you can view all the Hologres instances that are purchased by using your Alibaba Cloud account and the status of the instances. You can also create instances or modify the configurations of the instances. You can click the name of an instance to go to the details page of the instance. On the details page, you can perform more specific operations on the objects in the instance, such as databases and users.

![660](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5041572061/p94150.png)

The **Hologres Instances** page provides the following features:

-   **Create Instance**

    You can click **Create Instance** in the upper part of the **Hologres Instances** page to go to the buy page of Hologres. On the buy page, you can purchase a Hologres instance. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

-   **前往DataWorks-HoloStudio开发**

    HoloStudio是基于Hologres引擎的一站式开发平台，深度集成于DataWorks。

    HoloStudio支持使用可视化和SQL方式进行数据开发。您可以单击**前往DataWorks-HoloStduio**，即可进入HoloStudio的开发界面。进入HoloStudio开发数据之前，您需要绑定实例至DataWorks工作空间，详情请参见[Bind a Hologres database to a workspace]()。

-   **Enter an instance name**

    If you have purchased multiple instances in the current region, you can enter a keyword in the search box to search for an instance as needed.

-   **Status**

    The **Status** column displays the status of your Hologres instances. A Hologres instance must be in one of the following states:

    -   **Running**: The instance is running as expected.
    -   **Creating**: The payment is successful, and Hologres is creating an instance. You must wait for 3 to 5 minutes.
    -   **Stopped**: The instance stops running, and you cannot connect to it.
-   **Billing Method**

    The **Billing Method** column displays the billing methods of your Hologres instances.当前Hologres实例分为**包年包月**和**按量付费**两种付费模式，For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

-   **Actions**

    The **Actions** column displays the following operations that you can perform on Hologres instances:

    -   **Manage**

        You can click **Manage** to go to the details page of the instance. On the details page, you can view and perform more specific operations on the objects in the instance, such as databases and users.

    -   **Rename**

        After you click **Rename**, the Rename dialog box appears. Enter a new instance name and click **OK**.

    -   **转包年包月****Switch to Subscription**

        You can click **转包年包月****Switch to Subscription** to change the billing method of the instance from **Pay-as-you-go****包年包月** to **Subscription**.

        **Note:** You cannot change the billing method of a Hologres instance from **Subscription****包年包月** to **Pay-as-you-go**. For more information, see [转换计费方式](/intl.en-US/Pricing/Billing methods.mdsection_ulv_gft_vg3).

    -   **Upgrade**

        If an instance cannot meet your business requirements, you can click **Upgrade** to scale up the instance. You can upgrade Hologres instances only by using your Alibaba Cloud account or as an authorized RAM user.

        **Note:** When an instance is being upgraded, the instance stops working for a few minutes. We recommend that you upgrade instances during off-peak hours.

    -   **Downgrade**

        If an instance contains a large number of idle resources, you can click **Downgrade** to scale down the instance. You can downgrade Hologres instances only by using your Alibaba Cloud account or as an authorized RAM user.

        **Note:** When a Hologres instance is being downgraded, the instance stops working for a few minutes. We recommend that you downgrade instances during off-peak hours.

    -   **Renew**

        If the billing method of an instance is **包年包月****Subscription**, you can click **Renew** to extend the validity period of the instance.

    -   **Stop**

        If the billing method of an instance is **Pay-as-you-go**, you can click **Stop** to stop the instance. After you click **Stop**, the system no longer charges fees for the computing resources but still charges fees for the storage resources.

    -   **Delete**

        If you no longer need an instance, you can click **Delete** to delete it.


