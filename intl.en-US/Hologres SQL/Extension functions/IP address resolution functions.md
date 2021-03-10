---
keyword: [ip address resolution functions, hologres]
---

# IP address resolution functions

This topic describes the Hologres functions that are used to resolve IP addresses and shows you how to use these functions.

## Prerequisites

A Hologres instance is purchased. A development tool is connected to the instance. For more information, see [Use HoloWeb to develop data in a Hologres instance]().

## Overview

The following table describes the IP address resolution functions in Hologres.

|Function|Description|
|--------|-----------|
|[ip2region](#section_bor_6lz_pj2)|Queries the information about an IP address, such as the country, province, and city. The returned data can be displayed in different languages.|
|[ip\_country](#section_5fk_8x7_mol)|Queries the country to which an IP address belongs.|
|[ip\_province](#section_5fk_8x7_mol)|Queries the province to which an IP address belongs.|
|[ip\_city](#section_5fk_8x7_mol)|Queries the city to which an IP address belongs.|
|[ip\_isp](#section_5fk_8x7_mol)|Queries the Internet service provider \(ISP\) that manages an IP address.|
|[ip2long](#section_sp8_zsu_upl)|Converts an IP address to a number.|
|[long2ip](#section_qv0_o9x_014)|Converts a number to an IP address.|

## Limits

Take note of the following rules when you use the IP address resolution functions in Hologres:

-   The IP address resolution functions in Hologres support only IPv4 addresses.
-   Only Hologres V0.9 and later allow you to use the IP address resolution functions. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   **The IP address resolution functions work based on extension functions.** Before you use them, contact the superuser of your Hologres instance and ask the superuser to execute the following statement:

    ```
    create extension ipdb;
    ```

    **Note:** This statement takes effect only in the current database. If you switch to another database, the superuser must execute the statement again.


## ip2region

-   Description

    This function is used to query the information about an IP address, including the country, province, city, and ISP. The returned data can be displayed in different languages.

-   Syntax

    ```
    ip2region(ip, level, lang)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |ip|The IP address whose information is to be queried. You can specify only IPv4 addresses. The value is of the TEXT type.|
    |level|The category of the information to be queried. The value is of the TEXT type. Valid values:    -   Country: the country to which the IP address belongs.
    -   Province: the province to which the IP address belongs.
    -   City: the city to which the IP address belongs.
    -   isp: the ISP that manages the IP address. |
    |lang|The language of the data to be returned. The value is of the TEXT type. Valid values: CN and EN.|

-   The returned data is of the TEXT type.
-   Examples

    ```
    select ip2region('31.13.79.1', 'country', 'CN');
    select ip2region('31.13.79.1', 'country', 'EN');
    select ip2region('31.13.79.1', 'province', 'EN');
    select ip2region('31.13.79.1', 'city', 'EN');
    select ip2region('31.13.79.1', 'isp', 'EN');
    ```


## ip\_country, ip\_province, ip\_city, and ip\_isp

-   Description

    You can separately use the ip\_country, ip\_province, ip\_city, or ip\_isp function to query the country, province, city, or ISP that corresponds to an IP address. These four functions are simple versions of the ip2region function.

-   Syntax

    ```
    ip_country/ip_province/ip_city/ip_isp (ip, [lang])
    ```

    The ip\_country, ip\_province, ip\_city, and ip\_isp functions are used to query the following information about an IP address: the country, province, and city to which the IP address belongs, and the ISP that manages the IP address.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |ip|The IP address whose information is to be queried. You can specify only IPv4 addresses. The value is of the TEXT type.|
    |lang|The language of the data to be returned. The value is of the TEXT type. Valid values: CN and EN. If you leave this parameter unset, the returned data is displayed in English.|

-   The returned data is of the TEXT type.
-   Examples

    ```
    select ip_country('31.13.79.1'); // The default language of the data to be returned is English.
    select ip_country('31.13.79.1','EN');
    select ip_province('31.13.79.1');
    select ip_province('31.13.79.1','EN');
    select ip_city('31.13.79.1');
    select ip_city('31.13.79.1','EN');
    select ip_isp('115.239.210.27');
    select ip_isp('115.239.210.27','EN');
    ```


## ip2long

-   Description

    This function is used to convert an IPv4 address to a number.

-   Syntax

    ```
    ip2long(ip)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |ip|The IP address to be converted. You can specify only IPv4 addresses. The value is of the TEXT type.|

-   The returned data is of the BIGINT type.
-   Examples

    ```
    select ip2long('31.13.79.1');
    ```


## long2ip

-   Description

    This function is used to convert a number to an IPv4 address.

-   Syntax

    ```
    long2ip(longVal)
    ```

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |longVal|The number to be converted. The value is of the TEXT type.|

-   The returned data is of the TEXT type.
-   Examples

    ```
    select long2ip(ip2long('31.13.79.1'));
    ```


