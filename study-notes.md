AWS Certified Developer Associate
==============================================================

## AWS 10,000ft overview
* understand difference between a Region and Availability Zone (AZ), Edge Location
  * Region - physical location in the world consisting of 2 or more availability zones
  * Availability Zone (AZ) - one or more discrete data centers, with redundancy, housed in separate facilities
  * Edge Locations - endpoints for AWS used for caching content. These are 50+ edge locations, in data-centers,
   spread around the world.

# Identity Access Management (IAM)
centralized control to your AWS account
* IAM provides
    * shared access to your AWS account
    * Identity Federation
    * Multi-factor authentication
    * Provide temporary access for users/devices (a.k.a principals)
    * set-up password rotation policies

* Definitions
  * users - end users (people)
  * groups - collection of users under one set of permissions
  * roles - you create roles and can then assign them to AWS resources
  * policies - a document that defines one or more permissions (uses JSON). Policies define what a principal can do in
  your AWS environment.

### EXAM
* **IAM is global, not tied to any AWS Region**
* **Access Key ID and Secret Access Key are only used for programmatic access to AWS**
* **new users have no permissions when first created**


## Security Token Service (STS)
* grants users limited (and temporary) access to AWS resources
* users can come from 3 sources:
    * Federation (typically Active Directory, LDAP)
        * uses Security Assertion Markup Language (SAML)
        * grants temporary access based off of users Active Directory credentials
            * does not need to be a user in IAM
        * Single Sign On allows users to log into AWS console without assigning IAM credentials
    * Federation with mobile apps
        * uses Facebook, Amazon, Google or other OpenID providers to log-in
    * Cross account access
        * lets users from one AWS account access resources in another

### Definitions
* Federation - combining or joining a list of users in one domain (such as IAM) with a list of users in another 
domain (such as Facebook, Active Directory, LDAP)
* Identity Broker - a service that allows you to take an identity from point A and join it (federate) to point B
    * most of the time you have to develop these in-house
* Identity Store - services like Facebook,Active Directory,Google,etc...
* Identities - a user of a service (like Facebook)

#### Scenario 1 (gets user credentials from LDAP, and sends those to AWS STS)
1. Develop an Identity Broker to communicate with LDAP and AWS STS (you develop this in house)
2. Identity Broker always authenticates with **LDAP first, then with AWS STS**
3. Application then gets temporary access to AWS resources

#### Scenario 2 (gets predefined IAM role from LDAP, and uses that to work with AWS resources)
1. develop an Identity Broker to communicate with LDAP and AWS STS
2. Identity Broker always authenticates with LDAP first, gets a IAM role associated with a user (from LDAP)
3. application then authenticates with STS and assumes that IAM Role
4. application uses that IAM Role to interact with S3 (or some other AWS resource)

### Active Directory Federation (with SAML)
This is a Single Sign On scenario. A user can log into the AWS console from a link on their corporate intranet which
then transparently logs them into the AWS console. Uses SAML behind the scenes to do this
1. (E) You can authenticate with Active Directory using **SAML**
2. (E) Authenticate with Active Directory first and then get a temporary security credential

### Web Identity Federation with Mobile Applications
* need to know that you can authenticate your applications using providers such as Facebook,Google,Amazon.com,etc...
* **you'll be tested on how you are authenticating**
    * example 1
        1. log into facebook, google, etc... and receive an Access Token
        2. access token is sent to AWS to obtain temporary security credentials by making a 
        ```AssumeRoleWithWebIdentity``` request
        3. AWS returns temporary security credentials (as JSON) consisting of (Secret Key, Access Key ID, Session
        Token) with permissions defined by the access policy
        4. you can then perform actions granted by the access policy

#### Key Points
1. you authenticate first with your identity provider
2. you then get your temporary security credentials
3. then you can call ```AssumeRoleWithWebIdentity``` with temp. security credentials which allows you to perform
actions granted to that role



