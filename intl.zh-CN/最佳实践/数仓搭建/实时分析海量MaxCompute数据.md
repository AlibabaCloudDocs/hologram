---
keyword: [Hologres, 实时分析, 最佳实践]
---

# 实时分析海量MaxCompute数据

本文为您介绍交互式分析Hologres如何实时查询海量MaxCompute数据，并以可视化方式分析和展现查询结果的最佳实践。

-   开通MaxCompute，详情请参见[开通MaxCompute](/intl.zh-CN/准备工作/开通MaxCompute.md)。

    **说明：** 请确保MaxCompute和Hologres的地域相同。

-   开通Hologres并连接至HoloWeb，详情请参见快速入门。
-   开通Quick BI，详情请参见[前提条件]()。

Hologres是兼容PostgreSQL协议的实时交互式分析产品，在底层与MaxCompute无缝连接。

Hologres支持使用创建外部表的方式加速查询MaxComppute的数据。

本文以搭建访问某淘宝店铺的客户画像为例，展示客户所在城市、客户年龄、首选客户和出生于1980~1990年的首选客户所在城市人数的分布情况。

使用Hologres加速查询MaxCompute数据的完整链路如下所示。

1.  访问店铺的客户数据存储在MaxCompute表中。
2.  使用Hologres的创建外部表方式加速查询MaxCompute数据。
3.  Hologres对接数据大屏Quick BI，使用可视化方式展示客户画像。

1.  准备MaxCompute数据源。

    在MaxCompute中创建一张表并写入数据，详情请参见[创建和查看表](/intl.zh-CN/快速入门/创建和查看表.md)。

    本次试验采用已有的阿里云MaxCompute中**public\_data**项目的如下MaxCompute表。获取表的方法请参见[公开数据集](https://yq.aliyun.com/articles/89763?spm=a2c4g.11186623.2.8.14d01099q4lXK2)。

    |MaxCompute表名称|数据量|
    |-------------|---|
    |customer|1200万|
    |customer\_address|600万|
    |customer\_demographics|192万|

2.  Hologres创建外部表并查询表数据。

    通过使用[HoloWeb](https://holoweb-cn-shanghai.data.aliyun.com/connect)创建外部表，加速查询MaxCompute的数据。操作步骤如下：

    1.  Holoweb连接Hologers实例。

        登录[HoloWeb](https://holoweb-cn-shanghai.data.aliyun.com/connect)，单击**连接管理** \> **数据连接**，配置**新建连接**的参数，单击**确认**。

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

    2.  新建外部表。

        单击**连接管理** \> **外部表**，使用可视化的方式创建外部表。

        输入MaxCompute表的名称，就可以索引出表的字段，您可以根据实际业务，选择需要同步的表字段，单击**提交**。

        **说明：**

        -   目前暂不支持跨地域查询MaxCompute表的数据。
        -   建外部查询MaxCompute通过外部服务器来实现，您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。其详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。
        您也可以使用SQL语句批量创建外部表。

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

    3.  预览外部表数据。

        成功新建外部表后，选择**连接管理** \> **我的连接**。

        鼠标右击新建的外部表，单击**数据预览**，查询MaxCompute表的数据。

        **数据预览**只展示部分数据。

    4.  查询外部表数据。

        选择外部表所在的**连接名**和**数据库**。使用SQL命令在**Query查询**模块中查询所有数据。

        示例SQL语句如下。

        ```
        # SQL1: 查询首选客户分布情况，按人数降序排列。
        SELECT c_preferred_cust_flag,
               count(*) AS cnt
        FROM customer
        WHERE c_preferred_cust_flag IS NOT NULL
        GROUP BY c_preferred_cust_flag
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL2: 查询客户年龄人数大于1000的分布情况，按人数降序排列。
        SELECT c_birth_year,
               count(*) AS cnt
        FROM customer
        WHERE c_birth_year IS NOT NULL
        GROUP BY c_birth_year HAVING count(*) > 1000
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL3: 查询客户所在城市的人数大于10的分布情况，按人数降序排序。
        SELECT ca_city,
               count(*) AS cnt
        FROM customer ,
             customer_address
        WHERE c_current_addr_sk = ca_address_sk
          AND ca_city IS NOT NULL
        GROUP BY ca_city HAVING count(*) > 10
        ORDER BY cnt DESC LIMIT 10;
        
        # SQL4: 查询首选客户出生于1980~1990年且所在城市的人数大于10的分布情况，按人数降序排列。
        SELECT ca_city,
               count(*) AS cnt
        FROM customer ,
             customer_address
        WHERE c_current_addr_sk = ca_address_sk
          AND c_birth_year >= 1980
          AND c_birth_year < 1990
          AND c_preferred_cust_flag = 'Y'
          AND ca_city IS NOT NULL
        GROUP BY ca_city HAVING count(*) > 10
        ORDER BY cnt DESC LIMIT 10;
        ```

3.  使用Quick BI分析数据。

    Hologres查询出的MaxCompute数据直接对接Quick BI，使用可视化方式分析和展示数据。操作步骤如下：

    1.  添加数据源。

        进入Quick BI控制台首页，选择PostgreSQL数据源，并填写配置信息，详情请参见[t1841752.md\#](/intl.zh-CN/常见BI连接工具/Quick BI.md)。

    2.  创建数据集。

        成功连接Quick BI后，您可以将需要展示的数据创建为数据集并制作报表。

        本次试验使用**即席查询SQL**的方式来创建数据集，如下图所示。

    3.  使用可视化方式展示客户画像。

        根据业务需求展示报表。示例所配置的报表展示如下。

        ![a](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6560409951/p120766.png)


