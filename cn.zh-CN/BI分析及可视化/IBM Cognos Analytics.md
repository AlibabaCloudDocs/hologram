---
keyword: [IBM Cognos Analytics, Hologres, 可视化分析]
---

# IBM Cognos Analytics

IBM Cognos Analytics可以通过轻松地连接数据、处理数据并创建可视化效果，帮助您了助制定更有信心的决策。本文为您介绍IBM Cognos Analytics如何连接Hologres并可视化分析数据。

1.  安装IBM Cognos Analytics

    安装IBM Cognos Analytics，详请参见[IBM Cognos Analytics官方文档](https://www.ibm.com/docs/en/cognos-analytics/11.0.0?topic=started-getting-in-cognos-analytics)。

2.  连接Hologres

    1.  登录IBM Cognos Analytics，单击左下方的![manage](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1342088161/p264923.png)（Manage）图标，创建一个连接。

    2.  在页面上方单击**+**图标创建数据源连接，数据源类型选择PostgreSQL。

        ![创建数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1342088161/p264945.png)

    3.  在数据源类型选择PostgreSQL后，您需要在页面配置JDBC URL和Authentication method参数信息 。

        ![配置参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1342088161/p264959.png)

        |参数|说明|
        |--|--|
        |JDBC URL|JDBC的URL，填写格式为`jdbc:postgresql://<host>:<port>/<databasename>`，其中各参数解释如下所示：        -   host：Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。
        -   port：Hologres实例的端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。
        -   databasename：Hologres创建的数据库名称。 |
        |Driver class name|该参数值保持默认，不需要修改。|
        |Authentication method|选择Authentication method为**Use the following signon**。|

    4.  单击Authentication method下方的**+**图标增加认证信息，在页面中输入User ID和Password。

        ![用户名密码](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p264997.png)

        |参数|说明|
        |--|--|
        |User ID|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|

    5.  配置完成后，在页面左下方单击**Test**，如果页面下方提示Success，则表示测试成功。您可以在页面上方输入连接名称后，单击**Save**。

        ![测试](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265001.png)

    6.  连接配置保存完毕后，您可以单击进入Schemas页签，选择需要加载的Schema。单击目标Schema名称最右侧的显示更多图标，在弹框中选择目标参数**Load metadata**或**Load options**。

        ![schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265009.png)

        其中，**Load metadata**表示系统会加载该Schema下所有表的元数据。**Load options**表示您可以详细的设置数据信息。本文我们以**Load metadata**为例进行说明。

    7.  Schema信息加载完毕后，系统会提示相关内容已加载，如下图所示。数据加载完毕，表示您已经成功连接Hologres并进行数据可视化作业。

        ![loaded](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265014.png)

3.  创建Data module

    数据连接创建完成后，您可以创建Data module进行数据分析。

    1.  登录IBM Cognos Analytics，单击左下方的**+**图标，在列表中选择Data module。

        ![data module](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265216.png)

    2.  在Select sources页面左侧单击![data servers](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265217.png)图标。进入Data servers页签，单击之前创建的目标数据连接。

        ![选择数据集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265220.png)

    3.  在目标数据连接的详情页，系统会显示已经加载元数据的Schema，您可以选择目标Schema并在页面下方单击**OK**。

        ![添加schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265221.png)

    4.  在Add tables页面，选择**Select tables**并在页面下方单击**Next**。

    5.  在Select tables页面，勾选目标表的名称并单击**OK**。

        ![select tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265230.png)

    6.  在建模页面，单击Relationships进行表关系设置。

        ![relation](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265246.png)

    7.  在Create relationship页面，完成表关系设置后，单击**Save**保存该模型。

        ![表关系](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265271.png)

4.  模型创建完成后，您可以使用该模型进行可视化分析。更多关于IBM Cognos Analytics的操作指导，请参见[IBM Cognos Analytics官方文档](https://www.ibm.com/docs/en/cognos-analytics/11.0.0)。

    ![分析](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342088161/p265278.png)


