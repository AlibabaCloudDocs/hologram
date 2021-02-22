---
keyword: [Quick BI, 可视化分析, Hologres]
---

# Quick BI

Quick BI为您提供海量数据的实时在线分析服务以及丰富的可视化效果，您可以通过拖拽式操作或SQL语句方式，轻松地完成数据分析、业务数据探查及报表制作。本文为您介绍如何使用交互式分析Hologres连接Quick BI，并进行可视化分析。

-   开通Hologres，详情请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。
-   开通Quick BI，详情请参见[Quick BI购买、升级、降级、续费、欠费]()。

Hologres与Quick BI高效连通，支持支持通过Hologres数据源直接连接Quick BI，将Hologres高效查询的数据直接进行可视化分析。

**说明：** 当前Quick BI高级版和专业版支持Hologres数据源，其余版本请使用PostgreSQL数据源，更多关于PostgreSQL数据源的操作请参见[云数据源PostgreSQL]()。

本文以高级版Qucik BI为例，为您演示连接Hologres并进行可视化分析的相关操作。

## 使用限制

-   Quick BI当前仅支持使用公共网络和经典网络连接Hologres，不支持使用VPC网络连接Hologres。
-   您无需在Hologres中配置白名单即可连接至Quick BI。
-   Hologres连接Quick BI时，您需要根据Quick BI的版本选择相应的数据源，推荐使用Hologres数据源。

## 使用Quick BI进行可视化分析

1.  登录[Quick BI管理控制台](http://das.base.shuju.aliyun.com/console.htm?spm=a2c0j.8190895.737583.btn2.34f87d6a2MBlQ6)。

2.  添加数据源。

    1.  在Quick BI管理控制台页面，单击顶部菜单栏的**工作空间**。

    2.  在**工作空间**页面左侧导航栏，单击**数据源**。

    3.  在**数据源**页面，单击右上角的**+新建数据源**。

    4.  选择目标数据源并配置相应参数。

        1.  选择**云数据库** \> **Hologres**。

            ![选择Hologres数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1299147061/p190025.png)

        2.  配置**添加Hologres数据源**的各项参数。

            ![添加Hologres数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2063147061/p189738.png)

            参数说明如下表所示。

            |参数|描述|
            |--|--|
            |显示名称|自定义的显示名称。 |
            |数据库地址|连接的Hologres实例的服务器地址。

您可以登录[管理控制台](https://hologram.console.aliyun.com/#/instance)，进入实例详情页，从**实例配置**页面获取。

**说明：** Quick BI当前仅支持使用公共网络和经典网络连接Hologres，不支持使用VPC网络连接Hologres。 |
            |端口|连接的Hologres实例的端口。 |
            |数据库|连接的Hologres实例的数据库名称。

您可以登录[管理控制台](https://hologram.console.aliyun.com/#/instance)，进入实例详情页，从**DB管理**页面获取。 |
            |Schema|默认为**public** Schema。您也可以使用新创建的Schema，在数据源配置了Schema后，列表中会展示当前Schema下所有的表。但是在使用即席SQL时，还需要在表名称前手动添加对应的Schema名（即shcema.table），才能正确索引到Schema下面的表。 |
            |用户名|具有当前数据库登录权限账号的AccessKey ID。

您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
            |密码|具有当前数据库登录权限账号的AccessKey Secret。

您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |

        参数配置完成后，您可以单击**连接测试**，测试Quick BI与Hologres的连接状态：

        -   如果显示**数据源连通性正常**，则表示Quick BI与Hologres连接成功。
        -   如果显示**数据源连通性异常，请检查参数是否正确**，则表示Quick BI与Hologres连接失败。请根据报错信息处理异常。
    5.  单击**确定**，完成配置。

3.  可视化分析数据。

    成功连接数据源后，在**数据源** \> **我的数据源**页面，单击已创建的数据源，显示当前数据库中的所有表。

    -   如果您希望使用界面化操作完成数据的可视化分析，则可以使用拖拽方式。
    -   如果您希望使用SQL语句完成数据的可视化分析，则可以使用即席分析SQL方式。
    两种方式的具体操作如下：

    -   拖拽方式。
        1.  单击目标表**操作**列的![创建数据集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3948430061/p167521.png)图标，创建数据集。
        2.  配置**创建数据集**对话框的**名称**和**位置**参数。

            ![创建数据集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3975337061/p188765.png)

        3.  单击**确定**。
        4.  在**我的数据集**页面，您可以单击目标数据集**操作**列的如下图标，进行相应操作：

            ![数据集操作列描述](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3975337061/p188773.png)

            -   单击![编辑](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3948430061/p167525.png)图标，查看当前数据集的数据。
            -   单击![新建仪表盘](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167526.png)图标，新建仪表盘，将表数据导入并生成图表，进行可视化展示。
            -   单击![新建电子表格](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167527.png)图标，新建电子表格，将目标表数据导入并生成电子表格，进行可视化展示。
            -   单击![更多](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167528.png)图标，执行更多操作。例如，数据脱敏、缓存配置及清除缓存等。
            具体功能的操作，详情请参见[概述]()。

            示例新建仪表板，在仪表板编辑页面创建堆积柱状图，并设计图表样式展示数据。

            ![展示新建仪表盘](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4975337061/p188790.png)

    -   即席分析SQL方式。

        1.  在**数据源**页面，单击右上角的**即席分析SQL**，进入**即席分析SQL**页面。
        2.  您可以根据业务需求输入查询SQL语句，并单击**执行**。

            ![示例即席查询SQL语句](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4975337061/p188813.png)

        3.  获取需要查询的数据后，单击**创建数据集**。
        4.  配置**保存自定义SQL**对话框的**名称**、**位置**及**SQL**参数。

            ![配置保存自定义SQL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4975337061/p188814.png)

        5.  单击**确定**。
        成功创建数据集后，您可以在**数据集**中可视化分析表数据，详情请参见[即席分析SQL建模]()。


