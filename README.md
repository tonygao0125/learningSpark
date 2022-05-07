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


##### RDD (resilient distributed dataset)
The main abstraction Spark provides is a *resilient distributed dataset (RDD)*, which is a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel. 
- RDD contains the collection of records which are partitioned. The basic unit of parallelism in an RDD is called partition. Each partition is one logical division of data which is immutable and created through some transformation on existing partitions. Immutability helps to achieve consistency in computations. We can move from RDD to DataFrame (If RDD is in tabular format) by toDF() method or we can do the reverse by the *.rdd* method.
- RDD is a distributed collection of data elements spread across many machines in the cluster. RDDs are a set of Java or Scala objects representing data.
- RDDs has some major characteristics:
    1. Immutable - Once RDDs are created, they cannot be changed until some transformations are applied on it to create a new RDD.
    2. Fault tolerant - It can get recovered quickly in case if running failure, since DAG has the record of physical plan and logical plans of data.
    3. Partitioned - RDDs are a collection of records which are partitioned and stored across distributed nodes.
    4. Lazy evaluation - Spark transformations are lazily evaluated until an action is executed to trigger the evaluation.
    5. In-memory - The data can reside in the memory as long as possible.
- Normally, there are only a few senarios need to deal with RDD:
    1. Support Legacy code -> spark 1.x
    2. Granular level control of data, e.g., control how much partition we want, which machine has what partitions, what data should go in each of the          partitions and so on. 
    3. Working with very unstructured data, e.g., logs data.

##### DataFrame
- After transforming into DataFrame one cannot regenerate a domain object. For example, if you generate testDF from testRDD, then you won’t be able to recover the original RDD of the test class.
- spark dataframe comparing to pandas dataframe, the former is present on distributed multiple machines, while the pandas dataframe only present on single machine.
- The dataframe feature was added in spark 1.6. First let me introduce you the dataframes. Dataframe has some common features of RDD.[Reference](https://medium.com/@fahadsaadullahkhan/spark-has-three-data-representations-i-e-rdd-dataset-dataframe-1dd721b53ed2)


##### DataSet
It overcomes the limitation of DataFrame to regenerate the RDD from Dataframe. Datasets allow you to convert your existing RDD and DataFrames into Datasets.
- Dataset only available for scala and Java, not available for Python and R. Because Python and R are dynamically-typed language which means doesn't need to define data type. **And, dataset is nothing but dataframe with fixed schema and type safe (won't accidentally put some other data).**


#### Partitions
- RDD/datasets/Dataframe can have multiple partitions.
- One of the advantages of partitions is parallelism. 
- for each partition, there would be running in a separate executer. One machine could have one or multiple executers, but the number of partitions and the number of executers must always be same.


#### Transformations and Actions
No matter what data representation we use (RDD, DataFrame, Dataset), once we execute any instructions on it, that instruction can be classified into 2 categories: either Transformation or Action.

1. Transformations: Involves modification of data contents, e.g., Where/Filter clause.
2. Actions: Involves display of results on terminal, save to file, collect(), show(), reduce(), etc. Action executes the Transformations. 

***Spark is "LAZY", which means whatever TRANSFORMATION applied on RDD/DataFrame/Dataset will not be executed immediately unitl ACTIONS are called.***

WHY SPARK IS "LAZY"?
- In case of Hadoop MapReduce, the data transformations are intermidately mapped and stored in HDFS and finally reduced and stored in HDFS via HardDisk. HardDisk does'nt have so much limitation because of space. 
- In case of Spark, intermidiate data transformations are recorded in forms of DAG(Directed Acyclic Graph) and stored in RAM. And, until the Action assigned, the tranformations are eventually executed according to DAG and stored in HDFS. This is because the memory has space limitation, RAM space is not comparable to HardDisk space.


##### Narrow Transformation vs Wide Transformation
- Narrow transformation: data transformation within the partition and different partitions of data process in parallel. One to one process, e.g., filter transformation. 
- Wide transformation: partitions on different machines have data moving between machines, which is called shuffle. Shuffle takes time. e.g., join transformation.
**If the using RDD for data representation, we must be careful, for example, use filter transformation prior to join transformation, otherwise it will take very long time because of shuffle. If using dataframe and dataset API, we don't have to worry about it.**

