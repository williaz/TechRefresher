## Required Skills
### Transform, Stage, and Store
Convert a set of data values in a given format stored in HDFS into new data values or a new data format and write them into HDFS. 
```bash
/etc/hadoop/conf/core-site.xml
fs.defaultFS # url

/etc/hadoop/conf/hdfs-site.xml
dfs.blocksize
dfs.replication

hadoop fs -help

hadoop fs -put localPath remotePath # copyFromLocal
hadoop fs -ls -h remotePath # -R: all, human read
hdfs fsck file -blocks

hadoop fs -du -s -h remotePath

hadoop fs -tail remotePath

hadoop fs -get # copyToLocal
hadoop fs -cp # one to anther hdfs

gunzip part*

/etc/hadoop/conf/yarn-site.xml
resourcemanager

/etc/spark/conf/spark-env.xml
```

- **Load** data from HDFS for use in Spark applications
```py
pyspark --master yarn -- num-executors 6 --executor-cores 2 --executor-memory 3G

orig = read
selectExpr.groupBy
write

df.selectExpr('_c0 as id', '_c2 as name')
```


- **Write** the results back into HDFS using Spark
```py
to 1 file. no parition: repartiton VS coalesce
manaul add header
text format with ,
```
- Read and write files in a variety of file **formats**
```py
DataFrameReader.format(...).option("key", "value").schema(...).load() 
DataFrameWriter.format(...).option(...).partitionBy(...).bucketBy(...).sortBy(...).save() 

# export PYSPARK_PYTHON=/usr/local/bin/python3

# CSV
>>> csvFile = spark.read.format('csv')\
... .option('header', 'true')\
... .option('mode', 'FAILFAST')\
... .option('inferSchema', 'true')\
... .load('flight-data/csv/2010-summary.csv')

>>> csvFile.write.format('csv').mode('overwrite')\
... .option('sep', '\t').save('tmp/tsv-temp.tsv')

# validation
>>> import os
>>> os.system('ls -ltr tmp/tsv-temp.tsv')
>>> os.system('head -3 tmp/tsv-temp.tsv/part*')
>>> os.system('tail -3 tmp/tsv-temp.tsv/part*')
>>> os.system('vim tmp/tsv-temp.tsv/part*')



# JSON
>>> spark.read.format('json')\                       
... .option('mode', 'FAILFAST')\
... .option('inferSchema', 'true')\                  
... .load('flight-data/json/2010-summary.json').show(5)

>>> cvsvFile.write.format('json')\
... .mode('overwrite')\
... .save('tmp/json-temp.json')


# parquet
>>> spark.read.format('parquet')\
... .load('flight-data/parquet/2010-summary.parquet') 

>>> cvsvFile.write.format('parquet').mode('overwrite')\
... .save('tmp/parquet-temp.parquet')

# orc
>>> spark.read.format('orc')\
... .load('flight-data/orc/2010-summary.orc')

>>> cvsvFile.write.format('orc')\
... .mode('overwrite')\
... .save('tmp/orc-temp.orc'


## JDBC
$ pyspark --driver-class-path sqlite-jdbc-3.30.1.jar --jars sqlite-jdbc-3.30.1.jar

>>> driver = 'org.sqlite.JDBC'
>>> path = '/XXXX/flight-data/jdbc/my-sqlite.db'
>>> url = 'jdbc:sqlite:'+ path
>>> tableName = 'flight_info'
>>> dbDf = spark.read.format('jdbc')\
... .option('url', url)\
... .option('dbtable', tableName)\
... .option('driver', driver).load()


>> props = {'driver': 'org.sqlite.JDBC'}
>>> predicates = [ "DEST_COUNTRY_NAME != 'Sweden' OR ORIGIN_COUNTRY_NAME != 'Sweden'" , "DEST_COUNTRY_NAME != 'Anguilla' OR ORIGIN_COUNTRY_NAME != 'Anguilla'" ] 
>>> colName = 'count'
>>> lowerBound = 0
>>> upperBound = 40000
>>> spark.read.jdbc(url, tableName, column= colName,\
... properties=props, lowerBound=lowerBound, upperBound=upperBound, numPartitions=10).count()

>>> csvFile.write.jdbc(newPath, tableName,\
... mode='append', properties=props)              

# text
>>> spark.read.text('flight-data/csv/2010-summary.csv')\
... .selectExpr('split(value, ",") as rows').show(4) 

>>> csvFile.limit(10).select('DEST_COUNTRY_NAME', 'count')\
... .write.partitionBy('count')\
... .text('tmp/txt-temp3.txt')

```


- Perform standard extract, transform, load (**ETL**) processes on data using the Spark API
```py
>>> type(spark.read)
<class 'pyspark.sql.readwriter.DataFrameReader'>
>>> csvFile = spark.read.schema("""destination STRING, home STRING, count INT""")\
... .csv('flight-data/csv/2010-summary.csv', inferSchema=True, header=True)

>>> csvFile.printSchema()
root
 |-- destination: string (nullable = true)
 |-- home: string (nullable = true)
 |-- count: integer (nullable = true)

>>> csvFile.write.csv('tmp/csv-mydata.csv', header=True, mode='overwrite')

```

### Data Analysis
Use Spark SQL to interact with the metastore programmatically in your applications. Generate reports by using queries against loaded data.
- Use **metastore** tables as an input source or an output sink for Spark applications

- Understand the fundamentals of **querying** datasets in Spark
```py

>>> df = spark.read.format('json').load("flight-data/json/2015-summary.json")

>>> df.columns
['DEST_COUNTRY_NAME', 'ORIGIN_COUNTRY_NAME', 'count']
>>> df.select('ORIGIN_COUNTRY_NAME').show(3)


>>> df.createOrReplaceTempView('2015_summary')

>>> df.select(\
... expr("ORIGIN_COUNTRY_NAME as home"),\
... col('ORIGIN_COUNTRY_NAME'),\
... column("DEST_COUNTRY_NAME" ))\
... .show(3)

>>> spark.sql("""select ORIGIN_COUNTRY_NAME as home from 2015_summary limit 4""").show()
+-------------+
|         home|
+-------------+
|      Romania|
|      Croatia|
|      Ireland|
|United States|
+-------------+


>>> df.selectExpr('ORIGIN_COUNTRY_NAME', 'DEST_COUNTRY_NAME', '(ORIGIN_COUNTRY_NAME != DEST_COUNTRY_NAME) as is_abroad').show(3)

>>> df.selectExpr('avg(count)', 'count(distinct(DEST_COUNTRY_NAME))').show(1)

>>> df.selectExpr('*', '1 as dummy').show(3)
>>> df.select(expr('*'),\
... lit(1).alias("dummy")).show(3)

>>> df.selectExpr('ORIGIN_COUNTRY_NAME', 'DEST_COUNTRY_NAME', '(ORIGIN_COUNTRY_NAME = DEST_COUNTRY_NAME) as same_country').show(3)

# return list fo row
>>> df.take(3)

>>> df.limit(2).collect()

# sql
>>> spark.read.json('flight-data/json/2015-summary.json')\
... .createOrReplaceTempView('summary_view')
>>> spark.sql("""\
... select DEST_COUNTRY_NAME, sum(count)
... from summary_view
... group by DEST_COUNTRY_NAME
... """)\
... .show(4)

```


