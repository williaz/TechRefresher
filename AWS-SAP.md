
### Organization
- Organization (accounts, group into OU)<= SSO

- SCP(Service control policies): control the max available permissions
  - Deny list VS Allow list
- Tag policy

- Control Tower
  - landing zone: multi-account baseline
    - preventive Guardrails: SCPs
    - detective Guardrails

### IAM
- Principal: User, Role, Federated User, App
- User, Group, Role, Policy

- STS(Secuirty Token Service)
- IAM role
  - Trust policy: who can assume the role
    - cross account access
    - resource based
  - Permission policy: identity-based

- MFA
  - something you know: password
  - have: MFA device, authenticator
  - are: fingerprint

- inline policy: 1:1 with user/group/role
- managed policy: by AWS/customer
- resource-based policy: principle
- Access control
  - RBAC: role based
  - ABAC: Attribute/Tag(K:V)
    - Condition: tag
- IAM Permission Boundary: on Users/Roles
  - Preventing privilege escalation: cant bypass access by creating new users

- Effective permissions
  - union of ID based and reousrce based
  - interect: SCP, perm boundary

- Evaluation logic: Deny -> Org SCP -> Resource based -> IAM perm boundary -> session -> ID based

### AD
- Id providers
- 
### Id Federation
- IAM: LDAP(ID store) -> IdP <-> user -> STS
- SSO
- Cognito: web/mobile




### EC2

#### pricing VS use cases
- on-demand: no discount
- reserved: 1/3yr; predictable workloads; 75%
  - std RI: change AZ
  - convertible RI: OS, type
- scheduled reserved: @@ 
- spot: bid; 90% discount; terminated at any time
  - spot block: time
- Dedicated instance: physical isolation at hardware level
- Dedicated hosts: server; socket/core visibilty
- saving paln: usage amount; can be combination

#### AMI

#### Placement group

- cluster: 1 AZ
- parition, spread: multi-AZ

#### NI
- private/public subnet
- ENI
- ENA: newtork perf 
- EFA: ML

#### EIP
- across AZ
- static public IP
- public IP: lost when stop, keep if reboot
#### NAT

- EC2 only know private IP
- Internet gateway perform NAT

#### Auto scaling
- tracking: SQS with metric data
- step scaling: diff range


### ELB

#### NLB

- EIP
- can single encrupted connection

### AWS Batch
- job def, queue
- EC2 or farget

### LightSail
- simple cloud server





### EBS
- replics within AZ


### EFS
- region
- NFS protocol, only Linux
- NAS: network attached server
- mount IP for EFS in diff VPC peering

### S3
- Replication need enable versioning
- S3 Gateway endpoint, connect private IP
- Storage classes: 11-9 duratbility
  - std
  - std-IA: per GB retrieved
  - intelligent: > 30d duration
  - one Zone IA
  - Glacier: 90d; min/hours latency
  - Glacier Deep archv 180d; hours ltncy
- lifecycle
  - Transition
- encryption
  - SSE-S: S3
  - SSE-KMS
  - SSE-C
  - client encry/decry

- presigned URLs: limted time access











