---
keyword: [Hologres Instances page, Hologres console, create a Hologres instance, modify instance configurations]
---

# Instances

In the Hologres console, the Hologres Instances page lists all the instances that are purchased by using your Alibaba Cloud account. This topic describes the features on the Hologres Instances page and the related operations that you can perform.

## Features

On the **Hologres Instances** page, you can view all the Hologres instances that are purchased by using your Alibaba Cloud account and the status of the instances. You can also create instances or modify the configurations of the instances. You can click the name of an instance to go to the details page of the instance. On the details page, you can perform more specific operations on the objects in the instance, such as databases and users.

The **Hologres Instances** page provides the following features:

-   **Create an instance**

    You can click **Create Instance** in the upper part of the **Hologres Instances** page to go to the buy page of Hologres. On the buy page, you can purchase a Hologres instance. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

-   **View instance details**

    You can click the name of a Hologres instance in the instance list to go to the details page of the instance. On the details page, you can view the instance configurations, check the monitoring and alerting data, and manage users and databases. For more information, see [Instance configurations](/intl.en-US/Manage Instances/Hologres Console/Instance configurations.md).

-   **Go to the HoloWeb console**

    HoloWeb is an all-in-one big data development platform that is developed based on Hologres.

    HoloWeb allows you to perform data analytics operations in a visualized manner or by executing SQL statements. You can click **Go to HoloWeb** in the upper part of the Hologres Instances page to go to the HoloWeb console. For more information about HoloWeb, see [t2079397.md\#]().

-   **Search for instances by name**

    If you have purchased multiple instances in the current region, you can enter a keyword of an instance name in the search box to search for the required instance.

-   **View the status of an instance**

    The **Status** column displays the status of your Hologres instances. A Hologres instance must be in one of the following states:

    -   **Running**: The instance is running as expected.
    -   **Creating**: The payment is successful, and Hologres is creating the instance. You must wait for 3 to 5 minutes.
    -   **Stopped**: The instance stops running, and you cannot connect to it.
-   **View the billing method of an instance**

    The **Billing Method** column displays the billing methods of your Hologres instances. For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

-   **Perform operations on an instance**

    The **Actions** column displays the following operations that you can perform on a Hologres instance:

    -   **Manage**

        You can click **Manage** to go to the details page of the instance. On the details page, you can view and perform more specific operations on the objects in the instance, such as databases and users.

    -   **Rename**

        You can click **Rename** to rename the instance. After you click Rename, the Rename dialog box appears. Enter a new instance name and click **OK**.

    -   **Switch to Subscription**

        You can click **Switch to Subscription** to change the billing method of the instance from **Pay-as-you-go** to **Subscription**.

        **Note:** You cannot change the billing method of a Hologres instance from **Subscription** to **Pay-as-you-go**. For more information, see [Change the billing method](/intl.en-US/Pricing/Change the billing method.md).

    -   **Upgrade**

        If the instance cannot meet your business requirements, you can click **Upgrade** to scale up the instance. You can upgrade Hologres instances only by using your Alibaba Cloud account or as an authorized RAM user.

        **Note:** When an instance is being upgraded, the instance stops working for a few minutes. We recommend that you upgrade instances during off-peak hours.

    -   **Downgrade**

        If the instance contains a large number of idle resources, you can click **Downgrade** to scale down the instance. You can downgrade Hologres instances only by using your Alibaba Cloud account or as an authorized RAM user.

        **Note:** When a Hologres instance is being downgraded, the instance stops working for a few minutes. We recommend that you downgrade instances during off-peak hours.

    -   **Renew**

        If the billing method of the instance is **Subscription**, you can click **Renew** to extend the validity period of the instance.

    -   **Stop**

        If the billing method of the instance is **Pay-as-you-go**, you can click **Stop** to stop the instance. After you click **Stop**, the system no longer charges fees for the computing resources but still charges fees for the storage resources.

    -   **Delete**

        If you no longer need the instance, you can click **Delete** to delete it.


