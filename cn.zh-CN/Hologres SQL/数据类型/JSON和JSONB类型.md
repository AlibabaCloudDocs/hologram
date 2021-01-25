---
keyword: [Hologres, JSON, JSONB]
---

# JSON和JSONB类型

文将为你介绍Hologres中JSON和JSONB数据类型的语法和使用方法。

## 使用限制

Hologres支持JSON和JSONB两种JSON数据类型，在使用时需要注意的事项如下：

-   仅Hologres V0.9及以上版本支持JSON类型，如果您的实例是V0.9以下版本，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)或加入在线支持钉钉群申请升级实例。
-   Hologres当前版本暂不支持JSON和JSONB索引相关功能。
-   暂不支持的函数包括：json\_each/jsonb\_each、json\_each\_text/jsonb\_each\_text、json\_extract\_path/jsonb\_extract\_path、jsonb\_agg/jsonb\_each/jsonb\_to\_record。

## JSON和JSONB类型概述

JSON和JSONB两种JSON数据类型，主要的区别之一是效率，具体说明如下：

-   JSON类型数据存储输入文本的精准拷贝，处理函数在每次执行时必须重新解析该数据。由于JSON类型数据存储的是输入文本的准确拷贝，因此可能会保留因为语法使用而存在的空格等内容。如果一个值中的JSON对象包含同一个键超过一次，所有的键/值对都会被保留（ 处理函数会把最后的值当作有效值）。
-   JSONB类型数据被存储在一种分解好的二进制格式中，因为需要做附加的转换，在输入时要稍慢一些。由于不需要解析，因此在处理时要快很多。JSONB不保留空格、不保留对象键的顺序并且不保留重复的对象键。如果在输入中指定了重复的键，只有最后一个值会被保留。

**说明：** 键值对的键必须使用双引号。

## JSON和JSONB的操作符

JSON数据类型用来存储JSON数据，这种数据也可以被存储为TEXT类型。JSON数据类型的优势在于能强制要求每个被存储的值符合JSON规则，JSON支持的操作符使得其操作更为方便。

|操作符|右操作数类型|描述|操作示例|执行结果|
|---|------|--|----|----|
|-\>|int|获得JSON数组元素（索引从0开始，负整数从末尾开始计）|```
select '[{"a":"foo"},{"b":"bar"},{"c":"baz"}]'::json->2
```

|```
{"c":"baz"}
``` |
|-\>|text|通过键获得JSON对象域|```
select '{"a": {"b":"foo"}}'::json->'a'
```

|```
{"b":"foo"}
``` |
|-\>\>|int|以TEXT形式获得JSON数组元素|```
select '[1,2,3]'::json->>2
```

|```
3
``` |
|-\>\>|text|以TEXT形式获得JSON对象域|```
select '{"a":1,"b":2}'::json->>'b'
```

