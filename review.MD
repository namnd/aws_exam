### IAM
- AWS Security Token Service (STS) -> request temp, limited-privilege credentials for AWS IAM users 
- By default, available as a global service, single endpoint https://sts.amazonaws.com, requests map to US East region
- Recommend to use Regional AWS STS endpoints:
    + Reduce latency
    + Build in redundency
    + Increase session token validity: Regional STS endpoints are valid in all AWS Regions. Global STS endpoints are valid only in default regions

### S3
- S3 buckets can be configured to be automatically encrypted 
- S3 store data across multiple AZ by default, except for S3 One Zone - IA
- Enable cross-region replication, must has versioning enabled
- Encrypted options: 
    + SSE-S3 (Server-Side Encryption with Amazon S3-Managed Key)
    + SSE-KMS (Customer Master Keys)
    + SSE-C (Customer-Provided Keys)

- Should NOT use S3 without File Gateway / Using File Gateway is the recommended way to use S3 with shared document pools
- Multipart upload
    + improved throughput
    + pause/resume object uploads
    + quick recovery from network issues
    + begin upload before knowing final size
    + Not support append file

### CloudFront
- Signed Cookies
- Signed URLS
- Origin Access Identity
- Web distribution for Web
- RTMP distribution for Adobe Media Server / Adobe Real-Time Messaging Protocol

### Storage Gateway
- Attaching infrastructure located in a Data centre or office to AWS Storage infrastructure
- File Gateway: file system mount on S3
- Tape Gateway: VTL inteface (iSCSI virtual tape library)
- Volume Gateway

- RAID 0 can stripe multiple volumes together; for greater I/O performance
- RAID 1 can mirror two volumes together

### EBS
- Replicated within AZ by default
- blocked level storage service for use with EC2
- Can enable Encryption (NOT by default)
- Provisioned IOPS SSD > General Purpose SSD > Throughput Optimized HDD > Cold HDD

### EFS
- Replicated across multiple AZ
- Two storage classes: Infrequen Access (IA) and Standard
- Using Amazon EFS with Microsoft Windows is not supported

### RDS
- MySQL: no limit
- Oracle: 1
- SQL Server: 30
- PostgreSQL: no
- Stand by replica - synchronous replication with primary
- Read replica - asynchronous replication
- Multi-AZ for high availability
- Read replica is for offload read
- Automatically performs storage volume snapshot of the entire Database Instance
- Database snapshot has to be manualy backup by users

### DynamoDB
- Incoming Data Transfer is Free
- Outcoming Data Transfer is chargable
- Replicated across region is chargable
- Attribute name & value <= 400 KB
- Automatic data replication over 3 AZ in a single region
- Infinitely scalable read-write I/O running on IOPS-optimized SSD
- Data backed up to S3
- Integrated with EMR, Kinesis
- Read Consistency
    Eventually Consistent Reads
    Strongly Consistent Reads
        Possible server error (HTTP 500)
        Higher latency
        Not supported on global secondary indexes
        Use more throughput capacity
- ACID transactions
- Low-level API, every HTTP(S) request must be correctly formatted and carry a valid digital signature
    + X-Amz-Data: date
    + X-Amz-Target: name of DynamoDB operation
    + Host, Accept-Encoding, Content-Length, User-Agent, Content-Type, Authorization

- Max throughput rates of 10K write capacity units or 10K read capacity units for an individual table
- 20k for as single subscriber account
- Automatically spreads the data and traffic over a sufficient number of servers
- All data is stored on SSD, is automatically replicated across multiple AZs in a Region
- Use global tables to keep tables in sync across Regions

### EC2 - Elastic Compute Cloud
- Limited to max 20 instances per EC2 region (NOT per AZ)
- Auto Scaling group scales in - use the oldest launch template
- Cannot modify launch configuration after you created it. You can update Auto Scaling group to use new LC
- Underlying Hypervisor
    - Xen offers 2 kinds of virtualization: HVM (Hardware Virtual Machine) & PV (Paravirtualization)
    - Nitro
