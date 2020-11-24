---
keyword: [Hologres, limits]
---

# Limits

This topic describes the limits that apply to Hologres.

## Limits on data query in MaxCompute tables

-   Hologres can scan a maximum of 50 partitions from a partitioned table during a single query.
-   The maximum amount of underlying data scanned for each query is 200 GB, regardless of the number of tables and fields.

## Limits on UDFs

-   Hologres does not support user-defined functions \(UDFs\) that are created in MaxCompute.
-   Hologres does not allow you to create or use PostgreSQL UDFs. However, you can use the built-in functions of PostgreSQL.
-   Hologres does not support the built-in functions of MaxCompute.

## Limits on the number of connections

The maximum number of a single Hologres instance depends on the specification of the instance. Execute the following statement for details:

```
show max_connections;
```

## Limits on data types

The data types of Hologres and PostgreSQL are compatible. However, the data types of Hologres are a subset of the data types of PostgreSQL. For more information, see [Data types](/intl.en-US/Hologres SQL/Data types/Data types.md).

## Limits on HoloStudio

-   You can create multiple databases for a Hologres instance. You can also bind a Hologres database to multiple workspaces in the same region.
-   On the editing page of the **data analytics node** in HoloStudio, go to the Properties tab. In the Resource Group section, **Shared Resource Group** is specified by default. If you need to configure a **dedicated resource group**, [submit a ticket](https://workorder-intl.console.aliyun.com/).

## Limits on the purchase of Hologres instances

-   Each Alibaba Cloud account can buy a maximum of three instances per day.
-   Hologres supports the **pay-as-you-go** and **subscription** billing methods. You can only change the billing method from **pay-as-you-go** to **subscription**. You cannot change the billing method from **subscription** to **pay-as-you-go**.
-   If you stop the instance that uses the **pay-as-you-go** billing method in the Hologres console, you are still charged for the storage resources of the instance.
-   If you are using the **subscription** billing method and the quota of the storage resources is reached, the billing method is changed to **pay-as-you-go**.

