---
keyword: [Hologres, development tool]
---

# Overview

This topic describes the common development tools that you can connect to Hologres.

Hologres is compatible with PostgreSQL and provides a Java Database Connectivity \(JDBC\) or Open Database Connectivity \(ODBC\) driver. You can connect your familiar development tools to Hologres for data development.

**Note:**

-   You can download a JDBC driver from [PostgreSQL JDBC Driver](https://jdbc.postgresql.org/download.html). Make sure that the JDBC driver that you use to connect to Hologres is JDBC version 42.2.18 or later.
-   You can download an ODBC driver from [File Browser](https://www.postgresql.org/ftp/pgadmin/pgadmin4/v5.0/macos/). Make sure that the ODBC driver that you use to connect to Hologres is ODBC version psqlodbc\_11\_01\_0000 or later.

The following table describes common development tools.

|Tool|Description and scenario|Documentation|
|----|------------------------|-------------|
|HoloWeb|Recommended.An all-in-one big data development platform that is developed based on Hologres.

The tool supports data development in a visualized manner and by using SQL statements. This way, this tool meets the requirements of developers with different development experiences and is applicable to various development operations in Hologres.

|[HoloWeb](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/Manage a connection.md)|
|psql|An interactive command-line client based on PostgreSQL.Based on an SQL statement execution page, this tool is more suitable for developers with basic SQL development ability.

|[PSQL quick start](/intl.en-US/Quick Start/PSQL quick start.md)|
|JDBC|A Java API that is used to execute SQL statements, which allows you to access multiple relational databases in a unified manner. The Java API consists of a set of classes and API operations that are written in Java.Based on an SQL statement execution page, this tool is more suitable for developers with JDBC development experiences.

|[JDBC](/intl.en-US/Common Development Tools/JDBC.md)|
|Navicat|A development tool that connects multiple databases.The tool supports data development in a visualized manner and by using SQL statements. This way, this tool is more applicable to scenarios in which services are highly dependent on Navicat.

|[Navicat](/intl.en-US/Common Development Tools/Navicat.md)|
|SQL Workbench/J|A free, cross-platform SQL query tool.Based on an SQL statement execution page, this tool is more suitable for developers with basic SQL development ability.

|[SQL Workbench/J](/intl.en-US/Common Development Tools/SQL Workbench/J.md)|

