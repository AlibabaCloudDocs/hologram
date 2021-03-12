---
keyword: [账号与安全函数, Hologres, USER\_DISPLAY\_NAME]
---

# USER\_DISPLAY\_NAME

USER\_DISPLAY\_NAME用于转换Hologres账号为阿里云账号ID。本文为您介绍在交互式分析Hologres中USER\_DISPLAY\_NAME的用法。

## 命令语法

USER\_DISPLAY\_NAME的命令语法如下所示：

```
SELECT user_display_name ( user_name )
```

## 参数说明

参数说明如下表所示。

|参数|描述|
|--|--|
|user\_name|阿里云账号ID。通常为数字形式，例如`13532241323xxx`或RAM用户`p4_23402030200xxx`。|

返回值为邮箱形式或RAM形式的阿里云账号，例如`xx@aliyun.com`，`RAM$mainaccount:subuser`或`RAM$public`。

## 使用示例

转换Hologres账号为阿里云账号ID的示例语句如下。

```
SELECT user_display_name ('13532241323xxx');
SELECT rolname, user_display_name(rolname) FROM pg_roles;
```

