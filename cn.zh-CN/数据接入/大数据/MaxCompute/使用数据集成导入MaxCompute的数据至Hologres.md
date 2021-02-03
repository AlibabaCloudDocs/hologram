---
keyword: [数据集成, 导入MaxCompute数据, Hologres, 离线同步]
---

# 使用数据集成导入MaxCompute的数据至Hologres

本文为您介绍如何使用DataWorks数据集成的离线同步模块，导入MaxCompute的分区表及非分区表数据至Hologres。

-   开通DataWorks，详情请参见[入门概述]()。
-   开通Hologres并绑定至DataWorks的工作空间，详情请参见[HoloStudio快速入门](/cn.zh-CN/连接开发工具/DataWorks数仓开发/HoloStudio快速入门.md)。
-   开通MaxCompute，详情请参见[开通MaxCompute](/cn.zh-CN/准备工作/开通MaxCompute.md)。

Hologres与MaxCompute在底层无缝连通，如果您需要导入大量的MaxCompute数据至Hologres，建议使用SQL方式，详情请参见[使用SQL导入MaxCompute的数据至Hologres](/cn.zh-CN/数据接入/大数据/MaxCompute/使用SQL导入MaxCompute的数据至Hologres.md)。

## 导入MaxCompute非分区表至Hologres

1.  准备MaxCompute非分区数据。

    准备一张MaxCompute非分区表并导入数据，详情请参见[创建和查看表](/cn.zh-CN/快速入门/创建和查看表.md)。您也可以选用已有的MaxCompute非分区表。示例建表SQL语句如下。

    ```
    CREATE TABLE IF NOT EXISTS bank_data
    (
        age            BIGINT COMMENT '年龄',
        job            STRING COMMENT '工作类型',
        marital        STRING COMMENT '婚否',
        education      STRING COMMENT '教育程度',
        creditcard     STRING COMMENT '是否有信用卡',
        housing        STRING COMMENT '房贷',
        loan           STRING COMMENT '贷款',
        contact        STRING COMMENT '联系途径',
        month          STRING COMMENT '月份',
        day_of_week    STRING COMMENT '星期几',
        duration       STRING COMMENT '持续时间',
        campaign       BIGINT COMMENT '本次活动联系的次数',
        pdays          DOUBLE COMMENT '与上一次联系的时间间隔',
        previous       DOUBLE COMMENT '之前与客户联系的次数',
        poutcome       STRING COMMENT '之前市场活动的结果',
        emp_var_rate   DOUBLE COMMENT '就业变化速率',
        cons_price_idx DOUBLE COMMENT '消费者物价指数',
        cons_conf_idx  DOUBLE COMMENT '消费者信心指数',
        euribor3m      DOUBLE COMMENT '欧元存款利率',
        nr_employed    DOUBLE COMMENT '职工人数',
        y              BIGINT COMMENT '是否有定期存款'
    ) ;
    ```

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

3.  配置MaxCompute数据源。

    1.  在**数据源管理**页面，单击顶部菜单栏的**新增数据源**。

    2.  在**大数据存储**区域，选择**MaxCompute（ODPS）**。

    3.  配置**新增MaxCompute（ODPS）数据源**对话框的各项参数。

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9730409951/p139801.png)

        |参数|描述|
        |--|--|
        |数据源名称|自定义的MaxCompute数据源名称。|
        |数据源描述|数据源的信息描述。|
        |适用环境|        -   **开发**
        -   **生产**
**说明：** 仅标准模式的DataWorks工作空间涉及配置该参数。 |
        |ODPS Endpoint|MaxCompute的网络地址和端口。|
        |Tunnel Endpoint|Tunnel的网络地址和端口。|
        |ODPS项目名称|MaxCompute的项目名称。|
        |AccessKey ID|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |AccessKey Secret|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|

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
        |数据来源|数据源|需要同步的数据源类型及名称。本次试验同步的数据源类型为**ODPS**。

|是|
        |表|需要同步数据的表名称。|是|
        |分区信息|**无分区信息**表示数据源表为非分区表。|否|
        |数据预览|您可以单击**数据预览**，预览源表的数据。|否|
        |数据去向|数据源|接收源数据的数据源类型及名称。本次试验接收数据源的数据源类型为**Hologres**。

|是|
        |表|接收数据的表名称。单击**一键生成目标表**，在Hologres中自动创建一张用于接收同步数据的表。您可以根据业务情况修改建表SQL语句，注意保持字段类型一一对应。

您也可以提前在Hologres中创建用于接收数据的表。

|是|
        |写入模式|        -   **SDK（极速写入）**：通过Hologres的实时数据API写入数据，详情请参见[实时数据API](/cn.zh-CN/数据接入/大数据/实时计算Flink版/Blink独享集群（原产品线）/实时数据API.md)。

