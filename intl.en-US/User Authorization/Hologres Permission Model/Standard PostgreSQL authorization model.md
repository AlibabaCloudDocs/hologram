---
keyword: [Hologres, standard PostgreSQL authorization model, grant permissions on tables that will be created in the future]
---

# Standard PostgreSQL authorization model

Hologres is compatible with PostgreSQL and allows you to use the standard PostgreSQL authorization model. This topic shows you how to grant and revoke permissions by using the standard PostgreSQL authorization model.

## Grant permissions by using the standard PostgreSQL authorization model

After you use a development tool to connect to a Hologres instance, you can execute SQL statements to grant permissions on the instance to a user by using the standard PostgreSQL authorization model.

1.  Create a user.

    You must create a Hologres user for an account before the account can be used to access Hologres and analyze data.

    -   Execute SQL statements

        Create a user by executing an SQL statement in the following syntax:

        ```
        // Create a user that has the permission to log on to the Hologres instance. If you grant the permission to a RAM user, use the account format for a RAM user.
        CREATE USER "Alibaba Cloud account ID/Alibaba Mail address"; 
        // Create a user and grant the superuser permissions to the user.
        CREATE USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER;
        ```

        The following sample statements show how to create a user. For more information about the account formats for Alibaba Cloud accounts and RAM users, see [Account](/intl.en-US/Product Introduction/Terms.md).

        ```
        // Use the ID of an Alibaba Cloud account to create a user.
        CREATE USER "11822780xxx";
        // Grant the superuser permissions to a RAM user.
        CREATE USER "p4_1822780xxx" SUPERUSER; 
        ```

2.  Grant permissions to the user.

    After you create a Hologres user, you must grant permissions to the user so that the user can perform authorized operations in Hologres. The following table describes the commonly used statements that are executed to grant permissions in Hologres.

    **Note:** You can grant only permissions on existing objects to a user by using the standard PostgreSQL authorization model. The permissions do not take effect on new objects that are created after the authorization. For example, User A grants User B the SELECT permission on all tables in the **public** schema. Then, User A creates another table. User B does not have the SELECT permission on this table. To allow User B to query this table, User A must grant the SELECT permission to User B again.

    |Operation|Statement|Required|
    |---------|---------|--------|
    |Create a user that has the permission to log on to a Hologres instance.|    ```
CREATE USER "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|Yes|
    |Grant the superuser permissions to a user.|    ```
CREATE USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER ;
    ```

|No|
    |Grant all users the SELECT, INSERT, and UPDATE permissions on all tables in the **public** schema.|    ```
GRANT SELECT,INSERT,UPDATE ON ALL TABLES IN SCHEMA public to PUBLIC;
    ```

|No|
    |Grant a user the SELECT permission on the Table1 table.|    ```
GRANT SELECT ON TABLE Table1 TO "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|
    |Grant a user the SELECT permission on the Table1 table and authorize the user to grant the permission to other users.|    ```
GRANT SELECT ON TABLE Table1 TO "Alibaba Cloud account ID/Alibaba Mail address" WITH GRANT OPTION;
    ```

|No|
    |Grant a user the SELECT permission on all tables in the **public** schema.|    ```
GRANT SELECT ON ALL TABLES IN SCHEMA public TO "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|
    |Grant all users the read permissions as the default permissions on all tables, including the tables will be created in the future, in the **public** schema.|    ```
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC;
    ```

|No|
    |Create a user group and add a normal user to the user group.|    ```
CREATE GROUP xxx;
GRANT user group TO "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|
    |Change the role of a user from normal user to superuser.|    ```
ALTER USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER;
    ```

|No|
    |Set a user as the owner of a table created by another user.|    ```
ALTER TABLE TABLENAME OWNER TO "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|
    |Create a role that does not have the permission to log on to a Hologres instance.|    ```
CREATE ROLE "Alibaba Cloud account ID/Alibaba Mail address";
    ```

|No|

    A CREATE ROLE statement is used to create a role that does not have the permission to log on to a Hologres instance, for example, a virtual role or a user group that represents a specific user type.

3.  Grant the user the permission to delete a table.

    Only the superusers and the owner of a table can delete the table. You can authorize the user to delete a table by using one of the following methods:

    -   Set the user as the owner of the table.

        ```
        ALTER TABLE TABLENAME OWNER TO "Alibaba Cloud account ID/Alibaba Mail address";
        ```

    -   Grant the superuser permissions to the user.

        ```
        ALTER USER "Alibaba Cloud account ID/Alibaba Mail address" SUPERUSER;
        ```

    -   Add multiple users, including the user that you created, to a user group and assign the ownership of the table to the user group.

        ```
        CREATE ROLE <Group1>;
        GRANT user group TO "Alibaba Cloud account ID/Alibaba Mail address";
        ALTER TABLE TABLENAME OWNER TO <Group1>;
        ```


## Grant a user the permissions on tables that will be created in the future

The standard PostgreSQL authorization model does not allow you to grant a user the permissions on tables that will be created in the future. You can execute the ALTER DEFAULT PRIVILEGES statement to grant a user the permissions on tables that will be created in the future by performing the following steps:

**Note:**

-   This statement does not affect the permissions on existing objects.
-   This statement can be used only to grant the default permissions on tables, schemas, functions, sequences, and types.

1.  Grant the default permissions.

    Grant all users the read permissions as the default permissions on all tables that will be created in the future in the **public** schema. Sample statement:

    ```
    ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO PUBLIC;
    ```

2.  Check whether the default permissions are granted.

    Run the \\ddp command on the PostgreSQL client to view the result of the ALTER DEFAULT PRIVILEGES statement.

    When you create a table as a user, Hologres checks the user and schema that you use based on the pg\_catalog.pg\_default\_acl system table. If an ALTER DEFAULT PRIVILEGES statement is detected, Hologres uses the relevant matching rule for authentication. The system provides the following matching rules:

    -   If you perform operations as a user, Hologres checks whether you use a user that has the default permissions.
    -   If you execute the `SET SESSION ROLE GROUP1;` statement before you create a table as a user, the GROUP1 role is assigned to the user. Then, Hologres checks whether the GROUP1 role has the default permissions.
    The matching rules take effect only when you create tables. If you execute the `ALTER TABLE SET OWNER TO` statement to modify the table owner after a table is created, the matching rules are not triggered.


## Revoke permissions by using the standard PostgreSQL authorization model

To revoke specific permissions from a user, execute the following REVOKE statement:

```
REVOKE SELECT ON TABLE tablename FROM "Alibaba Cloud account ID/Alibaba Mail address" ; // If you need to revoke permissions from a RAM user, use the account format for a RAM user.
```

## View the permissions of a user

You can view the permissions of a user in a Hologres instance.

-   Execute SQL statements

    Execute the following SQL statements to view the permissions of a user:

    ```
    SELECT ROLNAME FROM pg_roles;
    SELECT user_display_name(ROLNAME) FROM pg_roles;
    ```


## Delete a user

You can delete a RAM user from a Hologres instance by using one of the following methods:

**Note:** After you delete a RAM user from a Hologres instance, the RAM user cannot connect to the instance or access objects in the instance. Proceed with caution.

-   Execute SQL statements

    Execute the following SQL statement to delete a RAM user:

    ```
    DROP USER "Alibaba Cloud account ID/Alibaba Mail address";
    ```

-   Use the Hologres console

    Log on to the **Hologres console**. In the left-side navigation pane, click **Instances**. Click the name of the instance that you want to view to go to the instance details page. On the instance details page, click the Users tab. Find the user that you want to delete and click Delete in the **Actions** column.

    ![729](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9754158951/p95315.png)


