---
keyword: [Hologres, billing method, subscription]
---

# Subscription

Subscription is a billing method that allows you to use resources only after you pay for them. The subscription billing method for Hologres supports dynamic configuration changes. You can scale in or out Hologres resources based on your business needs. This topic describes the subscription billing method for Hologres.

## Lifecycle management

If you select the subscription billing method, you must pay in advance to use Hologres. The following figure shows you how to manage a Hologres instance throughout its lifecycle in subscription mode.

![Lifecycle management in subscription mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1832490161/p214168.png)

-   Instance expiration:

    Assume that you purchase a Hologres instance based on the subscription billing method at the Alibaba Cloud International site \(alibabacloud.com\) and pay for computing and storage resources for four months in advance based on your business needs. You can use this instance immediately after successful payment. The Hologres instance is available to you for four months. After four months, Hologres stops providing services and the instance expires and enters the suspension state. To continue to use the instance, you must renew it based on the subscription billing method. If you do not renew your instance within 14 days during which no bill is pushed, the instance is released and deleted from the console. Data in the instance is also deleted and cannot be recovered.

-   Overdue payment:

    Assume that you purchase a Hologres instance based on the subscription billing method at the Alibaba Cloud International site \(alibabacloud.com\). However, your Alibaba Cloud account enters the overdue state during the use of the instance. Within 24 hours after the account is overdue, you can still use the instance. If you pay the arrears within this period, you can use the instance without interruption. If you do not pay the arrears within 24 hours, the instance is locked and cannot be accessed until it expires. The instance immediately enters the suspension state after it expires. You can continue to use the instance if you pay the arrears within 14 days after expiration. Otherwise, the instance is released and data in the instance is no longer retained.


**Note:** If you purchase a Hologres instance based on the pay-as-you-go billing method, you can change the billing method to subscription. If you purchase a Hologres instance based on the subscription billing method, you cannot change the billing method.

## Billable items and pricing

The following table describes the billable items and pricing when you purchase a Hologres instance in subscription mode.

|Billable item|Unit price|
|-------------|----------|
|Computing|The fee of computing resources varies based on different countries and regions.-   Singapore: USD 31.970149 per CU per month
-   Hong Kong \(China\): USD 30.194030 per CU per month
-   US: USD 29.432836 per CU per month
-   Kuala Lumpur: USD 35.268657 per CU per month
-   Indonesia: USD 39.642985 per CU per month
-   India: USD 34.723134 per CU per month

One CU represents 1 CPU core and 4 GB memory.|
|Storage|The fee of storage resources varies based on different countries and regions.-   Singapore: USD 0.182090 per GB per month
-   Hong Kong \(China\): USD 0.174627 per GB per month
-   US: USD 0.171642 per GB per month
-   Kuala Lumpur: USD 0.197015 per GB per month
-   Indonesia: USD 0.223970 per GB per month
-   India: USD 0.202567 per GB per month |

Billing formula: Fee paid for subscription = Computing capacity × Unit price × Number of months that you want to use the instance + Storage space × Unit price × Number of months that you want to use the instance.

Billing example: Assume that you purchase an instance with 128 CUs of computing resources and 500 GB storage resources based on the subscription billing method in the Singapore region. If you want to use this instance for six months, the total fee is USD 25,099.344432, which is calculated based on the following formula: 128 CUs × USD 31.970149/CU/month × 6 months + 500 GB × USD 0.182090/GB/month × 6 months = USD 25,099.344432.

## Configuration upgrade

If your Hologres instance that is purchased based on the subscription billing method does not have sufficient resources to meet your business needs, you can upgrade the instance configuration.

Billing formulas:

-   Existing resources:

    Amount paid for the existing resources = Computing capacity × Number of months you want to use the instance × Unit price + Storage space × Number of months you want to use the instance × Unit price

    Amount used for the existing resources = Amount paid for the existing resources/Number of hours you want to use the instance × Number of hours you have used the instance

    Amount remained for the existing resources = Amount paid for the existing resources - Amount used for the existing resources

