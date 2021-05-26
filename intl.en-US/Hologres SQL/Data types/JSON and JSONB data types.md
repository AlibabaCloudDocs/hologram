---
keyword: [Hologres, JSON, JSONB]
---

# JSON and JSONB data types

This topic describes the syntax supported by the JSON and JSONB data types and how to use these data types.

## Limits

Hologres supports the JSON and JSONB data types. Take note of the following rules when you use them:

-   Only Hologres V0.9 and later support the JSON and JSONB data types. To upgrade an instance, [submit a ticket](https://workorder-intl.console.aliyun.com/) or join the Hologres DingTalk group for technical support.
-   Hologres does not support the indexing feature of the JSON and JSONB data types.
-   Hologres does not support the following JSON-specific functions: json\_each, jsonb\_each, json\_each\_text, jonbs\_each\_text, json\_extract\_path, jsonb\_extract\_path, jsonb\_agg, jsonb\_each, and jsonb\_to\_record.
-   You cannot write data of the JSONB type from Flink to Hologres.

## Overview of the JSON and JSONB data types

The major practical difference between these two data types is their efficiency.

-   The JSON data type stores an exact copy of the input text. The processing functions must reparse the copy during each execution. In addition, insignificant characters such as spaces may exist in data of the JSON type to comply with the semantic constraints of the input text. If a JSON object within a value contains the same key more than once, all the key-value pairs are kept. The processing functions consider the last value as the operative one.
-   Data of the JSONB type is stored in a decomposed binary format. Compared with data of the JSON type, data of the JSONB type takes more time to be imported due to the additional conversion process. However, the processing of JSONB data takes less time because no reparsing is required. Spaces, the order of object keys, and duplicate object keys do not exist in JSONB data. If duplicate object keys are specified in the input text, only the last value is retained.

**Note:** Keys must be enclosed in double quotation marks \("\).

## Operators supported by JSON and JSONB

The JSON and JSONB data types are used to store JSON data. Such data can also be stored as the TEXT type. The JSON and JSONB data types have the advantage of enforcing that each stored value is valid based on the JSON rules. The following table describes the operators that are supported by these two data types.

|Operator|Right operand type|Description|Example|Result|
|--------|------------------|-----------|-------|------|
|-\>|int|Obtains a JSON array element with indexes starting from zero and negative integers counted from the end.|`select '[{"a":"foo"},{"b":"bar"},{"c":"baz"}]'::json->2`|`{"c":"baz"}`|
|-\>|text|Obtains a JSON object field based on a key.|`select '{"a": {"b":"foo"}}'::json->'a'`|`{"b":"foo"}`|
|-\>\>|int|Obtains a JSON array element as text.|`select '[1,2,3]'::json->>2`|`3`|
|-\>\>|text|Obtains a JSON object field as text.|`select '{"a":1,"b":2}'::json->>'b'`|`2`|
|\#\>|text\[\]|Obtains a JSON object from a specified path.|`select '{"a": {"b":{"c": "foo"}}}'::json#>'{a,b}'`|`{"c":"foo"}`|
|\#\>\>|text\[\]|Obtains a JSON object as text from a specified path.|`select '{"a":[1,2,3],"b":[4,5,6]}'::json#>>'{a,2}'`|`3`|

## Additional JSONB operators

The following table describes the operators that are supported by the JSONB data type.

|Operator|Right operand type|Description|Example|Result|
|--------|------------------|-----------|-------|------|
|@\>|jsonb|Specifies whether the left JSON value contains the right JSON value.|`select '{"a":1, "b":2}'::jsonb @> '{"b":2}'::jsonb`|`true`|
|<@|jsonb|Specifies whether the left JSON value is contained within the right JSON value.|`select '{"b":2}'::jsonb <@ '{"a":1, "b":2}'::jsonb`|`true`|
|?|text|Specifies whether a string exists as a key or as a string within the JSON value.|`select '{"a":1, "b":2}'::jsonb ? 'b'`|`true`|
|? \||text\[\]|Specifies whether one of the right array strings exists as a key or as a string within the JSON value.|`select '{"a":1, "b":2, "c":3}'::jsonb ? | array['b', 'c']`|`true`|
|? &|text\[\]|Specifies whether all of the right array strings exist as keys or as strings within the JSON value.|`select '["a", "b"]'::jsonb ? & array['a', 'b']`|`true`|
|\|\||jsonb|Concatenates two JSONB values into a new JSONB value.|`select '["a", "b"]'::jsonb || '["c", "d"]'::jsonb`|`["a", "b", "c", "d"]`|
|-|text|Deletes a key-value pair or string element from the left operand. Key-value pairs are matched based on the values.|`select '{"a": "b"}'::jsonb - 'a'`|`{}`|
|-|text\[\]|Deletes multiple key-value pairs or string elements from the left operand. Key-value pairs are matched based on the values.|`select '{"a": "b", "c": "d"}'::jsonb - '{a,c}'::text[]`|`{}`|
|-|integer|Deletes an array element that has a specified index. Negative integers are counted from the end. An error is returned if the top level container is not an array.|`select '["a", "b"]'::jsonb - 1`|`["a"]`|
|\#-|text\[\]|Deletes a field or an element with a specified path. For JSON arrays, negative integers are counted from the end.|`select '["a", {"b":1}]'::jsonb #- '{1,b}'`|`["a", {}]`|

## JSON creation functions

The following table describes the functions used to create JSON values.

|Function|Description|Example|Result|
|--------|-----------|-------|------|
|to\_json\(anyelement\)|Returns a value as a valid JSON object. Arrays and composites are converted recursively to arrays and objects. If a cast function is provided, that cast function is invoked to convert the input value into a JSON object. Otherwise, a scalar value is generated. If the scalar value is not a number, BOOLEAN value, or null, it is represented by JSON text that makes it a valid JSON string.|`select to_json('Fred said "Hi."'::text)`|`"Fred said \"Hi. \""`|
|to\_jsonb\(anyelement\)|
|array\_to\_json\(anyarray \[, pretty\_bool\]\)|Returns an array as a JSON array. If you enter a multidimensional array, a JSON array of arrays is returned. If the value of the pretty\_bool parameter is true, line feeds are added between dimension-1 elements.|`select array_to_json('{{1,5},{99,100}}'::int[])`|`[[1,5],[99,100]]`|
|json\_build\_array\(VARIADIC "any"\)|Builds a JSON array that may contain heterogeneous data based on a list of variable arguments.|`select json_build_array(1,2,'3',4,5)`|`[1, 2, "3", 4, 5]`|
|jsonb\_build\_array\(VARIADIC "any"\)|
|json\_build\_object\(VARIADIC "any"\)|Builds a JSON object based on a list of variable arguments. The argument list consists of alternating keys and values.|`select json_build_object('foo',1,'bar',2)`|`{"foo": 1, "bar": 2}`|
|jsonb\_build\_object\(VARIADIC "any"\)|
|json\_object\(text\[\]\)|Builds a JSON object based on a text array. The array can contain one dimension that has an even number of members. The members are taken as alternating key-value pairs. Alternatively, the array can contain two dimensions and each inner array has two elements, which are taken as a key-value pair.|`select json_object('{a, 1, b, "def", c, 3.5}');`|`{"a": "1", "b": "def", "c": "3.5"}`|
|jsonb\_object\(text\[\]\)|`select jsonb_object('{a, 1, b, "def", c, 3.5}');`|`{"a": "1", "b": "def", "c": "3.5"}`|
|json\_object\(keys text\[\], values text\[\]\)|Obtains key-value pairs based on two separate arrays. In other respects, the result is in the same format as that of a single argument.|`select json_object('{a, b}', '{1,2}')`|`{"a": "1", "b": "2"}`|
|jsonb\_object\(keys text\[\], values text\[\]\)|

## JSON processing functions

The following table describes the functions used to process JSON values.

|Function|Return value type|Description|Example|Result|
|--------|-----------------|-----------|-------|------|
|json\_array\_length\(json\)|int|Returns the number of elements in the outermost JSON array.|`select json_array_length('[1,2,3,{"f1":1,"f2":[5,6]},4]')`|`5`|
|jsonb\_array\_length\(jsonb\)|
|json\_extract\_path\_text\(from\_json json, VARIADIC path\_elems text\[\]\)|text|Returns the JSON value specified by the path\_elems parameter as JSON text. This function is equivalent to the \#\>\> operator.|`select json_extract_path_text('{"f2":{"f3":1},"f4":{"f5":99,"f6":"foo"}}','f4', 'f6')`|`foo`|
|jsonb\_extract\_path\_text\(from\_json jsonb, VARIADIC path\_elems text\[\]\)|
|json\_object\_keys\(json\)|setof text|Returns a set of keys in the outermost JSON object.|`select json_object_keys('{"f1":"abc","f2":{"f3":"a", "f4":"b"}}')`|```
json_object_keys
------------------
 f1
 f2
``` |
|jsonb\_object\_keys\(jsonb\)|
|json\_populate\_record\(base anyelement, from\_json json\)|anyelement|Expands the objects in the from\_json parameter into a row with columns that match the record type defined by the base parameter.|```
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
|json\_populate\_recordset\(base anyelement, from\_json json\)|setof anyelement|Expands the outermost array of objects in the from\_json parameter into a set of rows with columns that match the record type defined by the base parameter.|```
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
|json\_array\_elements\(json\)|setof json|Expands a JSON array into a set of JSON values.|`select * from json_array_elements('[1,true, [2,false]]')`|```
   value
-----------
 1
 true
 [2,false]
``` |
|jsonb\_array\_elements\(jsonb\)|setof jsonb|
|json\_array\_elements\_text\(json\)|setof text|Expands a JSON array into a set of text values.|`select * from json_array_elements_text('["foo", "bar"]')`|```
 value
-----------
 foo
 bar
``` |
|jsonb\_array\_elements\_text\(jsonb\)|
|json\_typeof\(json\)|text|Returns the type of the outermost JSON value as a text string. Possible types include OBJECT, ARRAY, STRING, NUMBER, BOOLEAN, and NULL.|`select json_typeof('-123.4')`|`number`|
|jsonb\_typeof\(jsonb\)|
|json\_to\_record\(json\)|record|Builds an arbitrary record based on a JSON object. As required by those functions that return records, you must explicitly define the structure of the record by using an AS clause.|`create table jpop (a text, b int, c timestamp);select * from json_to_record('{"a":1, "b":{"c":16, "d":2}, "x":8, "ca": ["1 2", 3], "ia": [[1,2],[3,4]], "r": {"a": "aaa", "b": 123, "c": "2020-01-01 12:00:00.000"}}'::json) as t(a int, b json, c text, x int, ca char(5)[], ia int[][], r jpop);`|```
a |        b        | c | x |        ca         |      ia       |                r

---+-----------------+---+---+-------------------+---------------+---------------------------------

1 | {"c":16, "d":2} |   | 8 | {"1 2  ","3    "} | {{1,2},{3,4}} | (aaa,123,"2020-01-01 12:00:00")
``` |
|jsonb\_to\_record\(jsonb\)|
|json\_to\_recordset\(json\)|setof record|Builds an arbitrary set of records based on a JSON array of objects. As required by those functions that return records, you must explicitly define the structure of the record by using an AS clause.|`select * from json_to_recordset('[{"a":1,"b":"foo"},{"a":"2","c":"bar"}]') as x(a int, b text);`|```
 a |  b
---+-----
 1 | foo
 2 |
``` |
|jsonb\_to\_recordset\(jsonb\)|
|json\_strip\_nulls\(from\_json json\)|json|Returns the objects in the from\_json parameter. Object fields that have null values are omitted. Other null values are retained.|`select json_strip_nulls('[{"f1":1,"f2":null},2,null,3]')`|`[{"f1":1},2,null,3]`|
|jsonb\_strip\_nulls\(from\_json jsonb\)|jsonb|
|jsonb\_set\(target jsonb, path text\[\], new\_value jsonb\[,create\_missing boolean\]\)|jsonb|Returns the objects in the target parameter. The section specified by the path parameter is replaced by the value of the new\_value parameter. If the value of the create\_missing parameter is true, which is the default value, and the item specified by the path parameter does not exist, the value of the new\_value parameter is inserted. As required by the path-oriented operators, negative integers that appear in the value of the path parameter are counted from the end of JSON arrays.|`select jsonb_set('[{"f1":1,"f2":null},2,null,3]', '{0,f1}','[2,3,4]', false);`|`[{"f1":[2,3,4],"f2":null},2,null,3]`|
|`select jsonb_set('[{"f1":1,"f2":null},2]', '{0,f3}','[2,3,4]')`|`[{"f1": 1, "f2": null, "f3": [2, 3, 4]}, 2]`|
|jsonb\_insert\(target jsonb, path text\[\], new\_value jsonb, \[insert\_after boolean\]\)|jsonb|Returns the objects in the target parameter with the value of the new\_value parameter inserted. Assume that the section specified by the path parameter is in a JSONB array. If the value of the insert\_after parameter is false, which is the default value, the value of the new\_value parameter is inserted before the value of the target parameter. Otherwise, the value of the new\_value parameter is inserted after the value of the target parameter. Assume that the section specified by the path parameter is in a JSONB object. The value of the new\_value parameter is inserted only when the value of the target parameter does not exist. As required by the path-oriented operators, negative integers that appear in the value of the path parameter are counted from the end of JSON arrays.|`select jsonb_insert('{"a": [0,1,2]}', '{a, 1}', '"new_value"')`|`{"a": [0, "new_value", 1, 2]}`|
|`select jsonb_insert('{"a": [0,1,2]}', '{a, 1}', '"new_value"', true)`|`{"a": [0, 1, "new_value", 2]}`|
|jsonb\_pretty\(from\_json jsonb\)|text|Returns the objects in the from\_json parameter as indented JSON text.|`select jsonb_pretty('[{"f1":1,"f2":null},2,null,3]')`|```
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

For more information about the JSON and JSONB data types, see [JSON functions and operators](http://www.postgres.cn/docs/11/functions-json.html).

