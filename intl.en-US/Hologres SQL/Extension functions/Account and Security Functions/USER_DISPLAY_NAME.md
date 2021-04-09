---
keyword: [username conversion function, Hologres, USER\_DISPLAY\_NAME]
---

# USER\_DISPLAY\_NAME

You can use the USER\_DISPLAY\_NAME function to convert the display name of an account in Hologres into the email address of an Alibaba Cloud account or a string that indicates a RAM user. This topic describes how to use the USER\_DISPLAY\_NAME function in Hologres.

## Syntax

The following code shows the syntax of the USER\_DISPLAY\_NAME function:

```
SELECT user_display_name ( user_name )
```

## Parameters

The following table describes the parameter of the USER\_DISPLAY\_NAME function.

|Parameter|Description|
|---------|-----------|
|user\_name|The ID of an Alibaba Cloud account or a RAM user in the format of a string that consists mostly of digits. For example, the ID of an Alibaba Cloud account can be `13532241323xxx` and the ID of a RAM user can be `p4_23402030200xxx`.|

The return value is the email address of the Alibaba Cloud account such as `xx@aliyun.com` or a string that indicates the RAM user such as `RAM$mainaccount:subuser` or `RAM$public`.

## Examples

The following sample code provides examples on how to use the USER\_DISPLAY\_NAME function to convert the display name of an account in Hologres into the email address of an Alibaba Cloud account or a string that indicates a RAM user:

```
SELECT user_display_name ('13532241323xxx');
SELECT rolname, user_display_name(rolname) FROM pg_roles;
```

