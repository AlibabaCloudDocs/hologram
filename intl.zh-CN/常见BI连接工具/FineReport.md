---
keyword: [FineReport, Hologres, BI工具, 可视化分析]
---

# FineReport

本文为您介绍交互式分析Hologres如何连接FineReport并可视化分析数据。

-   开通Hologres实例，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。
-   进入[帆软官网](http://www.fanruan.com/)，根据项目需求选择**解决方案**并下载客户端工具。本次示例采用[FineReport报表](http://www.fanruan.com/finereport)。

FineReport是一款专业、灵活及使用简捷的企业级Web报表软件产品，支持使用简单的拖拽方式设计出复杂的中国式报表、参数查询报表、填报表及驾驶舱，轻松搭建数据决策分析系统。

1.  打开FineReport客户端，在界面顶部菜单栏选择**服务器** \> **定义数据连接** 。

2.  配置**定义数据连接**对话框的各项参数。

    参数说明如下表所示。

    |参数|描述|示例|
    |--|--|--|
    |数据库|Hologres实例的数据库名称。

|testdb|
    |驱动器|使用FineReport自带的`org.postgresql.Driver`。FineReport添加数据库后会自动生成驱动器。

|无|
    |URL|Hologres的公共网络地址`jdbc:postgresql://localhost:port/dbname`。|`jdbc:postgresql://demo-cn-hangzhou.hologres.aliyuncs.com:80/postgres`|
    |用户名|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|无|
    |密码|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|无|

3.  单击**测试连接**，如果弹出的对话框提示**连接成功**，则表明Hologres与FineReport已成功连接。

    **说明：** ：如果提示连接失败，请先确认是否已安装PostgreSQL驱动，详细操作请参见[官网驱动安装](https://help.finereport.com/doc-view-2563.html)。同时，请您安装42.2.18及以上的JDBC版本。

4.  单击**确定**，完成配置。

5.  可视化分析数据。

    Hologres成功连接FineReport客户端后，即可进行可视化数据分析。具体操作步骤请参见[FineReport官网文档](https://www.fanruan.com/)。

    **说明：** FineReport支持查询Hologres的外部表，并进行可视化分析，但是在新增数据集时不会显示外部表，您需要使用SQL语句进行查询。

    示例使用Hologres的一张外部表数据制作柱状图，步骤如下：

    1.  打开FineReport客户端，在界面顶部菜单栏单击**文件** \> **新建决策报表（F）**，选择一张柱状图，如下图所示。

    2.  在左侧导航栏**模版数据集**新建**数据库查询**。

    3.  在**数据库查询**对话框，配置查询数据的SQL语句，并单击**确定**。示例查询Hologres外部表的其中200条数据，如下图所示。

        **说明：** 数据库查询界面的左侧菜单栏不会显示当前Hologres数据源的外部表，您可以选择在右侧编辑框中直接查询外部表。

    4.  配置完**数据库查询**后，返回图表界面。您可以根据展示要求配置图表数据。

        示例在图表界面右侧的**数据**栏中，为图表配置横纵坐标的数据信息（本示例横坐标为job数据，纵坐标为age数据），您可以根据需求设置图表的**样式**及**特效**。

    5.  配置完图表数据，单击顶部菜单栏左侧![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0154560061/p168552.png)按钮，保存图表数据。

    6.  单击![预览](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0154560061/p168553.png)按钮，查看当前数据配置的图表。

        使用Hologres外部表制作的图表展示如下。关于FineReport的更多操作，请参见[帆软官网](https://www.fanruan.com/)。


