---
keyword: [create a RAM user within an Alibaba Cloud account, authorize a RAM user within an Alibaba Cloud account, Hologres, create a RAM user, authorize a RAM user]
---

# Use Hologres as a RAM user

This topic shows you how to use your Alibaba Cloud account to create a RAM user and authorize the RAM user to connect to and use Hologres.

By default, the system sets the Alibaba Cloud account that is used to purchase an instance as a superuser of the instance. The superuser has all permissions on the instance.

Other users can access an instance only after the required permission is obtained from a superuser of the instance.

You can grant the following two types of permissions to a RAM user:

-   Hologres permissions that are granted in the RAM console

    Hologres permissions that are granted in the RAM console are optional. After you grant relevant Hologres permissions to a RAM user in the RAM console, you can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) as the RAM user. Then, you can purchase or delete instances, upgrade or downgrade instance specifications, modify the network configurations of instances, or view instance details.

-   Hologres permissions that are granted by using permission models

    Hologres permissions that are granted by using permission models are required. Before you can perform data analytics operations on a Hologres instance as a RAM user, you must use your Alibaba Cloud account to grant the required development permissions to the RAM user.


Hologres provides a simple permission model \(SPM\) and a standard PostgreSQL authorization model for you to authorize RAM users. The following content describes the two permission models:

-   SPM \(Recommended\)

    Alibaba Cloud introduces the SPM to Hologres based on the PostgreSQL authorization model to enhance user experience. The SPM is a coarse-grained model that authorizes users by user group. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).

-   Standard PostgreSQL authorization model

    Hologres is compatible with PostgreSQL and provides a permission model that is exactly the same as the standard PostgreSQL authorization model. When this model is used, you can authorize RAM users by using standard PostgreSQL statements. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).


## Create a RAM user

If you have created a RAM user, skip this step.

1.  Log on to the [International site \(alibabacloud.com\)](https://www.alibabacloud.com/) by using your Alibaba Cloud account.

2.  Go to the [RAM console](https://ram.console.aliyun.com/overview).

3.  In the left-side navigation pane, click **Users** under **Identities**.

4.  On the Users page, click **Create User**.

    On the Create User page, you can click **Add User** to create multiple RAM users at a time.

5.  In the **User Account Information** section, enter a logon name in the **Logon Name** field and a display name in the **Display Name** field.

6.  In the **Access Mode** section, select **Console Access**.

7.  Set a password for the RAM user.

8.  Click **OK**.

    If you create a RAM user for the first time, SMS verification is required.


## Create an AccessKey pair for a RAM user

An AccessKey pair ensures proper execution of the tasks that you create in Hologres. You must create an AccessKey pair for each RAM user. To create an AccessKey pair for a RAM user, perform the following steps:

1.  In the left-side navigation pane, click **Users** under **Identities**.

2.  On the Users page, click the logon name of the RAM user for which you want to create an AccessKey pair in the **User Logon Name/Display Name** column.

3.  On the page that appears, click **Create AccessKey** in the **User AccessKeys** section.

    You can click **Download CSV File** or **Copy** to save the AccessKey pair to your on-premises computer.

4.  Click **Close**.

    **Note:**

    -   The AccessKey secret is displayed only when you create an AccessKey pair and is unavailable for subsequent queries. We recommend that you keep the AccessKey secret confidential for subsequent use.
    -   If the AccessKey pair is disclosed or lost, you must create another AccessKey pair. You can create a maximum of two AccessKey pairs for a RAM user.

## Grant Hologres permissions to a RAM user

-   Grant relevant permissions to a RAM user in the RAM console.

    After you grant relevant Hologres permissions to a RAM user in the RAM console by using your Alibaba Cloud account, you can log on to the Hologres console and view, purchase, or delete instances as the RAM user. For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).

-   Grant relevant permissions to a RAM user by using a permission model.

    Before you can perform data analytics operations on a Hologres instance as a RAM user, you must use your Alibaba Cloud account to grant the required development permissions to the RAM user. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).


## Use Hologres as a RAM user

After you grant a RAM user permissions to use Hologres, you can connect to the instance that you want to manage from the PostgreSQL client and perform data analytics operations on the instance as the RAM user. For more information, see [Connect to a Hologres instance from the PostgreSQL client](/intl.en-US/Common Development Tools/Connect to a Hologres instance from the PostgreSQL client.md).

## View the permissions that are granted to a RAM user

You can view the permissions that are granted to a RAM user by using the following method:

-   View the permissions that are granted to a RAM user by executing SQL statements.

    Connect to the Hologres instance to which the RAM user is added from the PostgreSQL client and execute the following SQL statements to view the granted permissions:

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_user ID'; // Query the role that is assigned to the specified user.
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


