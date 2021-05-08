---
keyword: [永洪, Hologres, BI工具, 数据分析]
---

# Yonghong BI

本文将为您介绍如何通过Yonghong BI工具连接Hologres并进行数据分析。

1.  下载并安装客户端。

    下载并安装永洪Yonghong BI客户端，请参见[Yonghong官方文档](https://www.yonghongtech.com/help/Z-Suite/8.5.1/ch/index.html)的安装产品章节。

2.  连接Hologres。

    1.  登录客户端，在左侧导航栏单击**添加数据源**，选择**POSTGRESQL**进入新建页面。

        ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8734540261/p271640.png)

    2.  在弹框中配置连接信息，并单击**测试连接**。如果提示测试成功，即该数据源成功连接到Hologres。

        ![参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271641.png)

        具体参数说明如下：

        |参数|说明|
        |--|--|
        |选择数据源|您可以选择**POSTGRESQL**。|
        |驱动|驱动选择`org.postgresql.Driver`。|
        |URL|数据连接URL，填写格式为`jdbc:postgresql://<Databaseserver>:<port>`，其中各参数解释如下所示：        -   Databaseserver：主机，Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。
        -   port：端口，Hologres实例的端口。 |
        |服务器登录|您可以选择**用户名和密码**方式登录。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |数据库|Hologres的数据库名称。|
        |表结构模式|输入数据库下的Schema名称。|

    3.  单击页面上方的**保存**，并输入文件名称。单击**确定**，保存该数据源。

        ![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271658.png)

3.  创建数据集。

    1.  登录客户端，在左侧导航栏单击**创建数据集**，在页面中选择**自服务数据集**进入新建页面。

        ![创建数据集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271660.png)

    2.  单击页面上方的**数据集**，在列表中单击目标数据源。将目标表拖拽到右侧空白画布区域，并将表和**数据集结果**相连。

        ![连接数数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271669.png)

    3.  单击页面上方的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271670.png)图标，并输入文件名称。单击**确定**，保存该数据集。

4.  制作报告。

    1.  登录客户端，在左侧导航栏单击**制作报告**，在页面中选择**新建报告**。在主题页面选择目标主题来创建报告。

    2.  选择主题后，打开编辑页面。在右侧组建中单击目标组件，将其拖拽到左侧报告编辑区域。

        ![组件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271697.png)

    3.  您可以对选定的组件选择一个数据集进行报告的制作。更多关于报告的操作指导，请参见[Yonghong官方文档](https://www.yonghongtech.com/help/Z-Suite/8.5.1/ch/index.html)的制作可视化报告章节。

        ![制作报告](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9734540261/p271698.png)


