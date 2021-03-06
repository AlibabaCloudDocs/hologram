---
keyword: [数据脱敏, Hologres]
---

# 数据脱敏（Beta）

Hologres为您提供数据脱敏功能，支持按照列级别设置脱敏。启用该功能后，如果您查询的数据涉及敏感信息，则在展示结果中，该部分数据会被脱敏展示，提高了对敏感及私密数据的保护。本文为您介绍Hologres如何开启、查询及删除数据脱敏功能。

随着大数据时代的来临，大数据、云计算和人工智能等新技术应用不断深化，为数据的深度挖掘及分析提供了强有力的支撑，大数据中蕴含的巨大价值被逐步挖掘出来。与此同时也带来了敏感及私密信息保护方面的棘手难题。

Hologres提供的数据脱敏功能，实现了数据在高效共享、挖掘及分析的同时，使得敏感及隐私信息不被泄露，提高了对敏感及私密数据的保护。

## 使用数据脱敏

Hologres支持对目标列或目标用户设置数据脱敏。使用数据脱敏前，您需登录目标数据库开启脱敏功能。完成后可配置对应的脱敏规则，针对某一列或某个用户设置脱敏。具体操作步骤如下。

1.  为目标数据库开启数据脱敏。

    数据脱敏功能默认不开启，需要Superuser登录至目标数据库开启该功能，开启命令及相关参数说明如下。

    -   命令：

        ```
        CREATE EXTENSION IF NOT EXISTS hg_anon; --创建hg_anon扩展函数。
        ALTER DATABASE <db_name> SET hg_anon_enable = on;  --为指定数据库开启数据脱敏功能，默认为off。
        ```

    -   示例：

        ```
        CREATE EXTENSION IF NOT EXISTS hg_anon;--创建hg_anon扩展函数。
        ALTER DATABASE test SET hg_anon_enable = on;--为数据库test开启数据脱敏功能。
        ```

    -   参数说明：

        |参数|参数的相关描述|
        |--|-------|
        |hg\_anon|hg\_anon是Hologres内部封装的扩展函数，您需要调用该扩展函数才能开启数据脱敏功能。|
        |<db\_name\>|需要开启数据脱敏功能的数据库名称。使用时需要替换<db\_name\>为实际数据库名称。|
        |hg\_anon\_enable|选择开启或关闭数据脱敏功能，取值如下：        -   on，开启数据脱敏。
        -   off，关闭数据脱敏。
默认该参数设置为off。|

2.  配置数据脱敏规则。

    -   命令：

        ```
        ALTER DATABASE <db_name> SET hg_anon_labels = '[
          {"label": <label_name1>, "mathod", <method1>},
          {"label": <label_name2>, "mathod", <method2>},
          ... 
        ]'; --label_name是您自定义的名称，method是Hologres内置的一些方法。
        ```

        **说明：** 执行ALTER DATABASE命令后，当前链接会不生效，需要重开一个连接。您可使用如下命令查看设置是否生效。

        ```
        show hg_anon_enable;
        ```

    -   示例：

        ```
        ALTER DATABASE test_db SET hg_anon_labels = '[
        {"label":"ip", "method":{"desensType":"mask", "type":"ip"}},
        {"label":"email", "method":{"desensType":"mask", "type":"email"}},
        {"label":"name", "method":{"desensType":"mask", "type":"name"}},
        {"label":"first_mask", "method":{"desensType":"mask", "type":"user_define", "before":1, "after":0}},
        {"label":"hash", "method":{"desensType":"hash", "type":"md5", "salt":""}}]';
        ```

    -   参数说明：

        |脱敏项|脱敏内容描述|脱敏结果示例|
        |---|------|------|
        |\{"desensType":"mask", "type":"ip"\}|IP地址脱敏。|192.\*.\*.\*|
        |\{"desensType":"mask", "type":"email"\}|邮箱地址脱敏。|abc\*\*\*@gmail.com|
        |\{"desensType":"mask", "type":"name"\}|名称脱敏。|\*五|
        |\{"desensType":"hash", "type":"md5", "salt":""\}|Hash脱敏。|e086aa137fa19f67d27b39d0eca186103228f322c9c98a125554a24f875f0f7e|
        |\{"label":"first\_mask", "method":\{"desensType":"mask", "type":"user\_define", "before":1, "after":0\}\}\{"label":"last\_mask", "method":\{"desensType":"mask", "type":"user\_define", "before":0, "after":1\}\}|自定义内容脱敏。|无|

