---
keyword: [data de-identification, Hologres]
---

# Data de-identification

Hologres provides a data de-identification feature to allow you to de-identify data in columns. After you enable this feature, sensitive information is de-identified in query results. This helps you protect sensitive and private data. This topic shows you how to enable and disable the data de-identification feature, and check the configuration.

In the era of big data, new technologies such as big data, cloud computing, and artificial intelligence \(AI\) are widely applied. This provides powerful supports for in-depth data mining and analysis and helps enterprises gradually explore the potential value of big data. However, the sensitive and private data is difficult to protect when these technologies are applied.

When you use these technologies to implement highly efficient data sharing, mining, and analysis, the data de-identification feature provided by Hologres can prevent leaks of your sensitive and private data.

## Use the data de-identification feature

1.  Enable the data de-identification feature.

    By default, the data de-identification feature is disabled. To enable this feature for a database, log on to the database as a superuser and execute the following statements:

    ```
    CREATE EXTENSION IF NOT EXISTS hg_anon; // Create the hg_anon extension function.
    ALTER DATABASE <db_name> SET hg_anon_enable = on;  // Enable the data de-identification feature for the database <db_name>.
    ```

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |hg\_anon|The built-in extension function provided by Hologres. To enable the data de-identification feature, you must call this function.|
    |<db\_name\>|The name of the database for which you want to enable the data de-identification feature. Replace <db\_name\> with the name of the database.|
    |hg\_anon\_enable|Specifies whether to enable or disable the data de-identification feature. Valid values:    -   on: enables the data de-identification feature.
    -   off: disables the data de-identification feature.
Default value: off.|

    For example, you can execute the following statements to enable the data de-identification feature for a database named test:

    ```
    CREATE EXTENSION IF NOT EXISTS hg_anon;
    ALTER DATABASE test SET hg_anon_enable = on;
    ```

