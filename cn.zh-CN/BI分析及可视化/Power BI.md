---
keyword: [Power BI, Hologres, 可视化分析]
---

# Power BI

本文为您介绍Power BI如何连接Hologres并可视化分析数据。

Power BI是微软知名的BI软件。Hologres兼容PostgreSQL，支持直接连接Power BI Desktop进行数据分析。若是业务有需要您也可以将Power BI Desktop的报表发布至Power BI Service或者Power BI Report Server查看分析结果。

## 注意事项

Power BI默认不同步Hologres中的外部表，您需要在连接Hologres后，在数据库中执行如下SQL命令，才可以在Power BI中同步Hologres的外部表信息。

```
CREATE EXTENSION foreign_table_exposer;
```

## Power BI Desktop连接Hologres

1.  安装Power BI Desktop

    安装Power BI Desktop，详请参见[Power BI Desktop官方文档](https://docs.microsoft.com/zh-cn/power-bi/fundamentals/desktop-getting-started)。

2.  连接 Hologres

    1.  打开Power BI Desktop，在页面上方单击**获取数据** \> **更多**。

    2.  在获取数据页面的**数据库**类别中，选择**PostgreSQL 数据库**。

        ![获取数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2007545161/p248521.png)

    3.  单击**连接**，在弹框中配置实例连接信息。

        ![获取数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243426.png)

        |参数|描述|
        |--|--|
        |服务器|Hologres实例的网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络地址。|
        |数据库|Hologres创建的数据库名称。|
        |数据连接模式|数据连接模式选择**DirectQuery**.|
        |高级选项|此处示例无需配置，保持默认值即可。您也可以根据实际业务需求进行精细化配置。|

    4.  单击**确定**，配置用户名和密码信息。

        |参数|描述|
        |--|--|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|

    5.  单击**连接**，会在导航器页面展示数据库中的表信息，您可以根据需要选择要加载到Power BI Desktop的表数据进行数据可视化分析。

        ![导航器](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2007545161/p248525.png)

    6.  单击下方的**加载**，将数据导入Power BI Desktop当中。

        将数据导入Power BI Desktop之后您可以对数据进行查询操作并创建报表或仪表盘进行分析。更多关于Power BI Desktop对数据操作和分析的介绍，请参见[Power BI官方文档](https://docs.microsoft.com/zh-cn/power-bi/)。


## 发布至Power BI Service

您可以将数据发布至Power BI Service查看分析结果。Power BI Service通过Power BI Gateway和本地服务器进行通信。在将Power BI Desktop的报表发布至Power BI Service之后您需要安装并配置Power BI Gateway。

1.  Power BI Desktop数据报表发布

    1.  打开Power BI Desktop的数据报表，在页面上方单击**发布**。

    2.  在发布到Power BI弹框页面，选择需要发布的工作区。

    3.  单击**选择**，Power BI Desktop会将报表发布到Power BI Service。发布成功后，您可以单击页面中的链接访问Power BI Service。

        ![发布](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243582.png)

2.  安装Power BI Gateway

    Power BI Service通过Power BI Gateway和本地服务器进行通信。安装Power BI Gateway，详请参见[Power BI官方文档](https://docs.microsoft.com/zh-cn/data-integration/gateway/service-gateway-install)。在安装时您需要注意如下事项：

    -   Power BI Gateway可以不用和Power BI Desktop 安装在同一台机器上。
    -   需要保证运行Power BI Gateway的服务器网络通畅且能访问 Hologres 数据源。
    -   必须安装Npgsql 4.0.10，请单击[下载Npgsql 4.0.10安装包](https://github.com/npgsql/npgsql/releases/download/v4.0.10/Npgsql-4.0.10.msi)。安装时请务必安装Npgsql GAC Installation，并且在安装完毕后务必重启 Power BI Gateway。

        ![Npgsql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3007545161/p248497.png)

3.  配置Power BI Gateway

    Power BI Gateway安装完成后，在运行网关的服务器上，启动Power BI Gateway后您可以通过双击 Power BI Gateway 图标进行配置。具体操作步骤，请参见[微软Gateway官方文档](https://docs.microsoft.com/zh-cn/data-integration/gateway/service-gateway-app)。

4.  配置Power BI Service数据源

    1.  登录Power BI Service，在页面右上角，单击![管理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243538.png)图标，在列表中选择**管理网关**。

    2.  选择目标网关，单击**添加数据源**，进行参数配置。

        ![添加数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243545.png)

        数据源配置信息需与Power BI Desktop的连接信息保持一致。具体参数说明如下：

        |参数|说明|
        |--|--|
        |数据源名称|为新数据源命名。|
        |数据源类型|在下拉框中选择**PostgreSQL数据库**。|
        |服务器|Hologres实例的网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络地址。|
        |数据库|Hologres创建的数据库名称。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |高级设置|在高级设置中，您需要配置如下内容：        -   此数据源的“连接加密”设置选择为**未加密**。
        -   此数据源的隐私级别设置可以为默认值。 |

    3.  单击**添加**，系统会开始测试联通性，完成测试之后会提示您数据源连接成功。

5.  配置报表网关

    将数据报表发布到Power BI Service后需要配置报表网关才能查看您的报表并进行交互式分析。

    1.  登录Power BI Service，在左侧导航栏单击进入我的工作区页面。

    2.  单击目标数据集后的![设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243612.png)图标，选择**设置**。

        ![设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3007545161/p248533.png)

    3.  在设置页面，单击**网关连接**并打开**使用网关连接**开关。选择对应的网关和映射的数据源。

        ![设置报表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5250524161/p243614.png)

    4.  单击**应用**，系统提示网关已经更新，则表示网关设置成功。

    完成上述设置，您可以在Power BI Service上查看您的报表并进行交互式分析。

    ![PBS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6250524161/p243615.png)


## 发布至Power BI Report Server

您可以将数据发布至Power BI Report Server查看分析结果。

1.  安装Power BI Report Server

    安装Power BI Report Server，详请参见[Power BI Report Server官方文档](https://docs.microsoft.com/zh-cn/power-bi/report-server/install-report-server)。在安装时您需要注意如下事项：

    -   需要保证运行Power BI Report Server的服务器网络通畅且能访问 Hologres 数据源。
    -   必须安装Npgsql 4.0.10，请单击[下载Npgsql 4.0.10安装包](https://github.com/npgsql/npgsql/releases/download/v4.0.10/Npgsql-4.0.10.msi)。安装时请务必安装Npgsql GAC Installation，并且在安装完毕后务必重启 Power BI 报表服务器。

        ![Npgsql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3007545161/p248497.png)

2.  Power BI Desktop数据发布

    1.  打开Power BI Desktop的数据报表，在页面上方选择**文件** \> **另存为** \> **Power BI报表服务器**。

        ![另存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3007545161/p248567.png)

    2.  在弹框页面，输入服务器地址后单击**确定**。

    3.  选择报表的存储位置，单击**确定**，Power BI Desktop会将报表发布到Power BI Report Server。发布成功后，您可以单击页面中的链接访问Power BI Report Server。

3.  配置Power Report Server数据源

    1.  登录Power Report Server，单击目标数据源报表右侧的![报表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6250524161/p243621.png)图标，在列表中选择**管理**。

        ![管理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3007545161/p248606.png)

    2.  在数据源页面进行参数配置。

        ![配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6250524161/p243622.png)

        数据源配置信息需与Power BI Desktop的连接信息保持一致。具体参数说明如下：

        |参数|说明|
        |--|--|
        |类型|在下拉框中选择**PostgreSQL**。|
        |连接字符串|自动生成，无需填写。|
        |身份验证类型|在下拉框中选择**基本身份验证**。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|

    3.  单击**测试连接**，系统会开始测试联通性，完成测试之后会提示您数据源连接成功。

    完成上述设置，您可以在Power Report Server上查看您的报表并进行交互式分析。

    ![PBRS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6250524161/p243624.png)


