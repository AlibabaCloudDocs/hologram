---
keyword: [Hologres, user management, database management]
---

# User and database management

In the Hologres console, you can click the Users and Databases tabs on the instance details page to go to the HoloWeb console, where you can manage the users and databases in your Hologres instance, and grant permissions to the users.

## User management

You can click the **Users** tab on the details page of a Hologres instance to go to the User Management page in the HoloWeb console.

On the User Management page, you can add and remove users, and grant permissions to the users in your instance in a visualized manner. For more information about user management, see [Manage users](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/Manage users.md).

After you purchase a Hologres instance by using your Alibaba Cloud account, this account becomes a superuser of the instance. The superuser has all permissions on the Hologres instance. By default, the User Management page displays only the information about the Alibaba Cloud account that is used to purchase the instance. The information about a RAM user appears on this page only after you use the Alibaba Cloud account to add the RAM user to the instance.

![User management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0910493261/p168561.png)

## Database management

You can click the **Databases** tab on the details page of a Hologres instance to go to the Database Authorization page in the HoloWeb console.

On the **Database Authorization** page, you can create and delete databases in a visualized manner, and specify a permission model for a database. For more information about database management, see [Manage databases](/intl.en-US/Common Development Tools/HoloWeb/System Mangement/Manage databases.md).

**Note:** After you purchase a Hologres instance, a default database named **postgres** is generated. This database is provided for management purposes only. It is allocated with a few resources and is not displayed on the Database Authorization page. You can create databases based on your business requirements.

