---
keyword: [Create terminals, use terminals, HoloStudio]
---

# Terminal

The Terminal module of Hologres is a PostgreSQL client that executes SQL statements. It allows you to concurrently write and query a large amount of data in real time. You can also perform data analytics by executing SQL statements. This topic describes how to create, use, and delete a terminal in HoloStudio.

-   An Alibaba Cloud account is created.
-   Real-name verification is completed.
-   A Hologres instance is purchased and bound to a DataWorks workspace. For more information, see [Quick start to HoloStudio](/intl.en-US/Common Development Tools/HoloStudio/Quick start to HoloStudio.md).

## Create a terminal

1.  Log on to the [Hologres console](https://hologram.console.aliyun.com/#/instance).

2.  In the top navigation bar of the console, move the pointer over the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4259623061/p134229.png) icon and select the region where your Hologres instance resides.

3.  In the left-side navigation pane, click **Instances**. On the **Hologres Instances** page, click **Go to DataWorks - HoloStudio**.

4.  In the top navigation bar, click the ![Show icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171669.png) icon and select the workspace.

5.  In the left-side navigation pane, click the ![Terminal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8402096061/p171684.png) icon to go to the Terminal page.

6.  Click the ![Create icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5770096061/p171689.png) icon. In the **Create Terminal** dialog box, set the required parameters.

    ![Create a terminal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9402096061/p171700.png)

    |Parameter|Description|
    |---------|-----------|
    |Terminal Type|Default value: **Holo Terminal**.|
    |Terminal Name|Specify a terminal name. Example: holo.|

7.  Click **Commit**.


## Use a terminal

1.  On the Terminal page, find the terminal and click the ![Run icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9402096061/p171702.png) icon in the **Test** column.

2.  In the **Enable Terminal** dialog box, set the database from the **Database** drop-down list.

    ![Enable a terminal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9402096061/p171703.png)

3.  Click **OK**.

    You can execute SQL statements to perform data analytics. The following statements provide an example:

    ```
    BEGIN;
    CREATE TABLE holo_bank_data (
     age int8,
     job text,
     marital text,
     education text,
     card text,
     housing text,
     loan text,
     contact text,
     month text,
     day_of_week text,
     duration text,
     campaign int8,
     pdays float8,
     previous float8,
     poutcome text,
     emp_var_rate float8,
     cons_price_idx float8,
     cons_conf_idx float8,
     euribor3m float8,
     nr_employed float8,
     y int8
    );
    COMMIT;
    insert into holo_bank_data values
    (53,  'technician', 'married',  'unknown',  'no', 'no', 'no', 'cellular', 'nov',  'fri',  138,  1,  999,  0,  'nonexistent',  1.22, 93.20,  42.00,  4.02, 5195.80,  0),
    (28,  'management', 'single', 'university.degree',  'no', 'yes',  'no', 'cellular', 'jun',  'thu',  339,  3,  6,  2,  'success',  1.69, 94.05,  39.79,  0.72, 4991.60,  1),
    (39,  'services', 'married',  'high.school',  'no', 'no', 'no', 'cellular', 'apr',  'fri',  185,  2,  999,  0,  'nonexistent',  1.80, 93.07,  47.10,  1.40, 5099.10,  0);
    
    select * from holo_bank_data;
    ```

    ![Run a terminal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9402096061/p171716.png)


## Delete a terminal

On the Terminal page, find the terminal and click the ![Delete icon](../images/p171717.png) icon in the **Test** column. In the **Delete** dialog box, click **OK**.

![Delete icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9402096061/p171720.png)