**说明：** 如果使用**SDK（极速写入）**写入模式同步数据，则必须使用DataWorks的独享数据集成资源组。

        -   **SQL（INSERT INTO）**：通过PostgreSQL的`INSERT INTO`语句写入数据。
|是|
        |写入冲突策略|        -   **更新（Replace）**：如果写入的新数据与原有的旧数据产生冲突，则使用新数据覆盖旧数据。
        -   **忽略（Ignore）**：如果写入的新数据与原有的旧数据产生冲突，则忽略新数据。
**说明：** **写入冲突策略**仅适用于有主键的表。

|是|

    2.  在**字段映射**区域，您可以选择同步部分或全部字段，示例如下。

        ![740](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4198317951/p96508.png)

        映射类型如下表所示。

        |类型|描述|
        |--|--|
        |同名映射|根据名称建立相应的映射关系，请注意匹配数据类型。|
        |同行映射|在相同行建立相应的映射关系，请注意匹配数据类型。|
        |取消映射|取消已建立的映射关系。|
        |自动排版|根据相应的规律自动排版。|

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

            -   **独享资源组**必须配置为**独享数据集成资源组**。
            -   同步ODPS数据时，必须为独享资源组配置专有网络，详情请参见[独享资源组模式]()。
            -   独享资源组的可用区必须与ODPS专有网络的可用区一致。
            ![独享资源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p143755.png)

    5.  在任务编辑页面，单击顶部菜单栏的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140970.png)图标，保存作业。

    6.  在任务编辑页面，单击顶部菜单栏的![运行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140974.png)图标，运行作业，同步数据。

6.  在Hologres中查看已同步的数据。

    示例SQL语句如下。

    ```
    SELECT * from bank_data;
    ```


## 导入MaxCompute分区表至Hologres

Hologres支持导入MaxCompute的分区表数据，但Hologres的分区与MaxCompute的分区无强映射关系。您可以导入MaxCompute的分区表数据至Hologres的非分区表中，也可以导入MaxCompute的分区表数据至Hologres的分区表中。

1.  准备MaxCompute分区数据。

    准备一张MaxCompute分区表并导入数据，详情请参见[创建和查看表](/cn.zh-CN/快速入门/创建和查看表.md)。您也可以选用已有的MaxCompute分区表。示例建表SQL语句如下。

    ```
    CREATE TABLE odps_table
    (
        shop_name     string,
        customer_id   string,
        total_price   INT 
    )
    PARTITIONED BY (sale_date string ,sale_time string);
    ```

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

3.  配置MaxCompute数据源。

    1.  在**数据源管理**页面，单击顶部菜单栏的**新增数据源**。

    2.  在**大数据存储**区域，选择**MaxCompute（ODPS）**。

    3.  配置**新增MaxCompute（ODPS）数据源**对话框的各项参数。

        ![mysql](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9730409951/p139801.png)

        |参数|描述|
        |--|--|
        |数据源名称|自定义的MaxCompute数据源名称。|
        |数据源描述|数据源的信息描述。|
        |适用环境|        -   **开发**
        -   **生产**
**说明：** 仅标准模式的DataWorks工作空间涉及配置该参数。 |
        |ODPS Endpoint|MaxCompute的网络地址和端口。|
        |Tunnel Endpoint|Tunnel的网络地址和端口。|
        |ODPS项目名称|MaxCompute的项目名称。|
        |AccessKey ID|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey ID。|
        |AccessKey Secret|您可以单击[AccessKey 管理](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak)，获取AccessKey Secret。|

4.  新建离线同步任务。

    1.  在**工作管理空间**界面，单击顶部菜单栏左侧的![图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p139848.png)图标。

    2.  单击**数据集成**。

    3.  在**数据集成**页面，单击**新建离线同步**。

    4.  配置**新建节点**对话框的**节点类型**、**节点名称**和**目标文件夹**参数。

        ![参数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4560129951/p139854.png)

        如果您没有已创建的**业务流程**，则配置**新建节点**之前，您需要先新建**业务流程**，详情请参见[创建业务流程]()。

