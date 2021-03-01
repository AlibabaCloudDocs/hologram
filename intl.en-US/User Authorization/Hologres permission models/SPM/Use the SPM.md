---
keyword: [SPM, Hologres, procedure]
---

# Use the SPM

This topic describes how to use the simple permission model \(SPM\) in Hologres.

## Use the SPM to authorize users

When you use the SPM to authorize users of an instance in Hologres, you can use the following method:

-   Execute SQL statements to authorize users

    After you connect a Hologres instance to a development tool, you can execute SQL statements to authorize a user based on the SPM. This way, the user is granted the required permissions on the instance. To authorize a user by using the SPM, perform the following steps:


1.  Enable function calls.

    Before you enable the SPM, you must execute the following statement to enable function calls:

    ```
    create extension spm;
    ```

2.  Enable the SPM.

    By default, the SPM is disabled. You can execute the following statement to enable the SPM for a database. For more information about the spm\_enable function, see [spm\_enable](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.mdsection_3jy_fy7_o1f).

    ```
    call spm_enable (); // Enable the SPM for the current database.
    ```

    **Note:** After you enable the SPM for a database, users in the developer group are granted permissions on tables and table-like objects in all schemas in a database.

3.  Switch from the standard PostgreSQL authorization model to the SPM for existing objects.

    If the database uses the standard PostgreSQL authorization model and the database contains objects such as tables, views, and foreign tables, you must transfer the ownership of these objects to the developers that you specify in the SPM. Otherwise, users do not have the permissions to manage the objects and the business may be affected. Execute the following statement to transfer the ownership of objects:

    ```
    call spm_migrate (); // Transfer the ownership of existing objects in the database to the developers that are specified in the SPM.
    ```

    If the database contains no objects, skip this step.

    **Note:** When you enable the SPM for a database, make sure that no SQL statement is being executed in the database. Otherwise, you may fail to enable the SPM and the business may be affected.

    The spm\_migrate function may transfer the ownership of a large number of objects at a time. However, the number of objects for which you can transfer the ownership at a time by calling the spm\_migrate function is no more than the value of the max\_locks\_per\_transaction parameter. As a result, you may need to repeat calling the spm\_migrate function until the ownership of all objects is transferred. For more information about the spm\_migrate function, see [spm\_migrate](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.md).

4.  Create a user in the Hologres instance.

    Before you authorize a user, you must create the user in the Hologres instance. If you need to authorize an existing user in the instance, skip this step. For more information about the spm\_create\_user function, see [spm\_create\_user](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.mdsection_6gf_zbc_oon).

    ```
    call spm_create_user ('Alibaba Cloud account ID/Alibaba Mail address/RAM user UID'); // Create a user in the Hologres instance. For a RAM user, prefix the UID of the RAM user with p4_ and enclose it in double quotation marks (" "), such as "p4_uid".
    call spm_create_user ('Alibaba Cloud account ID/Alibaba Mail address/RAM user UID', '<dbname>_[admin|developer|writer|viewer]'); // Create a user in the Hologres instance and add the user to a user group of a database. For a RAM user, prefix the UID of the RAM user with p4_ and enclose it in double quotation marks (" "), such as "p4_uid".
    ```

    **Note:**

    -   When you call the `spm_create_user` function to add a RAM user to a Hologres instance, prefix the UID of the RAM user with p4\_ and enclose it in double quotation marks \(" "\), such as "p4\_uid".
    -   For more information about the formats of display names for RAM users in Hologres, see [Account](/intl.en-US/Product Introduction/Terms.md).
