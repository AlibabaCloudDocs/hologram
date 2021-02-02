---
keyword: [子账号授权, RAM用户]
---

# 授予RAM用户权限

本文为您介绍主账号如何为RAM用户授予权限，从而让RAM用户实现在Hologres管理控制台执行查看、购买或删除实例等操作。

访问控制（Resource Access Management，简称RAM）是阿里云提供的权限管理系统。

RAM主要用于控制账号系统的权限。

您可以使用RAM在主账号的权限范围内创建RAM用户，并为不同的RAM用户授予不同的权限，例如购买或删除实例、升降配实例资源、修改网络类型以及查看实例信息等。

RAM用户实例开发权限的权限控制如下：

-   如果主账号没有授予RAM用户权限，则RAM用户无法在管理控制台查看或操作实例。
-   主账号可以直接授予RAM用户实例的开发权限。RAM用户即使无法在管理控制台操作实例，也可以正常连接开发工具进行数据开发。详情请参见[授予RAM用户实例的开发权限](/intl.zh-CN/用户授权及角色管理/子账号使用Hologres/授予子账号实例的开发权限.md)。

## 授予RAM用户权限

1.  主账号登录[RAM管理控制台](https://ram.console.aliyun.com/overview)。

2.  选择需要授权的RAM用户。

    1.  在左侧导航栏的**人员管理**菜单下，单击**用户**。

    2.  在**用户**页面，单击目标RAM用户**操作**列的**添加权限**。

3.  新增授权。

    1.  配置**添加权限**对话框的各项参数。

        ![添加权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5504158951/p133226.png)

        |参数|描述|
        |--|--|
        |授权范围|取值如下：        -   云账号全部资源
        -   指定资源组 |
        |被授权主体|需要授权的RAM用户。|
        |选择权限|取值如下：        -   系统策略
        -   自定义策略
**说明：**

        -   您也可以根据业务需求自定义新建权限策略。
        -   每次最多添加5条策略。如果您需要添加的策略数量大于5条，请分多次执行。 |

    **系统策略**和**自定义策略**的权限说明如下：

    -   **系统策略**

        为RAM用户授予以下权限策略，RAM用户就会拥有所有操作的权限，如下表所示。

        |权限策略|描述|
        |----|--|
        |AliyunHologresFullAccess|管理Hologres服务的权限。设置该权限后，RAM用户可以查看管理控制台所有实例的信息，以及购买实例。

**说明：** 您需要设置**AliyunRAMReadOnlyAccess**权限后，才可以在管理控制台的**用户管理**查看用户信息。 |
        |AliyunBSSOrderAccess|在费用中心（BSS）查看、支付以及取消订单的权限。设置该权限后，子账号可以在管理控制台升级或降级实例的配置，以及为实例续费。 |
        |AliyunRAMReadOnlyAccess|只读访问控制（RAM）的权限。设置该权限后，RAM用户可以在管理控制台的**用户管理**查看当前实例的用户、组以及授权信息。 |
        |AliyunHologresReadOnlyAccess|只读管理Hologres的权限。设置该权限后，RAM用户可以查看Hologres管理控制台所有实例的信息，但是无法操作实例，例如修改网络类型。 |

        **说明：**

        -   RAM用户购买的实例，主账号和RAM用户默认均为Superuser。
        -   主账号购买的实例，RAM用户需要被主账号授权后，才能使用实例。
    -   **自定义策略**

        您可以根据业务需求，在**新建权限策略**中自定义不同的权限。

        ![新建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5504158951/p133299.png)

        在**新建自定义权限策略**页面，您可以使用**脚本配置**自定义权限。

        示例语句如下。

        ```
        {
            "Statement": [
                {   //授予RAM用户所有操作的权限。设置该权限后，则无需再设置下文的其他权限。
                    "Effect": "Allow",
                    "Action": "hologram:*", //表示拥有所有操作的权限。
                    "Resource": "acs:hologram:*:<主账号ID>:instance/*" //可以配置所有地域的实例。
                },
                {   //购买实例或为实例续费。
                    "Effect": "Allow",
                    "Action": "hologram:*",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //删除实例。
                    "Effect": "Allow",
                    "Action": "hologram:DeleteInstance",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //RAM用户购买权限。RAM用户配置该权限后才可以购买实例。
                    "Effect": "Allow",
                    "Action": "bss:PayOrder",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //显示实例详情。
                    "Effect": "Allow",
                    "Action": "hologram:DescribeInstance",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //显示实例列表。
                    "Effect": "Allow",
                    "Action": "hologram:ListInstances",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //暂停实例。
                    "Effect": "Allow",
                    "Action": "hologram:StopInstance",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //恢复实例。
                    "Effect": "Allow",
                    "Action": "hologram:ResumeInstance",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //显示实例监控告警。
                    "Effect": "Allow",
                    "Action": "hologram:GetInstanceMetrics",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                },
                {   //修改网络类型。
                    "Effect": "Allow",
                    "Action": "hologram:ModifyInstanceNetworkType",
                    "Resource": "acs:hologram:cn-<region>:<主账号ID>:instance/*"
                }
            ],
            "Version": "1"
        }
        ```

        参数说明如下表所示。

        |参数|描述|
        |--|--|
        |<region\>|地域，例如**beijing**。|
        |<主账号ID\>|阿里云主账号的ID。|
        |\*|表示该主账号中所有实例的ID。您也可以替换\*为具体的一个实例ID。 |

        示例语句如下。

        ```
        acs:hologram:cn-beijing:4322xxxxx:instance/hhhgggxxxx
        ```

4.  单击**确定**。


## 与控制台相关的RAM用户权限问题

Hologres管理控制台主要集成了RAM鉴权和实例的部分开发权限，与Hologres管理控制台相关的权限问题如下所示：

-   RAM用户无法查看实例列表及实例ID。
    -   问题现象

        RAM用户选择了正确的地域，但无法看到已购买的实例，并提示**暂无权限查看所有实例，请让主账号前往RAM中心授予当前用户“xxx/\*”资源的“hologram:ListInstances”权限**报错。

    -   问题原因

        当前RAM用户没有查看实例列表的权限。

    -   解决方法

        主账号登录[RAM控制台](https://ram.console.aliyun.com/overview)，授予RAM用户显示实例列表的权限**AliyunHologresReadOnlyAccess**。

-   RAM用户无实例的操作权限。
    -   问题现象

        被授权为Superuser的RAM用户无法购买、升配和降配实例，以及转换实例的付费方式由**按量付费**为**订阅**。提示**RAM子账号鉴权不通过**报错。

    -   问题原因

        购买、升配、降配及转换付费方式等操作涉及费用帐单，由主账号统一控制，当前RAM用户没有相关权限。

    -   解决方法

        主账号登录[RAM控制台](https://ram.console.aliyun.com/overview)，授予RAM用户与实例费用相关的权限**AliyunHologresFullAccess**及**AliyunBSSOrderAccess**。


