---
keyword: [可视化分析, Qlik Sense Desktop]
---

# Qlik

Qlik 是经典的商业智能分析软件，其家族拥有Qlik Sense等多款BI软件。它使您能够快速开发和交付交互式指导分析应用程序和仪表板。本文为您介绍Qlik Sense Desktop如何连接 Hologres并可视化分析数据。

## 使用限制

Qilk暂不支持可视化显示Hologres的外部表，但是您可以在**数据加载编辑器**中通过SQL语句查询外部表并可视化分析。

## Qlik Sense Desktop连接Hologres

1.  安装Qlik Sense Desktop

    安装Qlik Sense Desktop，详请参见[Qlik官方文档](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Introduction/install-desktop.htm)。

2.  连接Hologres

    1.  打开Qlik Sense Desktop，在页面右上方单击**创建新应用程序**。

        ![创建应用程序](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246399.png)

    2.  在创建新应用程序弹框，为应用程序命名后，单击**创建**。

        ![创建](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246401.png)

    3.  在弹框中单击**打开应用程序**，在应用程序对应页面添加数据，单击**从文件和其他源添加数据**。

        ![打开应用程序](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246402.png)

    4.  在弹框中选择PostgreSQL创建新的连接，配置参数信息。

        ![postgres](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p242745.png)

        |参数|描述|
        |--|--|
        |Host name|Hologres实例的公共网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取网络地址。|
        |Port|Hologres的实例端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。|
        |Database|Hologres创建的数据库名称。|
        |User Name|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|

    5.  单击**测试连接**，如果提示`Connection succeeded`，则表示连接成功。您可以单击弹框右下角的**创建**，保存新的连接信息。

        ![保存连接信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246404.png)

    6.  配置PostgreSQL数据连接。

        ![public](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1569754161/p242772.png)

        -   选择目标Owner（即Hologres中的 schema），此处您可以选择public。
        -   在Tables区域选择需要分析的表。
    7.  单击下方的**添加数据**，添加数据执行完毕后，Qlik Sense会将数据从Hologres导入Qlik Sense，您可以在Qilk查看Hologres中的数据。

        **说明：** 使用该模式Qlik Sense会将数据全部加载到Qlik Sense的引擎中，并非根据页面操作实时发送查询到数据库。