# EC2
### EC2 Instances
* On Demand - you pay a fixed rate, by the hour (or second) with no commitment
* Reserved - provide you with a capacity reservation and offer a significant discount on the hourly charge. 1 year or
3 year terms
* Spot - enable you to bid whatever price you want for an instance capacity, providing for greater savings if your
applications have flexible start/end times
* Dedicated Hosts - Physical EC2 server dedicated for your use. Dedicated hosts can help reduce costs by allowing you
to use your existing server bound software licenses.

### EC2 Instance Types

Family | Specialty | Use Case
:-----:|:---------:|:--------:
D2 | Dense Storage | File servers,Hadoop,Date warehousing
R4 | Memory Optimized | memory intensive apps, DBs (R = RAM)
M4 | General purpose | Application Servers
C4 | Compute Optimized | CPU intensive apps (C=CPU)
G2 | Graphics Intensive | Video Encoding, 3D app. streaming (G=graphics)
I2 | High Speed Storage | NoSQL DBs, data warehousing (I=IOPS)
F1 | Field Programmable Gate Array | hardware acceleration for your code (F=Field)
T2 | lowest cost general purpose | web servers, small DBs
P2 | graphics,general purpose GPU | machine learning, bit coins mining
X1 | Memory Optimized | SAP HANA, Apache SPARK (X=Xtreme Memory Optimized)
           
> Remember the acronym **DR MCGIF PX**

### Elastic Block Storage (EBS)
* allows you to create storage volumes and attach them to EC2 instances. Once attached you can create file systems on
top of these volumes, run a DB, or use them in any other way you would use a block device. EBS volumes are places in a
**specific availability zone (AZ)** where they are automatically replicated to protect you from the failure of a single
component
    * **these stay within an availability zone, they are not automatically replicated to different AZs

#### EBS Volume Types
* General Purpose SSD (GP2)
    * balance both price and performance
    * ratio of 3 IOPS per GB with up to 10,000 IOPS and the ability to burst up to 3000 IOPS for extended periods of
    time for volumes at 3334 GB and above
* Provisioned IOPS SSD (IO1)
    * for I/O intensive applications such as large relational/NoSQL DBs
    * use these if you need more than 10,000 IOPS
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

#### Upgrading EBS Volumes
* EBS volumes and EC2 instances have to be in **same** availability zone in order to mount them
* you can upgrade/modify EBS volumes for an EC2 instance on the fly
    * **cannot modify STANDARD MAGNETIC storage once configured for an instance**
* Exam Scenario:
    * suppose you have a EC2 instance and EBS volume in one AZ. How do you recreate the the EC2 instance and volume in
    another AZ?
        1. take a snaphot of the volume(s) and then recreate the volume(s) from the snapshot in a new AZ.
        2. attach your new EC2 instance to that volume
* How do you move an EBS volume to a new region?
    * create an image or snapshot, then use *copy* command to copy it to a new region
* snapshots are typically used for backups, Images are used to create(or recreate) new EC2 instances
* root device is where your operating system is installed
* snapshots exists on S3, they are stored there
* snapshots are incremental, only the blocks that have changed since your last snapshot are moved to S3
* to create a snapshot for EBS root volumes, you *should* stop the instance before taking the snapshot, but you *could*
take the snapshot while the instance is running
* you can create AMIs from both volumes and snapshots
* snapshots of encrypted volumes are encrypted automatically
* volumes restored from encrypted snapshots are encrypted automatically
* you can share snapshots, but only if they are unencrypted
* AMIs are regional, you can only launch an AMI from the region in which it is stored

#### EBS versus Instance Store
Instance Store is where hard disks are physically stored at/on the host computer (reduces latency)
* instance store volumes are sometimes called ephemeral storage
* instance store volumes cannot be stopped. If the underlying host fails, you lose the data
* EBS backed instances can be stopped, you will not lose the data if stopped
* you can reboot both storage types and will not lose the data
* by default both root volumes will be deleted on termination, however with EBS volumes you can tell AWS to keep the
root volume

