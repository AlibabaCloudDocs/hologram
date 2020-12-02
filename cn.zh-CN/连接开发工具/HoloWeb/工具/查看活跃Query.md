---
keyword: [查看活跃Query, HoloWeb]
---

# 查看活跃Query

本文为您介绍如何使用HoloWeb查看活跃Query的详细信息。

-   注册阿里云账号，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。
-   连接Hologres实例至HoloWeb，详情请参见[数据连接](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/数据连接.md)。

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)。

2.  单击**Hologres引擎管理**页面的**登录Hologres数据库**。

3.  在顶部菜单栏左侧，选择相应的地域。

    ![地域](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3542488951/p141749.png)

4.  在顶部菜单栏，单击**工具**。

5.  在左侧导航栏，单击**性能优化** \> **活跃Query**。

6.  配置**活跃Query**页面的各项参数。

    ![活跃query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399048951/p141399.png)

    |参数|描述|
    |--|--|
    |连接名|选择已创建的连接名称。|
    |数据库|所选连接中已创建的数据库名称。|
    |状态|数据库中进程的活动状态。    -   **idle**：表示进程在等待新的客户端命令。
    -   **active**：表示进程正在执行查询操作。
    -   **idle in transaction**：表示进程处于一个事务中，但是当前没有执行查询操作。
    -   **idle in transaction \(aborted\)**：表示进程处于一个事务中，该事务存在语句错误，并且进程当前没有执行查询操作。
    -   **fastpath function call**：表示进程正在执行一个`fast-path`函数。
    -   **disabled**：表示进程的**track activities**被禁用。 |

7.  单击**查询**。

    示例查看数据库中状态为**idle**的查询操作。

    ![查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399048951/p141659.png)

    您可以单击目标查询**操作**列的**详情**，查看当前查询的详细信息。

    ![详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399048951/p141697.png)

    您还可以单击**详情**页面的**复制**，复制当前查询执行的SQL语句。


