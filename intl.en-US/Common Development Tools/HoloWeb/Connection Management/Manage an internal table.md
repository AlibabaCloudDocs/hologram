---
keyword: [HoloWeb, internal table, preview the table data, preview the DDL statement]
---

# Manage an internal table

This topic describes how to use HoloWeb to create, edit, and delete an internal table. This topic also describes how to preview the data and the DDL statement of the internal table.

You have logged on to a Hologres instance. For more information, see [Log on to an instance](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Log on to an instance.md).

## Create an internal table

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the top navigation bar of the HoloWeb console, choose **Metadata Management** \> **Table**.

    You can also find the left-side **Instances Connected** list on the **Metadata Management** tab. Click the instance that you want to manage and click the database that you want to manage. Right-click the schema that you want to manage and select **New internal table**.

    ![Create an internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7799393261/p273758.png)

5.  On the **New internal table** tab, set the parameters as required.

    ![Configure the internal table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1130493261/p273762.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |Basic information|Instance Name|The name of the current instance.|
    |Database|The name of the current database.|
    |Table name|The name of the Hologres internal table.|
    |Description|The description of the Hologres internal table.|
    |Schema|The name of the schema. You can select the default schema **public** or a custom schema. |
    |Field|Field name|The name of the field in the internal table.|
    |Data type|The data type of the field.|
    |PK|Specifies whether to use the field as the primary key for the internal table.|
    |Nullable|Specifies whether the field can be left empty.|
    |Array|Specifies whether the field is an ordered array of elements.|
    |Description|The description of the field.|
    |Operation|The operations that you can perform on the field. You can click **Delete** to delete the field and click **Up** or **Down** to move the field up or down.|
    |Property|Storage Format|The storage mode of the table. Valid values: **Column storage** and **Row storage**. Default value: **Column storage**. |
    |Life Cycle \(seconds\)|The lifecycle of the table data. If the data is not updated within the specified period, the system deletes the data. Default value: **Forever**. |
    |Clustered Index|The index that is used to sort columns. The type of index determines the order of fields. Hologres can use clustered indexes to accelerate range and filter queries on index fields. |
    |Event Time Column|The fields that are used to segment data. If the specified fields are involved in the query conditions, Hologres can find the storage location of data based on the fields.|
    |Dictionary Encoding|The fields based on whose values a dictionary mapping is built. Dictionary encoding can convert string comparisons to numeric comparisons to accelerate queries such as GROUP BY and FILTER.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Bitmap Columns|The bit fields on which bit codes are built. You can filter the data that meets the query conditions based on the specified fields by using Hologres.

By default, the system selects all the fields of the **text** type for this parameter. |
    |Distribution Column|The distribution key. Hologres shuffles data to each shard based on the specified column. Data entries with the same distribution key value are distributed to the same shard. If you use a distribution key as a filter condition, the execution efficiency can be greatly improved.|
    |Partition Table|N/A|The partition fields of the internal table.|

6.  In the upper-right corner, click **Submit form**. After the internal table is submitted, you can refresh the left-side instance lists. The created internal table is displayed under the schema that you selected.

    On the configuration tab of the internal table, you can click **Query table** in the upper-right corner to go to the SQL query window and query data by using SQL statements.


## Edit an internal table

1.  In the left-side navigation pane, click **Instances Connected**. All the connected instances are displayed.

2.  Click the instance that you want to manage. All the created databases are displayed.

3.  Click the database that you want to manage, click the schema that you want to manage, and then click **Tables**. All the created internal tables are displayed.

4.  Right-click the internal table that you want to edit and select **Edit Table**.

5.  In the lower part of the configuration tab, click **Add Field**. In the section that appears, add a field to the internal table in visualized mode. After you submit the internal table, you can view the corresponding SQL statements and operational logs in the **Run Log** section.

    ![Edit Table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1130493261/p132198.png)

    **Note:** You cannot delete a submitted field in an internal table when you edit the table.

6.  Click **Submit form**. The internal table is edited.


## Delete an internal table

1.  In the left-side navigation pane, click **Instances Connected**. Find the internal table that you want to manage.

    For more information about how to find an internal table, see Step 1 to Step 3 in [Edit an internal table](#section_uu3_wol_suh).

2.  Right-click the internal table that you want to delete and select **Delete Table**.

3.  Click **OK**.


## Preview the table data

1.  In the left-side navigation pane, click **Instances Connected**. Find the internal table that you want to manage.

    For more information about how to find an internal table, see Step 1 to Step 3 in [Edit an internal table](#section_uu3_wol_suh).

2.  Double-click the internal table that you want to view. On the configuration tab of the table, click **Data preview**.

    Preview the table data, as shown in the following figure.

    ![Data preview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130493261/p273779.png)


## Preview the DDL statement

1.  In the left-side navigation pane, click **Instances Connected**. Find the internal table that you want to manage.

    For more information about how to find an internal table, see Step 1 to Step 3 in [Edit an internal table](#section_uu3_wol_suh).

2.  Double-click the internal table that you want to view. On the configuration tab of the table, click **DDL statement**.

    Preview the DDL statement of the internal table, as shown in the following figure.

    ![DDL statement](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2130493261/p273781.png)


