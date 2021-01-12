---
keyword: [迁移Lightning至MaxCompute BI加速版, 最佳实践, Hologres]
---

# 迁移Lightning至共享集群（Quick BI场景）

共享集群（MaxCompute BI加速版）是针对MaxCompute交互式分析场景设计的在线查询加速服务，基于Hologres存储计算分离的云原生架构，以共享集群资源的形式，加快存储在MaxCompute中的数据访问。本文内容将指导您在Quick BI场景下如何将原MaxCompute Lightning服务迁移至Hologres共享集群（MaxCompute BI加速版）。

在开始本教程之前，请确保您已经开通并使用MaxCompute Lightning服务。

由于Lightning后期会逐步下线，下线后不再继续维护。您可以将共享集群（MaxCompute BI加速版）认为是Lightning的升级版，性能和服务更优于Lightning。更多BI工具也可以按照本文描述步骤进行数据源替换，Hologres支持的BI工具请参见[BI分析及可视化](/cn.zh-CN/BI分析及可视化/概述.md)。

## 操作流程说明

针对使用Quick BI可视化分析的场景，Lightning和共享集群（MaxCompute BI加速版）的操作流程对比如下图所示。

![迁移Lightning至共享集群（BI场景）](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0130419061/p203421.png)

因此，将Lightning业务迁移至Hologres共享集群（MaxCompute BI加速版）的操作流程具体如下：

-   [步骤一：开通共享集群（MaxCompute BI加速版）](#section_ive_vix_elw)
-   [步骤二：新建数据库](#section_w0q_vjs_9z9)
-   [步骤三：连接开发工具](#section_mui_ct0_cjp)
-   [步骤四：新建外部表加速查询MaxCompute](#section_kyc_yfy_ivf)
-   [步骤五：配置Quick BI一键替换数据源](#section_hku_izl_2c4)

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

    ![数据连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0057818061/p116500.png)

3.  配置新建连接对话框的参数，单击**确认**完成配置。具体参数说明请参见[新建连接参数说明](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/数据连接.md)。

    ![新建连接](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8413376061/p116502.png)


## 步骤四：新建外部表加速查询MaxCompute

实例成功连接HoloWeb后，您可以创建外部表，加速查询MaxCompute的数据。新创建的外部表需要与MaxCompute表一一映射，其字段顺序和字段类型也需要与MaxCompute表保持一致。在进行加速查询时，您可以选择加速查询MaxCompute表的部分字段，也可以加速查询全部字段。

如下内容以在HoloWeb中新建外部表查询MaxCompute公共数据集中public\_data项目的表数据为例，指导您进行加速查询的操作。获取表的方法请参见[公开数据集](https://yq.aliyun.com/articles/89763?spm=a2c4g.11186623.2.8.14d01099q4lXK2)。

**说明：**

-   为了便于后续实现Quick BI数据源一键迁移，建议您创建同Lightning表名称一致的外部表。
-   Hologres当前仅支持加速查询MaxCompute表，不支持加速查询MaxCompute的View或者其他类型的外部表。

1.  在HoloWeb开发界面，单击**连接管理** \> **MaxCompute加速** \> **批量创建外部表**，使用可视化的方式创建外部表。

2.  在批量创建外部表编辑页面，配置各项参数。具体参数说明请参见[批量创建外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/批量创建外部表.md)。批量创建的外部表默认和ODPS中的表名称和数量一致，您可以在创建完成后同Lightning表名称进行比对，手动修改不一致的外部表名称。

    ![表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1180579061/p120737.png)

    您也可以选择手动创建单张外部表，在HoloWeb开发界面，单击**连接管理** \> **MaxCompute加速** \> **新建外部表**，具体操作指导和参数说明请参见[外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/外部表.md)。


## 步骤五：配置Quick BI一键替换数据源

共享集群（MaxCompute BI加速版）兼容PostgreSQL生态，支持直接对接各种BI分析工具。若您是使用其他BI工具，需要将原Lightning的域名修改为共享集群的域名之后，再进行可视化分析。

如下内容以Quick BI为例，指导您进行数据源替换操作。Quick BI提供一键替换的功能，您可以通过Quick BI将原Lightning的数据源替换为Hologres数据源。替换完成后，原Lightning的仪表盘、报表等数据将会默认修改为Hologres数据源。

**说明：** 在执行此操作之前，请确保您创建的外部表名称同Lightning表名称一致。

1.  登录[Quick BI管理控制台](http://das.base.shuju.aliyun.com/console.htm?spm=a2c0j.8190895.737583.btn2.34f87d6a2MBlQ6)，在页面上方选择工作空间。

2.  在数据源页面，单击我的数据列表中目标数据源的**替换**按钮。

    ![替换数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2815458061/p203801.png)

3.  在替换数据源页面的云数据库页签，单击选择**Hologres**。

4.  配置替换为Hologres数据源对话框的的参数。

    ![替换为Hologres数据源参数配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5194458061/p203776.png)

    具体参数说明如下表所示：

    |参数|说明|
    |--|--|
    |显示名称|显示名称可以根据需求自定义。|
    |数据库地址|连接的Hologres的服务器地址。目前仅支持公网和内网，进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取。获取方式请参见。|
    |端口|连接的Hologres的端口地址。目前仅支持公网和内网，进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取。|
    |数据库|填写创建完成的目标数据库名称。|
    |Schema|默认为**public**，您也可以使用其他创建的Schema。|
    |用户名|当前账号的的Access ID，您也可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取AccessKey ID。|
    |密码|当前账号的Access Key，您也可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)获取AccessKey ID。|

5.  单击**确定**完成数据源切换。


## 迁移用户权限

共享集群（MaxCompute BI加速版）的权限控制方式与Lightning不同，因此在完成数据源替换之后，需要您重新给共享集群（MaxCompute BI加速版）用户授予相关权限。

为共享集群（MaxCompute BI加速版）用户授权的具体操作请参见[t1917293.dita\#task\_2569531](/cn.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。

