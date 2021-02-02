---
keyword: [HoloHub API, Hologres]
---

# Overview of the HoloHub API

This topic describes the HoloHub API and its features.

-   Processing workflow for real-time data

    Traditionally, you can process real-time data in the following process: Collect and ingest real-time data to Realtime Compute for cleansing, import data to databases in real time, and then use business intelligence \(BI\) tools to analyze the data in a visualized manner.

-   Pain points in the processing workflow for real-time data

    To complete the process, databases must provide immense computing and storage capabilities and be capable of connecting to multiple BI tools at the same time. No single type of databases can meet the requirements of the preceding process. To complete this process, you must use multiple types of databases.

    However, the use of multiple types of databases may cause redundant storage. This causes the wastes of storage resources. In addition, multiple database systems are difficult to manage and maintain.

-   Introduction of the HoloHub API

    To resolve the preceding pain points, Hologres provides the HoloHub API. You can call the HoloHub API to write business data and log data to Hologres in real time. Then, Hologres processes the data with its immense computing and storage capabilities.

    In this process, you do not need to export or import data. All collected data is stored in Hologres. This generates no redundant storage and saves computing and storage resources. Hologres can meet your requirements for processing real-time data by using only one system. This saves your development and operations and management \(O&M\) costs.


