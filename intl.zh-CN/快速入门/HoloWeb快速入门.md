---
keyword: [HoloWeb, 快速入门]
---

# HoloWeb快速入门

HoloWeb是基于交互式分析Hologres引擎，使用可视化方式开发和管理数据库的一站式平台。本文以一个简单的案例，为您介绍使用HoloWeb的基本流程。

-   阿里云账号注册。
-   实名认证。
-   开通Hologres，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4547818061/p141749.png)

3.  单击**Hologres引擎管理**页面的**前往HoloWeb**，进入HoloWeb开发页面。

4.  登录Hologres实例。

    1.  单击元数据管理页签的**新增实例**。

    2.  配置**新增实例**弹框的参数，单击**确认**。

        ![新增实例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1294980261/p273423.png)

        |参数|描述|是否必选|
        |--|--|----|
        |网络类型|        -   **公网**：支持华东2（上海）、华南1（深圳）、华北2（北京）、华东1（杭州）、亚太东南1（新加坡）、中国（香港）、亚太东南3（吉隆坡）及美国西部1（硅谷）等地域。

![公网实例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3817980261/p273449.png)图标的为公网类型的实例。

        -   **VPC**：仅支持配置为HoloWeb所登录的地域。

![VPC网络](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3817980261/p273444.png)图标的为VPC类型的实例，该实例不支持编辑详细信息和删除。

|否|
        |实例名称|选择当前账号已创建的实例。|否|
        |名称|选择**实例名称**后，**连接名称**默认显示为所选实例的名称。您也可以重新自定义连接名称。|是|
        |描述|连接的描述信息。|否|
        |主机|Hologres实例的网络域名。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取主机。

如果您配置了**实例名称**，则系统自动为您匹配该实例的**主机**。您也可以选择手动输入主机地址。

|是|
        |端口|Hologres实例的网络端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。

如果您配置了**实例名称**，则系统自动为您匹配该实例的**端口**。您也可以选择手动输入端口地址。

|是|
        |登录方式|        -   **当前账户免密登录**：无需输入账号及密码，直接使用当前账户登录。
        -   **账户密码登录**：您可以输入自己或者其他账户的账号及密码，并登录。
|是|
        |AccessKey ID|登录方式配置为账户密码登录时，需要配置该参数。

当前账号的AccessKey ID。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。

|否|
        |AccessKey Secret|登录方式配置为账户密码登录时，需要配置该参数。

当前账号的AccessKey Secret。您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。

|否|
        |测试连通性|检测连接是否成功：         -   显示**测试通过**表示连接成功。
        -   显示**测试不通过**表示连接失败。
|否|
        |新增后登录|您可以选择是否登录该实例。        -   **是**该实例会登录并展示在左侧已登录实例列表中。
        -   **否**该实例会展示在左侧为登录实例列表中。
|是|

5.  新建数据库。

    开通Hologres实例后，系统自动创建**postgres**数据库。该数据库分配到的资源较少，仅用于管理，开发实际业务需要新建数据库。在此之前如果您已经新建数据库，请跳过该步骤，直接新建SQL查询。

    1.  单击**元数据管理** \> **数据库**。

        您也可以在**元数据管理**界面的**已登录实例**列表。鼠标右击目标连接，选择**新建数据库**。

        ![数据库](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2863790261/p273513.png)

    2.  配置**新建数据库**的参数，单击**确认**。

        ![新建数据库](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2863790261/p273507.png)

        |参数|说明|
        |--|--|
        |实例名|默认展示当前数据库所在的已登录实例的名称。|
        |数据库名称|您可以命名当前数据库的名称。**说明：** 配置的数据库名称必须唯一。 |
        |权限策略|您可以根据业务需求为数据库配置对应权限。更多关于权限策略的说明，请参见：        -   [SPM](/intl.zh-CN/账号与权限管理/Hologres权限模型/简单权限模型/简单权限模型概述.md)
        -   [SLPM](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)
        -   [专家权限模型](/intl.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md) |
        |立即登录|        -   是：登录后您就可以直接使用创建的数据库。
        -   否：登录数据库之后才可以进行使用。 |

