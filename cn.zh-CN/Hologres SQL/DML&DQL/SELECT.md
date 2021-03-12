# SELECT

本文将会为您介绍在交互式分析（Hologres） 中数据查询SELECT的用法。

## 命令介绍

SELECT：从零或更多表中以多种形式的数据查询。主要包含的参数如下表所示：

|参数列表|
|----|
|[WITH列表](#li_tgj_qrz_8c5)|[SELECT列表](#li_hst_350_5zi)|
|[FROM子句](#li_dw5_hao_j4f)|[WHERE子句](#li_w94_3db_f72)|
|[GROUP BY子句](#li_m62_vqm_6pw)|[CUBE子句](#li_z7w_r9m_rkk)|
|[DISTINCT 子句](#li_biy_pmc_01v)|[COUNT DISTINCT子句](#li_eam_z89_grh)|
|[UNION子句](#li_uc4_geq_75z)|[INTERSECT子句](#li_xst_4so_4s5)|
|[EXCEPT子句](#li_f62_isr_6tw)|[ORDER BY子句](#li_ck9_2co_bwo)|
|[LIMIT子句](#li_si1_xwl_kou)|

## 命令格式

SELECT语句的基本语法具体如下。

```
[ WITH with_query [, ...] ]
SELECT [ALL | DISTINCT [ON (expression [, ...])]]
  * | expression [[AS] output_name] [, ...]
  [FROM from_item [, ...]]
  [WHERE condition]
  [GROUP BY grouping_element [, ...]]
  [HAVING condition [, ...]]
  [{UNION | INTERSECT | EXCEPT} [ALL] select]
  [ORDER BY expression [ASC | DESC | USING operator] [, ...]]
  [LIMIT {count | ALL}]
```

其中，`grouping_element`和`from_item`包含的内容如下：

-   grouping\_element包含`expression``expression`可能是列名、常数、函数或者是列名或常数的任意组合，以及以算术运算符或逐位运算符连接的函数。
-   from\_item包含

    ```
    table_name [[AS] alias [( column_alias [, ...] )]]
    (select) [AS] alias [( column_alias [, ...] )]
    from_item [NATURAL] join_type from_item
              [ON join_condition | USING ( join_column [, ...] )]
    ```


## 命令用法

SELECT的常用方法如下：

1.  FROM列表中的所有元素都会被计算（ FROM中的每一个元素都是一个真实表或者虚拟表）。如果在FROM列表中指定了多于一个元素，得到的结果做并集。
2.  如果指定了WHERE子句，所有不满足该条件的行都会被从输出中消除。
3.  如果指定了`GROUP BY`子句或者如果有聚集函数，输出会被组合成由在一个或者多个值上匹配的行构成的分组，并且在其上计算聚集函数的结果。如果出现了HAVING子句，它会消除不满足给定条件的分组。
4.  对于每一个被选中的行或者行组，会使用SELECT输出表达式计算实际的输出行。
5.  `SELECT DISTINCT`从结果中消除重复的行。 `SELECT DISTINCT ON`消除在所有指定表达式上匹配的行。`SELECT ALL`（默认）将返回所有候选行， 包括重复的行。
6.  通过使用操作符UNION、INTERSECT和EXCEPT，多于一个SELECT语句的输出可以被整合形成一个结果集。UNION操作符返回位于一个或者两个结果集中的全部行。INTERSECT操作符返回同时位于两个结果集中的所有行。EXCEPT操作符返回位于第一个结果集但不在第二个结果集中的行。在所有三种情况下，重复行都会被消除（除非指定ALL）。可以增加DISTINCT来显式的消除重复行。注意虽然ALL是SELECT自身的默认行为，但这里DISTINCT是默认行为。
7.  如果指定了`ORDER BY`子句，被返回的行会以指定的顺序排序。如果没有给定`ORDER BY`，系统会以能最快产生行的顺序返回它们。
8.  如果指定了LIMIT（或FETCH FIRST） 或者OFFSET子句，SELECT语句只返回结果行的一个子集。

## 参数说明

-   **WITH列表**
    -   命令简介

        WITH列表是位于SELECT之前或者作为SELECT子句的subquery存在，通常位于SELECT之前，用于定义子查询，并为子查询声明一个名字和返回的列名，定义每一个WITH子句为一个CTE（Common Table Expression），定义语法如下：

        ```
        with_query_name [ ( column_name [, ...] ) ] AS ( select )
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |with\_query\_name|指定当前CTE的名字，可以是任意有效的标识符。|
        |column\_name|列表对应着子查询返回值的列名，类似于SELECT子句中的AS的语义，子查询可以是一个常规的SELECT查询。|

        CTE之间通过逗号分隔，后面出现的CTE定义可以引用前面定义的CTE，但是目前暂时不支持递归的CTE调用，在之后的查询中，可以直接将`with_query_name`作为一个视图（view）出现在查询中，如果没有指定`column_name`列表，该view的`column_name`为对应子查询返回列的列名，如果指定`column_name`列表，则需要使用定义`column_name`，并且`column_name`列表需要和SELECT返回值列表个数相同。

    -   使用示例

        ```
        WITH distributor_name(name) AS SELECT name from distributors
        SELECT name FROM distributor_name ORDER BY name;
        ```

-   **SELECT列表**
    -   命令简介

        SELECT列表（位于关键词 SELECT和FROM之间）指定构成SELECT语句输出行的表达式。这些表达式可以（并且通常确实会）引用FROM子句中计算得到的列。

    -   参数说明

        SELECT的每一个输出列都有一个名称。 在一个简单的SELECT中，该名称只用来标记要显示的列，当SELECT为一个大型查询的子查询时，大型查询会把该名称看做子查询产生的虚表的列名。要指定用于输出列的名称，在该列的表达式后面添加`AS output_name`（可以省略AS，但只能在期望的输出名称不匹配任何PostgreSQL关键词时省略。为了避免和未来增加的关键词冲突， 推荐您添加AS或者用双引号引用输出名称）。如果不指定列名，PostgreSQL会自动选择一个名称。如果列的表达式是一个简单的列引用，那么被选择的名称就和该列的名称相同。在使用函数或者类型名称的更复杂的情况中，系统可能会生成诸如`?column?`之类的名称。

        一个输出列的名称可以被用来在`ORDER BY`以及`GROUP BY`子句中引用该列的值，但是不能用于 WHERE和HAVING子句（在其中必须写出表达式）。

        可以在输出列表中写\*来取代表达式，它是被选中行的所有列的一种简写方式。还可以写 `%table_name*;`，它是只来自那个表的所有列的简写形式。在这些情况中无法用AS指定新的名称，输出行的名称将和表列的名称相同。

-   **FROM子句**
    -   命令简介

        FROM子句为SELECT 指定一个或者更多源表。如果指定了多个源表，结果将是所有源表的笛卡尔积（交叉连接）。但是通常会增加限定条件（通过 WHERE）来把返回的行限制为该笛卡尔积的一个小子集。

    -   参数说明

        FROM子句可以包含下列元素：

        |元素|说明|
        |--|--|
        |table\_name|一个现有表或视图的名称（可以限定表的schma模式）。|
        |alias|一个包含别名的FROM项的替代名称。别名被用于让书写简洁或者消除自连接中的混淆（其中同一个表会被扫描多次）。当提供一个别名时，表或者函数的实际名称会被隐藏。例如，给定`FROM foo AS f`，SELECT的剩余部分就必须以f而不是foo来引用这个 FROM项。|
        |select|一个子SELECT可以出现在 FROM子句中。这就好像把它的输出创建为一个存在于该SELECT命令期间的临时表。注意子-SELECT必须用圆括号包围，并且必须为它提供一个别名。|
        |function\_name|函数调用可以出现在FROM子句中（对于返回结果集合的函数特别有用，但是可以使用任何函数）。这就好像把该函数的输出创建为一个存在于该SELECT命令期间的临时表。

可以用和表一样的方式提供一个别名。如果写了一个别名，还可以写一个列别名列表来为该函数的组合返回类型的一个或者多个属性提供替代名称， 包括由ORDINALITY（如果有）增加的新列。

通过把多个函数调用包围在`ROWS FROM（。）`中可以把它们整合在单个FROM-子句项中。这样一个项的输出是把每一个函数的第一行串接起来，然后是每个函数的第二行，以此类推。如果有些函数产生的行比其他函数少，则在缺失数据的地方放上 NULL，这样被返回的总行数总是和产生最多行的函数一样。 |
        |join\_type|包含如下5种类型：        -   \[ INNER \] JOIN

对于INNER和OUTER连接类型，必须指定一个连接条件，即 `NATURAL、ON join_condition或者 USING （join_column [, ...]）`之一（只能有一种）。其含义见下文。对于`CROSS JOIN`，上述子句不能出现。一个JOIN子句联合两个FROM项（为了方便我们称之为“表“，尽管实际上它们可以是任何类型的FROM项）。如有必要可以使用圆括号确定嵌套的顺序。 在没有圆括号时，JOIN会从左至右嵌套。在任何情况下，JOIN的联合比用逗号分隔FROM-列表项更强。

        -   LEFT \[ OUTER \] JOIN

`LEFT OUTER JOIN`返回被限制过的笛卡尔积中的所有行（即所有通过了其连接条件的组合行），以及左表中每行的一个副本，因为没有右行通过了连接条件。通过在右手列中插入空值，这种左手行会被扩展为连接表的完整行。注意在决定哪些行匹配时，只考虑JOIN子句自身的条件。之后才应用外条件。

        -   RIGHT \[ OUTER \] JOIN

`RIGHT OUTER JOIN`返回所有连接行，外加每一个没有匹配上的右手行（在左端用空值扩展）。这只是为了记号上的方便，因为可以通过交换左右表把它转换成一个`LEFT OUTER JOIN`。

        -   FULL \[ OUTER \] JOIN

`FULL OUTER JOIN`返回所有连接行，外加每一个没有匹配上的左手行（在右端用空值扩展），再外加每一个没有匹配上的右手行（在左端用空值扩展）

        -   CROSS JOIN

`CROSS JOIN`和`INNER JOIN`会产生简单的笛卡尔积，也就是与在FROM列出两个表得到的结果相同，但是要用连接条件（如果有）约束该结果。 `CROSS JOIN`与`INNER JOIN ON （TRUE）`等效，也就是说条件不会移除任何行。这些连接类型只是一种记号上的方便，因为没有什么是用纯粹的FROM和 WHERE能做而它们不能做的。 |
        |join\_condition|`join_condition`是一个会得到boolean类型值的表达式（类似于一个WHERE子句），它说明一次连接中哪些行被认为相匹配。|
        |USING \( a, b, ... \)|形式`USING （a， b...）`的子句是 `ON left_table.a = right_table.a AND left_table.b = right_table.b ...`的简写。还有， USING表示每一对相等列中只有一个会被包括在连接输出中。|
        |NATURAL|列出在两个表中所有具有相同名称的列的USING的简写。|

-   **WHERE子句**
    -   命令简介

        可选的WHERE子句的形式 ：

        ```
        WHERE condition
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |condition|condition是任一计算得到布尔类型结果的表达式。任何不满足这个条件的行都会从输出中被消除。如果用一行的实际值替换其中的变量引用后，该表达式返回真，则该行符合条件。|

-   **GROUP BY子句**
    -   命令简介

        `GROUP BY`将会把所有被选择的行中共享相同分组表达式值的那些行压缩成一个行。可选的`GROUP BY`子句的形式：

        ```
        GROUP BY grouping_element [, ...]
        ```

    -   参数说明

        `grouping_element`中使用的expression可以是输入列名、输出列（SELECT列表项） 的名称或序号或者由输入列值构成的任意表达式。在出现歧义时， `GROUP BY`名称将被解释为输入列名而不是输出列名。

        如果任何`GROUPING SETS、ROLLUP`或者CUBE作为分组元素存在，则`GROUP BY`子句整体上定义了数个独立的分组集。其效果等效于在具有独立分组集作为它们的`GROUP BY`子句的子查询间构建一个UNION ALL。

        聚集函数（如果使用）会在组成每一个分组的所有行上进行计算，从而为每一个分组产生一个单独的值（如果有聚集函数但是没有`GROUP BY`子句，则查询会被当成是由所有选中行构成的一个单一分组）。传递给每一个聚集函数的行集合可以通过在聚集函数调用附加一个FILTER子句来进一步过滤。当存在一个FILTER子句时，只有那些匹配它的行才会被包括在该聚集函数的输入中。

        当存在`GROUP BY`子句或者任何聚集函数时， SELECT列表表达式不能引用非分组列（除非它出现在聚集函数中或者它函数依赖于分组列），因为这样做会导致返回非分组列的值时会有多种可能的值。如果分组列是包含非分组列的表的主键（ 或者主键的子集），则存在函数依赖。

        所有的聚集函数都是在HAVING子句或者 SELECT列表中的任何“标量“表达式之前被计算。 这意味着一个CASE表达式不能被用来跳过一个聚集表达式的计算。

    -   使用示例

        GROUP BY分组：

        ```
        SELECT kind, sum(length) AS total FROM films GROUP BY kind;
        ```

-   **CUBE子句**
    -   命令简介
        -   CUBE

            CUBE是自动对GROUP BY子句中列出的字段进行分组汇总，结果集将包含维度列中各值的所有可能组合，以及与这些维度值组合相匹配的基础行中的聚合值。它会为每个分组返回一行汇总信息，用户可以使用CUBE来产生交叉表值。例如，在CUBE子句中给出三个表达式`（n = 3）`，运算结果为2n = 23 = 8组。 以n个表达式的值分组的行称为常规行，其余的行称为超级聚集行。具体表达式如下：

            ```
            CUBE ( { expression | ( expression [, …] ) } [, …] )
            ```

        -   ROLLUP

            ROLLUP 函数是聚合函数，它是GROUP BY语句的简单扩展。在数据统计和报表生成过程中，它可以为每个分组返回一个小计，同时为所有分组返回总计。具体表达式如下：

            ```
            ROLLUP ( { expression | ( expression [, …] ) } [, …] ) 
            ```

        -   GROUPING SETS

            GROUPING SETS子句是GROUP BY子句的进一步扩展，它可以使用户指定多个GROUP BY选项。 这样做可以通过裁剪用户不需要的数据组来提高效率。 当用户指定了所需的数据组时，数据库不需要执行完整CUBE或ROLLUP生成的聚合集合。具体表达式如下：

            ```
            GROUPING SETS ( grouping_element [, …] ) 
            ```

    -   使用示例
        -   GROUP BY CUBE：

            ```
            SELECT  l_returnflag
                    ,l_shipmode
                    ,SUM(l_quantity)
            FROM    public.lineitem
            GROUP BY cube((l_returnflag),(l_shipmode))
            ORDER BY l_returnflag
                     ,l_shipmode
            ```

        -   GROUP BY ROLLUP：

            ```
            SELECT  l_returnflag
                    ,l_shipmode
                    ,SUM(l_quantity)
            FROM    public.lineitem
            GROUP BY ROLLUP ((l_returnflag),(l_shipmode))
            ORDER BY l_returnflag
                     ,l_shipmode
            ```

        -   GROUP BY GROUPING SETS：

            ```
            SELECT  l_returnflag
                    ,l_shipmode
                    ,SUM(l_quantity)
            FROM    public.lineitem
            GROUP BY GROUPING SETS ((l_returnflag,l_shipmode),())
            ORDER BY l_returnflag
                     ,l_shipmode
            ```

-   **DISTINCT子句**
    -   命令简介

        如果指定了SELECT DISTINCT，所有重复的行会被从结果集中移除（为每一组重复的行保留一行）。

    -   使用示例

        ```
        SELECT DISTINCT accountid FROM table;
        ```

-   **COUNT DISTINCT子句**
    -   命令简介

        `COUNT DISTINCT`支持计算去重之后的某一个column的个数，对于该列中出现多次的值只会被计算一次，和COUNT的计算类似，如果该列包含NULL值，它将不会计算在内。

    -   使用示例
        -   精确计算的语法示例如下：

            ```
            SELECT c1, COUNT(DISTINCT c2) FROM table GROUP BY c1
            ```

        -   由于精确计算的`COUNT DISTINCT`需要消耗较大的资源，因此交互式分析还支持非精确的`COUNT DISTINCT`计算，语法示例如下：

            ```
            SELECT c1, approx_count_distinct(c2) FROM table GROUP BY c1
            ```

-   **UNION子句**
    -   命令简介

        UNION子句语法如下：

        ```
        select_statement UNION [ ALL | DISTINCT ] select_statement
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |select\_statement|`select_statement`是任何没有`ORDER BY、LIMIT、 FOR NO KEY UPDATE、FOR UPDATE、 FOR SHARE`和`FOR KEY SHARE`子句的SELECT语句（如果子表达式被包围在圆括号内， `ORDER BY`和LIMIT可以被附着到其上。如果没有圆括号，这些子句将被应用到UNION的结果而不是右手边的表达式上）。|
        |UNION|UNION操作符计算所涉及的SELECT语句所返回的行的并集。如果一至少出现在两个结果集中的一个内，它就会在并集中。作为UNION两个操作数的SELECT语句必须产生相同数量的列并且对应位置上的列必须具有兼容的数据类型。

UNION的结果不会包含重复行，除非指定了ALL选项。ALL会阻止消除重复（因此，UNION ALL通常显著地快于UNION，尽量使用ALL）。可以写DISTINCT来显式地指定消除重复行的行为。

除非用圆括号指定计算顺序，同一个SELECT语句中的多个UNION操作符会从左至右计算。 |

-   **INTERSECT子句**
    -   命令简介

        INTERSECT子句具有下面的形式：

        ```
        select_statement INTERSECT [ ALL | DISTINCT ] select_statement
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |select\_statement|`select_statement`是任何没有`ORDER BY LIMIT`子句的SELECT语句。|
        |INTERSECT|INTERSECT操作符计算所涉及的 SELECT语句返回的行的交集。如果一行同时出现在两个结果集中，它就在交集中。

INTERSECT的结果不会包含重复行，除非指定了 ALL选项。如果有ALL，一个在左表中有m次重复并且在右表中有n 次重复的行将会在结果中出现min（m，n）次。 DISTINCT可以写DISTINCT来显式地指定消除重复行的行为。

除非用圆括号指定计算顺序， 同一个SELECT语句中的多个 INTERSECT操作符会从左至右计算。 INTERSECT的优先级比UNION更高。也即， `A UNION B INTERSECT C`将被读成`A UNION（B INTERSECT C）`。 |

-   **EXCEPT子句**
    -   命令简介

        EXCEPT子句具有下面的形式：

        ```
        select_statement EXCEPT [ ALL | DISTINCT ] select_statement
        ```

    -   参数说明

        |参数|说明|
        |--|--|
        |select\_statement|`select_statement`是任何没有`ORDER BY LIMIT`子句的SELECT语句。|
        |EXCEPT|EXCEPT操作符计算位于左SELECT语句的结果中但不在右SELECT语句结果中的行集合。

EXCEPT的结果不会包含重复行，除非指定了 ALL选项。如果有ALL，一个在左表中有 m次重复并且在右表中有n次重复的行将会在结果集中出现max（m-n，0） 次。 DISTINCT可以写DISTINCT来显式地指定消除重复行的行为。

除非用圆括号指定计算顺序， 同一个SELECT语句中的多个 EXCEPT操作符会从左至右计算。 EXCEPT的优先级与 UNION相同。

当前，`FOR NO KEY UPDATE、FOR UPDATE、 FOR SHARE`和`FOR KEY SHARE`不能用于EXCEPT结果或者 EXCEPT的任何输入。 |

-   **ORDER BY子句**
    -   命令简介

        可选的`ORDER BY`子句的形式如下：

        ```
        ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...]
        ```

    -   参数说明

        `ORDER BY`子句导致结果行被按照指定的表达式排序。 如果两行按照最左边的表达式是相等的，则会根据下一个表达式比较它们， 以此类推。如果按照所有指定的表达式它们都是相等的，则它们被返回的顺序取决于实现。

        每一个expression 可以是输出列（SELECT列表项）的名称或者序号，它也可以是由输入列值构成的任意表达式。

        序号指的是输出列的顺序（从左至右）位置。这种特性可以为不具有唯一名称的列定义一个顺序。但这不是必要操作，因为可以使用 AS子句为输出列赋予一个名称。

        也可以在ORDER BY子句中使用任意表达式，包括没有出现在SELECT输出列表中的列。因此， 下面的语句是合法的：

        ```
        SELECT name FROM distributors ORDER BY code;
        ```

        这种特性的一个限制是一个应用在UNION、 INTERSECT或EXCEPT子句结果上的 `ORDER BY`只能指定输出列名称或序号，但不能指定表达式。

        如果一个ORDER BY表达式是一个既匹配输出列名称又匹配输入列名称的简单名称，ORDER BY将把它解读成输出列名称。这与在同样情况下GROUP BY会做出的选择相反。这种不一致是为了与SQL标准兼容。

        可以为ORDER BY子句中的任何表达式之后增加关键词 ASC（上升）或者DESC（下降）。如果没有指定， ASC被假定为默认值。或者，可以在USING 子句中指定一个特定的排序操作符名称。一个排序操作符必须是某个 B-树操作符族的小于或者大于成员。ASC通常等价于 USING <而DESC通常等价于 USING \>（但是一种用户定义数据类型的创建者可以准确地定义默认排序顺序是什么，并且它可能会对应于其他名称的操作符）。

        如果指定`NULLS LAST`，空值会排在非空值之后；如果指定`NULLS FIRST`，空值会排在非空值之前。如果都没有指定， 在指定或者隐含ASC时的默认行为是`NULLS LAST`， 而指定或者隐含DESC时的默认行为是`NULLS FIRST`（因此，默认行为是空值大于非空值）。 当指定USING时，默认的空值顺序取决于该操作符是否为小于或者大于操作符。

        注意顺序选项只应用到它们所跟随的表达式上。例如`ORDER BY x， y DESC`和`ORDER BY x DESC，y DESC`是不同的。

        字符串数据会被根据引用到被排序列上的排序规则排序。根据需要可以通过在 expression中包括一个 COLLATE子句来覆盖，例如`ORDER BY mycolumn COLLATE “en_US“`。

    -   使用示例

        ```
        SELECT * FROM distributors ORDER BY name;
        ```

-   **LIMIT子句**
    -   命令简介

        LIMIT子句由两个独立的子句构成：

        ```
        LIMIT { count | ALL }
        OFFSET start
        ```

    -   参数说明

        count指定要返回的最大行数，而start指定在返回行之前要跳过的行数。在两者都被指定时，在开始计算要返回的count行之前会跳过 start行。

        如果count表达式计算为NULL，它会被当成`LIMIT ALL`，即没有限制。如果 start计算为 NULL，它会被当作OFFSET 0。

        在使用LIMIT时，用一个`ORDER BY`子句把结果行约束到一个唯一顺序是个好办法。否则讲得到该查询结果行的一个不可预测的子集 — 可能要求从第 10 到第 20 行，但是在什么顺序下的第10到第20呢？除非指定`ORDER BY`，否则是不知道顺序的。

        查询规划器在生成一个查询计划时会考虑LIMIT，因此根据使用的LIMIT和OFFSET，很可能得到不同的计划（得到不同的行序）。所以，使用不同的LIMIT/OFFSET值来选择一个查询结果的不同子集将会给出不一致的结果，除非用ORDER BY强制一种可预测的结果顺序。这不是一个缺陷，它是SQL不承诺以任何特定顺序（除非使用`ORDER BY`来约束顺序）给出一个查询结果这一事实造成的必然后果。

        如果没有一个`ORDER BY`来强制选择一个确定的子集， 重复执行同样的LIMIT查询甚至可能会返回一个表中行的不同子集。同样，这也不是一种缺陷，再这样一种情况下也无法保证结果的确定性。