5.  配置离线同步任务并查询数据。

    配置离线同步任务分为导入MaxCompute分区表数据至Hologres分区表和导入MaxCompute分区表数据至Hologres非分区表场景，步骤如下：

    -   导入MaxCompute分区表数据至Hologres分区表。
        1.  进入离线同步任务的编辑页面，配置**选择数据源**区域的各项参数。

            ![750](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5264633061/p96559.png)

            |类别|参数|描述|
            |--|--|--|
            |数据来源|数据源|需要同步的数据源类型及名称。本次试验同步的数据源类型为**ODPS**。 |
            |表|需要同步数据的表名称。|
            |分区信息|配置分区参数，详情请参见[调度参数]()。|
            |数据预览|您可以单击**数据预览**，预览源表的数据。|
            |数据去向|数据源|接收源数据的数据源类型及名称。本次试验接收数据源的数据源类型为Hologres。 |
            |表|接收数据的表名称。单击**一键生成目标表**，在Hologres中自动创建一张用于接收同步数据的分区表。您可以根据业务情况修改建表SQL语句为创建分区表的SQL语句。

您也可以提前在Hologres中创建用于接收数据的分区表。

示例创建分区表的SQL语句如下。

            ```
begin;
create table holo_test (
 "shop_name" text,
 "customer_id" text,
 "total_price" int8,
 "sale_date" text,
 "sale_time" text
)
partition by  list (sale_date);
commit;
            ```

**说明：** MaxCompute的分区和Hologres的分区并没有强映射关系，并且Hologres当前仅支持一级分区，您可以根据业务情况建立分区。您也可以在Hologres里面提前建好分区父表。 |
            |分区信息|为对应的分区字段赋值。可以赋参数也可以指定固定的值。|
            |写入模式|            -   **SDK（极速写入）**：通过Hologres的实时数据API写入数据，详情请参见[实时数据API](/cn.zh-CN/数据接入/大数据/实时计算Flink版/Blink独享集群（原产品线）/实时数据API.md)。

**说明：** 如果使用**SDK（极速写入）**写入模式同步数据，则必须使用DataWorks的独享数据集成资源组。

            -   **SQL（INSERT INTO）**：通过PostgreSQL的`INSERT INTO`语句写入数据。 |
            |写入冲突策略|            -   **更新（Replace）**：如果写入的新数据与原有的旧数据产生冲突，则使用新数据覆盖旧数据。
            -   **忽略（Ignore）**：如果写入的新数据与原有的旧数据产生冲突，则忽略新数据。
**说明：** **写入冲突策略**仅适用于有主键的表。 |

        2.  在离线同步任务的编辑页面右侧，单击**调度配置**，根据业务情况配置**参数**，示例如下图。

            ![753](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96565.png)

        3.  在数据集成里，MaxCompute表的分区字段将不会显示在**字段映射**区域，您需要手动添加对应的分区字段并与Hologres的字段一一映射，如下图所示。

            ![749](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96539.png)

            映射类型如下表所示。

            |类型|描述|
            |--|--|
            |同名映射|根据名称建立相应的映射关系，请注意匹配数据类型。|
            |同行映射|在相同行建立相应的映射关系，请注意匹配数据类型。|
            |取消映射|取消已建立的映射关系。|
            |自动排版|根据相应的规律自动排版。|

        4.  在**通道控制**区域，配置各项参数。

            ![通道控制](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7335470061/p140955.png)

            |参数|描述|是否必选|
            |--|--|----|
            |任务期望最大并发数|在离线同步任务中，从数据来源端并行读取或写入数据，并存储至数据去向端的最大线程数。|否|
            |同步速率|            -   **限流**：用于保护数据来源端或者数据去向端的读写压力。
            -   **不限流**：系统会提供现有硬件环境下最大的传输性能。
建议您选择**限流**，并根据同步任务合理配置同步速率。

|否|
            |错误记录数|表示脏数据的最大容忍条数。|否|

        5.  配置独享数据集成资源组。
            1.  在任务编辑页面，单击右侧导航栏的**数据集成资源组配置**。
            2.  在**数据集成资源组配置**对话框，**方案**选择**独享数据集成资源组**，**独享数据集成资源组**选择已创建的独享资源组。您也可以单击**新建独享资源组**，新建独享资源组。

                **说明：**

                -   **独享资源组**必须配置为**独享数据集成资源组**。
                -   同步ODPS数据时，必须为独享资源组配置专有网络，详情请参见[独享资源组模式]()。
                -   独享资源组的可用区必须与ODPS专有网络的可用区一致。
                ![独享资源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p143755.png)

        6.  在任务编辑页面，单击顶部菜单栏的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140970.png)图标，保存作业。
        7.  在任务编辑页面，单击顶部菜单栏的![运行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140974.png)图标，运行作业，同步数据。

            您也可以根据业务情况，选择将作业提交到调度，周期性导入数据，详情请参见[依赖关系]()。

        8.  Hologres查询数据。

            在任务完成之后将会生成一个对应的分区子表，您可以查询对应的分区父表或者分区子表，示例查询语句如下。

            ```
            SELECT * FROM holo_test_1585817112344
            ```

            ![755](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96577.png)

    -   导入MaxCompute分区表数据至Hologres非分区表。
        1.  进入离线同步任务的编辑页面，配置**选择数据源**区域的各项参数。

            ![750](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5264633061/p96559.png)

            |类别|参数|描述|
            |--|--|--|
            |数据来源|数据源|需要同步的数据源类型及名称。本次试验同步的数据源类型为**ODPS**。 |
            |表|需要同步数据的表名称。|
            |分区信息|配置分区参数，详情请参见[调度参数]()。|
            |数据预览|您可以单击**数据预览**，预览源表的数据。|
            |数据去向|数据源|接收源数据的数据源类型及名称。本次试验接收数据源的数据源类型为Hologres。 |
            |表|接收数据的表名称。单击**一键生成目标表**，在Hologres中自动创建一张用于接收同步数据的非分区表。您还需要在建表SQL中手动添加MaxCompute中的分区表字段，才能同步MaxCompute的分区信息。

