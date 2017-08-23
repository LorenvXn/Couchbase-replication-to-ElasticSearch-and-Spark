
Once everything has been implemented successfully, we can proceed with Spark and ElasticSearch:


1) Start Spark Shell, with ElasticSearch package <i> org.elasticsearch:elasticsearch-spark-20_2.10:5.4.0</i>

```
bash-4.1#
bash-4.1# spark-shell --packages org.elasticsearch:elasticsearch-spark-20_2.10:5.4.0

[=======snip=======]

Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 1.6.0
      /_/

[=======snip=======]

scala>
scala>
```

ElasticSearch Index "beer-sample/couchbaseDocument" will be used as example.

From spark-shell run the following scala lines:

```


import org.apache.spark.SparkConf
import org.elasticsearch.spark._
import org.apache.spark.sql.SQLContext
import org.apache.spark.{SparkConf, SparkContext}
import org.elasticsearch.spark.sql._

val indexy ="beer-sample/couchbaseDocument"


 val link = "172.17.0.2:9200"


val reader=sqlContext.read.format("org.elasticsearch.spark.sql")
                          .option("es.net.http.auth.user", "Administrator")
                          .option("es.net.http.auth.pass", "<insert password here>")
                          .option("es.nodes", link)

val df = reader.load(indexy)


```

Let's print the Schema:

```
scala> df.printSchema()
root
 |-- doc: struct (nullable = true)
 |    |-- abv: double (nullable = true)
 |    |-- address: string (nullable = true)
 |    |-- brewery_id: string (nullable = true)
 |    |-- category: string (nullable = true)
 |    |-- city: string (nullable = true)
 |    |-- code: string (nullable = true)
 |    |-- country: string (nullable = true)
 |    |-- description: string (nullable = true)
 |    |-- geo: struct (nullable = true)
 |    |    |-- accuracy: string (nullable = true)
 |    |    |-- lat: double (nullable = true)
 |    |    |-- lon: double (nullable = true)
 |    |-- ibu: double (nullable = true)
 |    |-- name: string (nullable = true)
 |    |-- phone: string (nullable = true)
 |    |-- srm: double (nullable = true)
 |    |-- state: string (nullable = true)
 |    |-- style: string (nullable = true)
 |    |-- type: string (nullable = true)
 |    |-- upc: long (nullable = true)
 |    |-- updated: string (nullable = true)
 |    |-- website: string (nullable = true)
 |-- meta: struct (nullable = true)
 |    |-- expiration: long (nullable = true)
 |    |-- flags: long (nullable = true)
 |    |-- id: string (nullable = true)
 |    |-- rev: string (nullable = true)

```

Create a temporary table:


```
scala> df.registerTempTable("pampam")
scala> val beerpow = sqlContext.sql("Select * from pampam where meta.flags=33554438")
```

... and let's extract data

```

scala> beerpow.map(t=>"Count: " + t(0) + " " + t(1)).collect().foreach(println)

[=======snip=======]

Count: null [0,33554438,anchor_brewing-our_special_ale_1997,1-14d92c02720a00000000000002000006]
Count: null [0,33554438,o_fallon_brewery-griesendiech_pilsner,1-14d92c02901f00020000000002000006]
Count: null [0,33554438,river_west_brewing-light,1-14d92c02594500000000000002000006]
Count: null [0,33554438,brauerei_gasthof_zur_krone-kronenbier,1-14d92c02594500010000000002000006]
Count: null [0,33554438,issaquah_brewhouse-oak_stout,1-14d92c02d3b300000000000002000006]
Count: null [0,33554438,brasserie_de_l_abbaye_des_rocs-ambree,1-14d92c02640700000000000002000006]
Count: null [0,33554438,ship_inn_brewpub-best_bitter,1-14d92c02670800000000000002000006]
Count: null [0,33554438,egan_brewing-abbot_pennings_grand_cru,1-14d92c028ddb00000000000002000006]
Count: null [0,33554438,fuller_smith_turner_pbc-london_pride,1-14d92c02677200000000000002000006]
Count: null [0,33554438,slab_city_brewing-shawano_gold,1-14d92c0249e100000000000002000006]
Count: null [0,33554438,brouwerij_van_steenberge-ertvelds_wit,1-14d92c02904e00000000000002000006]
Count: null [0,33554438,titletown_brewing-belgian_summer_ale,1-14d92c02d3d200000000000002000006]
Count: null [0,33554438,brasserie_des_cimes-yeti,1-14d92c02721700000000000002000006]
Count: null [0,33554438,bamberger_mahr_s_bru-pilsner,1-14d92c024a1700010000000002000006]
[=======snip=======]
```

And, if we go to http://[container IP]:9200/_plugin/head/ ... we can see our result look alike:

![ScreenShot](https://github.com/Satanette/test/blob/master/dodge_this_2.png.png)
