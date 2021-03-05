---
keyword: [Hologres, 阿里云实时数仓, 实时分析, MaxCompute加速]
---

# 阿里云实时数仓Hologres的应用场景

Hologres兼容PostgreSQL生态，是新一代的阿里云实时数仓产品，与大数据生态无缝连接，支持实时分析实时与离线数据，对接第三方BI工具，实现可视化分析业务。本文为您介绍基于Hologres核心功能的典型应用场景。

Hologres的典型应用场景如下：

-   搭建实时数仓。

    实时写入业务数据至实时计算后，使用ETL（Extract Transformation Load）方式清洗、转换及整理数据。您可以通过Hologres实时查询并输出数据至第三方分析工具进行实时分析。典型应用场景如下：

    -   数据部门搭建阿里云实时数仓、展示实时大屏和分析实时Reporting报表。
    -   运维和数据应用部门执行实时监控、实时异常检测预警与实时调试。
    -   业务部门进行实时风控、实时推荐、实时效果分析和实时训练。
    ![实时](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3324485061/p66688.png)

-   MaxCompute加速查询。

    写入业务数据至离线数据仓库MaxCompute，通过Hologres直接加速查询或导入数据至Hologres查询，并对接BI分析工具，实现实时分析离线数据。典型应用场景如下：

    -   实时查询MaxCompute离线数据。
    -   分析MaxCompute离线数据报表。
    -   输出MaxCompute离线数据的在线应用，例如RESTful API的使用。
    ![离线](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3324485061/p66690.png)

-   联邦分析实时数据和离线数据。

    业务数据分为冷数据和热数据，冷数据存储在离线数据仓库MaxCompute中，热数据存储在Hologres中。Hologres可以联邦分析实时数据和离线数据，对接BI分析工具，快速响应简单查询与复杂查询的业务需求。

    ![联邦](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3324485061/p66691.png)