---
## RDD (Resilient Distributed Dataset) Practical
### 1. Create RDD 
#### 1.1. Parallelize Method 
- Method is function defined in Class
- Method: *<spark.sparkContext.parallelize>* - use to create a RDD
```diff
scala> spark
res16: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@3b5a4aa5
+ // spark package contains sql package and SparkContext class (instantiation as "sc" in spark shell) SparkContext sql package contains SparkSession  
+ // class (instantiation as "spark"). SparkSession class contains sparkContext class. One is SparkContext, the other is sparkContext. 

scala> spark.
baseRelationToDataFrame   createDataFrame   experimental      range          sharedState    stop      udf       
catalog                   createDataset     implicits         read           sparkContext   streams   version   
close                     emptyDataFrame    listenerManager   readStream     sql            table               
conf                      emptyDataset      newSession        sessionState   sqlContext     time                
// SparkSession is a class belongs to org.apache.spark.sql package

scala> spark.sparkContext
res14: org.apache.spark.SparkContext = org.apache.spark.SparkContext@318ff889

scala> sc
res15: org.apache.spark.SparkContext = org.apache.spark.SparkContext@318ff889

// Create an String Array
scala> val carsArray = Array[String]("BMW", "Bentley", "Mercedes", "Suzuki", "Honda", "Jaguar", "Fiat", "Audi")
carsArray: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi)
!// Create RDD from the Array (Array is data structure in scala/java it's not distributed until being converted to RDD data type, then it has features
!// of partitions, fault tolerence, immutable, lazy evaluation and so on)
scala> val carsRDD = spark.sparkContext.parallelize(carsArray, 2)
carsRDD: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[8] at parallelize at <console>:25
@@// we can also use sc instead of spark.sparkContext@@
scala> carsRDD.collect()
res17: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi)
```
### 2. Transformations
#### 2.1.  distinct() - Get distinct values in RDD
```diff
-// duplicates of BMW in the array
scala> val carsArray = Array[String]("BMW", "Bentley", "Mercedes", "Suzuki", "Honda", "Jaguar", "Fiat", "Audi", "BMW")
carsArray: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi, BMW)

scala> val carsRDD = sc.parallelize(carsArray)
carsRDD: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[9] at parallelize at <console>:26

scala> carsRDD.collect()
res19: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi, BMW)
+// apply distinct() method to get distinct values in RDD
scala> val distinctcarsRDD = carsRDD.distinct()
distinctcarsRDD: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[12] at distinct at <console>:25

scala> distinctcarsRDD.collect()
res20: Array[String] = Array(Bentley, Suzuki, BMW, Mercedes, Jaguar, Fiat, Honda, Audi)
```

#### 2.2.  filter() - Get values of interests
```diff
scala> val carsArray = Array[String]("BMW", "Bentley", "Mercedes", "Suzuki", "Honda", "Jaguar", "Fiat", "Audi")
carsArray: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi)

scala> val carsRDD = sc.parallelize(carsArray)
carsRDD: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[13] at parallelize at <console>:26

scala> carsRDD.collect()
res21: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi)
+//startsWith() is one of the methods for string in Scala. 
scala> carsRDD.filter(carName => carName.startsWith("B")).collect()
res22: Array[String] = Array(BMW, Bentley)
```
Example - get even number out of numbers
```diff
scala> val numbersRDD = sc.parallelize(1 to 10, 2)
numbersRDD: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[25] at parallelize at <console>:24

scala> numbersRDD.filter(x => x%2 ==0).collect()
res31: Array[Int] = Array(2, 4, 6, 8, 10)

+//shorthand notation
scala> numbersRDD.filter(_%2 ==0).collect()
res32: Array[Int] = Array(2, 4, 6, 8, 10)
```
```diff
+ //define a function to get even numbers, the return type is Boolean
scala> def evenFilter(x : Int) =
     | x%2 == 0
evenFilter: (x: Int)Boolean

scala> numbersRDD.collect()
res34: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

scala> val evennumbers = numbersRDD.filter(x => evenFilter(x))
evennumbers: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[28] at filter at <console>:27

scala> evennumbers.collect()
res35: Array[Int] = Array(2, 4, 6, 8, 10)

+//use function instead of logic definition
scala> val evennumbers = numbersRDD.filter(evenFilter).collect()
evennumbers: Array[Int] = Array(2, 4, 6, 8, 10)
```