### EC2 Security Groups
* virtual firewall
* one EC2 instance can have multiple security groups
* any change you make to a security group applies instantly
* security group rules are **stateful**, if you add an inbound rule, an outbound rule is also added
    * eg. added an inbound rule for HTTP, will also automatically add an outbound rule
* security group inbound rules **cannot block or deny any traffic**
    * they only **allow** traffic in
        * Network Access Control Lists will let you deny traffic
* inbound traffic is initially blocked by default
* all outbound traffic initially allowed by default
* any number of EC2 instances allowed within a security group

### Elastic File System (not tested)
* supports NFSv4 protocol
* you only pay for storage you use
* can scale up to petabytes
* can support thousands of concurrent NFS connections
* data is stored across multiple AZs within a region
* read after write consistency

### Identity Access Management (using Roles)
* Roles are more secure than storing your access key and secret access key on EC2 instances
* **It is possible to attach/replace a role to a running EC2 instance**
* Roles are easier to manage
* can add Policy documents to a Role at anytime, takes effect immediately
* Roles are univeral, can be used in any region

### EC2 CLI Commands for Developers
* try to learn the general syntax that the commands use when *associating* something, or *detach* something, *copy* etc...
* ```describe-instances``` - describe one or more of your EC2 instances
* ```describe-images``` - list the images available to you, includes **all** public images by default (can take time to run)
* ```start-instance``` - starts a **stopped** instance
* ```run-instances``` - creates one or more EC2 instances using an AMI

### EC2 Instance Meta-Data
let's you get meta-data about the currently running EC2 instance
* know the URL used to access instance meta-data (know the IP address:)
    * **http://169.254.169.254/latest/meta-data/**
* know how to get public IPV4 address using above URL:
    * **http://169.254.169.254/latest/meta-data/public-ipv4**

### Elastic Load Balancers (ELB)
* know about the newer Application Load Balancer versus Classic Load Balancer
* once an EC2 instance is out of service, the ELB will not send traffic to that instance
* Application Load Balancer gives you a domain name to use, not a public IP, amazon manages the IPs
    * you could register your own domain name to point to the ELB domain
* **one subnet = one availability zone**
* application load balancer operates at layer 7, classic operates at layer 4
* health checks...check the instance help by talking to it

### AWS SDKs
* Available SDKs:
    * Android
    * iOS
    * Java
    * .Net
    * Node.js
    * PHP
    * Python
    * Ruby
    * GO
    * C++
    * AWS Mobile SDK, AWS IoT Device SDK
* default region for (some) SDKs is **US-EAST-1**

### Lambda
Lambda is a compute service where you can upload your code and create a Lambda function. Lambda takes care of
provisioning and managing the servers you need to run your code. You don't have to worry about operating systems,
patching, scaling etc...
* Lambda encapsulates:
    * Data Centers, Hardware, Protocols, High Level Languages, Operating Systems, Application Later/AWS APIs

* You can use Lambda in the following ways:
    * As an **event driven** compute service where Lambda runs your code in response to events
        * Event Triggers:
            * API Gateway
            * AWS:IoT
            * Alexa Skills Kit
            * CloudFront
            * CloudWatch Events
            * CloudWatch Logs
            * CodeCommit
            * Cognito Sync Trigger
            * DynamoDB
            * S3
            * SNS
            * Kinesis (Capture, process, and store video streams for analytics and machine learning)
    * as a **compute service** to run code in response to HTTP requests using Amazon API Gateway or API calls made
    using AWS SDKs
        * every HTTP request invokes a new (separate) Lambda function
* Lambda can do things globally, you can use it to back-up S3 buckets to other buckets etc...
* Lambda functions scale out automatically
* Supported languages for developing Lambda functions:
    * C#,GO,Java,NodeJS,Python
