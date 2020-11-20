---
keyword: [周期性调度, Hologres]
---

# Hologres开发：周期性调度

HoloStudio与DataWorks无缝连通，您可以通过HoloStudio将MaxCompute数据导入Hologres，并基于DataWorks的底层能力，前往DataWorks进行定时调度，实现周期性导入数据至Hologres。本文为您介绍如何将MaxCompute源表数据导入Hologres进行周期性调度。

1.  准备MaxCompute表数据。

    准备一张MaxCompute数据源表，您可以参考[MaxCompute创建表](https://help.aliyun.com/document_detail/27808.html?spm=a2c4g.11186623.6.589.613b2b17XHk7rR)进行建表，也可以直接从数据地图中选用一张表。示例选用数据地图中已创建的表，其建表DDL语句如下。

    ```
    CREATE TABLE IF NOT EXISTS bank_data_odps
    (
     age             BIGINT COMMENT '年龄',
     job             STRING COMMENT '工作类型',
     marital         STRING COMMENT '婚否',
     education       STRING COMMENT '教育程度',
     card         STRING COMMENT '是否有信用卡',
     housing         STRING COMMENT '房贷',
     loan            STRING COMMENT '贷款',
     contact         STRING COMMENT '联系途径',
     month           STRING COMMENT '月份',
     day_of_week     STRING COMMENT '星期几',
     duration        STRING COMMENT '持续时间',
     campaign        BIGINT COMMENT '本次活动联系的次数',
     pdays           DOUBLE COMMENT '与上一次联系的时间间隔',
     previous        DOUBLE COMMENT '之前与客户联系的次数',
     poutcome        STRING COMMENT '之前市场活动的结果',
     emp_var_rate    DOUBLE COMMENT '就业变化速率',
     cons_price_idx  DOUBLE COMMENT '消费者物价指数',
     cons_conf_idx   DOUBLE COMMENT '消费者信心指数',
     euribor3m       DOUBLE COMMENT '欧元存款利率',
     nr_employed     DOUBLE COMMENT '职工人数',
     y               BIGINT COMMENT '是否有定期存款'
    );
    ```

2.  新建外部表。

    进入HoloStudio，在PG管理或SQL Console中新建一张外部表，用于映射MaxCompute源表数据。示例新建外部表的SQL语句如下。

    ```
    begin;
    CREATE FOREIGN TABLE "public"."bank_data_foreign_holo" (
     "age" int8,
     "job" text,
     "marital" text,
     "education" text,
     "card" text,
     "housing" text,
     "loan" text,
     "contact" text,
     "month" text,
     "day_of_week" text,
     "duration" text,
     "campaign" int8,
     "pdays" float8,
     "previous" float8,
     "poutcome" text,
     "emp_var_rate" float8,
     "cons_price_idx" float8,
     "cons_conf_idx" float8,
     "euribor3m" float8,
     "nr_employed" float8,
     "y" int8
    )
    SERVER odps_server
    OPTIONS (project_name 'odps_4_holoworkshop_dev', table_name 'bank_data_odps');
    COMMIT;
    ```

    OPTIONS的连接参数说明如下表所示。

    |参数|描述|
    |--|--|
    |project\_name|MaxCompute的项目名称。|
    |table\_name|MaxCompute的表名称。|

3.  新建存储表。

    在HoloStudio中新建一张用于接收并存储数据的真实存储表。字段类型需要与外部表保持一致。示例DDL语句如下。

    ```
    begin;
    create table if not exists bank_data_holo (
     age int8,
     job text,
     marital text,
     education text,
     card text,
     housing text,
     loan text,
     contact text,
     month text,
     day_of_week text,
     duration text,
     campaign int8,
     pdays float8,
     previous float8,
     poutcome text,
     emp_var_rate float8,
     cons_price_idx float8,
     cons_conf_idx float8,
     euribor3m float8,
     nr_employed float8,
     y int8,
     ds text not null
    )
    partition by list(ds);
    call set_table_property('bank_data_holo', 'orientation', 'column');
    call set_table_property('bank_data_holo', 'time_to_live_in_seconds', '700000');
    commit;
    ```

4.  新建分区表的数据开发。

    并单击上方新建Hologres开发，输入作业逻辑，单击保存--前往DataWorks调度，示例SQL如下：

    1.  在HoloStudio中，单击左侧导航栏的![数据开发](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p178952.png)图标，进入数据开发页面。

    2.  鼠标悬停至![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p178953.png)图标，单击**Hologres开发**。

    3.  配置**新建节点**对话框的**节点名称**、**目标文件夹**及**数据库**参数。

        ![新建节点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p179024.png)

    4.  单击**提交**，成功创建Hologres开发节点。

    5.  在新建的Hologres开发节点的编辑界面，输入如下创建分区表的数据开发语句。

        ```
        create table if not exists bank_data_holo_1_${bizdate} partition of bank_data_holo
          for values in ('${bizdate}');
        
        insert into bank_data_holo_1_${bizdate}
        select 
            age as age,
            job as job,
            marital as marital,
            education as education,
            card as card,
             housing as housing,
            loan as loan,
            contact as contact,
            month as month,
            day_of_week as day_of_week,
             duration as duration,
            campaign as campaign,
             pdays as pdays,
            previous as previous,
            poutcome as poutcome,
             emp_var_rate as emp_var_rate,
            cons_price_idx as cons_price_idx,
            cons_conf_idx as cons_conf_idx,
            euribor3m as euribor3m,
            nr_employed as nr_employed,
            y as y,
            '${bizdate}' as ds 
        from bank_data_foreign_holo;
        ```

    6.  单击顶部菜单栏左侧的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6596744061/p179028.png)，保存Hologres开发。

    7.  单击顶部菜单栏右侧的**前往DataWorks调度**，进入调度页面。

        ![前往调度](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179031.png)

