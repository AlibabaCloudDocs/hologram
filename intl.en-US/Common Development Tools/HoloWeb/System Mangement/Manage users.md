---
keyword: [user management, Hologres, manage user permissions]
---

# Manage users

In the Hologres console, you can click the Users tab on the instance details page to go to the User Management page in the HoloWeb console, where you can add and remove users and grant permissions to the users in your Hologres instance. The User Management page allows you to manage users in a visualized manner.

After you purchase a Hologres instance by using your Alibaba Cloud account, this account becomes a superuser of the instance. The superuser has all permissions on the Hologres instance. By default, the User Management page displays only the information about the Alibaba Cloud account that is used to purchase the instance. The information about a RAM user appears on this page only after you use the Alibaba Cloud account to add the RAM user to the instance. The following table describes the GUI elements on the User Management page.

|GUI element|Description|
|-----------|-----------|
|Members|The name of the user in the instance. The value can be the name of an Alibaba Cloud account or that of a RAM user.|
|Cloud Account|The ID of the user in the instance. Examples:

-   Example for an Alibaba Cloud account that is added as a user: `11822780xxx`
-   Example for a RAM user that is added as a user: `p4_269499383xxxx` |
|Type|The role that is assigned to the user in the instance. Valid values: -   Superuser
-   Normal |
|Operation|The operation that you can perform on the user in the instance. You can click **Delete** in the **Operation** column to remove the user from the instance. After the user is removed, it has not permissions on the instance.|

## Add a user

On the User Management page, you can add a user to the specified Hologres instance in a visualized manner. To add a user, perform the following steps:

1.  In the upper part of the **User Management** page, select an instance from the Instance Name drop-down list. In the upper-right corner of the page, click **Add New User**.
2.  In the **Add New User** dialog box, select a RAM user within the current Alibaba Cloud account and set the Select Member Role parameter. You can assign the superuser or regular user role to the user.

    ![Add New User dialog box](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6210493261/p275142.png)

    **Note:** If no RAM users exist within your Alibaba Cloud account, create a RAM user. For more information, see [Use Hologres as a RAM user](/intl.en-US/Quick Start/Use Hologres as a RAM user.md).

    -   A superuser of a Hologres instance has all permissions on the instance. If the superuser role is assigned to a RAM user, no further authorization is required for the RAM user.
    -   A regular user added to a Hologres instance has no query or management permissions on the objects, such as databases, schema, and tables, in the instance. The regular user must obtain the required permissions before it can query or manage the objects in the instance.

        We recommend that you go to the Database Authorization page to grant permissions to regular users in a visualized manner. For more information, see [Manage databases](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/Manage databases.md). You can also execute SQL statements to grant permissions. For more information, see [Use Hologres as a RAM user](/intl.en-US/Quick Start/Use Hologres as a RAM user.md).

    **Note:** You must use an Alibaba Cloud account to add users to Hologres instances.


## Remove a user

To remove a user from a Hologres instance, select the instance from the Instance Name drop-down list in the upper part of the **User Management** page. Click **Delete** in the **Operation** column of the user that you want to remove. In the Delete User message, click OK. After the user is removed from the instance, the user has no permissions on the instance.

![Remove a user](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6210493261/p275143.png)