* Your lambda function cannot execute for more than **5 minutes**
* Lambda functions are independent, 1 event = 1 function
* Lambda functions can trigger other Lambda functions, 1 event can trigger X other functions
* know what AWS services are serverless: S3,API Gateway,Lambda,DynamoDB etc...
    * EC2 is NOT serverless because YOU are managing and configuring server instances


     
### Exam Tips
* some exam questions will present different scenarios and ask you to pick cheapest/best option
* know differences between On Demand, Spot, Reserved, Dedicated Hosts
* If AWS terminates the spot instance, you get the hour for free

* EBS Consists of

type | name | code | description
:---:|:---:|:---:|:---:
SSD|General Purpose|GP2|up to 10,000 IOPS
SSD|Provisioned IOPS|IO1|more than 10,000 IOPS
HDD|Throughput Optimized|ST1|frequently accessed workloads
HDD|Cold|SC1|less frequently accessed data
HDD|Magnetic|Standard|cheap,infrequently accessed storage

* you cannot mount 1 EBS volume to multiple EC2 instances, instead use Elastic File Service (EFS)
* **remember that one subnet equals one availability zone, subnets cannot span availability zones**
* termination protection is turned off by default, you must explicitly turn it on
* on an EBS backed instance, the default action is for the root EBS volume to be deleted when the instance is terminated
* EBS root volumes of your AMIs cannot be encrypted. You can use a third party tool (like BitLocker) to encrypt the
root volume after creation of the volume
    * additional volumes can be encrypted


# Simple Storage Service (S3)
Simple storage service provides secure, durable, highly-scalable object storage. The date is spread across multiple
devices and facilities

### S3 Basics
* S3 is object based (allows you to upload files)
* Files can be from 0 Bytes to 5TB
* files are stored in buckets
* S3 is a universal namespace, names must be unique globally
* S3 will automatically creates a DNS for your bucket
    * https://s3[-us-east-1].amazonaws.com/yourbucketname
        * part in square brackets my no longer by needed
    * buckets are a universal namespace, cannot have duplicate bucket name across regions
* when you successfully upload an object, you'll receive a HTTP 200 code
* Built for 99.99% availability
* Built for 99.999999999% durability (the eleven nines guarantee)
* Tiered storage is available
* Lifecycle management
* Versioning
* Encryption
* you control access to your buckets using Access Control Lists (ACL) or Bucket Policies
* by default, buckets and all objects stored in them are private

### Data Consistency Model
* **read after write consistency** for PUTS of new objects
    * you'll be able to read the object immediately
* Eventual consistency for **overwrite PUTS and DELETES (can take time to propagate)**
* updates are **atomic** you'll either get the new data or else the old data

### S3 is a simple key/value store
* Key - the name of the object
    * stored lexigraphically
    * to improve performance you may want to add random letters to beginning of file name in order to spread your files
    across S3 storage
* Value - the object data (a sequence of bytes)
* VersionID
* Meta-Data
* Sub-resources
    * access control list
    * ability to Torrent the file

### S3 Storage Tiers/Classes
* **S3** (aka S3 Standard)
    * 99.99% availability and 99.999999999% durability
    * designed to withstand loss of 2 facilities concurrently
    * minimum object size 0KB
* **S3-IA** (infrequently accessed)
    * for data accessed less frequently but requiring rapid access when needed
    * Lower fee than S3 but charged a retrieval fee
    * **minimum object size 128KB**
* **Reduced Redundancy Storage**
    * designed to provide **99.99% durability** and 99.99% availability of objects over a given year
    * useful for objects you can recreate (like thumbnail images)
    * lower costs than options above
* Glacier
    * very cheap but used for archival only
    * Takes 3-5 hours to restore

### S3 Encryption
* two main types of encryption
    * Client Side encryption
        * data is encrypted by owner before storing on S3
    * Server-Side encryption:
        * using Amazon S3 Managed Keys (SSE-S3)
        * using KMS (SSE-KMS)
        * using customer provided keys

