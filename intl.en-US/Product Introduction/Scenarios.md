---
keyword: [Hologres, real-time data warehouse, real-time analytics]
---

# Scenarios

Hologres is a next-generation real-time data warehouse provided by Alibaba Cloud. It is compatible with PostgreSQL and integrates seamlessly with the big data ecosystem. Hologres supports real-time analytics of real-time and offline data. You can connect Hologres to third-party BI tools to analyze data in a visualized manner. This topic describes typical scenarios where the core features of Hologres are used.

Hologres is typically used in the following scenarios:

-   Construct a real-time data warehouse.

    Business data is written to Realtime Compute in real time for extract, transform, load \(ETL\) operations. Then, you can use Hologres to query data in real time and export data to a third-party analytics tool for real-time analytics. For example, you can use Hologres to meet the following requirements:

    -   The data department wants to construct a real-time data warehouse, view real-time data on a dashboard, and create real-time reports.
    -   The O&M and data application departments require real-time monitoring, real-time exception detection and alerting, and real-time debugging.
    -   The business department requires real-time risk control, real-time recommendation, real-time effect analytics, and real-time training.
    ![Real-time analytics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4530495951/p66688.png)

-   Perform .

    After business data is written to the offline data warehouse MaxCompute, you can directly query data by using Hologres or import data to Hologres for queries. You can also connect Hologres to BI tools to analyze offline data in real time. For example, you can use Hologres to meet the following requirements:

    -   Query offline MaxCompute data in real time.
    -   Analyze offline MaxCompute data and generate reports.
    -   Apply offline MaxCompute data for online use. For example, expose your table data over a RESTful API.
    ![Offline analytics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4530495951/p66690.png)

-   Perform federated analytics on offline and real-time data.

    Business data is divided into hot data and cold data based on their access frequency. Cold data is stored in the offline data warehouse MaxCompute, whereas hot data is stored in Hologres. You can use Hologres to perform federated analytics on offline and real-time data. You can also connect Hologres to BI tools to respond to business requirements of simple and complex queries.

    ![Federated analytics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4530495951/p66691.png)


