---
keyword: [数据集成, Hologres, RDS MySQL]
---

# 离线同步RDS MySQL数据至Hologres

本文为您介绍如何通过DataWorks的数据集成模块，离线同步RDS MySQL数据至交互式分析Hologres。

-   开通DataWorks，详情请参见[入门概述]()。
-   开通云数据库RDS MySQL，详情请参见[快速入门](/intl.zh-CN/云数据库 RDS 快速入门/快速入门.md)。

**说明：** 跨地域是否可以同步数据，详情请参见[选择网络连通方案]()。

阿里云关系型数据库（Relational Database Service，简称RDS）是一种稳定、可靠及可弹性伸缩的在线数据库，为您提供容灾、备份、恢复和迁移等全套解决方案。

Hologres是实时交互式分析产品，与大数据生态及智能研发平台DataWorks深度集成。您可以通过DataWorks的数据集成模块，离线同步RDS MySQL的数据至Hologres，并使用Hologres查询分析。

1.  准备RDS MySQL数据。

    创建一张RDS MySQL表并导入数据。示例SQL语句如下。

    ```
    CREATE TABLE `rds_test` (
        `id` int(11) NULL COMMENT '编号',
        `name` text NULL COMMENT '姓名',
        `r_time` datetime NULL COMMENT '时间',
        `address` text NULL COMMENT '地址',
        `salary` double NULL COMMENT '薪资'
    ) ENGINE=InnoDB
    DEFAULT CHARACTER SET=utf8 COLLATE=utf8_general_ci;
    ```

    您也可以选择已创建的RDS MySQL表。

2.  配置Hologres数据源。

    1.  登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?#/)。

    2.  在左侧导航栏，单击**工作空间列表**。

    3.  选择工作空间所在地域后，单击相应工作空间后的**进入数据集成**。

    4.  在左侧导航栏，单击**数据源**。

    5.  在**数据源管理**页面，单击顶部菜单栏的**新增数据源**。

    6.  在**大数据存储**区域，选择**Hologres**。

    7.  配置**新增Hologres数据源**对话框的各项参数。

        ![新增数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1096705061/p139621.png)

        |参数|描述|
        |--|--|
        |数据源类型|目前仅支持**阿里云实例模式**。|
        |数据源名称|数据源名称必须是字母、数字和下划线的组合，并且以字母开头。|
        |数据源描述|数据源的信息描述，不得超过80个字符。|
        |适用环境|        -   **开发**
        -   **生产**
**说明：** 仅标准模式的实例涉及配置该参数。 |
        |实例ID|需要同步的Hologres实例ID。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，获取实例ID。 |
        |数据库名|Hologres的数据库名称。|
        |AccessKey ID|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |AccessKey Secret|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|

        **说明：** Hologres数据源仅支持独享数据集成资源组。

3.  配置RDS MySQL数据源。

    1.  在**数据源管理**页面，单击顶部菜单栏的**新增数据源**。

    2.  在**关系型数据库**区域，选择**MySQL**。

    3.  配置**新增MySQL数据源**对话框的各项参数。

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p161226.png)

        |参数|描述|
        |--|--|
        |数据源类型|        -   **阿里云实例模式**
        -   **连接串模式** |
        |数据源名称|数据源名称必须是字母、数字和下划线组合，并且以字母开头。|
        |数据源描述|数据源的信息描述，不得超过80个字符。|
        |适用环境|        -   **开发**
        -   **生产**