3.  配置Direct Query模式

    在日常生产场景中，数据库会包含PB级数据，建议您在Qlik Sense中使用Direct Query模式，而不用将数据导入Qlik Sense，关于Direct Query模式的详细解释，请参见[Qlik官方文档](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Scripting/ScriptRegularStatements/direct-query.htm)。

    1.  打开之前创建的应用，在页面上方选择**数据管理器** \> **数据加载编辑器**。

        ![打开数据加载编辑器](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246406.png)

    2.  在页面右侧显示数据源连接信息，单击右下方的![插入](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6565314161/p242955.png)图标，将连接信息插入到编辑器中。

        ![插入连接字符串](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1569754161/p242954.png)

    3.  在脚本编辑器中查询脚本的最前方，输入`Direct Query`，即可启用 Direct Query 模式。样例数据图示和脚本如下所示：

        ![启用query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8453815161/p246408.png)

        ```
        SET ThousandSep=',';
        SET DecimalSep='.';
        SET MoneyThousandSep=',';
        SET MoneyDecimalSep='.';
        SET MoneyFormat='¥#,##0.00;-¥#,##0.00';
        SET TimeFormat='TTh:mm:ss';
        SET DateFormat='YYYY/M/D';
        SET TimestampFormat='YYYY/M/D TTh:mm:ss[.fff]';
        SET FirstWeekDay=6;
        SET BrokenWeeks=1;
        SET ReferenceDay=0;
        SET FirstMonthOfYear=1;
        SET CollationLocale='zh-CN';
        SET CreateSearchIndexOnReload=1;
        SET MonthNames='1月;2月;3月;4月;5月;6月;7月;8月;9月;10月;11月;12月';
        SET LongMonthNames='一月;二月;三月;四月;五月;六月;七月;八月;九月;十月;十一月;十二月';
        SET DayNames='周一;周二;周三;周四;周五;周六;周日';
        SET LongDayNames='星期一;星期二;星期三;星期四;星期五;星期六;星期日';
        SET NumericalAbbreviation='3:k;6:M;9:G;12:T;15:P;18:E;21:Z;24:Y;-3:m;-6:μ;-9:n;-12:p;-15:f;-18:a;-21:z;-24:y';
        
        LIB CONNECT TO 'PostgreSQL_hgpostcn-cn-st21y8nlm007-cn-hangzhou.hologres.aliyuncs.com';
        
        --输入 Direct Query，启用 Direct Query 模式
        DIRECT QUERY
        
        dimension
        l_shipmode,
        l_shipdate,
        o_orderstatus
        
        measure
        l_tax,
        l_quantity
        
        FROM public.lineitem INNER JOIN public.orders
        ON public.lineitem.l_orderkey = public.orders.o_orderkey;
        ```

    4.  单击窗口右上方的**加载数据**，Qlik Sense将发送即时查询。

    5.  在页面上方选择**工作表** \> **编辑工作表**，开始创建可视化。

        ![编辑工作表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9504815161/p246412.png)

    6.  在编辑工作表页面，您可以在页面左侧单击图表，选择需要的图表类型创建可视化。

        **说明：**

        -   直接拖动字段到画布，Qlik Sense无法生成Direct Query的查询，因此建议您单击图表创建可视化。
        -   鉴于Qlik Sense的处理逻辑，建议您先添加度量项，后添加维度项，以提高响应速度。
        ![编辑工作表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9504815161/p242974.png)

    更多关于Qlik Sense对数据操作和分析的介绍，请参见[Qlik官方文档](https://help.qlik.com/zh-CN/sense/November2020/Subsystems/Hub/Content/Sense_Hub/Introduction/install-desktop.htm)。


## 使用自定义ODBC连接Hologres

1.  安装PostgreSQL ODBC驱动

    您可以从PostgreSQL官方[下载PostgreSQL ODBC驱动](https://www.postgresql.org/ftp/odbc/versions/msi/)，请务必安装**psqlodbc\_11\_01\_0000**以上的版本。

2.  配置DSN

    1.  打开计算机的**控制面板**，在页面右上方将查看方式切换为**大图标**，选择**管理工具**。

    2.  双击打开**ODBC 数据源（64位）**管理工具，单击进入系统DSN页签。

        **说明：** 用户DSN只有特定的用户可以调用，而系统DSN对该系统的所有登录用户可用。如果用户需要在Web BI Server通过ODBC访问Hologres，应使用系统DSN。

    3.  单击**添加**，在创建新数据源弹框选择**PostgreSQL Unicode\(x64\)**。

    4.  单击**完成**，在弹框内完成如下参数配置。

        ![DSN配置](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0672514161/p243140.png)

        |参数|描述|
        |--|--|
        |Database|Hologres创建的数据库名称。|
        |Server|Hologres实例的公共网络地址。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取公共网络地址。|
        |Port|Hologres的实例端口。进入[Hologres管理控制台](https://hologram.console.aliyun.com/#/instance)的实例详情页，从**实例配置**获取端口。|
        |User Name|当前阿里云账号的AccessKey ID。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|
        |Password|当前阿里云账号的AccessKey Secret。获取方式请参见[创建访问密钥](/intl.zh-CN/准备工作/准备阿里云账号.md)。|

    5.  单击**Test**，如果页面提示`Connection successful`，则表示连接成功。您可以单击弹框右下角的**Save**，保存该DSN。

3.  连接Hologres

    1.  打开Qlik Sense Desktop，在页面选择一个应用程序并在对应页面添加数据，单击**从文件和其他源添加数据**。

        ![添加数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7236159161/p268935.png)

    2.  在弹框中选择ODBC创建新的连接。

    3.  从列表中选择此前创建的DSN，并填写连接的名称，如Hologres。单击**创建**，以保存新的连接信息。

        ![ODBC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7236159161/p268938.png)

    4.  配置ODBC数据连接。

        ![ODBC数据](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7236159161/p268948.png)

        -   选择目标Owner（即Hologres中的Schema），此处您可以选择public。使用该连接方式即可读取外部表的Schema。
        -   在Tables区域选择需要分析的表。
    5.  数据加载完毕之后，数据已导入至Qlik Sense，您可以开始创建报告。

        使用该模式Qlik Sense会将数据全部加载Qlik Sense 的引擎中，并非根据页面操作实时发送查询到数据库。


