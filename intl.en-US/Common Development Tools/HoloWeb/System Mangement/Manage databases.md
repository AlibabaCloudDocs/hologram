---
keyword: [create a database, Hologres, grant permissions to a user, delete a database]
---

# Manage databases

This topic shows you how to create and delete databases and specify a permission model for a database on the Database Authorization page in the HoloWeb console.

## Create a database and specify a permission model

On the Database Authorization page, you can create databases in a visualized manner.

1.  In the upper part of the **Database Authorization** page, select a Hologres instance from the Instance Name drop-down list. In the upper-right corner of the page, click **Create Database**.

2.  In the **Create Database** dialog box, check the instance name, enter a database name in the **Database Name** field, and then select a permission model based on your business requirements for the **SPM** parameter.

    ![Create Database dialog box](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610493261/p275264.png)

    We recommend that you select the simple permission model \(SPM\) to facilitate authorization.

    Hologres provides the following complete and convenient permission models:

    -   Standard PostgreSQL authorization model: Hologres is compatible with PostgreSQL and provides a permission model that is exactly the same as the standard PostgreSQL authorization model. When this model is used, you can authorize RAM users by executing standard PostgreSQL statements. For more information, see [Standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md).
    -   SPM: Alibaba Cloud introduces the SPM to Hologres based on the PostgreSQL authorization model to enhance user experience. The SPM is a coarse-grained model that authorizes users by user group. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).
    -   Schema-level permission model \(SLPM\): This model divides permissions based on schemas. Compared with the SPM, this model manages permissions in a more fine-grained manner. If you want to strictly divide permissions and grant permissions with ease, we recommend that you use this model. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SLPM/Overview.md).
    **Note:** After you purchase a Hologres instance, a default database named **postgres** is generated. This database is provided for management purposes only. It is allocated with a few resources and is not displayed on the Database Authorization page. You can create databases based on your business requirements.


## Grant permissions to a user

If you specify the SPM for a created database, you can grant permissions to a user in the current instance in a visualized manner on the Database Authorization page.

1.  On the **Database Authorization** page, click **Authorize User** in the **Operation** column of the database the permissions on which you want to grant. The **User Authorization** page appears.

2.  On the **User Authorization** page of the database, click **Grant Permissions** in the upper-right corner.

3.  In the **Grant Permissions** dialog box, set the **User Account** and **User Group** parameters.

    ![Grant Permissions dialog box](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610493261/p275312.png)

    Valid values of the User Group parameter:

    -   Admin: Users in this group are the owners of the current database and are authorized to manage all objects and user groups.
    -   Developer: Users in this group are authorized to read and write data in the current database, and create, delete, or modify objects by executing data definition language \(DDL\) statements.
    -   Writer: Users in this group are authorized to read and write data in the current database.
    -   Viewer: Users in this group are authorized to read data in the current database.
4.  Click **OK**. Then, the user can connect to the Hologres instance by using a development tool to analyze data.

    **Note:** If you cannot find the required RAM user in the **User Account** drop-down list, the RAM user is not added to the current instance as a user. In this case, add the RAM user on the **User Management** page.


## Revoke permissions from a user

If you specify the SPM for a created database, you can revoke permissions from a user in the current instance in a visualized manner on the Database Authorization page.

1.  On the **Database Authorization** page, click **Authorize User** in the **Operation** column of the database the permissions on which you want to revoke. The **User Authorization** page appears.

2.  Click **Revoke Permissions** in the **Operation** column of the user from which you want to revoke permissions.

    ![Revoke Permissions](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610493261/p275314.png)

3.  In the message that appears, click **OK**.

    For information about the remaining permissions that the user has, see [Use the SPM](/intl.en-US/User Authorization/Hologres permission models/SPM/Use the SPM.md).


## Delete a database

On the **Database Authorization** page, you can click **Delete** in the **Operation** column of a database that you no longer need to delete the database. After the database is deleted, its data is also deleted and cannot be restored.

![Delete a database](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610493261/p275319.png)

