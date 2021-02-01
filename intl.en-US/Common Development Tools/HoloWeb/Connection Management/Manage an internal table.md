---
keyword: [HoloWeb, internal table, preview the table data, preview the DDL statement]
---

# Manage an internal table

This topic describes how to use HoloWeb to create, edit, and delete an internal table. This topic also describes how to preview the data and the Data Definition Language \(DDL\) statement of the internal table.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create an internal table

1.  Connect the purchased Hologres instance to HoloWeb. For more information, see [Manage a connection](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md).

2.  In the top navigation bar of the HoloWeb console, choose **Connection Management** \> **Table**.

    You can also go to the New internal table tab by performing the following steps:

    1.  In the left-side navigation pane, choose **Connection Management** \> **My connection**. All the configured connections are displayed.

    2.  Click the required connection and then click **Database**. All the created databases are displayed.

    3.  Click the required database and then click **Mode**. All the available modes are displayed.

    4.  Right-click the required schema and select **New internal table**.

3.  On the **New internal table** tab, set the parameters as required.

    ![New internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4099438951/p132195.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Basic information|Connection name|The name of the required connection.|
    |Database|The name of the Hologres database.|
    |Table name|The name of the Hologres internal table.|
    |Description|The description of the Hologres internal table.|
    |Mode|The name of the schema.Default value: **public**. You can also select a custom schema. |
    |Field|Field name|The name of the field in the internal table.|
    |Date type|The data type of the field.|
    |Primary key|Specifies whether to use the field as the primary key for the internal table.|
    |Nullable|Specifies whether the field can be left empty.|
    |Array|Specifies whether the field is an ordered array of elements.|
    |Description|The description of the field.|
    |Operation|You can click **Delete** to delete the field and click **Move up** or **Move down** to move the field up or down.|
    |Attribute|Storage mode|Valid values: **Column storage** and **Row deposit**.Default value: **Column storage**. |
    |Life Cycle \(seconds\)|If a record is not updated within the specified period, the system deletes the record.By default, the system retains records permanently. |
    |Clustered index|The index that is used to sort columns.The type of index determines the order of fields. By using clustered indexes, Hologres can accelerate range and filter queries on index fields. |
    |Segment Key|The fields that is used for segmenting. If the specified fields are involved in the query conditions, Hologres can find the storage location of data based on the fields.|
    |Dictionary encoding column|The fields based on whose values a dictionary mapping is built.Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Bitmap columns|Hologres allows you to build bit codes on bit fields.You can filter the data that meets the query conditions based on the specified fields by using Hologres.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Partition Table|N/A|The partition fields of the internal table.|

4.  Click **Submit form**.

    On the configuration tab of the internal table, you can click **Query table** in the upper-right corner to go to the SQL query window, and query data by using SQL statements.


## Edit an internal table

1.  In the left-side navigation pane, click **My connection**. All the configured connections are displayed.

2.  Click the required connection and then click **Database**. All the created databases are displayed.

3.  Click the required database and then click **Mode**. All the available modes are displayed.

4.  Click the required mode and then click **Table**. All the created internal tables are displayed.

5.  Right-click the required internal table and select **Edit table**.

6.  Click **Add Field**. In the section that appears, add a field to the internal table in visualized mode. You can also view the corresponding SQL statements and operational logs in the **Run Log** section when you submit the internal table.

    ![Edit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4099438951/p132198.png)

    **Note:** You cannot delete a submitted field in an internal table when you edit the table.

7.  Click **Submit form**.


## Delete an internal table

1.  In the left-side navigation pane, click **My connection**. Then, find the required internal table.

    For more information about how to find the required internal table, see Step 1 to Step 4 in [Edit an internal table](#section_uu3_wol_suh).

2.  Right-click the required internal table and select **Delete table**.

3.  Click **OK**.


## Preview the table data

1.  In the left-side navigation pane, click **My connection**. Then, find the required internal table.

    For more information about how to find the required internal table, see Step 1 to Step 4 in [Edit an internal table](#section_uu3_wol_suh).

2.  Double-click the required internal table and click **Data preview** on the configuration tab of the table.

    Preview the table data, as shown in the following figure.

    ![Data preview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4099438951/p132200.png)


## Preview the DDL statement

1.  In the left-side navigation pane, click **My connection**. Then, find the required internal table.

    For more information about how to find the required internal table, see Step 1 to Step 4 in [Edit an internal table](#section_uu3_wol_suh).

2.  Double-click the required internal table and click **DDL statement** on the configuration tab of the table.

    Preview the DDL statement of the internal table, as shown in the following figure.

    ![DDL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4099438951/p132202.png)


