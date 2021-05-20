---
keyword: Schema级别的简单权限模型
---

# 基于Schema级别的简单权限模型概述

本章节内容将会为您介绍在Hologres中基于Schema级别的简单权限模型。

## 背景信息

阿里云交互式分析Hologres兼容PostgreSQL，使用与Postgres完全一致的权限系统（简称专家模式，详请参见[专家权限模型](/intl.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)）。专家权限模型授权较为细致，在此基础上，Hologres提供两种简单权限模型供业务使用：

-   简单权限模型：简称SPM，该权限模型授权是以DB为粒度，提供简单方便的授权操作，详请参见[简单权限模型](/intl.zh-CN/账号与权限管理/Hologres权限模型/简单权限模型/简单权限模型概述.md)。
-   基于Schema级别的简单权限模型：简称SLPM，该权限模型基于Schema划分，相比于简单权限模型更为细粒度，若是对权限有严格划分且又希望授权操作简便可以使用该权限模型。

    **说明：** Hologres管理控制台暂未提供SLPM授权操作，您需要使用连接工具对接Hologres实例后使用SQL命令进行授权操作。


## 基于Schema级别的简单权限模型介绍

当您开启了基于Schema级别的简单权限模型时，每个DB会默认产生如下几种权限等级的用户组：

-   超级管理员：Superuser
-   DB管理员：\{db\}.admin
-   开发者：\{db\}.\{schema\}.developer
-   读写者：\{db\}.\{schema\}.writer
-   分析师：\{db\}.\{schema\}.viewer

每个角色对应的权限如下表所示：

|角色|权限|
|--|--|
|Superuser|整个实例的管理员，拥有实例的所有权限。|
|\{db\}.admin|-   某个DB的管理员（admin）。
-   admin组的权限是developer组、writer组及viewer组权限的合集。
-   某个DB的owner，可以删除DB。
-   可管理当前DB的\{db\}.admin、\{db\}.\{schema\}.developer、\{db\}.\{schema\}.writer和\{db\}.\{schema\}.viewer四个用户组的成员，包括新增及移除用户组成员。
-   可以创建用户，并将用户加入某个用户组。
-   可以在该DB创建对象，例如Schema，并可以对对象进行增删改查。
-   可以在DB级别修改DB的配置项。 |
|\{db\}.\{schema\}.developer|-   DB下某个Schema的开发者（developer）。
-   \{db\}.\{schema\}.developer组的权限是\{db\}.\{schema\}.writer组及\{db\}.\{schema\}.viewer组权限的合集。
-   该DB对应Schema中，除系统对象以外的所有表、外表、类表对象（如视图等）、Function、Procedure、Foreign Server、FDW、Type及Language的owner，可以增删查改该Schema中的所有表。
-   可使用DB级别的Foreign Server、FDW、Type及Language等对象。
-   该Schema的USAGE及CREATE权限，可以在任意非系统的Schema中进行创建表，创建视图及创建外表等DDL操作。 |
|\{db\}.\{schema\}.writer|-   DB下某个Schema的读写者（writer）。
-   \{db\}.\{schema\}.writer组的权限是\{db\}.\{schema\}.viewer组权限的合集。
-   Schema中所有表、外表及类表对象（如视图等）的数据，即拥有SELECT、INSERT、UPDATE及DELETE等权限。
-   可以增删查改该Schema下的对象。
-   可以访问或使用Schema的Function、Procedure。
-   可使用DB级别的Foreign Server、FDW、Type及Language等对象。
-   该Schema的USAGE权限，不可进行DDL操作。 |
|\{db\}.\{schema\}.viewer|-   DB的分析师（viewer）。
-   可以读取该Schema下所有表、外表及类表对象（如视图等）的数据，即拥有SELECT权限。
-   可以访问或使用Schema下的Function、Procedure。
-   可使用DB级别的Foreign Server、FDW、Type及Language等对象。
-   该Schema的USAGE权限。 |

