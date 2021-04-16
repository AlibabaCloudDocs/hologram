---
keyword: [RAM role authorization, Hologres, SSO]
---

# RAM authorization mode

This topic describes how to log on to Hologres by using role-based single sign-on \(SSO\).

## Background information

Alibaba Cloud allows enterprise users to manage and use cloud resources in the Alibaba Cloud Management Console. Enterprise users can use their Alibaba Cloud accounts and passwords to log on to the console. However, as the regulatory requirements for enterprise security become increasingly strict, some enterprises prefer to use role-based SSO to log on to Alibaba Cloud. For more information, see [Overview of role-based SSO](/intl.en-US/SSO Management/Role-based SSO/Overview of role-based SSO.md).

## Access methods supported by Hologres

Hologres supports two access methods:

-   Log on to Hologres by using an Alibaba Cloud account or as a RAM user.

    You can log on to the Alibaba Cloud Management Console by using an Alibaba Cloud account and password or as a RAM user. Then, you can have access to Hologres. In this case, the account becomes a member of a Hologres instance and has permissions to use Hologres features.

-   Log on to Hologres by using role-based SSO.

    You can also log on to Alibaba Cloud and have access to Hologres by using role-based SSO. For more information, see [Access Alibaba Cloud by using the console](/intl.en-US/SSO Management/Role-based SSO/Overview of role-based SSO.mdsection_hv1_3yr_bhb) and [Access Alibaba Cloud by using a program](/intl.en-US/SSO Management/Role-based SSO/Overview of role-based SSO.mdsection_rxk_lyr_bhb). In this case, the RAM role becomes a member of a Hologres instance. The user that assumes this RAM role has the same permissions as an Alibaba Cloud account or a RAM user. For information about RAM roles, see [RAM role overview](/intl.en-US/RAM Role Management/RAM role overview.md).


In Hologres, RAM roles have equal status with Alibaba Cloud accounts and RAM users. Therefore, in Hologres, a RAM role is regarded as an ordinary available account. For each RAM role, the superuser must grant permissions, such as the SELECT, INSERT, and UPDATE permissions, to the RAM role, not the Alibaba Cloud account or RAM user that assumes the role. After that, the RAM role can use Hologres based on the granted permissions.

## Introduction to role-based SSO

The access to Hologres by using role-based SSO is implemented based on Security Token Service \(STS\) that is provided by Alibaba Cloud. STS is a cloud service that provides short-term access control for Alibaba Cloud accounts or RAM users. You can use STS to issue an access credential with a custom validity period and access permissions to a user that is managed by your on-premises account system. A user can use an STS short-term access credential to directly connect to Hologres and use authorized resources.

STS tokens provide the following benefits:

-   STS tokens reduce the risk of disclosing the AccessKey ID and AccessKey secret of your Alibaba Cloud account. You need only to generate a temporary access credential for users to use.
-   STS tokens allow you to flexibly control access to resources and impose time limits. Therefore, you do not need to manually revoke permissions. A temporary access credential automatically becomes invalid upon expiration.

To create a RAM role and authorize the role to access Hologres, perform the following steps:

