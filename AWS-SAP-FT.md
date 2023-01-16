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