**说明：** 仅标准模式的DataWorks工作空间涉及配置该参数。 |
        |地区|选择开通RDS实例的地域。|
        |RDS实例ID|需要同步的RDS实例ID。您可以进入[RDS管理控制台](https://rdsnext.console.aliyun.com/#/rdsList/cn-shanghai/basic/)，获取实例ID。 |
        |RDS实例主账号ID|购买实例的账号ID。您可以进入[安全设置](https://account.console.aliyun.com/?spm=a1z3jh.11711356.0.0.72765dbadasqzc#/secure)查看账号ID。 |
        |数据库名|RDS MySQL的数据库名称。|
        |用户名|登录数据库的用户名称。|
        |密码|登录数据库的用户密码。|

4.  新建离线同步任务。

    1.  在**工作管理空间**界面，单击顶部菜单栏左侧的![图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p139848.png)图标。

    2.  单击**数据集成**。

    3.  在**数据集成**页面，单击**新建离线同步**。

    4.  配置**新建节点**对话框的**节点类型**、**节点名称**和**目标文件夹**参数。

        ![参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p139854.png)

        如果您没有已创建的**业务流程**，则配置**新建节点**之前，您需要先新建**业务流程**，详情请参见[创建业务流程]()。

5.  配置离线同步任务。

    1.  进入离线同步任务的编辑页面，配置**选择数据源**区域的各项参数。

        ![数据源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p140870.png)

        |类别|参数|描述|是否必选|
        |--|--|--|----|
        |数据来源|数据源|需要同步的数据源类型及名称。本次试验同步的数据源类型为MySQL。

|是|
        |表|需要同步数据的表名称。|是|
        |数据过滤|同步数据时需要设置的筛选条件。暂不支持使用`LIMIT`关键字进行过滤。

不同数据源的SQL语法不同，详情请参见[调度参数]()。

|否|
        |切分键|您可以设置源数据表中某一列作为切分键。建议使用主键或包含索引的列作为切分键。

|否|
        |数据去向|数据源|接收源数据的数据源类型及名称。本次试验接收数据源的数据源类型为Hologres。

|是|
        |表|接收数据的表名称。单击**一键生成目标表**，在Hologres中自动创建一张用于接收同步数据的表。您可以根据业务情况修改建表SQL语句，注意保持字段类型一一对应。

您也可以提前在Hologres中创建用于接收数据的表。

|是|
        |写入模式|        -   **SDK（极速写入）**：通过Hologres的实时数据API写入数据，详情请参见[实时数据API](/intl.zh-CN/数据接入/实时写入/实时计算Flink版/Blink独享集群（原产品线）/实时数据API.md)。

**说明：** 如果使用**SDK（极速写入）**写入模式同步数据，则必须使用DataWorks的独享数据集成资源组。

        -   **SQL（INSERT INTO）**：通过PostgreSQL的`INSERT INTO`语句写入数据。
|是|
        |写入冲突策略|        -   **更新（Replace）**：如果写入的新数据与原有的旧数据产生冲突，则使用新数据覆盖旧数据。
        -   **忽略（Ignore）**：如果写入的新数据与原有的旧数据产生冲突，则忽略新数据。
**说明：** **写入冲突策略**仅适用于有主键的表。

|是|

    2.  在**字段映射**区域，您可以选择同步部分或全部字段。

    3.  在**通道控制**区域，配置各项参数。

        ![通道控制](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7335470061/p140955.png)

        |参数|描述|是否必选|
        |--|--|----|
        |任务期望最大并发数|在离线同步任务中，从数据来源端并行读取或写入数据，并存储至数据去向端的最大线程数。|否|
        |同步速率|        -   **限流**：用于保护数据来源端或者数据去向端的读写压力。
        -   **不限流**：系统会提供现有硬件环境下最大的传输性能。
建议您选择**限流**，并根据同步任务合理配置同步速率。

|是|
        |错误记录数|表示脏数据的最大容忍条数。|否|

    4.  配置独享数据集成资源组。

        1.  在任务编辑页面，单击右侧导航栏的**数据集成资源组配置**。
        2.  在**数据集成资源组配置**对话框，**方案**选择**独享数据集成资源组**，**独享数据集成资源组**选择已创建的独享资源组。您也可以单击**新建独享资源组**，新建独享资源组。

            **说明：**

            1.  **独享资源组**必须配置为**独享数据集成资源组**。
            2.  同步RDS MySQL数据时，必须为独享资源组配置专有网络，详情请参见[独享资源组模式]()。
            3.  独享资源组的可用区必须与RDS MySQL专有网络的可用区一致。
    5.  在任务编辑页面，单击顶部菜单栏的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140970.png)图标，保存作业。

    6.  在任务编辑页面，单击顶部菜单栏的![运行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140974.png)图标，运行作业，同步数据。

6.  在Hologres中查看已同步的数据。

    示例SQL语句如下。

    ```
    SELECT * FROM rds_test;
    ```


