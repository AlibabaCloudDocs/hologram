---
keyword: [HoloStudio, create multiple foreign tables at a time]
---

# Create multiple foreign tables at a time

This topic describes how to use HoloStudio to create multiple foreign tables at a time.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/Common Development Tools/HoloStudio/Quick start to HoloStudio.md).

Hologres is integrated with MaxCompute at the underlying layer. You can create foreign tables to accelerate queries of MaxCompute data. For more information, see [Create a foreign table to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Big Data/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md).

The Data Analytics module of HoloStudio provides the **One-click MaxCompute table structure synchronization** submodule. In this submodule, you can create foreign tables in a visualized manner.

You can also use the `[IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)` statement to create multiple foreign tables at a time. You can execute this statement in a data analytics node in the Interactive Analytics Development submodule of Data Analytics and schedule the node to implement the following feature: A foreign table is automatically created in Hologres after a MaxCompute table is created. For more information, see [Hologres Data Analytics](/intl.en-US/Common Development Tools/HoloStudio/Data Analytics/Hologres Data Analytics.md).

**Note:** You can use Hologres to accelerate queries of only internal tables of MaxCompute. Queries of foreign tables and views of MaxCompute cannot be accelerated.

1.  Create a **schema sync node**.

    1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    2.  In the top navigation bar of the console, move the pointer over the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

    3.  In the left-side navigation pane, click **Instances**. On the **Hologres Instances** page, click **Go to DataWorks - HoloStudio**.

    4.  In the left-side navigation pane of **HoloStudio**, click the ![Develop data in HoloStudio](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134237.png).

    5.  On the **Data Analytics** page, move the pointer over the ![Create icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134243.png) icon.

    6.  Click **One-click MaxCompute table structure synchronization**.

    7.  In the **Create Node** dialog box, set **Node Name**, **Location**, and **Database**.

    8.  Click **Commit**.

2.  On the editing page of the created node, set the parameters as required.

    ![bia](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134363.png)

    |Parameter|Description|
    |---------|-----------|
    |Target Library|The name of the Hologres database where the foreign tables are created.|
    |Target schema|The name of the schema in the specified Hologres database.If you have not created a schema, select the default **public** schema. If you have created a schema, you can select the created schema. |
    |Remote Service type|The type of the external service. Default value: **odps**.You can create foreign tables that are sourced only from MaxCompute. |
    |Remote server|You can directly call the **odps\_server** server created at the underlying layer of Hologres. For more information, see [Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Remote library|The name of the MaxCompute project where the tables for which you want to create foreign tables reside.|
    |Table name rules|The regular expression that is used to specify the tables for which you want to create foreign tables. By default, foreign tables are created for all tables in the specified MaxCompute project. For more information about the rules that are used to filter tables, see [IMPORT FOREIGN SCHEMA](/intl.en-US/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md).|
    |Regular preview|The execution result of the regular expression.|

    When you create a foreign table, the following rules apply:

    -   The foreign table cannot be created if it has the same name as an existing foreign table in Hologres.
    -   An error occurs if the foreign table contains data types that are not supported by Hologres. In this case, you must exclude the MaxCompute table from the regular expression and create another foreign table.
3.  On the editing page of the created node, click the ![Save icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134275.png) icon to save the configurations.

4.  On the editing page of the created node, click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134276.png) icon to create multiple foreign tables.

5.  On the **PG management** page, view the created foreign tables and query data by using these tables. For more information, see [Create and manage foreign tables](/intl.en-US/Common Development Tools/HoloStudio/PG Management/Create and manage foreign tables.md).


