---
keyword: [create a database, PostgreSQL client, Hologres, HoloStudio]
---

# Create a database

This topic describes how to create a database in the Hologres console or from the PostgreSQL client.

A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

After you purchase a Hologres instance, a database named **postgres** is automatically created. This database is allocated with a few resources and is used only for management purposes. Therefore, we recommend that you create another database to process business data.

Only a superuser or a user granted the database creation permission can create a database.

## Create a database in the Hologres console

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the left-side navigation pane, click **Instances**.

2.  On the **Hologres Instances** page, find the instance for which you want to create a database and click the instance name.

    You can also find the instance for which you want to create a database and click **Manage** in the **Actions** column to go to the instance details page.

3.  In the left-side pane of the instance details page, click **Databases**.

4.  On the **Database Authorization** page, click **Create Database** in the upper-right corner.

5.  In the **Create Database** dialog box, select the name of the instance for which you want to create the database from the **Instance Name** drop-down list, enter a database name in the **Database Name** field, and then specify a permission model based on your business needs by setting the **SPM** parameter. We recommend that you select **SPM**.

    |Permission model|Description|
    |----------------|-----------|
    |SPM|If the simple permission model \(SPM\) is used, permissions are granted at the database level. The following user groups are provided: admin, developer, writer, and viewer. You need only to use a few functions to manage the permissions on the objects in a database in a convenient and secure way. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).|
    |SLPM|If the schema-level permission model \(SLPM\) is used, permissions are granted at the schema level. The following user groups are provided: <db\>.admin, <db\>.<schema\>.developer, <db\>.<schema\>.writer, and <db\>.<schema\>.viewer. Compared with the SPM, the SLPM manages permissions in a more fine-grained manner. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md).|
    |Standard PostgreSQL authorization model|Hologres allows you to use the standard PostgreSQL authorization model. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).|

    ![Create Database](../images/p284751.png)

6.  Click **OK**.

    You can view the created database on the **Database Authorization** page.


## Create a database from the PostgreSQL client

1.  Connect to the Hologres instance for which you want to create a database from the PostgreSQL client. For more information, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

2.  Execute the CREATE Database statement to create a database. The following code provides the syntax and a sample statement:

    ```
    CREATE Database NewDatabaseName;
    CREATE Database test; // Create a database named test. 
    ```

3.  Run the `\l` command to view the databases in the instance.

4.  Run the `\c NewDatabaseName` command to connect to the newly created database. When you run this command, replace NewDatabaseName with the name of the created database.


Execute standard PostgreSQL statements to analyze data from the PostgreSQL client. For example, you can execute SQL statements to import MaxCompute data to Hologres. For more information, see [Import data from MaxCompute to Hologres by executing SQL statements](/intl.en-US/Data Access/Batch synchronization/MaxCompute/Import data from MaxCompute to Hologres by executing SQL statements.md).

You can also use HoloWeb to analyze data. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).

