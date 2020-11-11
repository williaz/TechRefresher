- Physical memory
  - **RAM**
  - only storage directly accessibly to the **CPU** and holds the instructions of **programs** to execute
- Virtual memory
  - deal with RAM **shortage**
  - transferring pages of data from random access memory to **disk** storage. 
- Memory mapping
  - map file data into the process address space.
  - mmap()
  - File mapping: r/w the process' virtual memory cause file r/w

## [Architect](https://webassets.mongodb.com/_com_assets/collateral/MongoDB_Architecture_Guide.pdf)
- MQL: CRUD
  - Drivers: Client side lib
- Data Model
  - Replication
  - r/w concern
- Storage layer
  - WiredTiger
- shard
- replica set
- HA, failover
- config server: cluster setting

## JSON, BSON
- That BSON is a binary JSON data format
- BSON: support numerical data types


# RDBMS VS MongoDB
- sparse table => multiple tables

- wide column
  - data stored per col
- docu: polymorphic data structure

- Table VS Collection
- Row VS Document( a document is self-descriptive)
- join VS Embedding/Linking/$lookup
- view
- ACID
  - Atomicity: all or nothing
  - Consistency: only save valid data
  - Isolation: tranx don't affect each other
  - Durablilty: never lost written data
  - MongoDB can store data in one document that is traditionally stored across rows in different tables, the need for transactions is much less than in relational systems.
  - tranx since 4.2
  - limit updates to a single docu to achive ACID, over tranx

- DS
  - resiliency
  - many servers
  - process talk eath other
  - network speed

- Replica set
  - complete copy
  - HA
  - durability
  
- Shard
  - partition
  - placing data closer to users

- R/W operation gurantees
  - read concern
    - local: def
    - maj
  - write concern: 
    - primary: of 1
    - majority
  - read perference: which node to read
    - primary: def
    - nearest
    - my anlaysis node

- ER VS workload

- workload
  - size data
  - quantify ops
  - qualify ops
- Relationship
  - identify
  - quantify
  - embed or link
- Patterns
  - recognize
  - apply

- Data Staleness

- reference
  - many side: huge
  - one piece is freq use with memory issue
  
- [Schema design pattern](https://www.mongodb.com/blog/post/building-with-patterns-a-summary)
  - computed pattern
  - Bucket pattern: IoT


- JSON schema to validate
  - required field, types, values
  - shape: There is a construct in JSON Schema called oneOf that let you specify two sub-schemas.

- sharding
- change Stream to trigger checking

## Query: 
- MQL
  - db.<collection>.find({<field:value>, ...})
  - condtion: db.<collection>.find({<field:{<ops:value>}, ...})
  - limit
  - sort
- aggregation pipeline/stages


## HA

- Automatic election
- evolve schema without downtime

https://docs.mongodb.com/manual/sharding/


# M001
- instance, replica, cluster
- 

## JSON/BSON
- JSON: keys in ""
- BSON
  - mongodump
  - mongostore --drop dump
- JSON
  - mongoexport
  - mongoimport

- it iterates through the cursor.
```json
{"birth year": 1961, "start station name": "Howard St & Centre St"}

show dbs

use sample_training

show collections

db.zips.find({"state": "NY"})
db.zips.find({"state": "NY"}).count()

db.zips.find({"state": "NY", "city": "ALBANY"})

db.zips.find({"state": "NY", "city": "ALBANY"}).pretty()
```
- ```_id```: uniq value in collection
  - ObjectId, can other type

### CRUD
```
db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },
                       { "_id": 3, "test": 3 }],{ "ordered": false })
                       
                       
// $set, $inc, $push
db.zips.updateOne({ "zip": "12534" }, { "$set": { "population": 17630 } })
updateMany


db.inspections.deleteMany({ "test": 1 })
db.inspections.deleteOne({ "test": 3 })
db.inspection.drop()
```
- When all collections are dropped from a database, the database no longer appears in the list of databases when you run show dbs.


















