---
keyword: [RAM角色授权, Hologres, SSO]
---

# RAM角色授权模式

本文为您介绍如何使用角色SSO的方式访问Hologres。

## 背景信息

阿里云支持企业用户通过在阿里云控制台输入账号、密码后登录阿里云来管理和使用云资源。随着企业安全监管要求的日益严格，部分企业更愿意通过角色登录（Role Base\_SSO）的方式登录阿里云。详情请参见[进行角色SSO](/cn.zh-CN/单点登录管理（SSO）/角色SSO/进行角色SSO.md)。

## Hologres支持的用户访问方式

Hologres支持如下两种访问方式：

-   通过云账号（阿里云账号或RAM用户）访问Hologres。

    您可以通过输入账号、密码的方式登录阿里云管理控制台，并以当前登录账号的身份使用Hologres。此时，阿里云云账号将成为Hologres某个实例的成员，拥有Hologres产品的使用权限。

-   通过RAM角色SSO的方式登录Hologres。

    您也可以通过角色SSO的方式（包括[通过控制台访问阿里云](/cn.zh-CN/单点登录管理（SSO）/角色SSO/进行角色SSO.mdsection_hv1_3yr_bhb)和[使用程序访问阿里云](/cn.zh-CN/单点登录管理（SSO）/角色SSO/进行角色SSO.mdsection_rxk_lyr_bhb)）登录阿里云，并使用Hologres。此时，阿里云访问控制角色（RAM Role）将成为Hologres某个实例的成员，扮演该RAM Role的使用者将拥有和云账号类成员同样的产品使用权限。RAM角色的描述，详情请参见[RAM角色概览](/cn.zh-CN/角色管理/RAM角色概览.md)。


对于Hologres来说，RAM角色（RAM Role）和阿里云账号（包括主账号和RAM用户）是同等地位的账号。因此，对于Hologres来说，RAM角色就是一个普通的可登陆账号。Superuser需要对这个RAM角色（而不是背后的云账号）进行授权来赋予这个RAM角色权限（SELECT/INSERT/UPDATE等），这个RAM角色才能在权限范围内使用Hologres。

## RAM角色SSO（STS）介绍

通过RAM角色SSO的方式登录并访问Hologres是基于阿里云STS服务实现的。阿里云STS（Security Token Service）是为阿里云账号（或RAM用户）提供短期访问权限管理的云服务。通过STS，您可以为用户（您的本地账号系统所管理的用户）颁发一个自定义时效和访问权限的访问凭证。用户可以使用STS短期访问凭证直接连接Hologres并访问被授权的资源。

使用STS Token有以下优势：

-   减少了账号AccessKey ID和AccessKey Secret泄露的风险，只需要生成一个临时访问凭证给用户使用即可。
-   能使用灵活的权限控制，STS Token有一定的时间期限，不需要关心权限撤销问题，临时访问凭证过期后会自动失效。

如下操作步骤将指导您通过创建RAM角色并授权访问Hologres。

