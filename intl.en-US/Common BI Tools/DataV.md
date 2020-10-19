---
keyword: [DataV, Hologres]
---

# DataV

This topic describes how to connect DataV to Hologres and use DataV to present data.

DataV is a BI service that Alibaba Cloud provides to help you present data. On the graphical interface of the service, you can use templates to create data visualization projects and drag and drop elements to edit the projects. DataV can fulfill your diversified data presentation requirements.

Hologres is deeply integrated with DataV. You can connect DataV to Hologres and then present data in a visualized manner.

1.  Establish a connection to Hologres.

    1.  Log on to the[DataV console](https://account.alibabacloud.com/login/login.htm?oauth_callback=http%3A%2F%2Fdatav-ap-southeast-1.alibabacloud.com%2F1%2Fproject). Click the **Data Sources** tab and click **Add Source**.

        ![834](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1205013061/p100341.png)

    2.  In the **Add Data Source** dialog box, configure the parameters listed in the following table and click **OK**.

        ![40](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1205013061/p113812.png)

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |Type|The type of the data source. Select **Hologres**.|None.|
        |Name|The display name of the data source.|None.|
        |Domain Name|The public or internal endpoint of the Hologres instance.|You can view the endpoint of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Username|The AccessKey ID of your Alibaba Cloud account.|You can obtain the AccessKey ID on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |Password|The AccessKey secret of your Alibaba Cloud account.|You can obtain the AccessKey secret on the [Security Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak).|
        |Port|The public port number of the Hologres instance.|You can view the port number of the Hologres instance on the **Configurations** tab of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance).|
        |Database|The name of the database.|None.|

2.  Present data in a visualized manner.

    After DataV is connected to Hologres, you can create a project based on an existing template or a blank template to present data in a visualized manner. For more information, see [Use a template to create a project](/intl.en-US/Quick Start/Create a project (template)/Create a project.md) \(based on an existing template\) or [Create a project](/intl.en-US/Quick Start/Create a project (blank canvas)/Create a project.md) \(based on a blank template\).