### Static Website Hosting
* S3 can be used to host files for a **static** website
    * no PHP,asp,servlets, etc, allowed
* S3 will create a publicly available DNS for your site
    * http://**bucketname**.s3-website-**us-east-1**.amazonaws.com
        * NOTE that the static website names have *s3-website* in the name
* What is a good scenario for static website hosting?
    * a static site that needs to be scalable and handle lots of requests
        * like a site for a major blockbuster movie release

#### Cross Origin Resource Sharing (CORS)
* way of allowing resource/code in one S3 bucket to access a resource in another S3 bucket
* have to enable CORS in the **bucket that is being called** (the bucket containing the resource) and then configure
the URL for the origin that will be calling the bucket

### S3 Versioning
* **Once versioning is enabled for a bucket, it cannot be disabled**
* stores all versions of an object (including writes)
* integrates with lifecycle rules
* Multi-Factor Authentication delete capability is available for extra layer of security

### Cross Region Replication
* deleting object in source bucket **does not delete** it in replicated bucket
* **versioning must be enabled on both source and destination buckets**
* Regions must be unique
* files in an **existing** bucket are not replicated automatically
    * all subsequent uploaded files will be replicated automatically
* you cannot replicate to multiple buckets or use daisy chaining (at this time)
* understand what cross region replication is at a high level

### Lifecycle management S3-IA and Glacier
* can be used in conjunction with versioning
* can be applied to current versions and previous versions
* the following actions can now be done with lifecycle management:
    1. Transition to Standard-IA
        * 128KB min file size
        * 30 days after creation
    2. Archive to Glacier storage
        * 30 days after being stored in Standard-IA
    3. permanently delete objects


### Cloudfront Overview
AWS CloudFront is a Content Delivery Network (CDN):
* a system of distributed servers that deliver webpages and other web content to a user based on the geographic
locations of the user, the origin of the webpage and a content delivery server

* *Edge Location*
    * the location where content will be cached. This is separate to an AWS Region/AZ
    * edge locations are not just **Read Only**, you can write to them too
    * objects are cached for the life of the Time-To-Live (TTL)
    * you can force a clear of the cached objects, but you will be charged
* *Origin*
    * this is the origin of all files that the CDN will distribute. Cloudfront is optimized to work with:
        * S3 bucket
        * EC2 instance
        * Elastic Load Balancer
        * Route53
    * **origin can also be a non-AWS server** (e.g. a web-server you own on-premise)
* *Distribution*
    * the name given to the CDN which consists of a collection of edge locations
        * you can have multiple origins

#### CloudFront Key Terminology
* **Web Distribution** - typically used for websites
* **RTMP** - used for media streaming

#### Creating a CDN (done in a lab)
* know that you can configure the Default TTL (it defaults to 24 hours), you can change it if you have content that
needs to expire quickly
* you can restrict use access to content using **Signed URLs** or **Signed Cookies**
* Geographic Restrictions
    * you can whitelist and blacklist geographic locations (by country) so that they can/cannot view your content
* Invalidations
    * removes objects from the CloudFront edge caches.
    * scenario: you need to immediately remove something from the cache and can't wait for the TTL to expire it

### S3 Security and Encryption
* all newly created buckets are private
* You can setup access control to your buckets using:
    * Bucket Policies
        * applied to entire bucket
    * Access Control Lists
* S3 buckets can be configured to create access logs which will log all requests made to the S3 bucket

#### Encryption
Types of Encryption in S3
* *In-Transit* encryption
    * when sending data across the wire to the bucket
    * uses SSL/TLS (e.g. HTTPS)
* *At Rest* encryption
    * Server Side Encryption 
        * **S3 Managed Keys (SSE-S3)**
            * uses AES-256
            * Amazon handles the keys for you
        * **AWS Key Management Service (SSE-KMS)**
            * similar to SSE-S3 but more costly
            * managed by Amazon and also provides an audit trail (who used keys to access what)
        * **Customer Provided Keys (SSE-C)**
            * you manage encryption keys
