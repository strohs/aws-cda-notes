AWS Certified Developer - Associate (June 2018)
===================================================================================================================
[AWS Certified Developer](https://aws.amazon.com/certification/certified-developer-associate/)
## Exam Blueprint
1. Deployment 22%
2. Security 26%
3. Development with AWS Services 30%
4. Refactoring 10%
5. Monitoring and Troubleshooting 12%

IAM 101
====================================================================================================================
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
====================================================================================================================
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


Elasticache
====================================================================================================================
* a Web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud
* It improves the performance of web applications by allowing you to retrieve information from fast, managed, 
in-memory caches, instead of relying entirely on slower disk-based databases
* cached information may include the results of I/O intensive queries or the results of computationally intensive
calculations
* helps to take load off of your Database
* can be used in front of a RDS or DynamoDB 
* good for **read-heavy** databases where data is not changing too frequently
    * e.g. social networking, gaming media sharing, Q&A portals
* good for compute heavy workloads
    * e.g. recommendation engines
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
    * **individual nodes are expendable, and elasticache provides additional capabilities here**
        * **automatic node replacement**
        * **auto-discovery**
    * **if you are NOT concerned about multi-AZ redundancy, use memcached**
    * **Is object caching your primary goal? Use memcached**
    * **Are you interested in as simple a caching model as possbile? memcached**
    * **Are you planning on running large cache nodes that require multi-threaded performance with utilization of
    multiple cores? memcached**
    * **Do you want the ability to scale your cache horizontally (scale-out) as you grow? memcached**

### Caching Strategies
* 2 Strategies available when using elasticache (applies to both Redis and Memcached) 
    * **Lazy Loading**
    * **Write-Through**
    
#### Lazy Loading
* loads data into the cache only when necessary
* if requested data is in the cache, Elasticache returns the data to the application
* if the data is not in the cache or has expired, Elasticache returns a `null`
* your application then fetches the data from the database and writes the data received into the cache so that
it is available next time
* Advantages:
    * only requested data is cached
        * avoids filling up cached with useless data
    * Node failures are not fatal
        * a new empty node will just have a lot of cache misses initially
* Disadvantages
    * cache miss penalty:
        * initial request (to elasticache)
        * then a query to database
        * then writing of data to the cache
    * Stale Data
        * if data is only updated when there is a cache miss, it can become stale
        * Doesn't automatically update if the data in the DB changes
* TTL (Time to Live)
    * specifies the number of seconds until the key data expires to avoid keeping stale data in the cache
    * lazy loading treats an expired key as a cache miss and causes the application to retrieve the data from the
    database and subsequently write the data into the cache with a new TTL
    * does not eliminate stale data, but helps to avoid it

#### Write Through (to the cache)
* **Write Through** adds or updates data to the cache whenever data is written to the database
* Advantages
    * data in the cache is never stale
    * users are generally more tolerant of additional latency when updating data rather than retrieving it
* Disadvantages
    * write penalty
        * every write involves a write to the cache and the database
    * if a node fails and a new one is spun up, data is missing until added or updated in the database
        * mitigate this by implementing Lazy Loading in conjunction with write-through
    * wasted resources if most of the data is never read

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
    * elasticache sits between your application and database
    * 2 different caching strategies
        * Lazy Loading
        * Write Through
    * lazy loading only caches data when it is requested
        * elasticache node failures are not fatal, just lots of cache misses initially (with lazy loading)
        * lazy loading cache miss penalty:
            1. initial request (cache is missing data)
            2. query database (to get data)
            3. write data to cache
        * avoid stale data by implementing a TTL
    * write through strategy writes data into the cache whenever there is a change to the database
        * data is never stale
        * there is a write penalty
            * each write to DB involves a write to the cache
            * elasticache node failure means that data is missing until added or updated in the database
            * wasted resources if most of the data is never used


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
* **In Transit**
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
    * **by default resource in one bucket cannot access resources in another bucket**
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

* each Lambda function *version* has a unique ARN
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
    * BUT...each lambda function resides in a specific region
* know the lambda triggers (see the Supported event sources section below)
* you can increase a lambdas compute power:
    * the CPU time-share for a Lambda is proportional to the Lambdas RAM
* ephemeral disk space in /tmp **512MB**
* Lambda Versioning Tips
    * can have multiple versions of lambda functions
    * latest version will use `$LATEST`
    * qualified version ARN will have "$LATEST" in it, unqualified will not have it
    * versions are immutable (cannot be changed)
    * **can split traffic using aliases to different versions (only two way split)**
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


Step Functions
====================================================================================================
This is a separate [AWS Service](https://aws.amazon.com/step-functions/). In a nutshell it allows you
to combine different AWS services into a serverless workflow. The output of one workflow becomes the input to 
another. Your workflow is translated into a state machine by Step Functions, plus it also provides a
visualization of your workflow.

* **Step functions allows you to visualize and test your serverless applications**
* Step functions provides a graphical console to arrange and visualize the components of your application 
as a series of steps (or states)
    * this makes it simple to build and run multistep applications.
* **Step functions automatically triggers and tracks each step**, and retries when there are errors, so your
application executes in order and as expected
* **Step functions logs the state of each step, so when things do go wrong, you can diagnose and debug what went 
wrong and where**

## Examp tips
* **for the exam, you just need to know what step functions are** (know the bolded items above)


AWS X-Ray
=====================================================================================================
* AWS X-Ray is a service (for developers) that collects data about your requests that your application serves, and provides
tools that you can use to view, filter, and gain insights into that data to identify issues and opportunities
for optimization
* For any traced request to your application, you can see detailed information not only about
the request and response, but also about calls that your application makes to downstream AWS resources,
micro services, databases and web APIs
* in short, x-ray is a way to analyze your serverless application and find out where performance bottlenecks are and
can also help to debug your serverless application

## X-Ray Architecture
* you use the *X-Ray SDK* within your application to send trace data (as JSON) to the X-Ray Daemon
* the X-Ray Daemon runs locally on your development machine (or in an EC2 instance) and listens for UDP traffic
    * the daemon batch sends the JSON trace data generated by the X-Ray SDK to AWS X-Ray
* AWS X-ray will take all the data sent to it and create a detailed *service graph*, basically a 
visualization of your application in the X-Ray console
* you use the service graph to identify bottlenecks, latency spikes, and other issues to solve in order to
improve performance of your application

## X-Ray SDK
The X-Ray SDK provides:
* **interceptors to add to your code to trace incoming HTTP requests**
* **client handlers to instrument AWS SDK clients that your application uses to call other AWS Services**
* **an HTTP client to use to instrument calls to other internal and external HTTP web services**

## X-Ray Integration
X-Ray integrates with the following AWS services:
* **Elastic Load Balancing**
* **AWS Lambda**
* **Amazon API Gateway**
* **AWS Elastic Beanstalk**
* **Amazon Elastic Compute Cloud**
    * you'll have to run a user data script in order to install the X-Ray Daemon on the instance

## X-Ray Supported Languages (basically languages supported by lambda)
* **Java**
* **Go**
* **Node.js**
* **Python**
* **Ruby**
* **.Net**

## X-Ray Exam Tips
* understand what x-ray is (and does)
* know the bolded items above



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
    

## Advanced API Gateway
* you can use the API Gateway Import API Feature to import an API from an external file into API Gateway
    * currently only **Swagger v2.0** definition files are supported
* With the import API, you can either:
    * create a new API by submitting a POST request that includes a Swagger definition in the payload 
    and endpoint configuration
    * or you can update an existing API by using a PUT request that contains a swagger definition in the 
    payload
* You can update an API by overwriting it with a new definition
    * or merge a definition with an existing API
        * You specify the options using a mode query parameter in the request URL
        
### API Throttling
* by default, API Gateway limits the steady-state request rate to **10,000 requests per second**
* the **maximum CONCURRENT requests is 5000 requests across all APIs within an AWS account**
* if you go over 10,000 requests per second or 5000 concurrent requests you will receive a **429 Too Many
Request** error response

#### Throttling Examples
* if a caller submits 10,000 requests in a one second period evenly (for example, 10 requests every
millisecond), API Gateway processes all requests without dropping any.
* If the caller sends 10,000 requests in the first millisecond, API Gateway serves 5,000 of those
requests and throttles the rest in the one-second period
* If the caller submits 5,000 requests in the first millisecond and then evenly spreads another 5,000 
requests through the remaining 999 milliseconds (for example, about 5 requests every millisecond), API
Gateway processes all 10,000 requests in the one second period without returning `429 Too Many Requests`
error responses

### SOAP web service passthrough
* you can configure API gateway as a SOAP web service passthrough

## Exam tips
* know what API Gateway is at a high level
* API Gateway has caching capabilities to increase performance
* API Gateway is low cost and scales automatically
* you can throttle API gateway to prevent attacks
* you can log results to CloudWatch
* If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled
CORS on API Gateway
* CORS is *enforced* by the client (usually a browser)
* know that it is possible for API gateway to be a SOAP webservice passthrough
* import API's using Swagger 2.0 definition files
* API Gateway can be throttled
    * default limits are 10,000 RPS or 5000 concurrent requests
        * the limits can be increased upon request to the AWS Support Center



Dynamo DB
=====================================================================================================================
A fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency
 at any scale. It is a fully managed DB and supports both document and key-value data models. Its flexible data
 model and reliable performance make it a great fit for mobile, gaming, web etc...

## DynamoDB 101
* stored on SSDs
* spread across three geographically distinct data centres
* choice of 2 consistency models
    * **Eventually Consistent Reads** (default) 
        * best read response
        * The response might include some stale data
        * consistency across all copies of data is usually reached within a second
        * repeating a read after a short time should return the updated data
    * **Strongly Consistent Reads**
        * slower read performance
        * the result returned by dynamoDB will have the most up-to-date data
            * the result will have all writes that received a successful response prior to the read
* (E) you can export a DynamoDB table (or selected items) to a .CSV file
* can update your DynamoDB table capacity on the fly
    * *push button capacity scaling*
* DynamoDB supports nested attributes up to 32 levels deep.
* supports key-value and document data structures
* documents can be written in JSON, HTML or XML
* authentication and access control to DynamoDB is managed via AWS IAM
    * you can create an IAM role that enables you to obtain temp. access credentials to DynamoDB
    * **you can also use a special *IAM Condition* to restrict user access to only their own records**

## DynamoDB Data Model
* Tables
    * a collection of Items
* Items
    * a collection of Attributes
    * like a row of data in a RDB
    * aggregate size of an item, including all the attribute names and attribute values, **cannot exceed 400KB**
* Attributes
    * like columns of data in a RDB
            

## DynamoDB Key Types
* Primary Keys (two types)
    * Single Attribute key
        * consists of a unique ID
        * **Partition Key** (most common key type)
            * a hash key composed of **one attribute**
            * DynamoDB uses the partition keys value as input to a hash function. The output of the hash function
            determines the partition on which data is to be stored
            * **no two items in a table can have the same partition key value!**
    * Composite Primary key
        * composed of two attributes:
            * **Partition Key + Sort Key**
                * a hash and a range 
                * uses the partition key to determine the partition location
                    * two items can have the same partition key; however, **they must have a different sort key**
                * **all items with same partition key are stored together, in sorted order, by sort key value**

## IAM Conditions Example
* Imagine a gaming app where users should only be able to access their own data from DynamoDB
* this can be done by adding a **Condition** to an IAM Policy to allow access only to items where the **Partition 
key value matches their userId**
* example IAM Policy using a condition statement:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAccessToOnlyItemsMatchingUserID",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetItem",
                "dynamodb:BatchGetItem",
                "dynamodb:Query",
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem",
                "dynamodb:BatchWriteItem"
            ],
            "Resource": [
                "arn:aws:dynamodb:us-west-2:123456789012:table/GameScores"
            ],
            "Condition": {
                "ForAllValues:StringEquals": {
                    "dynamodb:LeadingKeys": [        <-- indicates the Partition key must match the userId below
                        "${www.amazon.com:user_id}"  <-- a substitution variable that will contain the userId
                    ],
                    "dynamodb:Attributes": [ <-- limits the users access to the specified attributes
                        "UserId",
                        "GameTitle",
                        "Wins",
                        "Losses",
                        "TopScore",
                        "TopScoreDateTime"
                    ]
                },
                "StringEqualsIfExists": {
                    "dynamodb:Select": "SPECIFIC_ATTRIBUTES" <--ensures that the calling app must always provide a
                }                                            <-- list of specific attributes. It will not allow ALL
            }                                                <-- attributes
        }
    ]
}
```

## DynamoDB Indexes
Many applications might benefit from having one or more **secondary** (or alternate) keys available to allow efficient 
access to data with attributes other than the primary key. To address this, you can create one or more secondary 
indexes on a table. Dynamo Supports two types:
* (E) **Local Secondary Index** (LSI)
    * has the **same partition key as the table but different sort key**
    * can **only be created when creating a table**
    * cannot be removed or modified after table creation
    * can create a maximum of 5 local secondary indexes per table
    * All scalar data types (Number, String, Binary) can be used for the sort key element of the local secondary 
    index key
    * support Strongly Consistent Reads and Eventually Consistent Reads
    * LSIs limit the total size of all elements (tables and indexes) to **10 GB per partition key value**
* (E) **Global Secondary Index** (GSI)
    * an index with a partition or a partition-and-sort key that can be **different** from those already on the table
    * can be created at table creation time or added later
    * can create a maximum of 5 global secondary indexes per table
    * GSIs do not enforce data co-location, or a maximum partition size (unlike LSIs)
    * GSIs have their own provisioned throughput (you must configure this)
    * GSIs only support eventually consistent reads


## DynamoDB Streams
* captures any kind of modification of the DynamoDB tables
    * if a new item is added to the table, the stream captures an image of the entire item, including all its attributes
    * if an item is updated, the stream captures the *before* and *after* image of any attributes that were modified
    in the item
    * if any item is deleted from the table, the stream captures an image of the entire item before it was deleted
* data is stored in DynamoDB streams for **24 hours**
    * you can process the data during this time using e.g. Lambda


## DynamoDB Query vs Scan
### Queries
* (E) a query operation finds items in a table using only primary key attribute values. 
    * **You must provide a partition attribute name and distinct value to search for** 
        * i.e. using equals... primaryKey = <SOME_VALUE>
    * you can optionally provide a sort key attribute name and value and use a comparison operator to refine 
    search results i.e. <,>,<=,>,=,BETWEEN, begins_with()
* (E) By default, a query returns all of the data attributes for items with the specified primary keys; however, you can
use `ProjectionExpression` parameter so that the query only returns some of the attributes rather than all of them
* (E) Query results are always sorted by the sort key
    * If the data type of the sort key is a number, the results are returned in numeric order; otherwise, the 
    results are returned in order of the ASCII character code values
    * By default the sort order is ascending, **to reverse the order of a Query, set the `ScanIndexForward` 
    parameter to false**
* **by default, queries are always eventually consistent** but can be changed to strongly consistent
    * in java we use: `.withConsistentRead(true)`

### Scans
* (E) **a scan operation examines every item in the table** and returns all of the data attributes for every item
    * However, you can use the `ProjectionExpression` parameter so that the scan only returns some of the 
    attributes, rather than all of them
* you can filter the results of Scan, after the Scan has run i.e.(note that your whole table is still scanned)

### What to use? Query vs Scan?
* Generally a query is more efficient than a Scan
* A Scan always scans the entire table then filters out values to provide the desired result, eventually adding the
extra step of removing data from the result set
    * Avoiding using scan operation on a large table with a filter that removes many results, if possible
    * Also, as a table grows, the scan slows. The scan operation examines every item for the requested values and 
    can use up the provisioned throughput for a large table in a single operation

#### How to Improve Performance
* you can reduce the impact of a query or scan by setting a smaller page size which uses fewer read operations
    * e.g. set the page size for a request (the *Limit* parameter) to return 40 items
        * this will limit the number of items scanned and returns immediately when the limit is reached
    * you will end up with a larger number of smaller operations that will allow other requests to succeed without 
    throttling
* avoid using Scans if you can
    * design your tables in a way that can use the ```Query```,```Get```, or ```BatchGetItem``` APIs
* By default, a Scan operation processes data sequentially and returns **1MB** increments before moving on to
retrieve the next 1MB of data.
    * **it can only Scan one partition at a time**
* you can configure DynamoDB to use Parallel scans instead by logically dividing a table or index into **segments** and
scanning each segment in parallel
    * best to avoid parallel scans if your table or index is already incurring heavy read/write activity from other
    applications



## DynamoDB Provisioned Throughput
### Read and Write Capacity Units
* DynamoDB Provisioned Throughput is measured in Capacity Units
* when you create your table, you specify your requirements in terms of:
    * Read Capacity Units  (RCU)
        * **1 RCU = 1 x Strongly Consistent Read of 4KB per second**
        *   OR
        * **2 x Eventually Consistent reads of 4KB per second (default)**
    * Write Capacity Units (WCU)
        * **1 x WCU = 1 x 1KB write per second**

#### example configuration
* table with 5 x RCUs and 5 x WCUs will be able to perform:
    * 5 x 4KB Strongly Consistent Reads = 20KB per second
    * twice as many eventually consistent reads = 40KB
    * 5 x 1KB Writes = 5KB per second
* if your application reads or writes larger items it will consume more capacity units and will cost you more as well
    
#### Strongly Consistent Reads Calculation
* your app needs to read 80 DynamoDb items per second
* each item is 3KB in size
* you need Strongly Consistent Reads
    1. calculate how many RCUs needed for each read
        * Size of each item / 4KB (rounded up to nearest whole number)
            * 3KB / 4KB = 0.75 = 1 RCU per operation
    2. multiply by the number of reads per second
         * 1 RCU x 80 reads per second = 80 RCUs required

#### Eventually Consistent Reads Calculation
* do the same calculation as Strongly consistent, but divide the result by 2 since Eventually consistent reads
double the throughput of Strongly Consistent Reads
* using the same example as above, but   80 / 2 = 40 RCUs required

#### Write Capacity Units Calculation
* you want to write 100 items per second
* each item is 512 bytes in size
    1. calculate how many WCU for each write
        * size of item / 1KB (rounded up to nearest whole number)
            * 512 bytes / 1 KB = 0.5 = 1 WCU per write operation
    2. multiply by the number of writes per second
        * 1 WCU * 100 items per seconds = 100 WCUs
        


### DynamoDB Provisioned Throughput Calculations
* Unit of Read provisioned throughput
    * all reads are rounded up to increments of 4KB
    * Eventually Consistent Reads (default) consist of **2 reads per second**
    * Strongly Consistent Reads consist of **1 read per second**
* Unit of Write provisioned throughput
    * All writes are 1KB in size
    * All writes consist of 1 write per second
            
### The Magic Formula
#### Read units
* (E) **(Size of Read rounded to nearest 4KB chunk / 4KB) * num of items = read throughput**
    * **Divide by 2 if eventually consistent**

* Example 1 - You have an application that requires to read 10 items (a row in DynamoDB) of 1KB per second using 
eventual consistency. What should you set the read throughput to?
* Solution 1:
    * First calculate how many read units *per item* we need
        * 1KB rounded to the nearest 4KB increment = 4
        * 4KB / 4KB = *1 read unit per item*
    * 1 * 10 read items = 10
    * using eventual consistency we get 10/2 = 5
    * *5 units of read throughput*

* Example 2 - You have an application that requires to read 10 items of 6KB per second using eventual consistency.
What should you set the read throughput to?
* Solution 2:
    * First calculate how many read units per item we need
        * 6KB rounded up to nearest increment of 4KB is 8KB
        * 8KB / 4KB = 2 read units per item
    * 2 * 10 read items = 20
    * using eventual consistency we get 20 / 2 = 10
    * *10 units of read throughput* 
    
* Example 3 - You have an application that requires to read 5 items of 10KB per second using eventual consistency.
What should you set the read throughput to?
* Solution 3:
    * First calculate how many read units per item we need
        * 10KB rounded up to nearest increment of 4KB is 12KB
        * 12KB / 4KB = 3 read units per item
    * 3 * 5 read items = 15
    * using eventual consistency we get 15 / 2 = 7.5
    * *8 units of read throughput*

* Example 4 - You have an application that requires to read 5 items of 10KB per second using strong consistency. What
should you set the read throughput to?
* Solution 4:
    * First calculate how many read units per item we need
        * 10KB rounded up to nearest 4KB increment is 12KB
        * 12KB / 4KB = 3 read units per item
    * 3 * 5 read items = 15
    * using **strong consistency** we get *15 units of read throughput*

#### Write Units
* Example 1 - You have an application that requires to write 5 items, with each item being 10KB in size per second.
What should you set the write throughput to?
    * **Each write unit consists of 1KB of data.** You need to write 5 items per second with each item using 10KB of data.
    * 5 * 10KB = 50 write units
    * *write throughput of 50 units*
    
* Example 2 - You have an application that requires to write 12 items of 100KB per item each second. What should you 
set the write throughput to?
    * Each write unit consists of 1KB of data.
    * You need to write 12 items per second with each item using 100KB of data
    * 12 * 100KB = 1200 write units
    * write throughput of 1200 units

* What happens if you exceed your write or read throughput?
    * (E) 400 HTTP Status Code - **ProvisionedThroughputExceededException**
    * You exceeded your maximum allowed provisioned throughput for a table or for one or more global secondary indexes


## DynamoDB Accelarator (DAX)
* **DAX** is a fully managed, clustered in-memory cache for DynamoDB
* it is optimized to work specifically with dynamodb
* delivers up to a 10x read performance improvement
* microsecond performance for millions of requests per second
* ideal for read-heavy and bursty workloads
    * e.g. auction applications, gaming, retail sites during black Friday promotions

### How Does DAX Work?
* **DAX is a write-through caching service**
    * data is written to the cache as well as the back-end store at the same time
* allows you to point your DynamoDB API calls at the DAX cluster
* if the item you are querying is in the cache (cache hit), DAX returns it to your application
* if the item is not available (cache miss) then DAX performs an **Eventually Consistent** GetItem operation against
DynamoDB
* retrieval of data from DAX reduces the read load on DynamoDB tables
    * you *may* be able to reduce Provisioned Read Capacity

#### What is DAX not suitable for?
* DAX uses Eventually Consistent Reads only, so not suitable for apps that require Strongly Consistent Reads
* not for write intensive applications
* applications that don't perform many read operations
* applications that do not require microsecond response times



## DynamoDB Exam Tips
* DynamoDb is a low latency NoSQL database
* consists of Tables, Items, and Attributes
* supports both document and key-value data models
* supported document formats are JSON, HTML, XML
* 2 types of Primary Key
    * Partition Key
    * Partition Key + Sort Key  (aka composite key)
* 2 consistency models:
    * Strongly Consistent
        * can take up to 1 second for new writes to be reflected in your read
    * Eventually Consistent (default)
* Access is controlled using IAM policies
    * fine grained access control is achieved by using the IAM Condition parameter:
        * `dynamodb:LeadingKeys` - allows users to access only the items where the partition key value matches their
        user ID
* DynamoDB Indexes
    * indexes enable fast queries on specific data columns
    * give you a different view of your data, based on alternative Partition / Sort Keys
    * important to understand the differences between GSIs and LSIs
        * Local Secondary Indexes:
            * must be created when you create the table
            * same partition key as your table
            * different sort key
            * can provide Strongly Consistent Reads or Eventually Consistent
        * Global Secondary Index
            * can create at any time (table creation or after table creation)
            * different partition key
            * different sort key
            * can only provide eventually consistent reads
* Scans and Queries
    * a query finds items in a table using only the Primary Key
        * you provide the primary key name and a distinct value to search for
    * a Scan operation examines every item in the table
        * by default it returns all data attributes
            * use a `ProjectionExpression` parameter to indicate which attributes to return
            * can also use a `FilterExpression` to perform comparisons on the results
    * query results are always sorted by the sort key (if there is one)
    * sorted in ascending order
        * set `ScanIndexForward` to `false` to reverse the order (**queries only**)
    * query operation is generally more efficient than a scan
    * reduce the impact of a query or scan by setting a smaller page size which uses fewer read operations
    * Isolate scan operations to specific tables and segregate them from your mission critical traffic
    * try parallel scans rather than the default sequential scan
    * avoid using scans if you can...
        * design your tables in a way that you can use the Query,Get, or BatchGetItem APIs
* 1 x Write Capacity Unit = 1 x 1KB write per second
* 1 x Read Capacity Unit = 1 x 4KB strongly consistent read OR 2 x 4KB eventually consistent read
* KNOW HOW to CALCULATE WRITE/READ CAPACITY REQUIREMENTS
* DAX
    * provides in-memory caching for DynamoDB tables
    * improves response times for **Eventually Consistent Reads** only
    * you point your API calls to the DAX cluster, instead of your table
    * if the item you are querying is on the cache, DAX will return it; otherwise it will perform an Eventually
    Consistent GetItem operation to your DynamoDB table
    * not suitable for write-intensive applications or applications that require Strongly Consistent reads
    
 

## Using Web Identity Providers with DynamoDB
* You can authenticate users using Web Identity providers
    * Facebook, Google, Amazon, or any other Open-ID connect compatible identity provider
    * this is done using ```AssumeRoleWithIdentity``` API
    * you will need to create a Role first
* (E) For the exam you need to know the basic steps taken to authenticate
    1. User authenticates with ID provider (eg. Facebook)
    2. They are passed a token by their ID provider
    3. Your code calls ```AssumeRoleWithIdentity``` API and provides the providers token and specifies the ARN for the
    IAM role
    4. App can now access DynamoDB from between 15 minutes to 1 hour (default is 1 hour)

## DynamoDB APIs
* CreateTable - create a new table and its indices
* UpdateTable - update provisioned throughput values
* DeleteTable - 
* DescribeTable - return table size, status and index information
* ListTables - return a list of all tables associated with the current account and endpoint
* PutItem - creates a new item, or replaces an old item with a new item
* (E) BatchWriteItem - inserts, replaces or deletes multiple items across multiple tables in a single request
    * supports batches up to 25 items to Put or Delete, **max total request size 16MB**
* UpdateItem - edit an existing items attributes. can use conditional operators
* DeleteItem - delete a single item by primary key. can use conditional operators
* GetItem - returns a set of attributes for an item that matches the primary key
* (E) BatchGetItem - returns attributes for multiple items across multiple tables using their primary key
    * size limit of **16MB** and returns a maximum of 100 items
* Query - gets one or more items using a primary key, or from a secondary index using the index key
    * can use comparison operators and expressions to narrow the scope of the query
    * a single response has a size limit of **1MB**
* Scan - gets all items and attributes by performing a full scan across the table or secondary index
    * response has a **1MB** size limit

## DynamoDB Data Types
* Scalar Data Types:
    * Number, String, Binary, Boolean
    * NULL
* Collection Data Types
    * Number Set
    * String Set
    * Binary Set
    * heterogeneous list
    * heterogeneous map
    

## Other important aspects of DynamoDB
* (E) Conditional Writes
    * update an item if some conditional statement is true
        * if item = $10 then update it to $12
    * conditional writes are idempotent
        * you can send the same conditional writes request multiple times, but it will have no further effect on the
        item after the first time DynamoDB performs the specified update
* (E) AtomicCounters
    * you use ```UpdateItem``` operation to increment or decrement the value of an existing attribute without interfering
    with other write requests
        * **all write requests are applied in the order in which they are received**
    * eg. a website visitor counter that you want to increment regardless of its current value
    * AtomicCounters are **NOT** idempotent
        * Each call to ```UpdateItem``` will perform the update
            * if you suspect an update failed, then each call to UpdateItem could potentially update the item again
        * NOT suitable for banking applications, safer to use Conditional update
* (E) Scenario Question
    * exam might ask if you should be using Conditional Writes or AtomicCounters
    * **ask yourself is it critical that the data has to be correct, or can you have margin of error**
* (E) Batch Operations
    * if your application needs to read multiple items, you can use ```BatchGetItem``` API. 
    * A single ```BatchGetItem``` request can retrieve up to 16MB of data, which can contain as many as 100 items. 
    * In addition, a single ```BatchGetItem``` request can retrieve items from multiple tables
* There are no throughput limits, BUT If you wish to exceed throughput rates of 10,000 writes/second or 
10,000 reads/second, you must first contact Amazon through this online form
* When defining primary keys, try to use a many to few principle
    * you want a partition key that has a large number of distinct values and that are requested fairly uniformly, as
    randomly as possible
* initial limit of 256 tables per region, can be upped by contacting Amazon support


KMS and Encryption on AWS
=====================================================================================================
* AWS Key Management Service is a managed service that makes it easy for you to create and control the encryption
keys used to encrypt your data
* KMS is integrated with other AWS services:
    * EBS
    * S3
    * Redshift
    * Amazon Elastic Transcoder
    * Amazon Workmail
    * Amazon RDS
    * plus others
* KMS makes it simple to encrypt your data with encryption keys that you manage
* **encryption keys are regional**
* when you set-up a key you will need to specify two types of permissions to either a user or group:
    * *Key Administrative Permissions*
        * this is the user/role that can administer key through the KMS API
        * NOTE: they can't use the key
    * *Key Usage Permissions*
        * IAM users/role that can use the key to encrypt and decrypt data

## KMS Envelope Encryption
* Envelope encryption is the practice of encrypting plaintext data with a data key and then encrypting the data key
under another key
* envelope encryption in a nutshell:
    1. Customer Master Key encrypts the Data Key (aka Envelope key)
    2. Envelope Key is used to encrypt your data
* decryption with an envelope key:
    * master key (CMK) + encryption algorithm is used to decrypt the envelope (data) key into a plaintext key
    * the plaintext key is used to decrypt your data
    

## KMS Exam Tips
* **The Customer Master Key (CMK)** consists of:
    * alias
    * creation date
    * description
    * key state (enabled or disabled)
    * **key material**
        * customer provided
        * AWS provided
    * CMKs stored in AWS **can never be exported out of KMS unencrypted**
    * CMKs are used to encrypt/decrypt up to 4KB of data
* **Data Keys**
    * data keys are encryption keys that you use to encrypt data, including:
        * large amounts of data
        * other data encryption keys
* Setting up a Customer Master Key:
    * create alias and description
    * choose a **key material option**:
        * **KMS**
            * kms generates the key material    
        * **External**
            * your own key material
    * Define Key **Administrative Permissions**
        * IAM users/roles that can administer (but not use) the key through the KMS API
    * Define Key **Usage Permissions**
        * IAM users/roles that can use the key to encrypt and decrypt data
* KMS CLI
    * the CLI operations mentioned below use a CMK to encrypt/decrypt up to 4KB of arbitrary data
    * know how to use the following KMS API calls and what they do:
        * `aws kms encrypt` --key-id CMK-ID --plaintext fileb://secret.txt --output text --query CiphertextBlob | base64 --decode > encryptedsecret.txt
            * Encrypts plaintext into ciphertext by using a customer master key (CMK)
            * You can use the Encrypt operation to move encrypted data from one AWS region to another
        * `aws kms decrypt` --ciphertext-blob fileb://encryptedsecret.txt --output text --query Plaintext | base64 --decode > decryptedsecret.txt
            * Decrypts ciphertext
                * Ciphertext is plaintext that has been previously encrypted
        * `aws kms re-encrypt` --destination-key-id NEW-CMK-ID --ciphertext-blob fileb://encryptedsecret.txt | base64 > newencryption.txt
            * Encrypts data on the server side with a new customer master key (CMK) without exposing the plaintext 
            of the data on the client side
            * **The data is first decrypted and then re-encrypted**
            * You can also use this operation to change the encryption context of a ciphertext
        * `aws kms enable-key-rotation` --key-id KEY-ID
            * Enables automatic rotation of the key material for the specified customer master key (CMK)
                * AWS KMS generates new cryptographic material for the CMK every year
            * You cannot perform this operation on a CMK in a different AWS account
* Envelope Encryption
    * know that the *customer master key* does the following:
        * **used to decrypt the data key (aka envelope key)**
        * **envelope key is then used to decrypt the data**


Simple Queue Service (SQS)
==================================================================================================================
* SQS is a message queue that stores "messages" while waiting for a computer to process them
* SQS is a distributed queue that enables applications to quickly and reliably queue messages that one component in
the application generates to be consumed by another component
* SQL enables you to **decouple** the components of an application so they run independently, easing message
management between components
* any component of a distributed application can store messages in the queue
* **messages can contain up to 256KB of text**
    * **a message can include only XML, JSON, and unformatted text**
    * To send messages larger than 256 KB, you can use the Amazon SQS Extended Client Library for Java. 
* any component can later retrieve the messages programmatically using the SQS API
* The queue acts as a buffer between the component producing and saving data, and the component receiving the data
for processing. This means the queue resolves issues that arise if the producer is producing work faster than 
the consumer can process it, or if the producer or consumer are only intermittently connected to the network

* fyi: SQS is the oldest AWS Service
* need to understand what SQS is at a very high level

## Queue Types
### Standard Queues
* the default queue type
* **unlimited throughput**
    * nearly unlimited number of transactions per second per API action
* **guaranteed At-least-once delivery** 
    * a message is delivered at least once, but **occasionally more than once copy of a message is delivered**
* **Best-Effort-Ordering**
    * occasionally messages might be delivered in an order different from which they were sent
* If your system requires that order be preserved, you can place sequencing information in each message, so that
you can reorder the messages when the queue returns them. Or see if you can use a FIFO queue

### FIFO Queues
Used when the **order** of operations and events is critical. FIFO queues not available in all regions
* High Throughput
    * support **300 transactions per second** (300 send,receive,or delete operations per second)
    * when you **batch 10 messages per operation**, FIFO queues can support up to **3,000 messages per second**
* **Exactly Once Message Processing**
    * a message is delivered once and remains available until a consumer processes and deletes it. Duplicates are
    not introduced into the queue
* **First-in-First-out delivery**
    * **the order in which messages are sent and received is strictly preserved**
* cannot convert existing standard queues into FIFO queues

### Dead Letter Queues
* a special type of queue that holds messages that can't be processed successfully
* useful for debugging because they enable you to isolate problematic messages to determine why they failed

## SQS Key Facts
* SQS is *pull-based* NOT push-based
* Messages are 256KB in size
* **Messages can be kept in the queue from 1 minute to 14 days**
    * default retention period is **4 days**
* SQS Standard queues guarantees your messages will be delivered **at least once**
    * you will need to plan for this when architecting a solution using SQS Standard Queues 

### SQS Visibility Timeout
* the amount of time that the message is invisible in the SQS queue after a reader picks up that message
* If a job (message) is processed before the visibility timeout expires, the message will then be deleted from
the queue
* If the job is not processed within that time, the message will become visible again and another consumer can
process it
    * this could result in the same message being delivered twice
* **default visibility timeout is 30 seconds**
    * increase it if your task takes > 30 seconds
* **maximum is 12 hours**

### SQS Long Polling
* a way to retrieve messages from your Amazon SQS queues
    * long polling doesn't return a response to your calling application until a message arrives in the queue
        * OR the long poll times out
* long polling can save you money, since you are not rapidly polling the queue for a message

## SQS Exam Tips
* SQS is a distributed message queueing system
* allows you to decouple the components of an application so that they are independent
* poll-based, not push
* Standard Queues (default)
    * best-effort ordering
    * messages delivered at least once
* FIFO Queues
    * ordering is strictly preserved
    * messages are delivered once, no duplicates
        * good for banking transactions which need to happen in strict order
* Visibility Timeout
    * default is 30 seconds
        * increase it if your task takes > 30 secs
        * max vis. timeout is 12 hours
    * you can call ```ChangeMessageVisibility``` to specify a new timeout value. SQS will restart the timeout 
    period using the new value
* Short Polling (default)
    * a request to SQS using short polling returns immediately even if no messages are in the queue
* Long Polling
    * a request using long polling polls the queue periodically and only returns a response when a message is in
    the queue or the timeout is reached
    * ```ReceiveMessage( WaitTimeSeconds )``` - API call to enable long polling, must set WaitTimeSeconds parameter 
        * **MAX poll wait time = 20 seconds**



Simple Notification Service (SNS)
====================================================================================================================
* SNS is a web-service that makes it easy to set up, operate, and send notifications from the cloud.
* It provides developers with a highly scalable, flexible, and cost-effective capability to publish messages from an
application and immediately deliver them to subscribers or other applications
* SNS follows the *publish-subscribe* messaging paradigm, with notifications being delivered to clients using a *push*
mechanism that eliminates the need to periodically check of *poll* for new information and updates
* With simple APIs requiring minimal up-front development effort, no maintenance or management overhead and 
pay-as-you-go pricing, Amazon SNS gives developers an easy mechanism to incorporate a powerful notification system
with their application
* Besides pushing cloud notifications directly to mobile devices, Amazon SNS can also deliver notifications by SMS text
message or email, to SQS queues, or to any HTTP endpoint.
* SNS notifications can also trigger Lambda functions:
    * when a message is published to an SNS topic that has a Lambda function subscribed to it, the Lambda
    is invoked with the payload of the published message
    * the lambda receives the payload as an input parameter and can manipulate the information in the message,
    publish the message to another SNS topic, or send the message to other AWS services
* To prevent messages from being lost, all messages published to SNS are stored redundantly across multiple AZs
* Pricing
    * users pay $0.50 per 1 million SNS requests
    * $0.06 per 100,000 notification deliveries over HTTP
    * $0.75 per 100 notification deliveries over SMS
    * $2.00 per 100,000 notification deliveries over Email

## Topics
* SNS allows you to group multiple recipients using topics. A topic is an *access point* for allowing recipients to
dynamically subscribe for identical copies of the same notification
* One topic can support deliveries to multiple endpoint types
    * eg. you can group together iOS, Android and SMS recipients. When you publish once to a topic, SNS delivers
    appropriately formatted copies of your message to each subscriber
* Topic for Email Subscriptions
    * (E) users must confirm via email reply that they want to be subscribed to the topic
    * (E) the subscription request will expire after 3 days
    * (E) the email subscription request can be sent as JSON (or as a regular text Email)

## SNS Benefits
* Instantaneous, push-based delivery (no polling)
* Simple APIs and easy integration with applications
* Flexible message delivery over multiple transport protocols
* Inexpensive, pay-as-you-go model, with no up front costs
* Web based AWS Management Console offers the simplicity of a point-and-click interface

## SNS vs SQS
(E)
* Both messaging services in AWS
* SNS - push
* SQS - Polls (pulls)

* (E) Should you use SQS or SNS?
    * Do you need to *push* messages out to users/apps/etc..? Use SNS
    * Do you need to *pull* or *poll* messages? use SQS
* (E) SNS message type for email push is JSON

## Format of structured notification messages
The notification message sent over **HTTP,HTTPS,Email-JSON and SQS** will consist of a simple JSON object, which includes
the following information:
* MessageId: a UUID
* Timestamp
* TopicArn: topic to which this message was published
* Type: the type of the delivery message (eg. 'Notification')
* UnsubscribeURL: a link to unsubscribe the end-point from this topic
* Message: the payload (body) of the message
* Subject: the subject field (if one was provided)
* Signature - base64 encoded signature of the message
* SignatureVersion - version of the Amazon SNS signature used

**Email** transport messages only contain the message body

## Mobile Push High Level Steps
Amazon SNS can send mobile push messages directly to an app on a mobile device. SNS can also send messages to mobile
endpoints subscribed to a topic.
1. Request credentials from Mobile platforms (APNS, GCM, etc...)
2. Request token from mobile platforms
3. Create platform application object
4. Create platform endpoint object
5. publish message to mobile endpoint 

## SNS Exam Tips
* SNS is a scalable and highly available notification service which allows you to send push notifications from the cloud
    * variety of message formats supported:
        * SMS text messages
        * email
        * SQS
        * any HTTP endpoint
* uses a Pub-Sub model whereby users subscribe to topics
* it is a push mechanism rather than pull
* SNS can push messages into (multiple) SQS queues
    * this is called *fan-out*

* SNS consists of a topic and each topic can have multiple subscriptions
    * max topic name length is 256 characters    
* once a message is successfully published to a topic, it cannot be recalled
* Be default, SNS offers **10 million** subscriptions per topic, and **100,000** topics per account
* With the exception of SMS messages, Amazon SNS messages can contain up to **256KB** of text data, including XML, 
JSON and unformatted text
* SNS subscriptions support multiple delivery protocols
    * Email
    * Email as JSON
    * HTTP
    * HTTPS
    * Amazon SQS 
        * standard queues only
    * Application (i.e. mobile app. push notifications)
        * Amazon Device Messaging
        * Apple Push Notification Service
        * Google Cloud Messaging (GCM)
        * Windows Push Notification Service
        * Microsoft Push Notification Service
        * Baidu Cloud Push (for Android devices in China)
    * AWS Lambda
    * SMS
* Messages can be customized for each protocol above
* messages could be delivered out of order
* AWS does **NOT** guarantee delivery of messages to subscribed end-points (due to potential internet issues, or 
email restrictions)
    * SNS implements a 4-phase retry policy
        1. retries with no delays in between attempts
        2. retries with some minimum delay in between attempts
        3. retries with some back-off model (linear or exponential)
        4. retries with some maximum delay between attempts


Simple Email Service (SES) vs SNS
==================================================================================================================
* SES is a scalable and highly available email service designed to help marketing teams and application developers
send marketing, notification, and transactional emails to their customers using a pay as you go model
* can also be used to receive emails
    * incoming emails can automatically be delivered to an S3 bucket
    * incoming emails can be used to trigger Lambdas and SNS notifications
* Basically SES is email only
    * automated emails
    * purchase confirmations, shipping notifications, order status updates....
        * e.g. a mobile phone company that needs to send automated confirmation emails every time a customer
        purchases pre-paid mobile phone minutes
    * marketing communications, advertisements, special offers...
        * e.g. an online retail business that needs to let customers know about special offers


| SES | SNS |
|:---:|:---:|
| Email messaging service  | pub/sub messaging services (formats include: SMS,HTTP,SQS,email) |
| Can trigger Lambdas or SNS notification  | can trigger Lambda function |
| Can be used for incoming or outgoing email  | Can fan out messages to large number of recipients  |
| An email address is all that is required to start sending messages to a user | Consumers must subscribe to a topic to receive the notifications |


## Exam tips
* SES is email only
    * can be used for incoming and outgoing email
    * it is NOT subscription based, you only need to know the email address
* SNS supports multiple formats (SMS,SQS,HTTP,email)
    * push notifications only
    * pub/sub model
        * consumers must subscribe to a topic
    * you can fan-out messages to a large number of recipients:
        * multiple clients each with their own SQS queue



Kinesis
===============================================================================================================
* what is streaming data?
    * data that is generated continuously by thousands of data sources, which typically send in their data records
    simultaneously, and in small sizes (think kilobytes)
        * eg. purchases from online stores
        * stock prices
        * game data (as the gamer plays)
        * social network data
        * geospatial data (think uber.com)
        * IoT sensor data
* What is Kinesis?
    * a platform on AWS to send your streaming data to
    * Kinesis makes it easy to load an analyze streaming data and also provides the ability for you to build your
    own custom application for your business needs
* What are the core Kinesis services?
    * kinesis streams
        * **kinesis data streams**
            * default 24 hour retention of data
            * can be increased to 7 days
        * **kinesis video streams**
    * **kinesis firehouse**
        * Amazon Kinesis Data Firehose is a fully managed service for delivering **real-time streaming data**
    * **kinesis analytics** 

## Kinesis Streams
### Kinesis Data Streams
* You can use Amazon Kinesis Data Streams to collect and process large streams of data records in real time
    * a common use is the real-time aggregation of data followed by loading the aggregate data into a 
    data warehouse or map-reduce cluster
* A Kinesis data stream is a set of *shards*
* data producers (mobile phones, laptops, web servers...) send data to kinesis streams and the data gets put into
one or more shards
* data consumers (usually EC2 instances) can then read data from the shards and process it
* processed data can be stored in a variety of AWS resources
    * DynamoDB, S3, EMR, Redshift, etc....

#### Shards
* A shard is a uniquely identified sequence of data records in a stream
* a shard gives you *5 transactions per second* for reads, up to a *max read rate of 2MB per second*
* up to *1,000 records per second for writes*, up to a *max total data write rate of 1MB per second* (including
partition keys)
* the data capacity of your stream is a function of the number of shards that you specify for the stream
* the total capacity of the stream is the sum of the capacity of its shards

### Kinesis Video Streams
* securely stream video from connected devices to AWS for analytics and machine learning

## Kinesis Firehouse
* data producers (mobile phones, web servers, laptops....) send data to kinesis firehose
* you don't worry about setting up streams and configuring shards because kinesis firehose is *more* automated
* data can optionally be analyzed by lambda functions before storing it
* data is then finally stored
    * usually in S3
    * can also store in *Elasticsearch cluster*, Redshift, Splunk
* kinesis firehose does not retain data (within kinesis streams)


## Kinesis Analytics
* With Amazon Kinesis Data Analytics, you can process and analyze streaming data using standard SQL
* The service enables you to quickly author and run powerful SQL code against streaming sources to perform time 
series analytics, feed real-time dashboards, and create real-time metrics
    * streaming sources are Kinesis Streams or Kinesis Firehose
* the results of your SQL are sent to a kinesis data stream and then onto S3, or Redshift table, or ElasticSearch

## Exam Tips
* KNOW the DIFFERENCE between kinesis streams and kinesis firehouse (you will be given scenario questions)
    * kinesis streams
        * you must manually provision stream sizes and shard counts at creation time  
        * partitions data into shards
        * can retain the data in a shard for 24 hours up to 7 days
        * you write a application to consume the stream (using a SDK)
            * typically the consumer app will run on EC2
    * kinesis firehouse
        * automatically scales to meet demand (no configuration of stream size/shard counts)
        * can optionally use Lambdas to process/transform data streams before storing the data
        * no data retention
* kinesis analytics
    * lets you process streaming data (from kinesis streams or kinesis firehose) using SQL
    * results of the SQL are sent to kinesis stream and then onto S3, or Redshift, or Elasticsearch 



Elastic Beanstalk
=======================================================================================
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
* Either you entire application is one EB application OR
    * each logical component of your application can be a EB application OR
     * a EB environment within an application
* Applications can have multiple environments (Prod, Staging, Dev, V1,V2,etc) or functional type (front-end, back-end)
* Environments are either single instances or scalable
* Environments are either web server environments or worker environments

* application versions are unique packages which represent versions of your apps.
* each application can have many versions (1:M relationship)
* application versions can be deployed to environments within an application

## Available Platforms on Elastic Beanstalk
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
    
## Using Elastic Beanstalk with a Database
* Two options
    * have EB create and provision a RDS DB for you
        * you can then configure it to delete the DB upon application termination, 
        * or configure it to take a snapshot of your DB and store it on S3 upon application termination 
    * use an existing RDS database
        * you create this yourself and then configure EB to use it

## Updating Elastic Beanstalk
* You can do application updates as well as configuration updates
* Elastic Beanstalk supports several options for Deployment policies:
    * All at once
        * deploys new version to all instances simultaneously
        * all instances are out of services while the deployment takes place
        * you will experience an outage while the deployment is taking place
            * not ideal for mission critical production systems
        * if the update fails, you need to roll-back the changes by re-deploying the original version to all your
        instances
    * Rolling
        * deploys the new version in batches
        * each batch of instances is taken out of service while the deployment takes place
        * you environment capacity will be reduced by the number of instances in a batch while the deployment 
        takes place
        * not ideal for performance sensitive systems
        * if the update fails, you need to perform an additional rolling update to roll back the changes
    * Rolling with additional batch
        * launches an additional batch of (the old) instances
        * deploys the new version in batches
        * maintains full capacity during the deployment process
        * if the update fails, you need to perform an additional rolling update to roll back the changes
    * Immutable
        * deploys the new version to a fresh group of instances in their own autoscaling group
        * when the new instances pass their health checks...
            * they are moved to your existing auto scaling group
            * finally the old instance are terminated
        * maintains full capacity during the deployment process
        * the impact of a failed update is far less, and the rollback process requires only terminating
        the new autoscaling group
        * preferred option for mission critical production systems

## Advanced Elastic Beanstalk
* you can customize your elastic beanstalk environment using Elastic Beanstalk configuration files
    * e.g. you can define packages to install, create linux users, run shell commands, config. your load balancer
* these are files written on YAML or JSON format
    * they can have a filename of your choice but **must have a .config extension**
    * they must be saved inside a folder called **.ebextensions**
        * the .ebextensions folder must be included in the top-level directory of your application source bundle
    * these config files can be placed inside source control along with the rest of your app. source code

#### example healthcheck .config
* configures an Elastic Load Balancer to make a HTTP request to `/health` om the ec2 instances it is fronting
```json
{
 "option_settings" :
    [
      {
        "namespace" : "aws:elasticbeanstalk:application",
        "option_name" : "Application Healthcheck URL",
        "value" : "/health"
      }
    ]
}
```

## Elastic Beanstalk (EB) with RDS
* elastic beanstalk supports two ways of integrating an RDS database with your Beanstalk environment
* you can launch the RDS instance from within the EB console
    * which means the RDS instance is created within your EB environment
        * a good option for Dev and Test deployments
    * this may not be ideal for production environments
        * the lifecycle of your database is tied to the lifecycle of your application environment
        * if you terminate the environment, the DB will be terminated too
    * For production environments, the preferred option is to decouple the RDS instance from your EB environment:
        * i.e. launch it outside of EB, directly from the RDS section of the console
        * this option gives you a lot more flexibility
            * allows you to connect multiple environments to the same DB
            * provides a wider choice of DB types
            * allows you to tear down your application environment without affecting the DB instance
            
* to allow the ec2 instances in your EB environment to connect to an outside DB, there are 2 additional steps:
    * an additional security group must be added to your environment's Auto Scaling Group
    * you'll need to provide connection string configuration information to your application servers
        * endpoint, password, connection string  using EB environment properties


## Exam tips
* deploys and scales your web applications including the web application server platform where required
* supports widely used programming technologies:
    * Java
    * PHP
    * Python
    * Ruby
    * Go
    * Docker
    * .NET
    * Node.js
* and application server platforms:
    * Tomcat
    * Passenger
    * Puma
    * IIS
* provisions the underlying resources for you
* can fully manage EC2 instance for you OR you can take full administrative control
* updates, monitoring, metrics and health checks all included    
* Your applications can be split into tiers (Web Tier, Application Tier, Database Tier)
* You can do application updates as well as configuration updates
    * Deployment policies
        * All at once, Rolling, Rolling with additional batch, and Immutable
* You pay for the AWS resources EB configures for you (EB itself is free) 
* KNOW HOW CODE UPDATES WORK IN ELASTIC BEANSTALK
    * all at once
        * service interruption while you update the entire environment at once
        * to roll bck, you need to perform a additional all at once upgrade
    * rolling
        * reduced capacity during deployment
        * to roll back, perform a further rolling update
    * rolling with additional batch
        * maintains full capacity
        * to roll back, perform a further rolling update
    * immutable
        * preferred for mission critical production systems
        * maintains full capacity
        * to roll back, just delete the new instances and auto-scaling group
* you can customize/configure your application environment by adding configuration files
    * they are written in YAML or JSON
    * they have a .config extension
    * the .config files are saved to the .ebextension folder
        * this folder must be at the top level dir of your app. source code bundle
* two different options for launching your RDS instance:
    * launch within EB
        * when you terminate the EB environment, the DB is also terminated
        * quick and easy to add your DB and get started
        * suitable for Dev and Test environments only
    * launch outside of EB
        * additional config steps required
            * Security Group and DB connection information
            * suitable for prod environments, more flexible
            * allows connections from multiple EB environments
                * you can tear down the app stack without impacting the DB


Developer Theory (Continuous Integration (CI) / Continuous Deployment (CD))
======================================================================================================================
* CI & CD are best practices for software development and deployment
* they enable frequent software changes to be applied whilst maintaining system and service stability
* companies like AWS,Netflix,Google and Facebook have pioneered this approach to releasing code, successfully
applying thousands of changes per day

### Continuous Integration Workflow
1. imagine multiple developers working on different features or bug fixes for an application
    * All contributing to the same application
    * sharing the same code repository (e.g. GIT)
    * frequently pushing their updates into the shared repo (at least daily)
2. the code repository is integrated with a build management system
    * code changes trigger an automated build (no matter how big or small the code change)
    * we need a way to ensure that any code change does not break the build or introduce new bugs into the application
        * ...so you need a test framework
3. the test framework runs automated tests on the newly built application
    * unit tests, integration tests, etc...
    * identifies any bugs, preventing issues from being introduced in the master code
    * CI focuses on small code changes which are committed into the main repository once they have been
    successfully tested

### Continuous Delivery / Deployment
* CD can mean either Continuous delivery or continuous deployment
* **continuous delivery** is a development practice where merged changes are automatically built, tested and prepared for
release into staging and eventually production environments
    * **there is usually a manual decision process to initiate deployment of the new code**
* **continuous deployment** takes the idea of automation one step further and automatically deploys the new code 
following successful testing, eliminating any manual steps
    * the new code is automatically released as soon as it passes through the stages of your release process
    * Small changes are released early and frequently
* both practices require the build, test and deployment processes to be fully automated but **continuous deployment**
also automates the release process as well

## CodeCommit
* AWS's code repository
* it's a private GIT repository
* developers create a branch (copy) of the master repository and make their code changes to the branch
* once their code changes are complete they push their changes, called a *commit*, to the master repository
* their changes can then be merged into the master repository (called a *pull* request)
* your data is encrypted in transit and at rest (within CodeCommit)

## CodeBuild
* AWS's build management system
* it can compile your code
* run some basic tests
* can create software packages that are ready to deploy to your environment

## CodeDeploy
* automated application deployment service
* it can deploy your code to:
    * EC2 instances
    * on premises systems
    * and lambda functions
* allows you to quickly release new features, avoid downtime during application deployments and avoid the risks
associated with manual processes
* automatically scales with your infrastructure and integrates with various CI/CD tools:
    * Jenkins, GitHub, Atlassian, CodePipeline
    * and config management tools like:
        * Ansible, Puppet, Chef
* **Two deployment approaches available:**
    * **in-place**
        * the application is stopped on each instance and the latest revision is installed
        * the instance is out of service during this time and your capacity is reduced
        * if the instances are behind a load balancer, you can configure the load balancer to stop sending requests
        to the instances which are being upgraded
        * a.k.a *Rolling Update*
        * it can only be used for EC@ and on-premise systems
            * it is **not supported for Lambda**
        * if you need to roll-back your changes, the previous version of the application will need to be re-deployed
    * **Blue/Green**
        * new instances are provisioned and the latest revision is installed on the new instances
        * Blue represents the active deployment, green is the new release
        * the new instances are registered with an Elastic Load Balancer, traffic is then routed to the new instance
        and the original instances are eventually terminated
        * advantages are: 
            * that the new instances can be created ahead of time and the code released to production by simply 
            switching all traffic to the new servers
            * switching back to the original environment is faster and more reliable and is just a case of routing 
            the traffic back to the original servers (assuming you haven't terminated them)
* CodeDeploy Terminology
    * *Deployment Group* - a set of EC2 instances or Lambda functions to which a new revision of the software is to 
    be deployed
    * *Deployment* - the process and components used to apply a new revision
    * *Deployment Configuration* - a set of deployment rules as well as success/failure conditions used during a
    deployment
    * *AppSpec File* - defines the deployment actions you want AWS CodeDeploy to execute
    * *Revision* - everything needed to deploy the new version: AppSpec File, application files, executables, configs
    * *Application* - unique identifier for the application you want to deploy, to ensure the correct combination of
    revision, deployment configuration and deployment group are referenced during a deployment

### AppSpec File
* The `appspec` file is used to define the parameters that will be used for a CodeDeploy deployment
and the file structure will depend on if you are deploying to Lambda or EC2/On Premise

#### Appspec Lambda Deployment
* the appspec may be written in YAML or JSON and contains the following fields:
    * `version` - reserved for future use (currently allows 0.0 as latest version)
    * `resources` - the name and properties of the Lambda function to deploy
    * `hooks` - specifies **Lambda functions** to run at set points in the deployment lifecycle to
    validate deployments
        * `beforeAllowTraffic` - used to specify tasks or functions you want to run before traffic is routed to the
        newly deployed Lambda function (e.g. test that the function has been deployed correctly)
        * `afterAllowTraffic` - used to specify the tasks or functions you want to run after the traffic has been
        routed to the newly deployed Lambda function (e.g. to test that the function is accepting traffic correctly)


```yaml
version: 0.0
Resources:
  - myLambdaFunction:
      Type: AWS::Lambda::Function
      Properties:
      # the following four fields are mandatory
        Name: "myLambdaFunction"
        Alias: "myLambdaFunctionAlias"
        CurrentVersion: "1"
        TargetVersion: "2"
