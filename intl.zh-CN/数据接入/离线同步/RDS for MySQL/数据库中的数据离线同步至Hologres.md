---
keyword: [数据集成, Hologres, 数据库, 离线同步]
---

# 数据库中的数据离线同步至Hologres

Hologres支持通过DataWorks平台将数据库中的数据离线同步至Hologres，进行高并发低延时的查询分析处理操作。本文将为您介绍使用DataWorks将各类数据库数据离线同步至Hologres的操作方法。

-   开通DataWorks，详情请参见[入门概述]()。
-   开通云数据库RDS MySQL，详情请参见[快速入门](/intl.zh-CN/快速入门/快速入门.md)。

**说明：** 跨地域是否可以同步数据，详情请参见[选择网络连通方案]()。

Hologres是一款实时交互式分析产品，与大数据生态无缝打通，与大数据智能研发平台DataWorks深度融合。您可以通过DataWorks数据集成同步将数据库中的数据离线同步至Hologres，再进行高并发低延时的查询分析处理。

常见的支持离线数据同步的数据库包括：RDS for MySQL、Oracle、Polar DB、SQL Server等。

**说明：** 如需查看更多支持的数据库，请参见[支持的数据源与读写插件]()。

相关原理：[MySQL Reader]()、[Oracle Reader]()、[PolarDB Reader]()、[SQL Server Reader]()、[Hologres Writer]()。

1.  配置数据源。

    离线同步前，需先配置输入源数据库和Hologres数据源。

    -   配置输入源数据库：
        -   [配置MySQL数据源]()
        -   [配置Oracle数据源]()
        -   [配置PolarDB数据源]()
        -   [配置SQLServer数据源]()
        -   更多数据源配置请参见[配置数据源](https://www.alibabacloud.com/help/zh/faq-list/137669.htm?spm=a2c63.l28256.b99.209.20df2466yvNcz6)。
    -   配置Hologres数据源：[配置Hologres数据源]()
2.  配置离线同步任务。

    配置数据源成功之后，可以配置同步任务将数据库中的数据离线同步至Hologres。您可以根据业务情况选择**向导模式**或者**脚本模式**。

    -   [通过向导模式配置任务]()
    -   [通过脚本模式配置任务]()
3.  查询数据。

    任务同步成功之后，您可以在Hologres中查询到已同步的数据。

    ```
    SELECT * FROM rds_test;
    ```