5.  Add a user to a user group.

    After you create a user in the Hologres instance, add the user to a user group in a database to authorize the user. After you authorize a RAM user, you can use the PostgreSQL client to connect to the current database and develop the database as the RAM user. If you have specified a user group for a user when you create the user, skip this step. For more information about the spm\_grant function, see [spm\_grant](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.md).

    In the following statement, the \{dbname\}\_\[admin\|developer\|writer\|viewer\] clause is used to specify a user group in the current database. For more information, see [Overview](/intl.en-US/User Authorization/Hologres permission models/SPM/Overview.md).

    ```
    
    call spm_grant ('<dbname>_[admin|developer|writer|viewer]', 'Alibaba Cloud account ID/Alibaba Mail address/RAM user UID'); // Add a user to a user group.
    ```

    You can execute one of the following statements to add a user to a specified user group:

    ```
    // Add a user to the <db>_admin group of a database.
    call spm_grant ('mydb_admin', 'p4_564306222995xxx', ); // Add the RAM user whose UID is 564306222995xxx to the mydb_admin group.
    call spm_grant ('mydb_admin', '197006222995xxx', ); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the mydb_admin group.
    call spm_grant ('mydb_admin', 'ALIYUN$xxx'); // Add the user whose Alibaba Mail address is xxx@aliyun.com to the mydb_admin group.
    
    // Add a user to the <db>_developer group of a database.
    call spm_grant ('mydb_developer', 'p4_564306222995xxx'); // Add the RAM user whose UID is 564306222995xxx to the mydb_developer group.
    call spm_grant ('mydb_developer', '197006222995xxx'); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the mydb_developer group.
    call spm_grant ('mydb_developer', 'RAM$mainaccount:subuser');// Add the RAM user subuser of the Alibaba Cloud account mainaccount to the mydb_developer group.
    
    // Add a user to the <db>_viewer group of a database.
    call spm_grant ('"MYDB_viewer"', 'p4_564306222995xxx'); // Add the RAM user whose UID is 564306222995xxx to the MYDB_viewer group.
    call spm_grant ('"MYDB_viewer"', '197006222995xxx'); // Add the user whose Alibaba Cloud account ID is 197006222995xxx to the MYDB_viewer group.
    call spm_grant ('mydb_viewer', '"xxx@aliyun.com"'); // Add the user whose Alibaba Mail address is xxx@aliyun.com to the mydb_viewer group.
    ```


## Remove a user from a user group

If you need to remove a user from a user group of a specified database in Hologres, you can use the following method based on the SPM:

-   Execute SQL statements to remove a user from a user group

    You can execute the following statement to remove a user from a user group. For more information about the spm\_revoke function, see [spm\_revoke](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.mdsection_ypq_e9k_v8i).

    ```
    call spm_revoke ('<dbname>_[admin|developer|writer|viewer]', 'Alibaba Cloud account ID/Alibaba Mail address/RAM user UID'); // Remove a user from a user group.
    
    Examples:
    // Remove a user from the <db>_admin group of a database.
    call spm_revoke ('dbname_admin', 'p4_564306222995xxx');// Remove the RAM user whose UID is 564306222995xxx from the dbname_admin group of the specified database.
    call spm_revoke ('dbname_admin', '197006222995xxx');// Remove the user whose Alibaba Cloud account ID is 197006222995xxx from the dbname_admin group of the specified database.
    call spm_revoke ('dbname_admin', '"xxx@aliyun.com"');
    
    // Remove a user from the <db>_developer group of a database.
    call spm_revoke ('mydb_developer', 'RAM$mainaccount:subuser'); // Remove the RAM user subuser of the Alibaba Cloud account mainaccount from the mydb_developer group.
    call spm_revoke ('mydb_developer', 'p4_564306222995xxx');// Remove the RAM user whose UID is 564306222995xxx from the mydb_developer group.
    
    // Remove a user from the <db>_viewer group of a database.
    call spm_revoke ('"MYDB_viewer"', 'p4_564306222995xxx'); // Remove the RAM user whose UID is 564306222995xxx from the MYDB_viewer group.
    ```


## Delete a user from a Hologres instance

You can execute the following statement to delete a user from a Hologres instance:

**Note:** After a user is deleted from a Hologres instance, the user has no permission on the instance. Proceed with caution.

```
DROP ROLE "Alibaba Cloud account ID/Alibaba Mail address/RAM user UID"; // Delete a user from a Hologres instance.
```

## Switch from the standard PostgreSQL authorization model to the SPM for existing objects \(Optional\)

If a database uses the [standard PostgreSQL authorization model](/intl.en-US/User Authorization/Hologres permission models/Standard PostgreSQL authorization model.md) and the database contains objects such as tables, views, and foreign tables, you can enable the SPM for better permission management. To call the spm\_migrate function to transfer the ownership of objects, execute the following statement:

```
call spm_migrate (); // Transfer the ownership of existing objects to the developers that are specified in the SPM.
```

