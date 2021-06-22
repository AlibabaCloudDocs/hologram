---
keyword: [Hologres, IP白名单, Holoweb]
---

# IP白名单

为保障Hologres的安全稳定，Hologres支持在Holoweb中设置IP白名单来进行访问管理，本文将指导您在Hologres中设置IP白名单。

## 注意事项

在Holoweb中设置IP白名单之前，您需要注意如下事项：

-   仅Hologres V0.9及以上版本支持设置IP白名单，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或加入在线支持钉钉群申请升级实例。
-   [购买Hologres](/cn.zh-CN/准备工作/购买Hologres.md)实例成功后，若是没有设置IP白名单， 则默认对所有网络开放。
-   仅支持实例管理员（Superuser）设置IP白名单。
-   在Holoweb配置数据连接时，需要将连接的登录方式设置为当前用户免密登录，才可以为当前连接配置IP白名单。连接Hologres实例配置操作指导，请参见[连接Hologres实例](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。

    ![当前用户免密登录](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9962371161/p232918.png)

-   为避免设置白名单之后，Hologres管理控制台、HoloStudio或Holoweb不能访问。因此，您需要按照[新增IP白名单](#section_hsq_vod_xpd)的操作指导，将其对应的分组加入到IP白名单，保证正常访问。

    |分组|说明|
    |--|--|
    |HologresConsoleGroup|加入该分组后可以访问Hologres管理控制台。|
    |HoloStudioGroup|加入该分组后可以访问HoloStduio。|
    |HoloWebGroup\_xxxx|加入该分组后可以访问HoloWeb。|


## 新增IP白名单

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，在顶部菜单栏左侧，选择相应的地域。

2.  单击**Hologres引擎管理**页面的**登录Hologres数据库**，进入**HoloWeb**开发界面。

3.  在Holoweb页面，在系统管理页面的左侧导航栏，选择**数据安全** \> **IP白名单**。

4.  在页面右上角单击**新增IP白名单**，配置如下参数信息。

    ![IP白名单](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2790321161/p229410.png)

    |参数|说明|
    |--|--|
    |分组|自定义的分组名称。当您将连接的登录方式设置为当前用户免密登录后，Hologres管理控制台、HoloStudio或Holoweb也必须加入到IP白名单内，否则其功能将不可用。在分组下拉框中选择各自对应的分组名称即可。 |
    |数据库限制|从下拉框中选择需要设置白名单的数据库。如果需要设置当前实例所有的DB，可以选择ALL。|
    |用户限制|从下拉框中选择需要设置白名单的用户。如果需要设置当前实例所有的用户，可以选择ALL。|
    |IP地址|设置白名单的IP地址。配置信息如下：    -   所有IP地址：填写为ALL。
    -   指定IP地址：如192.168.0.1，允许192.168.0.1的IP地址访问。
    -   指定IP段：如192.168.0.0/24，允许从192.168.0.1到192.168.0.255的IP地址访问。
    -   多个IP设置：换行展示。 |

5.  单击**确认**，完成配置。白名单设置成功后，可以允许您在IP白名单的范围内进行操作。


## 编辑IP白名单

如果您需要修改IP白名单地址信息，可以对IP白名单进行修改操作。当前仅允许更改IP地址，若是要更改数据库、用户限制等信息，请您新建IP白名单。

**说明：** 仅实例管理员（Superuser）可以编辑IP白名单。

1.  在Holoweb页面，在系统管理页面的左侧导航栏，选择**数据安全** \> **IP白名单**。

2.  在IP白名单管理页面，单击目标IP白名单右侧的**编辑**。

3.  在编辑IP白名单页面，修改IP地址信息。IP信息的配置内容，请参见[新增IP白名单](#section_hsq_vod_xpd)。

4.  单击**确认**，完成配置。


## 删除IP白名单

如果您不再需要设置的IP白名单信息，可以对IP白名单执行删除操作。如果您删除所有的IP白名单，则默认不设置白名单。

**说明：** 仅实例管理员（Superuser）可以编辑IP白名单。

1.  在Holoweb页面，在系统管理页面的左侧导航栏，选择**数据安全** \> **IP白名单**。

2.  在IP白名单管理页面，单击目标IP白名单右侧的**删除**。

3.  单击**确认**，删除完毕。


