---
keyword: [IP地址解析, Hologres]
---

# IP地址解析函数

本文将会为您介绍Hologres中IP地址解析相关函数以及函数的用法。

## 前提条件

在使用IP地址解析相关函数之前，您需要开通Hologres实例并连接开发工具，操作示例请参见[基于Hologres实例使用HoloWeb]()。

## 函数概述

Hologres中使用到的IP地址解析相关函数如下表所示。

|函数|说明|
|--|--|
|[ip2region](#section_bor_6lz_pj2)|获取IP地址对应的国家、省份、城市信息，且支持语言切换。|
|[ip\_country](#section_5fk_8x7_mol)|获取IP地址对应的国家。|
|[ip\_province](#section_5fk_8x7_mol)|获取IP地址对应的省份。|
|[ip\_city](#section_5fk_8x7_mol)|获取IP地址对应的城市。|
|[ip\_isp](#section_5fk_8x7_mol)|获取IP地址对应的互联网服务提供商。|
|[ip2long](#section_sp8_zsu_upl)|将IP地址转换成一个数字。|
|[long2ip](#section_qv0_o9x_014)|将数字转换成一个IP地址。|

## 使用限制

在Hologres中使用IP地址解析相关函数需要注意如下事项：

-   Hologres中使用IP地址解析相关函数目前仅支持IPv4的地址。
-   仅Hologres V0.9及以上版本支持IP地址解析相关函数，如果您的实例是V0.9以下版本，请您[提交工单](https://workorder-intl.console.aliyun.com/)或者加入在线钉钉群升级实例。
-   **IP地址解析函数是基于扩展实现的**，使用函数之前需要实例的管理员（Superuser）执行如下扩展命令。

    ```
    create extension ipdb;
    ```

    **说明：** 该命令是DB级别的，一个DB只需要执行一次即可，切换到新DB后需要管理员（Superuser）再执行一次。


## ip2region

-   函数说明

    该函数主要用于获取IP地址对应的国家、省份、城市和互联网服务提供商信息，且支持语言切换。

-   函数语法

    ```
    ip2region(ip, level, lang)
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |ip|text类型，表示待查询的IP地址（当前只支持ipv4）。|
    |level|text类型， 包含四个可选项：    -   IP地址所属国家（Country）
    -   IP地址所属省份（Province）
    -   IP地址所属城市（City）
    -   互联网服务提供商（isp） |
    |lang|text类型，仅支持CN、EN。指定返回结果的语言类型为中文或者英文。|

-   返回值类型：text类型。
-   使用示例

    ```
    select ip2region('31.13.79.1', 'country', 'CN');
    select ip2region('31.13.79.1', 'country', 'EN');
    select ip2region('31.13.79.1', 'province', 'EN');
    select ip2region('31.13.79.1', 'city', 'EN');
    select ip2region('31.13.79.1', 'isp', 'EN');
    ```


## ip\_country/ip\_province/ip\_city/ip\_isp

-   函数说明

    ip\_country/ip\_province/ip\_city/ip\_isp是ip2region函数的一种简洁用法，主要用于获取IP地址对应的国家、省份、城市和互联网服务提供商信息。

-   函数语法

    ```
    ip_country/ip_province/ip_city/ip_isp (ip, [lang])
    ```

    其中，ip\_country为获取IP地址对应的国家，ip\_province为获取IP地址对应的省份，ip\_cityip\_isp为获取IP地址对应的互联网服务提供商。

-   参数说明

    |参数|说明|
    |--|--|
    |ip|text类型，表示待查询的IP地址（当前只支持ipv4）。未指定语言类型时，默认是英文|
    |lang|text类型，仅支持CN、EN。指定返回结果的语言类型为中文或者英文。|

-   返回值类型：text类型。
-   使用示例

    ```
    select ip_country('31.13.79.1');//默认返回英文
    select ip_country('31.13.79.1','EN');
    select ip_province('31.13.79.1');
    select ip_province('31.13.79.1','EN');
    select ip_city('31.13.79.1');
    select ip_city('31.13.79.1','EN');
    select ip_isp('115.239.210.27');
    select ip_isp('115.239.210.27','EN');
    ```


## ip2long

-   函数说明

    该函数主要用于将IPv4地址转换成数字格式。

-   函数语法

    ```
    ip2long(ip)
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |ip|text类型，表示待查询的IP地址（当前只支持ipv4）。|

-   返回值类型：bigint类型。
-   使用示例

    ```
    select ip2long('31.13.79.1');
    ```


## long2ip

-   函数说明

    该函数主要用于将数字格式转换成IPv4地址。

-   函数语法

    ```
    long2ip(longVal)
    ```

-   参数说明

    |参数|说明|
    |--|--|
    |longVal|text类型，表示待转换的数字。|

-   返回值类型：text类型。
-   使用示例

    ```
    select long2ip(ip2long('31.13.79.1'));
    ```


