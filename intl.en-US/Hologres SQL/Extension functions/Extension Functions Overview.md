# Extension Functions Overview

This topic describes the extension functions provided by Hologres.

Hologres is compatible with PostgreSQL. The functions supported by Hologres are a subset of the PostgreSQL functions. You can use Hologres functions in the same way as the PostgreSQL functions. For more information, see [Mathematical functions](/intl.en-US/Hologres SQL/Functions/Mathematical functions.md). In actual use, PostgreSQL functions may not meet all your business requirements. Hologres provides some extension functions to simplify your business development, as listed in the following table.

|Function|Category|Internal table|Foreign table|
|--------|--------|--------------|-------------|
|[APPROX\_COUNT\_DISTINCT](/intl.en-US/Hologres SQL/Extension functions/Aggresgate Functions/APPROX_COUNT_DISTINCT.md)|Aggregate function|Supported|Supported|
|[USER\_DISPLAY\_NAME](/intl.en-US/Hologres SQL/Extension functions/Account and Security Functions/USER_DISPLAY_NAME.md)|Username conversion function|N/A|N/A|
|[HG\_USER\_DISPLAY\_NAME\_TO ID](/intl.en-US/Hologres SQL/Extension functions/Account and Security Functions/HG_USER_DISPLAY_NAME_TO ID.md)|Username conversion function|N/A|N/A|
|[GET\_JSON\_OBJECT](/intl.en-US/Hologres SQL/Extension functions/Hive-Compatible Function/GET_JSON_OBJECT.md)|Hive-compatible function|Supported|Supported|
|[MAX\_PT](/intl.en-US/Hologres SQL/Extension functions/MaxCompute-Compatible Function/MAX_PT.md)|MaxCompute-compatible function|Not supported|Supported|
|[HG\_CREATE\_TABLE\_LIKE](/intl.en-US/Hologres SQL/Extension functions/Toolkits Functions/HG_CREATE_TABLE_LIKE.md)|Tool function|N/A|N/A|
|[HG\_UPDATE\_DATABASE\_PROPERTY](/intl.en-US/Hologres SQL/Extension functions/Toolkits Functions/HG_UPDATE_DATABASE_PROPERTY.md)|Tool function|N/A|N/A|
|[SET\_TABLE\_PROPERTY](/intl.en-US/Hologres SQL/Extension functions/Toolkits Functions/SET_TABLE_PROPERTY.md)|Tool function|Supported|Supported|
|[HG\_SHARD\_ID\_FOR\_DISTRIBUTION\_KEY](/intl.en-US/Hologres SQL/Extension functions/Toolkits Functions/HG_SHARD_ID_FOR_DISTRIBUTION_KEY.md)|Tool function|Supported|Not supported|