-   [步骤一：创建RAM角色](#section_nf5_ape_79m)
-   [步骤二：添加RAM角色至Hologres实例并授权](#section_wy2_bz4_7cp)
-   [步骤三：登录阿里云并使用Hologres](#section_dyn_453_xv2)

## 步骤一：创建RAM角色

登录[访问控制](https://ram.console.aliyun.com/roles)，创建RAM角色。当前可信实体类型，可以选择阿里云账号或者身份提供商。

-   如果需要通过阿里云管理控制台切换身份方式扮演该角色，选择阿里云账号类型的角色。具体操作请参见[通过RAM用户扮演角色并添加权限](#section_g76_3bu_m98)。
-   如果需要通过本地IDP身份提供商账号登录至阿里云扮演该角色，选择身份提供商类型的角色。具体操作请参见[通过IDP身份提供商账号来扮演角色并添加权限](#section_4vt_03u_3kj)。

## 通过RAM用户扮演角色并添加权限

如果需要通过RAM用户来扮演RAM角色并基于阿里云控制台切换身份方式扮演该角色，请登录[访问控制](https://ram.console.aliyun.com/roles)，创建RAM角色。当前可信实体类型，可以选择阿里云账号。

1.  创建可信实体类型为阿里云账号的RAM角色。

    1.  登录[访问控制](https://ram.console.aliyun.com/roles)在左侧导航栏，单击RAM角色管理。
    2.  在RAM角色管理页面，单击**创建RAM角色**，当前可信实体类型选择阿里云账号。
    3.  单击**下一步**，配置角色名称，并选择当前云账号。
    4.  单击**完成**，页面提示角色创建成功完成创建。
2.  创建并添加权限策略。

    1.  单击**为角色授权**在添加权限页面，选择**新建权限策略**，
    2.  选择**新建权限策略**后默认进入[访问控制](https://ram.console.aliyun.com/roles)，的**权限策略管理**页签，新建自定义权限策略页面，配置如下参数信息。
        -   策略名称和备注，自定义策略名称和备注信息。
        -   配置模式，选择可视化配置或脚本配置。
            -   参数说明

                在策略配置时，您需要将如下脚本中的`acs:ram::主账号ID:root`信息中的主账号ID，替换为需要授权的账号信息。请前往[用户信息页面](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure)，获取账号ID。

            -   脚本配置

                ```
                {
                    "Statement": [
                        {
                            "Action": "sts:AssumeRole",
                            "Effect": "Allow",
                            "Principal": {
                                "RAM": [
                                    "acs:ram::主账号ID:root"
                                ]
                            }
                        },
                        {
                            "Action": "sts:AssumeRole",
                            "Effect": "Allow",
                            "Principal": {
                                "Service": [
                                    "dataworks.aliyuncs.com"
                                ]
                            }
                        }
                    ],
                    "Version": "1"
                }
                ```

    3.  单击**确定**，完成权限策略配置。
    4.  在左侧导航栏，单击进入RAM角色管理页面，单击目标角色操作列的**添加权限**。
    5.  在添加权限页面，单击**自定义策略**，选择创建的权限名称。
    6.  单击**确定**，完成角色授权。
3.  创建RAM用户并授予角色权限。

    通过RAM用户来扮演RAM角色，需要创建一个RAM用户并授予扮演角色的权限。

    1.  登录[访问控制](https://ram.console.aliyun.com/roles)，在左侧导航栏选择**人员管理** \> **用户**。
    2.  （可选，如果您已经存在RAM用户，可跳过该步骤。）单击**创建用户**，可一次性创建多个RAM用户。配置参数，其中访问方式选择编程访问。单击**确定**。
    3.  单击目标RAM用户操作列的**添加权限**。
    4.  在添加权限页面，为已创建的RAM用户添加AliyunSTSAssumeRoleAccess权限，获取调用STS服务AssumeRole接口的权限。

        ![设置STS权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0258311161/p228212.png)

    5.  单击**确定**，完成角色设置。

## 通过IDP身份提供商账号来扮演角色并添加权限

如果需要通过本地IDP身份提供商账号登录至阿里云扮演RAM角色，请登录[访问控制](https://ram.console.aliyun.com/roles)，创建RAM角色。当前可信实体类型，可以选择身份提供商。

1.  创建可信实体类型为身份提供商的RAM角色。

    1.  登录[访问控制](https://ram.console.aliyun.com/roles)在左侧导航栏，单击RAM角色管理。
    2.  在RAM角色管理页面，单击**创建RAM角色**，当前可信实体类型选择身份提供商。
    3.  单击**下一步**，配置角色名称和备注。
    4.  选择身份提供商并查看限制条件后，单击**完成**，页面提示角色创建成功完成创建。
2.  创建并添加权限策略。

    1.  单击**为角色授权**在添加权限页面，选择**新建权限策略**，
    2.  选择**新建权限策略**后默认进入[访问控制](https://ram.console.aliyun.com/roles)，的**权限策略管理**页签，新建自定义权限策略页面，配置如下参数信息。
        -   策略名称和备注，自定义策略名称和备注信息。
        -   配置模式，选择可视化配置或脚本配置。
            -   参数说明

                在策略配置时，您需要将如下脚本中的`acs:ram::主账号ID:root`信息中的主账号ID，替换为需要授权的账号信息。请前往[用户信息页面](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure)，获取账号ID。

            -   脚本配置

                ```
                "Statement": [
                        {
                            "Action": "sts:AssumeRole",
                            "Condition": {
                                "StringEquals": {
                                    "saml:recipient": "https://signin.aliyun.com/saml-role/sso"
                                }
                            },
                            "Effect": "Allow",
                            "Principal": {
                                "Federated": [
                                    "acs:ram::主账号ID:saml-provider/IDP"
                                ]
                            }
                        },
                        {
                            "Action": "sts:AssumeRole",
                            "Effect": "Allow",
                            "Principal": {
                                "Service": [
                                    "dataworks.aliyuncs.com"
                                ]
                            }
                        }
                    ],
                    "Version": "1"
                }
                ```

    3.  单击**确定**，完成权限策略配置。
    4.  在左侧导航栏，单击进入RAM角色管理页面，单击目标角色操作列的**添加权限**。
    5.  在添加权限页面，单击**自定义策略**，选择创建的权限名称。
    6.  单击**确定**，完成角色授权。

## 步骤二：添加RAM角色至Hologres实例并授权

RAM角色需要有Hologres实例的开发权限，才能在权限范围内使用Hologres。由于RAM角色默认没有Hologres管控台的查看和操作实例的权限，因此需要主账号完成RAM相关权限授予才能进行后续操作。具体操作，请参见[授予RAM用户权限](/cn.zh-CN/账号与权限管理/授予RAM用户权限.md)。添加RAM角色至Hologres实例，您可以通过如下方式进行授权。

1.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)进行授权。

    1.  在左侧导航栏选择进入实例列表页面，单击需要授权的实例，在用户管理页签找到RAM角色并新增用户至实例。
    2.  在DB管理页签，为该RAM用户授予具体的实例开发权限。
2.  通过SQL方式授权。

    您可以通过SQL方式进行授权，具体请参见[权限管理概述](/cn.zh-CN/账号与权限管理/权限管理概述.md)。

3.  若是通过RAM用户扮演RAM角色，RAM角色默认没有Hologres管理控制台的权限，需要主账号给RAM用户在访问控制页面授予**AliyunRAMReadOnlyAccess**权限，否则RAM角色无法在Hologres管理控制台进行任何操作。详情请参见文档[授予RAM用户权限](/cn.zh-CN/账号与权限管理/授予RAM用户权限.md)。


## 步骤三：登录阿里云并使用Hologres

当您完成授权之后，使用者就可以扮演user-role角色登录并使用Hologres。

1.  登录阿里云。使用者可以[通过控制台访问阿里云](/cn.zh-CN/单点登录管理（SSO）/角色SSO/进行角色SSO.mdsection_hv1_3yr_bhb)和[使用程序访问阿里云](/cn.zh-CN/单点登录管理（SSO）/角色SSO/进行角色SSO.mdsection_rxk_lyr_bhb)，扮演RAM角色user-role，并SSO登录至阿里云控制台。

2.  登录[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)，进行实例管理和监控。

3.  在[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)单击**登录Hologres数据库**，进入HoloWeb，进行Hologres结构设计和数据开发。具体操作请参见[HoloWeb快速入门](/cn.zh-CN/快速入门/HoloWeb快速入门.md)。


