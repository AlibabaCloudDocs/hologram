---
keyword: [HoloWeb, table, preview the table data, preview the DDL statement]
---

# Manage a table

This topic describes how to use HoloWeb to create, edit, and delete an internal table, the data of which is stored in Hologres. This topic also describes how to preview the data and the Data Definition Language \(DDL\) statement of the table.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   The purchased Hologres instance is connected to HoloWeb. For more information, see [Manage a connection](/intl.en-US/HoloWeb/Connection Management/Manage a connection.md).

## Create a table

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8969182061/p141749.png)

3.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log in to the hologres database**.

4.  On the **Connection Management** page of the HoloWeb console, click the **Table** tab. The New internal table tab appears.

    You can also go to the New internal table tab by performing the following steps:

    1.  In the left-side navigation pane of the **Connection Management** page, click **My connection**. All the configured connections are displayed.

    2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

    3.  Click the relevant database and then click **Schema**. All the available schemas are displayed.

    4.  Right-click the schema under which you want to create a table and select **New internal table**.

5.  On the **New internal table** tab, set the parameters as required.

    ![New internal table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4099438951/p132195.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Basic information|Connection name|The name of the connection based on which you want to create the table.|
    |Database|The name of the database where the table is to be created.|
    |Table name|The name of the table.|
    |Description|The description of the table.|
    |Mode|The schema under which the table is to be created.Default value: **public**. You can also select a custom schema. |
    |Field|Field name|The name of the field to be added to the table.|
    |Data type|The data type of the field.|
    |Primary key|Specifies whether to use the field as the primary key for the table.|
    |Nullable|Specifies whether the field can be left empty.|
    |Array|Specifies whether the field is an ordered array of elements.|
    |Description|The description of the field.|
    |Operation|The operations that you can perform on the field. You can click **Delete** to delete the field and click **Move up** or **Move down** to move the field up or down.|
    |Attribute|Storage mode|The storage mode of table data. Valid values: Column storage and Row deposit.Default value: Column storage. |
    |Life Cycle \(seconds\)|The period for retaining a record that involves no updates. If a record is not updated within that period, the system deletes the record.By default, the system retains records permanently. |
    |Clustered index|The index used for sorting.The type of index determines the order of fields. By using clustered indexes, Hologres can accelerate range and filter queries on index fields. |
    |Segment Key|The fields used for segmenting. Hologres can find the storage location of the corresponding data based on the specified fields.|
    |Dictionary encoding column|The fields based on the values of which a dictionary mapping is built.Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Bitmap columns|The fields on which bit codes are built.Hologres can find the records that meet the query conditions based on the specified fields.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Partition Table|N/A|The partition fields of the table.|

6.  Click **Submit form**.


## Edit a table

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed.

2.  Click the relevant connection and then click **Database**. All the databases that are created in the connected Hologres instance are displayed.

3.  Click the relevant database and then click **Schema**. All the available schemas are displayed.

4.  Click the schema for which you want to edit a table and then click **Table**. All the tables that are created in the database under the schema are displayed.

5.  Right-click the table that you want to edit and select **Edit table**.

6.  Click **Add Field**. In the section that appears, add a field to the table in visualized mode. Then, view the corresponding statements in the **SQL Script** editor.

    ![Edit table](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4099438951/p132198.png)

    **Note:** You cannot delete a submitted field in a table when you edit the table.

7.  Click **Submit form**.


## Delete a table

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the table that you want to delete.

    For more information about how to find the desired table, see Step 1 to Step 4 in [Edit a table](#section_uu3_wol_suh).

2.  Right-click the table and select **Delete table**.


## Preview the table data

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the table of which you want to preview the data.

    For more information about how to find the desired table, see Step 1 to Step 4 in [Edit a table](#section_uu3_wol_suh).

2.  Double-click the table.

    On the tab that appears, click Data preview and preview the table data, as shown in the following figure.

    ![Data preview](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4099438951/p132200.png)


## Preview the DDL statement

1.  In the left-side navigation pane of the Connection Management page, click **My connection**. All the configured connections are displayed. Then, find the table of which you want to preview the DDL statement.

    For more information about how to find the desired table, see Step 1 to Step 4 in [Edit a table](#section_uu3_wol_suh).

2.  Double-click the table.

    On the tab that appears, click DDL statement and preview the DDL statement of the table, as shown in the following figure.

    ![DDL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4099438951/p132202.png)


