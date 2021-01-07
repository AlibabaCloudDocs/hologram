---
keyword: [迁移Lightning至MaxCompute BI加速版, 最佳实践, Hologres]
---

# 迁移Lightning至共享集群概述

由于Lightning后期会逐步下线，下线后不再继续维护。因此，建议您选择共享集群（MaxCompute BI加速版）处理业务。本文将为您介绍将原MaxCompute Lightning服务迁移至Hologres共享集群（MaxCompute BI加速版）的相关流程，方便您进行业务迁移。

## Lightning与共享集群（MaxCompute BI加速版）操作流程

Lightning与共享集群（MaxCompute BI加速版）的操作流程分别如下图所示。

![迁移Lighting至共享集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0031579061/p184902.png)

## 操作流程说明

如下步骤为您介绍将原MaxCompute Lightning服务迁移至Hologres共享集群（MaxCompute BI加速版）并对接BI工具的简单操作流程。

1.  开通共享集群（MaxCompute BI加速版）并在Hologres管理控制台查看实例的详细信息。

2.  新建数据库并进行授权。

3.  使用共享集群（MaxCompute BI加速版）实例连接原有的开发工具进行数据开发。

4.  新建外部表加速查询MaxCompute。

    共享集群（MaxCompute BI加速版）是通过新建外部表的方式加速查询MaxCompute数据，因此在查询之前，您必须先创建一张与之映射的外部表。

5.  连接BI工具进行可视化分析。

    共享集群（MaxCompute BI加速版）兼容PostgreSQL生态，支持直接对接各种BI分析工具。连接BI工具时需要您修改原Lightning服务的Endpoint为共享集群（MaxCompute BI加速版）实例的Endpoint，然后再进行可视化分析。常见的BI工具请参见[BI工具概述](/cn.zh-CN/BI分析及可视化/概述.md)。


更多详细的操作指导请参见：

-   [迁移Lightning至共享集群（数据服务场景）](/cn.zh-CN/共享集群（MaxCompute BI加速版）/迁移Lighting至共享集群（MaxCompute BI加速版）/迁移Lightning至共享集群（数据服务场景）.md)
-   [迁移Lightning至共享集群（Quick BI场景）](/cn.zh-CN/共享集群（MaxCompute BI加速版）/迁移Lighting至共享集群（MaxCompute BI加速版）/迁移Lightning至共享集群（Quick BI场景）.md)

## 迁移用户权限

共享集群（MaxCompute BI加速版）的权限采用与Lightning服务不同的权限控制方式，需要您重新给用户授予相关权限，具体操作请参见[t1917293.dita\#task\_2569531](/cn.zh-CN/用户授权及角色管理/子账号使用Hologres/授予子账号实例的开发权限.md)。

