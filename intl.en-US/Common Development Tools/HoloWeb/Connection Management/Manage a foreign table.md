---
keyword: [HoloWeb, foreign table, preview the table data, preview the DDL statement]
---

# Manage a foreign table

This topic describes how to use HoloWeb to create, edit, and delete a foreign table that is sourced from MaxCompute. This topic also describes how to preview the source data and the Data Definition Language \(DDL\) statement of the foreign table.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create a foreign table

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  In the HoloWeb console, click **Connection Management** in the top navigation bar, move the pointer over **Accelerated Queries of MaxCompute Data**, and then select **Create Foreign Table**.

    You can also go to the New external table tab by performing the following steps:

    1.  In the left-side navigation pane, choose **Connection Management** \> **My connection**. All the configured connections are displayed.

    2.  Click the required connection and then click **Database**. All the created databases are displayed.

    3.  Click the required database and then click **Mode**. All the available modes are displayed.

    4.  Right-click the required mode and select **New external table**.

3.  On the **New external table** tab, set the parameters as required. Click **Submit form**.

    ![Foreign table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9726412161/p132279.png)

    |Parameter|Description|
    |---------|-----------|
    |Connection name|The name of the required connection.|
    |Database|The name of the Hologres database.|
    |Table name|The name of the Hologres foreign table.|
    |Description|The description of the Hologres foreign table.|
    |Mode|The name of the schema.Default value: **public**. You can also select a custom schema. |
    |Type|The service type of the source table.The default value is MaxCompute, which cannot be changed. |
    |Server List|You can select the **odps\_server** server that is created at the underlying layer of Hologres. For more information, see [postgres\_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k).|
    |Table|The name of the source MaxCompute table to be mapped, including the name of the MaxCompute project.Format: **project.table\_name**.

**Note:**

    -   You cannot query data in a source table that is stored in a region different from that of the selected Hologres instance.
    -   After you enter the name of a source table, the fields in the table are displayed. You can synchronize all the fields or part of the fields in the source table to the Hologres foreign table as needed. |

    **Note:** When you create a foreign table to synchronize data from a MaxCompute table, comments of both field names and columns of the MaxCompute table are synchronized to the foreign table.

4.  Click **Submit form** to create the foreign table.

5.  On the configuration tab of the foreign table, click **Query table** to go to the SQL query window and execute standard PostgreSQL statements to analyze data.

    ![SQL query window](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9726412161/p171289.png)


## Edit a foreign table

1.  In the left-side navigation pane, click **My connection**. All the configured connections are displayed.

2.  Click the required connection and then click **Database**. All the created databases are displayed.

3.  Click the required database and then click **Mode**. All the available modes are displayed.

4.  Click the required mode and then click **Foreign table**. All the created foreign tables are displayed.

5.  Right-click the required foreign table and select **Edit table**.

6.  You can modify the fields and partition of the foreign table that is mapped from the source MaxCompute table based on your business requirements.

    ![Edit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9726412161/p132300.png)

7.  Click **Submit form**.


## Delete a foreign table

1.  In the left-side navigation pane, click **My connection**. Then, find the required foreign table.

    For more information about how to find the required foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Right-click the foreign table and select **Delete table**.

3.  Click **OK**.


## Preview the table data

1.  In the left-side navigation pane, click **My connection**. Then, find the required foreign table.

    For more information about how to find the required foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Double-click the foreign table. On the configuration tab of the foreign table, click **Data preview**.

    Preview the source data, as shown in the following figure.

    ![Data preview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7566762061/p132309.png)


## Preview the DDL statement

1.  In the left-side navigation pane, click **My connection**. Then, find the required foreign table.

    For more information about how to find the required foreign table, see Step 1 to Step 4 in [Edit a foreign table](#section_ppd_18h_f0e).

2.  Double-click the foreign table. On the configuration tab of the foreign table, click **DDL statement**.

    Preview the DDL statement of the foreign table, as shown in the following figure.

    ![DDL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7566762061/p132312.png)


