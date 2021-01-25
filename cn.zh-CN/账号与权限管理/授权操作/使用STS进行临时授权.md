# 使用STS进行临时授权

Hologres可以通过阿里云STS（Security Token Service）进行临时授权访问。通过STS，您可以为第三方应用或RAM用户颁发一个自定义时效和权限的访问凭证。

## 使用限制

目前Hologres仅支持以STS角色访问Holoweb。如果您需要使用STS角色访问其他产品（如DataWorks），需要确认其他产品是否开通STS服务。关于阿里云各个模块下支持STS的云服务，请参见[支持STS的云服务](/cn.zh-CN/产品简介/支持STS的云服务.md)。

## 使用STS进行临时授权流程介绍

阿里云STS（Security Token Service）是为阿里云账号（或RAM用户）提供短期访问权限管理的云服务。通过STS，您可以为用户（您的本地账号系统所管理的用户）颁发一个自定义时效和访问权限的访问凭证。用户可以使用STS短期访问凭证直接连接Hologres并访问被授权的资源。

使用STS Token有以下优势：

-   减少了账号AccessKey ID和AccessKey Secret泄露的风险，只需要生成一个临时访问凭证给用户使用即可。
-   能使用灵活的权限控制，STS Token有一定的时间期限，不需要关心权限撤销问题，临时访问凭证过期后会自动失效。

如下操作步骤将指导您通过创建阿里云STS（Security Token Service）临时授权访问Hologres。

-   [步骤一：创建权限策略](#section_13e_84l_yoy)
-   [步骤二：创建RAM角色](#section_xdn_m7m_oxp)
-   [步骤三：创建RAM用户并设置STS角色](#section_5jy_62f_8by)
-   [步骤四：登录阿里云并使用Hologres](#section_dyn_453_xv2)

## 步骤一：创建权限策略

1.  登录[访问控制](https://ram.console.aliyun.com/roles)，在左侧导航栏选择**权限管理** \> **权限策略管理**，单击**创建权限策略**。

2.  在新建自定义权限策略页面，配置参数信息。

    -   策略名称和备注，自定义策略名称和备注信息。
    -   配置模式，选择可视化配置或脚本配置。

        由于使用STS授权使用Hologres会使用Dataworks相关接口，本操作以脚本配置为例，授权用户Dataworks服务账号，在策略内容中配置脚本内容，具体说明如下：

        -   参数说明

            在策略配置时，您需要将如下脚本中的`acs:ram::主账号ID:root`信息，替换为需要授权的账号信息。请前往[用户信息页面](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure)，获取账号ID。

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


## 步骤二：创建RAM角色

1.  登录[访问控制](https://ram.console.aliyun.com/roles)在左侧导航栏，单击RAM角色管理。

2.  在RAM角色管理页面，单击**创建RAM角色**，当前可信实体类型选择阿里云账号。

3.  单击**下一步**，配置角色信息。

    ![创建RAM角色](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0258311161/p228130.png)

    -   角色名称：自定义角色名称。
    -   选择云账号：选择当前云账号。
4.  单击**完成**，页面提示角色创建成功完成创建。

5.  单击**为角色授权**在添加权限页面，选择自定义权限策略，添加[步骤一：创建权限策略](#section_13e_84l_yoy)中创建的权限策略。


## 步骤三：创建RAM用户并设置STS角色

1.  登录[访问控制](https://ram.console.aliyun.com/roles)在左侧导航栏选择**人员管理** \> **用户**。

2.  （可选，如果您已经存在RAM用户，可跳过该步骤。）单击**创建用户**，可一次性创建多个RAM用户。配置如下参数，单击**确定**。

    -   登录名称和显示名称：自定义登录名称和显示名称信息。
    -   访问方式：选择编程访问。
3.  单击目标RAM用户操作列的**添加权限**。

4.  在添加权限页面，为已创建的RAM用户添加AliyunSTSAssumeRoleAccess权限。

    ![设置STS权限](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0258311161/p228212.png)

5.  单击**确定**，完成STS角色设置。

6.  当您完成RAM用户的STS角色设置之后，还需要为该RAM用户授予Hologres实例的权限，该RAM用户才能在权限范围内连接Hologres实例并开发。具体操作，请参见[授予RAM用户权限](/cn.zh-CN/账号与权限管理/授权操作/授予RAM用户权限.md)。


## 步骤四：登录阿里云并使用Hologres

当您完成为RAM用户设置STS角色操作之后，您可以使用STS方式登录阿里云并使用Hologres。

1.  RA用户M登录阿里云官网，鼠标移动至右上角用户头像处，在下拉菜单选择**切换身份**。

2.  在角色转换页面，填写如下信息并单击**切换**。

    -   企业别名/默认域名/UID：填写账号ID。账号ID信息，请通过[用户信息页面](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure)获取。
    -   角色名：填写[步骤二：创建RAM角色](#section_xdn_m7m_oxp)中创建的角色名称。
3.  切换STS角色成功登录阿里云官网后，可以开始使用角色连接Hologres并开发。


