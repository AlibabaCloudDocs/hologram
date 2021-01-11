# DataWorks权限相关

Hologres与DataWorks深度集成，在使用DataWorks进行Hologres开发时，可能会因为权限问题产生报错。本文内容将为您介绍几个常见权限问题的解决方法。

## 问题汇总

与DataWorks权限相关的问题及解决方法，请单击如下链接查看。

-   [工作空间配置绑定HologresDB按钮为灰色](#section_o30_kyw_u8i)
-   [绑定HologresDB时测试连通性不通过](#section_ka3_d41_yny)
-   [绑定HologresDB时报错“同名计算引擎实例已经存在”](#section_6oq_tpc_ho1)
-   [数据服务查询报错“permission denied”](#section_gd5_o47_zek)

## 工作空间配置绑定HologresDB按钮为灰色

-   问题现象

    当您在[DataWorks管理控制台](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.6.318514f6scc7RP)工作空间配置页面的计算引擎信息页签绑定HologresDB时，**+绑定HologresDB**按钮置灰，无法执行绑定操作。

    ![工作空间绑定Hologres按钮置灰](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6985700161/p211186.png)

-   问题原因
    -   RAM用户不具有工作空间的管理权限。
    -   RAM用户不具有实例的开发权限。
-   解决方法
    -   如果RAM用户不具有工作空间的管理权限，您可以在成员管理页面为当前用户授权，具体操作请参见[添加成员]()。
    -   如果RAM用户不具有实例的开发权限，您可以根据用户权限模式的不同，前往Hologres进行授权。
        -   简单权限模型授权，请参见[新增用户](/cn.zh-CN/了解控制台/Hologres管理控制台/用户管理.mdsection_fl8_01i_h48)。
        -   专家权限模型授权，请参见[专家模式授权](/cn.zh-CN/用户授权及角色管理/专家模式授权.md)。

## 绑定HologresDB时测试连通性不通过

-   问题现象

    登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.6.318514f6scc7RP)配置完成绑定HologresDB弹框参数时，单击**测试连通性**按钮，结果提示![测试不通过](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3642900161/p211229.png)。

-   问题原因

    如下图所示，在绑定HologresDB时需要配置相关信息，包括访问身份、Hologres实例名称和数据库名称等信息，信息填写不正确或账号权限不足均会导致报错。

    ![绑定HologresDB](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3642900161/p211232.png)

-   解决方法
    1.  检查Hologres实例运行状态。

        登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，在左侧导航栏单击实例列表进入Hologres引擎管理页面，您需要检查，确保从下拉框选取的实例为正常运行状态。

    2.  检查数据库是否存在。

        实例运行状态正常的情况下，您可以前往实例详情页的DB管理页签查看当前输入的数据库名称是否存在。否则您需要重新输入已经存在的数据库名称或者新增数据库，具体操作请参见[DB管理](/cn.zh-CN/了解控制台/Hologres管理控制台/DB管理.md)。

    3.  检查当前访问身份权限。

        您可以前往实例详情页的用户管理页签查看当前访问身份的权限。否则您需要添加用户并授权，具体操作请参见[用户管理](/cn.zh-CN/了解控制台/Hologres管理控制台/用户管理.md)。


## 绑定HologresDB时报错“同名计算引擎实例已经存在”

-   问题现象

    当您在绑定HologresDB弹框配置完参数，单击**确定**，报错提示![同名实例已存在](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3642900161/p211350.png)。

-   问题原因

    Hologres的实例显示名称与之前的实例名称重复。

-   解决方法

    修改实例显示名称后再次单击**确定**。


## 数据服务查询报错“permission denied”

-   问题现象

    登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.6.318514f6scc7RP)在数据服务页面查询表，报错提示“Execution failed：ERROR: permission denied for table xxxx”。

-   问题原因

    当前执行数据服务配置的用户账号没有查询表的权限。

-   解决方法
    1.  检查数据服务Hologres数据源配置的账号是否正确。

        您可以登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.6.318514f6scc7RP)，进入数据源管理页面，检查数据源用户账号配置。具体操作请参见[配置Hologres数据源]()。

    2.  检查数据服务Hologres数据源配置的账号是否有查看表的权限。

        您可以登录[DataWorks管理控制台](https://workbench.data.aliyun.com/console?spm=a2c4g.11186623.2.6.318514f6scc7RP)，进入数据源管理页面，检查数据源账号权限配置。具体操作请参见[通过RAM角色授权模式配置数据源]()。

        更多关于DataWorks的权限以及相关授权操作，请参见[权限列表]()。


