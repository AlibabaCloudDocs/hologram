---
keyword: [Hologres, permissions on Hologres]
---

# Permissions granted to RAM users

Permissions on Hologres consist of permissions that are granted in the RAM console and part of development permissions on instances. This topic provides answers to frequently asked questions about permissions on Hologres.

## FAQ

You can manage Hologres instances only after you are granted permissions to perform operations in the Hologres console. To view answers to frequently asked questions about permissions on Hologres, click the following links:

-   [How do I purchase a Hologres instance as a RAM user?](#section_3he_p1t_efq)
-   [Why am I unable to view the instance list and instance IDs as a RAM user?](#section_te9_8bd_4iy)
-   [Why am I unable to manage instances as a RAM user?](#section_ovp_spc_a51)

## How do I purchase a Hologres instance as a RAM user?

Before you can purchase a Hologres instance as a RAM user, you must authorize the RAM user by using your Alibaba Cloud account. For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).

**Note:** After you purchase a Hologres instance as a RAM user, you must use your Alibaba Cloud account to grant the RAM user the development permissions on Hologres instances. Then, the RAM user can use the Hologres instance that you purchase. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).

## Why am I unable to view the instance list and instance IDs as a RAM user?

-   Problem description

    When I logged on to the Hologres console as a RAM user and selected a valid region, I could not view the instances that I purchased. The following error message is returned: **You are not authorized to view the purchased instances. Contact the relevant Alibaba Cloud account to grant the hologram:ListInstances permission on xxx/\* to you in the RAM console**.

-   Cause

    The current RAM user does not have permissions to view the instance list in the Hologres console.

-   Solution

    Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account. Attach the **AliyunHologresReadOnlyAccess** policy to the RAM user so that the RAM user can view the instance list.


## Why am I unable to manage instances as a RAM user?

-   Problem description

    When I logged on to the Hologres console, I could not purchase an instance, upgrade or downgrade instance specifications, or change the billing method of an instance from pay-as-you-go to subscription. The following error message is returned: **Failed to authenticate the RAM user**.

-   Cause

    The current RAM user does not have permissions to purchase an instance, upgrade or downgrade instance specifications, or change the billing method of an instance. You can perform these operations by using your Alibaba Cloud account.

-   Solution

    Log on to the [RAM console](https://ram.console.aliyun.com/overview) by using your Alibaba Cloud account. Attach the **AliyunHologresFullAccess** and **AliyunBSSOrderAccess** policies to the RAM user so that the RAM user can manage instances.


