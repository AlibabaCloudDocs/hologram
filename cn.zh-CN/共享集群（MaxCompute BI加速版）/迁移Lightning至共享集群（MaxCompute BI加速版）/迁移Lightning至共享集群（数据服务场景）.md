---
keyword: [迁移Lightning至MaxCompute BI加速版, 最佳实践, Hologres]
---

# 迁移Lightning至共享集群（数据服务场景）

共享集群（MaxCompute BI加速版）是针对MaxCompute交互式分析场景设计的在线查询加速服务，基于Hologres存储计算分离的云原生架构，以共享集群资源的形式，加快存储在MaxCompute中的数据访问。本文内容将指导您在使用Lightning集群的前提下，如何使用Hologres共享集群（MaxCompute BI加速版）对接DataWorks数据服务。

由于Lightning后期会逐步下线，下线后不再继续维护。您可以将共享集群（MaxCompute BI加速版）认为是Lightning的升级版，性能和服务更优于Lightning。逐步使用共享集群（MaxCompute BI加速版）替换Lightning。

## 操作流程说明

针对使用数据服务场景的操作流程，Lightning和共享集群（MaxCompute BI加速版）的操作流程对比如下图所示。

![迁移Lightning服务至Hologres共享集群（数据服务场景）](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1641189061/p208681.png)

因此，在数据服务场景下，使用Hologres共享集群（MaxCompute BI加速版）对接DataWorks数据服务的操作流程具体如下：

-   开通共享集群（MaxCompute BI加速版）并确认开通实例的详细信息。
-   新建数据库并进行授权。
-   连接开发工具并将原开发工具上的Lightning集群的域名信息修改为共享集群的域名信息。
-   新建外部表加速查询MaxCompute共享集群（MaxCompute BI）加速版。
-   在数据服务新建Hologres数据源，重新创建数据服务任务。

## 步骤一：开通共享集群（MaxCompute BI加速版）

在迁移Lightning服务之前，需要您先开通共享集群（MaxCompute BI加速版），然后在管理控制台查看自己的共享集群实例是否开通成功。

