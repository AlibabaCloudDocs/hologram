---
keyword: [Hologres console, instance configurations, network configuration, connection method]
---

# Instance configurations

The instance configurations page of the Hologres console provides the basic information, network configuration, and connection methods of a Hologres instance. You can also change the specifications of the instance on this page.

## Basic information

The instance configurations page shows the basic information of a Hologres instance. The information includes the name, ID, region, version, billing method, specifications, and creation time.

You can click **Upgrade** or **Downgrade** to change the specifications of the instance.

![663](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6431417951/p94175.png)

## Network configuration

You can access a Hologres instance over a public network, the classic network, or a virtual private cloud \(VPC\). Select a network type and a connection tool based on your business requirements.

|Network type|Endpoint|Scenario|Remarks|
|------------|--------|--------|-------|
|**Public Network**|`<instancename>-cn-<region>.hologres.aliyuncs.com:80`|Select this network type if you want to access a Hologres instance over a network with no limits on network connections. You can use extract, transform, load \(ETL\) tools, business intelligence \(BI\) tools, and data applications to connect to the Hologres instance.|You can turn this network type on or off.|
|**Internal Network**|`<instancename>-cn-<region>-internal.hologres.aliyuncs.com:80`|Select this network type if you want to access a Hologres instance over the classic network. In this case, the access does not consume public network traffic.|This network type is turned on by default.|
|**VPC**|`<instancename>-cn-<region>-vpc.hologres.aliyuncs.com:80`|Select this network type if you want to access a Hologres instance over a VPC.|You can turn this network type on or off.|

## Connection methods

The **Connection Methods** section offers different methods for you to use common developer tools to connect to a Hologres instance. You can select a connection method and a developer tool based on your business requirements. The following two connection methods are supported:

-   **Connect over JDBC**

    To connect to a Hologres instance over JDBC or by using an ETL tool or BI tool, use the following connection string:

    ```
    postgres://<AccessId>:<AccessKey>@<Endpoint>:<Port>/<database>? preferQueryMode=simple&tcpKeepAlive=true
    ```

    For more information about the configuration of JDBC, see [JDBC](/intl.en-US/Common Development Tools/JDBC.md).

-   **Connect from the PostgreSQL client**

    Hologres is compatible with PostgreSQL. You can connect to a Hologres instance from the PostgreSQL client. To connect to a Hologres instance from the PostgreSQL client, use the following connection string:

    ```
    PGUSER=<AccessId> PGPASSWORD=<AccessKey> psql -p <Port> -h <Endpoint> -d <Database>
    ```

    For more information, see [Use Hologres from the PostgreSQL client](/intl.en-US/Quick Start/Use Hologres from the PostgreSQL client.md).


The following table describes the parameters in the preceding connection strings.

|Parameter|Description|
|---------|-----------|
|<Access ID\>|The AccessKey ID of the current Alibaba Cloud account.You can obtain the AccessKey ID on the [AccessKey Management page](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) of the Alibaba Cloud Management Console. |
|<Access Key\>|The AccessKey secret of the current Alibaba Cloud account. You can obtain the AccessKey secret on the [AccessKey Management](https://usercenter.console.aliyun.com/?spm=5176.2020520153.nav-right.dak.3bcf415dCWGUBj#/manage/ak) page of the Alibaba Cloud Management Console. |
|<Port\>|The port number of the Hologres instance.You can obtain the port number in the **Network Configuration** section of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
|<Endpoint\>|The endpoint of the Hologres instance.You can obtain the endpoint in the **Network Configuration** section of the instance details page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). |
|<Database\>|The name of the database to which you want to connect.You can obtain the name of the database on the **Databases** page in the [Hologres console](https://hologram.console.aliyun.com/#/instance). If you do not have a database, use the **postgres** database that is allocated by the system. |

