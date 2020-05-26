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

```bash
which python
vim ~/.bash_profile
vim .bash_profile.pysave

python3 get-pip.py --force-reinstall
pip install pyspark


# echo "alias python=/usr/local/bin/python3" >> ~/.bashrc
export PYSPARK_PYTHON=/usr/local/bin/python3

py3.7
```

- Starting the Spark Shell
- Using the Spark Shell 
- Getting Started with Datasets and DataFrames
- DataFrame Operations



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




