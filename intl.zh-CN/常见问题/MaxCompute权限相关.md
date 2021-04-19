---
keyword: [Hologres, 查询报错]
---

# MaxCompute权限相关

使用Hologres加速查询MaxCompute表数据时，可能会因为权限问题产生报错。本文内容将为您介绍几个常见权限问题的解决方法。

## 问题汇总

与MaxCompute权限相关的问题及解决方法，请单击如下链接查看。

-   [查询外部表报错：You have NO privilege 'odps:Select' on xxx](#section_o30_kyw_u8i)
-   [查询外表报错：The sensitive label of column 'xxx' is 2, but your effective label is 0](#section_ka3_d41_yny)
-   [跨project访问MaxCompute表报错：You have NO privilege 'odps:Select' on xxx](#section_6oq_tpc_ho1)
-   [创建外部表报错：You have NO privilege 'odps:List' on xxx](#section_ia3_abx_y9w)
-   [创建外表时报错Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx](#section_gd5_o47_zek)

## 查询外部表报错：You have NO privilege 'odps:Select' on xxx

-   问题现象

    当您在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)创建外部表之后，查询外部表时报错提示“You have NO privilege 'odps:Select' on xxx”。

-   问题原因

    当前账号不具备MaxCompute表的查询（Select）权限。

-   解决方法

    需要MaxCompute管理员在MaxCompute中授予当前账号查询表（Select）的权限，具体操作请参见[授权](/intl.zh-CN/安全管理/安全管理详解/用户及授权管理/授权.md)。


## 查询外表报错：The sensitive label of column 'xxx' is 2, but your effective label is 0

-   问题现象

    当您在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)创建外部表之后，查询外部表时报错提示“The sensitive label of column 'xxx' is 2, but your effective label is 0”。

-   问题原因

    当前账号只有MaxCompute表的部分字段权限。

-   解决方法

    您可以选择如下三种方法中的一种来解决该问题：

    -   （推荐）建议您[提交工单](https://workorder-intl.console.aliyun.com/)将当前实例版本升级至V0.8。
    -   您可以在执行的Query前增加如下参数解决报错问题。

        ```
        set hg_experimental_enable_odps_executor=on; 
        set hg_experimental_enable_query_master=on;
        ```

    -   获取MaxCompute表全部字段的权限，具体操作请参见[授权](/intl.zh-CN/安全管理/安全管理详解/用户及授权管理/授权.md)。

## 跨project访问MaxCompute表报错：You have NO privilege 'odps:Select' on xxx

-   问题现象

    当前账号已经具备MaxCompute表查询权限，但是跨project访问MaxCompute表报错”You have NO privilege 'odps:Select' on xxx“。

-   问题原因

    若是当前账号已经具备MaxCompute已经有表的查询权限，跨project访问MaxCompute表还是报错，则MaxCompute当前可能采用的是package授权，您需要添加SQL语句解决该问题。

-   解决方法

    当前如果MaxCompute是project授权方式，在Hologres中，您可以在SQL前添加如下参数解决。

    ```
    // V0.7版本的实例请执行以下语句授权
    set seahawks.seahawks_internal_current_odps_project='holoprojectname';
    //V0.8版本的实例请执行以下语句授权
    set hg_experimental_odps_current_project_name = 'holoprojectname';
    ```


## 创建外部表报错：You have NO privilege 'odps:List' on xxx

-   问题现象

    当您在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)使用HoloWeb或HoloStudio可视化创建外部表时报错提示“You have NO privilege 'odps:List' on xxx”。

-   问题原因

    当前账号在MaxCompute中不具备查看所有表（List）的权限。

-   解决方法
    -   需要MaxCompute管理员在MaxCompute中授予当前账号查看所有表（List）的权限，具体操作请参见[授权](/intl.zh-CN/安全管理/安全管理详解/用户及授权管理/授权.md)。
    -   使用SQL语句创建外部表查询数据，具体操作请参见[通过创建外部表加速查询MaxCompute数据](/intl.zh-CN/数据接入/离线同步/MaxCompute/通过创建外部表加速查询MaxCompute数据.md)。

## 创建外表时报错Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx

-   问题现象

    当您在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)使用HoloWeb创建外部表时报错提示“Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx”。

-   问题原因

    当前MaxCompute集群设置了白名单访问，Holoweb不在白名单内。

-   解决方法

    当MaxCompute项目开启白名单功能时，仅允许白名单内的设备访问项目空间；非白名单内的设备访问项目空间时，即使拥有正确的AccessKey ID及AccessKey Secret，也无法通过鉴权。因此需要将报错信息中的IP设置为白名单才可以创建外表，具体操作请参见设置IP白名单。


