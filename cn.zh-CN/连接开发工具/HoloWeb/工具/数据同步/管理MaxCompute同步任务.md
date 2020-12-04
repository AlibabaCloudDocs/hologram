---
keyword: [HoloWeb, 同步MaxCompute数据, 查看同步MaxCompute数据任务信息]
---

# 管理MaxCompute同步任务

HoloWeb支持使用可视化方式一键同步MaxCompute数据，方便您快速查询MaxCompute数据。本文为您介绍如何使用HoloWeb，新建MaxCompute同步数据任务并查看任务的状态信息。

-   阿里云账号注册，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。

## 新建MaxCompute数据同步任务

新建MaxCompute数据同步任务，详情请参见[一键同步MaxCompute数据](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/一键同步MaxCompute数据.md)。

## 查看MaxCompute数据同步任务信息

1.  连接Hologres实例至HoloWeb，详情请参见[数据连接](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/数据连接.md)。

2.  在HoloWeb开发页面的顶部菜单栏，单击**工具**。

3.  在左侧导航栏，单击**数据同步** \> **一键MaxCompute数据同步**。

4.  配置**一键MaxCompute数据同步**页面的各项参数，单击**查询**。

    ![查看同步任务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2749796061/p187700.png)

    参数描述如下表所示。

    |参数|描述|
    |--|--|
    |连接名|已配置的连接名称。|
    |数据库|Hologres的数据库名称。|
    |状态|取值如下：    -   **正在运行**：表示同步任务正在运行中。
    -   **执行成功**：表示同步任务已经执行成功。
    -   **执行失败**：表示同步任务执行失败。
    -   **被中断**：表示同步任务被中断。 |

    如果不配置**连接名**、**数据库**和**状态**，默认查询该账号下所有状态的MaxCompute数据同步任务。

    示例查询所选数据库中**状态**为**执行成功**的同步任务。

    ![执行成功列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4791186061/p186927.png)

    您可以对查看到的任务执行如下操作：

    -   查看任务详情。

        单击同步任务**操作**列的**详情**，进入**MaxCompute数据同步详情**，查看任务的详细信息。

        ![查看同步任务详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4791186061/p186937.png)

    -   重新运行任务。

        单击同步任务**操作**列的**重新运行**，重新执行该同步任务。

        任务重新运行后，如果您希望中断该任务，则可以单击**操作**列的**停止**。

    -   删除同步任务。

        单击同步任务**操作**列的**删除**即可删除该同步任务。

    -   查看同步任务的执行历史。

        单击同步任务**操作**列的**执行历史**，查看该MaxCompute数据同步任务中每条SQL命令的执行情况。例如，SQL语句执行成功或失败，执行语句的耗时时间。

        ![查看执行信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4791186061/p186943.png)

        您可以直接点击相应SQL语句复制使用。


