---
keyword: [real-time data warehouse, Hologres, overview, use Flink to write data to Hologres in real time]
---

# Overview

Realtime Compute for Apache Flink is powered by Ververica and developed by Alibaba Cloud based on Apache Flink. It is an enterprise-level, high-performance system that is used to process big data in real time. Hologres is seamlessly integrated with Flink. This way, you can use Flink to write data to Hologres and query the data in real time. This helps you construct a real-time data warehouse for your enterprise.

Realtime Compute for Apache Flink does not store data. All the data of Realtime Compute for Apache Flink is from external storage systems. Realtime Compute for Apache Flink supports the following types of data storage:

-   Source table

    A source table is used to import data from Realtime Compute for Apache Flink. You can import data from Realtime Compute for Apache Flink to a Hologres source table in batch mode rather than streaming mode. After the data is imported to the Hologres table, Hologres scans all the data in the table to finish the import process. Then, the data can be transmitted to downstream data stores.

-   Dimension table

    A dimension table is used to import static data. Dimension tables are applicable to point query scenarios. We recommend that you use the row-oriented storage model for dimension tables. Fields that are used to join dimension tables must be all the fields in the primary key.

-   Result table

    A result table is used to receive and store result data that is processed by Realtime Compute for Apache Flink and allows you to continually read or write the data before the data is transmitted to downstream data stores.

-   Binlog

    You can consume the change logs of a Hologres table in message queue mode.


The following table describes the Flink service types that are supported by Hologres and the features of these service types.

|Service type|Type of data storage|Description|
|Source table|Result table|Dimension table|Binlog|
|------------|--------------------|-----------|
|------------|------------|---------------|------|
|Flink全托管|支持行存储及列存储。|支持行存储及列存储。|建议使用行存储。|不支持|使用VVP开发平台。|
|Semi-managed Flink|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage is recommended.|Not supported|Uses the Ververica Platform \(VVP\).|
|Blink in exclusive mode|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage is recommended.|Hologres V0.8 supports only row-oriented storage. Hologres V0.9 and later support both row-oriented storage and column-oriented storage. Row-oriented storage is recommended.|Uses the Bayes development platform.

Blink V3.5 and earlier are incompatible with Hologres. We recommend that you update your Blink exclusive cluster to Blink V3.6 or later. |
|Open source Flink V1.10|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage and column-oriented storage are supported.|-|Not supported|-|
|Open source Flink V1.11|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage and column-oriented storage are supported.|Row-oriented storage is recommended.|Not supported|-|

