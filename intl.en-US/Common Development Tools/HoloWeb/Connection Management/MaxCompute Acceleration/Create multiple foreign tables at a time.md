---
keyword: [HoloWeb, create multiple foreign tables at a time]
---

# Create multiple foreign tables at a time

This topic describes how to use HoloWeb to create multiple foreign tables at a time in a visualized manner.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create multiple foreign tables at a time

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  In the top navigation bar of the HoloWeb page, click **Connection Management**, move the pointer over **Accelerated Queries of MaxCompute Data**, and then select **Create Multiple Foreign Tables**.

    You can also go to the New external tables tab by performing the following steps:

    1.  In the left-side navigation pane, choose **Connection Management** \> **My connection**. All the configured connections are displayed.

    2.  Click the required connection and then click **Database**. All the created databases are displayed.

    3.  Click the required database and then click **Mode**. All the created modes are displayed.

    4.  Right-click the required mode and select **Create Multiple Foreign Tables**.

3.  On the **Create Multiple Foreign Tables** tab, set the parameters as required.

    ![Create multiple foreign tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9136412161/p186605.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Basic information|Connection name|The name of the required connection.|
    |Database|The name of the Hologres database where the foreign tables are to be created.|
    |Destination Location|Mode|The name of the schema.You can select the default schema **public** or a newly created schema. |
    |Source MaxCompute Tables|Type|The default value is MaxCompute, which cannot be changed.|
    |Server List|You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Source Project|The name of the MaxCompute project.Format: **project.table\_name**.

**Note:**

    -   You cannot query data in a source table that is stored in a region different from that of the selected Hologres instance.
    -   After you enter the name of a source table, the fields in the table are displayed. You can synchronize all the fields or part of the fields in the source table to the Hologres foreign table as needed. |
    |Data Source|    -   **Entire Project**: specifies to create foreign tables for all tables in the MaxCompute project at a time.
    -   **Selected Tables**: specifies to create foreign tables only for specified tables in the MaxCompute project. You can search for required tables based on table names.

**Note:** If you select **Partial acceleration**, a maximum of 200 tables can be displayed when your search for source tables in the specified MaxCompute project. Extra tables are not displayed, but foreign tables are successfully created for these tables. For example, if the specified MaxCompute project contains 203 source tables with test in the table name, 200 tables can be displayed at most when you enter test in the search box. However, foreign tables are successfully created for 203 source tables. |
    |Advanced Settings|Processing Rule for Table Name Conflicts|    -   **Ignore, the existence of the same name table**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, the system skips the creation of this table and continues to create other tables.
    -   **Update and modify the same name table**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, the system updates data in the existing foreign table.
    -   **Error reported, no repeat creation**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, an error is reported to avoid repeated creation. |
    |Processing Rule for Unsupported Data Types|    -   **Which tables have unsupported types**: If specific data types in the source table are not supported by the foreign table that you want to create, an error is reported and data import fails.
    -   **Prompt with unsupported Tables are skipped**: If specific data types in the source table are not supported by the foreign table that you want to create, the system import data from all fields except for fields of the unsupported data types. |

    **Note:** When you create a foreign table to synchronize data from a MaxCompute table, comments of both field names and columns of the MaxCompute table are synchronized to the foreign table.

4.  Click **Run** to create multiple foreign tables at a time.


