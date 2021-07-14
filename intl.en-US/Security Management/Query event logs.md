# Query event logs

Hologres allows you to query the instance-related event logs that were generated within the last 90 days by using the ActionTrail console, calling the ActionTrail API, or using developer tools. You can also create a trail to deliver event logs to a specified Log Service Logstore or OSS bucket. This way, you can monitor events, set alert rules for events, audit events in real time, and troubleshoot issues. This topic describes how to query event logs in the ActionTrail console.

## Usage notes

-   After you perform an operation on a Hologres instance, you must wait about 5 to 10 minutes before the corresponding event log is generated.
-   If you use ActionTrail to query event logs related to Hologres instances, you can query only the event logs that were generated within the last 90 days.
-   If you need to handle anomalous activities in real time, you can set alert rules for trails to monitor important events. For more information, see [Use ActionTrail to monitor the use of an Alibaba Cloud account](/intl.en-US/Tutorials/Use ActionTrail to monitor the use of an Alibaba Cloud account.md).
-   If the ActionTrail console is unavailable, you can call the ActionTrail API or use developer tools to query event logs related to Hologres instances. For more information, see [API overview](/intl.en-US/API Reference/API overview.md).

1.  Log on to the [ActionTrail console](https://actiontrail.console.aliyun.com).

2.  In the left-side navigation pane, click **Event Detail Query**.

3.  In the top navigation bar, select the region where the event that you want to query occurred from the drop-down list.

4.  On the **Event Detail Query** page, select **Service Name** from the drop-down list and enter **Hologram** in the field on the right.

    ![Service Name](../images/p286093.png)

5.  Click the ![Search icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0875616261/p286067.png) icon. Then, you can view specific events below the search box. The following table describes the meanings of different event names.

    ![Event information](../images/p286101.png)

    |Event name|Operation|
    |----------|---------|
    |**create**|Create an instance.|
    |**Modify**|Upgrade or downgrade the specifications of an instance.|
    |**StopInstance**|Suspend an instance.|
    |**ResumeInstance**|Resume an instance.|
    |**DeleteInstance**|Delete an instance.|
    |**ModifyInstance**|Rename an instance.|
    |**modifyInstanceNetworkType**|Change network configurations. For example, you can enable or disable the public endpoint or VPC endpoint of an instance.|
    |**ListInstances**|View instances.|
    |**DescribeInstance**|View the details of an instance.|
    |**GetInstanceMetrics**|View monitoring metrics.|

6.  To view an event log, click the plus icon \(+\) to the left of the event that you want to query, and then click **Event Detail**.

    **Note:** For more information about the fields in the event log, see [ActionTrail event log reference](/intl.en-US/ActionTrail event log reference/ActionTrail event log reference.md).


