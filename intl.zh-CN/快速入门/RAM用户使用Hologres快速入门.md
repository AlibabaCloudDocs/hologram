---
keyword: [创建子账号, 授权子账号, Hologres, 创建RAM用户, RAM用户授权]
---

# RAM用户使用Hologres快速入门

本文为您介绍主账号如何创建并授权RAM用户，从而实现RAM用户连接并使用Hologres。

系统默认设置购买实例的阿里云主账号为超级管理员Superuser。Superuser拥有该实例的所有权限。

其他用户必须经过阿里云主账号授权才可以访问实例。

RAM用户包括以下两种权限控制：

-   RAM权限

    RAM权限为可选权限。RAM用户被授予相应RAM权限后，可以在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)管控实例，例如购买或删除实例、升降配实例资源、修改网络类型以及查看实例信息等。

-   实例开发权限

    实例开发权限为必选权限。RAM用户必须经过阿里云主账号授予实例的开发权限后，才能连接实例并进行数据开发。


Hologres支持使用简单权限模型SPM、专家权限模型和基于Schema的简单权限模型SLPM为RAM用户授权。几种权限模型的介绍如下：

-   简单权限模型SPM（推荐）

    简单权限模型是Hologres基于PostgreSQL的授权体系，为提升用户体验，制定的一种粗粒度的权限模型，详情请参见[简单权限模型概述](/intl.zh-CN/账号与权限管理/Hologres权限模型/简单权限模型/简单权限模型概述.md)。

-   专家权限模型

    专家权限模型是与标准PostgreSQL语句相同的授权体系，简称专家模式。您可以按照标准的PostgreSQL授权语句为RAM用户授权，详情请参见[专家权限模型](/intl.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)。

-   基于Schema的简单权限模型SLPM

    该权限模型基于Schema划分，相比于简单权限模型更为细粒度，若是对权限有严格划分且又希望授权操作简便可以使用该权限模型，详情请参见[基于Schema级别的简单权限模型概述](/intl.zh-CN/账号与权限管理/Hologres权限模型/基于Schema级别的简单权限模型/基于Schema级别的简单权限模型概述.md)。


## 创建RAM用户

如果您已有RAM用户，请直接跳过该步骤。

1.  使用主账号登录[阿里云官网](https://www.alibabacloud.com/)。

2.  使用主账号登录[RAM管理控制台](https://ram.console.aliyun.com/overview)。

3.  在左侧导航栏的**人员管理**菜单下，单击**用户**。

4.  单击**创建用户**。

    您也可以单击**添加用户**，一次性创建多个RAM用户。

5.  在**用户账号信息**区域下，输入**登录名称**和**显示名称**。

6.  在**访问方式**区域下，选择**控制台访问**。

7.  设置RAM用户的登录密码。

8.  单击**确定**。

    首次创建RAM用户需要进行手机验证。


## 创建RAM用户的访问密钥

访问密钥对您在Hologres中创建的任务顺利运行非常重要，因此主账号需要为RAM用户创建AccessKey。步骤如下：

1.  在左侧导航栏的**人员管理**菜单下，单击**用户**。

2.  在**用户登录名称/显示名称**列表下，单击目标RAM用户名称。

3.  在**用户 AccessKey**区域下，单击**创建 AccessKey**。

    您可以点击**下载CSV文件**或**复制**，将AccessKey保存至本地。

4.  单击**关闭**。

    **说明：**

    -   AccessKey Secret只在创建时显示，不提供查询，请妥善保管。
    -   如果AccessKey泄露或丢失，您需要创建新的AccessKey，最多可以为每个RAM用户创建两个AccessKey。

## 授权RAM用户

-   授予RAM用户权限。

    主账号授予RAM用户相应的权限后，RAM用户可以在Hologres管理控制台执行查看、购买或删除实例等操作。您可以登录访问控制管控台，查找目标RAM用户，为其添加权限。如果您需要为RAM用户授予拥有管控台查看实例信息的所有权限，为其添加**AliyunHologresFullAccess**和**AliyunRAMReadOnlyAccess**权限。

    更细粒度的权限授予，详情请参见[授予RAM用户权限](/intl.zh-CN/账号与权限管理/授予RAM用户权限.md)。

-   授予RAM用户实例开发权限。

    RAM用户必须经过主账号授予实例的开发权限后，才能在Hologres实例中进行数据开发。您可以登录Hologres管控台，访问HoloWeb，在用户管理页面新增用户并授予权限。更多关于RAM用户实例授权，详情请参见[授予RAM用户实例的开发权限](/intl.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。


## RAM用户使用Hologres

完成授权的RAM用户可以连接Hologres实例并进行相关开发。您可以在Hologres管控台使用HoloWeb进行可视化开发，更多关于HoloWeb的操作，详情请参见[HoloWeb快速入门](/intl.zh-CN/快速入门/HoloWeb快速入门.md)。

## 查看RAM用户的权限

您可以通过如下方式查看RAM用户的权限：

-   使用管理控制台查看RAM用户的权限。

    1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，在顶部菜单栏选择相应地域。
    2.  您可以在实例详情页左侧导航栏，单击**用户管理**自动登录并跳转到HoloWeb的对应页面。
    3.  查看RAM用户的权限。
    如果您的数据库已经开启简单权限模型SPM或者基于Schema的简单权限模型SLPM，您可以在**DB授权**页面，单击目标数据库的**用户授权**，查看RAM用户已加入的用户组。

-   使用SQL语句查看RAM用户的权限。

    连接开发工具至Hologres实例后，您可以使用如下SQL语句查看RAM用户的权限。

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_ID'; --查看某个成员拥有的角色。
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