|```
2
``` |
|\#\>|text\[\]|获取在指定路径的JSON对象|```
select '{"a": {"b":{"c": "foo"}}}'::json#>'{a,b}'
```

|```
{"c": "foo"}
``` |
|\#\>\>|text\[\]|以TEXT形式获取在指定路径的JSON对象|```
select '{"a":[1,2,3],"b":[4,5,6]}'::json#>>'{a,2}'
```

|```
3
``` |

## 额外的JSONB操作符

如下表格中为JSONB数据类型支持的操作符。

|操作符|右操作数类型|描述|操作示例|执行结果|
|---|------|--|----|----|
|@\>|jsonb|左侧的JSON值是否在顶层包含右侧的JSON路径或值？|```
select '{"a":1, "b":2}'::jsonb @> '{"b":2}'::jsonb
```

|```
true
``` |
|<@|jsonb|左侧的JSO 路径或值项是否被包含在右侧的JSON 值的顶层？|```
select '{"b":2}'::jsonb <@ '{"a":1, "b":2}'::jsonb
```

|```
true
``` |
|?|text|键或元素字符串是否存在于JSON值的顶层？|```
select '{"a":1, "b":2}'::jsonb ? 'b'
```

|```
true
``` |
|?\||text\[\]|数组字符串中的任何一个是否做为顶层键存在？|```
select '{"a":1, "b":2, "c":3}'::jsonb ?| array['b', 'c']
```

|```
true
``` |
|?&|text\[\]|是否所有数组字符串都作为顶层键存在？|```
select '["a", "b"]'::jsonb ?& array['a', 'b']
```

|```
true
``` |
|\|\||jsonb|将两个jsonb值串接成一个新的jsonb值|```
select '["a", "b"]'::jsonb || '["c", "d"]'::jsonb
```

|```
["a", "b", "c", "d"]
``` |
|-|text|从左操作数删除键/值对或者string元素。键/值对基于它们的键值来匹配。|```
select '{"a": "b"}'::jsonb - 'a'
```

|```
{}
``` |
|-|text\[\]|从左操作数中删除多个键/值对或者string元素。键/值对基于它们的键值来匹配。|```
select '{"a": "b", "c": "d"}'::jsonb - '{a,c}'::text[]
```

|```
{}
``` |
|-|integer|删除具有指定索引（负值表示倒数）的数组元素。如果顶层容器不是数组则抛出一个错误。|```
select '["a", "b"]'::jsonb - 1
```

|```
["a"]
``` |
|\#-|text\[\]|删除具有指定路径的域或者元素（对于JSON数组，负值表示倒数）|```
select '["a", {"b":1}]'::jsonb #- '{1,b}'
```

|```
["a", {}]
``` |

## JSON创建函数

如下为可以用于创建JSON值的函数描述及操作示例。

|函数|描述|操作示例|执行结果|
|--|--|----|----|
|to\_json\(anyelement\)|此函数可以将该值返回为JSON。数组和组合会被（递归）转换成数组和对象，对于不是数组和组合的值，如果有从该类型到JSON的造型，造型函数将被用来执行该转换，否则将产生一个标量值。对于任何不是数字、布尔、空值的标量类型，将使用文本表达，使其是一个有效的JSON值。|```
select to_json('Fred said "Hi."'::text)
```

|```
"Fred said \"Hi.\""
``` |
|to\_jsonb\(anyelement\)|
|array\_to\_json\(anyarray \[, pretty\_bool\]\)|此函数可以将数组作为一个JSON数组返回。一个PostgreSQL多维数组会成为一个数组的JSON数组。如果pretty\_bool为真，将在第1维度的元素之间增加换行。|```
select array_to_json('{{1,5},{99,100}}'::int[])
```

|```
[[1,5],[99,100]]
``` |
|row\_to\_json\(record \[, pretty\_bool\]\)|此函数可以将行作为一个 JSON对象返回。如果pretty\_bool为真，将在第1层元素之间增加换行。|```
select row_to_json(row(1,'foo'))
```

|```
{"f1":1,"f2":"foo"}
``` |
|json\_build\_array\(VARIADIC "any"\)|此函数可以从一个可变参数列表构造一个可能包含异质类型的JSON数组。|```
select json_build_array(1,2,'3',4,5)
```

|```
[1, 2, "3", 4, 5]
``` |
|jsonb\_build\_array\(VARIADIC "any"\)|
|json\_build\_object\(VARIADIC "any"\)|此函数可以从一个可变参数列表构造一个JSON对象。通过转换，该参数列表由交替出现的键和值构成。|```
select json_build_object('foo',1,'bar',2)
```

|```
{"foo": 1, "bar": 2}
``` |
|jsonb\_build\_object\(VARIADIC "any"\)|
|json\_object\(text\[\]\)|此函数可以从一个文本数组构造一个JSON对象。该数组必须可以是具有偶数个成员的一维数组（成员被当做交替出现的键/值对），或者是一个二维数组（每一个内部数组刚好有2个元素，可以被看做是键/值对）。|```
select json_object('{a, 1, b, "def", c, 3.5}');
```

|```
{"a": "1", "b": "def", "c": "3.5"}
``` |
|jsonb\_object\(text\[\]\)|```
select jsonb_object('{a, 1, b, "def", c, 3.5}');
```

|```
{"a": "1", "b": "def", "c": "3.5"}
``` |
|json\_object\(keys text\[\], values text\[\]\)|json\_object的这种形式从两个独立的数组得到键/值对。在其他方面和一个参数的形式相同。|```
select json_object('{a, b}', '{1,2}')
```

|```
{"a": "1", "b": "2"}
``` |
|jsonb\_object\(keys text\[\], values text\[\]\)|

## JSON处理函数

如下为可以用于处理JSON值的函数描述及操作示例。

|函数|返回值|描述|操作示例|执行结果|
|--|---|--|----|----|
|json\_array\_length\(json\)|int|返回最外层JSON数组中的元素数量。|```
select json_array_length('[1,2,3,{"f1":1,"f2":[5,6]},4]')
```

|```
5
``` |
|jsonb\_array\_length\(jsonb\)|
|json\_extract\_path\_text\(from\_json json, VARIADIC path\_elems text\[\]\)|text|以text返回由path\_elems指向的JSON值（等效于\#\>\>操作符）。|```
select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"foo"}}','f4', 'f6')
```

|```
foo
``` |
|jsonb\_extract\_path\_text\(from\_json jsonb, VARIADIC path\_elems text\[\]\)|
|json\_object\_keys\(json\)|setof text|返回最外层JSON对象中的键集合。|```
select json_object_keys('{"f1":"abc","f2":{"f3":"a", "f4":"b"}}')
```

|```
json_object_keys
------------------
 f1
 f2
