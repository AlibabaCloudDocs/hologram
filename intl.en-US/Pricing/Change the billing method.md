# Change the billing method

After you create a pay-as-you-go instance, you can change the billing method of the instance to subscription.

## Usage notes

Hologres allows you to change the billing method of pay-as-you-go instances to subscription in the [Hologres console](https://hologram.console.aliyun.com/#/instance).

When you change the billing method of an instance, take note of the following items:

-   After you change the billing method of an instance, computing resources are no longer billed based on actual usage but based on the subscription billing method.
-   By default, after you change the billing method of an instance, the storage capacity of the instance is 0 GB. If data is stored in the instance, the stored data is charged based on the pay-as-you-go billing method. If you want the stored data to be charged based on the subscription billing method, you must purchase a storage plan.
-   You cannot change the billing method of a subscription Hologres instance to pay-as-you-go. After you change the billing method of a pay-as-you-go instance to subscription, you cannot change the billing method back to pay-as-you-go.

## Procedure

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance). In the top navigation bar, select a region.
2.  In the left-side navigation pane, click Instances.
3.  On the Hologres Instances page, find the instance whose billing method you want to change and click Switch to Subscription in the **Actions** column.
4.  On the Confirm Order page, perform the following operations:
    -   Set the Purchase Cycle parameter.
    -   Read and agree to the Hologres \(Subscription\) Agreement of Service by selecting the check box.
5.  Click **Activate** and complete the payment.

