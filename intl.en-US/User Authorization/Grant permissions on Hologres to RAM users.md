---
keyword: [grant permissions to a RAM user, RAM user]
---

# Grant permissions on Hologres to RAM users

This topic describes how to grant permissions on Hologres to RAM users that belong to your Alibaba Cloud account. Authorized RAM users can view, purchase, and delete Hologres instances in the Hologres console.

Resource Access Management \(RAM\) is a permission management system that is provided by Alibaba Cloud.

RAM is used to control the permissions of accounts.

You can create RAM users within your Alibaba Cloud account and grant them different permissions on Hologres. For example, you can grant RAM users the permissions to purchase or delete instances, upgrade or downgrade instance specifications, modify the network configurations of instances, and view instance details.

When you perform data analytics operations on a Hologres instance as a RAM user, take note of the following rules:

-   If permissions on Hologres are not granted by the Alibaba Cloud account, the RAM user cannot view or manage instances in the Hologres console.
-   The RAM user can be granted the development permissions on Hologres instances. Even if the RAM user cannot manage instances in the Hologres console, the RAM user can connect to development tools to perform data analytics operations. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).

## Grant permissions on Hologres to a RAM user

1.  Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account.

2.  Select the RAM user to which you want to grant permissions.

    1.  In the left-side navigation pane, click **Users** under **Identities**.

    2.  On the **Users** page, find the RAM user to which you want to grant permissions and click **Add Permissions** in the **Actions** column.

3.  Grant permissions to the RAM user.

    1.  In the **Add Permissions** panel, set the parameters as required.

        ![Add Permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8504158951/p133226.png)

        |Parameter|Description|
        |---------|-----------|
        |Authorization|Valid values:        -   Alibaba Cloud account all resources
        -   Specified Resource Group |
        |Principal|The RAM user to which you want to grant permissions.|
        |Select Policy|Valid values:        -   System Policy
        -   Custom Policy
**Note:**

        -   You can create custom policies based on your business needs.
        -   You can attach a maximum of five policies at a time. To attach more policies, perform the operation multiple times. |

    You can select **System Policy** or **Custom Policy** based on the following descriptions:

    -   **System Policy**

        The following table describes the system policies that you can use to grant permissions on Hologres. If you attach all of these system policies to the RAM user, the RAM user is authorized to perform all operations in the Hologres console.

        |Policy|Description|
        |------|-----------|
        |AliyunHologresFullAccess|Grants full access permissions on Hologres.If you attach this policy to the RAM user, the RAM user can view the information about all instances and purchase instances in the Hologres console.

**Note:** To view user information on the **Users** tab of an instance details page in the Hologres console, you must attach the **AliyunRAMReadOnlyAccess** policy to the RAM user. |
        |AliyunBSSOrderAccess|Grants permissions to view, pay for, and cancel orders in the Billing Management console.If you attach this policy to the RAM user, the RAM user can upgrade or downgrade instance specifications and renew instances in the Hologres console. |
        |AliyunRAMReadOnlyAccess|Grants read-only permissions on RAM.If you attach this policy to the RAM user, the RAM user can view the information about the current users, groups, and permissions on the **Users** tab of an instance details page in the Hologres console. |
        |AliyunHologresReadOnlyAccess|Grants read-only permissions on Hologres.If you attach this policy to the RAM user, the RAM user can view the information about all instances but cannot manage the instances in the Hologres console. For example, the RAM user cannot modify the network configurations of instances. |

        **Note:**

        -   If you use a RAM user to purchase an instance, the RAM user and the Alibaba Cloud account are superusers by default.
        -   If you use an Alibaba Cloud account to purchase an instance, only the Alibaba Cloud account can use the instance by default. RAM users must be authorized by the Alibaba Cloud account before they can use the instance.
    -   **Custom Policy**

        You can click **Create Policy** to create a custom policy based on your business needs.

        ![Create](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8504158951/p133299.png)

        On the **Create Custom Policy** page, you can set the configuration mode to **Script**. Then, edit the script of the policy.

        Sample statements:

        ```
        {
            "Statement": [
                {   // Grant a RAM user the permissions to perform all operations. After the permissions are granted, the other permissions are not required.
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
                {   // Grant a RAM user the permissions to view the instance list.
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
                {   // Grant a RAM user the permissions to modify the network configurations of instances.
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

        Sample statement:

        ```
        acs:hologram:cn-beijing:4322xxxxx:instance/hhhgggxxxx
        ```

4.  Click **OK**.


## FAQ

Permissions on Hologres consist of permissions that are granted in the RAM console and part of development permissions on instances. This section provides answers to commonly asked questions about permissions on Hologres.

-   Why am I unable to view the instance list and instance IDs as a RAM user?
    -   Problem description

        When I logged on to the Hologres console as a RAM user and selected a valid region, I could not view the instances that I purchased. The following error message is returned: **You are not authorized to view the purchased instances. Contact the relevant Alibaba Cloud account to grant the hologram:ListInstances permission on xxx/\* to you in the RAM console**.

    -   Cause

        The current RAM user does not have permissions to view the instance list in the Hologres console.

    -   Solution

        Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account. Attach the **AliyunHologresReadOnlyAccess** policy to the RAM user so that the RAM user can view the instance list.

-   Why am I unable to manage instances as a RAM user?
    -   Problem description

        When I logged on to the Hologres console, I could not purchase an instance, upgrade or downgrade instance specifications, or change the billing method of an instance from pay-as-you-go to subscription. The following error message is returned: **Failed to authenticate the RAM user**.

    -   Cause

        The current RAM user does not have permissions to purchase an instance, upgrade or downgrade instance specifications, or change the billing method of an instance. You can perform these operations by using your Alibaba Cloud account.

    -   Solution

        Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account. Attach the **AliyunHologresFullAccess** and **AliyunBSSOrderAccess** policies to the RAM user so that the RAM user can manage instances.


