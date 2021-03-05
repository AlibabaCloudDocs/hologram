---
keyword: [Hologres, overview, real-time data warehouse, real-time query]
---

# What is Hologres?

Hologres is a real-time interactive analytics service developed by Alibaba Cloud. Hologres is compatible with PostgreSQL 11 and integrates seamlessly with the big data ecosystem. You can use Hologres to analyze and process petabytes of data with high concurrency and low latency.

Modern, knowledge-based society is characterized by vast improvements in data collection. These data collection methods help enterprises accelerate digital transformation, allowing them to manage terabytes, petabytes, or even exabytes of data. The steep increase in the volume of data requirements has been accompanied by rapid advancements in Data Mid-end. Data applications tend to focus more on core business areas, such as data support, user profiling, real-time target user identification, and precision advertising. Data services with high reliability and low latency are key to the digital transformation of enterprises.

Hologres is designed to provide large-scale, computing-optimized storage and excellent query capabilities supported by low costs, high performance, and high availability. Hologres provides real-time data warehouse solutions and real-time interactive query services.

## Features

-   Accelerated queries of MaxCompute data

    Hologres seamlessly integrates with MaxCompute at the underlying layer. You can execute standard PostgreSQL statements to query a large amount of MaxCompute data and obtain query results within a short period of time, without the need to synchronize data from MaxCompute to Hologres. For more information, see [Analyze large amounts of MaxCompute data in real time](/intl.en-US/Best Practices/Data warehouse construction/Analyze large amounts of MaxCompute data in real time.md).

-   Quick construction of a real-time data warehouse

    Hologres is integrated with Realtime Compute for Apache Flink to support real-time writing and query of data with high concurrency. This helps you construct a real-time data warehouse for your enterprise. For more information, see [Build a real-time data warehouse and display data analytics results](/intl.en-US/Best Practices/Data warehouse construction/Build a real-time data warehouse and display data analytics results.md).

-   Enhanced data warehouse features

    Hologres provides the following enhanced data warehouse features:

    -   Hologres supports both row-oriented and column-oriented storage models.
    -   Hologres optimizes highly concurrent queries and complex queries. This way, Hologres can respond to query requests for petabyte-level data in seconds.
    -   Hologres supports interactive and service-oriented queries.
-   Connection to mainstream BI tools

    Hologres is compatible with PostgreSQL and provides a Java Database Connectivity \(JDBC\) or Open Database Connectivity \(ODBC\) driver for you to connect Hologres to various BI tools. This way, you can query and analyze a large amount of data from multiple dimensions, without the need to migrate data. For more information, see [Overview](/intl.en-US/Common BI Tools/Overview.md).