-   [Step 1: Create a RAM role](#section_nf5_ape_79m)
-   [Step 2: Add the RAM role to a Hologres instance and authorize the role](#section_wy2_bz4_7cp)
-   [Step 3: Log on to Alibaba Cloud and use Hologres](#section_dyn_453_xv2)

## Step 1: Create a RAM role

Log on to the [Resource Access Management \(RAM\) console](https://ram.console.aliyun.com/roles) and create a RAM role. In the Create RAM Role panel, set the Trusted entity type parameter to Alibaba Cloud Account or IdP.

-   If you want an Alibaba Cloud account or a RAM user to assume the RAM role by switching the identity in the Alibaba Cloud Management Console, set the Trusted entity type parameter to Alibaba Cloud Account. For more information, see [Assign a RAM role to a RAM user and grant permissions](#section_g76_3bu_m98).
-   If you want an on-premises identity provider \(IdP\) to assume the RAM role, set the Trusted entity type parameter to IdP. For more information, see [Assign a RAM role to an IdP and grant permissions](#section_4vt_03u_3kj).

## Assign a RAM role to a RAM user and grant permissions

If you want a RAM user to assume a RAM role by switching the identity in the Alibaba Cloud Management Console, log on to the [RAM console](https://ram.console.aliyun.com/roles) and create a RAM role. In the Create RAM Role panel, set the Trusted entity type parameter to Alibaba Cloud Account.

1.  Create a RAM role for a trusted Alibaba Cloud account.

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/roles). In the left-side navigation pane, click RAM Roles.
    2.  On the RAM Roles page, click **Create RAM Role**. In the Create RAM Role panel, set the Trusted entity type parameter to Alibaba Cloud Account.
    3.  Click **Next**. Set a name for the RAM role and set the Select Trusted Alibaba Cloud Account parameter to Current Alibaba Cloud Account.
    4.  Click **OK**. The Finish step shows that the RAM role is created.
2.  Create an authorization policy and attach it to the RAM role.

    1.  On the RAM Roles page, click the name of the RAM role for which you want to attach an authorization policy to go to the details page of the RAM role.
    2.  Click the **Trust Policy Management** tab and replace the policy content with the following script.
        -   Parameters

            When you configure the policy, replace the Alibaba Cloud account ID in `acs:ram::Alibaba Cloud account ID:root` in the following script with the ID of the Alibaba Cloud account that you want to authorize. You can go to the [Security Settings](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure) page to obtain the account ID.

        -   Script

            ```
            {
                "Statement": [
                    {
                        "Action": "sts:AssumeRole",
                        "Effect": "Allow",
                        "Principal": {
                            "RAM": [
                                "acs:ram::Alibaba Cloud account ID:root"
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

    3.  Click **OK**.
3.  Create a RAM user and grant the RAM user the permission to assume a role.

    To assign a RAM role to a RAM user, you must first create a RAM user and grant the RAM user the permission to assume a role.

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/roles). In the left-side navigation pane, choose **Identities** \> **Users**.
    2.  Optional. If you already have a RAM user, skip this step.On the Users page, click **Create User**. You can create multiple RAM users at a time. On the Create User page, select Programmatic Access for the Access Mode parameter and set other parameters. Click **OK**.
    3.  On the Users page, find the RAM user that you created and click **Add Permissions** in the Actions column.
    4.  In the Add Permissions panel, attach the AliyunSTSAssumeRoleAccess policy to the RAM user that you created. After that, the RAM user has the permission to call the AssumeRole operation of STS.

        ![Grant STS permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2741494161/p228212.png)

    5.  Click **OK**.

## Assign a RAM role to an IdP and grant permissions

If you want an on-premises IdP to log on to Alibaba Cloud to assume a RAM role, log on to the [RAM console](https://ram.console.aliyun.com/roles) and create a RAM role. In the Create RAM Role panel, set the Trusted entity type parameter to IdP.

1.  Create a RAM role for a trusted IdP.

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/roles). In the left-side navigation pane, click RAM Roles.
    2.  On the RAM Roles page, click **Create RAM Role**. In the Create RAM Role panel, set the Trusted entity type parameter to IdP.
    3.  Click **Next**. Set the RAM Role Name and Note parameters.
    4.  Select a trusted IdP, view the conditions, and then click **OK**. The Finish step shows that the RAM role is created.
2.  Create an authorization policy and attach it to the RAM role.

    1.  On the RAM Roles page, click the name of the RAM role for which you want to attach an authorization policy to go to the details page of the RAM role.
    2.  Click the **Trust Policy Management** tab and replace the policy content with the following script.
        -   Parameters

            When you configure the policy, replace the Alibaba Cloud account ID in `acs:ram::Alibaba Cloud account ID:saml-provider/IDP` in the following script with the ID of the Alibaba Cloud account that you want to authorize. You can go to the [Security Settings](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure) page to obtain the account ID.

        -   Script

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
                                "acs:ram::Alibaba Cloud account ID:saml-provider/IDP"
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
            }
            ```

    3.  Click **OK**.

## Step 2: Add the RAM role to a Hologres instance and authorize the role

Before the RAM role can use Hologres based on the granted permissions, the role must obtain the required development permissions on the Hologres instance. By default, the RAM role does not have the permissions to view or manage instances in the Hologres console. Therefore, you must first use your Alibaba Cloud account to grant the required permissions to the RAM role. For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md). After you add the RAM role to a Hologres instance, you can use one of the following methods to authorize the RAM role:

1.  Authorize the RAM role in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

    1.  In the left-side navigation pane, click Instances. On the Hologres Instances page, click the name of the instance that you want to authorize the RAM role to access. On the Users tab, add the RAM role to the instance.
    2.  On the Databases tab, grant specific development permissions on the instance to the RAM role.
2.  Use an SQL statement to authorize the RAM role.

    For more information about how to use an SQL statement to authorize a RAM role, see [Permission management overview](/intl.en-US/User Authorization/Permission management overview.md).

3.  By default, a RAM role does not have the permissions to perform operations in the Hologres console. If you want a RAM user to assume the RAM role, you must first use your Alibaba Cloud account to attach the **AliyunRAMReadOnlyAccess** policy to the RAM user. Otherwise, the RAM role cannot perform operations in the Hologres console. For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).


## Step 3: Log on to Alibaba Cloud and use Hologres

After you complete the authorization, a user can assume the RAM role and use Hologres.

1.  Access Alibaba Cloud [by using the console](/intl.en-US/SSO Management/Role-based SSO/Overview of role-based SSO.mdsection_hv1_3yr_bhb) or [by using a program](/intl.en-US/SSO Management/Role-based SSO/Overview of role-based SSO.mdsection_rxk_lyr_bhb), assume the RAM role, and then log on to the Alibaba Cloud Management Console by using role-based SSO.

2.  Go to the [Hologres console](https://hologram.console.aliyun.com/#/instance) to manage and monitor instances.

3.  In the [Hologres console](https://hologram.console.aliyun.com/#/instance), click **Log on to Hologres Database** to go to HoloWeb that supports Hologres schema design and data development. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).


