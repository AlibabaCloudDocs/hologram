---
keyword: [billing method, subscription, pay-as-you-go, change of billing methods]
---

# Billing methods

This topic describes the billing methods of Hologres and how to estimate costs when the billing methods are used.

You scan the QR code and join the DingTalk group to get more information about Hologres.

![QR code of DingTalk group for users at the Alibaba Cloud International site (alibabacloud.com)](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6489390161/p132593.png)

## Terms

-   storage space

    Storage space refers to the volume of data without data replica that can be stored in a Hologres instance, including your business data, metadata, and intermediate results that are processed by Hologres. In most cases, this volume is greater than 50% of the volume of your business data. Check your bill for the actual volume. The storage space is charged in GB.

-   computing capacity

    Computing capacity refers to the total amount of computing resources that are required for operations such as data write and query in Hologres. The computing capacity is charged in CU, where 1 CU represents 1 CPU core and 4 GB memory.


## Billing methods

Hologres supports the subscription and pay-as-you-go billing methods, as shown in the following figure.

![Billing methods](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3359390161/p212954.png)

-   **Subscription**

    Alibaba Cloud charges for the computing and storage resources that are estimated for your business needs. **You can pay monthly or yearly fees in advance.** For more information, see [Subscription](/intl.en-US/Pricing/Subscription.md).

-   **Pay-as-you-go**

    Alibaba Cloud charges for computing resources based on the specifications and running duration of instances and storage resources based on the volume and duration of stored data. **The payments are settled on an hourly basis.** For more information, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

    **Note:** Even if no task is run in a Hologres instance, the payments for computing resources are also settled on an hourly basis.


## Calculate fees

The following table describes how to calculate fees of Hologres instances in both subscription and pay-as-you-go modes.

|Billing method|Billable item|Unit price|Billing rule|How to stop billing|
|--------------|-------------|----------|------------|-------------------|
|Subscription|Computing|The fee of computing resources varies based on different countries and regions.-   Singapore: USD 31.970149 per CU per month
-   Hong Kong \(China\): USD 30.194030 per CU per month
-   US: USD 29.432836 per CU per month
-   Kuala Lumpur: USD 35.268657 per CU per month
-   Indonesia: USD 39.642985 per CU per month
-   India: USD 34.723134 per CU per month

One CU represents 1 CPU core and 4 GB memory.|The fee is prepaid. When you purchase a Hologres instance, you are charged for the computing capacity and subscription duration that you select.|To stop billing for an instance, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) to release the instance.**Note:** After an instance is released, data and objects in the instance cannot be recovered. Exercise caution when you perform the operation. |
|Storage|The fee of storage resources varies based on different countries and regions.-   Singapore: USD 0.182090 per GB per month
-   Hong Kong \(China\): USD 0.174627 per GB per month
-   US: USD 0.171642 per GB per month
-   Kuala Lumpur: USD 0.197015 per GB per month
-   Indonesia: USD 0.223970 per GB per month
-   India: USD 0.202567 per GB per month

|The fee is prepaid. When you purchase a Hologres instance, you are charged for the storage space and subscription duration that you select. If the volume of data that is stored in this instance exceeds the purchased storage space, you are charged for excess storage resources in pay-as-you-go mode.|To stop billing for an instance, you can [submit a ticket](https://workorder-intl.console.aliyun.com/) to release the instance.**Note:** After an instance is released, data and objects in the instance cannot be recovered. Exercise caution when you perform the operation.

You can also delete the data that exceeds the purchased storage space. Then, the system stops billing for excess storage resources that are used.|
|Pay-as-you-go|Computing|The fee of computing resources varies based on different countries and regions.-   Singapore: USD 0.066604 per CU per hour
-   Hong Kong \(China\): USD 0.062904 per CU per hour
-   US: USD 0.061318 per CU per hour
-   Kuala Lumpur: USD 0.073476 per CU per hour
-   Indonesia: USD 0.082590 per CU per hour
-   India: USD 0.072340 per CU per hour

|You are charged for the computing resources immediately after you purchase an instance. Even if no task is run in a Hologres instance, the payments for computing resources are settled on an hourly basis based on the selected computing capacity.|To stop billing for an instance, you can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance), find the instance for which you want to stop billing, and then click **Stop** in the **Actions** column.|
|Storage|The fee of storage resources varies based on different countries and regions.-   Singapore: USD 0.000379 per GB per hour
-   Hong Kong \(China\): USD 0.000364 per GB per hour
-   US: USD 0.000358 per GB per hour
-   Kuala Lumpur: USD 0.000410 per GB per hour
-   Indonesia: USD 0.000467 per GB per hour
-   India: USD 0.000422 per GB per hour

