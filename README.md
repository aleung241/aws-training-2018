# AWS Training 2018

## Module 1 - Core AWS Knowledge

### Scopes
|Global|Regional|AZ|
|:---:|:---:|:---:|
|IAM|S3|EC2
|Route53|VPC|ELastic Block Store (EBS)
| |Internet Gateway|Subnet
| |SQS| |
| |DynamoDB| |

#### Regions
- Data is regionally scoped - a region is a boundary for data
- Edge locations for globally scoped services
- AZ scoped services require you to make highly available - single points of failure
- Two or more AZ's
- 18 regions worldwide
- YOU enable and control data replication across regions
- Communication between regions uses AWS backbone connections but not guaranteed


*Multi region use cases: Netflix*

#### AWS Datacenters
- Smaller data centers. But lots of them
- Houses several thousand servers
- All online. None are cold
- Designed for AZ failure

#### Availability Zones
- Made of one or more data centers
- Designed for fault isolation
- Interconnected with other AZ's using high-speed private links
- AWS recommends replicating AZ's for resilience

---

### Unmanaged vs Managed Services
#### Unmanaged

a.k.a. Self-managed  
- Scaling, fault tolderance, and availability are managed by YOU. AWS does not do this for you

*e.g. EC2*

#### Managed
Typically built into the service. These are fully managed by AWS

e.g. RDS

---

#### Security Responsibilities
-	Physical security, control, need-based access
-	Data centre locations are usually hidden
-	Infrastructure, knowing when it will fail 
-	Network infrastructure, intrusion detection. Can’t test own security or will be deemed as an attack.
-	Virtualisation infrastructure, hardware is dedicated to your instance 
-	Patch and maintain your OWN operating systems, AWS does not provide the service 
-	Think about user positions and role base access 
-	Security groups 
-	Intrusion protection 
-	Separation of access 



## Module 2 - Core Services
### Networking
#### Virtual Private Cloud (VPC)
- This is like a container
- Regionally scoped
- VPC is not a data center replacement
- VPC is a singularly logical isolated network
- 1 VPC can only have 1 Internet Gateway

#### Subnet
- Can be used to limit access to/from VPCs
- Each subnet must have its own CIDR block. They cannot overlap
- Only one route table associated per subnet
- Public subnets can send outbound traffic directly to internet
- Private subnets must do so through a NAT gateway sitting in the public subnet

#### Elastic IP
- Public IP that is statically assigned
- Associated with your AWS account
- Can mask failure of an instance by remapping the address to another instance

#### Security Groups
- App level protection
- No blacklisting option available. Default behaviour is to block already
- You can open ports on other security groups in case IP changes
|
#### NACL
- Subnet level protection
- Explicit blacklisting option available

#### NAT
- Like a bastian host sitting in public subnet
- Allows private instances outgoing connectivity to internet, while blocking inbound traffic from internet
- Indirect internet access
- Useful for hiding machines

---

#### EC2
- Virtual machines
- Placement groups: logical goruping of machines
- Instance storage is ephemeral. Shut downs clear it. Reboots keep it. 
- A shutdown then startup will spin up a new instance, clearing instance storage  
- Hardware is isolated to your instance except the T2 instance type. T2 is shared hardware  

Low/Moderate/High - Placement group - logical grouping of machines

Spot instances - choose highest price to continue using the machine. Imagine an auction?

**Careful! Giving full access to EC2 allows access to VPCs**

#### Amazon Machine Image

Used to create EC2 instances

---

#### Tags
- Sort, search, filter
- Recommended not to use tag "name". It's completely useless since everything already has a name
- Use eg. role, owner, environment, startTime, finishTime
- Basically metadata

---

### Storage
#### S3 (Simple Storage Service)
- Scalable, reliable, fast, durable
- Not a file system
- Object based storage
- 5TB per object
- Global Unique. No need for arn to specify region

One-zone infrequent access
- Less availability
- Used for backups
- Really new storage type offer

#### Elastic File System (EFS)
- Shared storage
- Expose and mount

#### Elastic Block Store (EBS)
- Snapshots stored in S3, regionally scoped due to S3 duplication

---

#### IAM
**Delete root access keys right after sign up!**
- Identity user management
- Access keys for APIs
- Can create temporary credentials to a user to assume a role
- Groups!
- Policies can be attached to groups, users or roles

---

