---
keyword: [数据服务, Hologres, API, BI工具]
---

# DataWorks数据服务

本文为您介绍交互式分析Hologres如何连接DataWorks数据服务并生成API。

-   开通DataWorks，详情请参见[入门概述]()。
-   开通Hologres实例，并绑定至DataWorks工作空间，详情请参见[HoloStudio快速入门](/cn.zh-CN/基于HoloStudio的开发/HoloStudio快速入门.md)。

DataWorks数据服务旨在为您搭建统一的数据服务总线，支持快速将数据表生成数据API，并注册现有API至数据服务平台，帮助您统一管理和发布API服务。

Hologres与DataWorks深度集成，支持直接对接DataWorks数据服务，帮助您快速将查询的数据生成API，并提供对外服务。

1.  配置Hologres数据源。

    1.  登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?#/)。

    2.  在左侧导航栏，单击**工作空间列表**。

    3.  选择工作空间所在地域后，单击相应工作空间后的**进入数据集成**。

    4.  在左侧导航栏，单击**数据源**。

    5.  在**数据源管理**页面，单击顶部菜单栏的**新增数据源**。

    6.  在**大数据存储**区域，选择**Hologres**。

    7.  配置**新增Hologres数据源**对话框的各项参数。

        ![新增数据源](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4560129951/p139621.png)

        |参数|描述|
        |--|--|
        |数据源类型|目前仅支持**阿里云实例模式**。|
        |数据源名称|数据源名称必须是字母、数字和下划线的组合，并且以字母开头。|
        |数据源描述|数据源的信息描述，不得超过80个字符。|
        |适用环境|        -   **开发**
        -   **生产**
**说明：** 如果使用的是标准模式的DataWorks工作空间，则需要配置该参数。 |
        |实例ID|需要同步的Hologres实例ID。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，获取实例ID。 |
        |数据库名|Hologres的数据库名称。|
        |AccessKey ID|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |AccessKey Secret|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|
        |资源组连通性|选择**数据服务**。您需要保证公共资源组和数据源是可以联通的。 |

2.  生成API。

    1.  在**DataWorks**开发界面，单击顶部菜单栏左侧的![图标](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4560129951/p139848.png)图标。

    2.  鼠标悬停至**全部产品**，在**数据开发**区域，单击**数据服务**。

    3.  在**服务开发**页面，鼠标悬停至![新建](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1771348951/p140098.png)图标。

    4.  鼠标悬停至**生成API**，您可以选择**向导模式**或**脚本模式**生成API。

        ![生成API](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/0245470061/p140103.png)

        使用**向导模式**生成API，详情请参见[通过向导模式生成API]()。

        使用**脚本模式**生成API，详情请参见[通过脚本模式生成API]()。

3.  测试API。

    1.  在API编辑页面，单击顶部菜单栏右侧的**测试**。

    2.  在**API测试**对话框，输入**请求参数**，单击**开始测试**。

        ![测试](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/2771348951/p140167.png)

        如果**API测试**对话框页面底部显示**测试成功**，则表示API测试通过。

        您也可以使用数据服务的测试API模块来完成测试，详情请参见[测试API]()。

4.  发布并查看API。

    1.  在API编辑页面，单击顶部菜单栏右侧的**发布**。

        发布API至API网关，并上架至API市场，详情请参见[发布API]()。

    2.  在**数据服务**页面，单击顶部菜单栏右侧的**服务管理**。

    3.  单击已发布的API名称，查看API详情。

5.  调用API。

    如果您需要调用已成功发布的API，请参见[客户端调用API示例]()。


