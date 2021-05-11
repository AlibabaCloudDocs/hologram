---
keyword: [Hologres, FineBI, 可视化分析, BI工具]
---

# FineBI

FineBI是帆软软件有限公司推出的一款商业智能（Business Intelligence）产品。FineBI自助分析以业务需求为方向，通过便捷的数据处理和管控，提供自由的探索分析。本文为您介绍FineBI如何连接Hologres进行可视化分析。

## 连接Hologres

1.  安装FineBI。

    安装FineBI，详细步骤请参见[FineBI官方文档](https://help.fanruan.com/finebi/doc-view-260.html)。

2.  安装JDBC驱动。

    请前往PostgreSQL官网[下载JDBC驱动](https://mvnrepository.com/artifact/org.postgresql/postgresql)。

    **说明：**

    -   请您下载并使用42.2.18以上版本的JDBC驱动。
    -   您需要将下载的PostgreSQL的JDBC驱动包放置于<FineBI安装目录\>\\webapps\\webroot\\WEB-INF\\lib目录下，并重启FineBI。
3.  连接Hologres。

    1.  使用管理员账号登录FineBI，选择**管理系统** \> **数据连接** \> **数据连接管理**。

    2.  单击**新建数据连接**，在所有页签下选择**PostgreSQL**。

        ![选择数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268316.png)

    3.  在PostgreSQL页面配置连接信息。

        ![配置信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268321.png)

        具体参数说明如下：

        |参数|说明|
        |--|--|
        |数据连接名称|您可以输入数据连接的名称。如Hologres。|
        |驱动|驱动选择`org.postgresql.Driver`。|
        |数据库名称|Hologres的数据库名称。|
        |主机|Hologres实例的公共网络地址。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取网络地址。|
        |端口|Hologres的实例端口。您可以进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从实例配置页签获取实例端口。|
        |用户名|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |密码|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。|
        |数据连接URL|数据连接URL，填写格式为`jdbc:postgresql://<host>:<port>/<databasename>`，其中各参数解释如下所示：        -   host：主机，Hologres实例的公共网络地址。
        -   port：端口，Hologres实例的端口。
        -   databasename：数据库名称，Hologres创建的数据库名称。 |
        |高级设置|该示例不涉及高级设置。如业务涉及配置，更多内容请参见[FineBI官方文档](http://help.finebi.com/doc-view-94.html)。|

    4.  配置完以上参数，在页面中单击**点击连接数据库**，读取模式列表并从下拉列表中选择您需要使用的Schema。

        ![选择Schema](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268347.png)

    5.  单击页面右上角的**测试连接**，如果提示**连接成功**表示FineBI能够正常连接Hologres数据库。

    6.  单击页面右上角的**保存**，完成数据连接的配置。

4.  添加数据表。

    1.  在左侧导航栏单击数据准备，进入数据列表页。

    2.  单击进入目标业务包详情页，单击**添加表**。

        ![添加表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268362.png)

    3.  您可以使用数据库表、SQL数据集等方式从Hologres中加载数据，此处以数据库表为例获取数据表信息。单击**数据库表**进入数据库选表页面，可以选择已创建的数据连接，右侧会列出数据连接的数据库对应Schema中的所有数据表。

        ![数据库表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268370.png)

    4.  选择需要加载的数据表，单击页面右上角的**确定**，FineBI即会加载选中的数据表，已添加的表灰化不可选择。

    5.  加载完成后，您可以在目标业务包的数据列表中选择具体的数据表，进行数据准备的相关操作。

        ![加载数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268373.png)

5.  数据分析。

    当您完成数据表的加载操作后，您可以利用这些数据表进行数据可视化相关操作，更多操作指导请参见[FineBI官方文档](https://help.fanruan.com/finebi/doc-view-259.html)。


## 时间筛选控件最佳实践

在Hologres创建表时，我们可以设置Segment\_key（别名event\_time\_column\)属性，对时间类型过滤条件进行索引优化，防止全表扫描，能够加速查询。Hologres默认把表中第一个时间戳类型作为Segment\_key。

您可以通过创建带参数的SQL数据集，当您的SQL的执行计划中有**Segment Filter**关键字出现时，即表示Segment\_key已经生效，您可以使用带参数的SQL数据集创建的时间控件。

![验证生效](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1898360261/p272291.png)

创建带参数的SQL数据集具体步骤如下：

1.  登录FineBI，在左侧导航栏单击数据准备，进入数据列表页。

2.  单击进入目标业务包详情页，单击**添加表**，选择**SQL数据集**。

    ![添加表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9153249161/p268362.png)

    **说明：**

    -   FineBI支持带参数的数据集，详细的功能介绍，请参见[FineBI官方文档](https://help.fanruan.com/finebi/doc-view-890.html)。
    -   您可以根据文档描述的带参数的SQL数据集功能，使用参数来设置日期控件。
3.  在表名处填写表名，并在SQL语句模块填写如下SQL。单击**刷新**，系统可自动识别参数，您可以手动选择参数类型，单击页面右上角的**确定**保存数据集。

    ![数据准备](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2880460261/p272296.png)

    ```
    SELECT 
      id,
      log_timestamp 
    from test_bi_timestamp 
    where log_timestamp <='${time}'::timestamp;
    ```

4.  在仪表盘中选择日期控件，选中绑定参数，从列表中选择time后单击**确定**。

    ![绑定参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2880460261/p272312.png)

5.  在页面设置日期并进行过滤。

    ![设置日期并过滤](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2880460261/p272324.png)

6.  设置完成后，您可以通过日志查看执行的SQL。查看日志具体操作，请参见[FineBI官方文档](https://help.fanruan.com/finebi/doc-view-583.html)。

    生成的SQL如下所示：

    ```
    select 
      "T_13E5C8"."id" as "id", 
      "T_13E5C8"."log_timestamp" as "log_timestamp"
    from (
      select 
          id,
          log_timestamp 
      from test_bi_timestamp 
      where log_timestamp <='2021-05-08'::timestamp
    ) as "T_13E5C8"
    limit 5000;
    ```

    生成的执行计划具体如下：

    ```
     Limit  (cost=0.00..0.15 rows=1000 width=16)
       ->  Gather Motion  (cost=0.00..0.14 rows=1000 width=16)
             ->  Limit  (cost=0.00..0.10 rows=1000 width=16)
                   ->  Parallelism (Gather Exchange)  (cost=0.00..0.10 rows=1000 width=16)
                         ->  DecodeNode  (cost=0.00..0.10 rows=1000 width=16)
                               ->  Limit  (cost=0.00..0.10 rows=1000 width=16)
                                     ->  Index Scan using holo_index:[1] on test_bi_timestamp  (cost=0.00..1.00 rows=1000 width=16)
                                           Segment Filter: (log_timestamp <= '2021-05-08 00:00:00+08'::timestamp with time zone)
     Optimizer: HQO version 0.8.0
    (9 行记录)
    ```

    当您SQL的执行计划中有**Segment Filter**关键字出现时，即表示Segment\_key已经生效，您可以使用带参数的SQL数据集创建的时间控件。


