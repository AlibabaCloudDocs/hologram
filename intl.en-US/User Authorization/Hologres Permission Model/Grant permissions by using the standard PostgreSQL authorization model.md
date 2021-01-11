---
keyword: [authentication process, Hologres, grant permissions by using the standard PostgreSQL authorization model, grant permissions on new tables]
---

# Grant permissions by using the standard PostgreSQL authorization model

Compatible with PostgreSQL, Hologres uses a permission model that is exactly the same as the standard PostgreSQL authorization model. This topic describes how to grant and revoke permissions by using the standard PostgreSQL authorization model.

## Authentication process

The following figures display the authentication processes in Hologres in different scenarios:

-   When a user tries to access an existing object in a Hologres instance, Hologres automatically checks the permissions of the user, as shown in the following figure.

    ![727](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8754158951/p95305.png)

-   When a user or a user group tries to create an object in a Hologres instance, Hologres automatically checks the permissions of the user or the user group, as shown in the following figure.

    ![728](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8754158951/p95307.png)


## Grant permissions by using the standard PostgreSQL authorization model

**Note:** You can only grant permissions on existing objects to a user by using the standard PostgreSQL authorization model.

1.  Add a user to a Hologres instance and assign a role to the user.

    Log on to the Hologres console as a superuser and add a user to a Hologres instance. Then, you can connect to the Hologres instance as the user. To add a user, execute the following statement:

    ```
    CREATE USER "Alibaba Cloud account ID/Alibaba Mail address"; // This statement is only used to add a user. The added user is unauthorized to connect to the Hologres instance.
    ```

    To add a user and assign a role to the user, execute the following statements:

    ```
    CREATE USER "holo_admin:holo_user1";
    CREATE USER "holo_admin:holo_user1" SUPERUSER; // Assign the superuser role to the RAM user.
    ```

    If you need to grant permissions to a Resource Access Management \(RAM\) user, specify the RAM user in the required format. For more information, see [Account](/intl.en-US/Product Introduction/Terms.md).

    You must execute a `CREATE USER` statement to authorize a RAM user to connect to a Hologres instance.

    You can also add a user in the [Hologres console](https://hologram.console.aliyun.com/#/instance) in visualized mode. For more information, see add a user.

2.  Grant permissions to the user.

    After you add a user to a Hologres instance, you must grant the required permissions to the user to perform operations on the instance. The following table describes the statements for granting common permissions.

    |Description|Statement|Required|
    |-----------|---------|--------|
    |Add a user to a Hologres instance and grant the access permission on the instance to the user.|`CREATE USER "Alibaba Cloud account ID/Alibaba Mail address";`|Yes|
    |Grant the superuser permissions to a user.|`CREATE USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER ;`|No|
    |Grant all users in a Hologres instance the query, add, and modify permissions on all tables under the **public** schema.|`GRANT SELECT,INSERT,UPDATE ON ALL TABLES IN SCHEMA public to PUBLIC;`|No|
    |Grant a user in a Hologres instance the query permission on the Table1 table.|`GRANT SELECT ON TABLE Table1 TO "Alibaba Cloud account ID/Alibaba Mail address";`|No|
    |Grant a user in a Hologres instance the query permission on the Table1 table and authorize the user to grant the permission to other users.|`GRANT SELECT ON TABLE Table1 TO "Alibaba Cloud account ID/Alibaba Mail address" WITH GRANT OPTION;`|No|
    |Grant a user in a Hologres instance the query permission on all tables under the **public** schema.|`GRANT SELECT ON ALL TABLES IN SCHEMA public TO "Alibaba Cloud account ID/Alibaba Mail address";`|No|
    |Grant all users in a Hologres instance the query permission on all tables, including the new tables, under the **public** schema.|`ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC;`|No|
    |Create a user group and add a normal user to the group.|    ```
CREATE GROUP xxx;
GRANT user group TO "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|
    |Change the role type of a user from normal user to superuser.|`ALTER USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER;`|No|
    |Set a user as the owner of a table created by another user.|`ALTER TABLE TABLENAME OWNER TO "Alibaba Cloud account ID/Alibaba Mail address";`|No|
    |Create a role in a Hologres instance that does not have the access permission on the instance.|`CREATE ROLE "Alibaba Cloud account ID/Alibaba Mail address";`|No|

    A `CREATE ROLE` statement is used to create a role that does not have the access permission on a Hologres instance, for example, a virtual role or a user group that represents a specific user type.

3.  Delete a table in the Hologres instance as the user.

    Only the superusers and owner of a table can delete the table. Therefore, you must authorize the user to delete a table in one of the following ways:

    -   Execute the following statement to set the user as the owner of the target table:

        ```
        ALTER TABLE TABLENAME OWNER TO "Alibaba Cloud account ID/Alibaba Mail address";
        ```

    -   Execute the following statement to grant the superuser permissions to the user:

        ```
        ALTER USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER;
        ```

    -   Execute the following statements to add multiple users, including the target user, to a user group, and assign the ownership of the table to the user group.

        ```
        CREATE ROLE <Group1>;
        GRANT user group TO "Alibaba Cloud account ID/Alibaba Mail address";
        ALTER TABLE TABLENAME OWNER TO <Group1>;
        ```


## Grant a user the permissions on new tables

To execute the `ALTER DEFAULT PRIVILEGES` statement to grant a user the permissions on new tables, perform the following steps:

**Note:**

-   This statement does not affect the permissions on existing objects.
-   This statement can be used only to grant the default permissions on tables, schemas, functions, sequences, and types.

1.  Grant a user the default permissions on new tables.

    Execute the following statement to grant all users in a Hologres instance the query permission on all new tables under the **public** schema. This is the default permission.

    ```
    ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC;
    ```

2.  Check whether the permission is granted.

    Run the `\ddp` command on the PostgreSQL client to view the result of the `ALTER DEFAULT PRIVILEGES` statement.

    When you create a table as a user, Hologres checks the user and schema that you use based on the `pg_catalog.pg_default_acl` system table. If an `ALTER DEFAULT PRIVILEGES` statement is detected, Hologres uses the relevant matching rule for authentication. The system provides the following matching rules:

    -   If you perform operations as a user, Hologres checks whether you use a user that has the default permission.
    -   If you execute the `SET SESSION ROLE GROUP1;` statement before you create a table as a user, the user turns into the GROUP1 role. Then, Hologres checks whether the GROUP1 role has the default permission.
    The matching rules take effect only when you create tables. If you execute the `ALTER TABLE SET OWNER TO` statement to modify the table owner after a table is created, the matching rules will not be triggered.


## Revoke permissions by using the standard PostgreSQL authorization model

To revoke specific permissions from a user, execute the following REVOKE statement:

```
REVOKE SELECT ON TABLE tablename FROM "Alibaba Cloud account ID/Alibaba Mail address" ; // If you need to revoke permissions from a RAM user, specify the RAM user in the required format.
```

## View permissions of a user

You can use the following methods to view permissions of a user in a Hologres instance:

-   Use PostgreSQL statements

    Execute the following PostgreSQL statements to view the permissions of a user:

    ```
    SELECT ROLNAME FROM pg_roles;
    SELECT user_display_name(ROLNAME) FROM pg_roles;
    ```

-   Use the Hologres console
    1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).
    2.  On the Instances page, click the name of the target instance. The instance details page appears.
    3.  In the left-side navigation pane of the instance details page, click **Users**.
    4.  On the **Users** tab, view the value in the Type column of the target user, which indicates the role assigned to the user.

        ![729](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9754158951/p95315.png)


## Delete a user from a Hologres instance

You can use the following methods to delete a user from a Hologres instance:

**Note:** After you delete a user from a Hologres instance, the user cannot connect to the instance or access any objects in the instance. Proceed with caution.

-   Use PostgreSQL statements

    Execute the following PostgreSQL statement to delete a user from a Hologres instance:

    ```
    DROP USER "Alibaba Cloud account ID/Alibaba Mail address";
    ```

-   Use the Hologres console
    1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).
    2.  On the Instances page, click the name of the target instance. The instance details page appears.
    3.  In the left-side navigation pane of the instance details page, click **Users**.
    4.  On the **Users** tab, find the target user and click **Delete** in the Actions column.

        ![729](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9754158951/p95315.png)


