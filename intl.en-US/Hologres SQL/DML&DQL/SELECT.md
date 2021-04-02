# SELECT

This topic describes the syntax of the SELECT statement in Hologres.

## Description

SELECT: queries data from zero or more tables in multiple ways.

## Syntax

```
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

where:

-   grouping\_element contains:

    ```
    expression
    ```

-   from\_item contains:

    ```
    table_name [[AS] alias [( column_alias [, ...] )]]
    (select) [AS] alias [( column_alias [, ...] )]
    from_item [NATURAL] join_type from_item
              [ON join_condition | USING ( join_column [, ...] )]
    ```


When you execute the SELECT statement, take note of the following items:

1.  All elements in the FROM list are computed. Each element in the FROM list is a real or virtual table. If more than one element is specified in the FROM list, the union of the results is returned.
2.  If the WHERE clause is specified, all rows that do not satisfy the condition are eliminated from the output.
3.  If the `GROUP BY` clause is specified, or if aggregate functions are called, the output is combined to groups of rows that match one or more values, and the results of aggregate functions are computed. If the HAVING clause is specified, it eliminates groups that do not satisfy the given condition.
4.  The actual output rows are computed by using the SELECT output expressions for each selected row or row group.
5.  `SELECT DISTINCT` eliminates duplicate rows from the result. `SELECT DISTINCT ON` eliminates rows that match all the specified expressions. `SELECT ALL` returns all candidate rows, including duplicates. ALL is the default keyword.
6.  If you use the operators UNION, INTERSECT, and EXCEPT, the output of more than one SELECT statement can be combined to form a single result set. The UNION operator returns all rows that are in one or both of the result sets. The INTERSECT operator returns all rows that are in both result sets. The EXCEPT operator returns the rows that are in the first result set but not in the second. In all three cases, duplicate rows are eliminated unless ALL is specified. The DISTINCT keyword can be explicitly added to eliminate duplicate rows. When you combine the result sets of multiple SQL statements, DISTINCT is the default behavior, even though ALL is the default behavior for a single SELECT statement.
7.  If the `ORDER BY` clause is specified, the returned rows are sorted in the specified order. If `ORDER BY` is not given, the rows are returned in whatever order the system finds fastest to produce.
8.  If the LIMIT \(or FETCH FIRST\) or OFFSET clause is specified, the SELECT statement returns only a subset of the output rows.

## Parameters

1.  **WITH list**

    The WITH list exists preceding a SELECT statement or serves as a subquery of a SELECT statement. It is usually located preceding a SELECT statement to define a subquery. It declares a name for the subquery and the names of returned columns. You can use the following syntax to define a WITH clause as a common table expression \(CTE\):

    ```
    with_query_name [ ( column_name [, ...] ) ] AS ( select )
    ```

    The `with_query_name` parameter specifies the name of the current CTE, which is a valid identifier. The `column_name` list shares similar semantics with the AS keyword in a SELECT statement and specifies the names of the columns returned by a subquery. A subquery can be a regular SELECT query.

    CTEs are separated by commas \(,\). Subsequent CTEs can reference the previously defined CTEs. However, CTEs cannot be recursively called. In subsequent queries, `with_query_name` can be used as a view. If the `column_name` list is not specified, the `column_name` list of the view contains the names of the columns returned by the corresponding subquery. If the `column_name` list is specified, you must use the specified `column_name` list. In addition, make sure that the number of columns in the `column_name` list is the same as the number of columns returned by the SELECT statement of the subquery.

2.  **SELECT list**

    The SELECT list is located between the keywords SELECT and FROM. It specifies expressions that form the output rows of the SELECT statement. The expressions can and usually do reference the columns computed in the FROM clause.

    Just as in a table, each output column of a SELECT statement has a name. In a simple SELECT statement, this name is used only to label the column for display. However, when the SELECT statement is a subquery of a larger query, the name is seen by the larger query as the column name of the virtual table produced by the subquery. To specify the name for an output column, write `AS output_name` after the expression of the column. You can omit AS if the specified output name does not match PostgreSQL keywords. To avoid conflicts with possible keywords to be added in the future, we recommend that you write AS or double-quote the output name. If you do not specify a column name, the system automatically assigns a name. If the column expression is a simple column reference, the original column name is used. In more complex cases, a function or type name may be used, or Hologres may fall back on a generated name such as `column` undefined.

    The name of an output column can be used to indicate the value of the column in `ORDER BY` and `GROUP BY` clauses, but not in the WHERE or HAVING clause, where you must write the expression instead.

    Instead of an expression, an asterisk \(\*\) can be written in the output list as a shorthand for all the columns of the selected rows. You can also write `%table_name*;` as a shorthand for all columns from that table. In these cases, you cannot specify new names by using the AS keyword. The output column names are the same as the names of the table columns.

3.  **FROM clause**

    The FROM clause specifies one or more source tables for the SELECT statement. If multiple source tables are specified, the result is the Cartesian product \(cross join\) of all the source tables. However, qualification conditions are usually added by using the WHERE clause to restrict the returned rows to a small subset of the Cartesian product.

    The FROM clause can contain the following elements:

    table\_name: the name of an existing table or view. The name can be qualified by a schema name.

    alias: the substitute name for the FROM item that contains an alias. An alias is used for brevity or to eliminate ambiguity for self-joins where the same table is scanned multiple times. When an alias is provided, the actual name of the table or function is completely hidden. For example, given `FROM foo AS f`, the remainder of the SELECT statement must refer to this FROM item as f but not foo.

    select: A sub-SELECT statement can appear in the FROM clause. This acts as though the output of the sub-SELECT statement were created as a temporary table for the SELECT statement. The sub-SELECT statement must be enclosed in parentheses \(\), and an alias must be provided for it.

    function\_name: Function calls can appear in the FROM clause. This applies to all functions and is especially useful for functions that return result sets. This acts as though the output of the function were created as a temporary table for the SELECT statement.

    An alias can be provided in the same way as for a table. If an alias is written, a column alias list can also be written to provide substitute names for one or more attributes of the composite return type of the function, including the column added by ORDINALITY if present.

    Multiple function calls can be combined into a single FROM-clause item by surrounding them with `ROWS FROM(...)`. The output of such an item is the concatenation of the first row from each function, then the second row from each function, and so on. If some of the functions produce fewer rows than others, null values are substituted for the missing data, so that the total number of rows returned is always the same as that for the function that produces the most rows.

    ```
    join_type
    [ INNER ] JOIN
    LEFT [ OUTER ] JOIN
    RIGHT [ OUTER ] JOIN
    FULL [ OUTER ] JOIN
    CROSS JOIN
    ```

    For the INNER and OUTER join types, one of the following join conditions must be specified: `NATURAL, ON join_condition, or USING (join_column [, ...])`. For more information about the meaning, see the following text. For `CROSS JOIN`, none of these clauses can appear. A JOIN clause combines two FROM items, which for convenience are referred to as tables, though in reality they can be other types of FROM items. Use parentheses \(\) if necessary to determine the order of nesting. In the absence of parentheses \(\), JOINs nest left to right. JOIN always binds more tightly than the commas that separate FROM-list items.

    `CROSS JOIN` and `INNER JOIN` produce a simple Cartesian product, the same result as you get from listing the two tables at the top level of FROM, but restricted by the join condition if present. `CROSS JOIN` is equivalent to `INNER JOIN ON (TRUE)`, that is, no rows are removed by qualification. These join types are only for a notational convenience, since they do nothing you cannot do with plain FROM and WHERE.

    `LEFT OUTER JOIN` returns all rows in the qualified Cartesian product \(that is, all combined rows that pass the join condition\), plus one copy of each row in the left-hand table for which no right-hand row passes the join condition. Such left-hand rows are extended to the full width of the joined table by inserting null values to the right-hand columns. Only the condition of the JOIN clause is considered for deciding which rows have matches. Then, outer conditions are applied.

    Conversely, `RIGHT OUTER JOIN` returns all the joined rows, plus one row for each unmatched right-hand row \(extended with null values on the left\). This is only for a notational convenience, since you could convert it to a `LEFT OUTER JOIN` by switching the left and right tables.

    `FULL OUTER JOIN` returns all the joined rows, plus one row for each unmatched left-hand row \(extended with null values on the right\), plus one row for each unmatched right-hand row \(extended with null values on the left\).

    `join_condition` is an expression that evaluates to a result of the BOOLEAN type \(similar to a WHERE clause\) that specifies which rows in a join are considered to match.

    A clause in the format of `USING ( a, b, ...)` is the shorthand for `ON left_table.a = right_table.a AND left_table.b = right_table.b ...`. Also, USING implies that only one of each pair of equivalent columns will be included in the join output, not both.

    NATURAL is the shorthand for a USING list that mentions all columns in the two tables that have matching names.

4.  **WHERE clause**

    The optional WHERE clause is in the following format:

    ```
    WHERE condition
    ```

    In this clause, condition is an expression that evaluates to a result of the BOOLEAN type. All rows that do not satisfy this condition will be eliminated from the output. A row satisfies the condition if it returns true when the actual row values are substituted for a variable reference.

5.  **GROUP BY clause**

    The optional `GROUP BY` clause is in the following format:

    ```
    GROUP BY grouping_element [, ...]
    ```

    `GROUP BY` condenses all selected rows that share the same values for the grouped expressions into a single row. An expression used inside a `grouping_element` can be an input column name, the name or ordinal number of an output column \(SELECT list item\), or an arbitrary expression formed from input column values. When ambiguity arises, the name specified in a `GROUP BY` clause will be interpreted as an input column name rather than an output column name.

    If `GROUPING SETS, ROLLUP` or CUBE is present as a grouping element, the `GROUP BY` clause as a whole defines some number of independent grouping sets. The effect of this is equivalent to constructing a UNION ALL between subqueries with the individual grouping sets as their `GROUP BY` clauses.

    Aggregate functions, if used, are computed across all rows making up each group, producing a separate value for each group. If aggregate functions are used whereas the `GROUP BY` clause is not, the query is treated as having a single group comprising all the selected rows. The set of rows fed to each aggregate function can be further filtered by attaching a FILTER clause to the aggregate function call. When a FILTER clause is present, only those rows matching it are included in the input to that aggregate function.

    When `GROUP BY` or an aggregate function is present, it is not valid for the SELECT list expressions to refer to ungrouped columns except within aggregate functions or when the ungrouped column is functionally dependent on the grouped columns. Otherwise, more than one possible value is returned for an ungrouped column. A functional dependency exists if the grouped columns are the primary key or a subset of the primary key of the table containing the ungrouped column.

    All aggregate functions are evaluated before scalar expressions in the HAVING clause or SELECT list. For example, a CASE expression cannot be used to skip evaluation of an aggregate function.

6.  **DISTINCT clause**

    If SELECT DISTINCT is specified, all duplicate rows are removed from the result set. One row is kept for each group of duplicates.

    ```
    SELECT DISTINCT accountid FROM table;
    ```

7.  **COUNT DISTINCT clause**

    `COUNT DISTINCT` counts the number of unique values in a column. A value that appears multiple times in the column is counted only once. Null values are not counted. This clause is similar to the COUNT function.

    The following syntax applies in accurate counting:

    ```
    SELECT c1, COUNT(DISTINCT c2) FROM table GROUP BY c1
    ```

    Accurate `COUNT DISTINCT` consumes many resources. Hologres also supports non-accurate `COUNT DISTINCT`, which uses the following syntax:

    ```
    SELECT c1, approx_count_distinct(c2) FROM table GROUP BY c1
    ```

8.  **UNION clause**

    ```
    select_statement UNION [ ALL | DISTINCT ] select_statement
    ```

    `select_statement` is a SELECT statement that does not contain an `ORDER or a BY, LIMIT, FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE`, or `FOR KEY SHARE` clause. `ORDER BY` and LIMIT can be attached to a subexpression if it is enclosed in parentheses \(\). Without parentheses \(\), these clauses will be taken to apply to the result of the UNION, not to its right-hand input expression.

    The UNION operator computes the set union of the rows returned by the involved SELECT statements. A row is in the set union of two result sets if it appears in at least one of the result sets. The two SELECT statements that represent the direct operands of the UNION must produce the same number of columns, and corresponding columns must be of compatible data types.

    The result of UNION does not contain duplicate rows unless the ALL option is specified. ALL prevents elimination of duplicates. Therefore, UNION ALL is usually significantly quicker than UNION. Use ALL whenever possible. The DISTINCT keyword can be explicitly specified to eliminate duplicate rows.

    Multiple UNION operators in the same SELECT statement are evaluated left to right, unless otherwise indicated by parentheses \(\).

9.  **INTERSECT clause**

    The INTERSECT clause is in the following format:

    ```
    select_statement INTERSECT [ ALL | DISTINCT ] select_statement
    ```

    `select_statement` is a SELECT statement that does not contain an `ORDER BY or a LIMIT` clause.

    The INTERSECT operator computes the set intersection of the rows returned by the involved SELECT statements. A row is in the intersection of two result sets if it appears in both result sets.

    The result of INTERSECT does not contain duplicate rows unless the ALL option is specified. If the ALL option is specified, a row that has m duplicates in the left table and n duplicates in the right table will appear min\(m,n\) times in the result set. The DISTINCT keyword can be explicitly specified to eliminate duplicate rows.

    Multiple INTERSECT operators in the same SELECT statement are evaluated left to right, unless parentheses \(\) dictate otherwise. INTERSECT binds more tightly than UNION. That is, `A UNION B INTERSECT C` will be read as `A UNION (B INTERSECT C)`.

10. **EXCEPT clause**

    The EXCEPT clause is in the following format:

    ```
    select_statement EXCEPT [ ALL | DISTINCT ] select_statement
    ```

    `select_statement` is a SELECT statement that does not contain an `ORDER BY or a LIMIT` clause.

    The EXCEPT operator computes the set of rows that are in the result of the left SELECT statement but not in the result of the right one.

    The result of EXCEPT does not contain duplicate rows unless the ALL option is specified. If the ALL option is specified, a row that has m duplicates in the left table and n duplicates in the right table will appear max\(m-n,0\) times in the result set. The DISTINCT keyword can be explicitly specified to eliminate duplicate rows.

    Multiple EXCEPT operators in the same SELECT statement are evaluated left to right, unless parentheses \(\) dictate otherwise. EXCEPT binds at the same level as UNION.

    `FOR NO KEY UPDATE, FOR UPDATE, FOR SHARE` and `FOR KEY SHARE` cannot be specified either for an EXCEPT result or for the input of an EXCEPT.

11. **ORDER BY clause**

    The optional `ORDER BY` clause is in the following format:

    ```
    ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...]
    ```

    The `ORDER BY` clause causes the result rows to be sorted based on the specified expressions. If two rows are equal based on the leftmost expression, they are compared based on the next expression and so on. If they are equal based on all specified expressions, they are returned in an implementation-dependent order.

    Each expression can be the name or ordinal number of an output column \(SELECT list item\), or it can be an arbitrary expression formed from input column values.

    The ordinal number refers to the ordinal \(left-to-right\) position of the output column. This feature makes it possible to define an ordering on the basis of a column that does not have a unique name. This is never absolutely necessary because it is always possible to assign a name to an output column using the AS clause.

    It is also possible to use arbitrary expressions in the ORDER BY clause, including columns that do not appear in the SELECT output list. Therefore, the following statement is valid:

    ```
    SELECT name FROM distributors ORDER BY code;
    ```

    A limit of this feature is that an `ORDER BY` clause applying to the result of a UNION, INTERSECT, or EXCEPT clause can specify only an output column name or number, not an expression.

    If an ORDER BY expression is a simple name that matches both an output column name and an input column name, ORDER BY will interpret it as the output column name. This is the opposite of the choice that GROUP BY will make in the same situation. This inconsistency is made to be compatible with the SQL standard.

    Optionally, you can add the keyword ASC \(ascending\) or DESC \(descending\) after an expression in the ORDER BY clause. If not specified, ASC is assumed by default. Alternatively, a specific ordering operator name can be specified in the USING clause. An ordering operator must be a less-than or greater-than member of some B-tree operator family. ASC is usually equivalent to USING < and DESC is usually equivalent to USING \>. However, the creator of a user-defined data type can define exactly what the default sort ordering is, and it might correspond to operators with other names.

    If `NULLS LAST` is specified, null values sort after all non-null values. If `NULLS FIRST` is specified, null values sort before all non-null values. If neither is specified, the default behavior is `NULLS LAST` when ASC is specified or implied, and `NULLS FIRST` when DESC is specified. Thus, null values are larger than non-null values by default. When USING is specified, the default nulls ordering depends on whether the operator is a less-than or greater-than operator.

    Ordering options apply only to the expression they follow. For example, `ORDER BY x, y DESC` does not mean the same thing as `ORDER BY x DESC, y DESC`.

    Character-string data is sorted based on the collation that applies to the column being sorted. That can be overridden as needed by including a COLLATE clause in the expression, for example, `ORDER BY mycolumn COLLATE "en_US"`.

12. **LIMIT clause**

    The LIMIT clause consists of two independent sub-clauses.

    ```
    LIMIT { count | ALL }
    OFFSET start
    ```

    In the LIMIT clause, count specifies the maximum number of rows to return, whereas start specifies the number of rows to skip before rows are returned. When both are specified, start rows are skipped before starting to count the count rows to be returned.

    If the count expression evaluates to NULL, it is treated the same as `LIMIT ALL`, which means no limit. If start evaluates to NULL, it is treated the same as OFFSET 0.

    When LIMIT is used, it is a good idea to use an `ORDER BY` clause that constrains the result rows into a unique order. Otherwise, you will get an unpredictable subset of the rows of the query result. For example, you might be asking for the tenth through twentieth rows, but do not know the ordering. In this case, you must specify `ORDER BY`.

    The query planner takes LIMIT into account when generating a query plan, so you are very likely to get different plans \(yielding different row orders\) depending on what you use for LIMIT and OFFSET. Thus, using different LIMIT or OFFSET values to select different subsets of a query result will give inconsistent results unless you enforce a predictable result ordering with ORDER BY. This is not a bug, but an inherent consequence of the fact that SQL does not promise to deliver the results of a query in a specified order unless `ORDER BY` is used to constrain the order.

    It is even possible for repeated executions of the same LIMIT query to return different subsets of the rows of a table, if `ORDER BY` is not used to enforce selection of a deterministic subset. Again, this is not a bug. Determinism of the results cannot be guaranteed in such a case.


## Examples

-   **This example shows you how to join Table films with Table distributors**:

    ```
    SELECT f.title, f.did, d.name, f.date_prod, f.kind FROM 
    distributors d, films f WHERE f.did = d.did
    ```

-   **This example shows you how to sum the column length of all films and group the results by kind**:

    ```
    SELECT kind, sum(length) AS total FROM films GROUP BY kind;
    ```

-   **This example shows you how to sum the column length of all films, group the results by kind, and show those group totals that are less than 5 hours**:

    ```
    SELECT kind, sum(length) AS total FROM films GROUP BY kind 
    HAVING sum(length) < interval '5 hours';
    ```

-   **This example shows you how to sort the returned results by name**:

    ```
    SELECT * FROM distributors ORDER BY name;
    ```


