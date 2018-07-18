# AWS Training 2018
  
## Contents

[Module 1 - Core AWS Knowledge](#module-1---core-aws-knowledge)  
- [Scopes](#scopes)
- [Regions](#regions)
- [AWS Datacenters](#aws-datacenters)
- [Availability Zones](#availability-zones)
- [Managed vs Unmanaged Services](#unmanaged-vs-managed-services)
    - [Unmanaged](#unmanaged)
    - [Managed](#managed)
- [Security Responsibilities](#security-responsibilities)

[Module 2 - Core Services](#module-2---core-services)  
- [Virtual Private Cloud (VPC)](#virtual-private-cloud-(vpc))
- [Subnet](#subnet)
- [Elastic IP](#elastic-ip)
- [Security Groups](#security-groups)
- [Network Access Control Lists (NACL)](#network-access-control-lists-(nacl))
- [Internet Gateway](#internet-gateway)
- [NAT](#nat)
- [EC2](#ec2)
- [Tags](#tags)
- [Simple Storage Service (S3)](#simple-storage-service-(s3))
- [Elastic File System (EFS)](#elastic-file-system-(efs))
- [Elastic Block Store (EBS)](#elastic-block-store-(ebs))
- [Glacier](#glacier)
- [Identity Access Management (IAM)](#identity-access-management-(iam))
- [Relational Database Service (RDS)](#scopes)
- [DynamoDB](#dynamodb)

[Module 3 - Designing your environment](#module-3---designing-your-environment)  
- [How to choose a region?](#how-to-choose-a-region?)
- [How many AZs to use](#how-many-azs-to-use?)
- [Directing traffic](#directing-traffic)
- [Logging VPC traffic](#logging-vpc-traffic)
- [VPC peering](#vpc-peering)

[Module 4 - Making your environment available](#module-4---making-your-environment-available)  
- [High Availability](#high-availability)
- [Avoid single points of failure](#avoid-single-points-of-failure)
- [Elastic Load Balancing (ELB)](#elastic-load-balancing-(elb))
- [Route 53](#route-53)

[Module 5 - Event Driven Scaling](#module-5---event-driven-scaling)  
- [Amazon CloudWatch](#amazon-cloudwatch)
- [CloudWatch Logs](#cloudwatch-logs)
- [Auto Scaling](#auto-scaling)
- [Scaling with RDS](#scaling-with-rds)

[Module 6 - Automating your infrastructure](#module-6---automating-your-infrastructure)  

[Module 7 - Decoupling your infrastructure](#module-7---decoupling-your-infrastructure)  

[Module 8 - Designing Web-Scale Storage](#module-8---designing-web-scale-storage)

---

## Module 1 - Core AWS Knowledge

# "Everything fails, all the time" - Werner Vogels, CTO, Amazon.com

### Scopes
|Global|Regional|AZ|
|:---:|:---:|:---:|
|IAM|S3|EC2
|Route53|VPC|ELastic Block Store (EBS)
|CloudFront|Internet Gateway|Subnet
| |SQS| |
| |DynamoDB| |
| |ELC| |
| |AMI| |
| |SQS| |
| |IGW| |

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
- AWS recommends replicating AZ's for resilience (ability to recover from failures quickly)

---

### Unmanaged vs Managed Services
#### Unmanaged

a.k.a. Self-managed  
- Scaling, fault tolerance, and availability are managed by YOU. AWS does not do this for you. Go replace your own unhealthy instances
- More fine tuned control

*e.g. EC2*

#### Managed
- Typically built into the service. These are fully managed by AWS

e.g. RDS, S3

---

#### Security Responsibilities
##### AWS will do:
-	Physical security - Controlled need-based access. Data centre locations are usually hidden. 24/7 guards, 2FA, disk degaussing/destruction
-	Infrastructure, knowing when it will fail, storage decommissioning, host OS patching (AMIs)
-	Network infrastructure, intrusion detection, routers, switches, cabling, etc

##### Do this yourself:
-	Patch and maintain your OWN instance operating systems, AWS does not provide the service 
-	Think about user positions and role base access 
-	Security groups 
-	OS firewalls, intrusion protection 
-	Separation of access 



## Module 2 - Core Services
### Networking
#### Virtual Private Cloud (VPC)
- This is like a container
- VPC is not a data center replacement
- VPC is a singularly logical isolated virtual network
- 1 VPC can only have 1 Internet Gateway
- Put all your stuff like EC2 instances into VPCs
- Can configure your own IP ranges, routing, network gateways, security settings

#### Subnet
- Partitions of a network divided by CIDR range
- Can be used to limit access to/from VPCs
- Each subnet must have its own CIDR block. They cannot overlap. Every step up of CIDR range halves the available IPs
- Only one route table associated per subnet
- Public subnets can talk directly to internet via a routing table entry to an IGW
- Private subnets do not have this routing table entry. Use a jump box (NAT/Proxy/bastion host) to send *outbound* to internet
- Protected subnets do not talk to internet at all, direct or indirect

#### Elastic IP
- Public IP that is statically assigned
- Associated with your AWS account
- Can mask failure of an instance by remapping the address to another instance

#### Security Groups
- App level protection
- Default allow all outbound traffic. Changing this will increase complexity. Not recommended
- No blacklisting option available. Default behaviour is to block already
- You can open ports on other security groups in case IP changes

#### Network Access Control Lists (NACL)
- Subnet level protection
- Optional virtual firewalls for subnet traffic
- Stateless
- Explicit blacklisting option available

#### Internet Gateway
- Allow communication between instances in VPC and internets by attaching to VPC
- AWS managed

#### NAT
- Like a bastian host sitting in public subnet
- Allows private instances outgoing connectivity to internet, while blocking inbound traffic from internet
- Indirect internet access
- Useful for hiding machines

---

#### EC2
- Virtual machines
- Xeon processors :D
- Placement groups: logical grouping of machines
- Instance storage is ephemeral. Shut downs clear it. Reboots keep it. 
- A shutdown then startup will spin up a new instance, clearing instance storage  
- Hardware is isolated to your instance except the T2 instance type. T2 is shared hardware  

Low/Moderate/High - Placement group - logical grouping of machines

Spot instances - choose highest price you're willing to pay to continue using the machine. Hibernation

**Careful! Giving full access to EC2 allows access to VPCs**

#### Amazon Machine Image
- Used to create EC2 instances

---

#### Tags
- Sort, search, filter
- Recommended not to use tag "name". It's completely useless since everything already has a name
- Use eg. role, owner, environment, startTime, finishTime
- Basically metadata

---

### Storage
#### Simple Storage Service (S3)
- Scalable, reliable, fast, durable
- 99.99% availability, 99.999999999% durability. Damn good
- Not a file system - object level storage
- 5TB per object, unlimited objects
- Global Unique. No need for arn to specify region
- Object PUT or DELETE can trigger notifications, workflows and scripts
- Data can be encrypted automatically
- S3 Analytics to use storage access patterns and move the right data to the right storage class
- Verifies using checksums
- Retrieve and restore every object's version
- Only read-after-write is consistent due to data being spread across places

Infrequent access
- Minimum 30 day storage
- Less availability
- Used for backups
- Really new storage type offer
- Higher cost per request
- Standard-IA (SIA) redundantly across multiple AZ, One Zone-IA 20% cheaper than SIA, but only 1 AZ

#### Elastic File System (EFS)
- Shared file storage
- Expose and mount via mount targets in appropriate subnets. Must be in same VPC
- Elastic. Petabyte scale
- Thousands of EC2 instances can access a single EFS without performance impact

#### Elastic Block Store (EBS)
- Block level storage as opposed to S3's object level storage
- Faster, but more costly than object level storage
- Attaches directly to EC2 instances
- Snapshots stored in S3, regionally scoped due to S3 duplication

#### Glacier
- 99.999999999% durability
- Data archiving. Security, durability and VERY low cost
- SSL/TLS in transit and rest

---

#### Identity Access Management (IAM)
**Delete root access keys right after sign up!**
- Identity user management
- Access keys for APIs
- Can create temporary credentials to a user to assume a role
- Groups!
- Policies can be attached to groups, users or roles
- 5000 users per account

---

### Databases
#### Relational Database Service (RDS)
- Managed service
- Relational DBs
- Do not use for massive read/write rates
- Bad throughput rate

#### DynamoDB
- SQL DB service
- Used for scale and performance
- No relationships



## Module 3 - Designing your environment
#### How to choose a region?
- Data sovereignty and compliance. Laws, etc
- Service and feature availability
- Cost
- Ping it from your current location! [Cloudping](http://cloudping.info)  

#### How many AZs to use?
- There are 3 in Sydney
- Don't need to use all of them
- Best practice - start with 2 AZs

#### Directing traffic
- Every subnet can have a route table, not multiple route tables per subnet
- Use custom route tables!
- Remember that server restarts result in IP address changes

#### Logging VPC traffic
- VPC flow logs are published to CloudWatch Logs
- Can enable for VPCs, subnets and ENIs (Elastic Network Interface)

#### Connecting VPCs
- Use VPC peering. Route traffic between peered VPCs
- Peering must be direct. Cannot flow through other peers
- No IGW or virtual GW required. No single point of failure
- Scalable
- Needs permissions to both VPCs to make and accept requests to pass packets between them
- Use route tables to peer between VPCs
- Complete routing control



## Module 4 - Making your environment available
#### High availability
- Any component can suffer loss
- Ensuring downtime is minimised without human intervention
- More hardware required for more availability
- Multi-region will increase cost, availability and complexity. Go for one region unless more is required
- If using one region, go for multi-AZ for HA

|Inherently HA|HA with the right architecture|
|:---:|:---:|
|S3 and Glacier|EC2|
|DynamoDB|VPC|
|CloudFront|Redshift|
|SQS|ElastiCache|
|SNS|DirectConnect|
|SES|
|Route53|
|ELB|
|IAM|
|CloudWatch|
|Auto Scaling|
|EFS|
|CloudFormation|
|Lambda|
|EBS|
|RDS|

#### Avoid single points of failure
- Consider EVERY component
- HAVE A SECONDARY BACKUP!

---

#### Elastic Load Balancing (ELB)
- Distributes incoming application traffic across multiple EC2 instances, containers or IP addresses
- Responds to unhealthy instances
- Application LB functioning at 7th layer. HTTP/HTTPS. Applications running inside EC2 instances, etc
- Network LB functioning at 4th layer (connection level). TCP. Super performance, same AZ routing 
- Connection draining to let back end instances to finish off requests before deregister

#### Route 53
- DNS (translates domain names into IP addresses)
- Nice domain names
- DNS failovers via health checks
- Simple routing, weighted round robin, latency-based routing, geolocation, geoproximity available




## Module 5 - Event Driven Scaling
- Enable scaling!
- Scale up - better specs
- Scale out - more instances

#### Amazon CloudWatch
- Monitors your instances
- Polls every minute (can be adjusted)
- Understand when things are nearing capacity
- YOU must specify what to monitor
- Understand where the bottlenecks are
- CloudWatch Alarms measure a SINGLE metric and performs actions

#### CloudWatch Logs
- Can be stored in S3 for other services or users to access
- Can be streamed in real time to Kinesis Streams or Lambda and others

---

#### Auto Scaling
- Launch/terminate instances based on conditions
- Registers new instances with LBs when specified
- Can launch across AZs
- CloudWatch Alarms can trigger an auto scaling policy
- Scale out early, scale in slowly. Avoid aggressive instance termination

#### Scaling with RDS
- ElastiCache for Memcached or Redis, or use your own self-managed caching solution
- Database sharding - breaking up databases into smaller ones. Higher performance and better operating efficiency
- Horizontal scaling with read replicas for read heavy workloads
- Vertical scaling often with no downtime



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
- Storing and retrieving processing output with high throughput
- Highly available
- Fault Tolerant
- Fully managed

#### API Gateway
- Fully managed
- Can throttle

#### Lambda
- processing data with high availability, less cost



## Module 8 - Designing Web-Scale Storage
- Store static content in S3

