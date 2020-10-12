---
keyword: [MySQL Binlog, 同步数据, Hologres]
---

# 实时同步MySQL Binlog的数据至Hologres

本文为您介绍如何通过DataWorks数据集成实时同步MySQL Binlog的数据至Hologres。

-   开通DataWorks，详情请参见[入门概述]()。
-   开通Hologres实例并绑定至DataWorks工作空间，详情请参见[HoloStudio快速入门](/cn.zh-CN/基于HoloStudio的开发/HoloStudio快速入门.md)。
-   开通云数据库RDS MySQL，详情请参见[快速入门](/cn.zh-CN/云数据库 RDS 快速入门/快速入门.md)。

**说明：** 跨地域是否可以同步数据，详情请参见[数据源测试连通性]()。

Hologres是实时交互式分析产品，与大数据生态无缝打通，深度集成智能研发平台DataWorks，支持高并发和低延时地查询分析数据。

MySQL Binlog输入基于Binlog的实时订阅功能实时读取MySQL数据库的表数据。您可以使用DataWorks数据集成实时同步MySQL Binlog的数据至Hologres，详细原理请参见[Hologres Writer]()。

MySQL Binlog输入支持V5.1、V5.5、V5.6、V5.7和V8.0版本。

## 操作步骤

1.  准备MySQL数据源。

    您需要在RDS中创建一张表并导入数据，详情请参见[快速入门](/cn.zh-CN/云数据库 RDS 快速入门/快速入门.md)。

    本次试验选用已创建的RDS表。

    示例建表SQL语句如下。

    ```
    CREATE TABLE `holo_test` (
      `id` int NULL, 
      `name` text NULL, 
      `r_time` timestamp NULL, 
      `address` text NULL, 
      `salary` double NULL ) 
      ENGINE=InnoDB DEFAULT CHARACTER SET=utf8;
    ```

