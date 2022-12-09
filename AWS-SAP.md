
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













