---
keyword: [import an on-premises file, HoloWeb]
---

# Import an on-premises file to Hologres

This topic describes how to use HoloWeb to import an on-premises file to Hologres.

-   An Alibaba Cloud account is created.
-   The real-name verification is complete.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

If you want to import a file to Hologres in a visualized manner by using HoloWeb, you must make sure that the file is smaller than 100 MB in size. To import files that are larger than 100 MB in size, you must execute the `COPY` statement. For more information, see [Use the COPY statement to import on-premises data to Hologres](/intl.en-US/Data Access/Batch synchronization/A local file/Use the COPY statement to import on-premises data to Hologres.md).

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  In the HoloWeb console, click **System Management** in the top navigation bar.

5.  In the left-side navigation pane, choose **Data Import** \> **Import On-premises File**. On the page that appears, click **New data import**.

6.  In the **Import On-premises File** dialog box, set the parameters as required in the **Select Target table** step and click **Next Step**.

    ![Import On-premises File](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7228993261/p275130.png)

    |Parameter|Description|
    |---------|-----------|
    |Job Name|The name of the import task.|
    |Instance Name|The name of the connected instance.|
    |Target Database|The name of the database to which the destination table belongs.|
    |Target Schema|The name of the schema to which the destination table belongs. If you have not created a schema, select the default **public** schema. If you have created a schema, you can select the created schema. |
    |Destination Table|The name of the destination table to which you want to import the data of the on-premises file. Before you import data from an on-premises file, you must create a table in the destination database to store the data of the on-premises file. |

7.  In the **Select File** step, set the parameters as required and click **Next Step**.

    ![Select File](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0680382061/p141340.png)

    |Parameter|Description|
    |---------|-----------|
    |Select File|The on-premises file to be uploaded. Only .txt, .csv, and .log files are supported. |
    |Delimiter|The delimiter that is used to separate fields in the on-premises file. Valid values:     -   **Comma**
    -   **Tab**
    -   **SEMICOLON**
    -   **Space**
    -   **\|**
    -   **\#**
    -   **&**
 You can also specify a custom delimiter. |
    |Character Encoding|    -   **GBK**
    -   **UTF-8**
    -   **CP936**
    -   **ISO-8859** |
    |First Line as Header|Specifies whether to use the first row as the table header.|

8.  In the **Import Overview** step, click **Execution**.

    After you run the import task, you can view the schema, database, and table that are related to the task in the Import Overview step. You can also view the status and start time of the task in this step.


