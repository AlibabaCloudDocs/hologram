---
keyword: [grant permissions, RAM]
---

# Grant permissions on Hologres to RAM users

This topic describes how to grant permissions on Hologres to Resource Access Management \(RAM\) users of your Alibaba Cloud account. Authorized RAM users can view, purchase, and delete Hologres instances in the Hologres console.

RAM is a permission management system that is provided by Alibaba Cloud.

RAM is used to control the permissions of accounts.

You can create RAM users within your Alibaba Cloud account and grant them different permissions on Hologres. For example, you can grant RAM users the permissions to purchase or delete instances, upgrade or downgrade instance specifications, modify network configurations of instances, and view instance details.

The RAM permissions are different from the development permissions on instances in the following ways:

-   If you do not grant the required RAM permissions to a RAM user, the RAM user cannot view or manage instances in the Hologres console.
-   You can grant a RAM user the permissions to develop Hologres instances. Even if the RAM user cannot manage instances in the Hologres console, the RAM user can connect to development tools to develop data. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Use Hologres as a RAM user/Grant the development permissions on a Hologres instance to a RAM user.md).

## Grant permissions on Hologres to RAM users

1.  Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account.

2.  Select the RAM user to which you want to grant permissions.

    1.  In the left-side navigation pane, click **Users** under **Identities**.

    2.  On the **Users** page, find the RAM user, and click **Add Permissions** in the **Actions** column.

3.  Grant permissions to the RAM user.

    1.  In the **Add Permissions** dialog box, set the required parameters.

        ![Add permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8504158951/p133226.png)

        |Parameter|Description|
        |---------|-----------|
        |Authorization|The scope of authorization. Valid values:        -   Alibaba Cloud account all resources
        -   Specified Resource Group |
        |Principal|The RAM user to which you want to grant permissions.|
        |Select Policy|The type of policies that you want to attach to the RAM user. Valid values:        -   System Policy
        -   Custom Policy
**Note:**

        -   You can create custom policies based on your business requirements.
        -   You can add a maximum of five policies at a time. To add more policies, perform the operation multiple times. |

    You can select **System Policy** or **Custom Policy** based on the following descriptions:

    -   **System Policy**

        The following table lists the system policies that you can use to grant permissions on Hologres. If you attach all of these system policies to the RAM user, the RAM user is authorized to perform all operations in the Hologres console.

        |Policy|Description|
        |------|-----------|
        |AliyunHologresFullAccess|Grants full access permissions on Hologres.If you attach this policy to the RAM user, the RAM user can view the information of all instances and purchase instances in the Hologres console.

