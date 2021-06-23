---
keyword: [create a RAM user within an Alibaba Cloud account, authorize a RAM user within an Alibaba Cloud account, Hologres, create a RAM user, authorize a RAM user]
---

# Use Hologres as a RAM user

This topic shows you how to use your Alibaba Cloud account to create a RAM user and authorize the RAM user to connect to and use Hologres.

By default, the system sets the Alibaba Cloud account that is used to purchase an instance as a superuser of the instance. The superuser has all permissions on the instance.

Other users can access an instance only after the required permissions are obtained from a superuser of the instance.

You can grant the following two types of permissions to a RAM user:

-   Hologres permissions that are granted in the RAM console

    Hologres permissions that are granted in the RAM console are optional. After you grant relevant Hologres permissions to a RAM user in the RAM console, you can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) as the RAM user. Then, you can purchase or delete instances, upgrade or downgrade instance specifications, modify the network configurations of instances, or view instance details.

-   Hologres permissions that are granted by using permission models

    Hologres permissions that are granted by using permission models are required. Before you can perform data analytics operations on a Hologres instance as a RAM user, you must use your Alibaba Cloud account to grant the required development permissions to the RAM user.


Hologres provides the simple permission model \(SPM\), the standard PostgreSQL authorization model, and the schema-level permission model \(SLPM\) for you to authorize RAM users. The following content describes the three permission models:

-   SPM \(Recommended\)

    Alibaba Cloud introduces the SPM to Hologres based on the PostgreSQL authorization model to enhance user experience. The SPM is a coarse-grained model that authorizes users by user group. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).

-   Standard PostgreSQL authorization model

    Hologres is compatible with PostgreSQL and provides a permission model that is the same as the standard PostgreSQL authorization model. When this model is used, you can authorize RAM users by executing standard PostgreSQL statements. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).

-   SLPM

    The SLPM divides permissions based on schemas. Compared with the SPM, the SLPM manages permissions in a finer-grained manner. If you want to strictly divide permissions and grant permissions with ease, we recommend that you use this model. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md).


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

    If you create a RAM user for the first time, you must complete the Short Message Service \(SMS\) verification.


## Create an AccessKey pair for a RAM user

An AccessKey pair ensures the proper execution of the tasks that you create in Hologres. You must create an AccessKey pair for each RAM user. To create an AccessKey pair for a RAM user, perform the following steps:

1.  In the left-side navigation pane, click **Users** under **Identities**.

2.  On the Users page, click the logon name of the RAM user for which you want to create an AccessKey pair in the **User Logon Name/Display Name** column.

3.  On the page that appears, click **Create AccessKey** in the **User AccessKeys** section.

    After an AccessKey pair is created, you can click **Download CSV File** or **Copy** to save the AccessKey pair to your computer.

4.  Click **Close**.

    **Note:**

    -   The AccessKey secret of an AccessKey pair is displayed only when you create the AccessKey pair and is unavailable for subsequent queries. We recommend that you keep the AccessKey secret confidential for subsequent use.
    -   If the AccessKey pair is disclosed or lost, you must create another AccessKey pair. You can create a maximum of two AccessKey pairs for a RAM user.

## Grant Hologres permissions to a RAM user

-   Grant relevant permissions to a RAM user in the RAM console.

    After you grant relevant Hologres permissions to a RAM user in the RAM console by using your Alibaba Cloud account, you can log on to the Hologres console and view, purchase, or delete instances as the RAM user. You can log on to the RAM console, find a RAM user, and then attach authorization policies to the RAM user. If you need to grant the RAM user all permissions to view instance information in the Hologres console, attach the **AliyunHologresFullAccess** and **AliyunRAMReadOnlyAccess** policies.

    For more information, see [Grant permissions on Hologres to RAM users](/intl.en-US/User Authorization/Grant permissions on Hologres to RAM users.md).

-   Grant relevant permissions to a RAM user by using a permission model.

    Before you can perform data analytics operations on a Hologres instance as a RAM user, you must use your Alibaba Cloud account to grant the required development permissions to the RAM user. You can log on to the Hologres console, go to the HoloWeb console, add a user on the User Management page, and then grant permissions to the user. For more information, see [Grant the development permissions on a Hologres instance to RAM users](/intl.en-US/User Authorization/Authorization operations/Grant the development permissions on a Hologres instance to RAM users.md).


## Use the Hologres service as a RAM user

After you grant a RAM user the permissions to use Hologres, you can connect to the instance that you want to manage and perform data analytics operations on the instance as the RAM user. You can use HoloWeb to perform data analytics operations in the Hologres console. For more information, see [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).

## View the permissions that are granted to a RAM user

You can view the permissions that are granted to a RAM user in one of the following ways:

-   View the permissions that are granted to a RAM user in the Hologres console.

    1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the top navigation bar, select a region.
    2.  Go to the details page of the instance that you want to manage. In the left-side navigation pane of the instance details page, click **Users** to go to the User Management page of the HoloWeb console.
    3.  View the role that is assigned to the RAM user.
    If the SPM or SLPM is enabled for your database, you can go to the **Database Authorization** page of the HoloWeb console. On the Database Authorization page, find the database that you want to manage and click **Authorize User** in the Operation column. Then, you can view the user group to which the RAM user belongs.

-   View the permissions that are granted to a RAM user by executing SQL statements.

    Use a development tool to connect to the Hologres instance to which the RAM user is added. Then, execute the following SQL statements to view the granted permissions:

    ```
    SELECT * FROM pg_roles WHERE rolname = 'p4_ID'; -- Query the role that is assigned to the specified user. 
    SELECT rolname FROM pg_roles;
    SELECT user_display_name(rolname) FROM pg_roles;
    ```