- Must be stop to resize to has more/less RAM, CPI, I/O, or networking capabilities
- AWS does NOT copy launch permissions, user-defined tags, S3 bucket permission from the source AMI to the new AMI

- Cluster Placement Group: within a single AZ, for low-latency, high network throughput, can NOT span multiple AZ
- Partition Placement Group: reduce hardware failure
- Spread Placement Group: placed on distinct rack, with its own network and power source, maximu 7 running instances per AZ

### ECS - Elastic Container Service

### VPC
- Public subnet within a VPC has at least one route in its routing table that uses an Internet Gateway (IGW)
- Once a VPC is set to Dedicated hosting, it can be changed back to default hosting via CLI, SDK or API. This will not change hosting settings for existing instances, only future ones. Existing instances can be changed via CLI, SDK or API but need to be in a stopped state to do so.
- New custom VPC: a default Security Group, Access Control List, Route Table are created automatically. Must create your own Subnets, Internet Gateway, and NAT Gateway
- Can replace the main route table with a custom route table
- NAT Gateway cannot be created without an elastic IP Address. During the creation of NAT Gateway, Elastic IP Allocation ID is a mandatory field, without which we cannot proceed to create NAT Gateway
- NAT Gateway cannot send traffic over VPC endpoints, VPC connections, AWS Direct Connect, or VPC peering connections.
- VPC Peering has only limitation that it does not support VPC Transitive Peering
- If instances are on same subnet, they share same route table
- Custom VPCs and subnets configuration that does not apply a public IP address by default
- VPC Gateway endpoint are not supported outside VPC
- Security group for allowing the traffic while NACL for denies the traffic
- Egress-Only Internet Gateway allow IPv6 based traffic within a VPC to access the internet. It also denies any IPv6 based Internet resources initiating a connection back into VPC.
- Egress-Only IG is stateful
    + cannot associate a Security group with an Egress-only IG, you can use SG for your instances in the private subnets to control the traffic to/from those instances
    + can use a network ACL to control the traffic to/from the subnet for which the Egress-only IG routes traffic.
- VPC Flow Logs can be created at levels of:
    - VPC
    - Subnet
    - Network inteface
- Can have multiple endpoint routes to different services in a route table
- Can have multiple endpoint routes to same service in different route tables
- Can NOT have multiple endpoint routes to same service in a single route table

- Bastion host in Public subnet
- NAT instance/gateway in public subnet

### WAF
- Allow/block/monitor web requests based on conditions
- Conditions include IP address, HTTP headers, body, URI String, SQL injection, corss-site scripting
- Tightly integrated with CloudFront, Application LB
- Rules run in all AWS Edge Locations, blocked requests are stopped before reaching web server
- No expense of performance
- Allow all requests except the ones...
- Block all requests except the ones...
- Count the request that match the properties

### Athena
- Serverless, interactive query service to analyze data in S3 using standard SQL
- Supporting data formats: CSV, JSON, ORC, Apache Parquet and Avro (NOT XML)

### Macie
- ML-powered security service to discover, classify and protect sensitive data (PII)

### Shield
- Managed DDoS protection service
- Standard & Advanced

### Direct Connect
- All AWS services: EC2, VPC, S3, DynamoDB, etc.


### SQS
- VisbilityTimeout: when a consumer instance retrieves a message, that message will be hidden from other consumer instances for a fixed period
- DelaySeconds: a new message is added to the queue, it will be hidden from consumer instances for a fixed period
- WaitTimeSeconds: when consumer instance polls for new work, the SQS service will allow it to wait a certain time for one or more message to be available before closing the connection
- Short Polling
- Long Polling: enable by setting the ReceiveMessageWaitTimeSeconds to > 0
- Messages can be retained in queues for 60 seconds up to 14 days (default 4 days)