* *Client Side Encryption*
    * you encrypt data on your client then upload it to S3

### Storage Gateway
A service that connects an on premises software appliance with AWS cloud-based storage. This service allows you to
securely store data to the AWS cloud for scalable and cost effective storage. There are 4 types of Storage Gateway
offered.

#### For Exam
* **File Gateway**
    * for flat files stored on S3
* **Volume Gateway**
    * block storage (uses iSCSI)
    * two types of volume gateway offered:
        * **Stored Volumes**
            * entire dataset is stored onsite and is asynchronously backed up to S3
        * **Cached Volumes**
            * entire dataset is in S3, and only most frequently accessed data is cached onsite
* **Gateway Virtual Tape Library (VTL)**
    * for backup, but uses popular backup appliances like NetBackup,Backup Exec, Veam, etc....
* Exam may present you with scenario questions and ask you to choose the best option from among the 4 gateway types


### Snowball
A physical data transfer device, for transporting data to Amazon, so they can store it AWS. It looks like a cube style
PC case with handles on the top. Snowball can import data to/from S3

* Replacement for Amazon's *import/export* service
    * customers would send in (mail) all manner of hard-drives and tapes for amazon to backup for them
    * all the different types of hardware was a nightmare for Amazon to manage
* 3 Types of Snowballs offered:
    * Snowball
        * just onboard storage
    * Snowball Edge
        * onboard storage plus compute capacity
        * like a mini data-center
        * can run lambda from them
    * Snowmobile
        * peta byte or exo-byte levels of data
        * this is driven to your site on a 18-wheeler truck

### S3 Transfer Acceleration
* Utilizes CloudFront edge network to accelerate your uploads to S3. 
* Instead of uploading directly to an S3 bucket, you can use a distinct URL to upload to an edge location, 
which will in turn transfer to S3. 
* Remember **You get a distinct URL for uploading to your bucket**

### S3 Section Notes
* S3   website URL: http://mybucketname.**s3-website**.us-east-1.amazonaws.com
* direct S3 bucket: http://s3.[us-east-1].amazonaws.com/mybucketname
* You can upload big files to S3 using multi-part upload
    * 5MB to 5TB in size
* You can upload up to **5GB** in a **single operation**


# Databases 101
AWS offers the following Database (DB) services:
* RDS
    * Relational Database
    * Suitable for Online Transaction Processing (OLTP)
    * AWS offers the following implementations
        * Microsoft SQL Server
        * Oracle
        * MySQL
        * MariaDB
        * PostgreSQL
        * Aurora (Amazon developed DB)
* DynamoDB
    * NoSQL Database
    * covered heavily in Developer Associate Exam
* ElastiCache
    * in memory cache service
    * AWS offers the following implementations
        * memcache
        * redis
* Redshift
    * data warehousing DB
    * used for Online Analytics Processing (OLAP)
    
### Data Warehousing
* used for business intelligence in order to analyze large amounts of data (OLAP)
* Online Analytics Processing (OLAP)
    * eg. net profit for EMEA and Pacific for some product
* Data warehousing DBs use different types of architecture, both from a DB perspective and infrastructure layer

### ElastiCache
* Exam Scenario question: you may get a question asking how to improve DB performance for frequently accessed queries.
The answer could be to setup ElastiCache to cache those queries

### Data Migration Services (DMS)
* Service that migrates your production DB to AWS
* can migrate a legacy DB (eg Oracle) to a new DB vendor (eg MySQL)
* can be done while your production DB is running
* handles the conversion of Schemas, views, stored procedures, etc.. to the target DB format



# Dynamo DB
A fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency
 at any scale. It is a fully managed DB and supports both document and key-value data models. Its flexible data
 model and reliable performance make it a great fit for mobile, gaming, web etc...

### DynamoDB 101
* stored on SSDs
* spread across three facilities in an AWS Region
* Eventually Consistent Reads (default) 
    * best read response
    * consistency across all copies of data is usually reached within a second
    * repeating a read after a short time should return the updated data
