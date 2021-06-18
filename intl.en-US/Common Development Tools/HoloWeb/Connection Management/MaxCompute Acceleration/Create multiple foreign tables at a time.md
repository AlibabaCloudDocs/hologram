---
keyword: [HoloWeb, create multiple foreign tables at a time]
---

# Create multiple foreign tables at a time

This topic describes how to use HoloWeb to create multiple foreign tables at a time in a visualized manner.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create multiple foreign tables at a time

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the top navigation bar of the HoloWeb console, choose **Metadata Management** \> **MaxCompute Acceleration** \> **Create Multiple Foreign Tables**.

    You can also find the left-side **Instances Connected** list on the **Metadata Management** tab. Click the instance that you want to manage and click the database that you want to manage. Right-click the schema that you want to manage and select **Create Multiple Foreign Tables**.

    ![Create an internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7799393261/p273758.png)

5.  On the **Create Multiple Foreign Tables** tab, set the parameters as required.

    ![Create multiple foreign tables at a time](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3830493261/p273805.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Basic information|Instance Name|The name of the current instance.|
    |Database|The name of the Hologres database where the foreign tables are to be created.|
    |Destination Location|Schema|The name of the schema. You can select the default schema **public** or a custom schema. |
    |Source MaxCompute Tables|Type|The default value is MaxCompute, which cannot be changed.|
    |Server List|You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Source Project|The name of the MaxCompute project.|
    |Data Source|    -   **Entire Project**: creates foreign tables for all tables in the MaxCompute project at a time.
    -   **Selected Tables**: creates foreign tables only for specified tables in the MaxCompute project. You can search for required tables based on table names or keywords.

**Note:** If you select **Selected Tables** and search for required tables, a maximum of 200 tables are displayed in the search results. The excess tables are not displayed, but foreign tables can be created for these tables.

For example, the MaxCompute project has 203 tables whose names contain test. When you enter test to search for tables, only 200 tables are displayed in the search results. However, foreign tables can be created for all 203 tables. |
    |Advanced Settings|Processing Rule for Table Name Conflicts|    -   **Ignore, the existence of the same name table**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, the system skips the creation of this table and continues to create other tables.
    -   **Update and modify the same name table**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, the system updates data in the existing foreign table.
    -   **Error reported, no repeat creation**: If the name of a foreign table that you want to create is the same as that of a foreign table in the database, an error is reported to avoid repeated creation. |
    |Processing Rule for Unsupported Data Types|    -   **Which tables have unsupported types**: If specific data types in the source table are not supported by the foreign table that you want to create, an error is reported and data import fails.
    -   **Prompt which unsupported Tables are skipped**: If specific data types in the source table are not supported by the foreign table that you want to create, the system imports data from all fields except for fields of the unsupported data types. |

    **Note:** When you create a foreign table to synchronize data from a MaxCompute table, the table comments and column comments of the MaxCompute table are synchronized to the foreign table.

6.  Click **Run**. Multiple foreign tables are created at a time.


