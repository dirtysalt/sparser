# sparser

This code base implements Sparser, raw filtering for faster analytics over raw data. Sparser can parse JSON, Avro, and Parquet data up to 22x faster than the state of the art. For more details, check out [our paper published at VLDB 2018](http://www.vldb.org/pvldb/vol11/p1576-palkar.pdf).

See the `demo-repl` directory for a brief example. To run it:

    # update rapidjson submodule
    git submodule init
    git submodule update
    cd demo-repl
    make
    ./bench /path/to/large/file.json

Then enter `1` at the `Sparser>` prompt.

Sparser itself is just a header file and only depends on standard C libraries available
on most systems.

# BUGS(yan)

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

FIX: 这个bug出在 `calibration` 和 `search` 阶段之间. `calibration`之后没有将 `cdata.count` 清零

```
diff --git a/demo-repl/query_driver.cpp b/demo-repl/query_driver.cpp
index 33a6a83..8324092 100644
--- a/demo-repl/query_driver.cpp
+++ b/demo-repl/query_driver.cpp
@@ -78,6 +78,7 @@ double bench_sparser_engine(char *data, long length, json_query_t jquery,
     sparser_query_t *query = sparser_calibrate(
         data, length, '\n', predicates, _rapidjson_parse_callback, &cdata);

+		cdata.count = 0;
     // XXX Apply the search.
     sparser_stats_t *stats = sparser_search(data, length, '\n', query,
                                             _rapidjson_parse_callback, &cdata);

```
