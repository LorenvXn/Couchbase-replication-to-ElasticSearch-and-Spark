1) Make sure you have the environment already set-up. If not, go here:
 [Spark and Couchbase integration on dockers](https://github.com/Satanette/Spark-Couchbase-Integration-on-Dockers-) 


2) Prepare environment to transfer data from Couchbase to ElasticSearch <i> (well...most of implementation manually done... I haven't had much time on my hands lately... )</i>

This has been implemented on same Couchbase container. 
<b>Please be aware that the dependecies/plug-ins correspond to this particular environment</b>


Implementation based on this link: https://developer.couchbase.com/documentation/server/current/connectors/elasticsearch-2.2/overview.html 

2.1) From Couchbase container, install Couchbase transport plug-in:

a) Download ElasticSearch 2.4.0 for Ubuntu:

```
root@e61e42e5b2b8:/# wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.4.0/elasticsearch-2.4.0.deb
```

..and install package:
```
root@e61e42e5b2b8:/# dpkg -i elasticsearch-2.4.0.deb
```

b) Go to /usr/share/elasticsearch and install the plug-in package:
```
root@e61e42e5b2b8:/# cd /usr/share/elasticsearch
root@e61e42e5b2b8:/usr/share/elasticsearch# bin/plugin install https://github.com/couchbaselabs/elasticsearch-transport-couchbase/releases/download/2.2.4.0-update1/elasticsearch-transport-couchbase-2.2.4.0-update1.zip
```

c) Install vim and modify /etc/elasticsearch/elasticsearch.yaml with following lines:

```
# Set the bind address to a specific IP (IPv4 or IPv6):
#
 network.host: 172.17.0.2 # or whatevs IP your Couchbase container has
#
# Set a custom port for HTTP:
#
 http.port: 9200


 couchbase.password: <insert password here>
 couchbase.username: Administrator
 couchbase.maxConcurrentRequests: 1024

```
d) Install web-based user-interface

```
bin/plugin install mobz/elasticsearch-head --verbose
```

2.2) start Elasticsearch:

```
/etc/init.d/elasticsearch start
```

f) check if something listens on that port

```
root@e61e42e5b2b8:/# netstat -tenpula | grep 9200
tcp        0      0 172.17.0.2:9200         0.0.0.0:*               LISTEN      102        26589       -    

```
g) configure an ElasticSearch Index, and instantiate an elasticsearch index
to be applied by Elasticsearch to data from a particular Couchbase bucket 

```
 curl -XPUT http://localhost:9200/_template/couchbase -d  @plugins/transport-couchbase/couchbase_template.json
 
 curl -XPUT http://localhost:9200/beer-sample
 ```

Now, time to replicate data from Couchbase to Elasticsearch: [XDCR Create Cluster](https://github.com/Satanette/Couchbase-replication-to-ElasticSearch-and-Spark/blob/master/XDCR_CreateCluster.md)