``` |
|jsonb\_object\_keys\(jsonb\)|
|json\_populate\_record\(base anyelement, from\_json json\)|anyelement|扩展from\_json中的对象成一个行，它的列匹配由base定义的记录类型。|```
begin;
create table 
myrowtype( a text, b text, c text);commit;
select * from json_populate_record(null::myrowtype, '{"a": 1, "b": ["2", "a b"], "c": {"d": 4, "e": "a b c"}}')
```

|```
 a |   b       |      c
---+-----------+-------------
 1 | {2,"a b"} | {"d": 4, "e": "a b c"}
``` |
|jsonb\_populate\_record\(base anyelement, from\_json jsonb\)|
|json\_populate\_recordset\(base anyelement, from\_json json\)|setof anyelement|扩展from\_json中最外的对象数组为一个集合，该集合的列匹配由base定义的记录类型。|```
begin;
create table 
myrowtype(a text,b text);
commit;
select * from json_populate_recordset(null::myrowtype, '[{"a":1,"b":2},{"a":3,"b":4}]')
```

|```
 a | b
---+---
 1 | 2
 3 | 4
``` |
|jsonb\_populate\_recordset\(base anyelement, from\_json jsonb\)|
|json\_array\_elements\(json\)|setof json|把一个JSON数组扩展成一个JSON值的集合。|```
select * from json_array_elements('[1,true, [2,false]]')
```

|```
   value
-----------
 1
 true
 [2,false]
``` |
|jsonb\_array\_elements\(jsonb\)|setof jsonb|
|json\_array\_elements\_text\(json\)|setof text|把一个JSON数组扩展成一个text值集合。|```
select * from json_array_elements_text('["foo", "bar"]')
```

|```
 value
-----------
 foo
 bar