3.  对目标列数据进行脱敏

    可由Superuser或表Owner对目标列进行脱敏，具体命令、示例、参数说明如下。

    -   命令：

        **说明：** 对多列数据进行脱敏时，需要多次执行该语句。

        ```
        SECURITY LABEL FOR hg_anon ON COLUMN <tablename>.<col_name> IS label_name;
        ```

    -   示例：对holotest表的id列按照名称脱敏。

        ```
        SECURITY LABEL FOR hg_anon ON COLUMN holotest.id IS 'name';
        ```

    -   参数说明：

        |参数|描述|
        |--|--|
        |hg\_anon|hg\_anon是Hologres内部封装的扩展函数，您需要调用该扩展函数才能开启数据脱敏功能。|
        |<tablename\>|需要脱敏的列所在的表名称。使用时需要替换<tablename\>为实际表名称。|
        |<col\_name\>|需要脱敏的列名称。使用时需要替换<col\_name\>为实际列名称。|
        |label\_name|系统预设的脱敏函数，详情请参见脱敏函数。例如，**replace**脱敏函数用于将任意字符串替换为**xxxx**。 |

    -   脱敏结果示例：

        ![结果示例](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2302455261/p291616.png)

4.  对用户进行脱敏。

    可由Superuser对目标用户设置脱敏权限，指定可查看脱敏前或脱敏后的内容，具体命令、示例与参数说明如下。

    -   命令：

        ```
        SECURITY LABEL FOR hg_anon ON ROLE user_name IS ANON_RULE;
        ```

    -   示例：
        -   对**云账号id**所有列均不脱敏。

            ```
            SECURITY LABEL FOR hg_anon ON ROLE '云账号id' IS 'all:unmasked';
            ```

        -   对RAM用户的所有列进行脱敏。

            ```
            SECURITY LABEL FOR hg_anon ON ROLE "RAM$主账号:子账号" IS 'all:masked';
            ```

        -   对RAM用户使用name脱敏函数的列不进行脱敏。

            **说明：** 您可通过`select current_user`查看当前用户名。

            ```
            SECURITY LABEL FOR hg_anon ON ROLE "RAM$主账号:子账号" IS 'name:unmasked';
            ```

    -   参数说明：

        **说明：** 如指定用户对于设置的脱敏（如repalce）没有unmasked属性，则系统将会默认他是maseked属性，查询对应的脱敏列时将会展示脱敏后的内容。

        |参数|描述|
        |--|--|
        |label\_name|自定义的脱敏函数。|
        |all:unmasked|设置完成之后，对该用户不脱敏。|
        |all:masked|设置完成之后，对该用户脱敏。|


## 查看脱敏设置

-   查看目标表所有列的脱敏设置，示例语句如下。

    ```
    SELECT * FROM pg_seclabel;
    ```

    结果如下图所示。

    ![查看脱敏设置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7562572061/p173614.png)

    参数说明如下表所示。

    |参数|描述|
    |--|--|
    |objoid|表示目标表。|
    |objsubid|表示对目标表的第几个字段脱敏。|
    |label|表示对目标字段脱敏时使用的脱敏函数。|

-   查看当前用户的脱敏设置。

    ```
    SELECT * FROM pg_shseclabel;
    ```


## 删除脱敏设置

您可以使用如下语句删除指定表目标列的脱敏设置。

```
SECURITY LABEL FOR hg_anon ON COLUMN tbname.col_name IS NULL；
```

