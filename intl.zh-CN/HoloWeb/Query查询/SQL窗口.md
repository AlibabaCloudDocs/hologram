---
keyword: [HoloWeb, SQL窗口, SQL查询]
---

# SQL窗口

本文为您介绍如何使用HoloWeb新建、编辑、删除、复制及重命名SQL查询。

-   阿里云账号注册。
-   实名认证。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/intl.zh-CN/准备工作/购买Hologres.md)。
-   连接Hologres实例至HoloWeb，详情请参见[数据连接](/intl.zh-CN/HoloWeb/连接管理/数据连接.md)。

## 新建SQL查询

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3542488951/p141749.png)

3.  单击**Hologres引擎管理**页面的**登录Hologres数据库**。

4.  在顶部菜单栏，单击**Query查询** \> **SQL窗口**。

    您也可以在左侧导航栏，鼠标右击**我的SQL查询**，选择**新建SQL查询**。

5.  在**新建SQL查询**弹框，配置各项参数。

    ![新建SQL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8042488951/p132716.png)

    |参数|描述|
    |--|--|
    |作业名称|新建的SQL查询名称。|
    |目录|新建SQL查询存放的位置。|
    |连接名称|已配置的连接名称。|
    |数据库名称|Hologres的数据库名称。|

    **说明：** 新建的SQL查询可以直接存放于**我的SQL查询**中，也可以存放于已创建的文件夹中。

6.  单击**确认**。

    您可以在SQL查询窗口，使用标准的PostgreSQL语言进行开发。

    **说明：**

    -   SQL查询对表名称和字段名称的大小写不敏感。如果您需要精确查询名称大小写完全一致的表，则需要使用双引号将该表名称引起来。
    -   默认只能开启一个SQL查询窗口，如果您开启了其他SQL查询窗口，系统会自动中断前一个窗口作业。

## 编辑SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您需要展开文件夹查找。

2.  鼠标右击目标SQL查询，单击**编辑SQL查询**。

3.  您可以根据业务需求，修改**连接名**或**数据库**。同时，您可以在SQL编辑框输入需要执行的SQL语句，单击![运行](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9042488951/p132774.png)图标。

    ![SQL](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9042488951/p132784.png)

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

    您可以通过如下步骤查看示例语句的执行结果：

    1.  在顶部菜单栏，单击**连接管理**。

    2.  在左侧**连接管理**界面，单击![刷新](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9042488951/p132799.png)图标。

    3.  在左侧导航栏**我的连接**中，查找当前SQL查询所在的数据库。

    4.  进入目标数据库**public**模式的**表**中，查看新建的**test**表。

        ![新建表](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9042488951/p132804.png)

4.  单击**保存**。

    单击**保存**后，当前SQL查询会保存所有输入的SQL语句。


## 删除SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您需要展开文件夹查找。

2.  鼠标右击目标SQL查询，单击**删除SQL查询**。

3.  单击**确认**。


## 复制SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您需要展开文件夹查找。

2.  鼠标右击目标SQL查询，单击**复制SQL查询**。

    该操作仅复制当前SQL查询中的SQL语句。您可以将复制的SQL语句粘贴至其他SQL查询窗口。

3.  单击**确认**。


## 重命名SQL查询

1.  在左侧导航栏，单击**我的SQL查询**，查找目标SQL查询。

    如果目标SQL查询存放于文件夹中，您需要展开文件夹查找。

2.  鼠标右击目标SQL查询，单击**重命名**。

3.  您可以根据业务需求，配置**重命名名称**。

4.  单击**确认**。


