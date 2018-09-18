# Demo REPL

A demo REPL that shows how to use sparser.

The typical way to use the system is:

1. Use `decompose` to get a set of RFs from a list of string tokens.

2. Use `sparser_calibrate` to get a `sparser_query_t`. This represents a
   schedule that the system uses to search the input.

3. Use `sparser_search` to perform the actual search.

Users can pass a callback (a function pointer) to both functions. The callback
returns a non-zero value if  a filter passes in `sparser_calibrate`. In
`sparser_search`, the callback can perform any user-defined action (e.g.,
processing the record, storing a pointer to the record for later processing,
etc.). For example, the first example query here just counts the number of
passing records after applying a full JSON parser.

# NOTES(yan)

我在目录下面创建了sample.json, 看上去现在sparser还是有bug

```
➜  demo-repl git:(sparser-opensource) ✗ ./bench sample.json
Reading data...done! read 0.000000 GB
Sparser> 1
Running query:
 ---------------------
SELECT count(*)
FROM tweets
WHERE text contains "Trump" AND text contains "Putin"
 ---------------------
RapidJSON with Sparser:	Result: 2 (Execution Time: 0.000106 seconds)
RapidJSON:		Result: 1 (Execution Time: 0.000010 seconds)
```

其中sample.json如下，正确输出应该是1

```json
{"text":"hello"}
{"text":"it's Trump time"}
{"text":"No way, Trump will win"}
{"text":"Maybe Putin likes it"}
{"text":"world ends"}
{"text":"Maybe Putin likes Trump"}

```
