
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
  - S3 Ine Zone IA: rapid access
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
- can create point- in-time consistent snapshot to S3
- AFR(annual failure rate): 0.1 -0.2 perc
- Volume
  - IOPS: input/output operation per second 
  - EC2 instance store: local to EC2, no snapshot support
  - EBS SSD backed: transactional workload like DB, latency-sensitive
  - EBS HDD-backed: throughput-intensive workload like log processing/mapreduce
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

- Elastic Network Interface(ENI)
  - create one or more network interfaces and attach them to you instance
  - attibutes of the ENI follow along with it
  - can't change deggault NI
  - ENI doesn't impact the network bandwidth
- Elastic IP address
  - static IP address
  - EIP is associated with the instacne during the stop and start of EC2, will detached when explicitly terminate EC2.
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
  - Cost effective

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

- A placement group is a logic grouping of instances within a single AZ
  - low latency or high network throughpt
  - better same instance type, enhanced neworking type
  - can't span AZ
  - unique name per AWS account
  
- Storage
  - EBS, persists independently for the life span of EC2 
    - General purpose: default
    - Provisioned IOPS
    - Magnetic: dev
  - instance store: ephemeral storage
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
  - using a single AMI, lauch diff instance types
  - lauch permission: pulic, explicit, implicit
  - Obtaining
  - Virtualization 
    - HVM(hardware WM)
    - PV(Paravirtual)
- Instance Root Volume  @@@
  - instance store-backed AMI
    - S3
    - can't stop
    - reboot: data persists
    - terminated/shut down: data gone
    - when use an instance with instance store, you can't detach an store vlolume form one instacne and reattach to another
  - EBS-backed AMI
    - data laways persis
  
  - can convet Linux AMI from store-backed to EBS backed; but no fro windows
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
    - Protocol
    - Port range
    - ICMP type and code
    - Source/destination
      - IP address
      - SG: rule will be stale if the SG is in a peer VOC and the SG or VPC peering are deleted
- ECS  
  - Elastic Container Service
  - manage Docker containers on a cluster of EC2
    - containers isolate the process running on a single OS
    - Container: isolated user space processes
    - image: portable, consistent and immutable; includes dependencies
  - AWS ECS API for cluster management infrastructure

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
  - an IAM entity that defines a set o permissions for making AWS service requests
  - control federated users, not assoicated with a user/group
  - specify 2 policy for a role
    - trust policy: principal, who can assume the role
    - permission/access policy: resouces/action is allowed access to
- Hierarchy
  - root user/account owner
  - IAM user
  - temp sc
  
- Best Practices
  - use IAM user: lock down root user
  - create strong password policy, expired after 90d
  - Rotate SC regularly: deactivate unused SC 
  - enable MFA: or critical account
  - manage permission with groups: job fucntion
  - grant the least privileges
  - use IAM role: no share SC
  - use IAM role for EC2
  - use IAM policy conditions for Extra Security
  - use CloudTrail: enale in all regions, not publicly accessible for S3

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
  - Load balancing: with ELB
  - Target tracking: CPU utilization
- Launch Config
  - a template stores all the info about the instance
  -  to Auto scaling group: one to many
  - can't edit ASG's lauch config, but can create a new one to assocaite, new instance will follow

- Auto Scaling Group
  - in one region per group
  - policy
    - maintaining instance level: num of instance
    - manual scaling
    - per the demand: CloudWatch
    - per schedule: time
  - scaling policy: one for up,one for down
  - prvide min and max num of instance
  - simple scaling
    - based on only one scaling adj
    - cooldown period for time before new
    - create alarm -> define action based on it
  - simple scaling with steps
    - on range
    - Exact capacity
    - chacne in capacity
    - percentage change in capacity: nearest digit
  - target-tracking scaling policy
    - metrix, CloudWatch
  - Termination policy
    - which EC2 to shut down first
    - longest running
    - billing
    - oldest launch config
- ELB
  - Auto multi-AZ as a seperate ELB VPC
  - types
  - concepts
  - health check
  - multi-AZ