-   New resources:

    Total amount to be paid for the new resources = Computing capacity × Number of months you want to use the instance × Unit price + Storage space × Number of months you want to use the instance × Unit price

    Actual amount to be paid for the new resources = Total amount to be paid for the new resources/Number of hours you want to use the instance × Number of remaining hours you can use the instance

-   Configuration upgrade:

    Amount to be paid for upgrading the configuration of the instance = Actual amount to be paid for the new resources - Amount remained for the existing resources


Assume that you purchase a Hologres instance with 64 CUs of computing resources and 300 GB storage resources based on the subscription billing method in the Singapore region and paid for the resources for two months on March 1. In this case, the instance is expected to expire on April 30. On March 13, you upgrade the configuration of the instance to 128 CUs of computing resources and 500 GB storage resources. The expiration time of the instance is not changed. The amount that you need to pay is calculated based on the following formulas:

-   The amount paid for the existing resources is USD 4,092.179072, which is calculated based on the following formula: 64 CUs × 2 months × USD 31.970149/CU/month + 300 GB × 2 months × USD 0.182090/GB/month = USD 4,092.179072.
-   The amount used for the existing resources is USD 818.4358144, which is calculated based on the following formula: USD 4,092.179072/1,440 hours × 288 hours = USD 818.4358144.

    **Note:** You have paid for existing resources for two months of 30 days. You have used the instance for 12 days from March 1 to March 13. The number of hours you want to use the instance is 1,440 hours, which is calculated based on the following formula: 2 months × 30 days/month × 24 hours/day = 1,440 hours. The number of hours you have used the instance is 288 hours, which is calculated based on the following formula: 12 days × 24 hours/day = 288 hours.

-   The amount remained for the existing resources is USD 3,273.7432576, which is calculated based on the following formula: USD 4,092.179072 - USD 818.4358144 = USD 3,273.7432576.
-   The total amount to be paid for the new resources is USD 8,366.448144, which is calculated based on the following formula: 128 CUs × 2 months × USD 31.970149/CU/month + 500 GB × 2 months × USD 0.182090/GB/month = USD 8,366.448144.
-   The actual amount to be paid for the new resources is USD 6,693.1585152, which is calculated based on the following formula: USD 8,366.448144/1,440 hours × 1,152 hours = USD 6,693.1585152.

    **Note:** You are expected to pay for the new resources for two months of 30 days. However, you can use the instance only for 48 days from March 13 to April 30. The number of hours you want to use the instance is 1,440 hours, which is calculated based on the following formula: 2 months × 30 days/month × 24 hours/day = 1,440 hours. The number of remaining hours you can use the instance is 1,152 hours, which is calculated based on the following formula: 48 days × 24 hours/day = 1,152 hours.

-   The amount to be paid for upgrading the configuration of the instance is USD 145.672, which is calculated based on the following formula: USD 6,693.1585152 - USD 3,273.7432576 = USD 145.672.

**Note:**

-   In this example, each month is assumed to contain 30 days to simplify calculation.
-   This example only illustrates the billing logic of configuration upgrade. Refer to your bill for the accurate amount.

## Configuration downgrade

If resources allocated to your Hologres instance that is purchased based on the subscription billing method are excessive, you can downgrade the instance configuration to reduce costs.

Billing formulas:

-   Existing resources:

    Amount paid for the existing resources = Computing capacity × Number of months you want to use the instance × Unit price + Storage space × Number of months you want to use the instance × Unit price

    Amount used for the existing resources = Amount paid for the existing resources/Number of hours you want to use the instance × Number of hours you have used the instance

    Amount remained for the existing resources = Amount paid for the existing resources - Amount used for the existing resources

-   New resources:

    Total amount to be paid for the new resources = Computing capacity × Number of months you want to use the instance × Unit price + Storage space × Number of months you want to use the instance × Unit price

    Actual amount to be paid for the new resources = Total amount to be paid for the new resources/Number of hours you want to use the instance × Number of remaining hours you can use the instance

