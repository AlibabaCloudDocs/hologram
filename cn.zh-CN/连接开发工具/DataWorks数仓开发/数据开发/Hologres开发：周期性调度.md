---
keyword: [周期性调度, Hologres]
---

# Hologres开发：周期性调度

HoloStudio与DataWorks无缝连通，您可以通过HoloStudio将MaxCompute数据导入Hologres，并基于DataWorks的底层能力，前往DataWorks进行定时调度，实现周期性导入数据至Hologres。本文为您介绍如何将MaxCompute源表数据导入Hologres进行周期性调度。

-   MaxCompute与Hologres的分区无强映射关系，MaxCompute的分区字段映射为Hologres的普通字段。因此，您可以将MaxCompute的分区表或非分区表导入Hologres的分区或非分区表，可以根据实际业务情况选择是否需要分区。
-   前往Dataworks调度会产生一定的调度费用，详细收费情况，请参见[DataWorks资源组概述]()。
-   如果需要实现写入更新，您需要使用[INSERT ON CONFLICT](/cn.zh-CN/Hologres SQL/DML&DQL/INSERT ON CONFLICT.md)语法。

1.  准备MaxCompute表数据。

    准备一张MaxCompute数据源表，您可以参考[t11950.dita\#concept\_rkk\_kcy\_5db](/cn.zh-CN/快速入门/创建和查看表.md)进行建表并导入数据。示例选用MaxCompute公共数据集**public\_data**的分区表dwd\_ product\_movie\_basic\_info表，您可以参照[使用公开数据集](/cn.zh-CN/公开数据集/概述.md)描述，登录并查询数据集。其建表DDL语句如下：

    ```
    --MaxCompute分区表DDL
    CREATE TABLE IF NOT EXISTS public_data.dwd_product_movie_basic_info(
      movie_name STRING COMMENT '电影名称',
      dirctor STRING COMMENT '导演',
      scriptwriter STRING COMMENT '编剧',
      area STRING COMMENT '制片地区/国家',
      actors STRING COMMENT '主演',
      `type` STRING COMMENT '类型',
      movie_length STRING COMMENT '电影长度',
      movie_date STRING COMMENT '上映日期',
      movie_language STRING COMMENT '语言',
      imdb_url STRING COMMENT 'imdb号'
    ) 
    PARTITIONED BY (ds STRING) STORED AS ALIORC;
    
    --查看分区表的某个分区数据
    SELECT * FROM public_data.dwd_product_movie_basic_info WHERE ds = '20170112';
    ```

    查询数据显示如下图所示。

    ![movie_basic](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8079404161/p242606.png)

2.  新建外部表。

    进入HoloStudio，在SQL Console中新建一张外部表，用于映射MaxCompute源表数据。外部表的字段顺序和字段类型需要和MaxCompute一一对应。示例新建外部表的SQL语句如下：

    ```
    BEGIN;
    CREATE FOREIGN TABLE public.foreign_dwd_product_movie_basic_info (
        "movie_name" text,
        "dirctor" text,
        "scriptwriter" text,
        "area" text,
        "actors" text,
        "type" text,
        "movie_length" text,
        "movie_date" text,
        "movie_language" text,
        "imdb_url" text,
        "ds" text
    )
    SERVER odps_server
    OPTIONS (project_name 'public_data', table_name 'dwd_product_movie_basic_info');
    comment on column public.foreign_dwd_product_movie_basic_info."movie_name" is '电影名称';
    comment on column public.foreign_dwd_product_movie_basic_info."dirctor" is '导演';
    comment on column public.foreign_dwd_product_movie_basic_info."scriptwriter" is '编剧';
    comment on column public.foreign_dwd_product_movie_basic_info."area" is '制片地区/国家';
    comment on column public.foreign_dwd_product_movie_basic_info."actors" is '主演';
    comment on column public.foreign_dwd_product_movie_basic_info."type" is '类型';
    comment on column public.foreign_dwd_product_movie_basic_info."movie_length" is '电影长度';
    comment on column public.foreign_dwd_product_movie_basic_info."movie_date" is '上映日期';
    comment on column public.foreign_dwd_product_movie_basic_info."movie_language" is '语言';
    comment on column public.foreign_dwd_product_movie_basic_info."imdb_url" is 'imdb号';
    COMMIT;
                        
    ```

    OPTIONS的连接参数说明如下表所示。

    |参数|描述|
    |--|--|
    |project\_name|MaxCompute的项目名称。|
    |table\_name|MaxCompute的表名称。|

3.  新建存储表。

    在HoloStudio中新建一张用于接收并存储数据的真实存储表。由于本次示例是将MaxCompute分区表导入Hologres分区表，因此需要在Hologres中创建一张分区表。

    该示例建表DDL仅是简单示例，实际建表DDL请根据实际业务需要创建，并给表设置合理的索引，以达到更优的查询性能，DDL示例如下：

    ```
    BEGIN;
    CREATE TABLE "public"."holo_dwd_product_movie_basic_info" (
     "movie_name" text,
     "dirctor" text,
     "scriptwriter" text,
     "area" text,
     "actors" text,
     "type" text,
     "movie_length" text,
     "movie_date" text,
     "movie_language" text,
     "imdb_url" text,
     "ds" text
    )
    PARTITION BY LIST (ds);
    CALL SET_TABLE_PROPERTY('"public"."holo_dwd_product_movie_basic_info"', 'orientation', 'column');
    CALL SET_TABLE_PROPERTY('"public"."holo_dwd_product_movie_basic_info"', 'bitmap_columns', '"movie_name","dirctor","scriptwriter","area","actors","type","movie_length","movie_date","movie_language","imdb_url","ds"');
    CALL SET_TABLE_PROPERTY('"public"."holo_dwd_product_movie_basic_info"', 'dictionary_encoding_columns', '"movie_name:auto","dirctor:auto","scriptwriter:auto","area:auto","actors:auto","type:auto","movie_length:auto","movie_date:auto","movie_language:auto","imdb_url:auto","ds:auto"');
    CALL SET_TABLE_PROPERTY('"public"."holo_dwd_product_movie_basic_info"', 'time_to_live_in_seconds', '3153600000');
    comment on column "public"."holo_dwd_product_movie_basic_info"."movie_name" is '电影名称';
    comment on column "public"."holo_dwd_product_movie_basic_info"."dirctor" is '导演';
    comment on column "public"."holo_dwd_product_movie_basic_info"."scriptwriter" is '编剧';
    comment on column "public"."holo_dwd_product_movie_basic_info"."area" is '制片地区/国家';
    comment on column "public"."holo_dwd_product_movie_basic_info"."actors" is '主演';
    comment on column "public"."holo_dwd_product_movie_basic_info"."type" is '类型';
    comment on column "public"."holo_dwd_product_movie_basic_info"."movie_length" is '电影长度';
    comment on column "public"."holo_dwd_product_movie_basic_info"."movie_date" is '上映日期';
    comment on column "public"."holo_dwd_product_movie_basic_info"."movie_language" is '语言';
    comment on column "public"."holo_dwd_product_movie_basic_info"."imdb_url" is 'imdb号';
    COMMIT;
    ```

4.  新建分区子表的数据开发。

    由于本示例是将MaxCompute分区表数据导入Hologres分区表，因此需要在Hologres中创建对应的分区键值的分区子表。然后将分区数据导入对应的分区子表。这里通过\{bizdate\}参数控制分区键值，在调度系统中自动赋值完成周期性调度。关于参数的配置，请参见DataWorks文档[DataWorks调度配置]()。其中，insert的分区数据必须和分区键（本文示例为ds）的值保持一致，否则会报错。

    1.  在HoloStudio中，单击左侧导航栏的![数据开发](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p178952.png)图标，进入数据开发页面。

    2.  鼠标悬停至![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p178953.png)图标，单击**Hologres开发**。

    3.  配置**新建节点**对话框的**节点名称**、**目标文件夹**及**数据库**参数。

        ![新建节点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p179024.png)

    4.  单击**提交**，成功创建Hologres开发节点。

    5.  在新建的Hologres开发节点的编辑界面，输入如下创建分区表的数据开发语句。

        ```
        --创建分区子表
        DROP TABLE IF EXISTS "public".holo_dwd_product_movie_basic_info_${bizdate};
        
        CREATE TABLE IF NOT EXISTS "public".holo_dwd_product_movie_basic_info_${bizdate} PARTITION OF "public".holo_dwd_product_movie_basic_info FOR VALUES IN ('${bizdate}');
        
        
        --导入指定分区数据
        INSERT INTO "public".holo_dwd_product_movie_basic_info_${bizdate}
        SELECT 
            "movie_name",
            "dirctor",
            "scriptwriter",
            "area",
            "actors",
            "type",
            "movie_length",
            "movie_date",
            "movie_language",
            "imdb_url",
            "ds"
        FROM "public".foreign_dwd_product_movie_basic_info
        WHERE ds='${bizdate}';
        ```

    6.  单击顶部菜单栏左侧的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p179028.png)，保存Hologres开发。

    7.  单击顶部菜单栏右侧的**前往DataWorks调度**，进入调度页面。

        ![前往调度](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0732504161/p179031.png)