* Strongly Consistent Reads
    * slower read performance
    * returns a result that reflects all writes that received a successful response prior to the read
* (E) you can export a DynamoDB table (or selected items) to a .CSV file
* can update your DynamoDB table capacity on the fly
    * "push button capacity scaling"
* DynamoDB supports nested attributes up to 32 levels deep.


### DynamoDB Data Model
* Tables
    * a collection of Items
* Items
    * a collection of Attributes
    * like a row of data in a RDB
    * aggregate size of an item, including all the attribute names and attribute values, **cannot exceed 400KB**
* Attributes
    * like columns of data in a RDB
            

### DynamoDB Key Types
* Primary Keys (two types)
    * Single Attribute key
        * consists of a unique ID
        * *Partition Key* (most common key type)
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

### DynamoDB Indexes
Many applications might benefit from having one or more **secondary** (or alternate) keys available to allow efficient 
access to data with attributes other than the primary key. To address this, you can create one or more secondary 
indexes on a table. Dynamo Supports two types:
* (E) Local Secondary Index (LSI)
    * has the **same** partition key as the table but different sort key
    * can **only** be created when creating a table
    * cannot be removed or modified after table creation
    * can create a maximum of 5 local secondary indexes per table
    * A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a 
    table partition that has the same partition key
    * LSIs limit the total size of all elements (tables and indexes) to 10 GB per partition key value
* (E) Global Secondary Index (GSI)
    * an index with a partition or a partition-and-sort key that can be **different** from those already on the table
    * can be created at table creation time or added later
    * can create a maximum of 5 global secondary indexes per table
    * GSIs do not enforce data co-location, or a maximum partition size (unlike LSIs)
    * A global secondary index is considered "global" because queries on the index can span all items in a table, 
    across all partitions

### DynamoDB Streams
* captures any kind of modification of the DynamoDB tables
    * if a new item is added to the table, the stream captures an image of the entire item, including all its attributes
    * if an item is updated, the stream captures the *before* and *after* image of any attributes that were modified
    in the item
    * if any item is deleted from the table, the stream captures an image of the entire item before it was deleted
* data is stored in DynamoDB streams for 24 hours
    * you can process the data during this time using e.g. Lambda

### DynamoDB Query vs Scan
#### Queries
* (E) a query operation finds items in a table using only primary key attribute values. You must provide a partition
attribute name and distinct value to search for.
* you can optionally provide a sort key attribute name and value and use a comparison operator to refine search results
* (E) By default, a query returns all of the data attributes for items with the specified primary keys; however, you can
use ```ProjectionExpression``` parameter so that the query only returns some of the attributes rather than all of them
* (E) Query results are always sorted by the sort key. If the data type of the sort key is a number, the results are
returned in numeric order; otherwise, the results are returned in order of the ASCII character code values. By default
the sort order is ascending, to reverse the order, set the ```ScanIndexForward``` parameter to **false**
* by default, queries are always eventually consistent but can be changed to strongly consistent

#### Scans
* (E) a scan operation examines every item in the table and returns all of the data attributes for every item. However,
you can use the ```ProjectionExpression``` parameter so that the scan only returns some of the attributes, rather than
all of them

#### What to use? Query vs Scan?
* Generally a query is more efficient than a Scan
* A Scan always scans the entire table then filters out values to provide the desired result, eventually adding the
extra step of removing data from the result set. Avoiding using scan operation on a large table with a filter that
removes many results, if possible. Also, as a table grows, the scan slows. The scan operation examines every item for
the requested values and can use up the provisioned throughput for a large table in a single operation
* For quicker response times, design your tables in a way that can use the ```Query```,```Get```, or ```BatchGetItem``` APIs.
Alternatively, design you application to use scan operations in a way that minimized impact on your tables request rate


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

### Using Web Identity Providers with DynamoDB
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