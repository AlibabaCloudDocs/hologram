---
keyword: [Hologres, date and time function]
---

# Date and time functions

Compatible with PostgreSQL, Hologres allows you to use standard PostgreSQL syntax for data development.

The following table describes the date and time functions supported by Hologres. The functions supported by Hologres are only a subset of the PostgreSQL functions. For more information about how to use these functions, see [Date/Time Functions and Operators](https://www.postgresql.org/docs/11/functions-datetime.html) and [Data Type Formatting Functions](https://www.postgresql.org/docs/11/functions-formatting.html) in the PostgreSQL documentation.

|Function|Description|Example|Result|
|--------|-----------|-------|------|
|to\_char\(timestamp, text\)|Converts a timestamp to a string.|to\_char\(current\_timestamp, 'HH12:MI:SS'\)|06:26:33|
|to\_char\(int, text\)|Converts an integer to a string.|to\_char\(125, '999'\)|125|
|to\_char\(double precision, text\)|Converts a real number or a double precision number to a string.|to\_char\(125.8::real, '999D9'\)|125.8|
|to\_date\(text, text\)|Converts a string to the DATE data type.|to\_date\('05 Dec 2000', 'DD Mon YYYY'\)|2000-12-05|
|to\_number\(text, text\)|Converts a string to a number.|to\_number\('12,454.8-', '99G999D9S'\)|-12454.8|
|to\_timestamp\(text, text\)|Converts a string to a timestamp.|to\_timestamp\('05 Dec 2000', 'DD Mon YYYY'\)|2000-12-05 00:00:00+08|
|date - date|Calculates the number of days between two dates.|date '2001-10-01' - date '2001-09-28'|3|
|clock\_timestamp\(\)|Returns the current time.|clock\_timestamp\(\)|2020-05-03 06:32:28.814918+08|
|current\_date|Returns the current date.|current\_date|2020-05-03|
|current\_timestamp|Returns the current timestamp.|current\_timestamp|2020-05-03 06:33:36.113682+08|
|date\_part\(text, timestamp\)|Returns a portion of a timestamp based on its arguments.|date\_part\('hour', timestamp '2001-02-16 20:38:40'\)|20|
|date\_trunc\(text, timestamp\)|Truncates a timestamp to a specified precision.|date\_trunc\('hour', timestamp '2001-02-16 20:38:40'\)|2001-02-16 20:00:00|
|extract\(field from timestamp\)|Returns a portion of a timestamp based on its arguments.|extract\(hour from timestamp '2001-02-16 20:38:40'\)|20|
|isfinite\(date\)|Determines whether a date is finite.|isfinite\(date '2001-02-16'\)|true|
|isfinite\(timestamp\)|Determines whether a timestamp is finite.|isfinite\(timestamp '2001-02-16 21:28:30'\)|true|
|isfinite\(interval\)|Determines whether an interval is finite.|isfinite\(interval '4 hours'\)|true|
|make\_date\(year int, month int, day int\)|Creates a date from its separate year, month, and day fields.|make\_date\(2013, 7, 15\)|2013-07-15|
|localtimestamp|Returns the current time that does not contain the time zone information.|localtimestamp|2020-08-21 12:02:21.178031|
|now\(\)|Returns the current timestamp.|now\(\)|2020-05-03 06:38:48.492168+08|
|statement\_timestamp\(\)|Returns the start time of the current statement.|statement\_timestamp\(\)|2020-05-05 06:39:11.125957+08|
|timeofday\(\)|Returns the current time.**Note:** The returned value is a formatted string.

|timeofday\(\)|Tue May 03 06:39:43.195368 2020 CST|
|transaction\_timestamp\(\)|Returns the current timestamp.|transaction\_timestamp\(\)|2020-05-03 06:40:08.023623+08|
|to\_timestamp\(double precision\)|Converts a Unix epoch value to a timestamp.**Note:** This Unix epoch value represents the number of seconds that have elapsed since the epoch time January 1, 1970, 00:00:00 UTC.

|to\_timestamp\(1284352323\)|2010-09-13 04:32:03+00|

