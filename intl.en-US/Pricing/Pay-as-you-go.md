---
keyword: [pay-as-you-go, change of billing methods]
---

# Pay-as-you-go

This topic describes the pay-as-you-go billing method for Hologres.

## Lifecycle management

In pay-as-you-go mode, Alibaba Cloud charges for computing resources based on the specifications and running duration of instances and storage resources based on the volume and duration of stored data. The payments are settled on an hourly basis. The following figure shows you how to manage a Hologres instance throughout its lifecycle in pay-as-you-go mode.

![Lifecycle management in pay-as-you-go mode at the Alibaba Cloud International site (alibabacloud.com)](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6809390161/p224844.png)

Sample scenario:

Assume that you purchase a Hologres instance based on the pay-as-you-go billing method at the Alibaba Cloud International site \(alibabacloud.com\). You can use this instance immediately after successful activation. The system pushes a bill to your Alibaba Cloud account that is used to purchase this instance on an hourly basis and automatically deducts the amount payable from your account balance. If your account enters the overdue state during the use of the instance, you can still use the instance. During this period, the system does not push a bill to you. The system pushes a bill and deducts the amount when the overdue payment reaches USD 1,000. If the bill is not paid within 14 days after the deduction fails, the instance is suspended and cannot be used. Make sure that your account has a sufficient balance. Otherwise, the instance may stop running due to overdue payments. If you do not renew your instance within 14 days, the instance is released and deleted from the console. Data in the instance is also deleted and cannot be recovered.

In addition, you can manually suspend your instance in pay-as-you-go mode. After you click Stop, the system stops charging for computing resources. However, data stored in your instance is still charged on an hourly basis, even if you do not use Hologres for data analytics. You can also manually restore the instance. After that, the system automatically calculates the fees for computing and storage resources. You can delete the instance if your business no longer requires it. After that, the system calculates the fees and pushes a bill to you. The system automatically releases the instance after you delete it. Data in the instance is also deleted and cannot be recovered.

**Note:** If you purchase a Hologres instance based on the pay-as-you-go billing method, you can change the billing method to subscription. If you purchase a Hologres instance based on the subscription billing method, you cannot change the billing method.

## Fee calculation

The following table describes how the Hologres instance fee is calculated in pay-as-you-go mode.

|Billing method|Billable item|Unit price|Description|
|--------------|-------------|----------|-----------|
|Pay-as-you-go|Computing|The fee of computing resources varies based on different countries and regions.-   Singapore: USD 0.066604 per CU per hour
-   Hong Kong \(China\): USD 0.062904 per CU per hour
-   US: USD 0.061318 per CU per hour
-   Kuala Lumpur: USD 0.073476 per CU per hour
-   Indonesia: USD 0.082590 per CU per hour
-   India: USD 0.072340 per CU per hour

|The fee is calculated on an hourly basis. One CU represents 1 CPU core and 4 GB memory.|
|Storage|The fee of storage resources varies based on different countries and regions.-   Singapore: USD 0.000379 per GB per hour
-   Hong Kong \(China\): USD 0.000364 per GB per hour
-   US: USD 0.000358 per GB per hour
-   Kuala Lumpur: USD 0.000410 per GB per hour
-   Indonesia: USD 0.000467 per GB per hour
-   India: USD 0.000422 per GB per hour

|The fee is calculated on an hourly basis.|

Assume that you purchase an instance with 64 CUs of computing resources and 256 GB storage resources based on the pay-as-you-go billing method in the Singapore region. If you want to store 100 GB data in this instance for 1 hour, the total fee in this hour is USD 4.300556, which is calculated based on the following formula: USD 0.066604/CU/hour × 64 CUs × 1 hour + USD 0.000379/GB/hour × 100 GB × 1 hour = USD 4.300556.

## Message notification mechanism

The following table describes the message notification mechanism of Hologres instances in pay-as-you-go mode.

|Billing method|Reminder time for service suspension due to failed deduction of an overdue payment of USD 1,000|Downtime|Release reminder time|
|--------------|-----------------------------------------------------------------------------------------------|--------|---------------------|
|Pay-as-you-go|Seven days before downtime|The instance stops providing services on the fifteenth day after fee deduction fails.|Seven days before release|
|Three days before downtime|Three days before release|
|One day before downtime|One day before release|

Example: Assume that you purchase a Hologres instance based on the pay-as-you-go billing method at the Alibaba Cloud International site \(alibabacloud.com\). The system calculates the fees for computing and storage resources on an hourly basis and deducts the fees from your account balance after a bill is pushed to you. If your account has an overdue payment that reaches USD 1,000 and the system fails to deduct the fee from your account balance, the system sends service suspension notifications by SMS or email seven days, three days, and one day before the instance stops providing services. If you do not pay off your overdue service fee, the instance stops providing services and cannot be used on the fifteenth day. If you do not pay the arrears within 14 days after the instance enters the suspension state, the system automatically releases the instance and sends release notifications by SMS or email seven days, three days, and one day before the release.