5.  新建分区表的调度作业。

    1.  在DataWorks中新建Hologres节点。

        单击**前往DataWorks调度**后，页面自动跳转至DataWorks的**新建节点**。您需要配置**新建节点**对话框的**节点类型**、**节点名称**及**目标文件夹**，创建Hologres节点。**节点类型**选择**Hologres开发**。

        ![新建节点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179052.png)

        如果是重新执行已有作业，则可以不需要重新创建节点。

    2.  在新建的节点页面，单击**更新节点版本**，将分区表的信息同步至该节点。

        ![同步数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0732504161/p179073.png)

6.  配置调度信息。

    在新建的节点页面，单击右侧导航栏的**调度配置**，配置调度参数。详情请参见[调度参数]()。本次示例选择的是日调度，因此将会每天自动生成一张分区子表，并导入对应的数据，以此来实现增量数据周期性调度。更多的调度逻辑（包括调度周期、调度参数、调度上下游等）请参见[DataWorks调度配置]()。

    具体配置如下所示：

    1.  配置基础属性。

        在**基础属性**区域，将**参数**赋值为时间节点。

        ![基础属性](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0732504161/p179120.png)

    2.  配置时间属性。

        在**时间属性**区域，配置**时间属性**为**正常调度**。其余参数请根据业务实际情况配置，详情请参见[配置时间属性]()。

        ![时间属性](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0732504161/p179125.png)

    3.  配置调度依赖。

        在**调度依赖**区域，您需要配置调度依赖为root节点，操作如下：

        1.  将**自动解析**配置为**是**。
        2.  单击**使用工作空间根节点**，自动解析出root节点。
        3.  再将**自动解析**配置为**否**，详情请参见[配置调度依赖]()。
        ![调度依赖](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179148.png)

        您也可以根据业务逻辑选择已有的父节点。