-   Configuration downgrade:

    Amount to be paid for downgrading the configuration of the instance = Actual amount to be paid for the new resources - Amount remained for the existing resources


Assume that you purchase a Hologres instance with 128 CUs of computing resources and 500 GB storage resources based on the subscription billing method in the Singapore region and paid for the resources for three months on March 1. In this case, the instance is expected to expire on May 31. On March 21, you downgrade the configuration of the instance to 64 CUs of computing resources and 300 GB storage resources. The expiration time of the instance is not changed. The amount that you need to pay is calculated based on the following formulas:

-   The amount paid for the existing resources is USD 12,549.672216, which is calculated based on the following formula: 128 CUs × 3 months × USD 31.970149/CU/month + 500 GB × 3 months × USD 0.182090/GB/month = USD 12,549.672216.
-   The amount used for the existing resources is USD 2,788.816, which is calculated based on the following formula: USD 12,549.672216/2,160 hours × 480 hours = USD 2,788.816.

    **Note:** You have paid for existing resources for three months of 30 days. You have used the instance for 20 days from March 1 to March 21. The number of hours you want to use the instance is 2,160 hours, which is calculated based on the following formula: 3 months × 30 days/month × 24 hours/day = 2,160 hours. The number of hours you have used the instance is 480 hours, which is calculated based on the following formula: 20 days × 24 hours/day = 480 hours.

-   The amount remained for the existing resources is USD 9,760.856216, which is calculated based on the following formula: USD 12,549.672216 - USD 2,788.816 = USD 9,760.856216.
-   The total amount to be paid for the new resources is USD 6,302.149608, which is calculated based on the following formula: 64 CUs × 3 months × USD 31.970149/CU/month + 300 GB × 3 months × USD 0.182090/GB/month = USD 6,302.149608.
-   The actual amount to be paid for the new resources is USD 4,901.67192, which is calculated based on the following formula: USD 6,302.149608/2,160 hours × 1,680 hours = USD 4,901.67192.

    **Note:** You are expected to pay for the new resources for three months of 30 days. However, you can use the instance only for 70 days from March 21 to May 31. The number of hours you want to use the instance is 2,160 hours, which is calculated based on the following formula: 3 months × 30 days/month × 24 hours/day = 2,160 hours. The number of remaining hours you can use the instance is 1,680 hours, which is calculated based on the following formula: 70 days × 24 hours/day = 1,680 hours.

-   The amount to be paid for downgrading the configuration of the instance is USD -4,859.1843, which is calculated based on the following formula: USD 4,901.67192 - USD 9,760.856216 = USD -4,859.1843.
-   Alibaba Cloud needs to refund USD 4,859.1843 to your account.

**Note:**

-   In this example, each month is assumed to contain 30 days to simplify calculation.
-   This example only illustrates the billing logic of configuration downgrade. Refer to your bill for the accurate amount.

## Message notification mechanism

The following table describes the message notification mechanism of Hologres instances in subscription mode.

|Billing method|Reminder time for service suspension due to instance expiration|Downtime|Release reminder time|
|--------------|---------------------------------------------------------------|--------|---------------------|
|Subscription|Seven days before expiration|The instance stops providing services immediately upon expiration.|Seven days before release|
|Three days before expiration|Three days before release|
|One day before expiration|One day before release|

Example: Assume that you purchase a Hologres instance based on the subscription billing method at the Alibaba Cloud International site \(alibabacloud.com\) and pay for computing and storage resources for two months in advance. You can receive expiration notifications by SMS, phone call, or email seven days, three days, and one day before the instance expires. The instance enters the suspension state when it expires. Then, Hologres stops providing services. If you do not renew your instance within 14 days, the system automatically releases the instance and sends release notifications by SMS, phone call, or email seven days, three days, and one day before the release.

