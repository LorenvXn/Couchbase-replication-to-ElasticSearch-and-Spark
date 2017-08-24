
Time to replicate data from Couchbase to ElasticSearch

After implementing the environment:

a) from Couchbase web-interface (http:/[container IP]:8091),go to <b> XDCR -> Create Cluster Reference -> Edit </b>,
and add the cluster's name, container's IP, credentials:

![ScreenShot](https://github.com/Satanette/test/blob/master/_replica_setup.png)

b) Now, go to <b> Ongoing Replications -> Create Replication ->  add beer-sample at Bucket -> change the value of the XDCR Protocol to 1</b>

It should be looking like this:


![ScreenShot](https://github.com/Satanette/test/blob/master/final.png)

Now, let's test what we have done so far: https://github.com/Satanette/Couchbase-replication-to-ElasticSearch-and-Spark/blob/master/Spark%20and%20ElasticSearch.md
