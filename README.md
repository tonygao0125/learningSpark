# learningSpark

## Introduction to Spark
### Background
#### Hadoop
First open-source project for distributed storage, resource management and computing. Consist of 3 components:
1. HDFS -> Distributed Storage
2. YARN -> Resource Management 
3. Map Reduce -> Computing in parallel process (mostly written in Java or can also written in Python)
- Hadoop 1.0: consists of HDFS and Map Reduce (taking care of resource management)
- Hadoop 2.0: inclusive of YARN
- Hadoop 3.0: Improved storage/HDFS layer

***Spark start to emerge and dominate over MapReduce in market, for a few reasons:*** 
1. Spark is In-Memory(RAM) processing framework, whereas MapReduce process in HDD/SSD, thus Spark has better performance than MapReduce. 
2. Hadoop MapReduce performance is related to HDD/SSD space. Say working on 50 hadoop node clusters (50 servers connected in parallel), if the hard disks space of those servers getting small, the CPU utilization will be affected and computing performance will drop, unless more and more clusters are continuously connected in parallel (horizontal scaling up). 
3. Since Hadoop makes use of servers for both computing and storage, the servers cannot be shut down, otherwise there will be data loss. So the deployment on cloud is expensive, as the node clusters must be running always. A better choice is to separate computing and storage.
4. Memory card becomes cheaper nowadays.
**Spark can run in standalong mode, then we can have separate storage, so we don't have to integrate it with Hadoop.**

#### Spark support four langurages:
- Scala (Most widely used since Spark itself is written in Scala, better integration) -> spark-shell
- Java (2nd most popular lang for data engineering) -> No shell, no CLI
- Python (Mostly for data science application) -> pyspark
- R (less used) -> sparkR

#### Definition
Spark is an unified computing engine for parallel data processing on clusters. **It supports batch processing, streaming processing, ML and SQL queries.** 

#### Low Level APIs of Spark
1. RDD - Resilient Dsitributed Datasets (it's like a row of data/record)
2. Broadcast (less used)
3. Accumulator (less used)

#### High Level APIs of Spark - Structured APIs
1. Dataframe - no fixed schema 
2. SQL - SQL statement for dataframe/dataset control (data query, definition, manipulation and control: DQL, DDL, DML, DCL)
3. Dataset - has fixed defined schema 

#### On top of High Level APIs, it supports: 
1. Structured Streaming. 
2. Advanced Analytics. (for ML use)
3. Other Libraries.

#### Versions
- 2014 -> 1.0 (1.6) -> RDD
- 2016 -> 2.0 (2.1, 2.4) -> Dataframe, Dataset
- 2020 -> 3.0 -> Faster speed and more ML libraries

#### Driver vs Executor
- Spark Driver is the central coordinator and it communicates with all the Workers. It controls the flow of program. 
- Each Worker node consists of one or more Executor(s) who are responsible for running the Task. Executors register themselves with Driver. The Driver has all the information about the Executors at all the time.
- A Spark Application consists of a Driver Program and a group of Executors on the cluster.
- The Driver is a process that executes the main program of your Spark application and creates the SparkContext that coordinates the execution of jobs. SparkSession is created within driver node.
- The executors are processes running on the worker nodes of the cluster which are responsible for executing the tasks the driver process has assigned to them.
- The cluster manager (such as Mesos or YARN) is responsible for the allocation of physical resources to Spark Applications
SparkSession is created within driver node.

#### Start Spark in Command Line
- "spark-shell" is the command to start scala spark shell
- "pyspark" is the command to start python spark shell
- "sparkR" is the command to start R spark shell
- Java has no shell, no command line interface

#### Set sc.setLogLevel (setting default log level to "WARN"
- if *sc.setLogLevel("INFO")* -> CLI show all infomation
- if *sc.setLogLevel("WARN")* -> CLI show both warning and errors
- if *sc.setLogLevel("ERROR")* -> CLI show errors only


### SparkSession vs SparkContext
- Since earlier versions of Spark or Pyspark, SparkContext (JavaSparkContext for Java) is an entry point to Spark programming with RDD and to connect to Spark Cluster
- Since Spark 2.0 SparkSession has been introduced and became an entry point to start programming with DataFrame and Dataset.
- Before creating

#### Entry Points
Every Spark Application needs an entry point that allows it to communicate with data sources and perform certain operations such as reading and writing data. 
- In Spark 1.x, three entry points were introduced: SparkContext, SQLContext and HiveContext. 
- Since Spark 2.x, a new entry point called SparkSession has been introduced that essentially combined all functionalities available in the three aforementioned contexts.
- ***Note that all contexts are still available even in newest Spark releases, mostly for backward compatibility purposes.***

#### SparkContext
The SparkContext is used by the Driver Process of the Spark Application in order to **establish a communication with the cluster and the resource managers in order to coordinate and execute jobs**.
- In order to create a SparkContext, you will first need to create a Spark Configuration (SparkConf) as shown below:
```
// Scala
import org.apache.spark.{SparkContext, SparkConf}
val sparkConf = new SparkConf() \
    .setAppName("app") \
    .setMaster("yarn")
val sc = new SparkContext(sparkConf)

# PySpark
from pyspark import SparkContext, SparkConf
conf = SparkConf() \
    .setAppName('app') \
    .setMaster(master)
sc = SparkContext(conf=conf)
```
- ***Note that if you are using the spark-shell, SparkContext is already available through the variable called sc.***

#### SQLContext
SQLContext is the entry point to SparkSQL which is **a Spark module for structured data processing**. Once SQLContext is initialised, the user can then use it in order to perform various “sql-like” operations over Datasets and Dataframes.
- In order to create a SQLContext, you first need to instantiate a SparkContext as shown below:
```
// Scala
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.sql.SQLContext
val sparkConf = new SparkConf() \
    .setAppName("app") \
    .setMaster("yarn")
val sc = new SparkContext(sparkConf)
val sqlContext = new SQLContext(sc)


# PySpark
from pyspark import SparkContext, SparkConf
from pyspark.sql import SQLContext
conf = SparkConf() \
    .setAppName('app') \
    .setMaster(master)
sc = SparkContext(conf=conf)
sql_context = SQLContext(sc)
```

#### HiveContext
If your Spark Application needs to **communicate with Hive and you are using Spark < 2.0 then you will probably need a HiveContext**. For Spark 1.5+, HiveContext also offers support for window functions.

```
// Scala
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.sql.hive.HiveContext
val sparkConf = new SparkConf() \
    .setAppName("app") \
    .setMaster("yarn")
val sc = new SparkContext(sparkConf)
val hiveContext = new HiveContext(sc)
hiveContext.sql("select * from tableName limit 0")


# PySpark
from pyspark import SparkContext, HiveContext
conf = SparkConf() \
    .setAppName('app') \
    .setMaster(master)
sc = SparkContext(conf)
hive_context = HiveContext(sc)
hive_context.sql("select * from tableName limit 0")
```

- ***Since Spark 2.x+, tow additions made HiveContext redundant:***
a) SparkSession was introduced that also offers Hive support
b) Native window functions were released and essentially replaced the Hive UDAFs with native Spark SQL UDAFs

