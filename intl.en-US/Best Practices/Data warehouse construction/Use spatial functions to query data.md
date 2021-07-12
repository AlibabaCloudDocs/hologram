---
keyword: [Hologres, spatial function, PostGIS]
---

# Use spatial functions to query data

Hologres allows you to use PostGIS spatial functions to query spatial data in tables. This topic shows you how to import data from an on-premises machine to Hologres and then use PostGIS spatial functions to query data in Hologres.

-   A Hologres instance is purchased. For more information, see [Purchase a Hologres instance](/intl.en-US/Preparations/Purchase a Hologres instance.md).
-   A database is created in the Hologres instance. For more information, see [Create a database]().
-   The sample spatial data used in this topic is downloaded. To download the data, click the following links:
    -   [The accommodations table](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1624341461923/accommodations.csv)
    -   [The zipcodes table](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/207600/cn_zh/1624341479658/zipcode.csv)

The demo tables used in this topic contain various types of spatial data, such as longitudes, latitudes, coordinates, and distances. You can perform Step 1 and Step 2 to use HoloWeb to import the downloaded sample spatial data from your on-premises machine to the **accommodations** and **zipcodes** Hologres tables that you create. Then, you can perform Step 3 to use PostGIS spatial functions to query the spatial data in the two Hologres tables.

PostGIS: PostGIS is a spatial database extension for PostgreSQL databases. Hologres is compatible with the protocols of PostgreSQL 11. You can use PostGIS spatial functions in Hologres.

