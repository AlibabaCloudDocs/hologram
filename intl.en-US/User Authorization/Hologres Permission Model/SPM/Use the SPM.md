---
keyword: [SPM, Hologres, procedure]
---

# Use the SPM

This topic describes how to use the simple permission model \(SPM\) in Hologres.

## Authorize users by executing SQL statements

1.  Enable function calls.

    Before you enable the SPM, you must execute the following statement to enable function calls:

    ```
    create extension spm;
    ```

2.  Enable the SPM.

    By default, the SPM is disabled. You can execute the following statement to enable the SPM for a database:

    ```
    call spm_enable (); // Enable the SPM for the current database.
    ```

    **Note:** After you enable the SPM for a database, add all the database users to user groups. Otherwise, the users cannot access the database and the business may be affected.

3.  Switch from the standard PostgreSQL authorization model to the SPM for existing objects.

    If the database used the standard PostgreSQL authorization model before and the database contains objects, such as tables, views, and foreign tables, you must transfer the ownership of these objects to the database developers that you specified in the SPM. Otherwise, users do not have the permissions to manage the objects and the business may be affected. Execute the following statement to transfer the ownership of objects:

    ```
    call spm_migrate (); // Transfer the ownership of existing objects in the database to the database developers that are specified in the SPM.
    ```

    If the database contains no objects, skip this step.

    **Note:** When you enable the SPM for a database, make sure that no SQL statement is running in the database. Otherwise, you may fail to enable the SPM and the business may be affected.

    The spm\_migrate function may transfer the ownership of a large number of objects at a time. This consumes more shared memory than that the operating system allocates to PostgreSQL. To resolve this issue, Hologres uses the max\_locks\_per\_transaction parameter of PostgreSQL to limit the number of database objects that can be simultaneously processed. In this case, the spm\_migrate function can transfer the ownership for objects of no more than the value of the max\_locks\_per\_transaction parameter at a time. As a result, you may need to repeat calling the spm\_migrate function until the ownership of all objects is transferred.

4.  Create a user in the Hologres instance.

    Before you authorize a user, you must create the user in the Hologres instance. If you need to authorize an existing user in the instance, skip this step.

    ```
    call spm_create_user ('ID of an Alibaba Cloud account, email address in the cloud, or UID of a RAM user prefixed with p4_'); // Create a user in the Hologres instance. For a RAM user, prefix the UID of the RAM user with p4_ and enclose it in double quotation marks (" "), that is, "p4_uid".
    call spm_create_user ('ID of an Alibaba Cloud account, email address in the cloud, or UID of a RAM user prefixed with p4_', '<dbname>_[admin|developer|writer|viewer]'); // Create a user in the Hologres instance and add the user to a user group of a database. For a RAM user, prefix the UID of the RAM user with p4_ and enclose it in double quotation marks (" "), that is, "p4_uid".
    ```

    **Note:**

    -   When you call the `spm_create_user` function to add a RAM user to a Hologres instance, prefix the UID of the RAM user with p4\_ and enclose it in double quotation marks \(" "\), that is, "p4\_uid".
    -   For more information about the formats of display names for RAM users in Hologres, see [Account](/intl.en-US/Product Introduction/Terms.md).
5.  Add a user to a user group.

    After you create a user in the Hologres instance, add the user to a user group in a database to authorize the user. To add a user to a user group, execute the following statement:

    ```
    
    call spm_grant ('<dbname>_[admin|developer|writer|viewer]', 'ID of an Alibaba Cloud account, email address in the cloud, or UID of a RAM user prefixed with p4_'); // Add a user to a user group.
    
    Examples:
    // Add a user to the <db>_admin group of a database.
    call spm_grant ('mydb_admin', 'p4_564306222995xxx', ); // Add the RAM user whose UID is 564306222995xxx to the <mydb>_admin group.
    call spm_grant ('mydb_admin', '197006222995xxx', ); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the <mydb>_admin group.
    call spm_grant ('mydb_admin', 'ALIYUN$xxx'); // Add the user whose email address is xxx@aliyun.com to the <mydb>_admin group.
    
    // Add a user to the <db>_developer group of a database.
    call spm_grant ('mydb_developer', 'p4_564306222995xxx'); // Add the RAM user whose UID is 564306222995xxx to the <mydb>_developer group.
    call spm_grant ('mydb_developer', '197006222995xxx'); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the <mydb>_developer group.
    call spm_grant ('mydb_developer', 'RAM$mainaccount:subuser');// Add the RAM user subuser of the Alibaba Cloud account mainaccount to the <mydb>_developer group.
    
    // Add a user to the <db>_viewer group of a database.
    call spm_grant ('"MYDB_viewer"', 'p4_564306222995xxx'); // Add the RAM user whose UID is 564306222995xxx to the <MYDB>_viewer group.
    call spm_grant ('"MYDB_viewer"', '197006222995xxx'); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the <MYDB>_viewer group.
    call spm_grant ('mydb_viewer', '"xxx@aliyun.com"'); // Add the user whose email address is xxx@aliyun.com to the <mydb>_viewer group.
    ```

    **Note:** If you have specified a user group for a user when you create the user, skip this step.

    After you authorize a RAM user, you can use the PostgreSQL client to connect to the current database and develop the database as the RAM user.