您也可以提前在Hologres中创建用于接收数据的非分区表。 |
            |分区信息|为对应的分区字段赋值。可以赋参数也可以指定固定的值。|
            |写入模式|            -   **SDK（极速写入）**：通过Hologres的实时数据API写入数据，详情请参见[实时数据API](/cn.zh-CN/数据接入/大数据/实时计算Flink版/Blink独享集群（原产品线）/实时数据API.md)。

**说明：** 如果使用**SDK（极速写入）**写入模式同步数据，则必须使用DataWorks的独享数据集成资源组。

            -   **SQL（INSERT INTO）**：通过PostgreSQL的`INSERT INTO`语句写入数据。 |
            |写入冲突策略|            -   **更新（Replace）**：如果写入的新数据与原有的旧数据产生冲突，则使用新数据覆盖旧数据。
            -   **忽略（Ignore）**：如果写入的新数据与原有的旧数据产生冲突，则忽略新数据。
**说明：** **写入冲突策略**仅适用于有主键的表。 |

            **说明：** 建议使用**SDK（极速写入）**导入数据，使用**SDK（极速写入）**需要开通实时数据API，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)开通。

        2.  在离线同步任务的编辑页面右侧，单击**调度配置**，根据业务情况配置**参数**，示例如下图。

            ![753](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96565.png)

        3.  在数据集成里，MaxCompute表的分区字段将不会显示在**字段映射**区域，您需要手动添加对应的分区字段并与Hologres的字段一一映射，如下图所示。

            ![749](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96539.png)

            映射类型如下表所示。

            |类型|描述|
            |--|--|
            |同名映射|根据名称建立相应的映射关系，请注意匹配数据类型。|
            |同行映射|在相同行建立相应的映射关系，请注意匹配数据类型。|
            |取消映射|取消已建立的映射关系。|
            |自动排版|根据相应的规律自动排版。|

        4.  在**通道控制**区域，配置各项参数。

            ![通道控制](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7335470061/p140955.png)

            |参数|描述|是否必选|
            |--|--|----|
            |任务期望最大并发数|在离线同步任务中，从数据来源端并行读取或写入数据，并存储至数据去向端的最大线程数。|否|
            |同步速率|            -   **限流**：用于保护数据来源端或者数据去向端的读写压力。
            -   **不限流**：系统会提供现有硬件环境下最大的传输性能。
建议您选择**限流**，并根据同步任务合理配置同步速率。

|否|
            |错误记录数|表示脏数据的最大容忍条数。|否|

        5.  配置独享数据集成资源组。
            1.  在任务编辑页面，单击右侧导航栏的**数据集成资源组配置**。
            2.  在**数据集成资源组配置**对话框，**方案**选择**独享数据集成资源组**，**独享数据集成资源组**选择已创建的独享资源组。您也可以单击**新建独享资源组**，新建独享资源组。

                **说明：**

                1.  **独享资源组**必须配置为**独享数据集成资源组**。
                2.  同步ODPS数据时，必须为独享资源组配置专有网络，详情请参见[独享资源组模式]()。
                3.  独享资源组的可用区必须与ODPS专有网络的可用区一致。
                ![独享资源](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p143755.png)

            3.  在任务编辑页面，单击顶部菜单栏的![保存](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140970.png)图标，保存作业。
            4.  在任务编辑页面，单击顶部菜单栏的![运行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5560129951/p140974.png)图标，运行作业，同步数据。

                您也可以根据业务情况，选择将作业提交到调度，周期性导入数据，详情请参见[依赖关系]()。

            5.  Hologres查询数据。

                在任务完成之后，您可以直接在Hologres中查询对应的表即可。示例查询语句如下。

                ```
                SELECT * FROM holo;
                ```

                ![756](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6198317951/p96587.png)


