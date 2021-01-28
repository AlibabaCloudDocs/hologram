---
keyword: [HoloWeb, SQL语句执行历史]
---

# 查看HoloWeb中SQL语句的执行历史

HoloWeb为您提供当前登录账号在HoloWeb中执行SQL语句的历史记录，方便您查看目标任务执行的语句，以及语句的执行状态及耗时等信息，并根据查询到的信息，快速判断执行的SQL语句是否合理，以便优化不合理语句。本文为您介绍如何查询SQL语句的执行历史信息。

-   阿里云账号注册，详情请参见[t12832.md\#]()。
-   实名认证，详情请参见[t12833.md\#]()或[t12834.md\#]()。
-   开通交互式分析Hologres，详情请参见[购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)。

## 注意事项

查询的SQL语句执行历史为当前登录账号在HoloWeb的执行历史，不包含其他账号以及其他工具的执行历史。

## 查看SQL语句的执行历史

1.  连接Hologres实例至HoloWeb，详情请参见[数据连接](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/数据连接.md)。

2.  在HoloWeb开发页面的顶部菜单栏，单击**系统管理**。

3.  在左侧导航栏，单击**执行历史**。

4.  在**执行历史**页面，配置**连接名**和**数据库**。

5.  单击**刷新**，查看目标数据库中所有已执行的SQL语句及其执行状态、耗时等相关信息。

    ![执行状态](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3761896061/p186995.png)

    查询到的参数信息描述如下。

    |参数|描述|
    |--|--|
    |开始时间|SQL语句开始执行的时间。|
    |连接名|实例的连接名称。|
    |数据库|Hologres的数据库名称。|
    |SQL|执行的SQL语句详情。|
    |状态|取值如下：    -   执行成功。
    -   执行失败。您可以点击![报错](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7871896061/p187715.png)可以查看失败原因。 |
    |行数|执行SQL语句进行查询时返回的结果行数。HoloWeb执行SQL语句时，默认在SQL语句后添加`Limit 200`限制，如果您需要展示更多的行数，您可以使用`Limit N`语句，设置N的取值大于200即可。 |
    |耗时|当前SQL语句的执行时间。如果SQL语句的执行时间不符合业务预期，您可以结合管理控制台的监控指标进行分析，对相应的任务进行调优，详情请见[Hologres管控台的监控指标](/cn.zh-CN/监控与告警/Hologres管控台的监控指标.md)和[优化SQL语句](/cn.zh-CN/最佳实践/性能调优/优化内部表的性能.md)。 |

    如果不选择**连接名**和**数据库**，则默认展示当前账号在HoloWeb的所有SQL执行历史。

    您也可以在**搜索SQL**框，输入需要查询的SQL语句，单击![查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2385186061/p186998.png)查询指定语句的执行历史。

    例如，查询目标数据库中所有已执行的`SELECT`语句。

    **说明：** HoloWeb支持使用`%`模糊匹配表名称。

    ![select历史执行](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3385186061/p187002.png)


