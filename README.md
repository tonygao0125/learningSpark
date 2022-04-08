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
- Scala (Most widely used since Spark itself is written in Scala, better integration)
- Java (2nd most popular lang for data engineering)
- Python (Mostly for data science application)
- R (less used)

#### Definition
Spark is an unified computing engine for parallel data processing on clusters. **It supports batch processing, streaming processing, ML and SQL queries.** 

#### Low Level APIs of Spark
1. RDD - Resilient Dsitributed Datasets (it's like a row of data/record)
2. Broadcast (less used)
3. Accumulator (less used)

#### High Level APIs of Spark - Structured APIs
1. Dataframe
2. SQL
3. Dataset
**On top of it, it supports: 1. Structured Streaming. 2. Advanced Analytics. 3. Other Libraries.**
#### Versions
- 2014 -> 1.0 (1.6) -> RDD
- 2016 -> 2.0 (2.1, 2.4) -> Dataframe, Dataset
- 2020 -> 3.0 -> Faster speed and more ML libraries

#### Driver vs Executor
- Spark Driver is the central coordinator and it communicates with all the Workers. It controls the flow of program. 
- Each Worker node consists of one or more Executor(s) who are responsible for running the Task. Executors register themselves with Driver. The Driver has all the information about the Executors at all the time.
- Normally, one driver corresponds to multiple executors. 
- SparkSession is created within driver node.

#### Start Spark in Command Line
- "spark-shell" is the command to start scala spark shell
- "pyspark" is the command to start python spark shell
- "sparkR" is the command to start R spark shell
- Java has no shell, no command line interface
