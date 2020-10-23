---
keyword: [Alibaba Cloud account authentication, Hologres]
---

# Alibaba Cloud account authentication

Hologres is deeply integrated with the Alibaba Cloud account system. A role must pass the Alibaba Cloud account authentication to connect to a Hologres instance. In Hologres, a database role can be an Alibaba Cloud account such as `abc@aliyun.com`, a RAM user such as `RAM$abc@aliyun.com:subuser1`, or a custom user group such as `developer`. This topic describes the Alibaba Cloud account system that is used in Hologres.

## Display name

Most display names in Hologres are in the following formats:

-   Alibaba Cloud account: `abc@aliyun.com`
-   RAM user: `RAM$abcaliyun.com:subuser1`

The following content describes the display names in Hologres:

-   For an Alibaba Cloud account, the display name can be in the `abc@aliyun.com` or `ALIYUN$abc@aliyun.com` format. The two formats refer to the same Alibaba Cloud account.

    In some cases, the Alibaba Cloud account system allows you to create an Alibaba Cloud account that is not in the format of an email address, such as `user1`. However, the account must be prefixed with `ALIYUN$`, that is, `ALIYUN$user1`.

-   For a RAM user, the display name can be in one of the following formats:
    -   `RAM$Display name of an Alibaba Cloud account:RAM user`. In this format, the display name of an Alibaba Cloud account follows the preceding rule, but does not include the `ALIYUN$` prefix. For example, `RAM$abc@aliyun.com:subuser1` is the display name of a RAM user, where the Alibaba Cloud account is `abc@aliyun.com` and the RAM user is `subuser1`.
    -   `RAM$ID of an Alibaba Cloud account:RAM user`. For example, `RAM$1898137152164646:subuser1` is the display name of a RAM user, where the ID of the Alibaba Cloud account is `1898137152164646` and the RAM user is `subuser1`.

In Hologres, you can use display names of Alibaba Cloud accounts or RAM users to grant related users permissions to perform CREATE, ALTER, and DROP operations on roles or users. For example, you can use the following statement to create a Hologres role with the same name as a RAM user so that the RAM user can be used to connect to Hologres:

```
CREATE USER "RAM$abc@aliyun.com:subuser1";
```

**Note:** :

-   If the display name of an Alibaba Cloud account or a RAM user contains dollar signs \(`$`\) or at signs \(`@`\), the display name must be enclosed in double quotation marks \(" "\).
-   The specified Alibaba Cloud account or RAM user must already exist in the Alibaba Cloud account system. To allow a user to connect to a Hologres instance, you must create an Alibaba Cloud account or RAM user in the Alibaba Cloud account system and then use the `CREATE USER` statement to create a role with the same name in Hologres.
-   Hologres relies on the Alibaba Cloud account system for user authentication. Therefore, you do not need to set a password for a role in Hologres. The authentication is completed in the Alibaba Cloud account system.
-   If you create a role with the LOGIN permission in Hologres, but the role is invalid in the Alibaba Cloud account system, this role cannot connect to a Hologres instance because it fails the authentication by Alibaba Cloud.
-   Other Hologres roles without the LOGIN permission may not be an Alibaba Cloud account.

You can use the following statement in Hologres to view the display name of the current user:

```
SELECT user_display_name(current_user);
```

## Account ID

The ID of an Alibaba Cloud account is a string of digits, for example, `189813715xxxx`. You can view the account ID on the [Security Settings](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure) page of the Alibaba Cloud official website.

![725](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5574032951/p95289.png)

The account ID of a RAM user is the same as the UID of the RAM user. You can view the account ID on the [Users](https://ram.console.aliyun.com/users) page in the RAM console. When you authorize a RAM user by using its UID in Hologres, the UID must be in the `p4_UID` format, such as `p4_12333388xxx`.

![726](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5574032951/p95292.png)

The following examples describe how to authorize users in Hologres by using account IDs:

```
create USER "189813715xxxx"; // Authorize the user whose Alibaba Cloud account ID is 189813715xxxx to connect to Hologres.
create USER "p4_12333388xxx" superuser;// Assign the superuser role to the RAM user whose UID is 12333388xxx.
```

You can run the following statement in Hologres to view your account ID:

```
SELECT current_user;
```

## AccessKey ID and AccessKey secret

The AccessKey ID and AccessKey secret are issued by Alibaba Cloud for you to connect to a Hologres instance. The AccessKey ID is similar to a logon account, and the AccessKey secret is similar to a logon password. You can view the AccessKey ID and AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=a2c8b.12215442.nav-right.dak.18fb6253I8gJRw#/manage/ak) page of the Alibaba Cloud official website.

The AccessKey ID and AccessKey secret are access credentials that are issued by Alibaba Cloud. They have a validity period. If the AccessKey ID and AccessKey secret expire, you can create another AccessKey pair. For more information, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).

The following example describes the connection string that is used to connect to a Hologres instance from the PostgreSQL client:

```
PGUSER=<AccessID> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
```