- **Filter** data using Spark
```py
# where/filter chain
>>> df.where('count < 2').show(3)
>>> df.where('count < 2').where('DEST_COUNTRY_NAME != "United States"').show(3)

# Boolean col for filtering
>>> df.where('InvoiceNo != 536365').select('InvoiceNo', 'Description').show(5, False) # no truncate

>>> priceFlt = col('UnitPrice') > 500
>>> descFlt = instr(df.Description, 'POSTAGE') >= 1
>>> df.where(df.StockCode.isin('DOT')).where(priceFlt | descFlt).select('StockCode', 'UnitPrice', 'Description').show(3, False)

>>> DotFlt = col('StockCode') == 'DOT'
>>> df.withColumn('isExpensive', DotFlt & (priceFlt | descFlt))\
... .where('isExpensive').select('UnitPrice', 'isExpensive').show(5)

# left_semi
>>> person.join(graduateProgram, joinExpr, 'left_semi').show(5)
+---+----------------+----------------+---------------+
| id|            name|graduate_program|   spark_status|
+---+----------------+----------------+---------------+
|  0|   Bill Chambers|               0|          [100]|
|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  2|Michael Armbrust|               1|     [250, 100]|
+---+----------------+----------------+---------------+

# left_anti
>>> graduateProgram.join(person, joinExpr, 'left_anti').show(5)
+---+-------+----------+-----------+
| id| degree|department|     school|
+---+-------+----------+-----------+
|  2|Masters|      EECS|UC Berkeley|
+---+-------+----------+-----------+

```


- Write queries that calculate **aggregate** statistics
```py
>>> df.select(min('UnitPrice').alias('min'),\
... max('UnitPrice').alias('max'))\
... .show(1)

>>> df.select(sum('UnitPrice').alias('sum'),\
... sumDistinct('UnitPrice').alias('sumDist'),\
... avg('UnitPrice').alias('avg'))\
... .show(1)

>>> df.select(var_pop('UnitPrice').alias('var_pop'),\
... var_samp('UnitPrice').alias('var_samp'),\
... stddev_samp('UnitPrice').alias('std_samp'),\
... stddev_pop('UnitPrice').alias('std_pop'),\
... skewness('UnitPrice').alias('skew'),\
... kurtosis('UnitPrice').alias('kurt'))\
... .show(1)

>>> df.select(corr('Quantity', 'UnitPrice'),\
... covar_pop('Quantity', 'UnitPrice'),\
... covar_samp('Quantity', 'UnitPrice'))\
... .show(1)

>>> df.agg(collect_set('Country'),\
... collect_list('Country'))\
... .show()

>>> df.agg(collect_set('Country').alias('region')).select(size('region')).show()

>>> df.agg(collect_set('Country').alias('region')).show(1, False)

## groupBy

>>> df.groupBy('InvoiceNo', 'CustomerId').count().show(5)
>>> df.groupBy('InvoiceNo').agg(\
... count('Quantity').alias('quan'),\
... expr('count(Quantity)')).show(3)

# rollup
>>> rolledUpDf = df.rollup('StockCode', 'InvoiceNo')\
... .agg(sum('Quantity').alias('total'))\
... .select('StockCode', 'InvoiceNo', 'total')

>>> rolledUpDf.show()

>>> rolledUpDf.where('StockCode is NULL and InvoiceNo is null').show()
# cube
>>> cubeDf = df.cube('StockCode', 'InvoiceNo')\
... .agg(grouping_id().alias('level'), sum('Quantity').alias('total'))\
... .select('level', 'StockCode', 'InvoiceNo', 'total')
>>> cubeDf.show()

# pivot
>>> pivoted = df.groupBy('StockCode').pivot('Country').sum('Quantity')
>>> pivoted.show(3)

# Window
>>> from pyspark.sql.window import Window
>>> winSpec = Window\
... .partitionBy('CustomerId', 'date')\
... .orderBy(desc('Quantity'))\
... .rowsBetween(Window.unboundedPreceding, Window.currentRow)
>>> maxQ = max(col('Quantity')).over(winSpec)
>>> pDenseRank = dense_rank().over(winSpec)
>>> pRank = rank().over(winSpec)

>>> dfWithDate.where('customerId is NOT NULL')\
... .orderBy('customerId')\
... .select(\
... col('customerId'),\
... col('date'),\
... col('Quantity'),\
... pDenseRank.alias('qDR'),\
... pRank.alias('qR'),\
... maxQ.alias('mP')).show()

```
- **Join** disparate datasets using Spark
```py
>>> joinExpr = person['graduate_program'] == graduateProgram['id']
>>> person.join(graduateProgram, joinExpr).show(3)
>>> person.join(graduateProgram, joinExpr, 'outer').show(5)
>>> person.join(graduateProgram, joinExpr, 'left_outer').show(5)
>>> person.join(graduateProgram, joinExpr, 'right_outer').show(5)
>>> person.join(graduateProgram, joinExpr, 'left_semi').show(5)
>>> graduateProgram.join(person, joinExpr, 'left_anti').show(5)
>>> graduateProgram.join(person, joinExpr, 'cross').show(5)
>>> graduateProgram.crossJoin(person).show()

```
- Produce **ranked or sorted** data
```py

>>> df.orderBy('count').show(5)

>>> df.orderBy(col('count').desc()).show(5) 

# desc not working in df.sort(expr('count desc')).show(5), 

>>> df.orderBy(col('count').desc()).limit(5).show()

# Window
>>> from pyspark.sql.window import Window
>>> winSpec = Window\
... .partitionBy('CustomerId', 'date')\
... .orderBy(desc('Quantity'))\
... .rowsBetween(Window.unboundedPreceding, Window.currentRow)
>>> maxQ = max(col('Quantity')).over(winSpec)
>>> pDenseRank = dense_rank().over(winSpec)
>>> pRank = rank().over(winSpec)

>>> dfWithDate.where('customerId is NOT NULL')\
... .orderBy('customerId')\
... .select(\
... col('customerId'),\
... col('date'),\
... col('Quantity'),\
... pDenseRank.alias('qDR'),\
... pRank.alias('qR'),\
... maxQ.alias('mP')).show()


```



