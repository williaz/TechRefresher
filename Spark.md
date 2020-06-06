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
  - Partitioning of the DataFrame defines the **layout** of the DataFrame or Dataset’s **physical** distribution across the cluster. 

  
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

#### Getting Started with Datasets and DataFrames
- unstructured log files to semi-structured CSV files and highly structured Parquet files (DB with types). 
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
```py
>>> mySchema = StructType([\
... StructField('num', LongType(), False),\
... StructField('word', StringType(), True),\
... StructField('name', StringType(), True),\
... ])
>>> myRow = Row(1, "hello world", 'Will')

>>> myDf = spark.createDataFrame([myRow], mySchema)
>>> myDf.show()
+---+-----------+----+                                                          
|num|       word|name|
+---+-----------+----+
|  1|hello world|Will|
+---+-----------+----+


```



#### DataFrame Operations
- We can add rows or columns
- We can remove rows or columns
- We can transform a row into a column (or vice versa)
- We can change the order of rows based on the values in columns

```py
export PYSPARK_PYTHON=/usr/local/bin/python3
>>> df = spark.read.format('json').load("flight-data/json/2015-summary.json")

>>> df.columns
['DEST_COUNTRY_NAME', 'ORIGIN_COUNTRY_NAME', 'count']
>>> df.select('ORIGIN_COUNTRY_NAME').show(3)
+-------------------+
|ORIGIN_COUNTRY_NAME|
+-------------------+
|            Romania|
|            Croatia|
|            Ireland|
+-------------------+
only showing top 3 rows

>>> df.createOrReplaceTempView('dfTable')

>>> df.select(\
... expr("ORIGIN_COUNTRY_NAME as home"),\
... col('ORIGIN_COUNTRY_NAME'),\
... column("DEST_COUNTRY_NAME" ))\
... .show(3)
+-------+-------------------+-----------------+
|   home|ORIGIN_COUNTRY_NAME|DEST_COUNTRY_NAME|
+-------+-------------------+-----------------+
|Romania|            Romania|    United States|
|Croatia|            Croatia|    United States|
|Ireland|            Ireland|    United States|
+-------+-------------------+-----------------+
only showing top 3 rows


>>> df.selectExpr('ORIGIN_COUNTRY_NAME', 'DEST_COUNTRY_NAME', '(ORIGIN_COUNTRY_NAME != DEST_COUNTRY_NAME) as is_abroad').show(3)
+-------------------+-----------------+---------+
|ORIGIN_COUNTRY_NAME|DEST_COUNTRY_NAME|is_abroad|
+-------------------+-----------------+---------+
|            Romania|    United States|     true|
|            Croatia|    United States|     true|
|            Ireland|    United States|     true|
+-------------------+-----------------+---------+

>>> df.selectExpr('avg(count)', 'count(distinct(DEST_COUNTRY_NAME))').show(1)
+-----------+---------------------------------+                                 
| avg(count)|count(DISTINCT DEST_COUNTRY_NAME)|
+-----------+---------------------------------+
|1770.765625|                              132|
+-----------+---------------------------------+

>>> df.selectExpr('*', '1 as dummy').show(3)
>>> df.select(expr('*'),\
... lit(1).alias("dummy")).show(3)


>>> df.selectExpr('ORIGIN_COUNTRY_NAME', 'DEST_COUNTRY_NAME', '(ORIGIN_COUNTRY_NAME = DEST_COUNTRY_NAME) as same_country').show(3)

# adding col
>>> df.withColumn('same_country', expr('DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME')).show(3)

# alter column name
>>> df1 = df.withColumnRenamed('ORIGIN_COUNTRY_NAME', 'where are you from')
>>> df1.select(col('where are you from')).show(2)
>>> df1.selectExpr('`where are you from`').show(2)
+------------------+
|where are you from|
+------------------+
|           Romania|
|           Croatia|
+------------------+
only showing top 2 rows


# drop, immutable
>>> df1.drop('is_aboard').columns
['DEST_COUNTRY_NAME', 'ORIGIN_COUNTRY_NAME', 'count']
>>> df1
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: bigint, is_aboard: boolean]

# cast
>>> df2 = df1.withColumn('count2', col('count').cast('string'))
>>> df2.columns
['DEST_COUNTRY_NAME', 'ORIGIN_COUNTRY_NAME', 'count', 'is_aboard', 'count2']
>>> df2.schema
StructType(List(StructField(DEST_COUNTRY_NAME,StringType,true),StructField(ORIGIN_COUNTRY_NAME,StringType,true),StructField(count,LongType,true),StructField(is_aboard,BooleanType,true),StructField(count2,StringType,true)))

# filter
>>> df.where('count < 2').show(3)
>>> df.where('count < 2').where('DEST_COUNTRY_NAME != "United States"').show(3)

# distinct
>>> df.select('DEST_COUNTRY_NAME').distinct().count()
132 

# sampling
>>> df.count()
256
>>> df.sample(False, 0.5, 5).count()
126

# split
>>> df1 = df.randomSplit([0.4, 0.4], 5)
>>> df1[0].count()
126
>>> df1[1].count()
130

# union
>>> from pyspark.sql import *
>>> schema = df.schema
>>> newRows = [\
... Row("home", "France", 1),\
... Row("US", "France", 4)\
... ]
>>> parellRows = spark.sparkContext.parallelize(newRows)
>>> df2 = spark.createDataFrame(parellRows, schema)
>>> df2.show(2)
+-----------------+-------------------+-----+                                   
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|             home|             France|    1|
|               US|             France|    4|
+-----------------+-------------------+-----+

>>> df.union(df2)\
... .where('ORIGIN_COUNTRY_NAME = "France"').show(5)
+-----------------+-------------------+-----+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|    United States|             France|  952|
|             home|             France|    1|
|               US|             France|    4|
+-----------------+-------------------+-----+

# sort
>>> df.orderBy('count').show(5)
+--------------------+-------------------+-----+
|   DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+--------------------+-------------------+-----+
|               Malta|      United States|    1|
|Saint Vincent and...|      United States|    1|
|       United States|            Croatia|    1|
|       United States|          Gibraltar|    1|
|       United States|          Singapore|    1|
+--------------------+-------------------+-----+

>>> df.orderBy(col('count').desc()).show(5) 
+-----------------+-------------------+------+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME| count|
+-----------------+-------------------+------+
|    United States|      United States|370002|
|    United States|             Canada|  8483|
|           Canada|      United States|  8399|
|    United States|             Mexico|  7187|
|           Mexico|      United States|  7140|
+-----------------+-------------------+------+
# desc not working in df.sort(expr('count desc')).show(5), 

# limit
>>> df.orderBy(col('count').desc()).limit(5).show()
+-----------------+-------------------+------+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME| count|
+-----------------+-------------------+------+
|    United States|      United States|370002|
|    United States|             Canada|  8483|
|           Canada|      United States|  8399|
|    United States|             Mexico|  7187|
|           Mexico|      United States|  7140|
+-----------------+-------------------+------+

# repartition/coalesce
>>> df.rdd.getNumPartitions()
1
>>> df.repartition(4)

>>> df.repartition(col('DEST_COUNTRY_NAME'))
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: bigint]

>>> df.repartition(5, col('DEST_COUNTRY_NAME')).coalesce(2)
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: bigint]


# collect/take
>>> df.take(3)
[Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Romania', count=15), Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Croatia', count=1), Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Ireland', count=344)]
>>> df.limit(2).collect()
[Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Romania', count=15), Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Croatia', count=1)]


```
- select()
- selectExpr()
  - add non-aggregating SQL statement
  - use sql.functions

