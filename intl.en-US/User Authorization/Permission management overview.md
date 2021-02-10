---
keyword: [Hologres, permission management, Hologres permissions]
---

# Permission management overview

You must grant the relevant permissions to users before they can use Hologres for data analytics. This topic describes the permission policies of Hologres to help you grant permissions to different users based on the scenario and manage user permissions in a fine-grained manner.

## User authentication process in Hologres

Users are authenticated for different permissions based on the scenario when they use Hologres. For example, when a user purchases an instance, Alibaba Cloud performs Resource Access Management \(RAM\) authentication to check whether the user has the permission to purchase an instance in the Hologres console. The user must be granted the permission to purchase an instance.

The following figure shows the complete authentication process when you log on to Alibaba Cloud and use Hologres.

![User authentication process in Hologres](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6608392161/p206160.png)

-   When you manage instances in the Hologres console, for example, when you purchase, upgrade or downgrade the configuration of, renew, or shut down instances, Alibaba Cloud performs RAM authentication on your account to check whether you have the permissions. You can perform these operations only when you have the permissions. For more information, see [RAM authentication](#section_c2p_abb_3tm).
-   When you connect to a Hologres instance and perform data analytics operations, Alibaba Cloud performs Hologres authentication to check whether you have the permissions. You can perform these operations only when you have the permissions. For more information, see [Hologres authentication](#section_eih_623_kcf).
-   When you use DataWorks to connect to a Hologres instance and perform data analytics operations, Alibaba Cloud performs both DataWorks authentication and Hologres authentication to check whether you have the permissions. For more information, see [DataWorks authentication](#section_gjl_9jf_cah).
-   When you use Hologres to query data in MaxCompute tables, Alibaba Cloud performs MaxCompute authentication to check whether you have the permissions to access the MaxCompute project and tables. For more information, see [MaxCompute authentication](#section_nuh_p8c_foh).

## RAM authentication

[RAM](https://ram.console.aliyun.com/permissions) is a permission management system provided by Alibaba Cloud. RAM is used to control permissions of accounts. You can manage permissions on Hologres instances by granting different permissions to different RAM users, including the permissions to purchase, delete, and upgrade or downgrade the configuration of instances, change the network type, and view instance information.

If you do not grant the permissions to a user by using RAM, the user cannot view instance details in the Hologres console. However, the user can still connect to a Hologres instance. For more information about RAM authorization, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).

## Hologres authentication

Hologres is a real-time data warehouse that is compatible with PostgreSQL 11. Before you use a Hologres instance for data analytics, you must pass the following authentications:

1.  Account authentication

    -   You can log on to the Hologres console by using your Alibaba Cloud account or as a RAM user.
    -   When you use tools such as the PostgreSQL client or a JDBC client to connect to a Hologres instance, you must enter your AccessKey ID and AccessKey secret as the username and password.
    For more information about account authentication, see [Account overview](/intl.en-US/User Authorization/Account overview.md).

2.  User authentication

    After you pass the account authentication, the system checks whether your account is a Hologres user when you use the account to connect to a Hologres instance. You become a user of the Hologres instance only after an administrator runs the `create user "xxx"` command for your account. For more information about users and how to create a user, see [t2021277.md\#section\_ya4\_yyy\_87e]().

3.  Instance authentication

    After a user is created for you in a Hologres instance, you must be granted relevant permissions to perform operations. For more information about how to grant permissions to a user, see [t2021277.md\#]().


## DataWorks authentication

Hologres is deeply integrated with DataWorks. When you perform data analytics operations on a Hologres instance in the DataWorks console, you must pass both DataWorks authentication and Hologres authentication. You must have the following permissions:

-   The permission to access the DataWorks workspace.
-   The permissions on the Hologres instance. These permissions are required to perform data analytics operations in HoloStudio.
-   The permissions to use DataWorks services such as Data Integration and DataService Studio if you need to perform relevant operations. For more information about DataWorks permissions, see [Permission list]().

The following figure shows the DataWorks authentication process.

![DataWorks authentication process](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6608392161/p206199.png)

## MaxCompute authentication

To use Hologres to query data in MaxCompute tables, make sure that your Hologres account has the permissions to access the MaxCompute project and tables. The following figure shows the authentication process when you use Hologres to query data in MaxCompute tables.

![MaxCompute authentication process](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6608392161/p206627.png)

