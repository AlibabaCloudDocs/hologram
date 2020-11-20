# HoloStudio快速入门

DataWorks-HoloStudio作为基于交互式分析（Hologres）引擎的一站式开发平台，在为您提供SQL界面开发的同时，也支持使用可视化UI界面完成Hologres开发，同时也与DataWorks深度集成，方便您完成数据的周期性调度。

本小节主要为您介绍使用DataWorks-HoloStudio的操作步骤，通过以下简单步骤的综述，帮助您快速掌握HoloStudio的基本使用流程。

![678](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3664633061/p94251.png)

**说明：** 如果您更倾向于使用psql客户端进行Hologres开发，可参见[psql快速入门](/intl.zh-CN/快速入门/psql快速入门.md)。

## 前提条件

在开始使用DataWorks-HoloStudio之前，请确保您已经成功开通Hologres实例，详情请见[计费方式](/intl.zh-CN/产品定价/计费方式.md)

## 操作步骤

使用DataWorks-HoloStudio进行开发的完整操作步骤如下：

1.  **新建数据库（推荐）**

    实例购买成功后，系统会自动生成一个名叫postgres的数据库，该数据库分配的资源少且仅供管理用途，实际业务需要您新建一个DB。为节省SQL操作，[Hologres管控台](https://hologram.console.aliyun.com/#/instance)支持直接可视化新建DB。

    进入管理控制台的实例详情页，单击左侧导航栏的**DB管理** \> **新增Database**，输入DB名即可新建成功。

    ![680](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7476385061/p94256.png)

    **说明：** Hologres兼容PostgreSQL，使用与PostgreSQL完全一致的权限模型（简称专家模式）。基于对业务理解和实践经验，Hologres抽象了一套简单权限模型（SPM）来简化授权操作，详情可以参见[简单权限模型概述](/intl.zh-CN/用户授权及角色管理/简单权限模型/简单权限模型概述.md)。

2.  **创建或绑定工作空间**

    绑定Hologres实例，分为以下两种情况：

    -   **无工作空间**

        如果您没有使用过DataWorks工作空间，需要在[DataWorks管控台](https://workbench.data.aliyun.com/?#/projectlist)创建一个同地域的工作空间并绑定。

        在DataWorks管理控制台单击左侧导航栏的**工作空间列表**，并单击**创建工作空间**，填写工作空间信息。您可以根据自身需求，选择是否同时开通其它计算引擎服务，详情请参见[新建工作空间绑定交互式分析实例](/intl.zh-CN/基于HoloStudio的开发/绑定交互式分析实例.md)，并填写实例配置信息。

        **说明：** Hologres在标准DataWorks工作空间分开发环境和生产环境，但由于当前HoloStudio暂不支持环境切换，导致生产环境的数据在HoloStudio不可查询，建议开发和生产绑定同一个DB。

        ![844](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p100528.png)

    -   已有工作空间

        如果您已创建DataWorks工作空间，可以直接使用该工作空间绑定Hologres实例。具体操作步骤如下：

        1.  进入Holostudio

            在[Hologres管控台](https://hologram.console.aliyun.com/#/instance)选择实例开通的region，单击页面上方的**前往DataWorks数仓开发**，即可进入对应的工作空间。

            ![681](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7476385061/p94258.png)

        2.  绑定实例

            进入Holostudio后，单击右上角**小扳手**，进入**工作空间配置**页面，单击**绑定HologresDB**进行实例绑定。更多关于绑定实例的详细操作步骤参见[选择已有工作空间绑定交互式分析实例](/intl.zh-CN/基于HoloStudio的开发/绑定交互式分析实例.md)。

            ![845](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p100529.png)

            绑定完成之后，返回**HoloStudio**，在PG管理页面单击**刷新**，既可看到已成功绑定的数据库。

            ![683](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p94262.png)

3.  **授权子账号（可选）**

    若您需要给子账号授权，可以按照以下步骤操作：

    1.  创建子账号

        在[Hologres管控台](https://hologram.console.aliyun.com/#/instance)实例详情页单击左侧导航栏**用户管理** \> **新增用户**，勾选需要授权的子账号，并选择**用户类型**。可以直接授权为superuser（拥有实例所有权限，无需额外授权），也可以授权为普通用户（访问Hologres还需要额外授权）。

        ![676](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p94190.png)

    2.  授权子账号

        若您的子账号是普通用户normal，还需要给子账号授权才能访问Hologres实例。您可以选择使用专家权限模式或者简单权限模型（推荐）为子账号授权。

        -   **简单权限模型（推荐）**

            在管控台新建DB并开启简单权限模型之后，可以直接将子账号加入对应的用户组来授权。

            ![677](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p94191.png)

            关于简单权限模型的使用可以参见[t1877880.md\#](/intl.zh-CN/用户授权及角色管理/简单权限模型/简单权限模型的使用.md)。

        -   **专家模式授权**

            专家权限模式授权采用的是标准的PostgreSQL授权语句，您可以参照以下语句进行授权：

            ```
            GRANT SELECT, INSERT ON TABLE TABLENAME TO "云账号ID/云邮箱";//若是子账号还需要在云账号ID前加"p4_"，即"p4_id"
            GRANT SELECT, INSERT ON ALL TABLES IN SCHEMA public TO "云账号ID/云邮箱";//若是子账号还需要在云账号ID前加"p4_"，即"p4_id"
            ```

            更多关于专家权限模型的使用可以参见[专家模式授权](/intl.zh-CN/用户授权及角色管理/专家模式授权.md)。

        -   使用Holostudio开发

            若您的子账号需要使用HoloStudio完成Hologres开发，还需要将子账号添加进DataWorks的项目工作空间。

            在DataWorks工作空间配置页面单击**工作空间管理** \> **成员管理** \> **添加成员**，并给成员授予一定的项目权限。

            ![685](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0463207951/p94268.png)

4.  **HoloStudio开发**

    进入HoloStudio之后，即可进行Hologres开发：

    -   SQL Console纯SQL开发，详情请参见[SQL Console](/intl.zh-CN/基于HoloStudio的开发/SQL Console.md)。
    -   表管理可视化UI一键建表、数据预览，详情请参见[创建及管理内部表](/intl.zh-CN/基于HoloStudio的开发/PG管理/表管理.md)。
    -   一键创建MaxCompute外部表、查询数据，详情请参见[创建及管理外部表](/intl.zh-CN/基于HoloStudio的开发/PG管理/外部表.md)。
    -   与DataWorks深度集成，实现周期性调度，详情请参见[Hologres开发：周期性调度](/intl.zh-CN/基于HoloStudio的开发/数据开发/Hologres开发：周期性调度.md)。

关于Hologres的更多开发工具请参见[概述](/intl.zh-CN/常见开发工具/概述.md)。