- lit(): literals
- withColumn(name, expr): adding col
- withColumnRenamed(old, new)
- backtick(`): escape expressions that use reserved characters or keywords

- drop(..):
- cast(): col().cast()
- where/filter(expr)
  - chain: spark auto perform all filtering in same time regardless of their ordering
- distinct()
- sample(withReplacement, fraction, seed)
- randomSplit(arr, seed)
  - will normalize proportion to sum as 1

- union(df)
  - make sure same schema and number of col
  - currently performed based on location, not on the schema

- orderBy(col)/sort()
  - asc(), desc()
  - asc_nulls_first , desc_nulls_first , asc_nulls_last , or desc_nulls_last
  - sortWithinPartitions(): for performance: sort within each partition before another set of transformations
  
- limit()

- repartition()
  - full shuffle
  - when new parition num is greater or partition key
- coalesce()
  - not shuffle, will try to combine partitions

- collect()/take(n)
  - as list of Row
  - expensive operation! -> crash drive




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



#### Creating DataFrames from Data Sources



#### Saving DataFrames to Data Sources



#### DataFrame Schemas 

```py
>>> df = spark.read.format('json').load("flight-data/json/2015-summary.json")
>>> df.printSchema()
root
 |-- DEST_COUNTRY_NAME: string (nullable = true)
 |-- ORIGIN_COUNTRY_NAME: string (nullable = true)
 |-- count: long (nullable = true)

>>> spark.read.format('json').load("flight-data/json/2015-summary.json").schema
StructType(
List(
StructField(DEST_COUNTRY_NAME,StringType,true),
StructField(ORIGIN_COUNTRY_NAME,StringType,true),
StructField(count,LongType,true)))

from pyspark.sql.types import StructField , StructType , StringType , LongType 

myManualSchema = StructType ([ StructField ( "DEST_COUNTRY_NAME" , StringType (), True ), StructField ( "ORIGIN_COUNTRY_NAME" , StringType (), True ), StructField ( "count" , LongType (), False , metadata = { "hello" : "world" }) ]) 
df = spark . read . format ( "json" ) . schema ( myManualSchema ) \
. load ( "/data/flight-data/json/2015-summary.json" ) 

```
- A schema defines the column names and types of a DataFrame. 
  - schema-on-read: ad hoc analysis; precision issue
  - explicitly define: production ETL: untyped data sources like csv, json
- A schema is a **StructType** made up of a number of fields, **StructFields**, that have a name, type, a **Boolean flag** which specifies whether that column can contain missing or **null** values, and, finally, users can **optionally** specify associated metadata with that column. 
- runtime error if data unmatch with schema


```py
>>> df.columns
['DEST_COUNTRY_NAME', 'ORIGIN_COUNTRY_NAME', 'count']
```
- Column
  - To Spark, columns are logical constructions that simply represent a value computed on a per-record basis by means of an expression. 
  - col/column: to construct and refer to columns 
    - Column and table resolution happens in the analyzer phase, 
- Expression
  - expressions to create a single value for each record like array or map
  - Columns provide a subset of expression functionality. 
  - created via the expr function, is just a DataFrame column reference. 
  - Columns are just expressions.
  - Columns and transformations of those columns compile to the **same logical plan** as parsed expressions.

```py
>>> df.first()
Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Romania', count=15)

>>> myRow = Row("CB", True, 12, None)
>>> myRow[1]
True
>>> myRow[3]
>>> myRow[2]
12
```
- Row
  - only DataFrames have schemas. Rows themselves do not have schemas. 
    - if you create a Row manually, you must specify the values in the same order as the schema of the DataFrame 




#### Eager and Lazy Execution


### Analyzing Data with DataFrame Queries

#### Querying DataFrames Using Column Expressions

- DataFrame ( Dataset ) Methods 
  - DataFrame is just a Dataset of Row types, 
  - DataFrameStatFunctions: statistically related functions
  - DataFrameNaFunctions: working with null data. 

- Column Methods 
  - org.apache.spark.sql.functions : Analytic func: transform rows of data in one format or structure to another. 

```py

>>> df = spark.read.format('csv').option('header', 'true').option('inferSchema', 'true')\ 
... .load('retail-data/by-day/2010-12-01.csv')                                           >>> df.printSchema()
root
 |-- InvoiceNo: string (nullable = true)
 |-- StockCode: string (nullable = true)
 |-- Description: string (nullable = true)
 |-- Quantity: integer (nullable = true)
 |-- InvoiceDate: timestamp (nullable = true)
 |-- UnitPrice: double (nullable = true)
 |-- CustomerID: double (nullable = true)
 |-- Country: string (nullable = true)

>>> df.createOrReplaceTempView('dfTable')

# lit
>>> df.select(lit(5), lit('five'), lit(5.0))
DataFrame[5: int, five: string, 5.0: double]

# Boolean for filtering
>>> df.where('InvoiceNo != 536365').select('InvoiceNo', 'Description').show(5, False) # no truncate
+---------+-----------------------------+
|InvoiceNo|Description                  |
+---------+-----------------------------+
|536366   |HAND WARMER UNION JACK       |
|536366   |HAND WARMER RED POLKA DOT    |
|536367   |ASSORTED COLOUR BIRD ORNAMENT|
|536367   |POPPY'S PLAYHOUSE BEDROOM    |
|536367   |POPPY'S PLAYHOUSE KITCHEN    |
+---------+-----------------------------+
only showing top 5 rows


>>> priceFlt = col('UnitPrice') > 500
>>> descFlt = instr(df.Description, 'POSTAGE') >= 1
>>> df.where(df.StockCode.isin('DOT')).where(priceFlt | descFlt).select('StockCode', 'UnitPrice', 'Description').show(3, False)
+---------+---------+--------------+
|StockCode|UnitPrice|Description   |
+---------+---------+--------------+
|DOT      |569.77   |DOTCOM POSTAGE|
|DOT      |607.49   |DOTCOM POSTAGE|
+---------+---------+--------------+

>>> DotFlt = col('StockCode') == 'DOT'
>>> df.withColumn('isExpensive', DotFlt & (priceFlt | descFlt))\
... .where('isExpensive').select('UnitPrice', 'isExpensive').show(5)
+---------+-----------+
|UnitPrice|isExpensive|
+---------+-----------+
|   569.77|       true|
|   607.49|       true|
+---------+-----------+

# number
>>> fabQ = pow(col('Quantity') * col('UnitPrice'), 2) + 5
>>> df.select('CustomerID', fabQ.alias('fabQ')).show(3)
+----------+------------------+
|CustomerID|              fabQ|
+----------+------------------+
|   17850.0|239.08999999999997|
|   17850.0|          418.7156|
|   17850.0|             489.0|
+----------+------------------+
only showing top 3 rows

>>> df.select(round(lit("2.5")), bround(lit("2.5"))).show(1)
+-------------+--------------+
|round(2.5, 0)|bround(2.5, 0)|
+-------------+--------------+
|          3.0|           2.0|
+-------------+--------------+

>>> df.stat.corr('Quantity', 'UnitPrice')
-0.04112314436835551
>>> df.select(corr('Quantity', 'UnitPrice')).show()
+-------------------------+
|corr(Quantity, UnitPrice)|
+-------------------------+
|     -0.04112314436835551|
+-------------------------+

>>> df.select('UnitPrice', 'Quantity').describe().show()
+-------+------------------+------------------+
|summary|         UnitPrice|          Quantity|
+-------+------------------+------------------+
|  count|              3108|              3108|
|   mean| 4.151946589446603| 8.627413127413128|
| stddev|15.638659854603892|26.371821677029203|
|    min|               0.0|               -24|
|    max|            607.49|               600|
+-------+------------------+------------------+

>>> df.select(stddev_pop('UnitPrice'), stddev_pop('Quantity')).show()
+---------------------+--------------------+
|stddev_pop(UnitPrice)|stddev_pop(Quantity)|
+---------------------+--------------------+
|   15.636143780280698|  26.367578764657278|
+---------------------+--------------------+

>>> colName = 'UnitPrice'
>>> quantile = [0.5, 0.95]
>>> relErr = 0.05
>>> df.stat.approxQuantile(colName, quantile, relErr)
[2.51, 607.49]

>>> df.stat.freqItems(['StockCode', 'UnitPrice']).show()
+--------------------+--------------------+
| StockCode_freqItems| UnitPrice_freqItems|
+--------------------+--------------------+
|[90214E, 20728, 2...|[1.95, 14.95, 0.4...|
+--------------------+--------------------+


>>> df.select(monotonically_increasing_id()).show(5)
+-----------------------------+
|monotonically_increasing_id()|
+-----------------------------+
|                            0|
|                            1|
|                            2|
|                            3|
|                            4|
+-----------------------------+


# String
>>> df.select('Description', initcap('Description'), lower(col('Description'))).show(10)
+--------------------+--------------------+--------------------+
|         Description|initcap(Description)|  lower(Description)|
+--------------------+--------------------+--------------------+
|WHITE HANGING HEA...|White Hanging Hea...|white hanging hea...|
| WHITE METAL LANTERN| White Metal Lantern| white metal lantern|
|CREAM CUPID HEART...|Cream Cupid Heart...|cream cupid heart...|
|KNITTED UNION FLA...|Knitted Union Fla...|knitted union fla...|
|RED WOOLLY HOTTIE...|Red Woolly Hottie...|red woolly hottie...|
|SET 7 BABUSHKA NE...|Set 7 Babushka Ne...|set 7 babushka ne...|
|GLASS STAR FROSTE...|Glass Star Froste...|glass star froste...|
|HAND WARMER UNION...|Hand Warmer Union...|hand warmer union...|
|HAND WARMER RED P...|Hand Warmer Red P...|hand warmer red p...|
|ASSORTED COLOUR B...|Assorted Colour B...|assorted colour b...|
+--------------------+--------------------+--------------------+
only showing top 10 rows

>>> df . select ( ltrim ( lit ( " HELLO " )) . alias ( "ltrim" ), rtrim ( lit ( " HELLO " )) . alias ( "rtrim" ), trim ( lit ( " HELLO " )) . alias ( "trim" ), lpad ( lit ( "HELLO" ), 3 , " " ) . alias ( "lp" ), rpad ( lit ( "HELLO" ), 10 , " " ) . alias ( "rp" )) . show ( 2 ) 
+------+------+-----+---+----------+
| ltrim| rtrim| trim| lp|        rp|
+------+------+-----+---+----------+
|HELLO | HELLO|HELLO|HEL|HELLO     |
|HELLO | HELLO|HELLO|HEL|HELLO     |
+------+------+-----+---+----------+


>>> reg_str = 'BLACK|WHITE|RED|GREEN|BLUE'
>>> df.select(regexp_replace(col('Description'), reg_str, 'COLOR').alias('color_clean'), col('Description')).show(2)
+--------------------+--------------------+
|         color_clean|         Description|
+--------------------+--------------------+
|COLOR HANGING HEA...|WHITE HANGING HEA...|
| COLOR METAL LANTERN| WHITE METAL LANTERN|
+--------------------+--------------------+

>>> df.select(regexp_extract(col('Description'), '(WHITE|BLACK)', 1).alias('color'), col('Description')).show(2) # first occurance
+-----+--------------------+
|color|         Description|
+-----+--------------------+
|WHITE|WHITE HANGING HEA...|
|WHITE| WHITE METAL LANTERN|
+-----+--------------------+
only showing top 2 rows

>>> df.select(translate(col('Description'), 'WHITE', '1337'), col('Description')).
+-----------------------------------+--------------------+
|translate(Description, WHITE, 1337)|         Description|
+-----------------------------------+--------------------+
|               1337 3ANG3NG 3AR7...|WHITE HANGING HEA...|
|                   1337 M7AL LAN7RN| WHITE METAL LANTERN|
|               CRAM CUP3D 3AR7S ...|CREAM CUPID HEART...|
+-----------------------------------+--------------------+

>>> containsWhite = instr(col('Description'), 'WHITE') >= 1
>>> df.withColumn('hasWhite', containsWhite).where('hasWhite')\
... .select('Description').show(3, False)
+----------------------------------+
|Description                       |
+----------------------------------+
|WHITE HANGING HEART T-LIGHT HOLDER|
|WHITE METAL LANTERN               |
|RED WOOLLY HOTTIE WHITE HEART.    |
+----------------------------------+



```


- lit(): convert to spark type

- Booleans
  - eqNullSafe()
- Numbers
  - pow(col)
  - round(col), bround(round down if .5)
  - stat.corr(): between 2 col; stat.approxQuantile()
  - df.describe(): summary statistics
  - monotonically_increasing_id()

- Strings
  - initcap(): words sep by space
  - upper(), lower()
  - lpad(), rpad(); ltrim(), rtrim(); trim()
  - Regex: 
    - regexp_extract()
    - regexp_replace()
    - translate()
  - instr()
  - locate()

Dates and timestamps
Handling null
Complex types
User-defined functions


#### Grouping and Aggregation Queries


#### Joining DataFrames 


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