7.  发布调度。

    1.  配置完调度参数，在新建节点页面的顶部菜单栏左侧，单击![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179177.png)图标，保存调度配置。

    2.  在新建节点页面的顶部菜单栏左侧，单击![提交](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179178.png)图标，提交调度信息。

    3.  发布包提交成功后，单击**任务发布**页面顶部菜单栏右侧的**运维中心**，进入运维中心。

    4.  在左侧导航栏，单击**周期任务运维** \> **周期任务**，勾选并单击目标任务名称。

    5.  在右侧编辑界面，鼠标右键单击目标任务节点，选择**补数据** \> **当前节点**。

        ![补数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179195.png)

    6.  根据业务实际情况，配置**补数据**对话框的各项参数。完成调度任务的发布。

8.  任务执行成功后，您可以通过如下方式查询数据。

    -   您可以返回Hologres中，通过标准SQL查看父表或分区表的数据，示例如下。

        ```
        --查看分区子表数据
        select * from holo_dwd_product_movie_basic_info_20170112;
        
        --查看分区父表总数据
        select count (*) from holo_dwd_product_movie_basic_info;
        ```

    -   返回HoloStudio。单击新建节点的![刷新](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179202.png)图标，刷新当前页面。您可以在**PG管理**模块找到目标分区表，查看调度成功的分区表数据。双击目标表名称，在表编辑页面单击**数据预览**，即可看到已成功导入的数据。