Hooks:
    # runs the specified Lambda before traffic is shifted to the deployed lambda function version 
    - BeforeAllowTraffic: "LambdaFunctionToValidateBeforeTrafficShift"
    # runs the specified Lambda after all traffic is shifted to the deployed Lambda function version
    - AfterAllowTraffic: "LambdaFunctionToValidateAfterTrafficShift"
```

#### Appspec EC2/On Premises deployments
* can be written in YAML or JSON
* must be placed in the root directory of your revision, otherwise deployments will fail
* `version` - reserved (allows 0.0)
* `os` - the os version you are using: either *linux* or *windows*
* `files` - location of any application files that need to be copied and where they should be copied to
* `hooks` - lifecycle event hooks allow you to specify **scripts** that need to run at set points in the deployment
lifecycle (e.g. to unzip app files prior to deployment, run functional tests, de-register/re-register instances with LB)
    1. `BeforeBlockTraffic` - run tasks on instances before they are deregistered form a load balancer
    2. `BlockTraffic` - deregister instances from a load balancer (cannot be scripted, only if using a LoadBalancer)
    3. `AfterBlockTraffic` - run tasks on instances after they are deregistered from a load balancer
    * common hooks when deploying any application (whether using a LoadBalancer or deploying direct to an instance)
        4. `ApplicationStop` - stop currently deployed app
        5. `DownloadBundle` - codeDeploy agent copies application revision to a temp location (not scriptable)
        6. `BeforeInstall`  - runs any preinstallation scripts
        7. `Install` - codeDeploy agent copies application revision files to their correct location (not scriptable)
        8. `AfterInstall` - runs any configured post-installation scripts
        9. `ApplicationStart` - restarts any services that were stopped during applicationStop
        10. `ValidateService` - runs scripts (to test) that the service is running correctly
    11. `BeforeAllowTraffic` - run tasks before instance is registered with a LoadBalancer
    12. `AllowTraffic` - register instances with a LoadBalancer (not scriptable)
    13. `AfterAllowTraffic` - run tasks on instances after they are registered with a Load Balancer
        

```yaml
version: 0.0
os: linux
files:
  - source: Config/config.txt
    destination: /webapps/Config
  - source: source
    destination: /webapps/myApp