**Note:** When you enable the SPM for a database, make sure that no SQL statement is being executed in the database. Otherwise, you may fail to enable the SPM and the business may be affected.

The spm\_migrate function may transfer the ownership of a large number of objects at a time. However, the number of objects for which you can transfer the ownership at a time by calling the spm\_migrate function is no more than the value of the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter. As a result, you may need to repeat calling the spm\_migrate function until the ownership of all objects is transferred.

## Disable the SPM

1.  Disable the SPM.

    A superuser can execute the following statement to call the spm\_disable function to disable the SPM for a database. For more information about the spm\_disable function, see [spm\_disable](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.mdsection_13d_pfx_0c1).

    ```
    call spm_disable ();
    ```

    The user groups of a database are not deleted if a superuser disables the SPM for the database. For more information about the permissions of the users in the user groups, see [Functions of the SPM](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.md).

2.  Delete the user groups of a database.

    After the SPM is disabled, you can call the spm\_cleanup function to delete the user groups of a database in one of the following cases:

    **Note:** Generally, we recommend that you retain user groups to facilitate user management.

    -   Case 1: Delete the user groups of an existing database

        To delete user groups but retain the database, execute the following statement as a superuser. For more information about the spm\_cleanup function, see [spm\_cleanup](/intl.en-US/User Authorization/Hologres permission models/SPM/Functions of the SPM.md).

        ```
        call spm_cleanup ( '{dbname}' );
        ```

        **Note:** When you call the spm\_cleanup function, make sure that no SQL statement is being executed in the database. Otherwise, the function may fail and the business may be affected.

        This operation may transfer the ownership of a large number of business objects. However, the number of objects for which you can transfer the ownership at a time by calling the spm\_cleanup function is no more than the value of the `max_locks_per_transaction` parameter. As a result, you may need to repeat calling the spm\_cleanup function until the ownership of all objects is transferred and all user groups of the database are deleted.

    -   Case 2: Delete the user groups of a deleted database

        To delete the user groups of a deleted database, execute the following statement in another database such as postgres as a superuser:

        ```
        postgres=# call spm_cleanup ( 'mydb' );
        ```


When you disable the SPM, take note of the following items:

-   Only a superuser can disable the SPM for a database.
-   After the SPM is disabled, the PUBLIC group is granted the USAGE and CREATE permissions on the public schema of the database.
-   After the SPM is disabled, the PUBLIC group is granted the CONNECT and TEMPORARY permissions on the database.
-   After the SPM is disabled, the PUBLIC group is granted the EXECUTE permission on the functions and procedures of the database.
-   After the SPM is disabled, the PUBLIC group is granted the USAGE permission on the languages and data types \(including domains\) of the database.
-   After the SPM is disabled, the PUBLIC group has no permission on the objects other than the preceding ones. For example, the PUBLIC group has no permission on tables, views, materialized views, columns, sequences, foreign data wrappers, foreign servers, and schemas \(excluding the public schema\).
-   After the SPM is disabled, the <db\>\_admin, <db\>\_developer, <db\>\_writer, and <db\>\_viewer groups have the following permissions:
    -   <db\>\_admin: retains the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_developer: retains the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_writer: retains the obtained permissions on existing objects and has no permission on new objects.
    -   <db\>\_viewer: retains the obtained permissions on existing objects and has no permission on new objects.

## Enable the SPM again

Assume that you enabled the SPM for your database before but have switched it to the standard PostgreSQL authorization model for existing objects. If you need to enable the SPM again, execute the following statements:

```
call spm_enable (); // Enable the SPM for the current database.
call spm_migrate (); // Transfer the ownership of existing objects in the database to the developers that are specified in the SPM.
```

**Note:** When you enable the SPM for a database, make sure that no SQL statement is being executed in the database. Otherwise, you may fail to enable the SPM and the business may be affected.

The spm\_migrate function may transfer the ownership of a large number of objects at a time. However, the number of objects for which you can transfer the ownership at a time by calling the spm\_migrate function is no more than the value of the [max\_locks\_per\_transaction](https://www.postgresql.org/docs/9.1/runtime-config-locks.html) parameter. As a result, you may need to repeat calling the spm\_migrate function until the ownership of all objects is transferred.

