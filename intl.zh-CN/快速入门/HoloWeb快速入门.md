---
keyword: [HoloWeb, 快速入门]
---

# HoloWeb快速入门

HoloWeb是基于交互式分析Hologres引擎，使用可视化方式开发和管理数据库的一站式平台。本文以一个简单的案例，为您介绍使用HoloWeb的基本流程。

-   阿里云账号注册。
-   实名认证。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3542488951/p141749.png)

3.  单击**Hologres引擎管理**页面的**登录Hologres数据库**，进入**HoloWeb**开发界面。

4.  互动学习。

    在**HoloWeb**开发界面，您可以单击顶部菜单栏右侧的**互动学习**，在界面右侧会出现**互动学习**区域。该区域为您介绍了HoloWeb的基本使用流程，您可以参考所给的流程步骤，快速学习并完成一个简单的试验。

    在**互动学习**区域底部，您可以反馈该区域的可用性。

5.  连接Hologres实例。

    1.  单击**连接管理** \> **数据连接**。

        ![数据连接](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3822488951/p116500.png)

    2.  配置**新建连接**弹框的参数，单击**确认**。

        ![新建连接](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5560409951/p116502.png)

        |参数名称|说明|备注|
        |----|--|--|
        |连接名称|自定义的连接名称。|无|
        |连接描述|连接的描述信息。|无|
        |主机|Hologres实例的公共网络域名。|进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取主机。|
        |端口|Hologres实例的公共网络端口。|进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。|
        |初始化数据库|Hologres实例中创建的数据库名称。|进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**DB管理**获取初始化数据库。|
        |用户名|当前账号的AccessKey ID。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取用户名。|
        |密码|当前账号的AccessKey Secret。|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取密码。|
        |测试连通性|检测数据连接是否成功：         -   成功：显示**测试通过**。
        -   不成功：显示**测试不通过**。
|无|

6.  新建数据库。

    开通Hologres实例后，系统自动创建**postgres**数据库。该数据库分配到的资源较少，仅用于管理，开发实际业务需要新建数据库。在此之前如果您已经新建数据库，请跳过该步骤，直接新建SQL查询。

    1.  单击**连接管理** \> **数据库**。

        ![数据库](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3822488951/p118032.png)

    2.  配置**新建数据库**的参数，单击**确认**。

        ![DB](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3542488951/p118018.png)

        |参数名称|说明|
        |----|--|
        |连接名称|存放数据库的连接。|
        |数据库名称|必须唯一。|
        |权限策略|建议使用简单模式。|

7.  新建SQL查询。

    成功连接Hologres实例后，您可以在**Query查询**模块，使用标准的PostgreSQL语言进行开发。具体操作步骤如下：

    1.  单击**Query查询** \> **SQL窗口**。

        ![sql](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3822488951/p118146.png)

    2.  配置**新建SQL查询**的参数，单击**确认**。

        ![chax](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3822488951/p118147.png)

        |参数名称|说明|
        |----|--|
        |作业名称|自定义的SQL查询名称。|
        |目录|存放SQL查询的文件夹名称，默认为我的SQL查询。|
        |连接名称|创建的连接名称。|
        |数据库名称|所选连接中的数据库名称。|

    3.  在SQL查询的编辑框输入如下示例语句，单击![r](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3822488951/p118141.png)。

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

        **说明：**

        -   SQL查询对表名称和字段名称的大小写不敏感。如果您需要精确查询名称大小写完全一致的表，则需要使用双引号将该表名称引起来。
        -   默认只能开启一个SQL查询窗口，如果您开启了其他SQL查询窗口，系统会自动中断前一个窗口作业。
    4.  查看结果。

8.  新建外部表。

    Hologres与MaxCompute底层资源无缝连通，支持使用新建外部表的方式加速查询MaxCompute数据。HoloWeb使用可视化的方式新建外部表的步骤如下：

    1.  单击**连接管理** \> **外部表**。

        ![o](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4822488951/p118150.png)

    2.  配置**新建外部表**的各项参数，单击**提交表**。

        ![c](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9428631061/p118151.png)

        |参数名称|说明|备注|
        |----|--|--|
        |连接名|已配置的连接名称。|无|
        |数据库|Hologres的数据库名成。|无|
        |表名|新建的Hologres外部表名称。|自定义|
        |描述|新建的Hologres外部表描述。|自定义|
        |模式|模式名称。|默认为**public**。您也可以选择新建的模式名称。|
        |类型|外部表类型。|目前仅支持MaxCompute。|
        |服务器列表|**odps\_server**|您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。|
        |表|MaxCompute的项目名和表名。|格式为`Project.Tablename`。 **说明：** 目前暂不支持跨地域查询外部表数据。 |
        |SQL Script|SQL语句编辑框。|无|

        **说明：** 创建外部表同步MaxCompute表的数据时，会将数据库中表字段的Comment和列的Comment一并同步至Hologres。

    3.  成功新建外部表后，选择左侧导航栏**连接管理** \> **我的连接**。双击目标外部表，在外部表的编辑界面单击**数据预览**，加速查询MaxCompute数据，示例如下。

        ![预览](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1015631061/p118166.png)


