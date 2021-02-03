---
keyword: [replace Lightning, best practice]
---

# Replace Lightning with Hologres

This topic describes how to replace Lightning with Hologres for data development.

Lightning is an interactive search service provided by MaxCompute. You can use Lightning to query data in MaxCompute projects based on PostgreSQL statements.

Lightning uses a shared cluster where multiple users share resources. When multiple users concurrently query a large amount of data, the query performance may deteriorate and query requests may be queued up for resources. The queries sometimes are time-consuming. In addition, Lightning does not support queries by index. To resolve the preceding issues, you can use Hologres to replace Lightning.

Hologres is a real-time interactive analytics engine that is compatible with PostgreSQL. It provides the following benefits:

-   Supports resource segregation among users so that queries performed by different users do not affect each other.
-   Integrates seamlessly with the big data ecosystem and allows you to analyze and process petabytes of data with high concurrency and low latency.
-   Integrates seamlessly with MaxCompute at the underlying layer and allows you to use foreign tables to directly query data from MaxCompute.
-   Supports using indexes to query MaxCompute data imported to Hologres, providing much better performance than other open source interactive search systems.
-   Provides the Java Database Connectivity \(JDBC\) or Open Database Connectivity \(ODBC\) driver that allows you to use Business Intelligence \(BI\) tools to analyze data from multiple dimensions.

## Comparison between Hologres and Lightning

The following table compares Hologres and Lightning.

|Category|Item|Hologres|Lightning|
|--------|----|--------|---------|
|Feature|Supported syntax|PostgreSQL 11|PostgreSQL 8.2|
|Data query based on internal tables|Supported|Not supported|
|Real-time data write|Supported|Not supported|
|Data query based on foreign tables|Supported|Supported|
|Parameter optimization|Custom parameter adjustment|Supported. For example, you can adjust database or user parameters based on your business requirements.|Not supported|
|Number of development tools concurrently connected to each instance, which reflects the number of queries per second|Increases in a linear manner with the volume of available resources.|Is fixed to 20.|
|Internal table|Using data definition language \(DDL\) statements to add, modify, and delete tables|Supported|Not supported|
|Data update|Supported|Not supported|
|Writing real-time data in real time|Supported|Not supported|
|Foreign table|Loading of the Meta module|Responds to queries in milliseconds.|Consumes more time if the number of MaxCompute tables increases, slowing down queries.|
|Data type|Supports the following data types: -   Basic data types
-   Decimal 2.0
-   Datetime
-   Array

|Only supports basic data types.|
|Performance|Engine|-   Supports using foreign tables to accelerate data queries.
-   Supports using internal tables to query data, which provides much better performance than open source interactive search systems.

|N/A|
|Resource|Supports resource segregation among users to provide stable query performance.|Does not support resource segregation among users, which is prone to latency and performance deterioration during queries.|

## Procedure

The following figure shows the process of replacing Lightning with Hologres.

![Process](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0706651161/p129010.png)

1.  Purchase a Hologres instance.

    To replace Lightning with Hologres, purchase a Hologres instance first. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

    **Note:** Make sure that you activate the MaxCompute and Hologres services in the same region.

    After you purchase a Hologres instance, you can view information about the instance in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  Create a database in the Hologres console.

    After you purchase a Hologres instance, a database named **postgres** is created by default. This database is allocated with a few resources and only used for management purposes. You can create a database based on your business requirements.

3.  Connect development tools to the Hologres instance.

    After you create a database in the Hologres instance, connect the instance to the development tools that are originally connected to Lightning. Replace the port number for connecting Lightning to development tools with the port number of the Hologres instance. Use the public endpoint of the Hologres instance.

    You can view the port number of the Hologres instance on the **Configuration** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    For more information about how to connect different development tools to a Hologres instance, see the following documentation:

    -   [PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md)
    -   [JDBC](/intl.en-US/Common Development Tools/JDBC.md)
    -   [Quick start](/intl.en-US/Quick Start/Quick start.md)
4.  Query data from MaxCompute.

    To use Hologres to query data from MaxCompute, create a foreign table in Hologres or import data from MaxCompute to Hologres. Perform the following steps:

    -   Create a foreign table.

        After you enter the name of the MaxCompute table to be queried in the Table field, fields in the table appear. You can select part or all of the fields to query as required. For more information, see [Create a foreign table to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Big Data/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md).

    -   Import data from MaxCompute to Hologres.

        If the MaxCompute table to be queried contains no less than 100 GB of data, we recommend that you import data from the MaxCompute table to Hologres. For more information, see [t1644102.md\#](/intl.en-US/Data Access/Big Data/MaxCompute/Use SQL statements to import data from MaxCompute to Hologres.md).

5.  Authorize a Resource Access Management \(RAM\) user.

    Authorize a RAM user to use Hologres. For more information, see [Use Hologres as a RAM user](/intl.en-US/Preparations/Use Hologres as a RAM user.md).

6.  Display data analysis results in a visualized manner.

    Replace the port number for connecting Lightning to BI tools with the port number of the Hologres instance. For more information about how to connect BI tools to a Hologres instance, see the following documentation:

    -   [DataService Studio of DataWorks](/intl.en-US/Common BI Tools/DataWorks DataService Studio.md)
    -   [Tableau](/intl.en-US/Common BI Tools/Tableau.md)
    -   [Quick BI](/intl.en-US/Common BI Tools/Quick BI.md)

