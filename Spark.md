## Required Skills
### Transform, Stage, and Store
Convert a set of data values in a given format stored in HDFS into new data values or a new data format and write them into HDFS. 
- **Load** data from HDFS for use in Spark applications
- **Write** the results back into HDFS using Spark
- Read and write files in a variety of file **formats**
- Perform standard extract, transform, load (**ETL**) processes on data using the Spark API

### Data Analysis
Use Spark SQL to interact with the metastore programmatically in your applications. Generate reports by using queries against loaded data.
- Use **metastore** tables as an input source or an output sink for Spark applications
- Understand the fundamentals of **querying** datasets in Spark
- **Filter** data using Spark
- Write queries that calculate **aggregate** statistics
- **Join** disparate datasets using Spark
- Produce **ranked or sorted** data
### Configuration
This is a practical exam and the candidate should be familiar with all aspects of generating a result, not just writing code.
- Supply command-line options to change your application **configuration**, such as increasing available memory


## Outline
### Introduction
Introduction to Apache Hadoop and the Hadoop Ecosystem 

- Apache Hadoop Overview
- Data Processing 
- Introduction to the Hands-On Exercises

### Apache Hadoop File Storage
- Apache Hadoop Cluster Components
- HDFS Architecture
- Using HDFS 

### Distributed Processing on an Apache Hadoop Cluster

- YARN Architecture
- Working With YARN

### Apache Spark Basics

#### What is Apache Spark?
- Apache Spark is a unified **computing engine** and a set of libraries for **parallel data processing** on computer clusters.  

- Spark is a distributed programming model in which the user specifies transformations . Multiple transformations build up a directed acyclic graph of instructions. An action begins the process of executing that graph of instructions, as a single job, by breaking it down into stages and tasks to execute across the cluster. The logical structures that we manipulate with transformations and actions are DataFrames and Datasets. To create a new DataFrame or Dataset, you call a transformation. To start computation or convert to native language types, you call an action. 


```bash
# local setup
# py3.7 for spark version 2.4.5
which python3
# mac installer

vim ~/.bash_profile # then open new command prompt
# vim .bash_profile.pysave
echo $PATH

python3 --version
pip3 --version
pip3 install pyspark

# echo "alias python=/usr/local/bin/python3" >> ~/.bashrc
export PYSPARK_PYTHON=/usr/local/bin/python3

pyspark

# 
>>> myRange = spark.range(1000).toDF("num")
>>> myRange.show()
>>> divisBy2 = myRange.where("num % 2 = 0")
>>> divisBy2.show()


#

head flight-data/csv/2015-summary.csv
>>> flightData2015 = spark\
... .read\
... .option('inferSchema', 'true')\
... .option('header', 'true')\
... .csv('flight-data/csv/2015-summary.csv')

>>> flightData2015.take(3)
>>> flightData2015.sort('count').explain()
>>> spark.conf.set('spark.sql.shuffle.partitions', '5')
>>> flightData2015.sort('count').take(3)
```

```bash
# AWS setup for spark
# a sep security group for InBound SSH 22 myIP
chmod 0400 key.pem
ssh -i key.pem ec2-user@IPv4
whoami
ping google.com

# lib
sudo su
yum update -y

# yum install -y httpd.x86_64
# start httpd.service
# systemctl enable httpd.service
# curl localhost:80

yum install python3.7
python3 --version
pip3 --version
pip3 install pyspark

exit
```


- SparkSession
  - one-to-one to Spark app
- Driver, Executor
- Spark core data structures are immutable
- DataFrame
  - mulitple partition
  
- Running production applications with spark-submit 
  - lets you send your application code to a cluster and launch it to execute there. 
  - master arg
- Datasets: type-safe APIs for structured data
  - for Java and Scala to manipulate it as a **collection** of typed objects, 
  - classes following the **JavaBean** pattern 
  
- Structured Streaming
- Machine learning and advanced analytics
  - centroid
- Resilient Distributed Datasets (RDD): Spark’s low level APIs
  - you might use RDDs when you’re reading or manipulating raw data
    - parallelize raw data that you have stored in memory on the driver machine. 
  - RDDs are lower level than DataFrames because they reveal physical execution characteristics (like partitions) to end users. 

- SparkR
- The third-party package ecosystem