|The storage resources are immediately charged when data is stored in the instance. The payments are settled on an hourly basis.|The system stops billing for the storage resources after the stored data is deleted.|

Sample scenarios:

-   Scenario 1

    Assume that you purchase an instance with 64 CUs of computing resources and 500 GB storage resources based on the subscription billing method in the Singapore region. If you want to use this instance for six months, the total fee that you need to pay is USD 12,822.80722, which is calculated based on the following formula: USD 31.970149/CU/month × 64 CUs × 6 months + USD 0.182090/GB/month × 500 GB × 6 months = USD 12,822.80722.

-   Scenario 2

    Assume that you purchase an instance with 100 GB storage resources based on the subscription billing method in the Singapore region. However, you store 200 GB data in this instance in one hour due to quick expansion of your business. The extra fee in this hour is USD 0.0379, which is calculated based on the following formula: \(200 GB - 100 GB\) × USD 0.000379/GB/hour × 1 hour = USD 0.0379.

-   Scenario 3

    Assume that you purchase an instance based on the pay-as-you-go billing method in the Singapore region. If 64 CUs of computing resources and 100 GB storage resources are consumed in one hour, the total fee in this hour is USD 11.69166, which is calculated based on the following formula: USD 0.182090/CU/hour × 64 CUs × 1 hour + USD 0.000379/GB/hour × 100 GB × 1 hour = USD 11.69166.


## Change billing methods

If you purchase a Hologres instance based on the pay-as-you-go billing method, you can log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance) and change the billing method to subscription.

When you change the billing method from pay-as-you-go to subscription, take note of the following information:

-   After the billing method is changed, you are charged for computing resources based on the subscription billing method.
-   After the billing method is changed, the storage space is 0 GB by default. If data is stored in the current instance, you are charged for storage resources based on the pay-as-you-go billing method. You must purchase storage resources before you are charged for storage resources based on the subscription billing method.
-   If you purchase a Hologres instance based on the subscription billing method, you cannot change the billing method. If you change the billing method of an instance from pay-as-you-go to subscription, the billing method cannot be restored.

## Billing cycle

This section describes the billing cycles for Hologres instances.

-   Subscription: The system pushes a bill to your Alibaba Cloud account when you purchase a Hologres instance and select the subscription billing method. If the volume of data that is stored in this instance exceeds the specified limit, you are charged for the excess volume based on the pay-as-you-go billing method and the payments are settled on an hourly basis. The system pushes a bill to your Alibaba Cloud account that is used to purchase this instance on an hourly basis.
-   Pay-as-you-go: The system pushes a bill to your Alibaba Cloud account that is used to purchase this instance on an hourly basis.

## Select a billing method

Hologres supports two billing methods. You can select one based on your business needs.

-   The subscription billing method is suitable for developers and analysts. If you want to perform data analytics from time to time and the amount of data to be analyzed is small, we recommend that you select this method. The subscription billing method helps you limit the computing and storage resources that can be used. This way, costs can be effectively reduced.
-   If you want to regularly perform data analytics in the production environment, we recommend that you select the pay-as-you-go billing method to prevent idle resources.

**Note:** If you are a new user, we recommend that you select the pay-as-you-go billing method. When you use Hologres in the initial stage, you may use only a few resources. If you purchase reserved resources by CU, such resources may be left unused. The cost is relatively lower if you select the pay-as-you-go billing method.

