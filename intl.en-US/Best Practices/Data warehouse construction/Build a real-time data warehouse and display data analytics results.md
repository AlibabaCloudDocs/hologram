---
keyword: [real-time data warehouse, best practice, Hologres]
---

# Build a real-time data warehouse and display data analytics results

This topic describes how to connect Hologres to Realtime Compute to build a real-time data warehouse and then connect Hologres to a Business Intelligence \(BI\) tool to display data analytics results.

-   A Hologres instance is purchased and a development tool is connected to the instance. For more information, see [Quick start to HoloStudio](/intl.en-US/HoloStudio/Quick start to HoloStudio.md) or Quick start to HoloWeb.
-   Realtime Compute is activated. For more information, see [Activate Realtime Compute and create a project](/intl.en-US/Exclusive Mode/Preparation/Activate Realtime Compute and create a project.md).

    **Note:** Make sure that you activate the Realtime Compute and Hologres services in the same region.

-   DataV is activated. For more information, see Activate DataV.

Hologres is an interactive analytics service developed by Alibaba Cloud. Based on the built-in HoloHub API, Hologres connects to Realtime Compute to support real-time writes and queries in high concurrency. Hologres can respond to queries within seconds.

Hologres is compatible with PostgreSQL. You can connect Hologres to a BI tool to analyze queried data and display data analytics results in a visualized manner.

This topic uses an e-commerce store as an example to describe how to display operating metrics in real time. The metrics include the number of unique visitors \(UVs\) to the store, number of UVs to each product, sales amount in each city, and statistics of hot-selling products.

The following figure shows the process of using Hologres to display operating metrics on a dashboard in real time.

![a](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2929670061/p120681.png)

-   Source data is collected and written to Realtime Compute in real time for cleansing and aggregation.
-   Data processed by Realtime Compute is written to Hologres for interactive searches.
-   Query results are displayed in DataV in real time, which is connected to Hologres.

1.  Collect source data.

    Use DataHub, a streaming data processing service, or service logs to collect source data.

    To simplify the process, this best practice uses Realtime Compute to generate source data. For more information, see step 3.