#### Starting the Spark Shell
#### Using the Spark Shell 
#### Getting Started with Datasets and DataFrames
- unstructured log files to semi-structured CSV files and highly structured Parquet files. 
  - Datasets
  - DataFrames
  - SQL tables and views


- DataFrames and Datasets are (distributed) table-like collections with well-defined rows and columns. 
- To Spark, DataFrames and Datasets represent immutable, lazily evaluated plans that specify what operations to apply to data residing at a location to generate some output. 

- A schema defines the column **names and types** of a DataFrame. 
  - define manually OR schema on read
- Column
- Row
  - Each record in a DataFrame must be of type Row 
- Spark Type
```py
from pyspark.sql.types import * 
b = ByteType () 
 
```




#### DataFrame Operations
  - Transformation
    - narrow depedendcy: 
      - pipelining: filter
      - in-memory
      - one output partition
    - wide dep: 
      - shuffle: aggregation
      - to disk
      - default 200 shuffle partition
    ```py
    spark.conf.set ( "spark.sql.shuffle.partitions" , "5" ) 
    ```
  - Lazy evalution:
    - Spark optimize data flow based on your plan
  - Action
    - to trigger compututaion for a result from multiple transformation\
    - 3 kinds: view, collect to object, persist

- spark.sql function
  - returns a new DataFrame. 


- Structured API Execution steps:
  - Write DataFrame/Dataset/SQL Code.
  - If valid code, Spark converts this to a Logical Plan .
    - convert the user’s set of expressions into the most optimized version by converting user code into an unresolved logical plan . 
  - Spark transforms this Logical Plan to a Physical Plan , checking for optimizations along the way.
    - Spark, as a compiler to take queries in DataFrames, Datasets, and SQL and compiles them into RDD transformations for you. 

  - Spark then executes this Physical Plan (RDD manipulations) on the cluster.


### Working with DataFrames and Schemas

- Creating DataFrames from Data Sources
- Saving DataFrames to Data Sources
- DataFrame Schemas 
- Eager and Lazy Execution


### Analyzing Data with DataFrame Queries

- Querying DataFrames Using Column Expressions
- Grouping and Aggregation Queries
- Joining DataFrames 


### RDD Overview

- RDD Overview 
- RDD Data Sources
- Creating and Saving RDDs 
- RDD Operations

### Transforming Data with RDDs

- Writing and Passing Transformation Functions 
- Transformation Execution
- Converting Between RDDs and DataFrames 

### Aggregating Data with Pair RDDs

- Key-Value Pair RDDs 
- Map-Reduce
- Other Pair RDD Operations 

### Querying Tables and Views with SQL

- Querying Tables in Spark Using SQL 
- Querying Files and Views
- The Catalog API 


### Working with Datasets in Scala

- Datasets and DataFrames 
- Creating Datasets
- Loading and Saving Datasets 
- Dataset Operations

### Writing, Configuring, and Running Spark Applications

- Writing a Spark Application 
- Building and Running an Application
- Application Deployment Mode 
- The Spark Application Web UI
  - Spark UI on port 4040 to see the physical and logical execution characteristics of your jobs. 
- Configuring Application Properties 

### Spark Distributed Processing

- Review: Apache Spark on a Cluster
- RDD Partitions
- Example: Partitioning in Queries 
- Stages and Tasks
- Job Execution Planning 
- Example: Catalyst Execution Plan
- Example: RDD Execution Plan 
- Distributed Data Persistence

### DataFrame and Dataset Persistence 
- Persistence Storage Levels
- Viewing Persisted RDDs 
- Common Patterns in Spark Data Processing

### Common Apache Spark Use Cases 
- Iterative Algorithms in Apache Spark
- Machine Learning 
- Example: k-means

### Introduction to Structured Streaming

- Apache Spark Streaming Overview 
- Creating Streaming DataFrames
- Transforming DataFrames 
- Executing Streaming Queries

### Structured Streaming with Apache Kafka

- Overview
- Receiving Kafka Messages
- Sending Kafka Messages


### Aggregating and Joining Streaming DataFrames

- Streaming Aggregation
- Joining Streaming DataFrames

### Conclusion

Message Processing with Apache Kafka

- What Is Apache Kafka?
- Apache Kafka Overview
- Scaling Apache Kafka
- Apache Kafka Cluster Architecture
- Apache Kafka Command Line Tools