hooks:
  BeforeInstall:
    - location: Scripts/UnzipResourceBundle.sh
    - location: Scripts/UnzipDataBundle.sh
  AfterInstall:
    - location: Scripts/RunResourceTests.sh
      timeout: 180
  ApplicationStart:
    - location: Scripts/RunFunctionalTests.sh
      timeout: 3600
  ValidateService:
    - location: Scripts/MonitorService.sh
      timeout: 3600
      runas: codedeployuser
```

## CodePipeline
* AWS's fully managed continuous integration and continuous delivery service
* allows you to model, visualize, and automate your entire release process
* CodePipeline can use all of the above mentioned AWS services (CodeCommit,CodeBuild,CodeDeploy)
    * plus Lambda, Elastic Beanstalk, CloudFormation, Elastic Container Service
    * and third party tools like GitHub and Jenkins
* CodePipeline allows you to model your release process as a workflow or pipeline made up of different tasks:
    * `CODE UPDATE -> BUILD -> TEST -> DEPLOY`
    * You define what happens and where for each of the different stages of the workflow
        * this can be modeled using the CodePipeline GUI or CLI
    * Every code change pushed to your code repository automatically enters the workflow and triggers the set of
    actions defined for each stage of the pipeline
    * the pipeline automatically stops if one of the stages fails
        * e.g. if one of your automated unit tests fails
        * this ensures that bugs are caught before the code is deployed while they are still easy to fix
        

## Developer Theory Exam tips
* CI/CD
    * read AWS's CI/CD [whitepaper](https://d1.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
    * continuous integration is **about integrating or merging code changes** frequently
        * at least once per day
        * enables multiple devs to work on the same application
    * continuous delivery is all **about automating the build, test and deployment functions**
        * human intervention is typically needed to approve code deployment
    * continuous deployment **fully automates the entire release process**
        * code is deployed into Production as soon as it has successfully passed through the release
        pipeline
* CodeCommit
    * private git repository
        * tracks and manages code changes
        * maintains version history
        * manages updates from multiple sources and enables collaboration
* CodeBuild - compiles code, rums tests, packages code
* CodeDeploy - automated deployment to EC2, on premise systems, or Lambda
    * can be used as part of a Continuous Delivery or Continuous Deployment process
    * In-Place or Rolling Update
        * application is stopped on each host and then the code is deployed
        * EC2 and on premise systems only
        * to roll-back you must re-deploy the previous version of the application
    * Blue / Green
        * new instances are provisioned and the new application is deployed to these new instances
        * traffic is routed to the new instances according to your own schedule
        * supported for EC2, on-premise, and Lambda functions
        * blue is active deployment and green is the new release
    * the `appspec` file defines all the parameters needed for the deployment
        * e.g. location of application files and pre/post deployment validation tests to run
    * For EC2/On Premise systems, the appspec.yml file must be placed in the root directory of your revision
        * **written in YAML only**
    * lambda appspec supports YAML or JSON
    * the run order for hooks in a EC2 appspec:
        * `BeforeBlockTraffic --> BlockTraffic --> AfterBlockTraffic`
        * `ApplicationStop`
        * `DownloadBundle`
        * `BeforeInstall`
        * `Install`
        * `AfterInstall`
        * `ApplicationStart`
        * `ValidateService`
        * `BeforeAllowTraffic --> AllowTraffic --> AfterAllowTraffic`
* CodePipeline
    * Continuous Integration / Continuous Delivery Service
    * automates your end-to-end software release process based on a user defined workflow
    * can be configured to automatically trigger your pipeline as soon as a change is detected in your source code
    repository
    * integrates with other services from AWS like CodeBuild and CodeDeploy, as well as third-party and custom plug-ins
    












CloudFormation
============================================================================================================
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

