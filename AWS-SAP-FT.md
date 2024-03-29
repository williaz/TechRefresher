### EC2
- instance
- AMI: template
  -  regional
  -  permission
  -  volume mapping: EBS, instance store(ephemeral)
- OS: root
- user data(<16kb script) and instance metadata are not encrypted
- Instance type: CPU, Memory, Storage, networking
  - Genral Purpose: M, T
  - Compute Optimized: C
  - Memory Optimized: R, X
  - Accelerated Computing(GPU, data pattern): P, G, F, VT, DL
  - Storage Optimized(IO): I, D, H
- Non-root volumes can be encrypted
- System Manager(session mgr): 'AmazonEC2RoleforSSM'

- Pricing Models
  - On demand
  - Spot
  - Reserved
  - Dedicated hosts
  - Dedicated instances

- Regional Data Transfer rates apply when 1. diff AZ; 2. public/Elastic IP
- IP
  - public
  - private: retain when stop
  - Elastic: static

- ENI(Network Interface): netowrk card, 1 AZ, eth0 create with EC2 lauched
- ENA(Adapter): bandwidth, PPS(packet), 1 VPC
  - HWM only EC2
- EFA(Fabric Adapter): OS bypass when access NI; 
  - HPC, ML using NVIDIA comm; 
  - no additional cost
- placement group
  - cluster: 1 AZ, network traffic between instances
  - spread: span AZ, 7 instances per AZ
  - partition: logical segments, diff rack; dedicated hosts dont's support
- IAM: universal, any region; only one IAM can be attached to an EC2 at a time
- EC2 as Bastion hosts/jump box to access VOC drom SSH/RDP
- Satus check(1m) is built-in, can't be disabled but can remove alarms
- prefer EC2 to reboot instead of OS
- CloudWatch monitoring: std - 5m; detailed - 1m

- CloudTrail: EC2/EBS; audit, 90d retent without configuring
- Tags:  most resources up to 50 tags
- auto scaling + ELB: auto recovery
- Route 53 health check: self-healing redirection
- Migration:
  - VM import export: only API, CLI
  - SMS(Server Migration Service)

### EBS
- by default, root volumne will be deleted using EBS
- instance store: low latency
- Volumne types
  - SSD, gp(general purpose): up to 16,000 IOPS
    - dev.test; low-latency
  - SSD, provisioned IOPS (io): > 16,000 IOPS
    - worloads; DB 
    - multi-attach
  - HDD, Throughput Optimized (st)
    - big date: datalake, Kafka, ETL
    - can't boot volume
  - HDD, cold(sc): lowest cost, can't boot vol


- same IOPS for encrypted volume
- encryption key(CMK in KMS)
- Update volume type/size/perf at run time
- RAID: increase IOPS, no boot/root
- VolumeR/WBytes: CloudWatch EBS
- DiskR/WBytes: CW - instance store  

### ELB
- 1 region
- target group
  - only 1 protocol and 1 port
  - only assciate to 1 LB

#### ALB
- host(dp,aom)/path based routing: other HTTP things
- >= 1 listner: rules per target group


#### NLB
- to IP-based across diff region

### Auto Scaling
- region specific
- options
  - Maintain: certain/min num
  - Manual: max/min 
  - Scheduled
  - Dynamic: real-time metrics
    - policy: Target tracking, simple/step scaling
  - Predictive: ML
- by default, use EC2 status check
- IAM role, service-linked role; no resource-based policies
- scale-in/out
- standby stuats: manual move; charged, not available

### ECS
- EKS: ECS for Kubernetes
- Launch types
  - EC2
  - Fargate: serverless, auto provision infra; only ECR/Docker hub
- Task: running continer
- Container Instance: EC2 runing the ECS agent
- task definition
  - JSON
  - describe >= 1 container <=10 

- Service/Custom scheduler to schedule ECS

### Lambdda
- can connect 1 VPC, slow down
- event driven
- VPC subnet ID, SG ID
- default timeout: 3 sec, max 15m
- event source mapping
  - on lambda side: SQS, DynamoDB, kinesis
  - S3, SNS side: async
- aias traffic shifting
  - canary deployment
  - version weight

- Lambda at Edge: custom content that CloudFront delivers
- AWSLambdaVPCAccessExecutionRole: createNI, DescribeNI, DeleteNI

- SAM(Serverless App Model)
- ALB's target
  - same region with Target Group
  - max 1MB in/out
  - no WebSocket
  - by default, disabled target's health check

### Beanstalk
- deployment infra
- PaaS
- 1 region
- compiance: ISO< PCI, SOC1-3
- if RDS in beanstalk, terminate beanstalk, lost RDS

### S3
- in a region, bucket name is globally unqiue as ARN no region/namespace
- max file: 5TB; single PUT: 5GB
- multipart upload: >100MB
- Notification: SQS, SNS, Lambda
- read after write consistency for create
- eventual consisitenyc for update or delete
- can requester pays
- no nested buckets, but prefix
- can't rename
- CORS config on bucket
- obj
  - uploader own
  - bucket owner can deny access, archive, pay charges

- charge:
  - into S3 is free
  - free data transfer between EC2 and S3 in the same region 

- Transfer Acceleration
  - leverage CloudFront's Edege

- static web
  - domain name have to be same with bucket name
  - auto scales
  - not support HTTPS
  - only GET, HEAD
- CRR(Cross Region replication)
  - auto replicates
  - becket level
  - no replicate obj with SS-c and SSE-KMS
- SRR(same region)


### Glacier

- can't specify Glacier as storage class when create obj
- upload: sync; download is async
- add 32-40kb meta to each obj -> zip small objs before
- GET btye range

### EFS
- region
- mount from on-prem using AWS Direct Connect or VPN
- encryption at rest enable at creation time

### Storage Gateway
- hybrid storage: cache freq data on-prem
- backed by S3 and Glacier
- diff interface: File, Volume, Tape

### FSX
- fully managed 3rd party file systems
- Windows File server: SMB, NTFS, AD
- auto encryption
- Lustre: ML< HPC, video processing, finanacial modeling, EDA

### VPC
- region
- logically isolated DC
- 1 IGW per vpc
- default VPC auto created, has all-public subnets(auto-assing public IPv4, IGW)
- maps of AZ names are diff for diff users
- subnet
  - in 1 AZ
  - IP range
- router: routing between AZs
  - subnet's route table 
  - main route table: auto create time, can't delete
  - routing between subnets always possible with a default rule which can't be deleted or changed


