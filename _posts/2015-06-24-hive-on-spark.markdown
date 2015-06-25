---
layout: post
title:  "SparkSQL with Hive (Setup & Performance)"
date:   2015-06-24 19:37:12
categories: Spark
---

Spark-sql has now provided compatibility with hive, where you can directly create a hive context within spark which connects to hive metastore and lets you access all the hive metadata (database and table info) from spark. 

You can check the release info from [https://spark.apache.org/sql](https://spark.apache.org/sql)
&nbsp;

###SETUP : ###

The default release of Spark-Sql does not contain hive compatibility and it prompts you to build your very own distribution of spark from the source code.

**To build a Hive compatible Spark distro :**

`1. ./make-distribution.sh --name custom-spark-hive --tgz -Dhadoop.version=2.5.0-cdh5.3.3 -Pyarn -Phive -Phive-thriftserver -DskipTests`
    
   * make-distribution is the script bundled in the spark code which lets you build deployable spark distros
   * hadoop.version is the hadoop version with which you want your spark distro to be compatible with. In my case, am running a hadoop cluster with cloudera 5.3.3 which is spark-1.2 compatible
   * Provide yarn profile if you want to run your spark cluster on yarn
   * Providing profiles hive and hive-thriftserver lets you build your code with hive capability

This will make a hive compatible spark distro for you.

`2. Make sure you have HADOOP_CONF_DIR set and this contains your hive-site.xml`

Spark reads HiveServer, HiveMetaStore and various other hive related configurations from _hive-site.xml_.

`3. Than as you run your spark-shell, make sure it adds the hadoop jars and all the jars in your distros lib directory to its classpath.`

To check for these just do a _ps -ef | grep spark_ and it will list the jars in the classpath. 
    Make sure there are no conflicts.

**To run Hive queries on spark :**

{% highlight ruby %}
 val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
val results = sqlContext.sql("Your Query")
 {% endhighlight %}

You can get more information about SparkSQL support for Hive from [https://spark.apache.org/docs/latest/sql-programming-guide.html#hive-tables](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark-Sql)

Also with Spark-1.3, SparkSQL has introduced a new DataFrame API that provides powerful and convenient operators when working with structured datasets. With this in place after the data loaded from Hive you can easily perform complex processing in a much simpler manner, then having to write complex UDF and UDAF's.

--------------------------------------------------

&nbsp;

###PERFORMANCE : ###

Its difficult to quantify performance. But in my case I got more than **double** the performance.<br/>
For a hive query which used to take 20 min on a 10 node cluster, got executed within 10 min in a spark-shell that too in standalone mode (single machine).

The query being a complex one with 4-5 joins with group by operating over a dataset of about 5GB, 2GB, 1GB (which was getting multiplied because of the nature of query) was flawlessly preocessed in spark. Although being in standalone I could not benefit from the data-locality which my hive shell was.