## Remove a user from a user group

To remove a user from a user group, execute the following statement:

```
call spm_revoke ('<dbname>_[admin|developer|writer|viewer]', 'ID of an Alibaba Cloud account, email address in the cloud, or UID of a RAM user prefixed with p4_'); // Remove a user from a user group.

Examples:
// Remove a user from the <db>_admin group of a database.
call spm_revoke ('dbname_admin', 'p4_564306222995xxx');// Remove the RAM user whose UID is 564306222995xxx from the <db>_admin group of the specified database.
call spm_revoke ('dbname_admin', '197006222995xxx');// Remove the user whose Alibaba Cloud account ID is 197006222995xxx from the <db>_admin group of the specified database.
call spm_revoke ('dbname_admin', '"xxx@aliyun.com"');

// Remove a user from the <db>_developer group of a database.
call spm_revoke ('mydb_developer', 'RAM$mainaccount:subuser'); // Remove the RAM user subuser from the <mydb>_developer group.
call spm_revoke ('mydb_developer', 'p4_564306222995xxx');// Remove the RAM user whose UID is 564306222995xxx from the <mydb>_developer group.

// Remove a user from the <db>_viewer group of a database.
call spm_revoke ('"MYDB_viewer"', 'p4_564306222995xxx'); // Remove the RAM user whose UID is 564306222995xxx from the <MYDB>_viewer group.
```

## Delete a user from a Hologres instance

To delete a user from a Hologres instance, execute the following statement:

**Note:** After a user is deleted from a Hologres instance, the user has no permission on the instance. Proceed with caution.

```
DROP ROLE "ID of an Alibaba Cloud account, email address in the cloud, or UID of a RAM user prefixed with p4_"; // Delete a user from a Hologres instance.
```

## Switch from the standard PostgreSQL authorization model to the SPM for existing objects

If a database used the [Grant permissions by using the standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres Permission Model/Grant permissions by using the standard PostgreSQL authorization model.md) before and the database contains objects, such as tables, views, and foreign tables, you can enable the SPM for better permission management. To call the spm\_migrate function to transfer the ownership of objects, execute the following statement:

```
call spm_migrate (); // Transfer the ownership of the objects to the database developers that are specified in the SPM.
```

**Note:** When you enable the SPM for a database, make sure that no SQL statement is running in the database. Otherwise, you may fail to enable the SPM and the business may be affected.

The spm\_migrate function may transfer the ownership of a large number of objects at a time. This consumes more shared memory than that the operating system allocates to PostgreSQL. To resolve this issue, Hologres uses the max\_locks\_per\_transaction parameter of PostgreSQL to limit the number of database objects that can be simultaneously processed. In this case, the `spm_migrate` function can transfer the ownership for objects of no more than the value of the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter at a time. As a result, you may need to repeat calling the `spm_migrate` function until the ownership of all objects is transferred.

## Disable the SPM

1.  Disable the SPM.

    A superuser can execute the following statement to call the spm\_disable function to disable the SPM for a database:

    ```
    call spm_disable ();
    ```

    The user groups of a database are not deleted if a superuser disables the SPM for the database. For more information about the permissions of the users in the user groups, see [Functions of the SPM](/intl.en-US/User Authorization/Hologres Permission Model/SPM/Functions of the SPM.md).

2.  Delete the user groups of a database.

    After the SPM is disabled, you can call the `spm_cleanup` function to delete user groups of a database in one of the following cases:

    **Note:** Generally, we recommend that you do not delete user groups because they can facilitate user management.

    -   Case 1: Delete user groups of an existing database

        To delete user groups but reserve the database, execute the following statement as a superuser:

        ```
        call spm_cleanup ( '<dbname>' );
        ```

        **Note:** When you call the `spm_cleanup` function, make sure that no SQL statement is running on the database. Otherwise, the function may fail and the business may be affected.

        This operation may transfer the ownership of a large number of business objects. The `spm_cleanup` function transfers the ownership for objects of no more than the value of the `max_locks_per_transaction` parameter. As a result, you may need to repeat calling the `spm_cleanup` function until the ownership of all objects is transferred and all user groups of the database are deleted.

    -   Case 2: Delete user groups of a deleted database

        To delete user groups of a deleted database, execute the following statement in another database such as postgres as a superuser:

        ```
        postgres=# call spm_cleanup ( 'mydb' );
        ```


Precautions for disabling the SPM

-   Only a superuser can disable the SPM for a database.
-   After the SPM is disabled, all users are granted the USAGE and CREATE permissions on the public schema of the database.
-   After the SPM is disabled, all users are granted the CONNECT and TEMPORARY permissions on the database.
-   After the SPM is disabled, all users are granted the EXECUTE permission on the functions and procedures of the database.
-   After the SPM is disabled, all users are granted the USAGE permission on the languages and data types, including domains, of the database.
-   After the SPM is disabled, users have no permission on the objects other than the preceding ones, including tables, views, materialized views, columns, sequences, foreign data wrappers, foreign servers, and schemas \(excluding the public schema\).
-   After the SPM is disabled, the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups have the following permissions:
    -   <db\>\_admin: reserves the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_developer: reserves the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_writer: reserves the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_viewer: reserves the obtained permissions on existing objects and has no permission on new objects.

