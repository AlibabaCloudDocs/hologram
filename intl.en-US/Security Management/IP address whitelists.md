---
keyword: [hologres, ip address whitelist, holoweb]
---

# IP address whitelists

Hologres allows you to manage IP address whitelists for your databases to control the access permissions on the databases. This ensures secure and stable operations in Hologres. This topic describes how to manage an IP address whitelist in the HoloWeb console.

## Notes

Take note of the following rules when you manage an IP address whitelist in the HoloWeb console:

-   Only Hologres V0.9 and later support the IP address whitelist feature. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   After you purchase an instance, if you do not configure an IP address whitelist, the databases in the instance can be accessed by using all types of networks. For more information about how to purchase an instance, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   To manage an IP address whitelist, you must be the superuser of your Hologres instance.
-   To manage an IP address whitelist in the HoloWeb console, you must set the Logon Method parameter to Password-free Logon when you create a connection to the destination instance. For more information about how to create a connection to a Hologres instance, see Step 4 in [HoloWeb quick start](/intl.en-US/Quick Start/HoloWeb quick start.md).

    ![Password-free Logon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0457494161/p232918.png)

-   You can separately manage the access permissions on the Hologres console, HoloStudio service, and HoloWeb console. To manage such permissions, select a preset group and specify the databases and users to be authorized, as instructed in [Create an IP address whitelist](#section_hsq_vod_xpd).

    |Group|Description|
    |-----|-----------|
    |HologresConsoleGroup|Users specified for this group have the permissions to access the Hologres console.|
    |HoloStudioGroup|Users specified for this group have the permissions to access the HoloStudio service.|
    |HoloWebGroup\_cn-shanghai|Users specified for this group have the permissions to access the HoloWeb console.|


## Create an IP address whitelist

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the top navigation bar, select a region.

2.  In the left-side navigation pane, click Instances. On the **Hologres Instances** page, click **Log on to Hologres Database** to go to the **HoloWeb console**.

3.  In the top navigation bar of the HoloWeb console, click **System Management**. In the left-side navigation pane of the System Management tab, click **IP Address Whitelist**.

4.  In the upper-right corner, click **Add IP Address to Whitelist**. In the dialog box that appears, set the parameters as required.

    ![Add IP Address to Whitelist](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0457494161/p229410.png)

    |Parameter|Description|
    |---------|-----------|
    |Group|The group name for the IP address whitelist.If you set the Logon Method parameter to Password-free Logon when you create the current connection, you must add users to the HoloStudioGroup, HologresConsoleGroup, and HoloWebGroup preset groups. Otherwise, features in the consoles and the service are unavailable to the users. To add a user to a preset group, select the group from the Group drop-down list and the user from the User Limits drop-down list. |
    |Accessible Databases|The databases that can be accessed by the specified users from the specified IP addresses. Select databases from the Accessible Databases drop-down list. To allow access to all the databases in the current Hologres instance, select ALL.|
    |User Limits|The users that can access the specified databases from the specified IP addresses. Select users from the User Limits drop-down list. To allow access by all the users of the current Hologres instance, select ALL.|
    |IP Address|The IP addresses from which the specified users can access the specified databases. Take note of the following rules:    -   To specify all the IP addresses, enter ALL.
    -   You can specify a specific IP address. For example, you can enter 192.168.0.1, which allows the specified users to access the specified databases from 192.168.0.1.
    -   You can specify a specific classless inter-domain routing \(CIDR\) block. For example, you can enter 192.168.0.0/24, which allows the specified users to access the specified databases from an IP address within the range of 192.168.0.1 to 192.168.0.255.
    -   To specify multiple IP addresses, start a new line for each IP address. |

5.  Click **OK**. After you create an IP address whitelist, you allow the specified users to perform operations on the specified databases from the specified IP addresses.


## Modify an IP address whitelist

After an IP address whitelist is created, you can modify only the IP addresses in the whitelist. To modify the authorized users and databases, you must create another IP address whitelist.

**Note:** To modify an IP address whitelist, you must be the superuser of your Hologres instance.

1.  In the top navigation bar of the HoloWeb console, click **System Management**. In the left-side navigation pane of the System Management tab, click **IP Address Whitelist**.

2.  On the IP Address Whitelist page, find the whitelist that you want to modify and click **Edit**.

3.  In the Edit IP Address in Whitelist dialog box, modify the IP addresses. For more information about how to specify IP addresses for an whitelist, see [Create an IP address whitelist](#section_hsq_vod_xpd).

4.  Click **OK**.


## Delete an IP address whitelist

If you no longer need an IP address whitelist that you created, you can delete the whitelist. After you delete all the IP address whitelists for a connection, no whitelist is available for the relevant instance and databases.

**Note:** To delete an IP address whitelist, you must be the superuser of your Hologres instance.

1.  In the top navigation bar of the HoloWeb console, click **System Management**. In the left-side navigation pane of the System Management tab, click **IP Address Whitelist**.

2.  On the IP Address Whitelist page, find the whitelist that you want to delete and click **Delete**.

3.  In the message that appears, click **OK**.


