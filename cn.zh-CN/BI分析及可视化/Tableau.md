---
keyword: [BI工具, 可视化分析, Tableau, Tableau Server]
---

# Tableau

本文为您介绍Tableau如何连接Hologres并可视化分析数据。

Tableau是安全并且灵活的端到端数据分析平台，提供从连接到协作的一整套功能。Hologres兼容PostgreSQL，支持直接连接Tableau并可视化分析数据。

## Tableau Desktop 连接 Hologres

1.  下载并安装Tableau。

    进入[Tableau官网](https://www.tableau.com/zh-cn/products)，根据业务需求下载相应的Tableau客户端，并根据提示安装。本次试验使用[Tableau Desktop](https://www.tableau.com/zh-cn/products/desktop)。

2.  连接Hologres。

    1.  成功安装客户端后，打开客户端。

    2.  在左侧导航栏的**连接** \> **到服务器**区域，选择**PostgreSQL**，配置连接Hologres的信息。

        ![Tableau](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1950420061/p166746.png)

        参数说明如下表所示。

        |参数|描述|
        |--|--|
        |服务器|Hologres实例的公共网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取公共网络地址。 |
        |端口|Hologres的实例端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。 |
        |数据库|Hologres创建的数据库名称。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**DB管理**获取数据库名称。 |
        |身份验证|选择**用户名和密码**。|
        |用户名|当前阿里云账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。 |
        |密码|当前阿里云账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。 |
        |需要SSL|不勾选。|

    3.  单击**登录**。

3.  Tableau可视化分析数据。

    使用Tableau成功连接Hologres后，您可以可视化分析已有的表数据，详情请参见[Tableau官网教程](https://www.tableau.com/zh-cn/learn/get-started)。


## 发布至Tableau Server

如果您需要将Tableau Desktop的报表发布至Tableau Server进行分析和管理，您可以按照如下步骤操作：

1.  下载并安装Tableau Server。

    进入[Tableau官网](https://www.tableau.com/zh-cn/products)，根据业务需求下载相应的Tableau Server客户端，并根据提示安装。

2.  通过Tableau Desktop访问Tableau Server。

    1.  打开Tableau Desktop客户端，在顶部菜单选择**服务器** \> **登录**。

    2.  在弹窗中输入您对应Tableau Server的地址之后，单击**连接**。

        ![Sign In](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0598091161/p236676.png)

    3.  在登录页面输入Tableau Server的用户名和密码，单击**登录**。

3.  将工作簿发布至 Tableau Server。

    1.  登录Tableau Server客户端后，在顶部菜单选择**服务器** \> **发布工作簿**。

    2.  在**发布工作簿**对话框中，配置如下参数信息。

        ![发布工作薄](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0598091161/p236693.png)

        |参数|说明|
        |--|--|
        |项目|选择目标项目名称。|
        |名称|为工作簿输入一个名称|
        |标记|在用户浏览服务器时，标记可帮助用户查找相关工作簿。使用逗号或空格分隔标记。|
        |权限|接受默认项目设置。|
        |数据源|Tableau Server支持两种数据源身份验证类型：        -   提示用户
        -   嵌入密码
选择嵌入式密码时，Tableau会将该报表与数据源的连接信息内嵌在报表中，并允许任何可以查看工作簿的用户查看工作簿。由于连接 Hologres时需要使用Access ID和Access Key，较为繁琐，所以更推荐使用嵌入式密码模式。|

    3.  参数配置完成后，单击**发布**。您就可以在Tableau Server中查看您发布的报表信息。


