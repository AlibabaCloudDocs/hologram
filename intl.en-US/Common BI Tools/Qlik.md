---
keyword: [visualized data analysis, Qlik Sense Desktop]
---

# Qlik

Qlik is a classic business intelligence \(BI\) platform. It provides various BI tools such as Qlik Sense. Qlik can help you develop and deliver apps and dashboards for interactive guidance and analysis. This topic describes how to connect Qlik Sense Desktop to Hologres and analyze data in a visualized manner.

## Limits

Qlik does not support visual display of foreign tables of Hologres. However, you can query foreign tables by executing SQL statements in the data loading editor. Then, you can analyze related data in a visualized manner.

## Connect Qlik Sense Desktop to Hologres by creating a PostgreSQL connection

1.  Installation Qlik Sense Desktop.

    For more information about how to install Qlik Sense Desktop, see [Qlik official documentation](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Introduction/install-desktop.htm).

2.  Connect Qlik Sense Desktop to Hologres.

    1.  Log on to Qlik Sense Desktop. On the Qlik Sense Desktop hub page, click **Create new app** in the upper-right corner.

        ![Create new app](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p246399.png)

    2.  In the Create new app dialog box, enter a name for the app to be created and click **Create**.

        ![Create](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p246401.png)

    3.  In the dialog box that appears, click **Open app**. On the page that appears, click **Add data from files and other sources**.

        ![Open app](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p246402.png)

    4.  In the dialog box that appears, select PostgreSQL and set the parameters that are described in the following table.

        ![postgres](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p242745.png)

        |Parameter|Description|
        |---------|-----------|
        |Host name|The public domain name of the Hologres instance to be connected to. You can view the public domain name of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of the Hologres instance to be connected to. You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database|The name of the Hologres database to be connected to.|
        |User Name|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey ID, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey secret, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|

    5.  Click **Test connection**. If the `Connection succeeded` message appears, the connection is valid. You can click **Create** in the lower-right corner to save the connection information.

        ![Save the connection information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p246404.png)

    6.  Specify the data to be imported by using the PostgreSQL connection.

        ![public](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p242772.png)

        -   Owner: Select the schema where the data to be imported resides. In this example, select public.
        -   Tables: Select the table where the data to be imported resides.
    7.  Click **Add Data**. After you specify the data to be imported, Qlik Sense Desktop imports the data from Hologres. Then, you can view the imported Hologres data in Qlik Sense Desktop.

        **Note:** In this mode, Qlik Sense Desktop loads all the imported data to the Qlik Sense engine. This way, Qlik Sense Desktop does not need to send real-time requests to the Hologres database each time a query is performed.

3.  Configure the Direct Query mode.

    In daily production scenarios, a database usually contains petabytes of data. We recommend that you use the Direct Query mode in Qlik Sense Desktop, instead of importing data to Qlik Sense Desktop. For more information about the Direct Query mode, see [Qlik official documentation](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Scripting/ScriptRegularStatements/direct-query.htm).

    1.  Go to the details page of the app created in Step 2. Choose **Data manager** \> **Data load editor**.

        ![Open the data loading editor](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1281442261/p246406.png)

    2.  Click the ![Insert icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p242955.png) icon in the Data connections pane to insert the connection information that is displayed in the upper-right corner to the data loading editor.

        ![Insert the connection information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p242954.png)

    3.  In the data loading editor, enter `Direct Query` preceding the query statements to enable the Direct Query mode. The following figure and script are for your reference.

        ![Enable the Direct Query mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3361932261/p246408.png)

        ```
        SET ThousandSep=',';
        SET DecimalSep='.';
        SET MoneyThousandSep=',';
        SET MoneyDecimalSep='.';
        SET MoneyFormat='¥#,##0.00;-¥#,##0.00';
        SET TimeFormat='TTh:mm:ss';
        SET DateFormat='YYYY/M/D';
        SET TimestampFormat='YYYY/M/D TTh:mm:ss[.fff]';
        SET FirstWeekDay=6;
        SET BrokenWeeks=1;
        SET ReferenceDay=0;
        SET FirstMonthOfYear=1;
        SET CollationLocale='zh-CN';
        SET CreateSearchIndexOnReload=1;
        SET MonthNames='Jan;Feb;Mar;Apr;May;Jun;Jul;Aug;Sep;Oct;Nov;Dec';
        SET LongMonthNames='January;February;March;April;May;June;July;August;September;October;November;December';
        SET DayNames='Mon;Tue;Wed;Thu;Fri;Sat;Sun';
        SET LongDayNames='Monday;Tuesday;Wednesday;Thursday;Friday;Saturday;Sunday';
        SET NumericalAbbreviation='3:k;6:M;9:G;12:T;15:P;18:E;21:Z;24:Y;-3:m;-6:μ;-9:n;-12:p;-15:f;-18:a;-21:z;-24:y';
        
        LIB CONNECT TO 'PostgreSQL_hgpostcn-cn-st21y8nlm007-cn-hangzhou.hologres.aliyuncs.com';
        
        -- Enable the Direct Query mode.
        DIRECT QUERY
        
        dimension
        l_shipmode,
        l_shipdate,
        o_orderstatus
        
        measure
        l_tax,
        l_quantity
        
        FROM public.lineitem INNER JOIN public.orders
        ON public.lineitem.l_orderkey = public.orders.o_orderkey;
        ```

    4.  Click **Load data** in the upper-right corner. Then, Qlik Sense Desktop instantly sends a request to the Hologres database to query data.

    5.  Click the **Sheet** tab at the top. On the Sheet tab, click **Edit sheet**.

        ![Edit sheet](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p246412.png)

    6.  On the page that appears, click Charts in the left-side pane and select a chart type for data visualization.

        **Note:**

        -   If you directly drag a field to the canvas, Qlik Sense Desktop cannot obtain related data in Direct Query mode. We recommend that you click Charts and create a chart for data visualization.
        -   To improve the response speed, we recommend that you add measures and then dimensions. This method works better with the processing logic of Qlik Sense Desktop.
        ![Edit sheet](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p242974.png)

    For more information about how to use Qlik Sense Desktop to manage and analyze data, see [Qlik official documentation](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Introduction/install-desktop.htm).