2.  Create a table in Hologres for receiving data.

    Use [HoloWeb](https://holoweb.data.aliyun.com/?accounttraceid=45e5a27e1d4e45498cce63682fbfc8b9fbzx#/) to create a table for receiving data. Make sure that this table contains the same fields of the same data types as the source table. The following SQL statements are used as an example:

    ```
    begin;
    drop table if exists order_details;
    create table order_details(user_id bigint, user_name text, item_id bigint, item_name text, price numeric(38, 2), province text, city text, ip text, longitude text, latitude text, sale_timestamp timestamptz not null);
    call set_table_property('order_details', 'distribution_key', 'user_id');
    call set_table_property('order_details', 'segment_key', 'sale_timestamp');
    call set_table_property('order_details', 'clustering_key', 'sale_timestamp');
    call set_table_property('order_details', 'shard_count', '20');
    commit;
    ```

3.  Use Realtime Compute to cleanse data.

    Log on to the [Realtime Compute console](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/). In the console, create a job to cleanse and aggregate collected data in the data source and call the HoloHub API to write processed data to Hologres in real time. The following SQL statements are used as an example:

    ```
    // Create a data source.
    create table order_details(
      user_id BIGINT,
      user_name VARCHAR,
      item_id BIGINT, 
      item_name VARCHAR,
      price numeric(38, 2),
      province VARCHAR,
      city VARCHAR,
      ip VARCHAR,
      longitude VARCHAR,
      latitude VARCHAR,
      sale_timestamp TIMESTAMP
    ) with (
      type = 'custom',
      tableFactoryClass = 'com.alibaba.blink.connectors.hologres.table.factory.DemoDataGeneratorFactory');
    
    // Create a connection to Hologres.
    create table hologres_sink(
        user_id BIGINT,
        user_name VARCHAR,
        item_id BIGINT, 
        item_name VARCHAR,
        price numeric(38, 2),
        province VARCHAR,
        city VARCHAR,
        ip VARCHAR,
        longitude VARCHAR,
        latitude VARCHAR,
        sale_timestamp TIMESTAMP 
      ) with (
      type = 'custom',
      tableFactoryClass = 'com.alibaba.blink.connectors.hologres.table.factory.HologresTableFactory',
      endpoint = 'Virtual Private Cloud (VPC) endpoint and port number used to call the HoloHub API',
      dbName = 'Name of the Hologres database to be connected to',
      tableName = 'Name of the Hologres table for receiving data',
      username = 'AccessKey ID of the current Alibaba Cloud account',
      password = 'AccessKey secret of the current Alibaba Cloud account',
      batchSize = '500',
      bufferSize = '500'
    );
    
    // Write data to Hologres.
    insert into hologres_sink
    select 
      user_id
    , user_name
    , item_id
    , item_name
    , price
    , province
    , city
    , latitude
    , longitude
    , ip
    , sale_timestamp 
    from order_details;
    ```

    You can run the following command in the target Hologres instance to query the VPC endpoint used to call the HoloHub API:

    ```
    show hg_datahub_endpoints;
    ```

4.  Publish a Realtime Compute job.

    To commit and publish a Realtime Compute job to the production environment, perform the following steps:

    1.  Reference a resource package in a job.

        Log on to the [Realtime Compute console](https://account.alibabacloud.com/login/login.htm?oauth_callback=http://stream-ap-southeast-3.console.aliyun.com/). In the left-side navigation pane, click **Resources**. On the page that appears, click **Create Resource**. In the **Upload Resource** dialog box, set parameters as required to upload a Realtime Compute resource package. To obtain a sample Realtime Compute resource package, click [Blink](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/170591/cn_zh/1591698479126/blink-connector-hologres-07-demo%281%29.jar).

        ![a](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2929670061/p118229.png)

    2.  Publish the job.

        After a resource package is referenced, click Save and then **Publish**. Set resource parameters as required and publish the job to the production environment.

        ![b](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2929670061/p118230.png)

    3.  Start the job.

        After the job is published, go to Administration page to start the job. The job then enters the Running state, as shown in the following figure.

        ![v](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2929670061/p118232.png)

5.  Use Hologres to query data in real time.

    Use the SELECT statement to query data that is written to Hologres in real time from different dimensions. The following SQL statements are used as an example:

    ```
    select sum(price) as "GMV" from order_details ;
    select count(distinct user_id) as "UV" from order_details ;
    select city as "City", count(distinct user_id) as "Number of customers who purchased products" from order_details group by "City" order by "Number of customers who purchased products" desc limit 100;
    select item_name as "Product", sum(price) as "Sales amount" from order_details group by "Product" order by "Sales amount" desc limit 100;
    select to_char(sale_timestamp, 'MM-DD') as "Date", sum(price) as "GMV" from order_details group by "Date" order by "GMV" desc limit 100;
    ```

6.  Create a dashboard in DataV to display query results in Hologres.

    To create a dashboard in DataV to display query results in Hologres, perform the following steps:

    1.  Add a connection to the data source.

        Log on to the DataV console. Click the **Data Sources** tab. On the Data Sources tab, click **Add Source**. In the **Add Data Source** dialog box, set parameters as required.

        Select Hologres from the **Type** drop-down list.

    2.  Create a dashboard to display real-time data.

        Select widgets to be contained on the dashboard and configure a data source for each widget based on your business requirements. For more information, see [Widget overview](/intl.en-US/Manage Widgets/Widget overview.md).

        This best practice selects the basic column chart, carousel widget, basic flat map, and ticker board for the dashboard. Take a multiple pie chart as an example.

        1.  Set parameters for the **data source** used by the multiple pie chart.
        2.  Set the title, border, font, and color for the multiple pie chart.
    3.  Decorate the dashboard.

        After you configure the widgets and their data sources, you can decorate the dashboard as required.

        -   The total number of UVs to each product and sales amount in top cities are shown on the left in real time.
        -   The map in the middle highlights the location of each transaction order and refreshes the total sales amount in real time. The total number of UVs to the store is shown on the top of the map.
        -   The sales percentage and sales ranking of each product are shown on the right in real time.

