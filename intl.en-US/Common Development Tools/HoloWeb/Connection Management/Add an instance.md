---
keyword: [HoloWeb, connect to a Hologres instance, add a Hologres instance]
---

# Add an instance

HoloWeb is a development platform that is used to process data in Hologres instances. This topic describes how to use HoloWeb to add a Hologres instance.

-   An Alibaba Cloud account is created.
-   The real-name verification is complete.
-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).

## Add an instance

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar, select a region from the drop-down list.

    ![Region](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8398778061/p141749.png)

3.  On the **Hologres Instances** page, click **Go to HoloWeb** to go to the HoloWeb console.

4.  On the Metadata Management tab, click **Add Instance**.

5.  In the **Add Instance** dialog box, set the parameters as required and click **OK**.

    ![Add Instance](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273423.png)

    |Parameter|Description|Required|
    |---------|-----------|--------|
    |Network type|    -   **public network**: Supported regions include China \(Shanghai\), China \(Shenzhen\), China \(Beijing\), China \(Hangzhou\), Singapore \(Singapore\), China \(Hong Kong\), Malaysia \(Kuala Lumpur\), and US \(Silicon Valley\).

The ![Public network](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8429393261/p273449.png) icon indicates that the network type of the instance is public network.

    -   **VPC**: You can select only the region from which you log on to the HoloWeb console.

The ![VPC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3229393261/p273444.png) icon indicates that the network type of the instance is VPC. This instance cannot be edited or removed.

|No|
    |Instance name|The Hologres instance that is created by using the current Alibaba Cloud account.|No|
    |Name|If you have set the **Instance name** parameter, the name of the specified instance is automatically entered in the **Name** field. You can also enter a custom connection name.|Yes|
    |Description|The description of the connection.|No|
    |Host|The endpoint of the Hologres instance. You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the endpoint of the specified instance is automatically entered in the **Host** field. You can also enter the endpoint of the Hologres instance.

|Yes|
    |Port|The port number of the Hologres instance. You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

If you have set the **Instance name** parameter, the port number of the specified instance is automatically entered in the **Port** field. You can also enter the port number of the Hologres instance.

|Yes|
    |Logon Method|    -   **Password-free Logon**: You can directly log on to the instance by using the current Alibaba Cloud account, without the need to enter the AccessKey ID and AccessKey secret.
    -   **Password Logon**: You can enter the AccessKey ID and AccessKey secret of the current or another Alibaba Cloud account to log on to the instance.
|Yes|
    |AccessKey ID|This parameter is displayed only when you set the Logon Method parameter to Password Logon.

The AccessKey ID of your Alibaba Cloud account. You can obtain the AccessKey ID from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|No|
    |AccessKey Secret|This parameter is displayed only when you set the Logon Method parameter to Password Logon.

The AccessKey secret of your Alibaba Cloud account. You can obtain the AccessKey secret from the [Security Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page.

|No|
    |Test Connectivity|Tests whether the instance can be connected to.     -   If the instance can be connected to, the message **Test passed** is displayed.
    -   If the instance cannot be connected to, the message **Test failed** is displayed.
|No|
    |Create Instance and Log On|Specifies whether to log on to the instance.     -   **Yes**: logs on to the instance. The connected instance is displayed in the left-side Instances Connected list.
    -   **No**: does not log on to the instance. The disconnected instance is displayed in the left-side Instances Disconnected list.
|Yes|

6.  After the instance is added, click the ![Refresh information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0610148951/p117260.png) icon to the right of **Instance Management** to refresh the instance lists. Click **Instances Connected** to view the information about the connected instance.

    To manage the instance, right-click the instance and select an action. For more information about instance management, see [t2079639.md\#]().


