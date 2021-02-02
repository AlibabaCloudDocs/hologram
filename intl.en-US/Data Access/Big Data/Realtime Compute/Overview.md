---
keyword: [real-time data warehouse, Hologres, overview, use Flink to write data to Hologres in real time]
---

# Overview

Alibaba Cloud Realtime Compute for Apache Flink is powered by Ververica and developed by Alibaba Cloud based on Apache Flink. It is an enterprise-level, high-performance system that is used to process big data in real time. Hologres is seamlessly integrated with Flink. This way, you can use Flink to write data to Hologres and query data that is written to Hologres in real time. This helps you construct a real-time data warehouse for your enterprise.

Alibaba Cloud Realtime Compute for Apache Flink does not provide data stores. All the data of Alibaba Cloud Realtime Compute for Apache Flink is from external storage systems. Alibaba Cloud Realtime Compute for Apache Flink supports the following types of data storage:

-   Source table

    A source table is used to import data from Flink. You can import data from Flink into a Hologres source table in batch mode rather than streaming mode. After the data is imported into the Hologres table, Hologres scans all data in the table in a uniform manner to finish the import process. Then, the data can be transmitted to downstream data stores.

-   Dimension table

    A dimension table is used to import static data. The dimension table applies to point query scenarios. In this case, a Hologres dimension table that is used to import data from Flink must use the row-oriented storage type. Fields that are used to join dimension tables must be complete primary key fields.

-   Result table

    A result table is used to receive and store result data that is obtained by Flink and allows you to continually read or write downstream data.

-   Binlog

    You can consume the change logs of a Hologres table in message queue mode.


The following table describes the Flink service types that are supported by Hologres and the features of these service types.

|Service type|Type of data storage|Description|
|Source table|Result table|Dimension table|Binlog|
|------------|--------------------|-----------|
|------------|------------|---------------|------|
|Blink exclusive cluster \(original product line\)|Supports row-oriented storage and column-oriented storage.|Supports row-oriented storage and column-oriented storage.|Supports only row-oriented storage.|N/A|Uses the Bayes development platform.

Blink V3.5 and earlier versions are incompatible with Hologres. We recommend that you update the Blink exclusive cluster to Blink V3.6 or later. |

