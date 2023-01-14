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

- Billing
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