**Note:** To view user information on the **Users** page of an instance in the Hologres console, you must attach the **AliyunRAMReadOnlyAccess** policy to the RAM user. |
        |AliyunBSSOrderAccess|Grants permissions to view, pay for, and cancel orders in the Billing Management console.If you attach this policy to the RAM user, the RAM user can upgrade or downgrade instance specifications and renew instance subscriptions in the Hologres console. |
        |AliyunRAMReadOnlyAccess|Grants read-only permissions to RAM.If you attach this policy to the RAM user, the RAM user can view the information of the current users, groups, and permissions on the **Users** page of your instance in the Hologres console. |
        |AliyunHologresReadOnlyAccess|Provides read-only permissions to Hologres.If you attach this policy to the RAM user, the RAM user can view the information of all instances but cannot manage the instances in the Hologres console. For example, the RAM user cannot modify the network configurations of instances. |

        **Note:**

        -   If you use a RAM user to purchase an instance, the RAM user and the Alibaba Cloud account are superusers by default.
        -   If you use an Alibaba Cloud account to purchase an instance, only the Alibaba Cloud account can use the instance by default. RAM users must be authorized before they can use the instance.
    -   **Custom Policy**

        You can click **Create Policy** to create a policy based on your business requirements.

        ![Create a policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8504158951/p133299.png)

        On the **Create Custom Policy** page, you can set the configuration mode to **Script**. Then, edit the script of the policy.

        The following statement provides an example to demonstrate how to configure a custom policy:

        ```
        {
            "Statement": [
                {   // Grant a RAM user the permissions to perform all operations. After the permissions are granted, the following permissions are not required.
                    "Effect": "Allow",
                    "Action": "hologram:*", // Indicates that the RAM user has the permissions to perform all operations.
                    "Resource": "acs:hologram:*:<Alibaba Cloud account ID>:instance/*" // Indicates that the RAM user has access to instances in all regions.
                },
                {   // Grant a RAM user the permissions to purchase or renew instances.
                    "Effect": "Allow",
                    "Action": "hologram:*",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to delete instances.
                    "Effect": "Allow",
                    "Action": "hologram:DeleteInstance",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to purchase instances. The RAM user can purchase instances only after the permissions are granted.
                    "Effect": "Allow",
                    "Action": "bss:PayOrder",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to view instance details.
                    "Effect": "Allow",
                    "Action": "hologram:DescribeInstance",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to view instances.
                    "Effect": "Allow",
                    "Action": "hologram:ListInstances",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to suspend instances.
                    "Effect": "Allow",
                    "Action": "hologram:StopInstance",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to resume instances.
                    "Effect": "Allow",
                    "Action": "hologram:ResumeInstance",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to view the monitoring metrics of instances.
                    "Effect": "Allow",
                    "Action": "hologram:GetInstanceMetrics",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                },
                {   // Grant a RAM user the permissions to modify the network types of instances.
                    "Effect": "Allow",
                    "Action": "hologram:ModifyInstanceNetworkType",
                    "Resource": "acs:hologram:cn-<region>:<Alibaba Cloud account ID>:instance/*"
                }
            ],
            "Version": "1"
        }
        ```

        The following table describes the parameters in the syntax.

        |Parameter|Description|
        |---------|-----------|
        |<region\>|The region of the Hologres instance. Example: **beijing**.|
        |<Alibaba Cloud account ID\>|The ID of your Alibaba Cloud account.|
        |\*|The IDs of all Hologres instances within your Alibaba Cloud account.You can also replace the asterisk \(\*\) with the ID of a specific Hologres instance. |

        The following statement provides an example to demonstrate how to define permissions on a specified Hologres instance:

        ```
        acs:hologram:cn-beijing:4322xxxxx:instance/hhhgggxxxx
        ```

4.  Click **OK**.


## FAQ

Hologres permissions consist of RAM permissions and instance development permissions. This section provides answers to the frequently asked questions about Hologres permissions.

-   A RAM user cannot view the instance list and instance IDs.
    -   Issue

        When I logged on to the console as a RAM user and selected a valid region, I could not view the instances that I purchased. The following error message is displayed: **You have no permissions to view the instances, please log on to the RAM console to grant the hologram:ListInstances permission to the current RAM user to access xxx/\***.

    -   Cause

        The current RAM user does not have permissions to view the instance list.

    -   Solution

        Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account and grant the RAM user the **AliyunHologresReadOnlyAccess** permission to view the instance list.

-   A superuser cannot add users.
    -   Issue

        A superuser cannot view the user list or add users on the **Users** tab of the instance details page in the Hologres console. The following error message is displayed: **You have no permissions to view the users, please log on to the RAM console by using your Alibaba Cloud account and grant the ram:ListUsers permission to the current superuser**.

    -   Cause

        The current RAM user does not have permissions to view the user list or add users in the Hologres console.

    -   Solution

        Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account and grant the RAM user the **AliyunRAMReadOnlyAccess** permission to view the user list.

-   A RAM user cannot view the information of the **Users** tab and **Databases** tab.
    -   Issue

        When I logged on to the Hologres console as a RAM user, I could not view the information of the **Users**tab and the **Databases** tab. The following error message is displayed: **You have no permissions, please ask a superuser to add the current user to view the instance details**.

    -   Cause

        The current RAM user does not have development permissions on the instance.

    -   Solution

        Grant the current RAM user the development permissions on the instance by using your Alibaba Cloud account or as a superuser. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Use Hologres as a RAM user/Grant the development permissions on a Hologres instance to a RAM user.md).

-   A RAM user does not have permissions to manage the instance.
    -   Issue

        When I logged on to the Hologres console, I could not purchase, upgrade, or downgrade the instance, or change the billing method from **pay-as-you-go** to **subscription**. The following error message is displayed: **The authentication of the current superuser failed**.

    -   Cause

        The current superuser does not have permissions to purchase, upgrade, or downgrade the instance, or change the billing method of the instance.

    -   Solution

        Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account and grant the superuser the **AliyunHologresFullAccess** and **AliyunBSSOrderAccess** permissions.