6.  新建SQL查询。

    成功连接Hologres实例后，您可以在**SQL编辑器**模块，使用标准的PostgreSQL语言进行开发。具体操作步骤如下：

    1.  单击**SQL编辑器** \> **新增SQL窗口**。

        您也可以在左侧导航栏，鼠标右键单击**我的SQL查询**，选择**新建SQL查询**。具体操作请参见[SQL窗口](/intl.zh-CN/连接开发工具/HoloWeb/SQL编辑器/SQL窗口.md)。

    2.  在新增的**临时Query查询**页面，选择已创建的**实例名**和**数据库**后，您可以在SQL查询的编辑框输入如下示例语句，单击**运行**。

        ```
        BEGIN;
        CREATE TABLE nation (
         n_nationkey bigint NOT NULL,
         n_name text NOT NULL,
         n_regionkey bigint NOT NULL,
         n_comment text NOT NULL,
        PRIMARY KEY (n_nationkey)
        );
        CALL SET_TABLE_PROPERTY('nation', 'bitmap_columns', 'n_nationkey,n_name,n_regionkey');
        CALL SET_TABLE_PROPERTY('nation', 'dictionary_encoding_columns', 'n_name,n_comment');
        CALL SET_TABLE_PROPERTY('nation', 'time_to_live_in_seconds', '31536000');
        COMMIT;
        
        INSERT INTO nation VALUES
        (11,'zRAQ', 4,'nic deposits boost atop the quickly final requests? quickly regula'),
        (22,'RUSSIA', 3  ,'requests against the platelets use never according to the quickly regular pint'),
        (2,'BRAZIL',  1 ,'y alongside of the pending deposits. carefully special packages are about the ironic forges. slyly special '),
        (5,'ETHIOPIA',  0 ,'ven packages wake quickly. regu'),
        (9,'INDONESIA', 2  ,'slyly express asymptotes. regular deposits haggle slyly. carefully ironic hockey players sleep blithely. carefull'),
        (14,'KENYA',  0  ,'pending excuses haggle furiously deposits. pending, express pinto beans wake fluffily past t'),
        (3,'CANADA',  1 ,'eas hang ironic, silent packages. slyly regular packages are furiously over the tithes. fluffily bold'),
        (4,'EGYPT', 4 ,'y above the carefully unusual theodolites. final dugouts are quickly across the furiously regular d'),
        (7,'GERMANY', 3 ,'l platelets. regular accounts x-ray: unusual, regular acco'),
        (20 ,'SAUDI ARABIA',  4 ,'ts. silent requests haggle. closely express packages sleep across the blithely');
        
        SELECT * FROM nation;
        ```

        **说明：** SQL查询对表名称和字段名称的大小写不敏感。如果您需要精确查询名称大小写完全一致的表，则需要使用双引号将该表名称引起来。

    3.  查看结果。

        您可以在下方的**运行日志**区域查看当前语句的执行结果。

        ![结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5362141261/p118138.png)

7.  新建外部表。

    Hologres与MaxCompute底层资源无缝连通，支持使用新建外部表的方式加速查询MaxCompute数据。HoloWeb使用可视化的方式新建外部表的步骤如下：

    1.  单击**元数据管理** \> **MaxCompute加速** \> **创建外部表**。

        ![新建内部表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7367790261/p273758.png)

    2.  配置**新建外部表**的各项参数，单击**提交表**。

        ![新建外部表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7128321261/p273796.png)

        |参数|描述|
        |--|--|
        |实例名|已登录的实例名称。|
        |数据库|Hologres当前已登录实例的数据库名称。|
        |表名|新建的Hologres外部表名称。输入目标MaxCompute表名后，将会自动创建同名外部表。在创建时不支持更改表名，如果您需要更改表名，可以在外部表创建成功后，在已登录实例列表中右键单击目标表进行修改。 |
        |描述|新建的Hologres外部表描述。|
        |模式|模式名称。您可以选择默认创建的**public**模式，也可以选择新建的模式名称。 |
        |类型|外部表类型。目前仅支持MaxCompute。 |
        |服务器列表|您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|
        |表|MaxCompute的项目名和表名。格式为**project.table\_name**。

**说明：**

        -   目前暂不支持跨地域查询外部表数据。
        -   输入表名称后，会显示外部源表的所有字段，创建外部表时也将会默认创建所有字段。如果您需要创建部分字段，请使用SQL语句创建外部表，请参见[CREATE FOREIGN TABLE](/intl.zh-CN/SQL参考/DDL/FOREIGN TABLE/CREATE FOREIGN TABLE.md)。 |

        **说明：** 创建外部表同步MaxCompute表的数据时，会将数据库中表字段的Comment和列的Comment一并同步至Hologres。

    3.  成功新建外部表后，选择左侧导航栏**元数据管理** \> **已登录实例**。双击目标外部表，在外部表的编辑界面单击**数据预览**，加速查询MaxCompute数据。


