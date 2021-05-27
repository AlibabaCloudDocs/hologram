---
keyword: [HoloWeb, SQL窗口, SQL查询]
---

# SQL窗口

本文为您介绍如何使用HoloWeb新建、编辑、删除、复制及重命名SQL查询。

存在已登录实例，请参见[登录实例](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/登录实例.md)。

## 新建SQL查询

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4547818061/p141749.png)

3.  单击**Hologres引擎管理**页面的**前往HoloWeb**，进入HoloWeb开发页面。

4.  在HoloWeb开发页面的顶部菜单栏，单击**SQL编辑器** \> **新增SQL窗口**，单击**确认**。

    您也可以在左侧导航栏，鼠标右击**我的SQL查询**，选择**新建SQL查询**。

    在**新建SQL查询**对话框，配置各项参数，系统默认会自动填充当前的实例名和数据库名。

    ![新建SQL查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8922231261/p275010.png)

    参数描述如下表所示。

    |参数|描述|
    |--|--|
    |作业名称|新建的SQL查询名称。|
    |目录|新建SQL查询存放的位置。默认目录为**我的SQL查询**，您也可以选择存放在已创建的文件夹中。|
    |实例名|您可以从当前已存在实例的下拉列表中选择目标实例名称。**说明：** 当前实例列表会展示已登录和未登录实例名称。当您选择的为未登录实例时，您可以在执行完SQL语句并保存后，登录该实例即可在列表中看到新增的内容。 |
    |数据库名称|Hologres当前实例下的数据库名称。|

5.  新增生成的SQL会展示在左侧**我的SQL查询**列表中。您可以在页面的**表目录**列表中选择目标表，查看对应表的字段名和类型。您也可以在SQL编辑区域使用标准的PostgreSQL语言进行开发。

    **说明：** SQL查询对表名称和字段名称的大小写不敏感。如果您需要精确查询名称大小写完全一致的表，则需要使用双引号将该表名称引起来。

    ![新增SQL窗口](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3883331261/p275030.png)

6.  单击**运行**，执行输入的SQL语句。

7.  单击**保存**，保存当前SQL查询窗口的相关内容。


## 表目录

在您完成新建SQL查询和编辑SQL查询之后，您可以查看表目录，方便您查看和了解表内部的结构。

1.  在HoloWeb开发页面的顶部菜单栏，单击**SQL编辑器** \> **新增SQL窗口**，单击**确认**。

    您也可以在**我的SQL查询**中打开目标**SQL查询**。

    ![新增SQL窗口](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3883331261/p275030.png)

2.  您可以在页面的**表目录**列表查看所有的内部表。选择目标表，查看对应表的字段名和类型。

    您也可以在SQL编辑区域使用标准的PostgreSQL语言进行开发。例如，您可以在SQL编辑区域运行新增表语句，并单击![刷新](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2342488951/p117260.png)图标，在表目录下方可以展示表结构。

    **说明：** SQL查询对表名称和字段名称的大小写不敏感。如果您需要精确查询名称大小写完全一致的表，则需要使用双引号将该表名称引起来。


## 编辑SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您可以在**Query查询**下方的搜索框输入目标SQL查询名称。

    ![文件夹快速查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9578131261/p274941.png)

2.  鼠标双击目标SQL查询，可以**编辑SQL查询**。

3.  您可以在SQL编辑框输入需要执行的SQL语句，单击**运行**。

    示例SQL语句如下。

    ```
    CREATE TABLE test (
     "id" bigint NOT NULL,
     "name" text NOT NULL,
     "age" bigint,
     "class" text NOT NULL,
    PRIMARY KEY (id)
    );
    ```

    您可以通过[表目录](#step_3rq_2i2_7zf)方式查看示例语句的执行结果。

4.  单击**保存**。保存成功后，当前SQL查询会保存所有输入的SQL语句。


## 删除SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您可以在**Query查询**下方的搜索框输入目标SQL查询名称。

    ![文件夹快速查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9578131261/p274941.png)

2.  鼠标右击目标SQL查询，单击**删除SQL查询**。

3.  单击**确认**。


## 复制SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您可以在**Query查询**下方的搜索框输入目标SQL查询名称。

    ![文件夹快速查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9578131261/p274941.png)

2.  鼠标右击目标SQL查询，单击**复制SQL查询**。

    该操作仅复制当前SQL查询中的SQL语句。

3.  您可以将复制的SQL语句粘贴至其他SQL查询窗口使用。


## 重命名SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您可以在**Query查询**下方的搜索框输入目标SQL查询名称。

    ![文件夹快速查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9578131261/p274941.png)

2.  鼠标右击目标SQL查询，单击**重命名**。

3.  您可以根据业务需求，配置**重命名名称**。

4.  单击**确认**。


