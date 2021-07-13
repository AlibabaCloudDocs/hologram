---
keyword: [create a database, PostgreSQL client, Hologres, HoloStudio]
---

# Create a database

This topic describes how to create a database in the Hologres console or from the PostgreSQL client.

A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

After you purchase a Hologres instance, a database named **postgres** is created by default. This database is allocated with a few resources and only used for management purposes. We recommend that you create a database based on your business needs.

Only a superuser or a user granted with the database creation permission can create a database.

## Create a database in the Hologres console

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click the name of the purchased instance. The instance details page appears.

    You can also click **Manage** in the **Actions** column of the target instance to go to the instance details page.

3.  In the left-side navigation pane of the instance details page, click **Databases**.

4.  On the **Databases** tab, click **Create Database**.

5.  In the **Create Database** dialog box, enter a database name in the **Database Name** field and specify whether to enable the simple permission model \(SPM\) by setting the **SPM** parameter based on your business needs.

    Hologres allows you to use the **standard PostgreSQL authorization model** or **SPM** for the management of user permissions.

    Compatible with PostgreSQL, Hologres uses a permission model that is exactly the same as the **standard PostgreSQL authorization model**. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).

    Backed by its understanding of customers' business and its practical experience, Alibaba Cloud introduced the **SPM** to Hologres to simplify the management of user permissions. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).

    If you want to simplify permission management, we recommend that you set **SPM** to **Enable** when you create a database.

    ![Create Database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1670289951/p136042.png)

6.  Click **OK**.

    After the database is created, it appears on the **Databases** tab.


## Create a database from the PostgreSQL client

1.  Connect to the target Hologres instance from the PostgreSQL client. For more information, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

2.  Execute the following SQL statement to create a database:

    ```
    CREATE Database NewDatabaseName;
    CREATE Database test; // Create a database named test.
    ```

3.  Run the `\l` command to view the databases in the current instance.

4.  Run the `\c NewDatabaseName` command to connect to the created database. Replace NewDatabaseName with the name of the created database when you run this command.


Use standard PostgreSQL statements to analyze data from the PostgreSQL client. For example, you can use SQL statements to import MaxCompute data to Hologres. For more information, see [t1644102.md\#](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Import data from MaxCompute to Hologres by executing SQL statements.md).

You can also use HoloStudio in DataWorks or HoloWeb for data analytics. For more information, see [Overview]() or Quick start.

