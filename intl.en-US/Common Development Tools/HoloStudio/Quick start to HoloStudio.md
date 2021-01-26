# Quick start to HoloStudio

As an all-in-one analytics platform based on Hologres, DataWorks HoloStudio allows you to analyze data on a visualized user interface or by using SQL statements. Being deeply integrated with DataWorks, HoloStudio also allows you to create nodes for periodic scheduling.

This topic describes the procedure for using DataWorks HoloStudio.

![678](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5206966951/p94251.png)

**Note:**

1.  If you prefer to analyze data by using a PostgreSQL client, see [Get started with Hologres](/intl.en-US/Quick Start/Use Hologres from the PostgreSQL client.md).
2.  U can only use HoloStudio in Singapore.

## Prerequisites

A Hologres instance is purchased. For more information, see [Billing methods](/intl.en-US/Pricing/Billing methods.md).

## Procedure

To use DataWorks HoloStudio to analyze data, follow these steps:

1.  **Bind a Hologres database to a workspace.**

    You can bind a Hologres database to a workspace in both of the following scenarios:

    -   **No workspaces exist.**

        If you have not created any DataWorks workspaces, you must create a workspace that belongs to the region where the Hologres instance resides in the [DataWorks console](https://workbench.data.aliyun.com/?#/projectlist). You can bind a Hologres database to a workspace when you create the workspace. The method is as follows:

        Log on to the DataWorks console and click **Workspaces** in the left-side navigation pane. On the Workspaces page that appears, click **Create Workspace**. In the Create Workspace pane that appears, configure basic information about the workspace, select compute engines, and configure the engine details. You can select other compute engines in addition to Hologres based on your requirements. For more information, see [Bind a Hologres database to a workspace](/intl.en-US/Common Development Tools/HoloStudio/Bind a Hologres database to a workspace.md).

        **Note:** In a workspace in the standard mode, the production environment and development environment are isolated from each other. Currently, HoloStudio cannot switch between the two environments. As a result, HoloStudio cannot obtain data from the production environment. To resolve this issue, we recommend that you bind the same database to the development and production environments.

        ![844](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5206966951/p100528.png)

    -   A workspace exists.

        If you have created a workspace, you can directly bind the Hologres database to the workspace by following these steps:

        1.  Go to HoloStudio.

            On the homepage of the [Hologres console](https://hologram.console.aliyun.com/#/instance), select the region where the Hologres instance resides and click **Go to DataWorks - HoloStudio**. The HoloStudio page appears.

        2.  Bind the Hologres database to the current workspace.

            On the HoloStudio page, click **Workspace Manage** in the upper-right corner. On the **Workspace Management** page that appears, click the Hologres tab in the Compute Engine section and click **Binding HologresDB** to bind the Hologres database to the workspace.

            A default database named **postgres** is automatically created after you purchase a Hologres instance. You can use it to binding the workspace. For more information, see [Bind a Hologres database to a workspace](/intl.en-US/Common Development Tools/HoloStudio/Bind a Hologres database to a workspace.md).

            ![845](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5206966951/p100529.png)

            Go back to the **HoloStudio** page, click **PG management** in the left-side navigation pane, and then click **Refresh**. The bound database appears.

2.  **Create a new database**

    **postgres** is used to manage instances and allocates less resources.We recommend that you create a new database based on your business needs.Use the following sql to create a new database in SQL Console.

    ```
    create database <dbname>;
    ```

    ![1212](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5206966951/p139136.png)

    After create the new database,you need to go to the **Workspace Manage** to binding the new database.And then , come back to HoloStudio and refresh the **PG management**. Finally ,you can use the new database as usual.

3.  **Grant permissions to RAM users\(Optional\).**

    To grant permissions to a RAM user, follow these steps:

    1.  Create a RAM user

        ```
        create user 'p4_uid'; // 
        ```

    2.  Grant permissions to a RAM user.

        You can use the standard PostgreSQL authorization model or SPM to grant permissions to the RAM user. We recommend that you use the SPM.

        -   Use SPM, please see [Use the SPM](/intl.en-US/User Authorization/Hologres Permission Model/SPM/Use the SPM.md).
        -   Use the standard PostgreSQL authorization model, please see [Grant permissions by using the standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres Permission Model/Grant permissions by using the standard PostgreSQL authorization model.md).
4.  **Use HoloStudio to analyze data.**

    On the HoloStudio page, you can perform the following operations:

    -   Execute SQL statements in the SQL Console module to query and analyze data. For more information, see [SQL Console](/intl.en-US/Common Development Tools/HoloStudio/SQL Console.md).
    -   Manage tables and preview data in a visualized manner. For more information, see [Manage tables](/intl.en-US/Common Development Tools/HoloStudio/PG Management/Create and manage internal tables.md).
    -   Create foreign tables sourced from MaxCompute and query data with one click. For more information, see [Manage foreign tables](/intl.en-US/Common Development Tools/HoloStudio/PG Management/Create and manage foreign tables.md).
    -   Configure periodic scheduling. For more information, see [Hologres Data Analytics](/intl.en-US/Common Development Tools/HoloStudio/Data Analytics/Hologres Data Analytics.md).

For more information about development tools that Hologres supports, see [Overview](/intl.en-US/Common Development Tools/Overview.md).

