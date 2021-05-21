---
keyword: [导入MaxCompute数据, HoloWeb]
---

# 一键同步MaxCompute数据

本文为您介绍如何使用HoloWeb，通过可视化的方式快捷导入MaxCompute数据。

存在已登录实例，请参见[登录实例](/intl.zh-CN/连接开发工具/HoloWeb/连接管理/登录实例.md)。

HoloWeb支持一键同步MaxCompute数据功能，您可以使用可视化方式导入MaxCompute表数据并进行查询。该方式比创建外部表直接查询数据的性能更好。

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4547818061/p141749.png)

3.  单击**Hologres引擎管理**页面的**前往HoloWeb**，进入HoloWeb开发页面。

4.  在HoloWeb开发页面的顶部菜单栏，选择**元数据管理** \> **MaxCompute加速**，单击**一键MaxCompute数据同步**。

5.  配置**新建MaxCompute数据同步**页面的各项参数。

    ![一键同步](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9128321261/p273814.png)

    参数描述如下表所示。

    |类别|参数|描述|
    |--|--|--|
    |选择连接|实例名|已登录的实例名称。|
    |数据库|Hologres已登录实例的数据库名称。|
    |MaxCompute源表选择|外部表来源|    -   **已有外部表**：表示在Hologres中已经建立映射MaxCompute数据的外部表。
    -   **新建外部表**：表示在Hologres中未建立MaxCompute数据映射的外部表。 |
    |外部Schema|Hologres中已创建的MaxCompute外部表所在的Schema。当**外部表来源**选择**已有外部表**时，需要配置该参数。 |
    |外部表表名字|Hologres中已创建的MaxCompute外部表的名称。当**外部表来源**选择**已有外部表**时，需要配置该参数。 |
    |服务器列表|Hologres存放新建外部表的服务器。您可以直接调用Hologres底层已创建的名为**odps\_server**的外部表服务器。详细原理请参见[Postgres FDW](https://www.postgresql.org/docs/11/postgres-fdw.html?spm=a2c4g.11186623.2.11.7e476020Gyif3k)。当**外部表来源**选择**新建外部表**时，需要配置该参数。 |
    |表名|Hologres新建的外部表的项目名和表名。格式为**project.table\_name**。

当**外部表来源**选择**新建外部表**时，需要配置该参数。 |
    |目标表设置|目标Schema|当前表所在的Schema名称。如果您没有新建Schema，则只能选择默认创建的**public** Schema。如果有新建的Schema，您也可以选择新建的Schema。 |
    |目标表名|接收MaxCompute表数据的Hologres内部表名称。|
    |目标表描述|目标表的信息描述。|
    |同步设置|同步字段|需要导入的MaxCompute表字段。您可以选择导入部分或全部字段。 |
    |分区配置|分区字段|选择分区字段，Hologres将会默认将表创建为分区表。Hologres仅支持一级分区。如果您需要导入MaxCompute的多级分区，则在Hologres中设置一级分区即可，其余分区自动映射为Hologres的普通字段。 |
    |业务日期|如果MaxCompute表使用日期进行分区，则您可以选择具体的分区日期，系统将会导入指定日期的数据至MaxCompute表。|
    |索引配置|存储模式|    -   **列存**，适用于各种复杂查询。
    -   **行存**，适用于基于主键的点查询和Scan。
如果不指定存储模式，则默认为**列存**。 |
    |生命周期（秒）|表数据的生命周期。默认为**永久**存储。指定生命周期后，如果数据在指定时间内未被修改，则引擎将会在到期后的某一个时间段删除数据。 |
    |聚簇索引|排序索引Clustering\_key。索引的类型和列的顺序密切相关。聚簇索引帮助您加速执行索引列的Range和Filter查询。 |
    |分段键|您可以指定部分列作为分段键Segment\_key。当查询条件包含分段列时，您可以通过分段键快速查找相应数据的存储位置。|
    |字典编码列|Hologres支持为指定列的值构建字典映射。字典编码可以将字符串的比较转换为数字的比较，加速Group By和Filter查询。

默认设置所有**text**列至字典编码列中。 |
    |位图列|Hologres支持在位图列构建比特编码。位图列可以根据设置的条件快速过滤字段内部的数据。

默认设置所有**text**列至位图列中。 |
    |分布列索引|Hologres会按照分布列指定的列将数据shuffle到各个Shard，同样的数值会在同样的Shard中。以分布列做过滤条件时，可以大大提高执行效率。|

    **SQL Script**为您自动解析当前可视化操作对应的SQL语句。

6.  单击右上角的**提交**。


