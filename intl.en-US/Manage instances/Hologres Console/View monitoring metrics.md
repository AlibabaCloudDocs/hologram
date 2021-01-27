---
keyword: [alerts, Hologres console]
---

# View monitoring metrics

The Hologres console provides a monitoring and alerting module. This module monitors metrics and reports alerts for Hologres instances. You can view and timely handle the exceptions of your Hologres instances. The metrics include the CPU utilization, memory usage, storage usage, query latency, write latency, query QPS, and write QPS.

A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## View monitoring metrics

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/overview).

2.  In the top navigation bar, select a region.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2965585061/p166691.png)

3.  In the left-side navigation pane, click **Instances** to go to the **Hologres Instances** page.

4.  On the Hologres Instances page, find the instance that you want to access and click its name to go to the instance details page.

    You can also click **Manage** in the **Actions** column that corresponds to the instance to go to the instance details page.

5.  In the left-side navigation tree, click **Alerts** to go to the **Alerts** page and view monitoring metrics.


## Monitoring metrics

The Hologres console provides the following monitoring metrics:

-   **CPU Usage**

    This metric indicates the total number of CPU cores used by all components of a Hologres instance. The value of this metric increases by 100% after a CPU core is used up. For example, the value 3200% indicates that 32 CPU cores are used up.

    If the number of CPU cores that are used approaches or exceeds your instance specifications, you must scale up the instance. Otherwise, the stability or performance of the instance may be affected.

-   **Memory Usage**

    This metric indicates the total memory used by all components of a Hologres instance.

    If the memory usage approaches or exceeds your instance specifications, you must scale up the instance. Otherwise, the stability or performance of the instance may be affected.

-   **Storage Usage**

    This metric indicates the total disk space used by the data that is stored in a Hologres instance.

    If the logical disk for an instance that is purchased in **Subscription** mode is used up, excess data stored in the instance will be charged in **Pay-as-you-go** mode. Set an appropriate lifecycle for the stored data and delete useless data in a timely manner to reduce unnecessary storage costs.

-   **Query Latency**

    Query latency indicates the average latency of data queries performed by using SQL statements.

    A high latency may indicate that the query statements are inappropriate, the instance does not have sufficient resources, or the number of concurrent queries is large. You can change the query pattern to ensure the performance and stability of the instance.

-   **Write Latency**

    Write latency indicates the average latency of data insertions performed by using SQL statements.

    A high latency may indicate that the instance does not have sufficient resources or large amounts of data need to be written. You can determine whether to scale up the instance based on the CPU utilization.

-   **Query QPS**

    Query QPS indicates the number of data queries performed by using SQL statements per second.

-   **Write QPS**

    Write QPS indicates the number of data insertions performed by using SQL statements per second.


