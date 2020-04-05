SAA C01 notes
```
IAM
    IAM is Global
    Best to use One IAM user per person - for login and long term. Service limit 5000 users
    One IAM role /application. Roles have policy and trust policy(trust policy controls which identities can assume the role)
    IAM shouldnt be shared
    STS - Security Token Service - To get access to temp credentials to access AWS resources eg: AssumeRole, AssumeRoleviaSAML..
    IAM Polciy - identiy policy or resource polciy - consists of effect, actions and resource
    Policy types for managed policy - aws managed, customer managed policy
    Service Control policy : Applied at Tree level
    Priority Order: Explicit Deny > Explicit Allow > Implicit Deny
  Identity Federation(IDF)
    Types:
      Cross Account Role: 
      SAML 2.0 IDF: Use SAML v2.0 and integrated with MS AD
      Web Identity Federation: eg google, Amazon..
  STS: Secure Token Service ; Short term temp credentials
  Cognito: SSO IDF
  SAML Assertion:
    login: AD login from ADFS portal > Post SAML Assertion to SSO endpoint> AWS SSO endpoint (inside AWS which trusts your ADFS portal) 
      > STS for temp creds and predefined roles > passed to user which can be used to login 
  Assume Role:
    Returns a set of temporary security credentials that you can use to access AWS resources that you might not normally have access to
 
Compute
    Instance Types
        T2/T3 - low cost general purpose and burst capability
        M5 - For General workloads
        C4 - CPU optimized
        X1 and R4 - Memory optimized
        I3 - I/O or Storage optimized
        P2, G3, F1 - GPU/FPGA
    EIP(Elastic IP) - 5 IP address/region
    ENI - Elastic network interface(network card)
      low cost network; seperate network for PROD ; virtual network card- one elastic IPv4 address; one public IPv4 address; 1 mac; 
       ENA(Elastic Network Adapter ) - regular type ENI; when we need speeds b/w 10-100Gbps;  reliable and good throughput 
          SRIOV - higher bandworth and better networking; elastic network adapter ; upto 100Gps
       EFA(Elastic Fiber Adapter) - Better network characteristics, used for High Performance Computing
    EFA: Elastic fabric Adapter- network device to accelerate High Performance Computing; OS-bypass; machine learning
    Snapshots of encrypted volums are encrypted automatically
    EBS - Block Storage ;over network
        EBS snapshots are stored in S3, EC2 lifecycle manager helps automating EBS snapshots
    SG:
        Software firewalls - attached ENI. Each ENI has upto 5 SG's associated
        SG default behavior - default deny for inbound
        SGs are stateful: When you allow inbound rule, you are automatically allowing outbound- you can't block that at outbound level in SG
             Sg could refer another SG
     EC2 Snapshots - Aws will charge only for difference in data between snapshots - not for each snapshots
     EC2 metadata:  http://169.254.169.254/latest/meta-data/
     AMI - create AMI while the instance is in a stopped state
     EC2 instance role - instance profile - assumed by EC2
 
    ECS: Scheduling and Orchestration, Cluster manager and Placement Engine
        EC2 Mode: ECS agents runs on EC2
        Fargate : No need to manage EC2 clusters
        Task Defn: Defines the application - what's gonna run on Clusters and how.
        Task Role: fine-grained at a Task level
        Task: Running copy of a container
        Service: Allows task defn's scaled by adding additional tasks. Define autoscaling
    EC2 Performance Enhancement techniques:
        1. EBS Optimization - default - better network path
        2. Enhanced Networking - for better network performance, use ENI virtualization
        3. EC2 Placement Groups:
            Cluster PG: For performance limited to 1 AZ. All instances are physically close to each other to improve network traffic
            Partition PG: For max application availability - makes sure instances are launched in different partitions(racks) in multiple AZ
            Spread PG: Ensures each individual instances are spreadout in multiple partitions in multiple AZ- ensures max availability
    Lambda 
        Can run for upto 15 mins
    ELB
        SSL offload: Customer > LB is https where LB > instance will be http
        LB's are created in AZ. Top level LB gets a DNS record which points to each LB instances in AZs. 
        LB's have cross-zone LB capability which will forward traffic to instances in another AZ
        LB's paired with Auto Scaling Groups(ASG's) to increse scaling 
        Classic LB(CLB): legacy not recommended; Layer 3 and 4(TCP and SSL); One SSL cert/LB; DNS A record
        ALB: Layer 7 - Only handles http and htttps inbound ; Target Groups and targets to point to lambda/ECS/EC2/IP
          Complex Routing Rules
        NLB: layer 4 ; capable of extreme scaling and performance; can support protocols other than http/https; 
          less latency because no processing of the packets; targets can be addressed by IP address
        Routing Policy: Classic and ALB use Round Robin for http and for https use target group/instancw with least load
          NLB use flow hash routing algorithm
    AWS Instance Scheduler is a simple AWS-provided solution that enables customers to easily configure custom start and stop schedules 
        for their Amazon EC2 and Amazon RDS instances
    Auto Scaling Groups(ASG)
         can't span regions; specify max, min and desired capacity
         Launch Config: Defines what to launch and what configs t be used for EC2;  Older version; can't change existing launch config
         Launch Templates: Will have advanced config options and versioning and inheritance ; can't update but can create new version 
         ASG: Based on monitors and metrics ASG increses/decreses desired capacity
           Needs to attach launch template/config; Needs min size, max capacity and desired capacity
           Default cooldown - wait time between scaling events 
           Scaling Poliies: Scheduling actions if we know time of load or based monitors like CPU utilization 
         Scaling Options:
            Maintain current instance levels all time
            Scale manually: provide /update the change in max, min and desired capavity
            Scale based on schedule
            Dynamic Scaling: Scale based on demand - based on load in CPU/instances; reactive 
            Predictive scaling : Use AI to predict ; proactive approach 
         Stress is a linux utility for stress testing for EC2; eg stress --cpu 2 --timeout 30000
         ASG Termination: Order: Instance from AZ with most # of insatnces; Oldest Launch Template; instance closest to next billing hour

 
HA,DR Encryption and Fault Tolerance
    Under a region there can be upto 6 AZ
    Except IAM, Route53 and S3 most of the services are region scoped
    HA - Ability of a system to Recover from a failure and minimize impact
    Fault Tolerant - Ability of a system to operate without impacting customers - FT system will survive failure
    Discover Recovery - Ability of a system to protecting critical systems data so that the system can be recreated
    RPO : Recovery Point Objective - Time between disaster occurs and last business data was created. Defines amount of data allowed to loose. We can minimize this time via regular backups, snapshots and transaction logs
    RTO : Recovery time Objective - Time between when a disaster occurs and when the system can be restored to an operational state and handed over to business for testing. How quickly the system can be fully restored. Improve this by decreasing restore time, having hot spares tested(DR) and enforcing efficient processed
    Encryption
        Encryption at rest - Symetric- Faster - using key and algorithm
        In transit - Asymmetric - have public and private key
    Well Architected: Set of general design principles,  best practices and principles

Network:
    7 layer OSI model : Physical, Data Link, Network, Transport, Session, Presentation, Application- [Please Do Not Throw Sausage Piece Away]
        Physical - bits (optical fiber/mechanical)
        Data Link - frame - mac address for identifying individual devices
        Network - packet -  IP Address to talk outside LAN
        Transport - ports - TCP/UDP - TCP for reliability(error checking resending if missed), UDP - speed
        Session - establish sessions/communication paths
        Presentation - TLS encryption/decryption
        Application : http/https/tls
    IP Address: IPv4 and IPv6
        0.0.0.0 > 255.255.255.255 [4 Bn]
        0.0.0.0/0 : All IP Address
        127.0.0.1 - localhost/loopback
        169.254.0.1 > 169.254.255.254 - range of IP address a device can auto configure in a network
    CIDR: Classless Inter Domain Routing for Subnetting: https://www.pcwdld.com/subnet-mask-cheat-sheet-guide
        Ipaddess/subnet mask
        xxx.xxx.xxx.xxx / 24 : 32-24 = 8 , 2exp8 = 256
    Proxy Server:
        Sits in the boundary between private and private -
        act as caching server
        can filter data at  content level
    Shared Security groups - important concept
    Route table - associated with subnets
    IGW- Internet Gateway - subnet level, allow all resources inside subnet to allow private IP, create IGW, attach IGW to subnet
    NAT: remapping source IP/Destination IP of packets. Static NAT is the process of translation where an internet gateway converts a private address to a public IP address.
        Dynamic NAT is a variation that allows many private IP addresses to get outgoing internet access using a smaller number of public IPs (generally one). Dynamic NAT is provided within AWS using a NAT gateway that allows private subnets in an AWS VPC to access the internet. NATGW- dynamic address translation- session level product
        Packets in EC2 with private address of EC2 > NATGW replaces the Ec2 private address with NATGW public address > IGW converts natgw private address to IGW public adddress
        NAT instance - not managed - customer have to manage
    NACL: network Address Control List: 
        Operate at layer 4, associated with subnet, 1 NACL/subnet, allows traffic to be allowed/denied based on protocol/IP/CIDR/Port range. This works at the boundary of the subnet not within subnet
        Rules are processed based on the rule#, lower the number, the higher priority it gets. All NACL inbound have a * implicit deny rule by default, Default deny unless an allow rule is created. By default NACL's are not applied to VPC's. can't be applied to logical aws resources, stateless ie we need to define both inbound and outbound. Main usecase for NACL when yoy want to explicitly deny traffic
    VPC Peering: 
        Connect two VPC's together , Span AWS accounts and region, Services can communicate using private IP's. Data is encrypted
        Create VPC peer , Accept it from other account. Can't create a VPC with CIDR overlap.
        Transitive Routing: Not possible in VPC peerign , any connectivity between accounts needs its own VPC peer
    AWS public services: S3, Dynamo, Cloudwatch, SNS, SQS…
    Security group references(referign to another security groups can be cross account but can't be cross region
    VPC Endpoints: Used to privately connect to AWS public services w/o going through internet via IGW, powered by AWS private link
        Gateway Endpoint: only for S3 and Dynamo, select route tables to be allowed, can attach policy(gateway endpoint policy)
        Interface Endpoints: For connecting to everything else other than S3 and Dynamo, Select the subnets . Provide another unique service for the service
    Egress only IGW: IPV6 outgoing and blocks incoming- IPv6 address translation

    Route 53:
        Is global service, DNS
        DNS, DNS Server DB is hierarchical and highly distributed
        ROOT servers : managed by 13 authorities across world, 
            authoritative; TLD: top level domains like .com, .in, .eu; Subdomains;  Zone/Zone File; Records; Name Server:  server that runs DNS service and store or cache info for DNS platform
        Splitview DNS:
            same route53 public and private zones with different targets
        Records in Zone:
        A records : Ipv4. maps host to IP
        AAA: IPv6 maps to host to IP
        CNAME: map IP address to alias
        MX: mail servers
        NS : Authoritative servers within subdomain
        TXT: descriptive text in domain
        Alias: extension of CNAME

        Run DNS tracing by command: "dig +trace 'google.com'"
        Global Service doesn't need region
        DNS: Tree like DB; chain of trust
          Root Servers: Global root servers [very top level]- managed by 13 companies; trusted globally
          TLS: root contain top level domains like .com/.gov/.in
          Subdomains: eg .aws. .fmr. ...
          zone: mapping of IP's and hosts gicen to domain
          Records: record types A, AAA, CNAME, TXT...
          name Server : runs DNS service which can store or cache info of DNS platform
        When you browse for a website Browser cache> ISP if they don't know forward to DNS root servers > DNS Root server if they don't know they provide authorative servers>  they reach the DNS service which run the website
        dig +trace <websitename> to troubleshoot internet route
        R53 Domain Registration: 
          Purchase avaialble domains
          Hosted Domains: purchased domains will show up on hosted domains - check by nslookup or dig
          Hosted Domain will contain record sets inside which is the mapping and will contain details about records
          TTL(Time to Live): duration to cache in browser level; 24hr is high TTL and 60s is too low
          CNAME vs ALIAS: 
            CNAME points URL to any other URL only for non-root domain; you could create a CNAME at Load balancer level
            Alias for AWS resource Alias is free and will have native healthcheck
          Routing Policy:
            Simple: maps a domain to one URL ; when you need redirect to single resource; can't attach healthchecks; 
              if multiple values returned, a random value will be chosen by browser
            Weighted: Control % of requests to go to a specific endpoint ; each endpoint will have a weight which is th percent of traffic
              Helpful for traffic splitting between regions or canary releases; can do healthchecks; 
            Latency: Route traffic based on least latency; if user will be routed to a record with least latency
            Failover: Will server primary and will route traffic to secondary if healthcheck fails in primary; select associate with healthcheks
            Geolocation: based on user location ; routes to resource closest to user
            GeoproximityL based on closest data center closer to user
            Multi value: upto 8 resources and ; will have healthchecks
          Health Checks: if resource fails healthchecks 3 times it deemed unhealthy and traffic will not be forwrdes; same 3 checks for healthy - it can create an alarm

Hybrid Networks
    VPC
    With a custom vpc , nacl, sg and route table are created. 
    CIDR.xyz - site to calculate subnet IP addressess
    NAT allows instances to go out to the internet- act as a bridge to IGW
      NAT instance - individual instances in a subnet
      NAT Gateway : HA service ; 1 per vpc ; created in an az
    
    NACL
      stateless; allows deny ; one subnet will have only one NACL
    Flow logs :
      once configured, it can't be changed
    Global Accelerator:
      Utilize edge locations 
    Egress-Only Internet Gateway:
      allow IPv6 based traffic within a VPC to access the Internet,
      denying any Internet based resources the possibility of initiating a connection back into the VPC.
    VPC Endpoint: 
      allows vpc to connect to supported services w/o traversing internet, use privatelink
        interface Endpoints: ENI with private ip supporting most coomon aws services
        gateway Endpoints: for s3 and dynamo 
    VPC Peering:::
      Connects two VPC's - span region and accounts, Layer 3
      VPC CIDRblocks can't overlap; SG's can be referenced but nit cross region; 
    WAF:
      Layer 7 ; allows to block based on IP, country, values, strings, Cross site scripting, SQL ijections
    VPN:
      Static: one directional ; Dynamic: Exchange info dynamically over PGP routing protoco
      Virtual private gateway(VGW): virtual gateway attached to VPC in aws. termination point for vpn tunnels. HA and managed by AWS
      Customer Gateway- physical router at customer data center, virtually connect it to Virtual Private gateway 
      layer 4 ; hardware component
    VPC Flow Logs:
      Only monitor traffic metadata; Not for monitoring contents of IP traffic; Can be stored in S3 or Cloudwatch9needs permission)
      monitoring point: Can be attached at  VPC, Network interface or Subnet level
      Captures - 
        Source and destination IP addresses
        Source and destination ports
        Protocol
        Bytes
        Start and end
        ALLOW or REJECT status
       
    Organizations - billing consolidation and aws account administration and management : Master account can't be restricted
    AWS Systems manager Session manager - for connecting to EC2 securly.https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html
    Billing
        On-Demand : Per second with 60sec min, 
        Spot : massively reduced pricing (upto 90%) but instance will be shut down with 2 min warning
        Reserved : Cheaper rates based on long term contracts 12 month contracts
        Dedicated Hosts: Expensive but you are not sharing the physical hardware with others

Storage:
        Data Persistence
        Ephemeral - Data which is local to a resource - temporary store eg: instance store volume  recommend use ElastCache
        Transient - Data exists in a form while its passed between sources eg: Queue
        Persistent - Data which is durable and survives power events eg: EBS, EFS recommend RDS
    S3:
        Names are globally unique ; read after write consistency for PUTS eventual consistency model for deletes and updates
        Manage S3 access using Identity policies to Roles, users and groups
        Buckets are Reginal but can be read from anyware
        Bucket trusts the account which created it. It doesn't allow any other account by default
        Max object size - 5Tb; he minimum object size is 0 bytes, however you will be billed for 128 KB. 
        Objects smaller that 128 can still be stored, but will be billed as if they are 128KB.
    Storage tiers 
        Sandard
            default storage class ; 99.999999999% durability & 99.99% availability
            Low latency and high throughput performanc ; designed to sustain the loss of data in a two facilities
        Standard IA(Infrequent Access)
            optimized for long-lived and less frequently accessed data; 99.999999999% durability & 99.9% availability
            suitable for objects greater than 128 KB kept for at least 30 days; Cheaper but require 30 days to be stored min
        Standard IA One Zone
            designed for noncritical, reproducible data stored at lower levels of redundancy than the STANDARD IA storage class
            99.999999999% durability & 99.5% availability; Cheaper but require 30 days to be stored min
        Glacier
            suitable for archiving data where data access is infrequent and retrieval time of several (3-5) hours  is acceptable
            99.999999999% durability; 90day and 40KB min charge
        Glacier Deep Archive
            Cold backup ; 180day and 40KB min charge ; Cheaper but takes more time to retrive
        Intelligent Tiering 
            Storage classes defined at object level; s3 will automatically decide the tier based on access pattern(two access tiers)
        Lifecycle Rules: Automated transition/expiration
    S3 single put upload max size: 5GB
    S3 multipart upload: Individual objects are brokern into smaller parts and these parts are uploaded in parallel in S3, 
      Once complete, all parts are combined. If an individual part fails, just need to retry that part
    S3 Transfer Acceleration: Makes use of cloudfront, once data reach cloudfront, it will be transfered to S3 at high speed
      Need to enable acceleration at bucketlevel bucketname.s3-accelerate.amazonaws.com 
    S3 Key: unique identifier for object in S3 :  bucket + key(directory and objectname) + version(optional)
    Encryption:
    Encryption occours at object level 
      Client Side : client is responsible for encrypting data before sending data before sending to S3/Server
      Server Side Enc with Customer mgd key(SSE-C): when you PUT provide encryption key, 4 decrypt provide decryption key
      Server Side Enc with S3 managed keys(SSE-S3): makes use of S3 AES-256 keys
      Server Side Enc with KMS managed keys(SSE-KMS): Use customer master key which is managed in KMS
    S3 Presigned URL: Gives bearer permission to access to an object publically. It have an expiry time(7day max)
    S3 CORS: cross origin resource sharing: Security measure build into web apps to allow apps in one domain 
      to run scripts/actions on another domain/app/bucket
    Versioning: Once enabled can't be disabled can be suspended. When you delete, the version creates a delete marker
    S3 CRR(Cross Site Replication): RR replicates every object-level upload that you make directly to your source bucket. 
      The metadata and ACLs associated with the object are also part of the replication\
      Not retroactive - will not pick old objects; Need a role with required permissions; requires versioning enabled on both buckets; 
      allows storage class transition of destination; Lifecycle events are not replicated; doesn't allow SSE-C
    Object Lock: prevents deletion and overwrittenObject lock is enabled at the bucket level; 
       when creating the bucket, you can select the feature to lock objects in it. 
       However, once the bucket has been created, you cannot enable object lock, you will have to contact customer support to do so

  CloudFront: 
            CDN: Content Delivery Network: CloudFront
            Orgin: Source where the content is stored like webserver/S3. It needs to be public
            Distribution: Config option for cloudfront
            Web: http/https
            RTMP: Adobe flash media server
      Closest CF Cache >if its a miss> Regional CF Cache >if its a miss> Origin also save a copy in regional CF edge loction
      S3 OAI(Origin Access Identities): Allow S3 to accept connections only from CloudFront
    EBS:
      General Purpose SSD(gp2): Most workloads; max IOPS 16000, 
      Provisioned SSG(io1): Highest performance; Max IOPS 64k
      Throughput Optimized HDD(st1): High throughput but low IOPS; max IOPS 500
      Cold HDD: file servers; IOPS 250
      EBS magnetic: previous generation ; infrequently accessed; IOPS 40-200
    EFS:
      FUlly managed; read after write consistency; HA
      NFS file system; Network based, can be attched to multiple servers; use NFS v4; Elastic no need to manage size ; 
      EFS tied to VPC; EFS Access points simplify access to datasets in EFS via IAM 
      EFS offers a Standard and an Infrequent Access storage class; EFS Performance modes are General purpose and Max I/O
      Mount targets:  lives within subnet ; EC2 connect to mount targets; 1/az
      AWS dataSync - to move data from on-prem to EFS
    FSX:
      File system for windows
      FSX for windows: SMB file system for windows
      FSX for Lustre: Fully managed file system for compute intensive, high performance workloads; big data; high speed
    Snowball:  ; S3 adapter and snowball client; 
      Ship Huge Amt of data via suitcase size HDD 50TB/80TB with 42/72 TB usable storage
    Snowball Edge: comes with compute if need to process data prior to importing ; llar to snowball  ;100Tb with 82Tb usable with 
    Snowmobile: Truck with datacenter in the shipping container; for more than 10TB; upto 100Pb
    Storage gateway: Allows to connect on-prem datacenter to AWS ; Virtual appliance run on Datacenter ; 
      works as datacenter extension or for migration ; works over public internet; offers a local cache 
      Three modes:
      File gateway: as objects in S3
      Volume gateway: access volumes(iSCSI) NTFS stored in EFS needs to be mounted 
      Tape gateway: (Virtual Tape Library(VTL)) Glacier
      It is a virtual appliance used for data center extensions or migrations
    Database Migration Service(DMS): for migrating SQL DB's; a virtual compute for migrating data from source to target
      over internet; providesschema conversion tool
    Data Sync: for replication ; bi-directional

DB
    RDS
      Oracle, MySQL, Postgres, MariaDB, MS SQLServer
      Max Read replicas for RDS: 5
      Read replicas are available in Amazon RDS for MySQL, MariaDB, PostgreSQL and Oracle as well as Amazon Aurora.
      DB parameter group: acts as a container for DB engine configuration values that are applied to one or more DB instances.
      DB options group: additional configuration and security options for provisioning and managing db
      Automated Backups: For point in time recovery. Retention period 0-35 days and default 7 days; captures everything including 
        full transaction logs; You can initiate a point-in-time restore and specify any second during your retention period; incremental; 
        rds backup size = db allocated size; 
      DB Snapshots: are user-initiated and enable you to back up your DB instance in a known state as frequently as you wish
        and then restore to that specific state at any time; snapshots not automatically deleted are kept even after the DB is deleted; 
        can be copied to different region but use new key in different key and kms should have right mater key.
        automatic backups of an RDS instance are deleted upon termination unless specified
      Restore
         when you restore, you are creating a new DB based on the backup; creating a new CNAME not restoring on top of existing
         enable encryption on non-encrypted - encrypt a copy of a Snapshot and restore the encrypted copy
      MultiAZ RDS- synchronous replication; CName point to primary; you can't update/modify standby; 
        All RDS support multi AZ
        there's gonna be a minor glitch with CNAME switch to standby
      Read Replica: 
         Asynchronous replication(eventual consistency); Multi region;  Used to scale read throughput adn reduce load on primary; 
         Read replica itself can have standby; Master should have auto backups enabled for read-replica
         RDS Read Replicas are not available for Microsoft SQL; Amazon RDS doesn’t support read replicas for Oracle version 11
    Aurora:
      Two types: Aurora Provisioned and Aurora Serverless
      Cluster ; Storage is shared among clusters; storage has 6 replicas across 3 az; Self Healing ; 
      Compatible with MySQL/PostgreSQL; Only Primary can write ; Billed for storage we use
      Performance Options: Memory optimized and Burstablw
      Scaling: 2 scale write, increse size of primary, 4 reads add readers(upto 15 readers) 
      Cluster contains atleast 1 primary with 0/more replicas
      Aurora auto scaling polciies can automatically adjust according to the load
      Failover: based on the priority number  ; can handle 2 failues w/o writes being imapcted and 3 failues w/o impacting reads
      Backtrack: Recovery feature- rewinds Db to time you specify in the same db instance - creates an outage ; limit  is 72 hours
      Parallel Query: Optimization that parallelizes some of the I/O and computation involved in processing data-intensive queries
        Allows queries to be executed across all the nodes at the same time
      Aurora Global DB: 1 primary aws region and 1 read only secondary - can't use burstable- expensive 
      Aurora Serverless: Best for light capacity apps ; Serverless- specify min and max and it will manage the DB - all handles end to end
      Aurora Capacity Unit(ACU) - can be scaled down to 0 capacity units ; Aurora Serverless use shared cluster storage same as Aurora      
        provisioned but compute is provisioned as per demand - it maintains pool of servers ready to go to reduce delays
      Servers are provisioned outside your VPC using Private link; can use query editor by using data API
    Dynamo:::
      Fully managed DBaaS ; Synchronously replicates data across atleast 3 AZ in a region
      Multi read and multi write; Later writer wins conflict resolution 
      Key value type but its columns are wide ; Every key needs to be unique ; charged by the hour for the throughput capacity; 
      Billing modes:
        On-demand capacity mode, DynamoDB charges you for the data reads and writes your application performs on your tables
        provisioned capacity mode, you specify the number of reads and writes per second that you expect your application to require, 
          can useauto-scaling; best for predictable apps
      Partition Key: unique identifier for a table aka hash key ; 
      Sort key: additional key when partition key is not suffivient to establish uniqueness aka range key
      Table is a collection of items sharing same partition key; 
      Item is a colln of attributes inside a table with same primary key 400Kb is max item size; attribute is key and value inside a table item;
      Scan is like list - like ls command gets all data and applys filter ; not very efficient for large tables
      Query is like SQL sqery it can retrieve data for only 1 partition key can filter based on only sort keys
      Point in time recovery for the last 35 days - needs to be enabled per table 
      Dynamo Can also create manual expilicit backup
      Global tables - replicates data to other regions via streams 
      Data Consistency 
      Eventually consistent (the default) – maximizes read throughput. However, read might not reflect the results of a recently completed write. All copies of data usually reach consistency within a second. Repeating a read after a short time should return the updated data.
      Strongly consistent — A strongly consistent read returns a result that reflects all writes that received a successful response \
        before the read.
      A unit of read capacity: 1 strongly consistent read per second or two eventually consistent reads per second for items as large as 4 KB
      A unit of write capacity: 1 write per second for items up to 1KB
      DB Streams: Used for replication ; Sreams are per table - rolling 24 hour window of changes; Streams can be used for trigger events
      the following are the stream view types
        keys only type: Only key attributes of the modified item are streamed
        New image type: Only the modified/changed part is streamed
        Old image item: Streams the old state (captures the old fields which were removed/modofoed)
        Old and New: Contain item before and after change
      Indexes: 
        For incresing performance for Scans and queries; 
        helps to create alternative perspective for queries based on alternative sort key
          Local Secondary Indesex(LSI): Exists Inside table ;gives additional filters for efficient scanning;
             LSI needs to be creted at table creation . limit 5
          Global secondary indexes (GSIs) allow data in a table to be presented using an alternative partition and sort key; which allows 
            radically different perspective ; GSI support alternative data access patterns, allowing efficient use of query operations. 
            seperate set of data stored seperately . can be created after table creation; limit 20
      Redshift:
      Data Warehouse ; Column based storage ; For large amount of data Pb scale; Usually target data-store after the transactions have been processed
      Can be provisioned ad-hoc basis or can be reserved cluster
      Redshift cluster contain a Leader Node and number of compute nodes; 
        Leader shift dives the query to smaller pieces and distributes it amongst comoute nodes for processing
      For OLAP(online analytical processing) - column based db will be better for analytics because it can handle very large amount of data
      row based Dbs are good for transaction based processing
      Analytics
    Athena:
      Serverless data processing; Creates virtual table(which acts as schema) in athena from S3; 
      Data lives on S3; Runs SQL on transactional type data ;
      Supports CSV/JSON/XML/PARQUET/AVRO/ORC; Schema on read; Ad-hoc reads from different sources; 
      billed only for compoute used for querying and any storage; can be used to query AWS logs including VPC flow logs 
    EMR:
      For large scale analytics works for longer duration ; Can be short lived, short lived or ad-hoc; 
      S3 can be used as intermediete even if we use HDFS
      master node: controls everything happends in the node - distributes the jobs
      core nodes: runs jobs, manage HDFS(if HDFS is used)
      task node: only runs tasks; og=ften uses spot instances
      Managed hadoop ; Distributed model; EFS as HDFS; 
      EMRFS: Uses S3 for persistant storage
    Kinesis:
      To process huge amounts of real time data; Collect and analyze high volume of data at any scale
      Helps to find patterns, trap exceptions
      Producers > Kinesis Strems > Consumers
      Kinesis data record: basic data wtitten to read from kinesis streams
      Stream can be scaled infinitely
      Shards will hav 1Mb ingestion(write) and 2 mb consumption(read) capacity; billed per shards
      Streams contains rolling 24 hours of persistance storage; can be extended to 7 days
      Scalable and resilient streaming service
      Four Products:
        Kinesis Video Streams: Such as videos/IOT device data
        Kinesis Data streams: Such as clickstreams/Logs
        Kinesis Data firehose: Reliable way to collect data and store in S3/analytics tools/third party tools; scales automatically
        Kinesis Data Analytics: provides templates and an interactive editor that enable you to build SQL queries that perform joins, 
          aggregations over time windows, filters, and more analytics on the stream data
    


App Utility Services
    Cloudwatch
    Near real time monitoring and metrics; By default anything visible to the instance like CPU usage will be monitoed by Cloudwatch
    Cloudwatch agent gives additional monitoring like process level memory utilization
    Free 10 alarms with CloudWatch
    Alarm States:
      insufficient: not enough data to judge state
      Alarm: alarm threshold have reached eg 90% CPU
      ok: Threshold has not breached
    Alarm Components:
      metric: data points over time being measured
      threshold: defines whats good and whats bad ; exceeding this is bad
      period: duration the threshold should be bad for the alarm
      Action: What to do eg SNS, ASG, EC2, lambda events
      Dynamic threshold: New alarm feature : auto detect anomaly level based on usage which defines dynamic threshold
    Logs:
      Designed for collecting data stream
      Log event - timestamp + Message
      Log Stream is a sequence of log events  
      Log group is a container for log streams which help to define retention, apply filters, monitong and access control
    
    CloudTrail:
      For governance, compliance and risk management service, enabled by default; All API calls are logged for 90 days
      Event history: 90 days events gives info of anything happened inside the account
      Trails: Whats logged and where its logged; trail is per region; Gives org wise audit trail
      Category:
        Management event: Logged at account management plane
        Data events: Object level logging eg actions in S3 and lambda 
      Log validation will add additional checks 
      Cloudtrail can be integrated with cloudwatch logs 
    Simple Notification Service (SNS): manages sending and delivery of messages
      Regional; highly resilient; fully mansged scalable; public endpoint; needs public endpoint; 
      can use resource polcies ; 
      Topics: Isolated config for SNS, messages <256kb are sent to topic
      Publisher: entity sends messages to topics apps/aws services S3/CFN/Cloudwatch/ASG
      Subscriber: Endpoints that receive message, When a mesaage is sent to a topic thats is sent to all subscribers
        http/https/email/email-json/SQS/lambda/mobile push/SMS; Filters can be applied on subscriptions; 
    Simple Queue Service(SQS): 
      messaging Queue to decouple apps; asynchronous messaging; inter-service/inter-app messaging; 
      Types of sns queues
        Standard: Wide/unlimited throughput: you might have more than one copy of message; best ever ordering
        FIFO: first-in-first-out: trade off in throughput; 300 messages and 3000 with batching ; no duplucates and ordering is preserved
      message upto 256kb ; Polling - process of retrieving the messages; 
        Short polling: single api call; get 0 to upto 10 messages per short poll, if we don't receive needs to poll again
        Log poll: if there are no messages, it will wait for messages for a given wait time seconds; 
          after that time the message gets delivered to receiver- its more efficient
      Once you read the message, the message will be hidden untill visibility timeout; the receiver meeds to acknowledge/delete
        otherwise the message stays in the queue
      Default retention period for quese: 4 days safter it will get deleted
    Elastic Transcoder:
      Serverless service to convert media files from one format to other; charged for compute ;use pipeline (queue of jobs)
      pipeline stores the config; can take 1 input and produce upto 40 different formats; presets defines output format
    AppSync: Store and sync data between mobile and web app
    KMS:
      Keys never leave region;
      Alias points at multiple keys - that way the apps are agnostic of keys/key changes
```
