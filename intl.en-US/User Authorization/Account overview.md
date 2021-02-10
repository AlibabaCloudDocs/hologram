---
keyword: [Alibaba Cloud account authentication, Hologres, account]
---

# Account overview

Hologres is deeply integrated with the Alibaba Cloud account system. Hologres users are authenticated by using Alibaba Cloud accounts. This topic describes the Alibaba Cloud account system that is used in Hologres.

## Account management

Hologres is deeply integrated with the Alibaba Cloud account system. Hologres accounts include Alibaba Cloud accounts and RAM users, as described in the following table. When you grant permissions on a Hologres instance to Alibaba Cloud accounts and RAM users, you must use their logon accounts or account IDs. For more information, see [Logon account](#section_li7_tj9_7di) and [Account ID](#section_o0a_mg3_w2p).

|Account type|Description|
|------------|-----------|
|Alibaba Cloud account|An Alibaba Cloud account is used to create and manage Hologres instances. For example, you can use your Alibaba Cloud account to log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), create a database, change the billing method of an instance from pay-as-you-go to subscription, and grant permissions on objects to other users.|
|RAM user|After you use your Alibaba Cloud account to create a RAM user and grant the permissions to the RAM user, the RAM user can also create and manage Hologres instances. For example, the RAM user can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), create a database, change the billing method of an instance from pay-as-you-go to subscription, and grant permissions on objects to other users.RAM users are subordinate to Alibaba Cloud accounts and do not possess actual resources. All resources belong only to Alibaba Cloud accounts. |

## Logon account

When you grant permissions to an Alibaba Cloud account or a RAM user, you must use the logon account of the Alibaba Cloud account. The logon account can be obtained from the [user information page](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure).

![Obtain the logon account](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2215682161/p210821.png)

-   When you grant permissions to an Alibaba Cloud account, the full expression of the Alibaba Cloud account must contain the logon account, as described in the following table.

    |Account format|Description|Example|
    |--------------|-----------|-------|
    |ALIYUN$<Login Account\>@aliyun.com|<Login Account\>: the logon account of the Alibaba Cloud account.|ALIYUN$company@aliyun.com|
    |<Login Account\>@aliyun.com|company@aliyun.com|

-   When you grant permissions to a RAM user, the full expression of the RAM user must contain the logon account of the Alibaba Cloud account to which the RAM user belongs, as described in the following table.

    |Account format|Description|Description|
    |--------------|-----------|-----------|
    |<subUserName\>@<Login Account\>.onaliyun.com|The account formats contain the following parameters:    -   <subUserName\>: the name of the RAM user.
    -   <Login Account\>: the logon account of the Alibaba Cloud account.
    -   <AccountID\>: the account ID of the Alibaba Cloud account.
|holouser@company.onaliyun.com|
    |<subUserName\>@<Login Account\>|holouser@company|
    |<subUserName\>@<Account ID\>.onaliyun.com|holouser@123456789xxxx|
    |RAM$<subUserName\>|RAM$holo\_test|
    |RAM$<Login Account\>:<subUserName\>|RAM$company:holouser|
    |RAM$<Account ID\>:<subUserName\>|RAM$123456789xxxx:holouser|
    |<subUserName\>@<Account ID\>|holouser@123456789xxxx|


## Account ID

An Account ID is a string of digits, such as `189813715xxxx`. The account ID can be obtained from the [user information page](https://account.console.aliyun.com/?spm=5176.cngpdb.amxosvpfn.21.4ad17cacTR7tmU#/secure).

![725](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0880522161/p95289.png)

The Account ID of a RAM user is the UID. The UID can be obtained from the [Users page](https://ram.console.aliyun.com/users) in the RAM console. When you grant permissions to a RAM user by using its UID in Hologres, the UID must be in the `p4_UID` format, such as `p4_12333388xxx`.

![726](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0880522161/p95292.png)

The following sample statements show how to grant permissions to users in Hologres by using account IDs:

```
create USER "189813715xxxx"; // Authorize the user whose Alibaba Cloud account ID is 189813715xxxx to connect to Hologres.
create USER "p4_12333388xxx" superuser;// Assign the superuser role to the RAM user whose UID is 12333388xxx.
```

You can execute the following statement in Hologres to view your account ID:

```
SELECT current_user;
```

## AccessKey ID and AccessKey secret

The AccessKey ID and AccessKey secret are issued by Alibaba Cloud for you to connect to a Hologres instance. The AccessKey ID is similar to a logon account, and the AccessKey secret is similar to a logon password. You can view the AccessKey ID and AccessKey secret on the [Security Management](https://usercenter.console.aliyun.com/?spm=a2c8b.12215442.nav-right.dak.18fb6253I8gJRw#/manage/ak) page of the Alibaba Cloud official website.

The AccessKey ID and AccessKey secret are access credentials that are issued by Alibaba Cloud. They have a validity period. If the AccessKey ID and AccessKey secret expire, you can create another AccessKey pair. For more information, see [Create an Alibaba Cloud account](/intl.en-US/Preparations/Create an Alibaba Cloud account.md).

When you use tools such as the PostgreSQL client or a JDBC client to connect to a Hologres instance, you must enter your AccessKey ID and AccessKey secret as the username and password.

## Role

Each Alibaba Cloud account maps a role. An account can access an instance only after the account is assigned a role in the instance. The following table describes the common types of roles in a Hologres instance.

|Role|Description|
|----|-----------|
|Superuser|By default, the system sets the Alibaba Cloud account used to purchase a Hologres instance as a superuser that has full permissions on the instance. The superuser can create and delete databases, create roles, and grant permissions to roles.|
|Normal user|Users except superusers are all normal user. A normal user can access a Hologres instance and analyze data only after a superuser grants the permissions to the normal user. A normal user can also be assigned the superuser role to obtain full permissions on a Hologres instance.|
|User group|You can add zero or multiple users to a user group to facilitate user management. A user group represent a role, for example, the administrator role and developer role. Users in a user group have the same permissions. For more information about how to set a user group and grant permissions to the user group, see [PostgreSQL database roles](https://www.postgresql.org/docs/11/user-manag.html).|

