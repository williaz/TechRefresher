
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

### Route 53
- DNS is on port 53
- defualt 50 name limit
- ELB don't have pre-define IPv4, you resolve to them using a DNS name
- Alias Rec, CNAME(batman) -> alias
- buy domain, up to 3d to register

#### Routing policy

- Simple: random return values for multi
- Weighted: traffic
- Health check: fail -> remove; SNS notify for it
- Latency 
- Failover: active/passive server
- Geolocation: set by user location
- Geoproximity(Trafiic Flow only): on user and your resource location
- Multivalue Answer: simple + health check


### VPC
- Virtual Private Cloud
- subnet: 1 AZ
- route table
- gateway
- securtiy groups - startful; Network Access control list - stateless
- VPC peering as in a same intranet, no transitive peering
- 5 VPC per region by default


- When you create a VPC, a default Route table, Network Access control list(NACL) and a default security group are created
- It won't create any subnets, nor will it create a default internet gateway
- randomized AZ
- Amazon reserver 5 IP addesses within your subnets
- 1 Internet Gateway per VPC
- An Application Load Balancer must be deployed into at least two subnets.
- When create a new security group, all outbound traffic is allowed by default.

#### NAT instance
- when creating NAT ins, disable Source/Destination check on the ins
- NAT instances must be in a public subnet
- must be a route out of the private subnet
- traffic size depends on the ins size
- behind a Security group

#### NAT Gateway
- preferred
- 5Gbps - 45, scale auto
- not associsate security 
- auto assign public IP
- no need disable S/D check

#### ACL
- default ACL, all allow i/o
- custom deny all i/o
- each subnet in you VPC must be assiocaited with a ACL
- block IP using ACL,not SG
- one to many: ACL: subnet

#### Flow log
- can't enable flow logs for peer VPC
- can't tag
- no monitor AMZN DNS, windows instance(AMZn license), metadata, reserved IP for default VPC router

#### Bastion Host
- used t securly administer EC2 instances(using SSH or RDP)
- can't use NAt gateway as a Bastion host

#### Direct connect
- connects your data center to AWS
- high throughput
- stable

#### Endpoints
- Interface
- Gateway
  - S3
  - DynamoDB


### HA


#### LB
- 3 types:
  - App: Intelligent
  - network: high speed
  - classic
- 504 Error: gateway time out
- X-Forwarded-For get IPv4 address of end user
- Instances monitored by ELB are reported as: InService,OutofService
- Health Check
- LB has own DNS name, never give IP
- [ELB FAQ](https://aws.amazon.com/elasticloadbalancing/faqs/)

#### theory
- Sticky session
- cross zone LB: m AZ
- Path pattern: path based routing


#### HA Arch
- m AZ, m Region
- Read Replica for RDS
- cost
- Adding Resillience: Resiliency can be described as the ability to a system to self heal after damage or an event. 
- autoscaling
- scale out: spread; scale up: higher
#### CloudFormation
 - scripting cloud env, template
#### Beanstalk
 - quicklu deploy and manage app
 - auto handle capacity, LB, scaling, health check
 

### APP
#### SQS
- pull based
- standard(default), general ordered
- FIFO
- 256K is size, if bigger in S3
- kept 1m - 14d; default retention 4d
- Visiblity Timeout; redo
  - max: 12h
- at least once proceed
- long polling
#### SWF
- SWF vs SQS
  - Retention: up to 1y; 14d
  - task-oriented API: message
  - never dup;
  - auto track all tasks/events;
- Actors
  - starter: init
  - Decider: control
  - activity workers
#### SNS
- messaging
- push
#### Elastic Transoder
- media transcoder, format
#### API gateway
- 5-10Q
- at a high level
- can caching
- low cost, auto scale
- can throttle 
- log to CloudWatch
- CORS
##### Kinesis
- load and analyze streaming data
- Stream 
 - shard(persistence)
 - 24h - 7d retention
- Firehouse
  - Optional: lambda
  - to S3/Elasticsearch
- Analytic
  - stream
  - firehouse

#### Cognito
- Federation allowes user to authenticate  with A web Identity Provider(G,F A)
- get token for WIP
- a broker between your app and Web ID provider
- user pool: user based: reg, auth, account recovery
- Identity pools: access to AWS resource


### Serverless
#### Lambda
- scale out auto
- func: independent
- serverless: S3, API Gataway, Aurora, 
- trigger other lambda
- X-ray to debug
- do thing globally
- triggers: Each time a Lambda function is triggered, an isolated instance of that function is invoked. Multiple triggers result in multiple concurrent invocations, one for each time it is triggered



```
These core services are also called foundational ser- vices. Examples include regions, AZs, Amazon Virtual Private Cloud (VPC), Amazon EC2 servers, ELB, AWS Auto Scaling, storage, networking, databases, AWS IAM, and security.


```
- Cloud Computing
  - On demand: IT infrastructute as a resource
  - Accessible from the Internet
  - Pay as you go model

- 3 Models
  - IaaS: Infrastucture
  - PaaS: Platform, AWS
  - SaaS: Software, Salesforce

- Deploy Model
  - All in cloud
  - Hybrid
  - On premise/Private cloud: virtualization-compute node: VMware ESX

- Region
  - AZ
  - Edge(Points Of Presence): content dilivery: CloundFront & Route 53
  - Redundant

- Shared Security model
  - AWS for the security of the cloud: phyical
  - Customers: in the cloud: data


- Compute
  - EC2
  - EC2 Auto Scaling
  - Lambda: code
  - ECS(EC2 Contaiiner Service)
  - Elastic Beanstalk: web app
  - Lightsail: VPS(virtual private server)
  - Batch
- Networking
  - VPC: data center
  - Route 53
  - ELB
  - Direct Connet
- Security
  - IAM (Identiy and Access Management)
  - Inspector: install and manage EC2 instances
  - Directory Service: MS Active dir
  - Web App Firewall(WAF): IP
  - Shield: DDos(Distributed denial-of-service)
- Storage
  - S3(Simple Shared Storage)
  - Glacier
  - EBS(Block): disk
  - EFS(File System)
  - Storage Gateway: integrate
  - Import/Export OPtions
    - Snowball
    - Direct Connect
  - CloudFront
- DB
  - RDS
  - DynamoDB
    - docu
    - key-value
  - Redshift
    - columnar format
  - ElastiCache
  - Aurora
- Analytic
  - Athena: SQL on S3
  - EMR; Hadoop
  - Easicsearch
  - CloudSearch
  - Data Pipeline
  - Kinesis
  - QuickSight
- APP
  - API gateway
  - Step fucntion: visualize component's workflow
  - SWF(Simple Workflow Service)
  - Elastic Transcoder
- Dev tools
  - CodeCommit: source control servie
  - CodePipeline: CI/CD
  - CodeBuild
  - CodeDeploy
- Management tools
  - CloudFormation: template of resoure stack
  - Servcie Catalog
  - OpsWorks: Chef
  - CloudWatch
  - Config
  - CloudTrail: Api call
- Messaging
  - SNS
  - SES(Email)
  - SQS(Queue)
- Migration
- AI
  - Lex: chatbot
  - Polly: lieflike spech
  - Rekognition: vision
  - ML: predictive
- IoT(Internet of Things)
  - IoT
  - Greengrass
  - Iot Button: Dash Button
- Mobile
  - Cognito: social identity, autentication
  - Mobile Hub
  - Device Farm: tst on real 
  - Mobile Analytics














  
  








