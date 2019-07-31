
- VPC(Virtual Private Cloud) - Networking Services, dedicated to a single AWS account.




- Region, Availability Zone(AZ), Edge location
  - Region: physical locatoion, contains 2+ AZ
  - AZ: data centers
  - Edge: endpoint for CloudFront(CDN)
  - \# Edge Locations > \# Availability Zones> \# Regions.

### IAM
- Identity Access Management
- user, group, policy, role
  - new users: NO permissions; Access key ID&Secret AK
- universal

- cloudWatch: billing alarm

### S3

- object-based(0b - 5TB)
  - Key(name), value, versionId, metadata, subresouce(access)
- universal namespace
- in Buckets, no for OS
- 99.99% Availablity; 11X9 Durability
- Tiered Storage (4-5ptr)
- lifecycle
- version
- encryption
- MFA delete
- HTTP 200 upload
- Access control list, bucket policy
  - new: default as Private

- Read after Write consistency for PUTs of new obj
- Eventual Consistency for overwrite PUTs and DELETEs(take time)

#### 6 Tiers
- S3 standard
- S3 -IA(Infrequennt Accessed): instant access
- S3 One Zone -IA
- S3 Intelligent Tiering
- S3 Glacier(Archive) 
- S3 Glacier Deep Archive: 12hr retrieval

#### Encryption 
- in Transit: SSL/TLS
- at REST
  - S3 Managed Keys SSE-S3
  - SSE-KMS(key manage)
  - SSE-C(custom)
- client side



#### Version
- delete is a version
- once enabled, versioing cannot be disabled
- must enabled for Cross Region Replication
  - only new updated be replicated automatically
  - delete marker are not replicated

#### Lifecycle
- auto moving to tiers

#### S3 Transfer Acceleration
- CloudFront Edge Network
  - Edege location
  - Origin: origin of the files
  - Distribution: a set of Edge
  - Web Distribution: websites
  - RTMP: Media Streaming
  - only write also
  - cached for the life of TTL(Time To Live)
  - can clear cached obj, but will be charged

- Snowball: big disk for data transport, i/o S3
- Storage Gateway
  - file Gateway: store directly
  - Volumne Gateway
    - Stord volume: stored on site,async backup to S3
    - Cached volumne: cache on site
  - Gateway Virtual Tape Lib

#### [S3 FAQs](https://aws.amazon.com/s3/faqs/)

### EC2
- Elastic Compute Cloud
- Price: on demand, Reserved, Spot, Dedicated hosts


#### Security Group
- All inbound traffic is bliced by default
- All outboud is allowed
- change effect immediately
- any # of EC2 under
- Stateful: if allow inbound, outbound auto allow
- can't block specific IP(use Network Access Control list)
- allow rules, no deny rules

#### EBS
- Elastic Block store
- virtual hard drive(SSD, HDD)
- Termination Protection is turned off by default, you must turn it on
- On an EBS-backed instance, the default action is for the root EBS volumn to be deleted when the instance is terminated.
- EBS Root volumes of your defualt AMI is can;t be encrypted.
- Voumn exist on EBS; Snapshot on S3
- snapshot are incremental, changes
- create AMI(Amazon Machine Image) from volume or snapshot
- volume AZ same with EC2
- to move EC2 volume AZ: take a snapshot, create an AMI from the snapshot, use AMI launch in new AZ
- to move EC2 volumn from region: snap, AMI, copy AMI to another region, launch

#### snapshot
- can either stop instance or not when create a snapshot for EBS volumes
- can create AMI for both Volumes and Snapshot
= can change EBS volume size, storage type on the fly
- Volume share same AZ as the EC2 instance


#### AMI
- Instance store volume(Ephemeral Storage) cant be stoped, or lose data
- reboot, not lose data for both
- default, ROOT volumes will be delted on termination; EBS can config to keep

- snapshot of encryted volume/volumnes restored from encrypted are encrypted auto
- only unencrypted snapshot can be shared among AWS accounts or public
- encrypt root device volume steps:
  - create a snapshpt of the unencrypted volume
  - create a copy of the snapshot and select encrypt option
  - create AMI from it
  - use AMI to lauch

#### CloudWatch
- for monitoring performance
- for EC2, monitor events 5m by default
- to have 1m interval by turning on detailed monitorung
- CloudWatch alarms to trigger notification
- CloudTrail for auditing(API call)

- CLI(command line)
```bash
curl http://IP/latest/meta-data
curl http://IP/latest/user-data
```

#### EFS
- Elastic File System
- can be share among EC2
- NFSv4 protocol
- petabytes
- multiAZ
- Read after Write Consistency

#### EC2 Placement Group
- clustered
  - in same AZ
  - for low latency, high network throghtput
- spread
  - risk, seprate
- unique naming in AWS account
- can't merge, can't move existing EC2 into a group


### DB
- RDS(OLTP): 6 SQL, with Encryption as rest enable
- DynamoDB (No SQL)
- Red Shift OLAP
- Elasticache

- Multi-AZ for disaster Recovery(DR)
- Read replicas for performance


- RDS on virtual machinee which you can't log in
- RDS is Not Serverless(Aurora is)

#### Backup
- Auto Backup: allow to recover in retention period(1- 35)
- DB Snapshot(users manul initiate)
- when resotre from both, get new RDS instance
#### Read Replica
- Read Replica must with backup turn on
- can be Multi-AZ and diff region
- can be promoted to master

#### DynamoDB
- stored on DDS 
- spread across 3 geo
- Eventual Consistent read(Default)
- Stongly consistent reads

#### Redshift
- for BI
- one AZ

#### Aurora
- default: 6 = 2 copies * 3 AZ
- Automated failover is only availble with Aurora replicas
- 2 types replicas: Aurora & MySql
- backup by default

#### Elasticache
- Reddis is Multi-AZ
- can do back ups and restores of Reddis
- need scale horizontally use Memcached

```
Which of the following is not a feature of DynamoDB?
With new RDS DB instances, automated backups are enabled by default?
Under what circumstances would I choose provisioned IOPS over standard storage when creating an RDS instance?
If you are using Amazon RDS Provisioned IOPS storage with a Microsoft SQL Server database engine, what is the maximum size RDS volume you can have by default?
In RDS, changes to the backup window take effect ________.
When you add a rule to an RDS DB security group, you must specify a port number or protocol.
```