``` |
|jsonb\_array\_elements\_text\(jsonb\)|
|json\_typeof\(json\)|text|把最外层的JSON值的类型作为一个文本字符串返回。可能的类型是： object、array、string、number、 boolean以及null。|```
select json_typeof('-123.4')
```

|```
number
``` |
|jsonb\_typeof\(jsonb\)|
|json\_to\_record\(json\)|record|从一个JSON对象（见下文的注解）构建一个任意的记录。正如所有返回record的函数一样，调用者必须用一个AS子句显式地定义记录的结构。|```
create table jpop  (a text, b int, c timestamp);
select * from json_to_record('{"a":1, "b":{"c":16, "d":2}, "x":8, "ca": ["1 2", 3], "ia": [[1,2],[3,4]], "r": {"a": "aaa", "b": 123, "c": "2020-01-01 12:00:00.000"}}'::json)    as t(a int, b json, c text, x int, ca char(5)[], ia int[][], r jpop);
```

|```
a |        b        | c | x |        ca         |      ia       |                r

---+-----------------+---+---+-------------------+---------------+---------------------------------

1 | {"c":16, "d":2} |   | 8 | {"1 2  ","3    "} | {{1,2},{3,4}} | (aaa,123,"2020-01-01 12:00:00")
``` |
|jsonb\_to\_record\(jsonb\)|
|json\_to\_recordset\(json\)|setof record|从一个JSON对象数组（见下文的注解）构建一个任意的记录集合。正如所有返回record的函数一样，调用者必须用一个AS子句显式地定义记录的结构。|```
select * from json_to_recordset('[{"a":1,"b":"foo"},{"a":"2","c":"bar"}]') as x(a int, b text);
```

|```
 a |  b
---+-----
 1 | foo
 2 |
``` |
|jsonb\_to\_recordset\(jsonb\)|
|json\_strip\_nulls\(from\_json json\)|json|返回from\_json，其中所有具有空值的对象域都被省略。其他空值不动。|```
select json_strip_nulls('[{"f1":1,"f2":null},2,null,3]')
```

|```
[{"f1":1},2,null,3]
``` |
|jsonb\_strip\_nulls\(from\_json jsonb\)|jsonb|
|jsonb\_set\(target jsonb, path text\[\], new\_value jsonb\[,create\_missing boolean\]\)|jsonb|返回target，其中由path指定的节用new\_value替换，如果path指定的项不存在并且create\_missing为真（默认为 true）则加上new\_value。正如面向路径的 操作符一样，出现在path中的负整数表示从JSON数组的末尾开始数。|```
select jsonb_set('[{"f1":1,"f2":null},2,null,3]', '{0,f1}','[2,3,4]', false);
```

|```
[{"f1":[2,3,4],"f2":null},2,null,3]
``` |
|```
select jsonb_set('[{"f1":1,"f2":null},2]', '{0,f3}','[2,3,4]')
```

|```
[{"f1": 1, "f2": null, "f3": [2, 3, 4]}, 2]
``` |
|jsonb\_insert\(target jsonb, path text\[\], new\_value jsonb, \[insert\_after boolean\]\)|jsonb|返回被插入了new\_value的target。如果path指定的target节在一个JSONB数组中，new\_value将被插入到目标之前（insert\_after为false，默认情况）或者之后（insert\_after为真）。如果path指定的target节在一个JSONB对象内，则只有当target不存在时才插入new\_value。对于面向路径的操作符来说，出现在path中的负整数表示从JSON数组的末尾开始计数。|```
select jsonb_insert('{"a": [0,1,2]}', '{a, 1}', '"new_value"')
```

|```
{"a": [0, "new_value", 1, 2]}
``` |
|```
select jsonb_insert('{"a": [0,1,2]}', '{a, 1}', '"new_value"', true)
```

|```
{"a": [0, 1, "new_value", 2]}
``` |
|jsonb\_pretty\(from\_json jsonb\)|text|把from\_json返回成一段 缩进后的JSON文本。|```
select jsonb_pretty('[{"f1":1,"f2":null},2,null,3]')
```

|```
[
    {
        "f1": 1,
        "f2": null
    },
    2,
    null,
    3
]
``` |

更多关于JSON类型的用法，请参见[JSON函数和操作符](http://www.postgres.cn/docs/11/functions-json.html)。