5.  新建分区表的调度作业。

    1.  在DataWorks中新建Hologres节点。

        单击**前往DataWorks调度**后，页面自动跳转至DataWorks的**新建节点**。您需要配置**新建节点**对话框的**节点类型**、**节点名称**及**目标文件夹**，创建Hologres节点。**节点类型**选择**Hologres开发**。

        ![新建节点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179052.png)

        如果是重新执行已有作业，则可以不需要重新创建节点。

    2.  在新建的节点页面，单击**更新节点版本**，将分区表的信息同步至该节点。

        ![同步数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179073.png)

6.  配置调度信息。

    在新建的节点页面，单击右侧导航栏的**调度配置**，配置调度参数。详情请参见[调度参数]()。

    具体配置如下所示：

    1.  配置基础属性。

        在**基础属性**区域，将**参数**赋值为时间节点。

        ![基础属性](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179120.png)

    2.  配置时间属性。

        在**时间属性**区域，配置**时间属性**为**正常调度**。其余参数请根据业务实际情况配置，详情请参见[配置时间属性]()。

        ![时间属性](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179125.png)

    3.  配置调度依赖。

        在**调度依赖**区域，您需要配置调度依赖为root节点，操作如下：

        1.  将**自动解析**配置为**是**。
        2.  单击**使用工作空间根节点**，自动解析出root节点。
        3.  再将**自动解析**配置为**否**，详情请参见[依赖关系]()。
        ![调度依赖](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179148.png)

        您也可以根据业务逻辑选择已有的父节点。

7.  发布调度。

    1.  配置完调度参数，在新建节点页面的顶部菜单栏左侧，单击![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179177.png)图标，保存调度配置。

    2.  在新建节点页面的顶部菜单栏左侧，单击![提交](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179178.png)图标，提交调度信息。

    3.  在新建节点页面的顶部菜单栏右侧，单击**发布**，前往任务发布页面。

    4.  在任务发布页面，选中当前任务，单击目标任务**操作**列的**发布**。发布包提交成功后，单击上方菜单栏运维中心进行手工补数据

    5.  在**确认发布**对话框，单击**发布**。

        ![确认发布](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179190.png)

    6.  发布包提交成功后，单击**任务发布**页面顶部菜单栏右侧的**运维中心**，进入运维中心。

    7.  在左侧导航栏，单击**周期任务运维** \> **周期任务**，勾选并单击目标任务名称。

    8.  在右侧编辑界面，鼠标右键单击目标任务节点，选择**补数据** \> **当前节点**。

        ![补数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179195.png)

    9.  根据业务实际情况，配置**补数据**对话框的各项参数。完成调度任务的发布。

        ![补数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179196.png)

8.  任务执行成功后，返回HoloStudio。单击新建节点的![刷新](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7596744061/p179202.png)图标，刷新当前页面。您可以在**PG管理**模块找到目标分区表，查看调度成功的分区表数据。

    双击目标表名称，在表编辑页面单击**数据预览**，即可看到已成功导入的数据。

    ![数据预览](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8596744061/p179203.png)

    您也可以使用SQL语句查看父表或分区表的数据，示例如下。

    ```
    SELECT * FROM  bank_data_holo;
    ```