2.  Configure a data de-identification rule.

    Hologres allows you to configure a data de-identification rule for a specified column or a specified user. You can specify a column name or user type, and use the built-in de-identification functions to de-identify data.

    -   De-identify a specified column.

        When you de-identify a specified column as a superuser or a table owner, use the following syntax:

        ```
        SECURITY LABEL FOR hg_anon ON COLUMN <tablename>.<col_name> IS label_name;
        ```

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |hg\_anon|The built-in extension function provided by Hologres. To enable the data de-identification feature, you must call this function.|
        |<tablename\>|The name of the table that contains the column to be de-identified. Replace <tablename\> with the name of the table.|
        |<col\_name\>|The name of the column to be de-identified. Replace <col\_name\> with the name of the column.|
        |label\_name|The built-in de-identification function to be used to de-identify data. For more information, see the "De-identification functions" section. For example, the replace function replaces a specific string with xxxx.|

        **Note:** To de-identify multiple columns, execute the preceding statement multiple times.

        Example 1: De-identify the id column in the holotest table and replace data in the column with xxxx.

        ```
        SECURITY LABEL FOR hg_anon ON COLUMN holotest.id IS 'replace';
        ```

        Example 2: De-identify the id and age columns in the holotest table and replace data in these columns with xxxx.

        ```
        SECURITY LABEL FOR hg_anon ON COLUMN holotest.id IS 'replace'; 
        SECURITY LABEL FOR hg_anon ON COLUMN holotest.age IS 'replace';
        ```

    -   De-identify data for a user or a user group.

        When you de-identify data for a user or a user group as a superuser, use the following syntax:

        ```
         SECURITY LABEL FOR hg_anon ON ROLE user_name IS ANON_RULE;
        ```

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |hg\_anon|The built-in extension function provided by Hologres. To enable the data de-identification feature, you must call this function.|
        |<user\_name\>|The name of the user or user group. To apply the same data de-identification rule to multiple users, add the users to a user group and configure a data de-identification rule for the user group. Replace <user\_name\> with the name of the user or user group.|
        |ANON\_RULE|The syntax that is used to configure a data de-identification rule for the user or user group. For more information, see the following section.|

        Syntax of ANON\_RULE:

        ```
          ANON_RULE:
                 [label_name|all]:[masked|unmasked]{;ANON_RULE}
        ```

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |label\_name\|all|The label\_name parameter specifies a built-in de-identification function. For more information, see [De-identification functions](#section_lbl_y6k_kjx). When the label\_name parameter is used, you specify whether to de-identify the columns to which the de-identification rule specified by the label\_name parameter applies for the specified user or user group.When the all keyword is used, you can specify whether to de-identify all columns for the specified user or user group.

The label\_name parameter or the all keyword is commonly used together with the masked or unmasked keyword. Examples:

        -   `all:masked`: De-identify all columns for the specified user or user group.
        -   `replace:masked`: De-identify the columns to which the replace de-identification function applies for the specified user or user group. The value of the label\_name parameter is a de-identification function that is supported by Hologres. In this example, the replace function is used. |
        |masked|The masked keyword indicates that the specified columns are de-identified for the specified user or user group. This keyword is commonly used together with the label\_name parameter or the all keyword.For example, when you use `all:masked`, all columns are de-identified for the specified user or user group. |
        |unmasked|The unmasked keyword indicates that the specified columns are not de-identified for the specified user or user group. This keyword is commonly used together with the label\_name parameter or the all keyword. For example, when you use `all:unmasked`, all columns are not de-identified for the specified user or user group.If the unmasked keyword is not included when you configure a data de-identification rule for the specified user or user group, the masked keyword is used by default.

Assume that you specify the replace de-identification function for the label\_name parameter to de-identify a column. The de-identification effects vary with different settings.

        -   When `all:unmasked` is used, all columns are not de-identified. In this case, data in the specified column is displayed in plaintext in a query result for the specified user or user group.
        -   When `all:masked` is used, all columns are de-identified. In this case, data in the specified column is de-identified in a query result for the specified user or user group.
        -   When `all` is used, all columns are de-identified. In this case, data in the specified column is de-identified in a query result for the specified user or user group. |

        Example 1: Do not de-identify all columns for a user group named advanced\_user, to which all advanced users belong.

        ```
        SECURITY LABEL FOR hg_anon ON ROLE advanced_user IS 'all:unmasked'; 
        ```

        Example 2: Do not de-identify the columns to which the name de-identification function applies for a user named user1.

        ```
        SECURITY LABEL FOR hg_anon ON ROLE user1 IS 'name:unmasked';
        ```


## View the data de-identification configuration

-   Execute the following statement to view the data de-identification configuration for all columns in the specified table:

    ```
    SELECT * FROM pg_seclabel;
    ```

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |objoid|The table in which data is de-identified.|
    |objsubid|The sequence number of the column that is de-identified in the table.|
    |label|The de-identification function that is used to de-identify the column.|

-   Execute the following statement to view the data de-identification configuration for the current user:

    ```
    SELECT * FROM pg_shseclabel;
    ```


## Delete a data de-identification rule

Execute the following statement to delete the data de-identification rule that is configured for the specified column in the specified table:

```
SECURITY LABEL FOR hg_anon ON COLUMN tbname.col_name IS NULL;
```

## De-identification functions

The following table describes the de-identification functions that are supported by Hologres.

|De-identification function|Description|Raw data|Data after de-identification|
|--------------------------|-----------|--------|----------------------------|
|replace|Replaces a specified string with `xxxx`.|187222|xxxx|
|mask|Replaces the second 1/3 part of a string with `*`. When this function is used, the length of the string is not changed and the first 1/3 part and the last 1/3 part of the string are displayed in plaintext.|110106333222011234|110106\*\*\*\*\*\*011234|
|cn\_id\_card|De-identifies an ID card number. Only the first and last digits are displayed in plaintext. The rest digits are replaced by `*`.|516626188808082234|5\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*4|
|cn\_phone|De-identifies a mobile number. Only the first three digits and the last two digits are displayed in plaintext. The rest digits are replaced by `*`.|13912345679|139\*\*\*\*\*\*79|
|bank\_card|De-identifies a bank card number. Only the last four digits are displayed in plaintext. The rest digits are replaced by `*`.|632200020089526127|\*\*\*\*\*\*\*\*\*\*\*\*\*\*6127|
|email|De-identifies an email address. Only the first three characters before the at sign \(`@`\) are displayed in plaintext. The other characters before the at sign \(@\) are replaced by `*`.|m1888@163.com|m18\*\*\*@163.com|
|ip|De-identifies an IP address or a MAC address. Only the content in the first byte in the address is displayed in plaintext. The content in the rest bytes is replaced by `*`.|192.188.2.2|192.\*\*\*. \*. \*|
|name|De-identifies a name. For a Chinese name:

-   If the number of Chinese characters is greater than or equal to four, replace the first two Chinese characters with `*`.
-   If the number of Chinese characters is less than or equal to three, replace the first Chinese character with `*`.

For an English name: If the number of fields is greater than or equal to two, replace the first field with `*`.

|-   Willam li

|-   \* li |
|address|De-identifies an address. Only the province, city, district, and county in the address are displayed in plaintext. The rest characters are replaced by `*`.``|-   Zhangjiang Hi-tech Park, Pudong District, Shanghai
-   No. 969, Wenyi West Road, Yuhang District, Hangzhou, Zhejiang

|-   \*\*\*\*Pudong District, Shanghai
-   \*\*\*\*\*\*\*\*Yuhang District, Hangzhou, Zhejiang |

