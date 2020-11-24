# Constraints

This topic describes the constraints on using Hologres.

## Constraints on directly querying data in MaxCompute projects

-   Hologres can scan a maximum of 50 partitions from a partitioned table during a single query.
-   Currently, you cannot create or use views in Hologres.
-   Currently, Hologres does not support the following data types: MAP, ARRAY, TINYINT, BINARY, and TIMESTAMP. These data types will be supported in the near future.
-   Hologres can scan a maximum of 100 GB data from a table during a single query.
-   The size of a query statement that you commit cannot exceed 100 KB.
-   The query timeout interval is 1 hour.
-   Hologres is compatible with PostgreSQL, but PostgreSQL does not support the DATETIME data type. When querying data of the DATETIME type in MaxCompute projects, Hologres converts the data to the TIMESTAMPTZ type that PostgreSQL supports.

## Constraints on UDFs

-   In the current Hologres version, you cannot use user-defined functions \(UDFs\) created in MaxCompute.
-   In the current Hologres version, you cannot create or use PostgreSQL UDFs. However, you can use the built-in functions of PostgreSQL in Hologres.
-   In the current Hologres version, you cannot use the built-in functions of MaxCompute.

## Constraints on the number of connections

A single Hologres instance supports a maximum of 100 connections.

## Constraints on data types

The data types that the current Hologres version supports are only a subset of data types in PostgreSQL. You cannot create or query a foreign table that contains the default field in Hologres.

## Constraints on using HoloStudio

Only one endpoint is generated for a Hologres instance that you have purchased. If you use HoloStudio for data analytics, you can bind a database of the Hologres instance to only one workspace. To bind a database to another workspace, you must purchase another Hologres instance on the Alibaba Cloud official website. Suppose that you have bound the database named postgres of instance test1 to Workspace 1. You cannot bind any databases of instance test1 to Workspace 2. You must purchase a new Hologres instance and bind a database of the new instance to Workspace 2.