### Configuration
This is a practical exam and the candidate should be familiar with all aspects of generating a result, not just writing code.
- Supply command-line options to change your application **configuration**, such as increasing available memory
```py

>>> type(spark)
<class 'pyspark.sql.session.SparkSession'>
>>> help(spark.read)

>>> spark.stop()
>>> spark = SparkSession.builder.appName('Demo').config('spark.ui.port', '0').getOrCreate()
>>> spark.sparkContext
<SparkContext master=local[*] appName=Demo>
>>> spark.sparkContext.getConf()
<pyspark.conf.SparkConf object at 0x106e84090>
>>> spark.sparkContext.getConf().getAll()
[('spark.driver.port', '64137'), ('spark.ui.port', '0'), ('spark.sql.catalogImplementation', 'hive'), ('spark.rdd.compress', 'True'), ('spark.app.id', 'local-1592361987030'), ('spark.serializer.objectStreamReset', '100'), ('spark.master', 'local[*]'), ('spark.executor.id', 'driver'), ('spark.submit.deployMode', 'client'), ('spark.driver.host', 'bos-mbp-24477.attlocal.net'), ('spark.ui.showConsoleProgress', 'true'), ('spark.app.name', 'Demo')]


```

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

- lazy evaluation: In general, Spark will fail only at job execution time rather than DataFrame definition time 


#### Creating DataFrames from Data Sources
```py
# CSV
>>> csvFile = spark.read.format('csv')\
... .option('header', 'true')\
... .option('mode', 'FAILFAST')\
... .option('inferSchema', 'true')\
... .load('flight-data/csv/2010-summary.csv')
>>> csvFile.write.format('csv').mode('overwrite')\
... .option('sep', '\t').save('tmp/tsv-temp.tsv')

tsv-temp.tsv williaz$ ls -ltr
total 16
-rw-r--r--  1 williaz  staff  7073 Jun 13 18:36 part-00000-a00a7f42-0d49-49d4-a7ae-46240f5a4939-c000.csv
-rw-r--r--  1 williaz  staff     0 Jun 13 18:36 _SUCCESS

# JSON
>>> spark.read.format('json')\                       
... .option('mode', 'FAILFAST')\
... .option('inferSchema', 'true')\                  
... .load('flight-data/json/2010-summary.json').show(5)
+-----------------+-------------------+-----+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|    United States|            Romania|    1|
|    United States|            Ireland|  264|
|    United States|              India|   69|
|            Egypt|      United States|   24|
|Equatorial Guinea|      United States|    1|
+-----------------+-------------------+-----+
only showing top 5 rows

>>> cvsvFile.write.format('json')\
... .mode('overwrite')\
... .save('tmp/json-temp.json')


{"DEST_COUNTRY_NAME":"United States","ORIGIN_COUNTRY_NAME":"India","count":69}
{"DEST_COUNTRY_NAME":"Egypt","ORIGIN_COUNTRY_NAME":"United States","count":24}
{"DEST_COUNTRY_NAME":"Equatorial Guinea","ORIGIN_COUNTRY_NAME":"United States","count":1}


# parquet
>>> spark.read.format('parquet')\
... .load('flight-data/parquet/2010-summary.parquet') 
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: bigint]
>>> cvsvFile.write.format('parquet').mode('overwrite')\
... .save('tmp/parquet-temp.parquet')

# orc
>>> spark.read.format('orc')\
... .load('flight-data/orc/2010-summary.orc')
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: bigint]

>>> cvsvFile.write.format('orc')\
... .mode('overwrite')\
... .save('tmp/orc-temp.orc'


## JDBC
$ pyspark --driver-class-path sqlite-jdbc-3.30.1.jar --jars sqlite-jdbc-3.30.1.jar

>>> driver = 'org.sqlite.JDBC'
>>> path = '/XXXX/flight-data/jdbc/my-sqlite.db'
>>> url = 'jdbc:sqlite:'+ path
>>> tableName = 'flight_info'
>>> dbDf = spark.read.format('jdbc')\
... .option('url', url)\
... .option('dbtable', tableName)\
... .option('driver', driver).load()

>>> dbDf
DataFrame[DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string, count: decimal(20,0)]
>>> dbDf.show(3)
[Stage 0:>                                                                                                +-----------------+-------------------+-----+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|    United States|            Romania|    1|
|    United States|            Ireland|  264|
|    United States|              India|   69|
+-----------------+-------------------+-----+

>>> dbDf.filter('DEST_COUNTRY_NAME = "United States"').explain()
== Physical Plan ==
*(1) Scan JDBCRelation(flight_info) [numPartitions=1] [DEST_COUNTRY_NAME#6,ORIGIN_COUNTRY_NAME#7,count#8] PushedFilters: [*IsNotNull(DEST_COUNTRY_NAME), *EqualTo(DEST_COUNTRY_NAME,United States)], ReadSchema: struct<DEST_COUNTRY_NAME:string,ORIGIN_COUNTRY_NAME:string,count:decimal(20,0)>


>> props = {'driver': 'org.sqlite.JDBC'}
>>> predicates = [ "DEST_COUNTRY_NAME != 'Sweden' OR ORIGIN_COUNTRY_NAME != 'Sweden'" , "DEST_COUNTRY_NAME != 'Anguilla' OR ORIGIN_COUNTRY_NAME != 'Anguilla'" ] 
>>> colName = 'count'
>>> lowerBound = 0
>>> upperBound = 40000
>>> spark.read.jdbc(url, tableName, column= colName,\
... properties=props, lowerBound=lowerBound, upperBound=upperBound, numPartitions=10).count()
255

# JDBC write
>>> csvFile = spark.read.format('csv')\
... .option('header', 'true')\
... .option('mode', 'FAILFAST')\
... .option('inferSchema', 'true')\
... .load('flight-data/csv/2010-summary.csv')
>>> newPath = 'jdbc:sqlite://tmp/sqlite-tmp.db'

>>> csvFile.write.jdbc(newPath, tableName,\
... mode='overwrite', properties=props)
20/06/14 11:55:44 WARN JdbcUtils: Requested isolation level 1 is not supported; falling back to default isolation level 8
>>> spark.read.jdbc(newPath, tableName, properties=props).count()
255
>>> csvFile.write.jdbc(newPath, tableName,\
... mode='append', properties=props)              
20/06/14 11:57:21 WARN JdbcUtils: Requested isolation level 1 is not supported; falling back to default isolation level 8
>>> spark.read.jdbc(newPath, tableName, properties=props).count()
510

# text
>>> spark.read.text('flight-data/csv/2010-summary.csv')\
... .selectExpr('split(value, ",") as rows').show(4) 
+--------------------+
|                rows|
+--------------------+
|[DEST_COUNTRY_NAM...|
|[United States, R...|
|[United States, I...|
|[United States, I...|
+--------------------+


>>> csvFile.limit(10).select('DEST_COUNTRY_NAME', 'count')\
... .write.partitionBy('count')\
... .text('tmp/txt-temp3.txt')

$ cd txt-temp3.txt/
:txt-temp3.txt williaz$ ls -ltr
total 0
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=1
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=24
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=25
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=29
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=44
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=54
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=69
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=264
drwxr-xr-x  4 williaz  staff  128 Jun 14 15:18 count=477
-rw-r--r--  1 williaz  staff    0 Jun 14 15:18 _SUCCESS



```