## Connect Qlik Sense Desktop to Hologres by customizing an ODBC connection

1.  Install a PostgreSQL ODBC driver.

    You can download a PostgreSQL ODBC driver from [File Browser](https://www.postgresql.org/ftp/odbc/versions/msi/). Make sure that the version of the ODBC driver is **psqlodbc\_11\_01\_0000** or later.

2.  Configure a data source name \(DSN\).

    1.  Open **Control Panel** of your computer. In the upper-right corner of the window, select **Large icons** from the View by drop-down list. Click **Administrative Tools**.

    2.  Double-click **ODBC Data Sources \(64-bit\)**. In the dialog box that appears, click the System DSN tab.

        **Note:** User DSNs are available only to specific users, whereas system DSNs are available to all users. If you want to connect a web BI server to Hologres by using ODBC, you must use a system DSN.

    3.  Click **Add**. In the Create New Data Source dialog box, select **PostgreSQL Unicode\(x64\)**.

    4.  Click **Finish**. In the PostgreSQL Unicode ODBC Driver \(psqlODBC\) Setup dialog box, set the parameters that are described in the following table.

        ![DSN settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p243140.png)

        |Parameter|Description|
        |---------|-----------|
        |Database|The name of the Hologres database to be connected to.|
        |Server|The public domain name of the Hologres instance to be connected to. You can view the public domain name of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Port|The port number of the Hologres instance to be connected to. You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |User Name|The AccessKey ID of your Alibaba Cloud account. For more information about how to obtain the AccessKey ID, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|
        |Password|The AccessKey secret of your Alibaba Cloud account. For more information about how to obtain the AccessKey secret, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).|

    5.  Click **Test**. If the `Connection successful` message appears, the connection is valid. You can click **Save** in the lower-right corner to save the DSN settings.

3.  Connect Qlik Sense Desktop to Hologres.

    1.  Log on to Qlik Sense Desktop and go to the details page of an app. On the page that appears, click **Add data from files and other sources**.

        ![Add data from files and other sources](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p268935.png)

    2.  In the dialog box that appears, select ODBC.

    3.  Select the DSN created in Step 2 and enter the name of the connection, such as Hologres. Click **Create** to save the connection information.

        ![ODBC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p268938.png)

    4.  Specify the data to be imported by using the ODBC connection.

        ![ODBC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4361932261/p268948.png)

        -   Owner: Select the schema where the data to be imported resides. In this example, select public. When an ODBC connection is used, Qlik Sense Desktop can read the schema of a foreign table.
        -   Tables: Select the table where the data to be imported resides.
    5.  After the data is imported to Qlik Sense Desktop, you can start creating reports.

        In this mode, Qlik Sense Desktop loads all the imported data to the Qlik Sense engine. This way, Qlik Sense Desktop does not need to send real-time requests to the Hologres database each time a query is performed.