#### 2.3. map() - Mapping is transforming each RDD element using a function and returning a new RDD
```diff
scala> carsRDD.collect()
res38: Array[String] = Array(BMW, Bentley, Mercedes, Suzuki, Honda, Jaguar, Fiat, Audi)

scala> val carsRDDwithB = carsRDD.map(carName => (carName, carName.startsWith("B")))
carsRDDwithB: org.apache.spark.rdd.RDD[(String, Boolean)] = MapPartitionsRDD[30] at map at <console>:25

+// Make a combination of data for each RDD elements
scala> carsRDDwithB.collect()
res39: Array[(String, Boolean)] = Array((BMW,true), (Bentley,true), (Mercedes,false), (Suzuki,false), (Honda,false), (Jaguar,false), (Fiat,false), (Audi,false))

+// Map transformation to get (CarName + CarName length)
scala> val carsRDDMap = carsRDD.map(carName => (carName, carName.length)).collect()
carsRDDMap: Array[(String, Int)] = Array((BMW,3), (Bentley,7), (Mercedes,8), (Suzuki,6), (Honda,5), (Jaguar,6), (Fiat,4), (Audi,4))
```

#### 2.4. flatMap()
- flatMap() method is identical to the map() method, but the only difference is that in flatMap the inner grouping of an item is removed and a sequence is generated.
- The output obtained by running the map method followed by the flatten method is same as obtained by the flatMap(). So, we can say that flatMap first runs the map method and then the flatten method to generate the desired result.
```diff
scala> val array1D = Array("1,2,3", "4,5,6", "7,8,9")
array1D: Array[String] = Array(1,2,3, 4,5,6, 7,8,9)

+//map()
scala> val array2D = array1D.map(x => x.split(","))
array2D: Array[Array[String]] = Array(Array(1, 2, 3), Array(4, 5, 6), Array(7, 8, 9))
+//flatMap() is combination of map() and flatten()
scala> val FlatArray = array1D.flatMap(x => x.split("x"))
FlatArray: Array[String] = Array(1,2,3, 4,5,6, 7,8,9)

scala> val FlatArray = array1D.flatMap(x => x.split(","))
FlatArray: Array[String] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9)
```
***Words count example by using RDD (word count is to learning Hadoop/Spark/Distributed Data processing engine what print("hello world") to learning programing language***
```diff
// Create an Array of texts
scala> val book = Array("A book is a medium for recording information in the form of writing or images, typically composed of many pages (made of papyrus, parchment, vellum, or paper) bound together and protected by a cover.", "The technical term for this physical arrangement is codex (plural, codices). In the history of hand-held physical supports for extended written compositions or records, the codex replaces its predecessor, the scroll. A single sheet in a codex is a leaf and each side of a leaf is a page.", "As an intellectual object, a book is prototypically a composition of such great length that it takes a considerable investment of time to compose and still considered as an investment of time to read.", "In a restricted sense, a book is a self-sufficient section or part of a longer composition, a usage reflecting that, in antiquity, long works had to be written on several scrolls and each scroll had to be identified by the book it contained.", "Each part of Aristotle's Physics is called a book. In an unrestricted sense, a book is the compositional whole of which such sections, whether called books or chapters or parts, are parts.")
book: Array[String] = Array(A book is a medium for recording information in the form of writing or images, typically composed of many pages (made of papyrus, parchment, vellum, or paper) bound together and protected by a cover., The technical term for this physical arrangement is codex (plural, codices). In the history of hand-held physical supports for extended written compositions or records, the codex replaces its predecessor, the scroll. A single sheet in a codex is a leaf and each side of a leaf is a page., As an intellectual object, a book is prototypically a composition of such great length that it takes a considerable investment of time to compose and still considered as an investment of time to read., In a restricted sense, a book is a self-sufficient section or part of a longe...

+//Create the RDD by parallelizing an existing collection in your driver program                        
scala> val bookRDD = spark.sparkContext.parallelize(book, 2)
bookRDD: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[33] at parallelize at <console>:25

+//Split all the texts in the array into words 
scala> val flatbookRDD = bookRDD.flatMap(text => text.split(" "))
flatbookRDD: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[34] at flatMap at <console>:25
```

#### 2.5. sortBy()
```diff
scala> flatbookRDD.sortBy(word => word.length())
res48: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[39] at sortBy at <console>:26
//Ascending
scala> flatbookRDD.sortBy(word => word.length()).collect()
res49: Array[String] = Array(a, A, a, a, a, a, a, a, a, a, a, a, a, a, of, by, In, of, or, in, is, of, is, As, an, is, of, it, of, to, as, an, of, to, In, is, or, of, in, to, be, on, to, be, by, it, In, an, is, of, or, or, for, the, and, The, for, the, for, the, its, and, had, and, had, the, the, are, form, term, this, each, side, leaf, book, such, that, time, time, book, part, long, each, book, Each, part, book, such, codex, sheet, codex, great, takes, still, read., usage, that,, works, book., whole, which, books, medium, single, length, sense,, longer, scroll, called, sense,, called, parts,, parts., history, written, a page., object,, compose, section, written, several, scrolls, whether, composed, a cover., physical, physical, supports, extended, records,, replaces, chapters, A book i...
scala> 
//Descending 
scala> flatbookRDD.sortBy(word => word.length(), false).collect()
res50: Array[String] = Array(of papyrus, parchment, vellum,, is codex (plural, codices)., of Aristotle's Physics is, recording information in, or paper) bound together, of writing or images,, many pages (made, self-sufficient, prototypically, compositional, compositions, predecessor,, intellectual, considerable, composition,, unrestricted, arrangement, the scroll., composition, a leaf and, investment, considered, investment, restricted, reflecting, antiquity,, identified, contained., A book is, typically, protected, technical, hand-held, sections,, composed, a cover., physical, physical, supports, extended, records,, replaces, chapters, history, written, a page., object,, compose, section, written, several, scrolls, whether, medium, single, length, sense,, longer, scroll, called, sense,...
```

#### 2.6. randomSplit()
- Application: Data Science - Machine Learning - Because need to break the entire data set into 70:30 training and test data sets.
```diff
//Random split the RDD by 70%:30% into Array[RDD[String]]
scala> val MLbookRDD = flatbookRDD.randomSplit(Array[Double](0.7,0.3))
MLbookRDD: Array[org.apache.spark.rdd.RDD[String]] = Array(MapPartitionsRDD[52] at randomSplit at <console>:25, MapPartitionsRDD[53] at randomSplit at <console>:25)
+//Call the first element of the RDD Array, which makes up 70% of the RDD for training purpose
scala> MLbookRDD(0).collect()
res54: Array[String] = Array(A book is, a, medium, for, form, of writing or images,, typically, many pages (made, or paper) bound together, and, protected, a cover., The, technical, term, for, physical, arrangement, the, of, hand-held, for, extended, written, or, records,, the, replaces, its, predecessor,, the scroll., single, in, is, a leaf and, each, a, leaf, is, a page., As, an, intellectual, object,, a, is, a, composition, of, great, length, that, it, takes, a, considerable, investment, of, time, to, and, still, investment, to, restricted, sense,, a, book, is, a, section, or, part, of, a, longer, composition,, a, that,, in, antiquity,, long, had, be, on, several, scrolls, scroll, to, be, identified, by, the, book, it, Each, part, called, a, In, unrestricted, sense,, book, is, the, w...
+//Call the second element of the RDD Array, which makes up 30% of the RDD for test purpose
scala> MLbookRDD(1).collect()
res55: Array[String] = Array(recording information in, the, composed, of, of papyrus, parchment, vellum,, by, this, is codex (plural, codices)., In, history, physical, supports, compositions, codex, A, sheet, a, codex, side, of, book, prototypically, such, compose, considered, as, an, of, time, read., In, a, self-sufficient, usage, reflecting, works, to, written, and, each, had, contained., of Aristotle's Physics is, book., an, a, compositional, such, chapters)
```

### 3. Actions
Starts the Transformation
#### 3.1. collect()
- Return a list that contains all of the elements in this RDD.
- **This method should only be used if the resulting array is expected to be small, as all the data is loaded into the driver’s memory.**
```diff
+//Display all the elements in this RDD (from all partitions)
scala> flatbookRDD.collect()
res0: Array[String] = Array(A book is, a, medium, for, recording information in, the, form, of writing or images,, typically, composed, of, many pages (made, of papyrus, parchment, vellum,, or paper) bound together, and, protected, by, a cover., The, technical, term, for, this, physical, arrangement, is codex (plural, codices)., In, the, history, of, hand-held, physical, supports, for, extended, written, compositions, or, records,, the, codex, replaces, its, predecessor,, the scroll., A, single, sheet, in, a, codex, is, a leaf and, each, side, of, a, leaf, is, a page., As, an, intellectual, object,, a, book, is, prototypically, a, composition, of, such, great, length, that, it, takes, a, considerable, investment, of, time, to, compose, and, still, considered, as, an, investment, of, tim...
```

#### 3.2. reduce()
- Reduce is a spark action that aggregates a data set (RDD) element using a function. That function takes two arguments and returns one. e.g., add operation.
```diff
scala> sc.parallelize(1 to 10).collect()
res2: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

scala> sc.parallelize(1 to 10).reduce(_ + _)
res3: Int = 55
```
- **Example of find the smallest length of word in the RDD**
```diff
+//define a logic function taking 2 arguments (word here) and return the word with smaller length 
scala> def smallestWord(leftWord:String, rightWord:String):String={
     | if (leftWord.length >= rightWord.length) {rightWord}
     | else {leftWord}}
smallestWord: (leftWord: String, rightWord: String)String

//use the self-defined function as the reduce behavior
scala> flatbookRDD.reduce(smallestWord)
res9: String = a

//without using the pre-defined logic function (NOT RECCOMENDED)
scala> flatbookRDD.reduce((x,y)=>{if(x.length>=y.length) {y} else {x}})
res10: String = a
```

#### 3.3. count()
- Get the number of elements in the RDD
```diff
scala> flatbookRDD.count()
res11: Long = 166
```


#### 3.4. countApprox(timeout: Long, confidence: Double = 0.95)
- (Experimental) Approximate version of count() that returns a potentially incomplete result within a timeout, even if not all tasks have finished.
```diff
// Returns a potentially incomplete result (range from 105 to 136) in 16 millisecond timeout. Default confidence interval is 95%.
scala> flatbookRDD.countApprox(16)
res26: org.apache.spark.partial.PartialResult[org.apache.spark.partial.BoundedDouble] = (partial: [105.000, 136.000])

// Count returned within 20 millisecond timeout
scala> flatbookRDD.countApprox(20)
res12: org.apache.spark.partial.PartialResult[org.apache.spark.partial.BoundedDouble] = (final: [166.000, 166.000])
```


#### 3.5. countByValue() - used for word count in Spark
- Find the number of times each word/value comes in the text.
```diff
scala> flatbookRDD.countByValue()
res28: scala.collection.Map[String,Long] = Map(side -> 1, parts. -> 1, for -> 3, works -> 1, Each -> 1, records, -> 1, this -> 1, in -> 2, scroll -> 1, are -> 1, is -> 5, protected -> 1, extended -> 1, compose -> 1, history -> 1, predecessor, -> 1, of papyrus, parchment, vellum, -> 1, In -> 3, whether -> 1, usage -> 1, contained. -> 1, of writing or images, -> 1, a page. -> 1, book -> 4, had -> 2, antiquity, -> 1, of Aristotle's Physics is -> 1, it -> 2, prototypically -> 1, sheet -> 1, A -> 1, term -> 1, a -> 13, self-sufficient -> 1, recording information in -> 1, as -> 1, scrolls -> 1, chapters -> 1, or -> 4, such -> 2, medium -> 1, each -> 2, sections, -> 1, section -> 1, compositional -> 1, that -> 1, to -> 4, the scroll. -> 1, hand-held -> 1, single -> 1, considerable -> 1, long -...
```


#### 3.6. countByValueApprox()
- similar to countApprox()


#### 3.7. first()
- return 1st record/word/string in RDD
```
scala> flatbookRDD.first()
res29: String = A book is
```


#### 3.8. max() and min()
- gives max value and min value
```
scala> flatbookRDD.max()
res30: String = written

scala> flatbookRDD.min()
res31: String = A
```


#### 3.9. take(), takeOrdered() and top()
```diff
scala> val sampleRDD = spark.sparkContext.parallelize(List(6, 7, 5, 3, 10, 25, 8, 70, 35))
sampleRDD: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[9] at parallelize at <console>:23

+//take first 3 elements from the collection
scala> sampleRDD.take(3)
res32: Array[Int] = Array(6, 7, 5)
+//take first 3 elements in Ascending Order
scala> sampleRDD.takeOrdered(3)
res33: Array[Int] = Array(3, 5, 6)
+//take first 3 elements in Descending Order
scala> sampleRDD.top(3)
res34: Array[Int] = Array(70, 35, 25)
```


#### 3.10. takeSample()
- 1st argument: true -> same word can come again (with replacement), e.g, the word "written" appears twice.
- 2nd argument: 70 -> number of Words
- 3rd argument: 10L -> seed which ensures the reproducibility (same samples are taken for same seed), and it must be long data type. The "L" here indicate a long type data. If don't specify the seed, then random samples will be taken each time.
```diff
scala> flatbookRDD.takeSample(true, 70, 10L)
res35: Array[String] = Array(books, written, In, by, scrolls, A book is, antiquity,, part, a leaf and, great, In, this, for, the scroll., section, is codex (plural, codices)., Each, term, the, object,, supports, each, technical, written, of writing or images,, chapters, In, a, sense,, a page., parts,, codex, that,, self-sufficient, extended, written, The, protected, and, this, compositional, length, investment, book., composed, composition,, a, or paper) bound together, of, book, a, its, a, a, part, an, A book is, compose, supports, composed, a, leaf, its, In, of, or, by, physical, for, records,)
```


#### 3.11. saveAsTextFile()
```diff
scala> flatbookRDD.saveAsTextFile("/home/tony/temp20220508")
```
***Check the generated file in hdfs by using following syntax:***
```diff
@@tony@tony-ubuntu:~/temp20220508$ hdfs dfs -ls /home/tony/temp20220508@@
Found 3 items
-rw-r--r--   1 tony supergroup          0 2022-05-08 01:05 /home/tony/temp20220508/_SUCCESS
-rw-r--r--   1 tony supergroup        511 2022-05-08 01:05 /home/tony/temp20220508/part-00000
-rw-r--r--   1 tony supergroup        635 2022-05-08 01:05 /home/tony/temp20220508/part-00001
```
***Note: there are two files are generated (part-00000 and part-00001) because we sepcify two partitions for this RDD.


#### 3.12. cache and persist (Frequently asked interview questions)
*Both caching and persisting are used to save the Spark RDD, Dataframe, and Dataset’s. But, the difference is, RDD cache() method default saves it to memory (MEMORY_ONLY) whereas persist() method is used to store it to the user-defined storage level.*
##### 3.12.1. cache()
***Example: the below "flatMap(x => x.split(" "))" transformation was executed two times, one for take() and one for top().***
```diff
scala> val flatbookRDD = bookRDD.flatMap(x => x.split(" "))
flatbookRDD: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[18] at flatMap at <console>:25

scala> flatbookRDD.take(5)
res42: Array[String] = Array(A book is, a, medium, for, recording information in)

scala> flatbookRDD.top(5)
res43: Array[String] = Array(written, written, works, whole, which)
```
***Solution: use cache()***
- RDD cache() method default saves it to memory. 
- Reusing the repeated computations saves lots of time.
```diff
scala> val flatbookRDD = bookRDD.flatMap(x => x.split(" "))
flatbookRDD: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[20] at flatMap at <console>:25

+//cache RDD in Memory 
scala> flatbookRDD.cache()
res46: flatbookRDD.type = MapPartitionsRDD[20] at flatMap at <console>:25

scala> flatbookRDD.take(5)
res47: Array[String] = Array(A book is, a, medium, for, recording information in)

+// will not execute flatMap() tranformation again, intead will take from memory.
scala> flatbookRDD.top(5)
res48: Array[String] = Array(written, written, works, whole, which)
```
***Check the storage level by using getStorageLevel***
```diff
scala> flatbookRDD.getStorageLevel
+res49: org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, 1 replicas)
```

##### 3.12.2. persist()
```diff
-//uncache or unpersist the RDD storage level since it's already being assigned in_memory by using cache() above.
scala> flatbookRDD.unpersist()
res51: flatbookRDD.type = MapPartitionsRDD[20] at flatMap at <console>:25

+//persist() to assign the RDD storage level to disk only
scala> flatbookRDD.persist(org.apache.spark.storage.StorageLevel.DISK_ONLY)
res52: flatbookRDD.type = MapPartitionsRDD[20] at flatMap at <console>:25
+//check storage level
scala> flatbookRDD.getStorageLevel
res53: org.apache.spark.storage.StorageLevel = StorageLevel(disk, 1 replicas)
```
