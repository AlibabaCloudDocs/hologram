---
keyword: [Quick BI, 可视化分析, Hologres]
---

# Quick BI

阿里云Quick BI为您提供海量数据的实时在线分析服务以及丰富的可视化效果，支持拖拽式操作。帮助您轻松地完成数据分析、业务数据探查及报表制作。本文为您介绍如何使用交互式分析Hologres连接Quick BI，并进行可视化分析。

-   开通Hologres，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。
-   开通Quick BI，详情请参见[Quick BI购买、升级、降级、续费、欠费]()。

1.  登录[Quick BI管理控制台](http://das.base.shuju.aliyun.com/console.htm?spm=a2c0j.8190895.737583.btn2.34f87d6a2MBlQ6)。

2.  添加数据源。

    1.  在Quick BI管理控制台页面，单击顶部菜单栏的**工作空间**。

    2.  在**工作空间**页面，单击左侧导航栏的**数据源**。

    3.  在**数据源**页面，单击右上角的**+新建数据源**。

    4.  选择**云数据库** \> **PostgreSQL**。

    1.  配置**添加PostgreSQL数据源**对话框的各项参数。

        ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4394147061/p167504.png)

        参数说明如下表所示。

        |参数|描述|
        |--|--|
        |显示名称|自定义的显示名称。|
        |数据库地址|连接的Hologres实例的公共网络地址。|
        |端口|连接的Hologres实例的公共网络端口。|
        |数据库|连接的Hologres实例的数据库名称。|
        |Schema|默认为**public**。|
        |用户名|当前账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
        |密码|当前账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
        |vpc数据源|不勾选|
        |SSL|不勾选|

        参数配置完成后，您可以单击**连接测试**查看连接情况，如果显示**数据源连通性正常**，则表明Hologres与Quick BI成功连接。

    2.  单击**确定**完成配置。

3.  可视化分析数据。

    成功连接数据源后，在**数据源** \> **我的数据源**页面，单击已创建的数据源，显示当前数据库中的所有表。您可以使用拖拽方式或即席分析SQL两种方式可视化分析数据。

    -   拖拽方式
        1.  单击目标表**操作**列的![创建数据集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3948430061/p167521.png)图标，创建数据集。
        2.  配置**创建数据集**对话框的**名称**和**位置**参数。
        3.  单击**确定**。
        4.  在**工作空间**页面左侧导航栏，单击**数据集**，显示所有可以可视化分析的表。
        5.  单击目标表**操作**列![编辑](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3948430061/p167525.png)图标，查看当前表数据。

            您也可以单击目标表**操作**列的![新建仪表盘](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167526.png)或![新建电子表格](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167527.png)，将表数据生成仪表盘或电子表格可视化展示。同时，也可以单击![更多](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4948430061/p167528.png)，执行更多操作。详情请参见[概述]()。

    -   即席分析SQL

        1.  在**数据源**页面，单击右上角的**即席分析SQL**，进入**即席分析SQL**页面。
        2.  您可以根据业务需求输入查询SQL语句，并单击**执行**。
        3.  获取需要查询的数据后，单击**创建数据集**。
        4.  配置**保存自定义SQL**对话框的**名称**、**位置**及**SQL**参数。
        5.  单击**确定**。
        成功创建数据集后，您可以在**数据集**中可视化分析表数据，详情请参见[即席分析SQL建模]()。