2.  配置MySQL数据源。

    1.  登录[DataWorks控制台](https://workbench.data.aliyun.com/console)，单击左侧导航栏的**工作空间列表**。

    2.  单击所选工作空间名称**操作**列的**进入数据集成**。

    3.  单击**数据集成**页面的**数据源管理**。

    4.  单击**新增数据源**。

    5.  选择**实时**类别中的**MySQL Binlog**，并配置**新增MySQL Binlog数据源**弹框的参数。

        ![new](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4074827951/p129499.png)

        |参数名称|描述|
        |----|--|
        |数据源类型|选择**阿里云实例模式**。|
        |数据源名称|数据源名称为字母、数字和下划线的组合，且必须以字母开头。|
        |数据源描述|数据源的描述说明，不得超过80个字符。|
        |适用环境|包括**开发**和**生产**两种环境。 **说明：** 仅标准模式的工作空间会显示该配置。 |
        |地区|选择数据源所在的地域。|
        |RDS实例ID|进入[RDS管理控制台](https://rdsnext.console.aliyun.com/?spm=5176.10695662.782131.4.184f73c2rEdse2#/rdsList/cn-hangzhou/basic/)的，获取**实例ID**。|
        |RDS实例主账号ID|登录[DataWorks控制台](https://workbench.data.aliyun.com/console)，鼠标悬停至顶部菜单栏右侧的用户头像，单击**安全设置**，获取账号ID。|
        |数据库名|提供数据源的数据库名称。|
        |用户名|MySQL数据库的用户名称。|
        |密码|MySQL数据库的用户密码。|

3.  配置Hologres数据源。

    成功配置MySQL数据源后，您需要在**数据集成**页面配置Hologres数据源。

    1.  单击**数据源管理**。

    2.  单击**新增数据源**。

    3.  选择**Hologres**，并配置**新增Hologres数据源**弹框的参数。

        ![新建hologres数据源](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4074827951/p129514.png)

        |参数名称|描述|
        |----|--|
        |数据源类型|目前仅支持**阿里云实例模式**。|
        |数据源名称|数据源名称为字母、数字和下划线的组合，且必须以字母开头。|
        |数据源描述|数据源的描述说明，不得超过80个字符。|
        |适用环境|包括**开发**和**生产**两种环境。 **说明：** 仅标准模式的工作空间会显示该配置。 |
        |实例ID|需要同步数据的Hologres实例ID。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，获取实例ID。|
        |数据库名|提供数据源的数据库名称。|
        |AccessKey ID|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |AccessKey Secret|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|

4.  配置同步任务。

    成功配置数据源后，您需要配置实时同步任务，同步MySQL的数据至Hologres。具体步骤如下：

    1.  新建实时同步节点。

        1.  在**数据集成**页面单击**新建同步任务**。
        2.  配置**新建节点**弹框的**节点类型**、**节点名称**和**目标文件夹**参数。
        3.  单击**提交**。
    2.  配置**输入**。

        1.  进入实时同步节点的编辑页面，**输入**选择**MySQL Binlog**。
        2.  单击**MySQL Binlog\_1**，配置**节点配置**对话框中的各项参数。

            ![输入](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5074827951/p129516.png)

            |参数|描述|
            |--|--|
            |**数据源**|选择已经配置好的MySQL Binlog数据源。|
            |**表**|选择当前数据源下需要同步数据的表名称。您可以单击右侧的**数据预览**进行确认。|
            |**输出字段**|选择需要同步的字段。|

    3.  配置**输出**。

        1.  **输出**选择**Hologres**。
        2.  单击**Hologres\_1**，配置**节点配置**对话框中的各项参数。

            ![输出](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5074827951/p129518.png)

            |参数|描述|
            |--|--|
            |数据源|选择已经配置好的Hologres数据源。|
            |表|选择Hologres用于接收数据的表。 您可以单击右侧的**一键建表**新建数据表，也可以单击**数据预览**进行确认。 |
            |作业类型|包括**重放**和**插入**两种类型：             -   **重放**：表示镜像功能，即源端执行一次插入、更新或删除操作，Hologres会执行相应的操作。
            -   **插入**：表示将Hologres作为流存储，通过插入操作保存源端同步的数据。 |
            |写入冲突策略|包括**覆盖**和**忽略**两种类型：             -   **覆盖**：使用源端同步过来的新数据覆盖已有的数据。
            -   **忽略**：忽略源端同步过来的新数据，保留已有的数据。 |
            |字段映射|单击**字段映射**，设置源端和目标端字段的映射。同步任务会根据字段的映射关系同步数据。|

    4.  配置**基本配置**。

        成功配置**输入**和**输出**后，单击右侧导航栏的**基本配置**，配置任务的**资源组**。

        **说明：** 实时同步任务仅支持在**独享数据集成资源**上运行。

        ![基本配置](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5074827951/p129520.png)

    5.  提交任务。

        1.  成功填写任务后，单击工具栏中的![提交](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7190659951/p98393.png)图标。
        2.  在**提交**对话框，选中需要提交的节点和**忽略输入输出不一致的告警**，输入**备注**。
        3.  单击**提交**。

            如果您使用的是标准模式的工作空间，提交成功后，请单击右上角的**发布**。具体操作请参见[发布管理]()。

    6.  启动任务。

        1.  提交或发布节点成功后，单击右上角的**运维**。
        2.  在**运维中心**的左侧导航栏，单击**实时任务运维** \> **实时同步任务**。
        3.  在**实时同步任务**页面，单击相应**任务名称**后的**启动**，启动任务。

            您可以根据需求配置**重置位点**，为业务设定同步数据的时间。

            ![提交](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5074827951/p129522.png)

5.  Hologres查询数据。

    启动任务后，您可以在Holostudio中实时查询数据。示例查询语句如下。

    ```
    SELECT COUNT (*)FROM public.holo_test_2;
    ```

    ![结果](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5074827951/p129523.png)


## 实时同步任务支持的数据源

DataWorks支持实时同步多种异构数据源，详情请参见[实时同步支持的数据源]()。

