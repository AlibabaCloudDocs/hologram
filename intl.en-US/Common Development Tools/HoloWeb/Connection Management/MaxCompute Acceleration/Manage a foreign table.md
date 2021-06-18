---
keyword: [HoloWeb, foreign table, preview the table data, preview the DDL statement]
---

# Manage a foreign table

This topic describes how to use HoloWeb to create, edit, and delete a foreign table that is sourced from MaxCompute. This topic also describes how to preview the source data and the DDL statement of the foreign table.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create a foreign table

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the top navigation bar of the HoloWeb console, choose **Metadata Management** \> **MaxCompute Acceleration** \> **Create Foreign Table**.

    You can also find the left-side **Instances Connected** list on the **Metadata Management** tab. Click the instance that you want to manage and click the database that you want to manage. Right-click the schema that you want to manage and select **New external table**.

    ![Create an internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7799393261/p273758.png)

5.  On the **New external table** tab, set the parameters as required. Click **Submit form**.

    ![Create a foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7799393261/p273796.png)

    |Parameter|Description|
    |---------|-----------|
    |Instance Name|The name of the current instance.|
    |Database|The name of the current database.|
    |Table name|The name of the Hologres foreign table. After you enter the name of the source MaxCompute table, the name of the foreign table must be the same as that of the source MaxCompute table. In this case, you cannot modify the name of the foreign table. To rename the foreign table, you must create the foreign table, find the foreign table in the left-side Instances Connected list, and then right-click the foreign table. |
    |Description|The description of the Hologres foreign table.|
    |Schema|The name of the schema. You can select the default schema **public** or a custom schema. |
    |Type|The service type of the source table. The default value is MaxCompute, which cannot be changed. |
    |Server List|You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Table|The name of the source MaxCompute table to be mapped, including the name of the MaxCompute project. Format: **project.table\_name**.

**Note:**

    -   You cannot query data in a source table that is stored in a region different from that of the selected Hologres instance.
    -   After you enter the name of the source MaxCompute table, all the fields of the source table are displayed. By default, the created foreign table contains all the fields of the source table. If you need to create a foreign table that contains only specific fields of the source table, use SQL statements to create the foreign table. For more information, see [CREATE FOREIGN TABLE](/intl.en-US/Hologres SQL/DDL/FOREIGN TABLE/CREATE FOREIGN TABLE.md). |

    **Note:** When you create a foreign table to synchronize data from a MaxCompute table, the table comments and column comments of the MaxCompute table are synchronized to the foreign table.

6.  Click **Submit form**. The foreign table is created. After the foreign table is submitted, you can refresh the left-side instance lists. The created foreign table is displayed under the schema that you selected.

7.  On the configuration tab of the foreign table, click **Query table** to go to the SQL query window and execute standard PostgreSQL statements to analyze data.


## Edit a foreign table

1.  In the left-side navigation pane, click **Instances Connected**. All the connected instances are displayed.

2.  Click the instance that you want to manage. All the created databases are displayed.

3.  Click the database that you want to manage, click the schema that you want to manage, and then click **Foreign Tables**. All the created foreign tables are displayed.

4.  Right-click the foreign table that you want to manage and select **Edit Table**.

5.  Modify the fields and partition of the foreign table that is mapped from the source MaxCompute table based on your business requirements.

    ![Edit a foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7799393261/p273800.png)

6.  Click **Submit form**. The foreign table is edited.


## Rename a foreign table

1.  In the left-side navigation pane, click **Instances Connected**. Find the foreign table that you want to manage.

    For more information about how to find a foreign table, see Step 1 to Step 3 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Right-click the foreign table that you want to rename and select **Rename**. Rename the table as needed.

3.  Click **OK**.


## Delete a foreign table

1.  In the left-side navigation pane, click **Instances Connected**. Find the foreign table that you want to manage.

    For more information about how to find a foreign table, see Step 1 to Step 3 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Right-click the foreign table that you want to delete and select **Delete Table**.

3.  Click **OK**.


## Preview the table data

1.  In the left-side navigation pane, click **Instances Connected**. Find the foreign table that you want to manage.

    For more information about how to find a foreign table, see Step 1 to Step 3 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Double-click the foreign table that you want to view. On the configuration tab of the foreign table, click **Data preview**.