### Databases
#### RDS
- Managed service
- Relational DBs
- Do not use for massive read/write rates
- Bad throughput rate

#### DynamoDB
- SQL DB service
- Used for scale and performance
- No relationships



## Module 3 - Designing your environment
#### How to choose region?
- Cost
- Ping it from your current location! [Cloudping](cloudping.info)  
- 5 VPCs per region default
- VPCs are regional

#### How many AZs to use?
- There are 3 in Sydney
- Don't need to use all of them
- Best practice - start with 2 AZs

#### Directing traffic
- Every subnet can have a route table, not multiple route tables per subnet
- Use custom route tables!
- Remember that server restarts result in IP address changes

#### Security Groups
- Assign addresses and control traffic. Can assign rules to group
- Can allow traffic from certain load balancers
- Allow complete control of packet routing through different tiers

#### Network Access Control Lists (ACL)
- Subnet level protection
- Contol in and out traffic
- Optional firewalls

#### Logging VPC traffic
- Uhh...can be done. Yeah...

#### VPC peering
- Route traffic between peered VPCs
- Peering must be direct. Cannot flow through other peers
- Scalable
- Needs permissions to both VPCs to make and accept requests to pass packets between them
- Use route tables to peer between VPCs
- Complete routing control



## Module 4 - Making your environment available
#### High availability
- Any component can suffer loss
- Ensuring downtime is minimised without human intervention
- More hardware required for more availability

#### Avoid single points of failure
- Consider EVERY component
- HAVE A SECONDARY BACKUP!

#### Amazon CloudWatch
- Monitors your instances
- Polls every minute (can be adjusted)
- Understand when things are nearing capacity
- YOU must specify what to monitor
- Understand where the bottlenecks are
- CloudWatch Alarms measure a SINGLE metric and performs actions



## Module 5 - Event Driven Scaling
#### Scaling with RDS
- Database sharding - breaking up databases into smaller ones



## Module 6 - Automating your infrastructure
- Getting rid of manual stuff
- We want the system to self deploy and monitor
- Infrastructure as code

#### CloudFormation
- JSON templates
- Creates resource stacks
- Launch, configure and update AWS resources
- Each fail rolls back by default, but can be changed to allow a human to manually fix instead
- Log events

#### Organising templates
- Think about optimal reusage
- Metadata allows configuration information for later use


## Module 7 - Decoupling your infrastructure
Design architectures with independent components - change or failure of 1 will not affect others

#### Strategies
- Managed services and serverless - reliability and efficiency
- Message queues instead of applications communicating directly
- Use S3

#### Microservices
- Each process does one task
- Language-agnostic APIs leveraging JSON/REST
- Split features into individual components - smaller parts to iterate on
- Get reduced test surface area
- Every component can scale horizontally, more highly available, reduced cost

### Best practices
- Change components without breaking them - should not affect consumers, interface is a contract
- Use a simple API - lower cost, can hide details, less breakage, less resistant to change
- Keep it technology-agnostic - be ready for change
- Design with failure in mind - They WILL happen. They rarely happen during testing unless you force it, so be careful. [Simian Army](https://github.com/Netflix/SimianArmy)
- Monitor your environment, not just your infrastructure
- Treat servers as stateless - interchangeable members of the group. Have enough capacity to handle workload. Use auto scaling

---

### SQS
- First service AWS released. 2006
- Communication between components
- Fully managed message queueing service
- Any volume at any level of throughput

#### Standard SQS
- Scalable
- Simultaneous read/write
- Secure - requires API credentials
- Cannot guarantee no duplicates or order


- Visibility timeout prevents multiple components from processing the same message
- Message gets picked up, then locks, then deletes once processed

#### Use cases
- Work queues
- Buffering batch operations
- Request offloading
- Fan-out
- Auto scaling

---

### SNS
- set up, operate, send notifications
- Create topic, can have subscribers. Push message to topic that distributes to all subscribers
- Single published message
- Order not guaranteed
- No recall
- HTTP/HTTPS retry
- 256KB max per message

################Get table from notes##################

#### Amazon MQ

#### DynamoDB
- Storig and retrieving processing output with high throughput
- Highly available
- Fault Tolerant
- Fully managed

#### API Gateway
- Fully managed
- Can throttle

#### Lambda
- processing data with high availability, less cost



## Module 8 - Designing Web-Scale Storage