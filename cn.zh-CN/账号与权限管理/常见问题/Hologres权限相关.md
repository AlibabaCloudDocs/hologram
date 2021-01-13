---
keyword: [Hologres, 实例开发权限]
---

# Hologres权限相关

在使用Hologres开发时，可能会因为权限问题产生报错。本文内容将为您介绍几个Hologres实例开发常见权限问题的解决方法。

## 问题汇总

在使用Hologres开发时，需要有实例的具体开发权限，才能在实例内进行操作。与实例权限相关的常见问题，请单击如下链接查看解决方法。

-   [如何选择合适的Hologres权限模型？](#section_0fa_ll5_xi4)
-   [在Hologres实例内操作报错：role “RAM$xxx” doesn't not exsit](#section_p5q_1as_7nu)
-   [连接实例时报错：Cloud authentication failed for access id "xxxx"](#section_l8p_ucf_v8g)
-   [查询表时报错： permission denied for table xxxx](#section_mwo_pph_wmh)
-   [在Hologres实例内操作报错：permission denied for database“xxx”](#section_oqr_45c_kin)
-   [操作表时报错：must be the owner of table xxxx](#section_jst_an7_8a4)

## 如何选择合适的Hologres权限模型？

Hologres具有专家权限模型、简单权限模型和基于Schema级别的简单权限模型（简称SLPM），那么该如何选择合适的Hologres权限模型，具体操作场景说明如下：

-   专家权限模型指的是Postgres原生的权限模型，若您对Postgres及其权限管理已经比较熟悉，可以零学习成本使用专家权限模型实现授权操作。如果您需要细粒度到表级别的权限管理，并且有充足的时间、精力和必要性对每个表、每个用户进行权限赋予、回收等操作可以使用专家权限模型。
-   简单权限模型（简称SPM）指的是DB级别的简单权限管理模型，所有需要访问DB的用户都需要加入到某个用户组中，每个用户组都有DB中任意Schema下对象特定的访问权限。如果您很少采用Schema进行开发，或者只是像使用目录一样采用Schema对表对象进行分类，而无需Schema级别的权限隔离，推荐您使用简单权限模型SPM。
-   基于Schema级别的简单权限模型（简称SLPM），每个Schema都有自己的developer、writer、viewer用户组。若您强依赖Schema级别的用户隔离，表权限隔离，推荐您使用SLPM。

## 在Hologres实例内操作报错：role “RAM$xxx” doesn't not exsit

-   问题现象

    连接开发工具之后，在实例内进行查询等操作时报错提示：role “RAM$xxx” doesn't not exsit。

-   问题原因

    当前RAM用户账号未被创建进实例中。

-   解决方法

    您可以根据业务情况，为当前RAM用户账号授予实例的相关权限，如Superuser。更详细的授权请参见[授予RAM用户实例的开发权限](/cn.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。


## 连接实例时报错：Cloud authentication failed for access id "xxxx"

-   问题现象

    在连接开发工具时报错提示Cloud authentication failed for access id "xxxx"。

-   问题原因

    当前账号填写的密码错误。

-   解决方法

    您需要检查当前连接的密码，需要确保密码为Access key且不包含空格。更多关于Access key的详细内容请参见[创建访问密钥](/cn.zh-CN/准备工作/准备阿里云账号.md)。


## 查询表时报错： permission denied for table xxxx

-   问题现象

    在实例内进行查询等操作时报错提示Execution failed：ERROR: permission denied for table xxxx。

-   问题原因

    当前账号没有对应表的查看权限。

-   解决方法

    您可以根据当前授权模式的不同，选择对应的解决方法。

    -   专家权限模式授权：执行如下命令为用户授权，其中p4\_UID为RAM用户的账号信息。更多专家权限模式的信息，请参见[专家权限模型](/cn.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)。

        ```
        grant select on table tablename to "p4_UID";
        ```

    -   简单权限模型：将当前用户加入viewer及以上用户组。具体操作请参见[用户授权](/cn.zh-CN/了解控制台/Hologres管理控制台/DB管理.md)。

## 在Hologres实例内操作报错：permission denied for database“xxx”

-   问题现象

    连接开发工具之后，在实例内进行查询等操作时报错提示FATAL:permission denied for database“xxx” detail：user does not have CONNECT privilege。

-   问题原因

    当前账号只被创建进了实例中，但是没有对应实例的开发权限。

-   解决方法

    您可以根据业务情况，为当前用户账号授予实例的相关权限，如Superuser。更详细的授权请参见[授予RAM用户实例的开发权限](/cn.zh-CN/账号与权限管理/授权操作/授予RAM用户实例的开发权限.md)。


## 操作表时报错：must be the owner of table xxxx

-   问题现象

    在实例内进行操作时报错提示：must be the owner of table xxxx。

-   问题原因

    当前RAM用户不是表的owner，无法创建分区子表或者执行删除表的操作。

-   解决方法

    您可以根据当前授权模式的不同，选择对应的解决方法。

    -   专家权限模式授权：执行如下命令将表的owner授予当前账号，其中p4\_UID为RAM用户的账号信息。更多专家权限模式的信息，请参见[专家权限模型](/cn.zh-CN/账号与权限管理/Hologres权限模型/专家权限模型.md)。

        ```
        alter table tablename owner to "p4_UID";
        ```

    -   简单权限模型：将当前用户加入developer及以上用户组。具体操作请参见[用户授权](/cn.zh-CN/了解控制台/Hologres管理控制台/DB管理.md)。

