---
keyword: [Hologres, array functions]
---

# Array functions

Hologres is compatible with PostgreSQL and allows you to use standard PostgreSQL syntax for data development.

The following table describes the array functions that are supported by Hologres. Hologres supports a subset of PostgreSQL functions. For information about how to use the following array functions, see [Array Functions and Operators](https://www.postgresql.org/docs/11/functions-array.html).

|Function|Description|Example|Result|
|--------|-----------|-------|------|
|array\_append\(anyarray, anyelement\)|Appends an element to the end of an array.|array\_append\(ARRAY\[1,2\], 3\)|\{1,2,3\}|
|array\_cat\(anyarray,anyarray\)|Concatenates two arrays.|array\_cat\(ARRAY\[1,2,3\], ARRAY\[4,5\]\)|\{1,2,3,4,5\}|
|array\_ndims\(anyarray\)|Returns the number of dimensions of an array.|array\_ndims\(ARRAY\[\[1,2,3\], \[4,5,6\]\]\)|2|
|array\_dims\(anyarray\)|Returns a text representation of the dimensions of an array.|array\_dims\(ARRAY\[\[1,2,3\], \[4,5,6\]\]\)|\[1:2\]\[1:3\]|
|array\_length\(anyarray, int\)|Returns the length of the requested array dimension.|array\_length\(array\[1,2,3\], 1\)|3|
|array\_lower\(anyarray, int\)|Returns the lower bound of the requested array dimension.|array\_lower\('\[0:2\]=\{1,2,3\}'::int\[\], 1\)|0|
|array\_positions\(anyarray, anyelement\)|Returns an array of subscripts of all occurrences of the second argument in the array given as the first argument. The array given as the first argument must be one-dimensional.|array\_positions\(ARRAY\['A','A','B','A'\], 'A'\)|\{1,2,4\}|
|array\_prepend\(anyelement, anyarray\)|Prepends an element to the start of an array.|array\_prepend\(1, ARRAY\[2,3\]\)|\{1,2,3\}|
|array\_remove\(anyarray, anyelement\)|Removes all elements that are equal to the given value from an array.|array\_remove\(ARRAY\[1,2,3,2\], 2\)|\{1,3\}|
|array\_to\_string\(anyarray,text\[,text\]\)|Concatenates array elements by using a specified delimiter and an optional null string.|array\_to\_string\(ARRAY\[1, 2, 3, NULL, 5\], ',', '\*'\)|1,2,3,\*,5|
|array\_upper\(anyarray, int\)|Returns the upper bound of the requested array dimension.|array\_upper\(ARRAY\[1,8,3,7\], 1\)|4|
|unnest\(anyarray\)|Expands an array to a set of rows.|unnest\(ARRAY\[1,2\]\)|1

2

\(2 rows\) |

