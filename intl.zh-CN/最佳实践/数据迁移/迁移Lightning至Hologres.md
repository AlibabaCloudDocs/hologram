---
keyword: [迁移Lightning, 最佳实践]
---

# 迁移Lightning至Hologres

本文为您介绍如何迁移Lightning服务至交互式分析Hologres进行数据开发。

Lightning是MaxCompute产品的交互式查询服务，支持使用PostgreSQL语句查询分析MaxCompute项目中的数据，快速获取查询结果。

Lightning采用公共集群，多用户之间共用资源。查询大量数据时，会出现性能波动和资源排队问题，并且耗时较长。Lightning不支持通过创建索引查询数据。您可以迁移Lightning服务至Hologres，避免上述问题。

Hologres是兼容PostgreSQL协议的实时交互式分析产品，具有如下优势：

-   用户之间资源独立，查询操作互不干扰。
-   与大数据生态无缝打通，支持高并发和低延时地分析处理PB级数据。
-   在底层与MaxCompute资源无缝打通，支持使用新建外部表的方式加速查询MaxCompute数据。
-   支持通过创建索引查询导入至Hologres的MaxCompute数据，性能相比开源系统大幅提升。
-   提供JDBC/ODBC Driver，支持对接各种BI工具，多维分析数据。

## 对比Hologres与Lightning的产品特性

Hologres与Lightning的产品特性对比如下。

|类别|对比项|Hologres|Lightning|
|--|---|--------|---------|
|功能|支持的语法|PostgreSQL11|PostgreSQL8.2|
|是否支持使用内部表查询数据|支持|不支持|
|是否支持写入实时数据|支持|不支持|
|是否支持使用外部表查询数据|支持|支持|
|优化参数|是否支持定向调整参数|支持，例如您可以根据实际业务调整数据库或用户的相关参数。|不支持|
|实例连接开发工具的数量（QPS）。|随资源规模线性增加|固定为20个|
|内部表|是否支持使用DDL语句增加、修改或删除表|支持|不支持|
|是否支持更新数据|支持|不支持|
|是否支持实时写入实时数据|支持|不支持|
|外部表|加载Meta模块的速度|加载Meta模块的速度快，实现查询数据的响应速度达到毫秒级。|加载Meta模块的速度随Maxcompute表的数量线性增加，导致查询数据速度较慢。|
|数据类型|支持的数据类型如下： -   基本类型
-   Decimal2.0
-   Datetime
-   Array类型

|仅支持基本类型|
|性能|引擎|-   支持使用外部表加速查询数据
-   使用内部表查询数据的性能大幅领先开源系统

|不涉及|
|资源|用户之间资源独立，查询操作性能稳定。|用户之间共享资源，查询数据时容易出现延时和性能波动。|

## 操作步骤

迁移Lightning至Hologres进行数据开发的操作流程如下图所示。

![全流程](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7223432161/p129010.png)

1.  开通Hologres实例。

    迁移Lightning前，您需要开通Hologres实例，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。

    **说明：** Hologres实例与MaxCompute实例的地域必须保持一致。

    成功开通实例后，您可以前往[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)查看实例信息。

2.  新建数据库。

    开通Hologres实例后，系统自动创建**postgres**数据库。该数据库分配到的资源较少，仅用于运维管理。您需要新建数据库来处理实际业务。

3.  连接开发工具。

    成功新建数据库后，您需要连接Hologres实例至原有的开发工具。修改原Lightning服务的端口为对应Hologres实例的端口，网络地址使用公网地址。

    进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。

    连接开发工具操作如下：

    -   使用Psql客户端开发数据，详情请参见[psql客户端](/intl.zh-CN/连接开发工具/psql客户端.md)。
    -   使用JDBC开发数据，详情请参见[JDBC](/intl.zh-CN/连接开发工具/JDBC.md)。
    -   使用HoloWeb开发数据，详请请参见[HoloWeb快速入门](/intl.zh-CN/快速入门/HoloWeb快速入门.md)。
4.  查询MaxCompute数据。

    您可以通过新建外部表或导入MaxCompute数据至Hologres，加速查询MaxCompute的数据。操作如下：

    -   新建外部表。

        输入MaxCompute表名称后即可索引出当前MaxCompute表的字段，您可以根据需要选择查询部分或全部字段。详情请参见[通过创建外部表加速查询MaxCompute数据](/intl.zh-CN/数据接入/大数据/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。

    -   导入MaxCompute数据至Hologres。

        如果您单次需要查询的MaxCompute表数据超过100 GB，建议您导入MaxCompute表数据至Hologres后再进行查询。详情请参见[使用SQL导入MaxCompute的数据至Hologres](/intl.zh-CN/数据接入/大数据/MaxCompute/使用SQL导入MaxCompute的数据至Hologres.md)。

5.  授权子账号。

    授权子账号使用Hologres，详情请参见[RAM用户使用Hologres](/intl.zh-CN/准备工作/RAM用户使用Hologres.md)。

6.  可视化分析数据。

    您需要修改原Lightning服务的端口为Hologres实例的端口，使用如下BI工具通过可视化方式分析数据：

    -   使用DataWorks数据服务分析数据，详情请参见[DataWorks数据服务](/intl.zh-CN/常见BI连接工具/DataWorks数据服务.md)。
    -   使用Tableau分析数据，详情请参见[Tableau](/intl.zh-CN/常见BI连接工具/Tableau.md)。
    -   使用Quick BI分析数据，详情请参见[Quick BI](/intl.zh-CN/常见BI连接工具/Quick BI.md)。