- 6 core data sources
  - CSV
  - JSON
    - in spark, line-delimited JSON as more stable(appendable) 
    - multiLine . When you set this option to true , you can read an entire file as one json object 

  - Rarquet
    - spark default
    - colum-oriented with columnar compression
    - more efficient ready than JSON/CSV
    - supports complex type
    - schema is built into the file itself
    - be aware of incompatible due to Spark versions
  - ORC
    - optimized for Hive
  - JDBC
    - classpath + jars
    - option: url, dbtable, driver, user, password
    - option: numPartitions => how much r/w in parallel
    - Spark gather schema from dbtable itself and map to its data types
    - PushedFilters: 
      - it translate
      - use query as dbtable
  - text
    - textFile: ignore partitioned dir name[404]
    - text: partitioning on read and write
    - write
      - only one string column
      - partitionBy: partition col as folder
      - option: maxRecordsPerFile

```py
DataFrameReader.format(...).option("key", "value").schema(...).load() 
```
- Read
  - optional: format default Parquest 
  - opional schema: from ds or schemaInference
  - required path option, or pass map
  - spark.read

- read mode: deal with malformed records
  - permissive
    - default
    - set null
    - _corrupt_records column
  - dropMalformed
  - failFast

#### Saving DataFrames to Data Sources

```py
DataFrameWriter.format(...).option(...).partitionBy(...).bucketBy(...).sortBy(...).save() 
```
- Write
  - PartitionBy , bucketBy , and sortBy work only for file-based data sources; 
  - required path option
  - df.write
  - one file **per partition** will be written out, and the entire DataFrame will be written out as a folder. 


- save mode
  - append: if exist file
  - overwrite: discard exist one
  - errorIfExists
    - default
  - ignore: do nothing

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

# Date
>>> dataDf = spark.range(10)\
... .withColumn('today', current_date())\
... .withColumn('now', current_timestamp())
>>> dataDf.printSchema()
root
 |-- id: long (nullable = false)
 |-- today: date (nullable = false)
 |-- now: timestamp (nullable = false)

>>> dataDf.show(4)
+---+----------+--------------------+
| id|     today|                 now|
+---+----------+--------------------+
|  0|2020-06-07|2020-06-07 10:55:...|
|  1|2020-06-07|2020-06-07 10:55:...|
|  2|2020-06-07|2020-06-07 10:55:...|
|  3|2020-06-07|2020-06-07 10:55:...|
+---+----------+--------------------+
only showing top 4 rows

>>> dataDf.select(date_sub(col('today'), 5), date_add(col('today'), 5)).show(1)
+------------------+------------------+
|date_sub(today, 5)|date_add(today, 5)|
+------------------+------------------+
|        2020-06-02|        2020-06-12|
+------------------+------------------+
only showing top 1 row




>>> dataDf.withColumn('week_ago',\                       
... date_sub(col('today'), 7))\
... .select(datediff(col('week_ago'), col('today')))\
... .show(1)
+-------------------------+
|datediff(week_ago, today)|
+-------------------------+
|                       -7|
+-------------------------+
only showing top 1 row

>>> dateFmt = 'yyyy-MM-dd'
>>> dataDf.select(\
... to_date(lit('2019-01-01'), dateFmt).alias('start'),\
... to_date(lit('2020-02-28'), dateFmt).alias('end')\
... )\
... .select(months_between(col('start'), col('end') ))\
... .show(1)
+--------------------------------+
|months_between(start, end, true)|
+--------------------------------+
|                    -13.87096774|
+--------------------------------+
only showing top 1 row

>>> dataDf.select(\
... to_date(lit('2019-01-01')),\
... to_date(lit('2020-06-06')))\
... .show(1)
+---------------------+---------------------+
|to_date('2019-01-01')|to_date('2020-06-06')|
+---------------------+---------------------+
|           2019-01-01|           2020-06-06|
+---------------------+---------------------+
only showing top 1 row


>>> dateFmt = 'yyyy-dd-MM'
>>> dataDf.select(\
... to_date(lit('2019-01-01')),\
... to_date(lit('2020-06-06')))\
... .printSchema()
root
 |-- to_date('2019-01-01'): date (nullable = true)
 |-- to_date('2020-06-06'): date (nullable = true)
 
 >>> cleanDateDf = spark.range(1)\
... .select(\
... to_date(lit('2019-01-01'), dateFmt).alias('start'),\ ... to_date(lit('2020-28-02'), dateFmt).alias('end'))\
... 
>>> cleanDateDf.show()
+----------+----------+
|     start|       end|
+----------+----------+
|2019-01-01|2020-02-28|
+----------+----------+

>>> cleanDateDf.select(to_timestamp(col('start'), dateFmt)).show()
+-----------------------------------+
|to_timestamp(`start`, 'yyyy-dd-MM')|
+-----------------------------------+
|                2019-01-01 00:00:00|
+-----------------------------------+

>>> cleanDateDf.where(col('end') > lit('2019-06-30')).show()
+----------+----------+
|     start|       end|
+----------+----------+
|2019-01-01|2020-02-28|
+----------+----------+


# NULL
>>> df.select(coalesce(col('Description'), col('CustomerId'))).show(4)
+---------------------------------+
|coalesce(Description, CustomerId)|
+---------------------------------+
|             WHITE HANGING HEA...|
|              WHITE METAL LANTERN|
|             CREAM CUPID HEART...|
|             KNITTED UNION FLA...|
+---------------------------------+
only showing top 4 rows