### KMS
- Create & control keys used for cryptographic operations
- Create
- Import your own key
- Temporarily disable keys
- Re-enable disabled keys
- Schedule the deletion
- Audit use of keys
- Region-specific, have different API endpoint

### ElastiCache
- Memcached: support snapshots
- Redis: scale up to 170.6TB of in-memory data. Redis Cluster up to 250 nodes & 250 shards. Scale in and out without downtime

Stateless Web service: RDS, DynamoDB, ElastiCache

ELB help to delivery stateful services
EMP is a data crunching service

ElastiCache for Redis does offer native encryption service

### Route53
- Hosted zones have a grace period of 12 hours - no charge if delete hosted zone within 12 hours
- Max of 500 hosted zones, 10k resource record sets per hosted zone. Can be requested to increase
- Alias Records: Amazon Route 53-specific extension to DNS. Can create alias records to route traffic to selected AWS resources, ELB, CloudFront, Elastic Beanstalk, API Gateway, VPC inteface endpoints, S3. Can also point to AWS Resource that are hosted in other accounts by manually entering the ARN
- Support wildcard entries, except for NS records
- Doesn't have default TTL, must be set
- Can use 'Alias' record with sub-domains to ELB, CloudFront, S3
- CloudTrail can record and log Route 53 history call 
- Point zone apex to ELB with Alias
- Cname non Alias for domain alias

### API Gateway
- Control access to an API in API Gateway
    + Resource policies: allow/deny access to API and methods from specified source IP address or VPC endpoint
    + Standard IAM roles and policies
    + CORS
    + Lambda authorizers
    + Cognito user pools
    + Client-side SSL cert
    + Usage plan

- Simple
- Weighted 
- Latency
- Failover
- Geolocation
- Multivalue Answer

- Traffic Flow

### CloudTrail
- Logs is be encrypted by default using SSE. You can also choose to use AWS KMS key
- Enable the CloudTrail log file integrity validation feature, create a hash file called digest file which refers to logs that are generated


### CloudFormation
- Version
- Description
- Metadata
- Parameter
- Mappings
- Conditions
- Transform
- Resources
- Outputs

Windows instances are billed by the full hour
Storage charges even if the DB instance is idle

### Storage Gateway
- Tape Gateway: backup application with iSCSI virtual tape library (VTL) inteface, stored in S3, archived to S3 Glazier
- File Gateway: store/retrieve objects (NFS), can be directly accessed in S3
- Volume Gateway: block storage using iSCSI protocol, can take EBS snapshots -> used to create EBS volumes
    + Cached volumes: low latency access to frequently accessed data. Retain a copy of frequently accessed data subsets locally.
    + Stored volumes: low latency access to entire datasets 

### VPN
- Virtual Private Gateway: has information regarding AWS side of the VPN
- Customer Gateway: contains information about 3rd party site: external IP, type of routing
- Site to Site VPN
    + 1 internet gateway per VPC
    + 5 virtual private gateway per account per region
    + 50 customer gateway per account per region
    + 10 IPSec VPN Connections per virtual private gateway

- Configurable ASN (Autonomous System Number) allows customers to set the ASN on the Amazon side for VPN and AWS Direct Connect private VIFs

### PrivateLink
- provides secure private connectivity for services between separate VPC's.

### AWS Trusted advisor
- S3 Bucket permission
- Security Group
- IAM use
- MFA on Root Account
- EBS public snapshots
- RDS public snapshots
- Redshift, EC2 Reserved instances lease expiration
- Service Limits (IAM)


### Opsworks
- Stacks
- Chef Automate
- Puppet Enterprise

### Lambda
- Pricing based on Duration of execution (seconds) and Amount of memory assigned

### Application Load Balancers
- Monitor by:
    + CloudWatch metrics
    + Access logs
    + Request tracing
    + CloudTrail logs