Spatial functions: For more information, see [t2086375.md\#section\_7i3\_sr4\_p4o]().

The Hologres tables to be created by using HoloWeb are used to store the following data:

-   The **accommodations** Hologres table stores the accommodation information of Berlin, such as the geographical location, including the longitude and latitude, and the name of each accommodation.
-   The **zipcodes** Hologres table stores the ZIP codes in Berlin.

## Procedure

|Step|Description|
|----|-----------|
|[Step 1: Create Hologres tables](#section_ewb_kzd_al2)|Create the following two tables in the database in your Hologres instance: 1. The **accommodations** Hologres table that is used to store the accommodation information of Berlin, such as the geographical location, including the longitude and latitude, and the name of each accommodation. 2. The **zipcodes** Hologres table that is used to store the ZIP codes in Berlin.|
|[Step 2: Import the sample spatial data](#section_a36_x7q_qfb)|Use HoloWeb to import spatial data from your on-premises machine to the **accommodations** and **zipcodes** Hologres tables.|
|[Step 3: Use spatial functions to query data](#section_1um_20j_p46)|Use PostGIS spatial functions to query the spatial data in the two Hologres tables.|

## Step 1: Create Hologres tables

Perform the following operations to create the **accommodations** and **zipcodes** Hologres tables.

1.  Log on to the HoloWeb console and go to the [SQL Editor](https://holoweb-cn-hongkong.data.aliyun.com/query) tab.

2.  Click **Ad-hoc Query**. Select your Hologres instance from the Instance drop-down list and the database where you want to create Hologres tables from the Database drop-down list.

    ![Ad-hoc Query](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2195706261/p284746.png)

3.  Install the PostGIS extension.

    Enter the following statement in the SQL editor and click **Run**:

    ```
    create extension if not exists postgis; -- Install the PostGIS extension.
    ```

4.  Create the **accommodations** Hologres table.

    Execute the following SQL statements to create the **accommodations** Hologres table. The table is used to store the accommodation information of Berlin, such as the geographical location, including the longitude and latitude, and the name of each accommodation.

    **Note:** In the **Table Directory** section, you can click the **Refresh** icon and choose **public** \> **Tables** to check whether the Hologres table is created. Alternatively, you can check the operational log in the **Run Log** section.

    ```
    CREATE TABLE public.accommodations (
      id INTEGER PRIMARY KEY,
      shape GEOMETRY,
      name VARCHAR(100),
      host_name VARCHAR(100),
      neighbourhood_group VARCHAR(100),
      neighbourhood VARCHAR(100),
      room_type VARCHAR(100),
      price SMALLINT,
      minimum_nights SMALLINT,
      number_of_reviews SMALLINT,
      last_review DATE,
      reviews_per_month NUMERIC(8,2),
      calculated_host_listings_count SMALLINT, 
      availability_365 SMALLINT
    );
    ```

5.  Create the **zipcodes** Hologres table.

    Execute the following SQL statements to create the **zipcodes** Hologres table. The table is used to store the ZIP codes in Berlin.

    **Note:** In the **Table Directory** section, you can click the **Refresh** icon and choose **public** \> **Tables** to check whether the Hologres table is created. Alternatively, you can check the operational log in the **Run Log** section.

    ```
    CREATE TABLE public.zipcode (
      ogc_field INTEGER PRIMARY KEY NOT NULL,
      wkb_geometry GEOMETRY,
      gml_id VARCHAR(256),
      spatial_name VARCHAR(256),
      spatial_alias VARCHAR(256),
      spatial_type VARCHAR(256)
     );
    ```


## Step 2: Import the sample spatial data

After the **accommodations** and **zipcodes** Hologres tables are created, import the downloaded sample spatial data from your on-premises machine to the two tables on the **Import On-premises File** page.

1.  In the [HoloWeb console](https://holoweb-cn-hongkong.data.aliyun.com/tool/perfence/query), click **System Management** in the top navigation bar.

2.  On the **System Management** tab, choose **Data Import** \> **Import On-premises File** in the left-side navigation pane. Then, click **New data import** on this page.

3.  Specify the Hologres table to which you want to import data.

    In the **Import On-premises File** dialog box, enter a job name, select your Hologres instance, the created database, and a created Hologres table \(**accommodations** or **zipcodes**\), and then click **Next Step**.

    ![Specify the Hologres table to which you want to import data ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2195706261/p284747.png)

4.  Specify the data to be imported and the encoding format.

    In the **Select File** step, set the parameters as described in the following table and click Next Step.

    ![Data to be imported](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2195706261/p284748.png)

    |Parameter|Description|
    |---------|-----------|
    |Select File|The file that contains the data to be imported. Click **Browse...** and select a file from your on-premises machine. .txt, .csv, and .log files are supported. In this example, select the **accommodations** or **zipcodes** table that you downloaded.|
    |Delimiter|The delimiter used to separate data entries. In this example, select SEMICOLON. **Note:** You can also select the option to the right of the drop-down list and specify a custom delimiter based on your business requirements. |
    |Character Encoding|The encoding format of the data. In this example, select **UTF-8**.|
    |First Line as Header|Specifies whether to set the first line of the data as the header of the Hologres table. By default, this option is not selected.|

5.  Confirm the configurations.

    In the Import Overview step, check whether the configurations of the data import job are as expected and click **Execution**.

    ![Confirm the configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2195706261/p284749.png)

6.  Verify the execution result.

    After the job is complete, the system shows whether the execution is successful in the Import Overview step. If the execution fails, you can view the error cause for troubleshooting and import the data again.

    You can also execute one of the following SQL statements in the SQL editor to query the number of data entries or detailed data in the destination Hologres table:

    -   Query the number of data entries

        In this example, the **accommodations** Hologres table contains 22,248 data entries, and the **zipcodes** Hologres table contains 190 data entries.

        ```
        select count(*) from accommodations; -- Query the number of data entries in the accommodations Hologres table.
        select count(*) from zipcodes; -- Query the number of data entries in the zipcodes Hologres table.
        ```

    -   Query the detailed data

        ```
        select * from accommodations; -- Query the detailed data in the accommodations Hologres table.
        select * from zipcodes; -- Query the detailed data in the zipcodes Hologres table.
        ```


## Step 3: Use spatial functions to query data

After the required Hologres tables are created and the sample spatial data is imported to the tables, you can use spatial functions to query the spatial data in Hologres. The following examples are for your reference. For information about the syntax of spatial functions, see [t2086375.md\#section\_7i3\_sr4\_p4o]().

-   Query the number of data entries in the **accommodations** Hologres table with the spatial reference system identifier \(SRID\) set to 4326.
    -   Sample code:

        ```
        SELECT count(*) FROM public.accommodations WHERE ST_SRID(shape) = 4326;
        ```

    -   Return results:

        ```
         count 
        -------
         22248
        (1 row)
        ```

-   Use the well-known text \(WKT\) format to query geometry objects that meet the specified conditions. In this example, you can check whether the ZIP codes in the zipcodes Hologres table are stored in World Geodetic System 1984 \(WGS84\). The system uses an SRID of 4326.

    **Note:** Only the spatial data entries that are in the same spatial reference system can be referenced by each other.

    -   Sample code:

        ```
        SELECT  ogc_field
                ,spatial_name
                ,spatial_type
                ,ST_SRID(wkb_geometry)
                ,ST_AsText(wkb_geometry)
        FROM    public.zipcode
        ORDER BY spatial_name
        ;
        ```

    -   Return results:

        ```
        ogc_field  spatial_name  spatial_type  st_srid  st_astext
        ---------------------------------------------------------------
        0           10115        Polygon        4326     POLYGON((...))
        4           10117        Polygon        4326     POLYGON((...))
        8           10119        Polygon        4326     POLYGON((...))
        ...
        (190 rows returned)
        ```

-   Use the GeoJSON format to query the surface, the surface size, and the number of points on the surface for Mitte in Berlin with the SRID set to 10117.
    -   Sample code:

        ```
        SELECT  ogc_field
                ,spatial_name
                ,ST_AsGeoJSON(wkb_geometry)
                ,ST_Dimension(wkb_geometry)
                ,ST_NPoints(wkb_geometry)
        FROM    public.zipcode
        WHERE   spatial_name = '10117'
        ;
        ```

    -   Return results:

        ```
        ogc_field  spatial_name  spatial_type                                   st_dimension  st_npoint
        -----------------------------------------------------------------------------------------------
        4           10117         {"type":"Polygon", "coordinates":[[[...]]]}    2             331
        ```

-   Query the number of accommodations within 500 meters of the Brandenburg Gate with the SRID set to 4326.
    -   Sample code:

        ```
        SELECT  COUNT(*)
        FROM    public.accommodations
        WHERE   ST_DistanceSphere(shape, ST_GeomFromText('POINT(13.377704 52.516431)', 4326)) < 500
        ;
        ```

    -   Return results:

        ```
         count 
        -------
            29
        (1 row)
        ```

-   Perform a rough estimate of the location of the Brandenburg Gate based on the information about nearby accommodations.
    -   Sample code:

        ```
        WITH
            poi(loc) AS ( 
                SELECT st_astext(shape) 
                FROM accommodations 
                WHERE name LIKE '%brandenburg gate%' )
        SELECT  COUNT(*)
        FROM    accommodations a
                ,poi p
        WHERE   ST_DistanceSphere(a.shape, ST_GeomFromText(p.loc, 4326)) < 500
        ;
        ```

    -   Return results:

        ```
         count 
        -------
            60
        (1 row)
        ```

-   Query the detailed information about all the accommodations around the Brandenburg Gate and sort the accommodations in descending order by price.
    -   Sample code:

        ```
        SELECT  name
                ,price
                ,ST_AsText(shape)
        FROM    public.accommodations
        WHERE   ST_DistanceSphere(shape, ST_GeomFromText('POINT(13.377704 52.516431)', 4326)) < 500
        ORDER BY price DESC
        ;
        ```

    -   Return results:

        ```
                                name                        | price |                st_astext                 
        ----------------------------------------------------+-------+------------------------------------------
         DUPLEX APARTMENT/PENTHOUSE in 5* LOCATION! 7583    |   300 | POINT(13.3826510209548 52.5159819722552)
         DUPLEX-PENTHOUSE IN FIRST LOCATION! 7582           |   300 | POINT(13.3799997083855 52.5135918444834)
         Luxury Apartment in Berlin Mitte with View         |   259 | POINT(13.3835653528534 52.516360156825)
         BIG APT 4 BLNCTY-CNTR 43-H6                        |   240 | POINT(13.3800222998777 52.5134224506894)
         BIG APARTMENT-PRIME LOCATION-BEST PRICE! B0303     |   240 | POINT(13.379745196599 52.5162648947249)
         BIG APARTMENT IN BRILLIANT LOCATION-CTY CENTRE B53 |   240 | POINT(13.381383105167 52.5157082721072)
         SONYCENTER: lux apartment - 3room/2bath. WIFI      |   235 | POINT(13.3743158954191 52.5125308432819)
         CENTRE APARTMENT FOR 6 | 8853                      |   220 | POINT(13.3819039478615 52.5134866767369)
         BIG APARTMENT FOR 6 - BEST LOCATION 8863           |   209 | POINT(13.3830430841658 52.5147824286783)
         3 ROOMS ONE AMAZING EXPERIENCE! 8762               |   190 | POINT(13.3819898503053 52.5144190764637)
         AAA LOCATION IN THE CENTRE H681                    |   170 | POINT(13.3821787206534 52.5129769242004)
         H672 Nice Apartment in CENTRAL LOCATION!           |   170 | POINT(13.3803137710339 52.5132386929089)
         "Best View -best location!"                        |   170 | POINT(13.3799551247135 52.5147888483851)
         H652 Best Location for 4!                          |   170 | POINT(13.3805705422409 52.5143845784482)
         H651 FIT´s for Four in a 5* Location!              |   150 | POINT(13.3822063502184 52.5134994650996)
         NEXT TO ATTRACTIONS! H252                          |   110 | POINT(13.3823616629115 52.5136258446666)
         CTY Centre Students Home| G4                       |   101 | POINT(13.3808081476226 52.5130957830586)
         Room for two with private shower / WC              |    99 | POINT(13.3786877948382 52.5208018292043)
         StudentsHome CityCentre Mitte 91-0703              |    95 | POINT(13.3810390515141 52.5142363781923)
         FIRST LOCATION - FAIR PRICE K621                   |    80 | POINT(13.3823909855061 52.5131554670458)
         LONG STAY FOR EXPATS/STUDENTS- CITY CENTRE | K921  |    75 | POINT(13.380320945399 52.512364557598)
         Nice4Students! City Centre 8732                    |    68 | POINT(13.3810147526683 52.5136623602892)
         Comfy Room in the heart of Berlin                  |    59 | POINT(13.3813167311819 52.5127345388756)
         FO(U)R STUDENTS HOME-Best centre Location!         |    57 | POINT(13.380850032042 52.5131726958513)
         Berlin Center Brandenburg Gate !!!                 |    55 | POINT(13.3849641540689 52.5163902851474)
         !!! BERLIN CENTER BRANDENBURG GATE                 |    55 | POINT(13.379997730927 52.5127577639174)
         Superb Double Bedroom in Central Berlin            |    52 | POINT(13.3792991992688 52.5156572293422)
         OMG! That's so Berlin!                            |    49 | POINT(13.3754883007165 52.5153487677272)
         Apartment in Berlin's old city center              |    49 | POINT(13.3821761577766 52.514037240604)
        (29 rows)
        ```

-   Query the detailed information about the accommodation with the highest price and its ZIP code.
    -   Sample code:

        ```
        SELECT  a.price
                ,a.name
                ,ST_AsText(a.shape)
                ,z.spatial_name
                ,ST_AsText(z.wkb_geometry)
        FROM    accommodations a
                ,zipcode z
        WHERE   price = 9000
        AND     ST_Within(a.shape, z.wkb_geometry)
        ;
        ```

    -   Return results:

        ```
        price   name                                 st_astext                                  spatial_name      st_astext
        -------------------------------------------------------------------------------------------------------------------------------------------------
        9000    Ueber den Dächern Berlins Zentrum    POINT(13.334436985013 52.4979779501538)    10777             POLYGON((13.3318284987227 52.4956021172799,...
        ```

-   Query the popular accommodations in Berlin, group the accommodations by ZIP code, and then sort the groups by the order volume.
    -   Sample code:

        ```
        SELECT  z.spatial_name AS zip
                ,COUNT(*) AS numAccommodations
        FROM    public.accommodations a
                ,public.zipcode z
        WHERE   ST_Within(a.shape, z.wkb_geometry)
        GROUP BY zip
        ORDER BY numAccommodations DESC
        ;
        ```

    -   Return results:

        ```
        zip      numaccommodations
        ----------------------------
        10245    872
        10247    832
        10437    733
        10115    664
        ...
        (187 rows returned)
        ```


