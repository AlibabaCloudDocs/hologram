---
keyword: [HoloWeb, connect to a Hologres instance]
---

# Manage a connection

HoloWeb is a development platform that is used to process data in Hologres instances. This topic describes how to use HoloWeb to create, edit, and delete a connection to a Hologres instance. This topic also describes how to manage users for a connection on the User Management tab of the HoloWeb console.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Create a connection

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Log on to Hologres Database** to go to the HoloWeb console.

4.  Choose **Connection Management** \> **Data Connection**.

    You can also right-click **My connection** on the **Connection Management** page and select **New Connection**.

5.  In the **New Connection** dialog box, set the parameters as required and click **OK**.

    ![New Connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2560409951/p116502.png)

    The following table describes the parameters.

    |Parameter|Description|Required|
    |---------|-----------|--------|
    |Network type|    -   **public network**: Supported regions include China \(Shanghai\), China \(Shenzhen\), China \(Beijing\), China \(Hangzhou\), Singapore, China \(Hong Kong\), Malaysia \(Kuala Lumpur\), and US \(Silicon Valley\).
    -   **VPC**: You can select only the region from which you log on to the HoloWeb console.
|No|
    |Instance name|The Hologres instance that is created by using the current account.|No|
    |Connection name|If you have set the **Instance name** parameter, the value of the **Connection name** parameter is automatically set to the name of the specified instance. You can also enter a custom connection name.|Yes|
    |Connection description|The description of the connection.|No|
    |Host|The endpoint of the Hologres instance.You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the value of the **Host** parameter is automatically set to the endpoint of the specified instance. You can also enter the endpoint of the Hologres instance.

|Yes|
    |Port|The port number of the Hologres instance.You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the value of the **Port** parameter is automatically set to the port number of the specified instance. You can also enter the port number of the Hologres instance.

|Yes|
    |Logon Method|    -   **Password-free logon**: You can directly connect to the instance by using the current Alibaba Cloud account, without the need to enter the AccessKey ID and AccessKey secret.
    -   **Password logon**: You can enter the AccessKey ID and AccessKey secret of the current or other Alibaba Cloud account to connect to the instance.
|Yes|
    |AccessKey ID|The AccessKey ID of the current Alibaba Cloud account.You can obtain the AccessKey ID on the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

If you set the Logon mode parameter to Password logon, you must set this parameter.

|No|
    |AccessKey Secret|The AccessKey secret of the current Alibaba Cloud account.You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).

If you set the Logon mode parameter to Password logon, you must set this parameter.

|No|
    |Test connectivity|Tests whether the instance can be connected to.     -   If the instance can be connected to, the message **Test passed** is displayed.
    -   If the instance cannot be connected to, the message **Test failed** is displayed.
|No|

6.  After the connection is created, click the ![Refresh](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610148951/p117260.png) icon on the **Connection Management** page to refresh the connection list.

7.  Click **My connection** to view the connection that you create.


## Manage users on the User Management tab

After a connection is created, the **User Management** module is displayed under the connection.

![User Management](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1416412161/p187322.png)

To manage users for a connection, double-click **User Management** under the connection to go to the **User Management** tab. The following operations are supported:

-   Delete a user on this tab, as shown in the following figure.

    ![Delete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7764762061/p141200.png)

-   Click **User Management** on the User Management tab. In the **Add new user** dialog box, select the user that you want to add and assign the superuser or normal user role to the user.

    ![Edit](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7764762061/p141063.png)


## Edit a connection

1.  Choose **Connection Management** \> **My connection**. All the configured connections are displayed.

2.  Right-click the required connection and select **Edit connection**.

3.  Edit the connection information based on your business requirements.

    ![Edit connection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1610148951/p131992.png)


## Delete a connection

1.  Choose **Connection Management** \> **My connection**. All the configured connections are displayed.

2.  Right-click the required connection and select **Delete Connection**.


