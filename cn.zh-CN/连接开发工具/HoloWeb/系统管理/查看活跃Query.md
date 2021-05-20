---
keyword: [查看活跃Query, HoloWeb]
---

# 查看活跃Query

本文为您介绍如何使用HoloWeb查看活跃Query的详细信息。

-   注册阿里云账号，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。

1.  在HoloWeb开发页面的顶部菜单栏，单击**系统管理**。

2.  在左侧导航栏，单击**性能优化** \> **活跃Query**。

3.  配置**活跃Query**页面的各项参数。

    ![活跃query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5973331261/p275086.png)

    |参数|描述|
    |--|--|
    |实例名|选择已登录的实例名称。|
    |数据库|所选连接中已创建的数据库名称。|
    |状态|数据库中进程的活动状态。    -   **idle**：表示进程在等待新的客户端命令。
    -   **active**：表示进程正在执行查询操作。
    -   **idle in transaction**：表示进程处于一个事务中，但是当前没有执行查询操作。
    -   **idle in transaction \(aborted\)**：表示进程处于一个事务中，该事务存在语句错误，并且进程当前没有执行查询操作。
    -   **fastpath function call**：表示进程正在执行一个`fast-path`函数。
    -   **disabled**：表示进程的**track activities**被禁用。 |

4.  单击**查询**。

    示例查看数据库中状态为**idle**的查询操作。

    ![查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5973331261/p141659.png)

    您可以单击目标查询**操作**列的**详情**，查看当前查询的详细信息。

    ![详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2399048951/p141697.png)

    您还可以单击**详情**页面的**复制**，复制当前查询执行的SQL语句。