1.  单击[购买](https://common-buy.aliyun.com/?spm=5176.19600405.J_5253785160.6.26f84c60Y15NCq&regionId=cn-hangzhou&commodityCode=hologram_maxcomputeAccelerate_public_cn)，进入交互式分析Hologres共享集群（MaxCompute BI加速版）购买页面进行如下配置。

    -   **商品类型**：选择**共享集群（MaxCompute BI加速版）**。
    -   **地域**：请根据业务实际情况进行选择。
    -   **实例名称**：支持长度为2~64个字符的名称。
2.  配置完成后，单击**立即购买**进行订单确认。

3.  勾选我已阅读并同意交互式分析Hologres共享集群（MaxCompute BI加速版）服务协议，单击**立即开通**。

    订购开通交互式分析Hologres服务实例，一般需要5-10分钟，请您耐心等待。

4.  购买成功后，进入[Hologres的管理控制台](https://hologram.console.aliyun.com/#/instance)。

5.  在**Hologres引擎管理**页面，单击目标实例名称。

    您也可以单击目标实例**操作**列的**管理**，进入实例详情页，查看实例的详细信息。

    ![实例详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7901358061/p184909.png)


## 步骤二：新建数据库

成功创建实例后，系统默认生成一个名为**postgres**的数据库，用于监控管理。实际业务需要您按照如下操作指导新建数据库。

1.  进入实例详情页左侧导航栏的DB管理页面，单击**新增Database**。

2.  在新增Database对话框，输入**Database名称**，并根据实际业务选择是否开启简单权限模型。创建数据库时，为了方便权限管理，建议您选择**开启**简单权限模型。

    ![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4194458061/p136042.png)

    Hologres为您提供了专家模式授权和简单权限模型两套授权体系。

    -   专家模式授权：与PostgreSQL的权限模型完全一致，简称专家模式，详请参见[专家权限模型](/cn.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)。
    -   简单权限模型：Hologres基于实际业务，为了简化授权操作而抽象的一套简单权限模型（SPM），详请参见[简单权限模型概述](/cn.zh-CN/账号与权限管理/Hologres权限模型/简单权限模型/简单权限模型概述.md)。
3.  单击**确定**完成数据库创建，您可以在DB管理页面，查看已创建的数据库。


## 步骤三：连接开发工具

新建数据库后，您需要使用共享集群（MaxCompute BI加速版）实例连接原有的开发工具进行数据开发。如下内容以Hologres的自研开发工具HoloWeb为例，指导您使用共享集群（MaxCompute BI加速版）实例连接开发工具。

**说明：** 连接原有的开发工具时，需要将开发工具里原Lightning的域名修改为对应共享集群（MaxCompute BI加速版）的域名信息。您可以在**Hologres管理控制台** \> **Hologres引擎管理**页面，单击实例名称，进入实例详情页的网络配置模块获取共享集群（MaxCompute BI加速版）实例的域名信息。

1.  在Hologres管理控制台的Hologres引擎管理页面，单击**登录Hologres数据库**，进入HoloWeb开发界面。

2.  单击**连接管理** \> **数据连接**。

    ![数据连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1570812161/p116500.png)

3.  配置新建连接对话框的参数，单击**确认**完成配置。具体参数说明请参见[新建连接参数说明](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/数据连接.md)。

    ![新建连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8413376061/p116502.png)


## 步骤四：新建外部表加速查询MaxCompute

成功连接HoloWeb后，您可以创建外部表，加速查询MaxCompute的数据。新创建的外部表需要与MaxCompute表一一映射，其字段顺序和字段类型也需要与MaxCompute表保持一致。在进行加速查询时，您可以选择加速查询MaxCompute表的部分字段，也可以加速查询全部字段。

如下内容以在HoloWeb中新建外部表查询MaxCompute公共数据集中public\_data项目的表数据为例，指导您进行加速查询的操作。

获取表的方法请参见[公开数据集](https://yq.aliyun.com/articles/89763?spm=a2c4g.11186623.2.8.14d01099q4lXK2)。

**说明：** Hologres当前仅支持加速查询MaxCompute表，不支持加速查询MaxCompute的View或者其他类型的外部表。

1.  在HoloWeb开发界面，单击**连接管理** \> **MaxCompute加速** \> **创建外部表**，使用可视化的方式创建外部表。

2.  在新建外部表编辑页面，配置各项参数。具体参数说明请参见[新建外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/外部表.md)。

    ![表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1180579061/p120737.png)

3.  输入MaxCompute表的名称，就可以索引出表的字段，您可以根据实际业务，选择需要同步的表字段，单击**提交表**。

    如果您需要加速查询的外部表较多，则可以新建一个Query查询窗口，使用如下示例语句批量创建外部表。

    其中，创建Query查询窗口请参见[SQL窗口](/cn.zh-CN/连接开发工具/HoloWeb/SQL编辑器/SQL窗口.md) 。更多关于批量创建外部表的语句说明，请参见[IMPORT FOREIGN SCHEMA](/cn.zh-CN/Hologres SQL/DDL/SCHEMA/IMPORT FOREIGN SCHEMA.md)。

    ```
    IMPORT FOREIGN SCHEMA public_data LIMIT to(
      customer,
      customer_address,
      customer_demographics,
      inventory,item,
      date_dim,
      warehouse) 
      FROM server odps_server INTO PUBLIC options(if_table_exist 'update');
    ```


## 步骤五：对接DataWorks的数据服务

共享集群（MaxCompute BI加速版）兼容PostgreSQL生态，支持直接对接各种BI分析工具。若您是使用其他BI工具，需要将原Lightning的域名修改为共享集群的域名之后，再进行可视化分析。

如下内容以DataWorks的数据服务为例，指导您进行BI工具的连接。

1.  配置Hologres数据源。

    1.  登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?#/)，在页面上方选择目标区域后，在左侧导航栏单击进入工作空间列表页面。

    2.  单击目标工作空间操作的进入数据集成按钮。

    3.  在数据集成首页的数据源页签，单击进入数据源管理页面。

    4.  单击顶部菜单栏的**新增数据源**，在**大数据存储**区域，选择**Hologres**。

    5.  配置新增Hologres数据源对话框的参数。

        ![新增数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1096705061/p139621.png)

        具体参数说明如下表所示：

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

2.  生成并配置API。

    1.  登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?#/)，在页面上方选择目标区域后，在左侧导航栏单击进入工作空间列表页面。

    2.  单击目标工作空间操作的**进入数据开发**按钮。

    3.  在数据开发页面，单击顶部菜单栏左侧的![图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p139848.png)图标，选择**全部产品** \> **数据开发** \> **数据服务**。

    4.  在**服务开发**页面，单击顶部菜单栏的![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1771348951/p140098.png)图标，选择**API** \> **生成API**。

        ![生成API](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1096705061/p140103.png)

        您可以选择**向导模式**或**脚本模式**生成API。

        -   使用**向导模式**生成并配置API，详请参见[通过向导模式生成API]()。
        -   使用**脚本模式**生成并配置API，详请参见[通过脚本模式生成API]()。
3.  测试API。

    1.  生成API后，在右侧菜单栏依次进行属性、请求参数和返回参数的配置并保存。

    2.  在API编辑页面，单击顶部菜单栏右侧的**测试**。

    3.  在**API测试**对话框，检查输入的**请求参数**，单击**开始测试**。

        ![测试](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2771348951/p140167.png)

        如果**API测试**对话框页面底部显示**测试成功**，则表示API测试通过。您也可以使用数据服务的测试API模块来完成测试，详情请参见[测试API]()。

4.  发布并查看API。

    API测试成功之后，在API编辑页面，单击顶部菜单栏右侧的**发布**。将API发布至API网关，并上架至API市场，详请参见[发布API]()。您也可以在**数据服务**页面，单击顶部菜单栏右侧的**服务管理**，查看已发布的API名称，查看API详情。

5.  调用API。

    如果您需要调用已成功发布的API，请参见[客户端调用API示例]()。


## 迁移用户权限

共享集群（MaxCompute BI加速版）的权限控制方式与Lightning不同，需要您重新给共享集群（MaxCompute BI加速版）用户授予相关权限。

为共享集群（MaxCompute BI加速版）用户授权的具体操作请参见[t1917293.dita\#task\_2569531](/cn.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。

