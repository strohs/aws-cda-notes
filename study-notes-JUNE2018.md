AWS Certified Developer - Associate (June 2018)
=====================================
[AWS Certified Developer](https://aws.amazon.com/certification/certified-developer-associate/)
## Exam Blueprint
1. Deployment 22%
2. Security 26%
3. Development with AWS Services 30%
4. Refactoring 10%
5. Monitoring and Troubleshooting 12%

IAM 101
=====================================================================================
* centralized control over your AWS account
* Shared access to your AWS account
* Granular permissions
* Identity Federation
    * Active Directory
    * Facebook
    * Linkedin
    * plus others....
* Multifactor Authentication (MFA)
* Provides temporary access for users/devices and services (as necessary)
* Allows you to set up password rotation policies
* supports PCI DSS Compliance

## Important Terms
* Users - end users (people)
* Groups - a collections of **users** under one set of permissions
* Roles - you create roles and assign them to AWS resources
* Policies - a document that defines one or more permissions
    * can be shared among users, groups, and roles

* IAM is universal (it applies to all regions (for now))
* new users have no permissions when first created
* new users are assigned an access key and secret access key when the user is first created
    * if you loose them, you have to regenerate them


EC2
=======================================================================================================================
## EC2 Instances
* **On Demand** - you pay a fixed rate, by the hour (or second) with no commitment
    * perfect for users that want low cost and flexibility of AWS EC2 without any up-front payment or long term commitment
    * applications with short term, spiky, unpredictable workloads that cannot be interrupted
    * applications being developed or tested EC2 for the first time
* **Reserved** - provide you with a capacity reservation and offer a significant discount on the hourly charge. 
1 year or 3 year terms
    * applications with steady state or predictable usage
    * applications that require reserved capacity
    * users can make up front payment costs to reduce their total computing costs even further
        * *Standard RIs* (up to 75% off on-demand)
        * *Convertible RIs* (up to 54% off on-demand) feature the ability to change the attributes of the RI as long as
        the exchange results in the creation of RIs of equal of greater value
        * *Scheduled RIs* are available to launch within the time window you reserve. This allows you to match your
        capacity reservation to a predictable recurring schedule that only requires a fraction of a day, week, or month
* **Spot** - enable you to bid whatever price you want for an instance capacity, providing for greater savings if your
applications have flexible start/end times
    * application that have flexible start and end times
    * applications that are only feasible at very low compute prices
* **Dedicated Hosts** - Physical EC2 server dedicated for your use. Dedicated hosts can help reduce costs by allowing you
to use your existing server bound software licenses.
    * useful for regulatory requirements that may not support multi-tenant virtualization
    * great for licensing which does not support multi-tenancy or cloud deployments
    * can be purchased On-Demand (hourly)
    * can be purchased as a Reservation for up to 70% off the On-Demand price

### EC2 Instance Types

Family | Specialty | Use Case
:-----:|:---------:|:--------:
D2 | HDD Dense Storage | File servers,Hadoop,Date warehousing,Massively Parallel Processing (MPP) 
H1 | HDD Storage | MapReduce-based workloads, distributed file systems such as HDFS
R4 | Memory Optimized | memory intensive apps, DBs (R = RAM)
M5 | General purpose | Application Servers
C5 | Compute Optimized | CPU intensive apps (C=CPU)
G3 | Graphics Intensive | Video Encoding, 3D app. streaming (G=graphics)
I3 | High Speed Storage | NoSQL DBs, data warehousing (I=IOPS)
F1 | Field Programmable Gate Array | genomics,financial analytics,real-time video processing, big data (F=Field)
T2 | lowest cost general purpose | web servers, small DBs
P3 | graphics,general purpose GPU | machine learning, bit coins mining
X1 | Memory Optimized | SAP HANA, Apache SPARK (X=Xtreme Memory Optimized)
           
> Remember the acronym FIGHT Dr McPX

## Elastic Block Storage (EBS)
* allows you to create storage volumes and attach them to EC2 instances. Once attached you can create file systems on
top of these volumes, run a DB, or use them in any other way you would use a block device. EBS volumes are places in a
**specific availability zone (AZ)** where they are automatically replicated to protect you from the failure of a single
component
    * **these stay within an availability zone, they are not automatically replicated to different AZs**

### EBS Volume Types
* General Purpose SSD (GP2)
    * balance both price and performance
    * ratio of 3 IOPS per GB with up to 10,000 IOPS and the ability to burst up to 3000 IOPS for extended periods of
    time for volumes at 3334 GB and above
* Provisioned IOPS SSD (IO1)
    * for I/O intensive applications such as large relational/NoSQL DBs
    * **use these if you need more than 10,000 IOPS**
    * can provision up to 20,000 IOPS per volume
* Throughput Optimized HDD (ST1)
    * Big Data, Data Warehouses, Log Processing
    * Large amounts of sequential data
    * **cannot be a boot volume**
* Cold HDD (SC1)
    * lowest cost storage for infrequently accessed workloads
    * file server
    * **cannot be a boot volume**
* Magnetic (Standard)
    * lowest cost per GB of all EBS volume types that is bootable
    * ideal for workloads where data is accessed infrequently + applications where the lowest storage cost is important

#### Snapshots
* allow you to back up your EBS volumes by taking a point in time "snapshot"
* snapshots are **incremental** backups
    * only blocks on the device that have changed after your most recent snapshot are saved
* each snapshot contains all the information needed to restore your data to a new EBS volume



## Elastic Load Balancers (ELB)
* **Application Load Balancer**
    * best suited for balancing of HTTP and HTTPS traffic
    * operate at layer 7 and are application aware
    * they are intelligent and you can create advanced request routing, sending specified requests to specific web-servers
* **Network Load Balancer**
    * are best suited for load balancing of TCP traffic where extreme performance is required
    * operate at connection layer (Layer 4)
    * Capable of handling millions of requests per second while maintaining ultra-low latencies
    * will cost the most out of the ELBs
    * **use these for extreme performance**
* **Classic load Balancer**
    * the *legacy* load balancer (could be on the exam)
    * can balance HTTP/HTTPS applications and use Layer-7 specific features such as `X-Forwarded*` and sticky sessions
        * note that it is not as intelligent as the application load balancer
    * you can also use *strict layer 4* load balancing for applications that rely purely on the TCP protocol

### Load Balancer Errors
* classic load balancer
    * if your application stops responding, the classic load balancer responds with a **504 error (Gateway Timeout)**
    * this means that the application is having issues (not the LB) and the problem could be at the 
    DB Layer or Web/Application Server layer
    * identify where the application is failing and **scale it up** not out where possible

### X-Forwarded-For Header (supported by Classic Load Balancer)
* set by the LB, and contains the IPV4 address of your end user

## Route53
* Route53 is Amazon's DNS service
* Allows you to map domain names to:
    * EC2 Instances
    * Load Balancers
    * S3 Buckets

RDS 101 (Relational Database Service)
=====================================
* RDB types offered by AWS (these all support multi-AZ)
    * **SQL Server**
    * **ORACLE**
    * **MySQL**
    * **PostgreSQL**
    * **Amazon Aurora**
        * Amazon's Flagship RDB
        * fully compatible with MySQL
    * **Maria DB**
* RDS is most commonly used for OLTP (online transaction processing)
    * OLTP - very simple transaction storage and retrieval that happens very frequently
* Data Warehousing
    * used for business intelligence
    * used to pull in very large and complex data sets in order to do complex queries on data
    * OLTP vs OLAP
        * Online Transaction Processing differs from Online Analytics Processing in terms of the types of
        queries you will run
        * OLAP - ex: net profit for EMEA and Pacific for for a certain product, and want to know stuff like
        unit cost of the product in each region, sales figures per month/day/week
            * pulls in large # of records
            * much more complex type of processing is done
    * data warehousing databases will use different type of architecture both from a database perspective and infra-
    structure layer
    * *Redshift* is AWS's OLAP database offering


## RDS - Back-Ups, Multi-Availability Zones & Read replicas
### Back-ups
* two types of backups for AWS:
    * automated backups
    * database snapshots

#### Automated backups
* allow you to recover your database to any point in time within a *retention period*
* the retention period can be between one and 35 days
* will take a full daily snapshot and also store transaction logs throughout the day
* when you do a recovery, AWS will first choose the most recent daily back-up and then apply
transaction logs relevant to that day
    * this allows you to do a point in time recovery **down to a second**, within the retention period
* automated backups are enabled by default
* the data is stored in S3 and you get free storage space equal to the size of your database
* backups are taken within a defined window
    * during the backup window, storage I/O may be suspended while your data is being backed up 
    and **you may experience elevated latency**
* automated backups are deleted when you delete the database

#### Database snapshots
* these are done manually (user initiated)
* they are stored even after you delete the original RDS instance, unlike automated backups

#### Restoring Backups
* whenever you restore either and automated back-up or a manual snapshot, the restored version of the database
will be a **new RDS instance** with a **new DNS endpoint**

### Encryption
Encryption at rest is supported for:
* MYSQL
* Oracle
* SQL Server
* PostgreSQL
* MariaDB
* Aurora

* Encryption is done using the the AWS Key Management Store (KMS) service
* once your RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted **as are its
automated backups, read replicas, and snapshots**
* at present, **encrypting an existing DB instance is not supported**
    * to use encryption on an existing DB, you must:
        1. create a snapshot
        2. make a copy of that snapshot
        3. encrypt the copy
        
### Multi-AZ
* Allows you to have an exact copy of your production database in another AZ
* **AWS handles the replication for you**,  so when your production DB is written to, the writes are automatically
synchronized to the stand by database
* in the event of planned DB maintenance, DB instance failure, or an AZ failure, AWS RDS will automatically
failover to the standby so that DB operations can resume quickly without administrative intervention
* it is not on by default, you must manually enable it
* **Multi-AZ is for disaster recovery only, it is not for improving performance**
* **FOR PERFORMANCE IMPROVEMENTS, YOU NEED READ REPLICAS**

### Read Replicas
* allow you to have a **read-only** copy of your production database
* achieved via asynchronous replication from the primary RDS instance to the read replica
* read replicas are used primarily for very read-heavy DB workloads
* used for scaling NOT for disaster recovery
* must have automatic backups turned on in order to deploy a read replica
* 5 read replicas allowed per production DB (by default)
* you can have read replicas of read replicas (watch out for latency)
* each read replica will have its own DNS endpoint
* you **can** have read replicas that have Multi-AZ enabled
* you can create read replicas of Multi-AZ source databases
* read replicas can be promoted to be their own database. This breaks the replication
* you can have a read replica in a second region
* available for the following DBs:
    * MySQL
    * PostgreSQL
    * MariaDB
    * Aurora

## Elasticache
a Web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud. It improves the
performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches, instead
of relying entirely on slower disk-based databases.
* cached information may include the results of I/O intensive queries or the results of computationally intensive
calculations
* **ElasticCache supports memcached and redis caching engines**
    
### memcached
* a widely adopted memory object caching system
* ElastiCache is protocol compliant with Memcached
    * popular tools that you use today with existing memcached environments will work seamlessly

### redis
* popular open-source in-memory key value store that supports data structures such as sorted sets and lists
* Elasticache supports Master/Slave replication and Multi-AZ which can be used to achieve cross AZ
redundancy 

### difference between redis and memcached
* redis 
    * has replication and persistence features
    * elasticache manages redis more as a relational DB
    * redis elasticache clusters are managed as stateful entities that include failover, similar to how
    Amazon RDS manages DB failover
    * **if you need multi-AZ with failover, use redis**
    * **Do you need data-types, such as lists, hashes and sets? Use redis**
    * **Does sorting and ranking datasets in memory help you, such as with leaderboards? Use redis**
    * **Do you need Pub/Sub capabilities?** redis
    * **Do you need persistence of your key store? use redis**
* memcached
    * designed as a pure caching solution with **no persistence**
    * elasticache manages memcached nodes as a pool that can grow and shrink, similar to an EC2 autoscaling group
    * **individual nodes are expendable, and elasticache provides additional capabilities here**:
        * **automatic node replacement**
        * **auto-discovery**
    * **if you are NOT concerned about multi-AZ redundancy, use memcached**
    * **Is object caching your primary goal? Use memcached**
    * **Are you interested in as simple a caching model as possbile? memcached**
    * **Are you planning on running large cache nodes that require multi-threaded performance with utilization of
    multiple cores? memcached**
    * **Do you want the ability to scale your cache horizontally (scale-out) as you grow? memcached**


## EC2 Section Exam Tips
* know differences between On Demand, Spot, Reserved, Dedicated Hosts
* If AWS terminates the spot instance, you get the hour for free. If **you** terminate the instance, you will be
charged for the complete hour
* FIGHT DR McPX
* EBS Types
    * SSD
        * General Purpose SSD - balances price and performance for a wide variety of workloads
            * up to 10,000 IOPS
        * Provisioned IOPS SSD - highest performance SSD volume for mission critical low latency or high-throughput
        workloads
            * when you need > 10,000 IOPS
    * Magnetic
        * Throughput Optimized HDD - 
            * cannot be a boot volume 
            * low cost HDD volume designed for frequently accessed, throughput-intensive workloads
        * Cold HDD - 
            * cannot be a boot volume
            * lowest cost HDD volume designed for less frequently accessed workloads
        * Magnetic - previous generation. **can be a boot volume**
* EBS Encryption
    * volumes created from encrypted snapshots are automatically encrypted
    * volumes created from unencrypted snapshots are automatically unencrypted
    * How to encrypt an existing, unencrypted root volume
        * use a program like bitlocker to encrypt the volume
        * uses AWS console:
            1. create a snapshot of the existing volume
            2. copy the snapshot and tell AWS to encrypt the copy
            3. can then make an AMI of the snapshot and deploy the encrypted volume
    * you can encrypt additional attached volumes using the console, CLI, or API
* CLI Tips
    * Least Privilege - always give users the minimum amount of access required
    * Create Groups
        * assign your users to groups
        * users will automatically inherit the permissions of the group
        * group permissions are assigned using policy documents
    * Secret Access Key
        * you will see this only once
        * can be re-generated if you forget to save the secret access key
        * will need to run `aws configure` again
    * do not use just one access key
        * don't create one access and share it across developers
        * create one access key pair per developer (in case a developer leaves on bad terms)
* Roles
    * Assigning Roles to EC2 instances is preferred from a security perspective
    * roles are controlled by policies
    * changing a policy on a role will take effect immediately
    * you can attach/detach roles to a running EC2 instance (don't have to stop the instance)
* Elastic Load Balancers (ELB)
    * know the three types of Load Balancers
    * know the 504 error (Gateway Timeout) and how to troubleshoot (database or webserver...)
    * know about `X-Forwarded-For` header
* RDS
    * most commonly used for OLTP
    * supports the following DBs
        * SQL Server
        * ORACLE
        * MySQL
        * PostgreSQL
        * Amazon Aurora
            * Amazon's Flagship RDB
            * fully compatible with MySQL
        * Maria DB
     * if your ec2 instance can't connect to your RDS instance make sure the security group of the RDS
     instance is allowing inbound connections from the security group of the EC2 instance 
     on the (DB's port... ex. 3306 for MySQL)
* redshift - OLAP
* dynamodb - NoSQL
* elasticache - in memory caching
    * memcached
    * redis
* multi-AZ
    * **Multi-AZ is for disaster recovery only, it is not for improving performance**
* read-replicas
    * know everything in the read replica section above
* elasticache
    * typically given a scenario question where a particular database is under a lot of stress/load. You may be asked
    which service you should use to alleviate this (Hint: answer is usually elasticache)
    * Elasticache is a good choice if your DB is particularly read-heavy and NOT prone to frequent changing
    * **Redshift** is a good choice if the reason your database is feeling stress is because management is running OLAP
    transactions on it
    * KNOW the different USE CASES BETWEEN MEMCACHED and REDIS (see above)


S3
=====================================================================================================
Simple storage service provides secure, durable, highly-scalable object storage. The data is spread across multiple
devices and facilities. Read the [S3 FAQ](https://aws.amazon.com/s3/faqs)

## S3 Basics
* **S3 is object based (allows you to upload files)**
* **Files can be from 0 Bytes to 5TB**
* **there is unlimited storage, AWS will add more storage as needed**
* **files are stored in buckets**
* **S3 is a universal namespace, names must be unique globally**
* **S3 will automatically creates a DNS for your bucket**
    * https://s3[-us-east-1].amazonaws.com/yourbucketname
        * part in square brackets my no longer by needed
* **when you successfully upload an object, you'll receive a HTTP 200 code**
* **Built for 99.99% availability**
* **Built for 99.999999999% durability** (the eleven nines guarantee)
* Tiered storage is available
* Lifecycle management
* Versioning
* Encryption


## Data Consistency Model
* **read after write consistency for PUTS of new objects**
    * you'll be able to read the object immediately
* **Eventual consistency for overwrite PUTS and DELETES (can take time to propagate)**
* updates are **atomic** you'll either get the new data or else the old data

## S3 is a simple key/value store
(E) 
* **Key** - the name of the object
    * stored lexicographically
    * to improve performance you may want to add random letters to beginning of file name in order to spread your files
    across S3 storage - NOTE no longer true as of Oct. 2018, randomization no longer needed
* **Value** - the object data (a sequence of bytes)
* **VersionID**
* **Meta-Data**
* **Sub-resources**
    * access control list
    * ability to Torrent the file
    * Cross Origin Resource Sharing (CORS)
    * Transfer Acceleration - (when uploading lots of files into S3)

## S3 Storage Tiers/Classes
(E) 
* **S3** (aka S3 Standard)
    * 99.99% availability and 99.999999999% durability
    * designed to withstand loss of 2 facilities concurrently
    * minimum object size 0KB
* **S3-IA** (infrequently accessed)
    * for data accessed less frequently but requiring rapid access when needed
    * Lower fee than S3 but charged a retrieval fee
    * 99% Availability
    * 99.999999999% durability
    * minimum object size 128KB
* **S3 One Zone-IA** (S3 One Zone-Infrequent Access)
    * stores data in a single AZ
        * therefore not resilient to the loss of the AZ
    * 99.5% availability
    * 20% less cost than S3-IA
    * everything else the same as S3-IA
* **Reduced Redundancy Storage**
    * designed to provide **99.99% durability** and 99.99% availability of objects over a given year
    * useful for objects you can recreate (like thumbnail images)
    * lower costs than options above
* Glacier
    * very cheap but used for archival only
    * you're locked into, at minimum, 90 days of storage
    * objects over 250MB can take 3-5 hours to restore

## S3 Charges
* charged for:
    * storage per GB
    * Requests (Get, Put, Copy)
    * Storage Management Pricing
        * inventory, analytics, object tags
* Data Management Pricing
    * for data transferred out of S3
* Transfer Acceleration
    * use of CloudFront to optimize transfers

## S3 Security
* you control access to your buckets using:
    * **Access Control Lists (ACL)**
        * **can be applied at the object level**
        * A document that defines who can access a particular bucket or object. Each bucket and object in Amazon 
        S3 has an ACL. The document defines what each type of user can do, such as write and read permissions.
    * **Bucket Policies**
        * bucket policies **can only be applied at the bucket level**
        * A bucket policy is a resource-based AWS Identity and Access Management (IAM) policy that grants other AWS 
        accounts or IAM users access to an S3 bucket. Bucket policies supplement, and in many cases, replace 
        ACL-based access policies
* **by default, buckets and all objects stored in them are private**

### S3 Encryption
#### types of encryption
* **In Transit**:
    * using SSL/TLS
* **At Rest**:
    * **Server-Side encryption**
            * using Amazon S3 Managed Keys - **SSE-S3** (uses AES256)
            * using AWS Key Management Service - **S3-KMS**
            * using customer provided keys - **SSE-C**
    * **Client Side encryption**
        * data is encrypted by owner before storing on S3

#### enforcing encryption
* files are uploaded to S3 using a `PUT` request
    * if the file is to be encrypted at upload time, the header `x-amz-server-side-encryption` will be used
    * two options for this header:
        * `x-amz-server-side-encryption: AES256`  (SSE-SE S3 managed keys)
        * `x-amz-server-side-encryption: aws:kms` (SSE-KMS KMS Managed Keys)
* **you can enforce server side encryption by using a Bucket Policy which denies any S3 PUT request that doesn't
include the `x-amz-server-side-encryption` parameter** 

### Cross Origin Resource Sharing (CORS) (E) 
* way of allowing resource/code in one S3 bucket to access a resource in another S3 bucket
    * **by default resource in one bocket cannot access resources in another bucket**
* have to enable CORS in the **bucket that is being called** (the bucket containing the resource) and then configure
the URL for the origin that will be calling the bucket


## Static Website Hosting (E) 
* S3 can be used to host files for a **static** website
    * no PHP,asp,servlets, etc, allowed
* S3 will create a publicly available DNS for your site
    * http://**bucketname**.s3-website-**us-east-1**.amazonaws.com
        * NOTE that the static website names have *s3-website* in the name
* What is a good scenario for static website hosting?
    * a static site that needs to be scalable and handle lots of requests
        * like a site for a major blockbuster movie release

## CloudFront Overview
* AWS CloudFront is a Content Delivery Network (CDN):
    * a system of distributed servers that deliver webpages and other web content to a user based on the geographic 
    locations of the user, the origin of the webpage and a content delivery server

* (E) **Edge Location**
    * the location where content will be cached. This is separate to an AWS Region/AZ
    * **edge locations are not just Read Only, you can write to them too (the objects will be put into S3)**
    * **objects are cached for the life of the Time-To-Live (TTL)**
    * **you can force a clear of the cached objects, but you will be charged**
* (E) **Origin**
    * this is the origin of all files that the CDN will distribute. Cloudfront is optimized to work with:
        * **S3 bucket**
        * **EC2 instance**
        * **Elastic Load Balancer**
        * **Route53**
    * **origin can also be a non-AWS server** (e.g. a web-server you own on-premise)
* (E) **Distribution**
    * the name given to the CDN which consists of a collection of edge locations
        * you can have multiple origins
* **Web Distribution** - typically used for websites, HTTP/HTTPS
* **RTMP** - Adobe Real Time Messaging Protocol
    * used for media streaming, i.e Adobe Flash


### CloudFront and S3 Transfer Acceleration
* **Utilizes CloudFront edge network to accelerate your uploads to S3** 
* Instead of uploading directly to an S3 bucket, you can use a distinct URL to upload to an edge location, 
which will in turn transfer to S3. 
* Remember **You get a distinct URL for uploading to your bucket**
    * **always us the S3 website URL to access bucket resources when running a website on S3** 
        * http://mybucketname.**s3-website**.us-east-1.amazonaws.com


### Creating a CDN (lab)  (E) 
* know that you can configure the Default TTL (it *defaults to 24 hours*), you can change it if you have content that
needs to expire quickly
* you can restrict use access to content using *Signed URLs* or *Signed Cookies*
* Geographic Restrictions
    * you can whitelist and blacklist geographic locations (by country) so that they can/cannot view your content
* Invalidations
    * you manually force removal of objects from the CloudFront edge caches
        * **you will be charged for doing this**
    * scenario: you need to immediately remove something from the cache and can't wait for the TTL to expire it

## S3 Performance Optimization
* S3 is designed to support very high request rates
    * if your bucket receives >100 PUT/LIST/DELETE or >300 GET requests per second, then there are some
    best practice guidelines that will help optimize S3 performance
    * **GET-Intensive Workloads**
        * **you should use CloudFront to get best performance**
            * it will Cache your most frequently accessed objects and will reduce latency for GETs
    

## S3 Section Notes
* S3 website URL: http://mybucketname.**s3-website**.us-east-1.amazonaws.com
* direct S3 bucket: http://s3.[us-east-1].amazonaws.com/mybucketname
* You can upload big files to S3 using multi-part upload
    * from 5MB to 5TB in size
* You can upload up to **5GB** in a **single operation**
* can create 100 buckets per AWS account (default)
* Bucket Names
    * at least 3 characters long, but no more than 63
    * Bucket names must be a series of one or more labels. 
        * Adjacent labels are separated by a single period (.)
        * Bucket names can contain lowercase letters, numbers, and hyphens
        * Each label must start and end with a lowercase letter or a number
    * bucket names CANNOT be ip addresses (ie. 192.123.45.11)



Lambda
============================================================================================================
AWS Lambda is a compute service where you can upload your code and create a Lambda function. AWS Lambda takes
care of provisioning and managing the servers that you use to run code. You don't have to worry about operating
systems, patching, scaling etc.


* You can use Lambda in the following ways:
    * as an event-driven compute service where Lambda runs your code in response to events
        * events could be changes to data in S3 buckets or a DynamoDB table
    * as a compute service to run your code in response to HTTP requests using API Gateway or API calls made
    using AWS SDKs
    
* Lambdas can run code using the following runtimes
    * **Node.js**
    * **Java**
    * **Python**
    * **C#**
    * **Go**
    * support for PowerShell recently added

## Lambda Pricing
* Number of requests
    * first 1 million requests are free (per month)
    * $0.20 per 1 million requests thereafter
* Duration
    * duration is calculated from the time your code begins executing until it returns or otherwise terminates
    rounded up the nearest *100ms*
    * the price depends on the amount of memory you allocate to your function
        * you are charged $0.00001667 for every GB-second used

* Lambda functions and event sources are the core components of AWS Lambda
* Event sources (are usually) the AWS services that can be configured to invoke a lambda function
    * this configuration is called the *event source mapping*. It enables the automatic invocation of your lambda
    function when events occur


## Supported Event Sources (aka Triggers)
* For poll-based sources, the **lambda function maintains the event source mapping**
* For all others, the event source mapping is maintained by the source


| Event Source | (A)Synchronous | Notes |
|:------------:|:--------------:|:------|
| DynamoDB | Sync | **polled by lambda**,streams must be enabled |
| Kinesis Data Streams | Sync | **polled by lambda** once per second |
| Simple Queue Service | Sync | **polled by lambda**, time affected by `VisibilityTimeout` and `TimeToWait` |
| Cognito | Sync  |
| Amazon Alexa | Sync  | build lambdas to give new skills to Alexa |
| Amazon Lex | Sync | uses lambda to perform init., validation, fulfillment
| API Gateway | Sync |
| Kinesis Data Firehose | Sync | lambda can process a stream before it is sent downstream
| Simple Notification Service | Async | |
| S3 | Async | 
| Simple Email Service | Async | SES message in SNS event |
| CloudFormation | Async | CloudFormation message in SNS Event
| CloudWatch Logs | Async |
| CloudWatch Events | Async | can create rules to send events to lambda |
| CodeCommit | Async | repository events can trigger lambda |
| Scheduled Events | Async | powered by CloudWatch Events |
| AWS Config | Async |
| AWS IoT Button | Async | relies on Lambda to perform the button click operation(s)
| CloudFront | Async | uses Lambda@Edge to change cloudfront requests and responses |
| On Demand | Sync or Async | you invoke the lambda (from cli using `invoke` or programmatically)


## Versioning with Lambda
When you use [versioning](https://docs.aws.amazon.com/lambda/latest/dg/versioning-intro.html) in AWS Lambda 
you can publish one or more versions of your Lambda function. As a result you can work with different 
variations of your Lambda in your development workflow, such as dev, beta, prod etc..

* each Lamda function *version* has a unique ARN
* after you publish a version, it is immutable, you cannot edit the new (published) version
* your latest function code will be in the `$LATEST` version.
* when you update your function code, Lambda replaces the code in the `$LATEST` version of the Lambda function

#### Qualified/Unqualified ARNs
* You can refer to the LATEST function version using its ARN
    * there are two ARNs associated with this initial version:
        * **Qualified ARN**
            * the function ARN *WITH* the version suffix
                * ex:  `arn:aws:lambda:aws-region:acct-id:function:helloworld:$LATEST`
        * **Unqualified ARN**
            * the function ARN *WITHOUT* the version suffix
                * ex: `arn:aws:lambda:aws-region:acct-id:function:helloworld`

#### ALias(es)
* an **alias** is just a name that points to a particular version of your function
* For example:
    1. After initially creating a Lambda function (the $LATEST version), you can publish a version 1 of it. 
    If you were to create an alias named "PROD" that points to version 1, you can now use the "PROD" alias to invoke 
    version 1 of the Lambda function.
    2. Now, you can update the code (the $LATEST version) with all of your improvements and then publish another stable 
    and improved version (version 2). You can promote version 2 to production be remapping the PROD alias so that it
    points to version 2. If you find something wrong, you can easily roll back the production version to version 1
    by remapping the PROD alias so that it points to version 1

## Lambda Exam Tips
* Lambda scales out (not up) automatically
* Lambda functions are independent, 1 event = 1 function
* Lambda is serverless
* Lambda functions can trigger other lambda functions
    * 1 event can = X functions (if functions trigger other functions)
* Architectures can get extremely complicated
    * AWS X-Ray allows you to debug what is happening
* Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc..
* know the lambda triggers (see the Supported event sources section below)
* Versioning Tips
    * can have multiple versions of lambda functions
    * latest version will use `$LATEST`
    * qualified version ARN will have "$LATEST" in it, unqualified will not have it
    * versions are immutable (cannot be changed)
    * **can split traffic using aliases to different versions**
        * **cannot split traffic using $latest, instead create an alias to $latest**
     

## Limits
* Memory
    * **128MB** min to **3008MB** max (in 64MB increments)
* ephemeral disk space in /tmp **512MB**
* number of processes and threads combined **1024**
* max execution duration **15 minutes** (used to be 5 min)
* Synchronous (RequestResponse) invoke request body payload size: **6MB**
* Asynchronous (Event) invoke request **128MB**
* Deployment package size (compressed .zip/.jar) **50MB**
* Size of code dependencies  (uncompressed zip/jar) **250MB**
* Environment variables size (total) **4KB**
* Total size of **all** deployment packages per region **75GB**

### Concurrency Limits
* Account Level limits per Region:
    * **1000** concurrent executions by default
    * can be increased by opening a case in AWS Support Center
    * If there is a sudden burst in traffic, AWS will increase your concurrently executing functions by a 
    predetermined amount that **varies per region**
        * if that burst limit is reached:
            * AWS will increase lambda function executions by **500 per minute** until either:
                * the account safety limit is reached
                * the number of concurrently executing functions is sufficient to process the increased load
* Function Level limits 
    * set per function
        * set in the console or via aws cli (`PutFunctionConcurrency`)
        * Use the `GetFunction` cli command to view concurrency limits for a specific function 
    * count towards the account level limits
    * By setting a concurrency limit on a function, Lambda guarantees that allocation will be applied 
    specifically to that function, regardless of the amount of traffic processing remaining functions
    * once concurrency limit is reached for a function, the function is throttled


## Scaling Behaviour
### Estimating Concurrent invocations
* In general, to estimate concurrent invocations
    * `events (or requests) per second * function duration`
    * ex. S3 lambda function takes 3 secs to process an event. S3 publishes 10 events per seconds = 30 concurrent executions
* For Poll based sources that are stream based:
    * the number of shards is the unit of concurrency
        * 100 active shards = at most 100 lambda functions running concurrently. This is because Lambda processes 
        each shard’s events in sequence.
* Poll-based event sources that are not stream-based:
    * SQS Queues:
        * lambda will scale polling on the queue until maximum concurrency level is reached
            * either 1000, the account concurrency limit, or (optional) function limit, whichever is lower
        * each message batch can be considered a single concurrent unit
        * Lambda uses long-polling (to reduce cost)
        * SQS supports an initial burst of 5 concurrent function invocations per minute, and increases it 
        by **60** concurrent invocations per minute

### Request Rate 
rate at which your lambda is invoked
* For all services except stream based, the `request rate = rate at which event source generates the events`
* For stream-based services:
    * `request rate = number of concurrent executions / function duration`
    * ex. 5 active shards on a stream (i.e. 5 active lambda functions running in parallel), and your lambda takes
    2 seconds to run, request rate = 2.5 requests per second
    
### Throttling behavior
* Non-stream based event sources
    * Synchronous invocations:
        * function returns **429** error and the **invoking service is responsible for retries**
        * the `ThrottleReason` error code explains if there was a function level throttle or account level throttle
        * each service may have its own retry policy
    * Asynchronous invocation:
        * aws will automatically retry the throttled event for up to **six hours**, with delays between retries
        * you can configure a Dead Letter Queue to investigate why your function was throttled
* Poll based sources, stream-based (such as kinesis, dynamodb)
    * lamda will attempt to process the throttled batch of records until the time the data expires
        * can be up to **7 days** for kinesis streams
    * the throttled request is treated as blocking per shard, Lambda doesn't read any new records from the shard
    until the throttled batch of records either expires or succeeds
    * if there is more than one shard in the stream, lambda continues invoking on the non-throttled shards until
    one gets through
* Poll based event source, not stream-based (such as SQS)
    * lambda will attempt to process the throttled batch of records until it is successfully invoked (in which case
    it is automatically deleted from the queue) or until the `MessageRetentionPeriod` set for the queue expires


API Gateway
=====================================================================================================
Amazon API Gateway is a fully managed service that makes it easy for developers to publish, maintain, monitor
and secure APIs at any scale. With a few clicks in the AWS Management Console, you can create an API that acts
as a "front door" for applications to access data, business logic, or functionality from your back-end
services, such as applications running on EC2, code running on AWS Lambda, or any web application

## What can API Gateway Do?
* expose HTTPS endpoints to define a RESTful API
* "serverlessly" connect to services like Lambda & DynamoDB 
    * and HTTP endpoints running on EC2 or somewhere else on the internet
* Send each API endpoint to a different target
    * one to lambda, one to dynamodb, etc...
* run efficiently with low cost
* scales effortlessly
* track and control usage using an API Key
* throttle requests to prevent attacks
* connect to CloudWatch to log all requests for monitoring
* maintain multiple versions of your API

## How do you configure API Gateway?
* Define an API
* Define resources and nested resources (URL Paths)
* For each resource:
    * select supported HTTP methods (verbs... GET POST DELETE PUT OPTIONS etc...)
    * set security (if needed)
    * choose target (such as EC2, Lambda, DynamoDB, etc..)
    * Set request and response transformations
* Deploy API to a *Stage*
    * uses API Gateway domain, by default
    * you can have a "prod" stage, "dev" stage, "test" stage, "v1" etc...
    * you can use a custom domain
    * supports AWS Certificate Manager (free SSL/TLS certs!)

## What is API Caching?
You can enable **API Caching** in API Gateway to cache your endpoint's response. With caching, you can reduce
the number of calls made to your endpoint and also improve the latency of requests to your API. When you enable
caching for a stage, API Gateway caches responses from your endpoint for a specified (TTL) period, in seconds.
API Gateway then responds to the request by looking up the endpoint response from the cache instead of making
a request to your endpoint.    
    
## Same Origin Policy
In computing, the **same-origin policy** is an important concept in the web application security model. Under
the policy, a web browser permits **scripts** contained in a first web page to access data in a second web page,
but only if both web pages have the same origin.

This is done to prevent *Cross-Site Scripting (XSS)* attacks
* enforced by web browsers
* ignored by tools such as Postman and Curl

## (E) Cross-Origin Resource Sharing (CORS)
**Cross-Origin Resource Sharing (CORS)** is one way the server at the other end (not the client code in the
browser) can relax the same-origin policy. CORS is a mechanism that allows restricted resources (e.g. fonts)
on a web page to be requested from another domain outside the domain from which the first resource was served.

1. Browser makes an HTTP OPTIONS call for a URL
2. Server returns a response that says:
    * "These other domains are approved to GET this URL"

* If you ever get this Error - "Origin policy cannot be read at the remote resource?"
    * you need to enable CORS on API Gateway

## Exam tips
* remember what API Gateway is at a high level
* API Gateway has caching capabilities to increase performance
* API Gateway is low cost and scales automatically
* you can throttle API gateway to prevent attacks
* you can log results to CloudWatch
* If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled
CORS on API Gateway
* CORS is *enforced* by the client (usually a browser)

CloudFormation
=====================================================================================================
* CloudFormation allows you to take what was once traditional hardware infrastructure and convert it into **code**
* CloudFormation gives developers and system administrators an easy way to create and manage a collection of related
AWS resources, provisioning and updating them in an orderly and predictable fashion
* You don't need to figure out the order for provisioning AWS services or the subtleties of making those dependencies
work. CloudFormation takes care of it for you
* After your AWS resources are deployed, you can modify and update them in a controlled and predictable way, in
effect applying version control to your AWS infrastructure the same way you do with your software

## CloudFormation Stack vs. Template
* A CloudFormation Template is essentially an architectural diagram
* A CloudFormation Stack is the end result of that diagram (i.e. what is actually provisioned)
* You create,update, and delete a collection of resources by creating,updating and deleting stacks using CloudFormation
Templates
* CloudFormation templates are in JSON format or YAML

## Elements of a Template
* Mandatory Elements
    * List of AWS resources and their associated configuration values
* Optional Elements
    * The template's file format and version number
    * Template parameters
        * the input values that are supplied at stack creation time. Limit of 60
    * Output Values
        * The output values required once a stack has finished building (such as the public IP address, ELB address
        ,etc.) Limit of 60
    * List of data tables
        * used to look up static configuration values such as AMI's etc...

## A Simple Template
```json
{
  "Resources" : { 
    "HelloBucket" : {
        "Type":"AWS::S3::Bucket"
    } 
  }
}
```

## Outputting Data
* (E) **You can use ```Fn:GetAtt``` to output data**
    * prints the values of the resources that have been configured by CloudFormation

```json
{ "PublicIP" : {
    "Description":"Public IP address of the web server", 
    "Value" : { 
        "Fn:GetAtt" : [
            "WebServerHost", "PublicIp"
        ]
    }
 }
}
```

## Exam Tips
* CloudFormation *automatic rollback on error* is enabled by default
    * if stack fails to start-up, it will automatically roll-back and delete any resources it created
* You are charged for errors (e.g. any EC2 instances you accidentally spin up...)
* CloudFormation itself is free (the resources it creates are not)
* **Stacks can wait for applications to be provisioned using the ```WaitCondition```**
    * e.g. you need to wait for some resource to be created first, before moving on
* You can use ```Fn:GetAtt``` to output data
* You can use the `Ref` function to refer to an identifying property of a resource.
    * Frequently, this is the physical name of the resource; however, sometimes it can be an identifier, 
    such as the IP address for an AWS::EC2::EIP resource or an Amazon Resource Name (ARN) for an Amazon SNS topic
* Route53 is completely supported. This includes creating **new** hosted zones or **updating** existing ones
* You can create A-Records, Aliases etc...
* IAM Role Creation and Assignment is also supported
* May get scenario question on exam comparing ElasticBeanstalk to CloudFormation



Elastic Beanstalk
=======================================================================================
## Overview
* With Elastic Beanstalk you can deploy, monitor and scale an application quickly
* It provides developers or end users with the ability to provision application infrastructure in an almost
transparent way
* Elastic Beanstalk enables developers to just "upload their code" and have Elastic Beanstalk provision the underlying
resources underneath
* It has a highly abstract focus towards infrastructure, focusing on components and performance, not configuration and
specifications
* It attempts to remove or significantly simplify infrastructure management, allowing applications to be deployed into
infrastructure environments easily
* AWS invented this so developers that didn't have much AWS experience would start deploying their applications to AWS

## Beanstalk key architecture components
* Applications are the high level structure in beanstalk
* Either your entire application is one EB application OR
    * each logical component of your application can be a EB application or a EB environment within an application

* Applications can have multiple environments (Prod, Staging, Dev, V1,V2,etc) or functional type (front-end, back-end)
* Environments are either single instances or scalable
* Environments are either web server environments or worker environments

* application versions are unique packages which represent versions of your apps.
* each application can have many versions (1:M relationship)
* application versions can be deployed to environments within an application

## Available Platforms on Elastic Beanstalk
(E)
* Preconfigured
    * PHP
    * Java
    * Tomcat
    * Node.js
    * Ruby
    * .NET
    * Python
    * Go
    * Packer
* Preconfigured - Docker
    * Glassfish
    * Go
    * Python
* Generic
    * Docker
    * Docker multi-container

### Custom Platform
You may also create your own custom platform using *Packer*
    
## Using Elastic Beanstalk with a Database
* Two options
    * have EB create and provision a RDS DB for you
        * you can then configure it to delete the DB upon application termination, 
        * or configure it to take a snapshot of your DB and store it on S3 upon application termination 
    * use an existing RDS database
        * you create this yourself and then configure EB to use it

## Exam tips
* You can have multiple versions of your applications
* Your applications can be split into tiers (Web Tier, Application Tier, Database Tier)
* You can update your application
* You can do application updates as well as configuration updates
    * Deployment policies
        * All at once, Rolling, Rolling with additional batch, and Immutable
* You pay for the AWS resources EB configures for you (EB itself is free)


Cognito
=======================================================================
## User Pools
* use user pools when you have:
    * users who want to create an account
    * users who have an existing corporate or social(facebook,google,etc.) account
* use user pools when you want:
    * a managed scalable and secure user directory
    * user authentication
    * user profiles
    * OpenID connect id token, access token, and refresh token to authenticate/authorize against your backend service
    * OAuth 2.0 flows for your app to authenticate with User Pool
    * OAuth 2.0 and SAML2 redirect/POST bindings to authenticate with identity providers
    * integration with API Gateway

## Identity Pools
* use identity pools if you have:
    * a user that has already authenticated with a social or corporate identity provider and has a token
    * a user that is unauthenticated
* use identity pools if you want:
    * scoped time-bound AWS credentials for that identity
    * direct access to AWS services from your web or mobile app
    * role mapping

