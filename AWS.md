
## EC2, VPC, RDS, Kinesis, ACL, SG, serverless
[EC2](https://tutorialsdojo.com/aws-cheat-sheet-amazon-elastic-compute-cloud-amazon-ec2/)
[RDS](https://tutorialsdojo.com/amazon-relational-database-service-amazon-rds/)
[DynamoDB](https://tutorialsdojo.com/amazon-dynamodb/)


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
  - for low latency, high network throghtput in between
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


## AWS AIO
```
These core services are also called foundational ser- vices. Examples include regions, AZs, Amazon Virtual Private Cloud (VPC), Amazon EC2 servers, ELB, AWS Auto Scaling, storage, networking, databases, AWS IAM, and security.



```
- Cloud Computing
  - On demand: IT infrastructute as a resource
  - Accessible from the Internet
  - Pay as you go model

- 3 Models
  - IaaS: Infrastucture: EC2
  - PaaS: Platform: RDS
  - SaaS: Software: Salesforce, S3

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

### Storage

- Object storage: docu/img/video with flat structure metadata
  - immured/ unstructured data
  - app manage storage inquiry
- Block storage: EC2 disk, EBS, DB
- File storage: EFS 

#### S3
- all obj stored in a flat namespace organized by buckets
- regional sercieL ato replicated within
- Advantages:
  - Simple to use
  - Scalable: unlimited storage
  - Durable 9 of 9 after .
  - Secured
  - High performace: mulitpart, region
  - Available
  - Low cost
  - Easy to manage
  - Easy integration
- Uses cases:
  - Backup
  - Tape replacement
  - Static website hosting
  - App hosting ??
  - Disaster revobery
  - Content Distribution
  - Data lake
  - Private repository
- Basics
  - Bucket: container, name must be unique universal; folder name under it can be any
    - by default, not cross region replication
    - Object inside has a key: path
    - REST API
  - Write once, read many
  - read after write consistency for new objects: sync to multi facilities before returnig success
  - eventually consistent system aprt from new ones
  - no Object Locking, latest timestamp opt wins
- Performance
  - consider partition when the workload to run ob an S3 bucket is > 100 PUT/LOST/DELETE rps or 300 GET rps
  - S3 auto partition based on the first 6 to 8 characters in object key prefix
    - rename object key
    - reverse key
    - MD5 hash of the char seq
    - URL-safe implementation and avoiding the ‘+’ and ‘/’ characters, instead using ‘-‘ (dash) and ‘_’ (underscore) in their places.
- S3 Select: SQL on object
- Encrytion
  - In transit
    - HTTPS and use SSL-encryted endpoints
    - S3 encryption client: KMS(Key Management Service) encrytion keys
  - At rest: SSE(Server Side Encryption)
    - SSE-SE(S3 Key Management)
    - SSE-C(customer-provided keys): provide custom encryption key in upload/download request
    - SSE-KMS: separate permission, audit trail
- Access control
  - ARN(Amazon resurce name): uniquely identify AWS resources, URI
  - IAM policy to a group, user, role; finegrained control
  - Bucket policy: condition, resource based, finegrained control
  - ACL(access contril list) apply to bucket level and object level; coarse-grained control
- Storage CLass(attribute on objects, sa,e buckets)
  - S3 standard(SLA): 11 nines durability; sync cross 3 AZ
  - S3 Standard Infrequent Access(IA)
  - S3 Reduced Redundancy Storage(RRS): lower durability: 4 nines, sustain 1 AZ loss
  - S3 One Zone IA: rapid access, store in 1 AZ only
  - Glacier
  - move class by creating a lifecycle policy, CLI S3 copy, S3 console, SDK
- Versioning
  - once enable, can't disable, but can suspend
  - on bucket level
- Lifecycle
  - attached to a bucket, can enable on file prefix
  - Expiration action
  - Transition action
- Cross Region Replication(CRR)
  - Management-Replication
  - auto async copy
  - must enable visioning on both source and target bucket(can in same or diff AWS account)
  - options for changing storage class, ownership
  - can't CCR in same region
  - (rule)status-Enabled to start right away
  - select IAM
  - CRR copied only the new object after it applied
  - HEAD for meta data includes replication status
- Static hosting
  - Properties
- tips
  - By using Versioning and enabling MFA (Multi-Factor Authentication) Delete, you can secure and recover your S3 objects from accidental deletion or overwrite. 
  - 
#### Glacier
- same durablility of S3
  - This object is in Glacier. You cannot download it or make it public. You cannot edit any property or permission.
- Archive: immuntable, write once; if >100m, multipart upload
  - aggreegate: tar/zip
- vault contains archives
  - need clean archives inside to delete vault
  - IAM t set vault l;evel access policy
  - Vault lock: policy is immutable
  - cold index: inventory, SNS
  - Vault name must be unique in an account and region
- Retrievel: Expediated, standard, bulk

#### EBS
- independent outside the life span of an EC2 instance
- as boot partition or attached to EC2
- can attached to only one EC2 at a time, detach
- AZ level replication
- can create point-in-time consistent snapshot to S3
  - occur asynchronously, no affect on-going r/w
- AFR(annual failure rate): 0.1 -0.2 perc
- Volume
  - IOPS: input/output operation per second 
  - EC2 instance store: local to EC2, no snapshot support
  - EBS SSD backed: transactional workload like DB, latency-sensitive
    - IOPS, small/random workload, transactional
  - EBS HDD-backed: throughput-intensive workload like log processing/mapreduce
    - Throughput, large/sequential workload, infreq access
- for ensuring consistent snapshot, recommend to detach EBS from EC2 when issue snapshot

#### EFS
- auto availble in multi-AZ
- NFS protocol
- A mount target is an NFS v4 endpoiint in your VPC, create for each AZ

#### On-Premise Intg

- transfer > 10TB, on-premise instead of S3

- SGW(Storage Gateway): deploy in VM in your env
  - file gateway
  - volume gateway
    - cached: locally cache
    - stored: stored all locall and async backup to S3
  - tape gateway
- Snowball
  - NAS(newwork-attached storage)
  - Snowball edge: 100TB
  - Snowmobile


##### S3 standard/EFS redundent in a region, EBS redundent in a AZ


### VPC
- private space in the cloud, manage IP namespace
- max VPC size: /16
- once create a VPC, can't alter the size(CIDR block), have to migrate
  - in IPv4, the prefix length /29 gives: 2^32 − 2^9 = 2^3 = 8 addresses.
- in a region
- subnet
  - various subnets in a VPC
  - private, public, VPN-only
  - a subnet tied to only one AZ
  - CIDR blocks can't overlap in subnets
  - for any subnet, AWS reserves first 4 IP and last IP
- Route Table
  - each subnet must have a route table; but multiple subnets can have same route table
  - IPv4 and IPv6 treated separeately
  - auto created the main route table for the VPC, better keep i with only the local route
  - destionation specifies the IP ranges that can be directed to the target
  - target is where the traffic is directed 
  - all subnets that are not explicitly associated with a more specific route table will use Main route table by default.
  
- Internet Gateway (IG)
  - allow VPC to commmunicate with the Internet
  - IG is Horizontally scaled
  - igw-

- Network Address Translation
  - NAT device used for IP{v4 traffic only
  - enable private subnet instance to connect to the Internet, but internet can't initiate a connection to it
  - stateful
  - NAT instance
    - an elastic IP associate with the NAT instance
    - single point of failure
  - NAT gateway
    - meanaged service
    - better availablity and bandwidth
- Egress-only Internet gateway
  - IPv6 version of NAT gateway
  - stateful @@
- Bring Your Own IP Addresses (BYOIP): 
  - You can bring part or all of your public IPv4 address range from your on-premises network to your AWS account. 
  - The ROA authorizes Amazon to advertise an address range under a specific AS number
  - IP pool, use EIP
- Elastic Network Interface(ENI)
  - create one or more network interfaces and attach them to you instance
  - attibutes of the ENI follow along with it
  - can't change/detach default NI(eth0)
  - ENI doesn't impact the network bandwidth
- Elastic IP address
  - static IP address
  - regional, limited to 5 per region
  - EIP is associated with the instacne during the stop@@ and start of EC2, will detached when explicitly terminate EC2.
  - map EIP to EC2, as launch a new EC2, get a new IP
  - support only IPv4
  - no charge as long as EIP is assoicate to a runing EC2
- Security Group
  - virtual firewall, reflected in instance immediately and auto
  - instance level, many to many
  - stateful, outbound = inbound; by default all outbound is allowed, conside all rules
  - always associated with the NI
  - default security group
    - inbound in the same SG
    - can't delete but can change
- NACL
  - optional, sunet level
  - stateless, can allow/deny
  - default NACL, allow all i/o
  - each subnet must be assigned with one and only one ACL
  - apply ASAP from lowest numbered rule, mutiples of 100

- VPC Peering
  - route the traffic across VPC using a private IPv4/6 address
  - NO transitive peering 
  - within same region, can across accounts
  - AWS internal infrastructure(hardware) => no signle point failure
  - steps:
    - accept peering connection, no CIDR block overlapo
    - add a route in route table
    - mod SG/ enable DNS
- VPC endpoint
  - private connection, no leave AWS
  - for S3 and DynamoDB
  - route table to control traffic
- DNS ad VPC
  - IPv4 for AWS 
  - instance in default VPC has public and private IP
  - instance get a public DNS hostname, if both true
    - enableDnsHostnames
    - enableDnsSupport
- DHCP@@@
  - Dynamic Host Config Protocol
  - default domain name and DNS server for instances
  - VPC must have only one DHCP assigned
  - can't modify
  - new instance apply new DHCP, running instance ick up when DHCP lease is renewed
#### Connecting to a VPC
- Virtual private gateway: AWS side, VPN concentrator
- Customer gateway
- private connectivity types:@@@
  - AWS hardware VPN: IPsec(Internet Protocol Seurity)
  - AWS Direct Connect: dedicated private connection
  - VPN CloudHub: multiple sites, simple hub-and spoke model
  - Software VPN: an EC2 ruing VPN app
#### Flow logs
- IP traffice in and out from NI in VPC
#### Default VPC
- a VPC created in each region by default

#### VPC lab
1. create VPC -> assign CIDRs(/16 - /28)
2. get main route table, Network ACL, security group
3. create subnet -> select VPC, AZ, IPv4/6 CIDR block(-5 IP)
4. subnet -> action -> modify auto-assign IP setting -> enable
5. Internet Gateway create -> action -> attach to VPC; 1 IGW per VPC
6. Route table -> select VPC; Edit route -> dest : target(IGW)
7. Route table -> edit association -> add subnet
8. EC2 -> Newtwork: VPC, select subnet
9. EC2 -> SG -> add i/o bound rules for private subnet's EC2


### EC2

- benefits:
  - Time to market: instant deploy
  - Scalability: pike
  - Control: 
  - Reliable
  - Secure
  - Multiple instance type
  - Integration: with other AWS services
  - Cost effective: transfering data from EC2 to S3, DynamoDB, SQS in same Region has no cost 

- types:
  - General(T2, M5, M4, M3)
    - balance
    - T2 burstable performace
  - Compute optimized (C5, C4, C3)
    - media transcoding, app concurrent, long-runing batch, gaming
  - Memory (X12, X1, R4, R3)
    - in memory DB, big data processing engines, Genome
  - Storage (H1, I3, D2)
    - I/O bound app
    - Cache, MapReduce, DB
  - Advanced computing (P3, P2, G3, F1)
    - GPU, CF

- A placement group is a logic grouping of instances within a **single AZ**
  - low latency or high network throughpt
  - better same instance type, enhanced neworking type
  - can't span AZ
  - unique name per AWS account
  
- Storage
  - (Redundant Array of Inexpensive disk, data storage virtualization)RAID0: throughput of i/o multiplied by num of disk; RAID1: data mirroring
  - EBS, persists independently for the life span of EC2 
    - General purpose: default
    - Provisioned IOPS
    - Magnetic: dev
  - instance store(for some EC2,  directly attached, block-device storage): ephemeral storage, always be deleted when stop(instances store backed can't stop) or terminate, though the instacne is EBS-backed; persist when reboot
- lab
  - select preconfig AMI(Amazon Machine Image)
  - config VPC, subnet, SG
  - choose instance type
  - choose AZ, Attach EBS, EIP
  - start
- pricing
  - On-demand
  - Reserved
    - 75% discount 
    - standard
    - convertible: exchange class based on compute req
  - Spot
    - 90% disc
- Shared Tenancy: multi-tenant
- Dedicated hosts: carve as many VM
- Dedicated instances: in VPC on heardware dedicated to a single customer
- lab:
  - create an EC2 instanc eusing an SSH key pair, pem file
- AMI
  - preconfig
  - regional
  - can share through sharing AWS ID
  - using a single AMI, launch diff instance types
  - you can launch encrypted EBS-backed EC2 instances from unencryoted AMI directly
  - lauch permission: pulic, explicit, implicit
  - Obtaining
  - Virtualization 
    - HVM(hardware WM), it's required t use enhanced networking and GPU processing
    - PV(Paravirtual)
- Instance Root Volume  @@@
  - instance store-backed(S3) AMI
    - S3
    - can't stop
    - reboot: data persists
    - terminated/shut down: data gone
    - when use an instance with instance store, you can't detach an store vlolume form one instance and reattach to another
  - EBS-backed AMI
    - data laways persis
    - (by default, delete root volume when terminate)
  - can convet Linux AMI from store-backed to EBS backed; but no for windows
- Lifecycle
  - Lauch
  - start, stop
    - can stop only if EBS-backup
    - once stop, no charge; but charge on EBS
  - Reboot: like for OS, lose nothing
  - Termination
    - Termination protection enabled
    - DeleteOnTemination attribute: 
      - default: delete root devcie volume, preserve any other EBS
  - Retirement: due to irreparable hardware failure
- Connecting
  - public key(in EC2) for encrypt; private key for decrypt
- SG
  - by default
    - allow all outbound 
    - can't change outbound rules for EC2-classic
    - permissive: no deny rule
    - stateful, if send out, always back
    - can change, auto apply shortly
  - rule:
    - Protocol: The SSH protocol uses TCP and port 22.
    - Port range
    - ICMP type and code
    - Source/destination
      - IP address: The /32 denotes one IP address and the /0 refers to the entire network.
      - SG: rule will be stale if the SG is in a peer VPC and the SG or VPC peering are deleted
- ECS  
  - Elastic Container Service
  - manage Docker containers on a cluster of EC2
    - containers isolate the process running on a single OS
    - Container: isolated user space processes
    - image: portable, consistent and immutable; includes dependencies
  - AWS ECS API for cluster management infrastructure
  - Amazon ECS enables you to inject sensitive data into your containers by storing your sensitive data in either AWS Secrets Manager secrets or AWS Systems Manager Parameter Store parameters and then referencing them in your container definition. This feature is supported by tasks using both the EC2 and Fargate launch types.

### IAM
- Authentication
  - access
  - SSO(federation)
- Authorizaion
  - privilege
  - IAM policy- JSON: allow/deny
  - entity: group/user/service
- Auditing
  - CloudTrail
  - API call/event made
  
- Security Credential
  - types
    - IAM usernam and password
    - E-mail addresss and password
    - Access key
    - Key pair
    - Multifactor authentication
  - temp SC
    - short term: < a few hr
    - AES STS(Security Token Service)
      - SAML, IAM Role
  
- Users
  - created by IAM
    - create a user via IAM
    - provide SC
    - attach permissions, roles and responsibilities
    - Add the user to groups
  - root account
    - unrestricted access
    - for creating Admin
  - access key for each user
    - access key ID
    - secret access key(accessible only at the creating time)
  
- Groups
  - consists of roles and privileges
  - to users: many to many
  - contains users, no group. no role
  - no default group
- Roles
  - principle: 3rd-party users, AWS services
  - an IAM entity that defines a set of permissions for making AWS service requests
  - control federated users, not assoicated with a user/group
  - specify 2 policy for a role
    - trust policy: principal, who can assume the role
    - permission/access policy: resouces/action is allowed access to
  - you can assign an IAM Role to the users or groups from your Active Directory once it is integrated with your VPC via the AWS Directory Service AD Connector.
- Hierarchy
  - root user/account owner
  - IAM user
  - temp sc
  
- Best Practices
  - use IAM user: lock down root user
  - create strong password policy, expired after 90d
  - Rotate SC regularly: deactivate unused SC 
  - enable MFA: or critical account
  - manage permission with groups: job function
  - grant the least privileges
  - use IAM role: no share SC
  - use IAM role for EC2
  - use IAM policy conditions for Extra Security
  - use CloudTrail: enable in all regions, not publicly accessible for S3

 - AWS Compliance program
   - report from Artifact menu in Security, Identity & Compliance
 
 - Shared Responsibility Model
   - AWS: OF cloud
     - Physical security of data center
     - EC2 secrity
     - Network
     - configuration management
     - HA data center
     - disk management
     - storage device decommissioning
   - Customers: IN cloud
     - guest OS
     - app
     - firewall
     - VPC
     - service config
     - Authentication and account management


### Auto Scaling
- Benefit
  - Dynamic scaling
  - best user experience
  - Health check and fleet management. fleet: a collection of EC2
    - instance status check (VS ELB app/port health check)
  - Load balancing: with ELB
  - Target tracking: CPU utilization
- Launch Config
  - a template stores all the info about the instance
  - to Auto scaling group: one to many
  - can't edit ASG's lauch config, but can create a new one to assocaite, **new instance will follow**

- Auto Scaling Group
  - in one region per group
  - policy
    - maintaining instance level: num of instance
    - manual scaling
    - per the demand: CloudWatch
    - per schedule: time
  - scaling policy: one policy for up,one for down
  - prvide min and max num of instance
  - simple scaling
    - based on only one scaling adj
    - cooldown period for time before new
    - create alarm -> define action based on it
  - simple scaling with steps
    - on range
    - Exact capacity
    - change in capacity
    - percentage change in capacity: nearest digit
  - target-tracking scaling policy
    - metrix, CloudWatch
  - Termination policy
    - which EC2 to shut down first
    - longest running
    - billing
    - oldest launch config
    - Default termination policy
      - AZ has most non-scale-in-protected instance
      - oldest launch config
      - closest to next billing hour
      - random
- ELB
  - Auto multi-AZ as a seperate ELB VPC
  - if ELB instance failed, ELB cuts off the traffic to that instacne ans starts a new
  - types
    - Network LB
      - OSI layer4, connection based, supports TCP and SLL
      - **no touch packet**, no header modification(no X-Forwarded-For for IP address)
    - App LB
      - OSI layer7
      - check header, content-based routing
        - host- based routing
        - path-based routing
    - Classic LB
      - support both network and App
      - for classic EC2
      - always Internal LB
    - Define Schemar for it's internal or external
      - External LB, for Internet
  - concepts
    - multipe app:
      - if classic LB, use DNS in front of LB
      - ALB support content bsed rounting, up to ten rules(app)
    - ALB for multiple ports from a host
    - ECS: auto, dynamic port mapping
    - Listener:
      - per LB, at lest one listener to accept incoming traffic; <= 10
      - define on the protocol and port
      - support WebSocket
      - HTTP/2 <= 128 req in parallel, use healthy targets with round-robin routing
    - Target Group
      - regional
      - with Auto scaling
      - a single target can be registered with multiple groups
    - Rule
      - like listener and target
      - condition(host/path) and action(forward)
      - path pattern is case-sensitive
      - higest priority excuted first, default rule(created default for a listener) has the lowest
  - health check
    - HA, by CloudWatch
    - HealthCheckIntervalSeconds
    - can customize frequency, failure thresholds, and list of successful response codes
    - status: init, healthy, unhealthy, unused, drainiing
  - multi-AZ
    - better maintain the state info ourside EC2, use DynmaoDB for session
    - cross-zone LB
      - default enabled
      - evenly distribute to EC2 across AZ
      - NLB only rout to EC2 in its AZ, flow hash on IP

### APP

#### Lambda
- event-driven
- serverless
  - no infrastructrue to manage; Scalability; Built-in redundancy(HA); Pay only for usage(pay-for-usage model)
  - S3, DynamoDB, API gateway, Lambda, SNS, SQS, CloudWatch Events, Kinese
- event source:
  - SNS function
  - API Gateway event
  - S3, CloudWatch
- Lambda function is stateleess
- support Java, Node.js, Python, C#
- max execution duration per req is 5m
- soft limit of 1000 concurrent executions
- steps:
  - upload code in ZIP
  - scehdule, event
  - compute resouce
  - timeout
  - VPC details
  - launch
- When you create or update Lambda functions that use environment variables, AWS Lambda encrypts them using the AWS Key Management Service.
  - Although Lambda encrypts the environment variables in your function by default, the sensitive information would still be visible to other users who have access to the Lambda console. This is because Lambda uses a default KMS key to encrypt the variables, which is usually accessible by other users. 
- Lambda@Edge lets you run Lambda functions to customize the content that CloudFront delivers, executing the functions in AWS locations closer to the viewer. 
  - 
#### API Gateway
- ease to define, publish, deploy, maintain, monitor, and secure APIs at any scale
- Benefits:
  - Resiliency and preformance at any scale
  - caching: output
    - You can add caching to API calls by provisioning an Amazon API Gateway cache and specifying its size in gigabytes
  - secuirty: authorize
  - Metering: meters traffic
    - throttling and quota limits to protect backend
  - Monitoring: dashboard
  - lifecycle management: version
  - integration with other AWS products: serverless API with Lambda
  - Open API spec(Swagger) support
  - SDK generation for iOS, Android and JS: integration test with auto generated client SDK

#### Kinesis
- continuous stream of data
- app scenarios
  - real time analytics
  - real time app: app performace monitoring
- Kinesis Data Stream
  - enable to build custom app that process or analyze streaming data
  - real time
  - secure: VPC endpoint
  - easy to use: data stream producer is any app to put data into DC(data ingestion)
  - parallel processing: concurrently
  - elasticL dynamically adjust throughput
  - low cost
  - reliable: replicates data across 3 AZ in a region and preserve up to 7d
- Kinesis Data Firehose
  - to load streaming data into data stores and analytics tools
  - auto scale, can batch, compress,a nd encrypt the data before loading it
  - Benefits:
    - eay to use
    - integrated with AWS data stores
    - Serverless data transformation without having to build you own data-processing pipeline
    - Near real time
    - NO ongoing admin
    - pay only for what you use
- Kinesis Data Analytics
  - process and analyze real-time, steaming data
  - running SQL queries conitnuously on data
  - transformation
  - Benefits:
    - real-time processing
    - fully managed
    - auto elasticity
    - easy to use
    - SQL
    - pay for usage
  - Use cases:
    - Time-series analyics
    - Feed real-time Dashboards
    - create Real-time Alarm and Notification
#### Reference Arch for using Serverless 
- Lambda, API gateway, Kinesis
- real-time file processing
- real-time stream processing
- ETL processing
- IoT bank ends
#### CloudFront
- CDN, global content delivery newowrk
- served by the closest edge
- no data transfer charge for tranf between region and edge
- use cases:
  - caching static asset
  - accelaerating dynamic contnent
  - helping protect against DDoS attachs: with AWS shield and WAF
  - Improving security
  - Accelerating API calls
  - distributing software
  - streaming video: 4K
- Concepts
  - Edge loc: major cities
  - Regional edge loc: cache longer at the nearest, enabled by default
  - Distibution: your files' location
  - origin: server, public DNS
  - behaviors
    - Path pattern matching
    - headers
    - query strings/cookies
    - Signed URL or Signed cookies: valid for a limited period of time
    - protocol policy: HTTP, HTTPS
    - TTL(time to live): in sec
    - Gzip compression
- Geo Restriction
  - whitelist
  - blacklist
- Error handling
  - custom error page for 4xx or 5xx
  - can set min TTL for caching errors
- create an origin access identity(OAI) for CloudFront and grant access to objects in your non-public S# to that OAI
#### Route 53
- DNS(Domain Name Service)
- 100% SLA(uptime), across region
- suport record types
  - A(address record)
  - AAAA(IPv6)
  - CNAME(canonical)
- use Alias record for zone apex
- health check
- routing policy
  - Weighted round robin: A/B testing
  - Latency-based: resource with best latency
  - Failover: one resource takes all traffic
  - Geo DNS: based on Users'location, ustomize localized content
#### AWS shield
- AWS Shield Advanced also gives you 24x7 access to the AWS DDoS Response Team (DRT) and protection against DDoS related spikes in your Amazon Elastic Compute Cloud (EC2), Elastic Load Balancing(ELB), Amazon CloudFront, and Amazon Route 53 charges
#### Web app Firewall
- use cases
  - Vulnerability protection
  - Malicious requests: overload crawler; scraper(extracing large amount)
  - DDos mitigation(HTTP/HTTPS flood)
- intg with CloudFront
- WAF filter conditions
  - corss-site scripting
  - IP
  - Geo
  - Request size constraint
  - SLQ Injection
  - String match
  - Regex match
- action: allow, block, count
- rules:
  - regular rule
  - rate-based rule(a rate limit in 5m interval)
  - trigger action designated in web ACL
- WAF resources can be managed with API, propaged globally in 1m
- 1 minute metrixs are availabe in CloudWatch

#### Amazon MQ
- standard API
- If you're using messaging with existing applications and want to move your messaging service to the cloud quickly and easily, it is recommended that you consider Amazon MQ. 

#### SQS
- CAP
- mesage queue: a form of asynchronous service to service communication
- MQ provides a buffer to temp store message and endpoints
- redundant across AZ in each region
- messages in the SQS queue will continue to exist even after the EC2 instance has processed it, until you delete that message. 
- message retained up to 14d, max size 256k
- types:
  - standard
    - default, unlimited transactions per sec
    - at least onece message delivery
    - generally in insertion order
  - FIFO
    - exactly once processing
    - limited to 3000 tranx per sec
- visiblity timeout: 
  - stay in queue while processed, no return
  - 0 sec - 12h
- Message retention periodL 1m - 14d
- max message size: 1k - 256K
- Delivery delay: reamin invisible to consumers for this duration
  - standard: not retroactive
  - FIFO: retroactive, affect already in queue messages
- receive message wait time
  - return
  - short polling: 0s
  - long polling: 1 - 20s
- content-based deduplication
  - SHA-256 hash to gen deduplication ID
- dead-letter queue: can't processed successfully, Max Receive: 1 -100
#### SNS
- send notification
- pub-sub messaging
- topic: communication channel, owner set policy for limiting sub/sub, protocol
- publisher: to send msg to topic
- subscriber tp receive message
- features:
  - across AZ
  - support transport protocols: HTTP(S), email, SMS, Lambda, SQS
  - push-based
  - monitioring: CloudWatch
  - message contain up to 256K, or split as multiple msg to fit(signle 1600b); SMS(140b)
#### SWF
- Simple WorkFlow
- coordinate the components using visual workslow
- AWS Step function: replacing SWF
  - state as JSON blob
  - multistep app
  - state types:
    - task: call your services
    - choice: branching logic
    - Parellel: fork-join
    - wait
    - fail
    - succeed
    - pass: pass input to output

#### Elastic Beanstalk
- simplest and fastest way to deploy web app
- 3 key components
  - environment
    - standard web servers
    - workers with a background processing task listening SQS
  - app version
  - saved config(on ev and resource)
    - single instance
    - LB with Auto scalling
    - never manul config EC2
#### OpsWorks
- auto operational work, infrastructure
- no charge
- Chef Automate
- AWS OpsWorks for Puppet Enterprise
- AWS OpsWorks Stack


#### Cognito
- a user identity and data sync service
- key-value pairs
- control access to AWS resources
#### EMR
- Elastic MapReduce
- load data to S3, lauch EMR cluster
- in Hadoop, data remains on the servers that process the data
- can access OS of these EC2
- 3 types of nodes
  - Master node: coordinating
  - core node: running
  - taks node: optional, doesn't store data(can use spot instance)
#### CloudFormation
- model insfra architecture with version control
- template: JSON/YAML
- stack: a collection of AWS resources
- update: a change set to summarize proposed change
#### CloudWatch
- metrics, custom metrix
- events: any changes made to your AWS resource
- log agent, send your log to it and minitor in real time
- alarm, watch a single metrix
  - state must have changed and been maintained for a specified period of time
  - OK, ALARM, INSUFFICIENT_DATA
- dashboard, view graph and stat
- CloudWatch does not automatically provide memory and disk utilization metrics of your instances. => custom metrix
- You can also install CloudWatch Agent to collect more system-level metrics from Amazon EC2 instances
- CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance, and RDS Enhanced Monitoring gathers its metrics from an agent on the instance.

#### CloudTrail
- logs all API calls, including console activities and command line instructions
- diff accounts can send yheir trails to a central account
- can enable in al region
- by default, log sent to S3 is encrypted
#### AWS Config
- record cinfig changes
- config rule: represents desired config for a resource to assess your overall compliance and risk status
- continuous monitoring
- continous assessment
- changes management
- operational troubleshooting
- compliance monitoring

#### VPC Flow Log
- info on IP traffic
- use CloudWatch log, alarm
- levels
  - VPC
  - Subnet
  - Network interface
- security monitoring , app troubleshooting
#### Trusted Advisor
- 5 categories
  - cost optimization
  - security
  - fault tolerance
  - performance
  - service limits
- status
  - red: action
  - yellow: investigation
  - green
#### AWS Organization
- AWS account management
- SCP(servuce control policy): centrally control the use fo AWS services down to the API level across multiple accounts
- single payment method for all accounts


## DB
### RDS
- hosting and managing relational database
- benetifs:
  - no infra management
  - instant provisioning
  - scaling
  - cost effective
  - app compatibility
  - HA: easy multi-AZ 
  - security
- IAM database authentication provides the following benefits:
  - encrypted Network traffic using Secure Sockets Layer (SSL).
  - to centrally manage access to your database resources, instead of managing access individually on each DB instance.
  - For applications running on Amazon EC2, you can use profile credentials specific to your EC2 instance to access your database instead of a password, for greater security
- limits:
  - no access to host OS
  - storagee limit: 64 for Aurora, 16T for others
- Multi-AZ deployment(2 choice: single vs multi)
  - you choose AZ for primary DB, RDS then choose a standby instance and storage in anohter AZ, all same config
  - active/passive DB
  - RDS auto does the DNS failover
  - cross-region replica
  - no standby for Aurora, as it has sync read replica
- Scaling
  - chaneg instance type
    - steps:
      - moify 
      - choose new class
      - apply immediately or in preferred maintainance window
    - not integrated with Auto scaling
  - Read replica
    - read-only copy
    - up to 15 rep
    - offload read only traffic
    - can promoted to a master DB
    - async, while master-standby is sync
    - cross regional read replica
    - not support Oracle or SQL server

- Security
  - VPC
    - private subnet, firewall
    - SG
  - Data Encryption
    - when encrypt RDS with AWS-provided encryption
      - instance storage(EBS)
      - auto backup
      - read replica
      - standby
      - snapshot
    - a default key created from KMS, and tie to your account
    - or crate your own master key
  - points;
    - can encrypt only during data base creation
    - once enctypt, can't remove
    - when create a read replica, bot master and read replica must be encrypted; so for master-standby @@@with 1
    - can't copy snapshot of encrypted DB, as KMS is regional
    - can migrate an encrypted from MySQL to Aurora MySQl
    
 - backup
   - Aurora: conitnuously back up to S3
   - others: one backup per day
   - multiple copies of backup in eac AZ where instance deployed
   - store to any class of server
   - custom restore time
 - Snapshot
   - manully 
   - temporaray I/O suspension
   - in S3
 - Monitoring
   - standard monitoring: 1m
   - enhanced monitoring: 50 metrix: 1s interval
   - event notification: SNS
   - performace insgihts: on by default for Auroara PostgreSQL
   
#### Aurora
- MySQL and PostgreSQL compatible
- auto repliacted across 6 storage nodes in 3 AZ, no cost, sync
- use endpoint to connect cluster
- no standby DB
- uses a quorum system for reads and writes to ensure that you data is available in multiple storage data
#### Redshift
- data warehouse, read-oriented
- OLAP(online analytical processing)
- ETL
- Benefits:
  - Fast: columnar, parelleized
  - cheap
  - good comparession
  - managed service
  - scalable
  - secure
  - zone map functionality: track block
- Arch
  - built on Postgres
  - signle node: one node 
  - multi node: a leader and some compute
  - cluster types: uses EC2; dense compute(DC)/dense storage(DS)
  - leader node
    - SQL endpoint
    - one leader per cluster
    - DB function, metadata, ODBC/JDBC, encryt, comnpress
    - plan execution and aggregate result
  - compute node
    - can communicate with each other, and S3
    - in own VPC, interconnected network, you can't access
    - further divided into slices(a slice is allocated aportion of a node's CPU, memory and storage)
- In Amazon Redshift, you use workload management (WLM) to define the number of query queues that are available, and how queries are routed to those queues for processing. WLM is part of parameter group configuration. A cluster uses the WLM configuration that is specified in its associated parameter group.
- sizeing
  - compression
  - data mirroring is already included
- VPC
  - vps is manndatory for new 
  - can choose a cluster subnet group(contains some subnets)
  - enhanced VPC routing: enable to traffic through you VPC, otherwise through Internet
- Encryption
  - can encrypt all data
  - all cluseter communication are secured by default
- Security
  - db user
  - master username
- Back up
  - auto backup
  - snapshot incremental
  - can turen off autp backup
  - can table level restore
- data loading
  - file-based loading
    - file from S3
    - Kinesis Firehose
    - database
    - Redshift-specific tools to compute code from Dynamo DB, EMR via SSH cmd
  - COPY
    - can use multiple input files as each slice loads one file at a time
    - 
  - UNLOAD: export
    - can write to S3 only
    - can generate more than 1 file per slice for all compute node
  - VACUUM
    - reclaim space after deletion
    - after COPY
  - ANALYZE
    - update stattistics for execution paln optimizer
    - after made nontrivial number of changes to data
- Data Distribution
  - ALL
    - distribute a copy of entire table to first slice(slice0) on eac node
    - help join
    - slower
  - KEY
    - hash of the defined column
    - same key to same location
  - EVEN
    - round robin
    - small dim table
    - withput JOIN, GROUP BY, agg query
#### DynamoDB
- NoSQL
  - ease of dev, scalable performace, HA, and resilience
- support both document and key-value
- specify request capacity per table
- use cases:
  - cookie, session, uer preference
  - log, sensor data
- benefits
  - scalable: auto up/down
  - managed service
  - fast, consistent performace
  - fine-grained access control: IAM
  - cost effective
  - Integration with other AWS services: Lambda trigger for data change
- Term
  - item: table
    - composed of a primary key and key-value pairs(attributs)
    - must have a PK
    - can't exceed 400K
    - object serialization and messaging
  - Manipulation of data programmatically using API
  - scalar data types: number, string, binary and boolean => PK
  - NULL
  - PK:
    - parittion key: hash on it; must be scalar
    - partition key and sort key: composite; sort key as range attribute(store physically close)
  - Capacity
    - Unit of cap req for Write: num of item writes per sec X item size per 1K block
    - Unit for Read: read X 4K (can twice if use eventually consistent reads)
  - Global Secondary index
    - local secondary index: same parition key, local to parition
    - global secondary index
  - Consistency Model
    - stores 3 geo replica 
    - Eventualy consistent read: default
    - Strongly consistent reads: reflect for all successful responsed writes
  - Global table
    - across AWS regions
- Stream:
  - updates and receive item-level data before and after items are changed
  - auto notify status change, Lambda, Redshift sync, ElasticSearch
- Accelerator
  - DAX: tem times improvement >> ElasticCache
  - in-memory cache
- Encryption and secrity
  - encryption at rest
  - VPC endpoint
- tips:
  - DAX: accelerator
  - stream with Lambda, SNS, notify status update
#### ElastiCache
- in-memory cache
- scaling with sharding
- engine
  - Memcached: meultithreaded
  - Redis: replication, stateful
    - Using Redis AUTH command can improve data security by requiring the user to enter a password before they are granted permission to execute Redis commands on a password-protected Redis server.
- does not directly communicate with your DB tier, with APP
- choose AZ(Preferred Zone) the cluster lives in

#### tips
  - AWS Budgets gives you the ability to set custom budgets that alert you when your costs or usage exceed (or are forecasted to exceed) your budgeted amount.
  - 

## Well-Architected Framework
- is docu and arch
- benefits:
  - Build and depoly faster: reducing firefighting
  - Lower or mitigate risks: understand risk
  - Make infromed decision: highlight impact on busisness
  - Implement AWS best practices
- goal: secure, efficient, scalable, reliable and cost effective
### Operation
- understands bussiness's goal, priorites and metrics
- design principles:
    - Preform operation as code: script
    - Document everything: annotate
    - Push small changes instead of big
    - Refine operating procedure often
    - Anticipate failure: assume failres
    - Learn from the operational failures: not happen twice
- areas:
  - Prepare: baseline
  - Operate: 
    - success is measured by outcome and metrics
    - dashboard
      - CloudWatch log
      - ES: Elasticsearch
      - Personal Health dashboard
      - Service Health dashboard
    - automate as much as possible
  - Evolve
    - start with saml and continuously keep on adding
### Security
- Strong Identity Foundation
  - IAM manage acount: least privilege, central team to manage
  - IAM user/federate users
  - employee life cycle, strong password policy
  - Security Token Service: temporay credential
- Enable Traceability
  - log everythime, real time
  - auto action for respond
  - AWS Config: auto check
  - CloudTrail
- Implement at all layers
  - NACL: filter the traffic at subnet level
  - isolate every component
  - seperate firewall
- Secure data
  - at rest / in transit
  - tier to tier
  - data flow path
- Automate
  - alert
  - auto triggered response
  - log
- plan fo events
  - simulation
#### Best Pratice
- Use Identity and Access Management
- Use Detective Control: identify a threat or incident
  - AWS Config
  - AWS Config rule: overal complicance and risk status
  - AWS CloudTrail
  - CloudWatch
  - VPC flow logs to help with networking monitoring
  - Amazon Inspector
- Use Infra protection
  - AWS Ssytem manager: visibilty and control of your infra
- Use Data protection
  - classify data sensitivity
- Use Incident Response
  - Wb App Firewall
  
### Performace
- speed
- principles
  - Go global in a few click
  - Leverage new tech sch as serverless
  - Consume advanced tech: managed services
  - Leverage multiple tech
  - Experiment more often
- Efficiency
  - selection
    - by workload
    - compute: instance, container, function
    - storage
    - network
    - database
  - Review
  - Monitoring
### Reliability
- SLA(service level agreement)
- failure scenarios and work backward
- principles
  - Test recovery procedures
  - Automate recovery from failure
  - Scale horizontally
  - Stop guessing capacity
  - Automate changes to the system
- Best Practices
  - Lay the Foundation: HA
    - Fault isolation zones: > 1 AZ
    - Redundant components
    - Leveraging managed services
  - Implement Changes Management
    - Blue-green deployment: two stack for old and new version, slowly increaing traffic
    - Canary deployment: A/B
    - Feature toggle: can turn off feature
  - Implement Failure Management: DR(Disaster Recovery) plan
### Cost optimization
- principles
  - choose the best comsumption module: on-demand, pay-as-you-go
  - use managed services
  - measure the overal efficiency
  - analyze the expenditure
  - stop sending on a data ceneter
- Finding Cost-Effectve Resources
  - start with minimum resource
  - Trusted Advisor
- Matching Supply with Demand
- Being aware of expenditures
- optimizing over time: gap analysis

### AWS Best Practices
- deploy to cloud
  - life and shift
  - cloud optimized
  - cloud native architecture
- Design for Failure
  - all layers
  - assume everything fails and design backward
```
•Use multiple availability zones

•Use elastic load balancing

•Use elastic IP addresses

•Do real-time monitoring with CloudWatch

•Use Simple Notification Service (SNS) for real-time alarms based on
CloudWatch metrics

•Create database slaves across availability zones
```
- Build Secuirty in Every Layer
- Leverage Multiple Storage Options
```
Amazon S3: Large objects

Amazon Glacier: Archive data

Amazon CloudFront: Content distribution

Amazon DynamoDB: Simple nonrelational data

Amazon EC2 Ephemeral Storage: Transient data

Amazon EBS: Persistent block storage with snapshots

Amazon RDS: Automated, managed MySQL, PostgreSQL, Oracle, Maria DB, SQL Server

Amazon Aurora: Cloud-optimized flavors of MySQL and PostgreSQL

Amazon Redshift: Data warehouse workloads

```
- Implement Elasticity
```
Auto Scaling: Use Auto Scaling to automatically scale your infrastructure by adding or removing EC2 instances to your environment.

Elastic load balancing: Use ELB to distribute the load across EC2 instances in multiple availability zones.

DynamoDB: Use DynamoDB to maintain the user state information
```
- Think Parellel
  - use multithreading and concurrent requests to the cloud service: a thread waiting for a response from a cloud service is not consuming CPU cycles
  - Run parallel MapReduce jobs
  - Use elastic load balancing to distributed load
  - Use Amzon Kinesis for concurrent processing of data

- Loosely couple your Architecutr
- There are No Constreaintes in the AWS cloud
  - rip-and replace for hardware


















