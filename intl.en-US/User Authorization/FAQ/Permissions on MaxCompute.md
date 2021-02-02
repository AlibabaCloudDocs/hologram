---
keyword: [Hologres, errors returned in queries]
---

# Permissions on MaxCompute

When you accelerate queries on data in MaxCompute tables in Hologres, an error may occur if you are not granted relevant permissions. This topic provides answers to commonly asked questions about permissions on MaxCompute.

## FAQ

To view the FAQ about permissions on MaxCompute, click the following links:

-   [Why is the error message "You have NO privilege 'odps:Select' on xxx" returned when I query data in a MaxCompute table?](#section_o30_kyw_u8i)
-   [Why is the error message "The sensitive label of column 'xxx' is 2, but your effective label is 0" returned when I query data in a MaxCompute table?](#section_ka3_d41_yny)
-   [Why is the error message "You have NO privilege 'odps:Select' on xxx" returned when I select a MaxCompute table across MaxCompute projects?](#section_6oq_tpc_ho1)
-   [Why is the error message "You have NO privilege 'odps:List' on xxx" returned when I create a foreign table?](#section_ia3_abx_y9w)
-   [Why is the error message "Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx" returned when I create a foreign table?](#section_gd5_o47_zek)

## Why is the error message "You have NO privilege 'odps:Select' on xxx" returned when I query data in a MaxCompute table?

-   Problem description

    After I created a foreign table in the [Hologres console](https://hologram.console.aliyun.com/#/instance), I could not query data in the source MaxCompute table. The following error message is returned: You have NO privilege 'odps:Select' on xxx.

-   Cause

    The current RAM user is not granted the SELECT permission on data in the MaxCompute table.

-   Solution

    Contact the MaxCompute administrator to grant the current RAM user the SELECT permission on data in the MaxCompute table. For more information, see [Authorize users](/intl.en-US/Management/Configure security features/Manage users and permissions/Authorize users.md).


## Why is the error message "The sensitive label of column 'xxx' is 2, but your effective label is 0" returned when I query data in a MaxCompute table?

-   Problem description

    After I created a foreign table in the [Hologres console](https://hologram.console.aliyun.com/#/instance), I could not query data in the source MaxCompute table. The following error message is returned: The sensitive label of column 'xxx' is 2, but your effective label is 0.

-   Cause

    The current RAM user is granted the permissions to query only part of fields in the MaxCompute table.

-   Solution

    To resolve this issue, you can select one of the following three methods:

    -   Recommended. [Submit a ticket](https://workorder-intl.console.aliyun.com/) to update the version of the current instance to version 0.8.
    -   Add the following statements before the existing query statements:

        ```
        set hg_experimental_enable_odps_executor=on; 
        set hg_experimental_enable_query_master=on;
        ```

    -   Obtain the permissions to query all fields in the MaxCompute table. For more information, see [Authorize users](/intl.en-US/Management/Configure security features/Manage users and permissions/Authorize users.md).

## Why is the error message "You have NO privilege 'odps:Select' on xxx" returned when I select a MaxCompute table across MaxCompute projects?

-   Problem description

    The RAM user that I used is granted the permissions to query data in a MaxCompute table in Project 2. However, when I selected the MaxCompute table to query data in Project 1, an error occurred. The following error message is returned: You have NO privilege 'odps:Select' on xxx.

-   Cause

    The current RAM user is granted the permissions to query data in existing MaxCompute tables in Project 2. However, the error message is still returned when the RAM user queries data in Project 1 and selects a MaxCompute table that belongs to Project 2. This is because the RAM user is granted the permissions on Project 2 based on package-based resource sharing across projects. You can add SQL statements to resolve this issue.

-   Solution

    If the current RAM user is granted the permissions on Project 2 based on package-based resource sharing across projects, you can add the following statement before the existing SQL statements in Hologres to resolve the preceding issue:

    ```
    // Execute the following statement for the instance of version 0.7.
    set seahawks.seahawks_internal_current_odps_project='holoprojectname';
    // Execute the following statement for the instance of version 0.8.
    set hg_experimental_odps_current_project_name = 'holoprojectname';
    ```


## Why is the error message "You have NO privilege 'odps:List' on xxx" returned when I create a foreign table?

-   Problem description

    When I used HoloWeb or HoloStudio to create a foreign table in a visualized manner in the [Hologres console](https://hologram.console.aliyun.com/#/instance), an error occurred. The following error message is returned: You have NO privilege 'odps:List' on xxx.

-   Cause

    The current RAM user is not granted the LIST permission on a MaxCompute project.

-   Solution
    -   Contact the MaxCompute administrator to grant the current RAM user the LIST permission on the MaxCompute project. For more information, see [Authorize users](/intl.en-US/Management/Configure security features/Manage users and permissions/Authorize users.md).
    -   Execute SQL statements to create a foreign table to query data. For more information, see [Create a foreign table to accelerate queries of MaxCompute data](/intl.en-US/Data Access/Big Data/MaxCompute/Create a foreign table to accelerate queries of MaxCompute data.md).

## Why is the error message "Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx" returned when I create a foreign table?

-   Problem description

    When I used HoloWeb to create a foreign table in the [Hologres console](https://hologram.console.aliyun.com/#/instance), an error occurred. The following error message is returned: Access denied by project ip white list: sourceIP:'xxxx' is not in white list. project: xxxx.

-   Cause

    A whitelist is configured for the current MaxCompute cluster. However, when you create a foreign table, HoloWeb is not included in the whitelist.

-   Solution

    After a whitelist is configured, only the IP addresses in the whitelist have access to MaxCompute projects. If you use an IP address that is not in the whitelist, your access request is denied even if you have the valid AccessKey pair. In this case, you can create a foreign table only after you add the IP address in the error message into the whitelist. For more information, see Configure an IP address whitelist.


