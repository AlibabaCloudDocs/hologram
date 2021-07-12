---
keyword: [Hologres, PostGIS]
---

# PostGIS \(Beta）

PostGIS是数据库PostgreSQL的空间扩展，PostGIS可以提供空间对象、空间索引、空间操作函数和空间操作符等空间信息服务功能。本文为您介绍PostGIS在Hologres中的使用方法。

## 使用限制

在Hologres中使用PostGIS，目前该功能为Beta版本，在使用过程中不能100%保证其稳定性。目前也暂不支持PostGIS索引。如果您在使用过程中有任何问题，请您[提交工单](https://workorder-intl.console.aliyun.com/)进行反馈。

## 安装PostGIS

在使用PostGIS之前，需要Superuser在DB内执行以下语句安装扩展包才可以正常使用。一个DB只需执行一次即可，如果创建新的DB，还需要再次执行如下语句。

```
create extension if not exists postgis;-- 加载PostGIS插件
```

在安装完成后，您可以执行如下SQL语句查看当前PostGIS版本。

```
select postgis_full_version();
```

## 创建并查询包含空间数据类型的表

目前在Hologres中使用PostGIS，支持两类空间数据类型，即几何类型（Geometry Type）和地理类型（Geography Type）。

在实际使用过程中，几何类型（Geometry Type）是使用较频繁的类型，如下内容将以几何类型为例，指导您创建包含Geometry数据类型的表并进行典型空间查询。更多关于地理类型（Geography Type）的参数和使用说明，请参见[PostGIS Geography Type](https://postgis.net/docs/postgis_usage.html#PostGIS_Geography)。

1.  **创建包含Geometry数据类型的表**

    **说明：** 创建包含几何类型的表时，您可以指定创建的几何体类型。包括Point、MultiPoint、Linestring、MultiLinestring、Polygon、MultiPolygon等。

    -   创建不指定几何类型的表。

        ```
        CREATE TABLE holo_gis_1 ( id int, geom geometry, PRIMARY KEY (id)) ;
        ```

        以上示例中，创建了包含几何类型的表，但是该表中不指定具体的几何类型。

    -   创建指定几何体类型和SRID的表。

        ```
        CREATE TABLE holo_gis_2 ( id int, geom geometry(point, 4326), PRIMARY KEY (id)) ;
        ```

        以上示例中，Geometry类型指定为Point，SRID为4326，SRID不指定默认为0。更多关于SRID释义，请参见[PostGIS官方文档](https://postgis.net/docs/manual-dev/using_postgis_dbmanagement.html)。

2.  **向表中插入数据**

    您可以通过如下方式向表中插入数据，其中，关于空间函数的使用说明，请参见[空间函数](#section_7i3_sr4_p4o)。关于SRID释义，请参见[PostGIS官方文档](https://postgis.net/docs/manual-dev/using_postgis_dbmanagement.html)。

    -   不指定SRID值。

        ```
        insert into holo_gis_1 values (1, ST_GeomFromText('point(116 39)'));
        ```

    -   指定SRID值。

        ```
        insert into holo_gis_2 values (1, ST_GeomFromText('point(116 39)', 4326));
        ```

3.  **执行数据查询**

    您可以在创建表并插入数据后，执行矩形范围查询和多边形相交判定两种典型场景的查询。如下示例中，更多关于空间函数的使用说明，请参见[空间函数](#section_7i3_sr4_p4o)。

    -   矩形范围查询
        -   不指定SRID值。

            ```
            select st_astext(geom) from holo_gis_1
            where ST_Contains(ST_MakeBox2D(ST_Point(116, 39),ST_Point(117, 40)), geom);
            ```

        -   指定SRID值。

            ```
            select st_astext(geom) from holo_gis_2 
            where ST_Contains(ST_SetSRID(ST_MakeBox2D(ST_Point(116, 39),ST_Point(117, 40)), 4326), geom);
            ```

    -   多边形相交判定（在内部或在边界上）
        -   不指定SRID值。

            ```
            select st_astext(geom) from holo_gis_1
            where ST_Contains(ST_MakeBox2D(ST_Point(116, 39),ST_Point(117, 40)), geom);
            ```

        -   指定SRID值。

            ```
            select st_astext(geom) from holo_gis_2 
            where ST_Contains(ST_SetSRID(ST_MakeBox2D(ST_Point(116, 39),ST_Point(117, 40)), 4326), geom);
            ```


## 空间函数

PostGIS为您提供了一些空间函数， 每种函数可以将一种数据类型的值转换为另一种类型。空间函数说明具体见下表，关于函数语法，部分参数说明如下：

-   geom：表格中涉及的geom均表示一个GEOMETRY类型的值，或一个计算结果为GEOMETRY类型的表达式。
-   precision：表格中涉及的precision均表示一个INTEGER类型的值。坐标系geom将使用指定的精度1~20显示。如果未指定精度，则默认值为15。
-   index：表格中涉及的index为索引，均表示一个INTEGER类型的值。
-   srid：表格中涉及srid均表示一个INTEGER类型的值，它是空间参考标识符SRID。

更多关于空间函数的说明，请参见[PostGIS官方文档](https://postgis.net/docs/reference.html#Management_Functions)。

|函数|函数语法|返回类型|说明|
|--|----|----|--|
|GeometryType|GeometryType\(geom\)|VARCHAR|GeometryType以字符串形式返回输入几何体的子类型。例如，geom为POINT子类型时返回的字符串为POINT。|
|ST\_AddPoint|ST\_AddPoint\(geom1, geom2\)geom1子类型必须为LINESTRING，geom2子类型必须是POINT。

|GEOMETRY|ST\_AddPoint将一个坐标点添加到LineString之中。|
|ST\_Angle|ST\_Angle\(geom1, geom2, geom3\)ST\_Angle\(geom1, geom2, geom3, geom4\)

geom子类型必须是POINT。

|DOUBLE|ST\_Angle返回顺时针方向测量的点之间的角度（返回值以弧度为单位且在 \[0, 2π\) 范围内）。例如：-   如果输入三个点，则测量P1P2P3三个点顺时针组成的角度。
-   如果输入四个点，则测量有向线P1P2和P3P4顺时针形成的角度。如果输入为两条线平行（即，P1等于P2，或P3等于P4），则返回 null。 |
|ST\_Area|ST\_Area\(geom\)|DOUBLE|ST\_Area返回多边形几何体的笛卡尔面积。-   对于点、线串、多点和多线串，返回0。
-   对于几何体集合，返回集合中几何体的面积之和。 |
|ST\_AsBinary|ST\_AsBinary\(geom\)|BYTEA|ST\_AsBinary使用ASCII十六进制字符（0~9，A~F）返回几何体的WKB（Well-Known Binary）表示形式，不包含SRID数据。|
|ST\_AsEWKB|ST\_AsEWKB\(geom\)|BYTEA|ST\_AsEWKB使用ASCII十六进制字符（0~9，A~F）返回几何体的EWKB（Well-Known Binary）表示形式，包含SRID数据。|
|ST\_AsEWKT|ST\_AsEWKT\(geom\)|VARCHAR|ST\_AsEWKT返回几何体的EWKT（Well-Known Text）表示形式，包含SRID数据。|
|ST\_AsGeoJSON|ST\_AsGeoJSON\(geom\)ST\_AsGeoJSON\(geom, precision\)

|VARCHAR|ST\_AsGeoJSON返回几何体的GeoJSON 表示形式。|
|ST\_AsText|ST\_AsText\(geom\)ST\_AsText\(geom, precision\)

|VARCHAR|ST\_AsText返回几何体的WKT（Well-Known Text）表示形式，不包含SRID数据。|
|ST\_Azimuth|ST\_Azimuth\(point1, point2\)point1和point2为GEOMETRY类型的值。其两者的空间参考系统标识SRID必须相互匹配。

|DOUBLE|ST\_Azimuth返回两个输入点的基于北向的笛卡尔方位。方位角以北为参考方向，顺时针为正，例如北为0，则东为π/2、南为π。如果两点重合则返回null。|
|ST\_Bilands|ST\_Boundary\(geom\)|GEOMETRY|ST\_Bilands返回输入几何体的边界。-   如果输入几何为空（即不包含点），则按原样返回。
-   如果输入几何是点或非空多点，则返回空几何集合。
-   如果输入是线串或多线串，则返回包含边界上所有点的多点（多点可能为空）。
-   如果输入是一个没有任何内环的面，则返回一个表示其边界的闭合线串。
-   如果输入是具有内环的面，或者是多面，则返回多线串。多线串包含面积几何中所有环的所有边界作为闭合线串。 |
|ST\_Buffer|ST\_Buffer\(geography,float8\)|GEOMETRY|用于指定与围绕其计算缓冲区的几何图形实例的距离。返回一个几何图形，该几何图形覆盖与输入参数geography的距离小于等于给定值的所有点。|
|ST\_Contains|ST\_Contains\(geom1, geom2\)|BOOLEAN|如果第一个输入几何体包含第二个输入几何体，则ST\_Contains返回true。如果B中的每个点均为A中的一个点，并且其内部有非空相交区域，则几何体A包含几何体B。ST\_Contains\(A, B\)与ST\_Within\(B, A\)等效。|
|ST\_ContainsFeorth|ST\_ContainsProperly\(geom1, geom2\)geom的子类型不能是GEOMETRYCOLLECTION。

|BOOLEAN|如果两个输入几何体都是非空的，并且第二个几何体的所有点都是第一个几何的内部点，则 ST\_ContainsFeorth返回true。|
|ST\_Convexhull|ST\_ConvexHull\(geom\)|GEOMETRY|ST\_convexhull返回一个几何体，该几何体表示输入几何体中包含的非空点的凸壳。|
|ST\_CoveredBy|ST\_CoveredBy\(geom1, geom2\)|BOOLEAN|如果第一个输入几何体被第二个输入几何体覆盖，则ST\_CoveredBy返回true。即，如果几何体A和几何体B都是非空的，并且A的每个点均为B中的一个点，则前者被后者覆盖。ST\_CoveredBy\(A, B\)与 ST\_Covers\(B, A\)等效。 |
|ST\_Covers|ST\_Covers\(geom1, geom2\)|BOOLEAN|如果第一个输入几何体被第二个输入几何体覆盖，则ST\_Covers返回 true。即，如果几何体A和几何体B都是非空的，并且B的每个点均为A中的一个点，则前者覆盖了后者。ST\_Covers\(A, B\)与ST\_CoveredBy\(B, A\) 等效。 |
|ST\_Crosses|ST\_Crosses\(geom1, geom2\)|BOOLEAN|如果两个输入几何体部分交叉，则ST\_Crosses返回true。|
|ST\_Dimension|ST\_Dimension\(geom\)|INTEGER|ST\_Dimension返回输入几何体的固有维度。固有维度是几何体中定义的子类型的维度值。|
|ST\_Disjoint|ST\_Disjoint\(geom1, geom2\)|BOOLEAN|如果两个输入几何体没有共同点（不相交），则ST\_Disjoint返回true。|
|ST\_Distance|ST\_Distance\(geom1, geom2\)|DOUBLE|ST\_Distance返回两个输入几何体之间的欧氏距离。|
|ST\_DWithin|ST\_DWithin\(geom1, geom2, threshold\)|BOOLEAN|如果两个输入几何体值之间的欧氏距离在给定的阈值内，则ST\_DWithin返回true。|
|ST\_Envelope|ST\_Envelope\(geom\)|GEOMETRY|ST\_Envelope返回输入几何体的最小边界框。-   如果输入几何体为空，则返回的几何体也为空。
-   如果输入几何体的最小边界框退化为一个点，则返回的几何体是一个点。
-   如果输入几何体的最小边界框是一维的，则返回两点线串。
-   如果上述条件都不成立，则函数将返回一个顺时针方向的多边形，其顶点为最小边界框的角。返回几何体的SRID与输入几何体的相同。 |
|ST\_Equals|ST\_Equals\(geom1, geom2\)|BOOLEAN|如果两个输入几何体在几何上相等，则ST\_Equals 返回true。即，如果几何体具有相等的点集且其内部具有非空相交区域，则将几何体视为在几何上相等。|
|ST\_ExteriorRing|ST\_ExteriorRing\(geom\)|子类型LINESTRING的GEOMETRY。|ST\_ExteriorRing返回一个表示多边形几何体外环的闭合线串。如果输入不是多边形，则返回null。|
|ST\_GeometryN|ST\_GeometryN\(geom, index\)|GEOMETRY|ST\_GeometryN返回由输入几何体的输入索引指向的几何体。-   当输入是点、线串或多边形时，如果索引等于1，则按原样返回几何体；如果索引不是1，则返回 null。
-   如果输入是多点、多线串、多边形或几何体集合，则返回由输入索引（从1开始）指向的点、线串、多边形或几何体集合。返回几何体的SRID与输入几何体的相同。 |
|ST\_GeometryType|ST\_GeometryType\(geom\)|VARCHAR|ST\_GeometryType以字符串形式返回输入几何体的子类型。例如，当geom为POINT子类型时返回字符串ST\_Point。|
|ST\_GeomFromText|ST\_GeomFromText\(wkt\_string\)ST\_GeomFromText\(wkt\_string, srid\)

wkt\_string为VARCHAR数据类型的值，它是几何体的WKT表示形式。

|GEOMETRY|ST\_GeomFromText从输入几何体的WKT（Well-Known Text）表示形式构造几何体对象。该函数有两种形式：-   第一种不包含SRID，并返回SRID=0的几何图形。
-   第二种采用SRID作为第二个参数，并返回一个几何图形，该几何图形将该SRID作为元数据的一部分。 |
|ST\_teriorRingn|ST\_InteriorRingN\(geom, index\)|子类型 LINESTRING 的 GEOMETRY。|ST\_teriorRingn返回与索引位置处输入多边形的内环相对应的闭合线串。|
|ST\_Intersects|ST\_Intersects\(geom1, geom2\)|BOOLEAN|如果两个输入几何体至少有一个共同点，则ST\_Intersects返回true。|
|ST\_Intersection|ST\_Intersection\(geom1, geom2\)|GEOMETRY|返回两个输入几何体之间的交集。|
|ST\_IspolyGonCW|ST\_IsPolygonCW\(geom\)|BOOLEAN|测试多边形是否具有顺时针方向的外环和逆时针方向的内环。-   如果输入多边形是逆时针的，则ST\_IspolyGonCW返回true。
-   如果输入几何是点、线串、多点或多线串，则返回true。
-   对于几何体集合，如果集合中的所有几何体均为逆时针方向，ST\_IspolygonCW将返回true。 |
|ST\_IsClosed|ST\_IsClosed\(geom\)|BOOLEAN|如果输入几何体已闭合，则ST\_IsClosed返回 true。如下内容定义闭合的几何体：-   输入的几何体是一个点或一个多点。
-   输入几何体是一个线串，并且该线串的起点和终点是重合的。
-   输入几何体是一个非空的多线串，并且其所有线串均已闭合。
-   输入几何体是一个非空多边形，所有多边形的环都是非空的，并且所有环的起点和终点都是重合的。
-   输入几何体是一个非空的多边形集合，并且其所有多边形均已闭合。
-   输入几何体是一个非空几何体集合，并且其所有组件均已闭合。 |
|ST\_IsCollection|ST\_IsCollection\(geom\)|BOOLEAN|如果输入几何体为下列子类型之一，则ST\_IsCollection返回true：-   GEOMETRYCOLLECTION
-   MULTIPOINT
-   MULTILINESTRING
-   MULTIPOLYGON |
|ST\_IsEmpty|ST\_IsEmpty\(geom\)|BOOLEAN|如果输入几何体为空，则ST\_IsEmpty返回true。|
|ST\_IsSimple|ST\_IsSimple\(geom\)|BOOLEAN|如果输入几何体没有异常几何点，如自交或自切线，则ST\_IsSimple返回true。|
|ST\_IsValid|ST\_IsValid\(geom\)|BOOLEAN|如果输入几何体有效（结构稳定），则ST\_IsVALID返回 true。|
|ST\_Length|ST\_Length\(geom\)|DOUBLE|ST\_Length返回输入线性几何体的笛卡尔长度。长度单位与用于表示输入几何体坐标的单位相同。-   对于点、多点和平面几何体，此函数返回零。
-   当输入为几何体集合时，此函数返回集合中的几何体长度之和。 |
|ST\_LineFromMultiPoint|ST\_LineFromMultiPoint\(geom\)|GEOMETRY|ST\_LineFromMultiPoint返回输入多点几何体中的线串。点的顺序将保留。返回几何体的SRID与输入几何体的相同。|
|ST\_LineInterpolatePoint|ST\_LineInterpolatePoint\(geom, fraction\)geom子类型必须为LINESTRING，fraction是一个DOUBLE数据类型的值，介于0和1之间。

|子类型POINT的GEOMETRY。|ST\_LineInterpolatePoint为插值点，即返回一个距离线起点为小数距离的点。|
|ST\_MakeEnvelope|ST\_MakeEnvelope\(xmin, ymin, xmax, ymax\)ST\_MakeEnvelope\(xmin, ymin, xmax, ymax, srid\)

x和y均为一个DOUBLE类型的值。

|GEOMETRY子类型的EMR群集POINT、LINESTRING或者POLYGON。|从X和y的最小值和最大值创建一个矩形多边形。-   如果输入坐标指定一个点，则返回的几何体是一个点。
-   如果输入坐标指定一条线，则返回的几何为线串。
-   如果输入坐标指定框的左下角和右上角，则返回的几何为面。

输入的值必须在SRID指定的空间参考系统中，如果没有指定SRID，则SRID默认为0。|
|ST\_MakeLine|ST\_MakeLine\(geom1, geom2\)|子类型 LINESTRING的GEOMETRY。|ST\_MakeLine从输入几何体创建线串。|
|ST\_MakePoint|ST\_MakePoint\(x, y\)|子类型 POINT的GEOMETRY。|ST\_MakePoint返回其坐标值为输入值的点。|
|ST\_Multi|ST\_Multi\(geom\)|GEOMETRY带子类型的MULTIPOINT、MULTILINESTRING、MULTIPOLYGON，或者GEOMETRYCOLLECTION。|ST\_multi将几何体转换为相应的多类型。-   如果输入几何已经是多类型或几何集合，则其返回值不变。
-   如果输入几何体是点、线串或多边形，则分别返回包含输入几何体的多点、多线串或多边形。 |
|ST\_NPoints|ST\_NPoints\(geom\)|INTEGER|ST\_NPoints返回输入几何体中点的数量。|
|ST\_NRings|ST\_NRings\(geom\)|INTEGER|ST\_NRings返回输入几何体中环的数量。|
|ST\_NumGeometries|ST\_NumGeometries\(geom\)|INTEGER|ST\_NumGeometries返回输入几何体集合中的几何体数量。|
|ST\_NumInteriorRings|ST\_NumInteriorRings\(geom\)|INTEGER|ST\_NumInteriorRings返回输入多边形几何体中的环的数量。|
|ST\_NumPoints|ST\_NumPoints\(geom\)|INTEGER|ST\_NumPoints返回LineString或CircularString中点的数量。|
|ST\_Perimeter|ST\_Perimeter\(geom\)|DOUBLE|ST\_Perimeter返回输入平面几何体的笛卡尔周长（边界长度）。周长单位与用于表示输入几何体坐标的单位相同。-   当输入为点、多点和线性几何体，此函数返回0。
-   当输入为几何体集合时，此函数返回集合中的几何体周长之和。 |
|ST\_Point|ST\_Point\(x, y\)|子类型 POINT的GEOMETRY。|ST\_Point从输入坐标值返回点。|
|ST\_PointN|ST\_PointN\(geom, index\)|子类型 POINT的GEOMETRY。|ST\_PointN返回由索引值指定的线串中的点。负索引值从线串的末尾开始倒计数，因此-1是最后一个点。|
|ST\_Points|ST\_Points\(geom\)|子类型 MULTIPOINT的GEOMETRY。|ST\_Points返回包含输入几何体中所有非空点的多点。ST\_Points不会移除输入中重复的点，包括环形几何的起点和终点。|
|ST\_Polygon|ST\_Polygon\(linestring, srid\)linestring为一个GEOMETRY类型的值，或一个计算结果为GEOMETRY类型的表达式。子类型必须是表示线串的LINESTRING。

|子类型 POLYGON的GEOMETRY。|ST\_Polygon返回一个多边形，其外部环形是输入线串，其值是SRID的输入值。|
|ST\_RemovePoint|ST\_RemovePoint\(geom, index\)|GEOMETRY|ST\_RemovePoint返回一个线串，该几何体已删除输入几何体在索引位置的点。索引是从零开始的。返回线串的SRID与输入几何体的相同。|
|ST\_Reverse|ST\_Reverse\(geom\)|GEOMETRY|ST\_Revert可反转几何体的顶点顺序。-   对于点或多点，则其返回值不变。
-   对于几何体集合，ST\_Revert将反转集合中每个几何体的顶点顺序。 |
|ST\_setPoint|ST\_SetPoint\(geom1, index, geom2\)|GEOMETRY|用给定的点替换索引指定的输入线串中的点。|
|ST\_SetSRID|ST\_SetSRID\(geom, srid\)|GEOMETRY|ST\_SetSRID返回一个与输入几何体相同的几何体，但是其SRID值进行了更新。|
|ST\_Simplify|ST\_Simplify\(geom, tolerance\)tolerance为一个DOUBLE类型的值，表示Ramer-Douglas-Peucker算法的容差水平。如果公差是负数，则取值为零。

|GEOMETRY|ST\_Simplify使用Ramer-Douglas-Peucker算法返回输入几何体的简化版本。输入几何的拓扑可能不会保留。|
|ST\_SRID|ST\_SRID\(geom\)|INTEGER|ST\_SRID返回输入几何体的SRID值。|
|ST\_StartPoint|ST\_StartPoint\(geom\)|GEOMETRY|ST\_StartPoint返回输入线串的第一个点。返回几何体的SRID值与输入几何体相同。|
|ST\_Touches|ST\_Touches\(geom1, geom2\)|BOOLEAN|如果两个输入几何体接触，则 ST\_Touches返回true。即，如果两个几何体是非空的、相交并且没有共同的内部点，则它们是接触的。|
|ST\_Within|ST\_Within\(geom1, geom2\)|BOOLEAN|如果第一个输入几何体在第二个输入几何体中，则ST\_Within返回true。例如，如果几何体A中的每个点均为几何体B中的一个点，并且其内部有非空相交区域，则几何体A在几何体B中。ST\_Within\(A, B\)与ST\_Contains\(B, A\) 等效。|
|ST\_X|ST\_X\(point\)point为GEOMETRY数据类型的值。

|DOUBLE|ST\_X返回输入点的X坐标。|
|ST\_XMax|ST\_XMax\(geom\)|DOUBLE|ST\_XMax返回输入几何体最大的X坐标。|
|ST\_XMin|ST\_XMin\(geom\)|DOUBLE|ST\_XMin返回输入几何体最小的X坐标。|
|ST\_Y|ST\_Y\(point\)|DOUBLE|ST\_Y返回输入点的Y坐标。|
|ST\_YMax|ST\_YMax\(geom\)|DOUBLE|ST\_YMax返回输入几何体最大的Y坐标。|
|ST\_YMin|ST\_YMin\(geom\)|DOUBLE|ST\_YMin返回输入几何体最小的Y坐标。|

## 空间函数最佳实践

阿里云为您提供了空间函数使用方法的最佳实践，详情请参见[使用空间函数查询数据方法](/intl.zh-CN/最佳实践/数仓搭建/使用空间函数查询数据方法.md)。