# na.drop
>>> df1 = df.na.drop('all', subset=['StockCode', 'InvoiceNo'])
>>> df1.count()
3108
>>> df.na.drop('any')
DataFrame[InvoiceNo: string, StockCode: string, Description: string, Quantity: int, InvoiceDate: timestamp, UnitPrice: double, CustomerID: double, Country: string]
>>> df.count()
3108
>>> df.na.drop('any').count()
1968
>>> df.count()
3108
>>> df.na.drop('all').count()
3108
>>> df1 = df.na.drop()
>>> df1.count()
1968

# ?
>>> df1 = df.na.fill(5, subset=['Description'])
>>> df.na.replace([""], ['UNKNOWN'], 'Description').where('Description = "UNKNOWN"').show(3)


>>> df1 = df.select(\
... struct('InvoiceNo', 'Description').alias('complex')\ 
... )
>>> df1.select('complex.Description').show(2)
+--------------------+
|         Description|
+--------------------+
|WHITE HANGING HEA...|
| WHITE METAL LANTERN|
+--------------------+
only showing top 2 rows

>>> df1.select('complex').show(2)
+--------------------+
|             complex|
+--------------------+
|[536365, WHITE HA...|
|[536365, WHITE ME...|
+--------------------+
only showing top 2 rows

>>> df.select(split(col('Description'), " ").alias('descArr'))\
... .selectExpr('descArr[0]').show(2)                   
+----------+
|descArr[0]|
+----------+
|     WHITE|
|     WHITE|
+----------+
only showing top 2 rows

>>> df.select(size(split(col('Description'), " "))).show(2)
+---------------------------+
|size(split(Description,  ))|
+---------------------------+
|                          5|
|                          3|
+---------------------------+
only showing top 2 rows

>>> df.select(array_contains(split(col('Description'), " "), 'WHITE')).show(2)
+--------------------------------------------+
|array_contains(split(Description,  ), WHITE)|
+--------------------------------------------+
|                                        true|
|                                        true|
+--------------------------------------------+
only showing top 2 rows

>>> df.withColumn('splitted', split(col('Description'), ' '))\
... .withColumn('exploded', explode(col('splitted')))\
... .select('Description', 'InvoiceNo', 'exploded').show(5)
+--------------------+---------+--------+
|         Description|InvoiceNo|exploded|
+--------------------+---------+--------+
|WHITE HANGING HEA...|   536365|   WHITE|
|WHITE HANGING HEA...|   536365| HANGING|
|WHITE HANGING HEA...|   536365|   HEART|
|WHITE HANGING HEA...|   536365| T-LIGHT|
|WHITE HANGING HEA...|   536365|  HOLDER|
+--------------------+---------+--------+
only showing top 5 rows


>>> df.select(create_map(col('Description'), col('InvoiceNo')).alias('cMap')).show(3, False)
+----------------------------------------------+
|cMap                                          |
+----------------------------------------------+
|[WHITE HANGING HEART T-LIGHT HOLDER -> 536365]|
|[WHITE METAL LANTERN -> 536365]               |
|[CREAM CUPID HEARTS COAT HANGER -> 536365]    |
+----------------------------------------------+
only showing top 3 rows

>>> df.select(create_map(col('Description'), col('InvoiceNo')).                                                TERN"]').show(3, False)
+-------------------------+
|cMap[WHITE METAL LANTERN]|
+-------------------------+
|null                     |
|536365                   |
|null                     |
+-------------------------+
only showing top 3 rows

>>> df.select(create_map(col('Description'), col('InvoiceNo')).alias('cMap')).selectExpr('explode(cMap)').show(3, False)
+----------------------------------+------+
|key                               |value |
+----------------------------------+------+
|WHITE HANGING HEART T-LIGHT HOLDER|536365|
|WHITE METAL LANTERN               |536365|
|CREAM CUPID HEARTS COAT HANGER    |536365|
+----------------------------------+------+
only showing top 3 rows


>>> jsonDF = spark.range(1).selectExpr("""\
... '{"myJSONKey" : {"myJSONVal" : [1, 2, 3]}}' as jsonStr"""\
... )

>>> jsonDF.show(1, False)
+------------------------------------------+
|jsonStr                                   |
+------------------------------------------+
|{"myJSONKey" : {"myJSONVal" : [1, 2, 3]}}|
+------------------------------------------+

>>> jsonDF.withColumn('jCol',\                          
... get_json_object(col('jsonStr'), "$.myJSONKey.myJSONVal[1]")).show(1)
+--------------------+----+
|             jsonStr|jCol|
+--------------------+----+
|{"myJSONKey" : {"...|   2|
+--------------------+----+

>>> jsonDF.select(\                                     
... get_json_object(col('jsonStr'), "$.myJSONKey.myJSONVal[1]").alias("vCol"),\
... json_tuple(col('jsonStr'), "myJSONKey"))\
... .show(2)
+----+--------------------+
|vCol|                  c0|
+----+--------------------+
|   2|{"myJSONVal":[1,2...|
+----+--------------------+

# favor withColumn over as and alias

>>> df.selectExpr('(InvoiceNo, Description) as myStt')\
... .withColumn('nJson', to_json(col('myStt')))\
... .select(from_json(col('nJson'), pSchema),\
... col('nJson')).show(2)
+--------------------+--------------------+
|jsontostructs(nJson)|               nJson|
+--------------------+--------------------+
|[536365, WHITE HA...|{"InvoiceNo":"536...|
|[536365, WHITE ME...|{"InvoiceNo":"536...|
+--------------------+--------------------+
only showing top 2 rows

# UDF
# DF func
>>> udfDat = spark.range(5).toDF('num')
>>> def pow3(dd):
...     return dd ** 3
... 
>>> pow3udf = udf(pow3)
>>> udfDat.select(pow3udf(col('num'))).show(3)
+---------+
|pow3(num)|
+---------+
|        0|
|        1|
|        8|
+---------+
only showing top 3 rows

# register as Spark SQL func
>>> spark.udf.register('pow3py', pow3, IntegerType())
<function pow3 at 0x102632950>
>>> udfDat.selectExpr('pow3py(num)').show(3)
+-----------+
|pow3py(num)|
+-----------+
|          0|
|          1|
|          8|
+-----------+
only showing top 3 rows

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

- Dates and timestamps
  - spark.conf.sessionLocalTimeZone: Java format
  - TimestampType class supports only second-level precision, 
  - date_sub(), date_add()
  - datediff(), months_between()
  - to_timestamp() : always requires a format 
  - specify our string according to the right format of yyyy-MM-dd if we’re comparing a date
    - Implicit type casting is an easy way to shoot yourself in the foot, 


  
- Handling null
  - coalesce(): first not null col
  - SQL: ifnull, nullif, nvl, nvl2
  - na.drop()/drop('any'): if any col /drop('all'): if all col are null
  - 

- Complex types
  - Struct: DF within DF
  - array
    - split, size, array_contains
    - explode(): col array to rows
  - map
    - create_map
    - explode
- JSON
  - get_json_object
  - json_tuple: one level of nesting
  - to_json, from_json
  
- User-defined functions (UDF)
  - operate on data, record by record
  - register as temp func in SparkSession or Context
  - better write UDF in Scala or Java for performance
  - spark.udf.register()
  - udf()



#### Grouping and Aggregation Queries

```py

>>> df.select(\
... count('StockCode'),\
... countDistinct('StockCode'),\
... approx_count_distinct('StockCode', 0.1),\
... first('StockCode'),\
... last('StockCode')).show(1)

+----------------+-------------------------+--------------------------------+-----------------------+----------------------+
|count(StockCode)|count(DISTINCT StockCode)|approx_count_distinct(StockCode)|first(StockCode, false)|last(StockCode, false)|
+----------------+-------------------------+--------------------------------+-----------------------+----------------------+
|            3108|                     1351|                            1382|                  21249|                85226A|
+----------------+-------------------------+--------------------------------+-----------------------+----------------------+

>>> df.select(min('UnitPrice').alias('min'),\
... max('UnitPrice').alias('max'))\
... .show(1)
+---+------+
|min|   max|
+---+------+
|0.0|607.49|
+---+------+

>>> df.select(sum('UnitPrice').alias('sum'),\
... sumDistinct('UnitPrice').alias('sumDist'),\
... avg('UnitPrice').alias('avg'))\
... .show(1)
+------------------+------------------+-----------------+
|               sum|           sumDist|              avg|
+------------------+------------------+-----------------+
|12904.249999999998|2302.7200000000003|4.151946589446589|
+------------------+------------------+-----------------+


>>> df.select(var_pop('UnitPrice').alias('var_pop'),\
... var_samp('UnitPrice').alias('var_samp'),\
... stddev_samp('UnitPrice').alias('std_samp'),\
... stddev_pop('UnitPrice').alias('std_pop'),\
... skewness('UnitPrice').alias('skew'),\
... kurtosis('UnitPrice').alias('kurt'))\
... .show(1)
+------------------+------------------+------------------+------------------+-----------------+------------------+
|           var_pop|          var_samp|          std_samp|           std_pop|             skew|              kurt|
+------------------+------------------+------------------+------------------+-----------------+------------------+
|244.48899231761075|244.56768204799943|15.638659854603892|15.636143780280698|34.12992647682192|1264.9297964102832|
+------------------+------------------+------------------+------------------+-----------------+------------------+

>>> df.select(corr('Quantity', 'UnitPrice'),\
... covar_pop('Quantity', 'UnitPrice'),\
... covar_samp('Quantity', 'UnitPrice'))\
... .show(1)
+-------------------------+------------------------------+-------------------------------+
|corr(Quantity, UnitPrice)|covar_pop(Quantity, UnitPrice)|covar_samp(Quantity, UnitPrice)|
+-------------------------+------------------------------+-------------------------------+
|     -0.04112314436835551|            -16.95454821409937|            -16.960005101197567|
+-------------------------+------------------------------+-------------------------------+

>>> df.agg(collect_set('Country'),\
... collect_list('Country'))\
... .show()
+--------------------+---------------------+
|collect_set(Country)|collect_list(Country)|
+--------------------+---------------------+
|[France, Australi...| [United Kingdom, ...|
+--------------------+---------------------+

>>> df.agg(collect_set('Country').alias('region')).select(size('region')).show()
+------------+
|size(region)|
+------------+
|           7|
+------------+

>>> df.agg(collect_set('Country').alias('region')).show(1, False)
+-----------------------------------------------------------------------+
|region                                                                 |
+-----------------------------------------------------------------------+
|[France, Australia, Norway, EIRE, Germany, United Kingdom, Netherlands]|
+-----------------------------------------------------------------------+

## groupBy

>>> df.groupBy('InvoiceNo', 'CustomerId').count().show(5)
+---------+----------+-----+
|InvoiceNo|CustomerId|count|
+---------+----------+-----+
|   536596|      null|    6|
|   536530|   17905.0|   23|
|   536414|      null|    1|
|   536400|   13448.0|    1|
|   536550|      null|    1|
+---------+----------+-----+

>>> df.groupBy('InvoiceNo').agg(\
... count('Quantity').alias('quan'),\
... expr('count(Quantity)')).show(3)
+---------+----+---------------+
|InvoiceNo|quan|count(Quantity)|
+---------+----+---------------+
|   536596|   6|              6|
|   536597|  28|             28|
|   536414|   1|              1|
+---------+----+---------------+
only showing top 3 rows

# rollup
>>> rolledUpDf = df.rollup('StockCode', 'InvoiceNo')\
... .agg(sum('Quantity').alias('total'))\
... .select('StockCode', 'InvoiceNo', 'total')

>>> rolledUpDf.show()
+---------+---------+-----+
|StockCode|InvoiceNo|total|
+---------+---------+-----+
|    22914|   536368|    3|
|    71053|   536396|    6|
|    22111|   536423|   16|
|    21123|   536520|    1|
|    21742|   536522|    1|
|    21774|   536544|    2|
|    48194|   536562|    2|
|   15056P|   536575|   48|
|    22771|   536582|   24|
|    22775|   536592|    3|
|    21556|     null|    6|
|    82552|     null|   14|
|    20711|     null|   20|
|    22477|     null|    2|
|    22145|     null|    1|
|    37446|     null|   17|
|    21164|     null|    1|
|    22184|     null|    1|
|    22224|   536384|    6|
|    22730|   536395|    4|
+---------+---------+-----+
only showing top 20 rows

>>> rolledUpDf.where('StockCode is NULL and InvoiceNo is null').show()
+---------+---------+-----+
|StockCode|InvoiceNo|total|
+---------+---------+-----+
|     null|     null|26814|
+---------+---------+-----+

>>> cubeDf = df.cube('StockCode', 'InvoiceNo')\
... .agg(grouping_id().alias('level'), sum('Quantity').alias('total'))\
... .select('level', 'StockCode', 'InvoiceNo', 'total')
>>> cubeDf.show()
+-----+---------+---------+-----+
|level|StockCode|InvoiceNo|total|
+-----+---------+---------+-----+
|    0|    22914|   536368|    3|
|    0|    71053|   536396|    6|
|    0|    22111|   536423|   16|
|    0|    21123|   536520|    1|
|    0|    21742|   536522|    1|
|    0|    21774|   536544|    2|
|    0|    48194|   536562|    2|
|    0|   15056P|   536575|   48|
|    0|    22771|   536582|   24|
|    0|    22775|   536592|    3|
|    2|     null|   536365|   40|
|    1|    21556|     null|    6|
|    1|    82552|     null|   14|
|    1|    20711|     null|   20|
|    1|    22477|     null|    2|
|    1|    22145|     null|    1|
|    1|    37446|     null|   17|
|    1|    21164|     null|    1|
|    1|    22184|     null|    1|
|    0|    22224|   536384|    6|
+-----+---------+---------+-----+
only showing top 20 rows



# pivot
>>> pivoted = df.groupBy('StockCode').pivot('Country').sum('Quantity')
>>> pivoted.show(3)
+---------+---------+----+------+-------+-----------+------+--------------+
|StockCode|Australia|EIRE|France|Germany|Netherlands|Norway|United Kingdom|
+---------+---------+----+------+-------+-----------+------+--------------+
|    22728|     null|null|    24|   null|       null|  null|            13|
|    21259|     null|null|  null|   null|       null|  null|             8|
|    21889|     null|  24|  null|   null|       null|  null|            28|
+---------+---------+----+------+-------+-----------+------+--------------+
only showing top 3 rows

# Window
>>> from pyspark.sql.functions import *
>>> dfWithDate = df.withColumn('date', \
... to_date(col('InvoiceDate'), 'MM/d/yyy H;mm')
... )
>>> from pyspark.sql.window import Window
>>> winSpec = Window\
... .partitionBy('CustomerId', 'date')\
... .orderBy(desc('Quantity'))\
... .rowsBetween(Window.unboundedPreceding, Window.currentRow)
>>> maxQ = max(col('Quantity')).over(winSpec)
>>> pDenseRank = dense_rank().over(winSpec)
>>> pRank = rank().over(winSpec)

>>> dfWithDate.where('customerId is NOT NULL')\
... .orderBy('customerId')\
... .select(\
... col('customerId'),\
... col('date'),\
... col('Quantity'),\
... pDenseRank.alias('qDR'),\
... pRank.alias('qR'),\
... maxQ.alias('mP')).show()
+----------+----------+--------+---+---+---+
|customerId|      date|Quantity|qDR| qR| mP|
+----------+----------+--------+---+---+---+
|   12431.0|2010-12-01|      24|  1|  1| 24|
|   12431.0|2010-12-01|      24|  1|  1| 24|
|   12431.0|2010-12-01|      12|  2|  3| 24|
|   12431.0|2010-12-01|       8|  3|  4| 24|
|   12431.0|2010-12-01|       6|  4|  5| 24|
|   12431.0|2010-12-01|       6|  4|  5| 24|
|   12431.0|2010-12-01|       6|  4|  5| 24|
|   12431.0|2010-12-01|       4|  5|  8| 24|
|   12431.0|2010-12-01|       4|  5|  8| 24|
|   12431.0|2010-12-01|       4|  5|  8| 24|
|   12431.0|2010-12-01|       3|  6| 11| 24|
|   12431.0|2010-12-01|       2|  7| 12| 24|
|   12431.0|2010-12-01|       2|  7| 12| 24|
|   12431.0|2010-12-01|       2|  7| 12| 24|
|   12433.0|2010-12-01|      96|  1|  1| 96|
|   12433.0|2010-12-01|      72|  2|  2| 96|
|   12433.0|2010-12-01|      72|  2|  2| 96|
|   12433.0|2010-12-01|      50|  3|  4| 96|
|   12433.0|2010-12-01|      48|  4|  5| 96|
|   12433.0|2010-12-01|      48|  4|  5| 96|
+----------+----------+--------+---+---+---+



```

- DF-level Agg Func
  - count(): 
    - count(*) inlcude null; count(col) exclude null
  - countDistinct()
  - approx_count_distinct(col, max_err/rsd)
  - first(), last(): based on row, not val
  - min(), max()
  - sum()
  - sumDistinct()
  - avg(), mean()
  - var_samp, var_pop, stddev_pop, stddev_samp
  - corr, covar_pop, covar_samp
  - skewness, kurtosis
  - Complex types: agg(): collect_set, collect_list
  
- group by
  - agg(): pass in arbitrary expr
  - with expr
  - with mapping
- window
  - rolling agg
  - VS group by:
    - A group-by takes data, and every row can go only into **one grouping**. 
    - A window function calculates a return value for every input row of a table based on a group of rows, called a frame. Each row can fall into one or **more frames**. 
  - 3 kinds func: ranking, analytic, agg
  


- grouping set: an agg across multiple groups
  - Grouping sets depend on null values for aggregation levels. If you do not filter-out null values, you will get incorrect results. This applies to cubes, rollups, and grouping sets. 
  - rollup: subtotal, grant total
  - cube: all combination's 
  - grouping_id
    - gives us a column specifying the level of aggregation 
    - cube: 0: any comb; 1 by col1, 2 by col2; max: total
    - rollup: 0: anyl 1 by col1; nax: total
  - pivot

#### Joining DataFrames 

```py

person = spark . createDataFrame ([ ( 0 , "Bill Chambers" , 0 , [ 100 ]), ( 1 , "Matei Zaharia" , 1 , [ 500 , 250 , 100 ]), ( 2 , "Michael Armbrust" , 1 , [ 250 , 100 ])]) \
. toDF ( "id" , "name" , "graduate_program" , "spark_status" ) 

graduateProgram = spark . createDataFrame ([ ( 0 , "Masters" , "School of Information" , "UC Berkeley" ), ( 2 , "Masters" , "EECS" , "UC Berkeley" ), ( 1 , "Ph.D." , "EECS" , "UC Berkeley" )]) \
. toDF ( "id" , "degree" , "department" , "school" ) 

sparkStatus = spark . createDataFrame ([ ( 500 , "Vice President" ), ( 250 , "PMC Member" ), ( 100 , "Contributor" )]) \
. toDF ( "id" , "status" ) 

# inner join
>>> joinExpr = person['graduate_program'] == graduateProgram['id']
>>> person.join(graduateProgram, joinExpr).show(3)
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+
| id|            name|graduate_program|   spark_status| id| degree|          department|     school|
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+
|  0|   Bill Chambers|               0|          [100]|  0|Masters|School of Informa...|UC Berkeley|
|  1|   Matei Zaharia|               1|[500, 250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|  2|Michael Armbrust|               1|     [250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+

# outer
>>> person.join(graduateProgram, joinExpr, 'outer').show(5)
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+
|  id|            name|graduate_program|   spark_status| id| degree|          department|     school|
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+
|   0|   Bill Chambers|               0|          [100]|  0|Masters|School of Informa...|UC Berkeley|
|   1|   Matei Zaharia|               1|[500, 250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|   2|Michael Armbrust|               1|     [250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|null|            null|            null|           null|  2|Masters|                EECS|UC Berkeley|
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+

# left_ outer
>>> person.join(graduateProgram, joinExpr, 'left_outer').show(5)
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+
| id|            name|graduate_program|   spark_status| id| degree|          department|     school|
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+
|  0|   Bill Chambers|               0|          [100]|  0|Masters|School of Informa...|UC Berkeley|
|  1|   Matei Zaharia|               1|[500, 250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|  2|Michael Armbrust|               1|     [250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
+---+----------------+----------------+---------------+---+-------+--------------------+-----------+

# right_outer
>>> person.join(graduateProgram, joinExpr, 'right_outer').show(5)
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+
|  id|            name|graduate_program|   spark_status| id| degree|          department|     school|
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+
|   0|   Bill Chambers|               0|          [100]|  0|Masters|School of Informa...|UC Berkeley|
|   1|   Matei Zaharia|               1|[500, 250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|   2|Michael Armbrust|               1|     [250, 100]|  1|  Ph.D.|                EECS|UC Berkeley|
|null|            null|            null|           null|  2|Masters|                EECS|UC Berkeley|
+----+----------------+----------------+---------------+---+-------+--------------------+-----------+

# left_semi
>>> person.join(graduateProgram, joinExpr, 'left_semi').show(5)
+---+----------------+----------------+---------------+
| id|            name|graduate_program|   spark_status|
+---+----------------+----------------+---------------+
|  0|   Bill Chambers|               0|          [100]|
|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  2|Michael Armbrust|               1|     [250, 100]|
+---+----------------+----------------+---------------+

# left_anti
>>> graduateProgram.join(person, joinExpr, 'left_anti').show(5)
+---+-------+----------+-----------+
| id| degree|department|     school|
+---+-------+----------+-----------+
|  2|Masters|      EECS|UC Berkeley|
+---+-------+----------+-----------+

# cross
>>> graduateProgram.join(person, joinExpr, 'cross').show(5)
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+
| id| degree|          department|     school| id|            name|graduate_program|   spark_status|
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+
|  0|Masters|School of Informa...|UC Berkeley|  0|   Bill Chambers|               0|          [100]|
|  1|  Ph.D.|                EECS|UC Berkeley|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  1|  Ph.D.|                EECS|UC Berkeley|  2|Michael Armbrust|               1|     [250, 100]|
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+

>>> graduateProgram.crossJoin(person).show()
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+
| id| degree|          department|     school| id|            name|graduate_program|   spark_status|
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+
|  0|Masters|School of Informa...|UC Berkeley|  0|   Bill Chambers|               0|          [100]|
|  0|Masters|School of Informa...|UC Berkeley|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  0|Masters|School of Informa...|UC Berkeley|  2|Michael Armbrust|               1|     [250, 100]|
|  2|Masters|                EECS|UC Berkeley|  0|   Bill Chambers|               0|          [100]|
|  2|Masters|                EECS|UC Berkeley|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  2|Masters|                EECS|UC Berkeley|  2|Michael Armbrust|               1|     [250, 100]|
|  1|  Ph.D.|                EECS|UC Berkeley|  0|   Bill Chambers|               0|          [100]|
|  1|  Ph.D.|                EECS|UC Berkeley|  1|   Matei Zaharia|               1|[500, 250, 100]|
|  1|  Ph.D.|                EECS|UC Berkeley|  2|Michael Armbrust|               1|     [250, 100]|
+---+-------+--------------------+-----------+---+----------------+----------------+---------------+

# broadcast
>>> graduateProgram.join(broadcast(person), joinExpr, 'left_outer').explain()
== Physical Plan ==
*(2) BroadcastHashJoin [id#171L], [graduate_program#157L], LeftOuter, BuildRight
:- *(2) Project [_1#163L AS id#171L, _2#164 AS degree#172, _3#165 AS department#173, _4#166 AS school#174]
:  +- Scan ExistingRDD[_1#163L,_2#164,_3#165,_4#166]
+- BroadcastExchange HashedRelationBroadcastMode(List(input[2, bigint, true]))
   +- *(1) Project [_1#147L AS id#155L, _2#148 AS name#156, _3#149L AS graduate_program#157L, _4#150 AS spark_status#158]
      +- *(1) Filter isnotnull(_3#149L)
         +- Scan ExistingRDD[_1#147L,_2#148,_3#149L,_4#150]
```
- join expression
  - Complex type: Any expression is a valid join expression, assuming that it returns a Boolean: 

- join types: determines what should be in the result set
  - inner join
    - default
  - outer
  - left_outer
  - right_outer
  - left_semi
    - filter
    - keep duplicate key
  - left_anti
    - NOT IN
  - natural join
  - cross join: cartesian product, m X n
    - spark.sql.crossJoin.enable 

- join strategy
  - big table to big table: shuffle join
  - big to small: broadcast/copy small to all nodes
  - small to small: let spark decide

- partition your data like 2 diff df in same machine to acoide shuffle

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


```py
>>> spark.sql('select 1 + 1').show()
+-------+
|(1 + 1)|
+-------+
|      2|
+-------+

>>> spark.read.json('flight-data/json/2015-summary.json')\
... .createOrReplaceTempView('summary_view')
>>> spark.sql("""\
... select DEST_COUNTRY_NAME, sum(count)
... from summary_view
... group by DEST_COUNTRY_NAME
... """)\
... .show(4)
20/06/14 15:50:04 WARN ObjectStore: Failed to get database global_temp, returning NoSuchObjectException
+-----------------+----------+
|DEST_COUNTRY_NAME|sum(count)|
+-----------------+----------+
|         Anguilla|        41|
|           Russia|       176|
|         Paraguay|        60|
|          Senegal|        40|
+-----------------+----------+

```
- Spark
  - OLAP, not for low-latency queries
  - can connect Hive metastores
- spark-sql
- spark.sql

#### Querying Tables in Spark Using SQL 
#### Querying Files and Views
#### The Catalog API 


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




