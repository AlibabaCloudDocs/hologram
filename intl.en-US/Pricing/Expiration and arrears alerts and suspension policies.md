# Expiration and arrears alerts and suspension policies

This topic describes expiration and arrears alerts and suspension policies of a Hologres instance.

A Hologres instance enters the suspension state after it expires or falls into arrears. The instance can be unlocked after you pay the arrears or renew the instance.

## Expiration and arrears alerts

|Billing method|Alert type|Instance status|Proposed operation|Remarks|
|--------------|----------|---------------|------------------|-------|
|**Subscription**|**Instance expiration**|The instance stops providing services immediately after it expires and cannot be accessed.|Purchase the instance based on the subscription billing method again.|You will be notified by SMS or email at the points in time below to renew your instance as soon as possible to avoid service suspension:-   Seven days before downtime
-   Three days before downtime
-   One day before downtime |
|From the first day to the fourteenth day after the instance expires, the instance is locked and cannot be accessed. During this period, Alibaba Cloud does not push a bill to you.|Purchase the instance based on the subscription billing method again.|You will be notified by SMS or email at the points in time below to renew your instance as soon as possible to avoid instance release: -   Seven days before release
-   Three days before release
-   One day before release |
|On the fifteenth day after the instance expires, the instance is released and deleted from the console. Data in the instance is also deleted and cannot be recovered.|The instance cannot be restored.|
|**Arrears**|The instance runs as expected within 24 hours after balance overdue.|Pay the arrears as soon as possible to avoid service suspension.|You will be notified by SMS or email at the points in time below to pay the arrears as soon as possible to avoid instance lockup:-   18 hours before service suspension
-   12 hours before service suspension
-   6 hours before service suspension |
|After 24 hours of balance overdue, the instance stops providing services and cannot be accessed.|
|From the second day to the instance expiration date, the instance is locked and cannot be accessed.|
|**Pay-as-you-go**|**Arrears**|The instance runs as expected if the overdue payment is less than USD 1,000.|Pay the arrears as soon as possible to avoid service suspension.|If you have turned on [Available Credit Alert](https://usercenter2.aliyun.com/home?spm=a2c4g.11186623.2.9.2da032ad2zyEqW), the system sends you an SMS or email notification when your account balance is less than the threshold you set.|
|If the overdue payment in your account reaches USD 1,000 and the deduction fails, the instance runs as expected within 14 days.|You will be notified by SMS or email at the points in time below to pay the arrears as soon as possible to avoid instance lockup:-   Seven days before service suspension
-   Three days before service suspension
-   One days before service suspension |
|On the fifteenth day after the instance stops providing services due to balance overdue, the instance is locked and cannot be accessed. During this period, Alibaba Cloud does not push a bill to you.|The instance is immediately unlocked after you pay the arrears.|You will be notified by SMS or email at the points in time below to pay the arrears as soon as possibleto avoid instance lockup:-   Seven days before service suspension
-   Three days before service suspension
-   One days before service suspension |
|On the fifteenth day after the instance stops providing services due to balance overdue, the instance is released and deleted from the console. Data in the instance is also deleted and cannot be recovered.|The data cannot be restored.|You will be notified by SMS or email at the points in time below to pay the arrears as soon as possible to avoid instance release:-   Seven days before release
-   Three days before release
-   One day before release |

## Configure the notification methods of alerts

When your Alibaba Cloud account has an overdue payment or a Hologres instance expires, Alibaba Cloud sends an alert notification by SMS or email. By default, the system sends the notification to the mobile phone number and email address that are bound to your Alibaba Cloud account. To have these notifications sent to a Resource Access Management \(RAM\) user, perform the following steps on the Message Center page within your Alibaba Cloud account. To configure the notification methods of alerts, perform the following steps:

1.  Log on to the Alibaba Cloud International site \(alibabacloud.com\) with your Alibaba Cloud account. Move the pointer over the message icon in the upper-right corner and click **Message Settings**.

    ![628](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5658390161/p77376.png)

2.  On the Message Center page, click **Common Settings** in the left-side navigation pane, select **Product Overdue Payment, Suspension, and Imminent Release Notifications**, and then click **Modify**.

    ![619](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5658390161/p77377.png)

3.  In the Modify Contact dialog box, click **Add Receiver**, enter required information to set a new account as a message receiver, and then click **Save**.

    ![630](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5658390161/p77379.png)