#### SparkSession
Spark 2.0 introduced a new entry point called SparkSession that essentially replaced both SQLContext and HiveContext. Additionally, it gives to developers immediate access to SparkContext. In order to create a SparkSession with Hive support, all you have to do is

```diff
// Scala
import org.apache.spark.sql.SparkSession
val sparkSession = SparkSession \
    .builder() \
    .appName("myApp") \
    .enableHiveSupport() \
    .getOrCreate()
! // Two ways you can access spark context from spark session
val spark_context = sparkSession._sc
val spark_context = sparkSession.sparkContext

# PySpark
from pyspark.sql import SparkSession
spark_session = SparkSession \
    .builder \
    .enableHiveSupport() \
    .getOrCreate()
# Two ways you can access spark context from spark session
spark_context = spark_session._sc
spark_context = spark_session.sparkContext
```

#### RDD vs Dataframe vs Dataset
*Spark has three data representations, i.e., RDD, Dataset & Dataframe. For each data representation, Spark has different API.*
A Spark DataFrame is an integrated data structure with an easy-to-use API for simplifying distributed big data processing. DataFrame is available for general-purpose programming languages such as Java, Python, and Scala. It is an extension of the Spark RDD API optimized for writing code more efficiently while remaining powerful. [Reference](https://stackoverflow.com/questions/31508083/difference-between-dataframe-dataset-and-rdd-in-spark)
Dataframe is much faster than RDD because it has meta data(some information about data) associated with it, which allows Spark to optimize query plan.


##### RDD
The main abstraction Spark provides is a resilient distributed dataset (RDD), which is a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel. 
- RDD contains the collection of records which are partitioned. The basic unit of parallelism in an RDD is called partition. Each partition is one logical division of data which is immutable and created through some transformation on existing partitions. Immutability helps to achieve consistency in computations. We can move from RDD to DataFrame (If RDD is in tabular format) by toDF() method or we can do the reverse by the .rdd method.
- RDD is a distributed collection of data elements spread across many machines in the cluster. RDDs are a set of Java or Scala objects representing data.
- RDDs has two major characteristics,
    1. Immutable (once you performed any transformation on it you can’t change it)
    2. Fault tolerant (It can get recovered in case if it’s lost)


##### DataFrame
- After transforming into DataFrame one cannot regenerate a domain object. For example, if you generate testDF from testRDD, then you won’t be able to recover the original RDD of the test class.
- spark dataframe comparing to pandas dataframe, the former is present on distributed multiple machines, while the pandas dataframe only present on single machine.
- The dataframe feature was added in spark 1.6. First let me introduce you the dataframes. Dataframe has some common features of RDD.[Reference](https://medium.com/@fahadsaadullahkhan/spark-has-three-data-representations-i-e-rdd-dataset-dataframe-1dd721b53ed2)


##### DataSet
It overcomes the limitation of DataFrame to regenerate the RDD from Dataframe. Datasets allow you to convert your existing RDD and DataFrames into Datasets.


#### Partitions
- RDD/datasets/Dataframe can have multiple partitions.
- One of the advantages of partitions is parallelism. 
- for each partition, there would be running in a separate executer. One machine could have one or multiple executers, but the number of partitions and the number of executers must always be same.


#### Transformations and Actions
No matter what data representation we use (RDD, DataFrame, Dataset), once we execute any instructions on it, that instruction can be classified into 2 categories: either Transformation or Action.

1. Transformations: Involves modification of data contents, e.g., Where/Filter clause.
2. Actions: Involves display of results on terminal, save to file, collect(), show(), etc. Action executes the Transformations. 

***Spark is "LAZY", which means whatever TRANSFORMATION applied on RDD/DataFrame/Dataset will not be executed immediately unitl ACTIONS are called.***

WHY SPARK IS "LAZY"?
- In case of Hadoop MapReduce, the data transformations are intermidately mapped and stored in HDFS and finally reduced and stored in HDFS via HardDisk. HardDisk does'nt have so much limitation because of space. 
- In case of Spark, intermidiate data transformations are recorded in forms of DAG(Directed Acyclic Graph) and stored in RAM. And, until the Action assigned, the tranformations are eventually executed according to DAG and stored in HDFS. This is because the memory has space limitation, RAM space is not comparable to HardDisk space.
