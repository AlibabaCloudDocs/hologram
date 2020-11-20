---
keyword: [HoloStudio, 批量创建外部表]
---

# 一键同步MaxCompute表结构

本文为您介绍如何使用HoloStudio批量创建外部表。

-   阿里云账号注册，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。
-   开通交互式分析Hologres并绑定至DataWorks工作空间，详情请参见[HoloStudio快速入门](/cn.zh-CN/基于HoloStudio的开发/HoloStudio快速入门.md)。

Hologres与MaxCompute在底层无缝连接，支持使用新建外部表的方式加速查询MaxCompute的数据，详情请参见[通过创建外部表加速查询MaxCompute数据](/cn.zh-CN/数据接入/大数据/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。

HoloStudio支持**一键MaxCompute表结构同步**功能，您可以使用可视化方式批量创建外部表。

您也可以使用`[IMPORT FOREIGN SCHEMA](/cn.zh-CN/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)`语句批量创建外部表。也可以将该命令语句在数据开发中执行，并前往调度，实现MaxCompute表新增后Hologres外表也自动新增，详情见[t1501233.md\#](/cn.zh-CN/基于HoloStudio的开发/数据开发/Hologres开发：周期性调度.md)。

**说明：** Hologres只能加速查询MaxCompute的内表，不能加速查询MaxCompute的外表和View。

1.  登录[阿里云官网](https://www.aliyun.com/)。

2.  新建**一键MaxCompute表结构同步**节点。

    1.  进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

    2.  鼠标悬停至顶部菜单栏左侧的![三角](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6312129951/p134229.png)图标，选择目标地域。

    3.  在**实例列表** \> **Hologres引擎管理**页面，单击**前往DataWorks-HoloStudio开发**。

    4.  在**Holo Studio**界面的左侧导航栏，单击![数据开发](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6312129951/p134237.png)图标。

    5.  进入**数据开发**页面，鼠标悬停至顶部菜单栏的![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6312129951/p134243.png)图标。

    6.  单击**一键MaxCompute表结构同步**。

    7.  在**新建节点**对话框中，输入**节点名称**，并选择**目标文件夹**和**数据库**。

    8.  单击**提交**。

3.  在编辑节点页面，配置各项参数。

    ![bia](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2212129951/p134363.png)

    |参数|描述|
    |--|--|
    |目标库|Hologres的数据库名称。|
    |目标Schema|当前数据库的Schema名称。如果您没有新建Schema，则只能选择默认创建的**public**。如果有新建的Schema，您也可以选择新建的Schema。 |
    |远程服务类型|默认为**odps**。目前仅支持MaxCompute。 |
    |远程服务器|您可以直接调用Hologres底层已创建的名为**odps\_server**的远程服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|
    |远程库|MaxCompute的项目名称。|
    |表名规则|您可以使用正则表达式选择需要同步的表名称，默认同步远程数据库中的所有表。更多筛选外部表的规则请参见[IMPORT FOREIGN SCHEMA](/cn.zh-CN/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。|
    |正则预览|查看正则表达式的运行结果。|

    同步外部表的规则如下：

    -   如果同步外部表时存在名称相同的表，则忽略当前表。
    -   如果同步外部表时，存在外部表不支持的数据类型，则系统会报错请在正则表达式中排除当前表再重新导入。
4.  在新建节点的编辑页面，单击![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2212129951/p134275.png)图标，保存配置信息。

5.  在新建节点的编辑页面，单击![运行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2212129951/p134276.png)图标，批量创建外部表结构。

6.  在**PG管理**页面，查看批量创建的外部表并查询表数据，详情请参见[创建及管理外部表](/cn.zh-CN/基于HoloStudio的开发/PG管理/创建及管理外部表.md)。


