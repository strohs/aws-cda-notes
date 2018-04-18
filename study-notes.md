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


Security Token Service (STS)
==================================================================
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

## Definitions
* Federation - combining or joining a list of users in one domain (such as IAM) with a list of users in another 
domain (such as Facebook, Active Directory, LDAP)
* Identity Broker - a service that allows you to take an identity from point A and join it (federate) to point B
    * most of the time you have to develop these in-house
* Identity Store - services like Facebook,Active Directory,Google,etc...
* Identities - a user of a service (like Facebook)

## Identity Federation Options
### Federating Users of a Mobile or Web-based App with Amazon Cognito
### Federating Users with Public Identity Service Providers or OpenID Connect
### Federating users with SAML 2.0
* This feature enables federated single sign-on (SSO), so users can log into the AWS Management Console or call the
 AWS APIs without you having to create an IAM user for everyone in your organization. By using SAML, you can simplify 
 the process of configuring federation with AWS, because you can use the IdP's service instead of writing custom 
 identity proxy code. 
1. (E) You can authenticate with Active Directory using **SAML2.0**
2. (E) Authenticate with Active Directory first and then get a temporary security credential

### Federating users by creating a custom identity broker application
* If your identity store is not compatible with SAML 2.0, then you can build a custom identity broker application 
to perform a similar function. The broker application authenticates users, requests temporary credentials for 
users from AWS, and then provides them to the user to access AWS resources.
* To get **temporary** security credentials, the identity broker application calls either ```AssumeRole``` or 
```GetFederationToken``` to obtain temporary security credentials, depending on how the developer wants to manage the 
policies for users and when the temporary credentials should expire
    * ```GetFederationToken``` - Returns a set of temporary security credentials (consisting of an access key ID, 
    a secret access key, and a security token) call this using the long-term security credentials of an IAM user, 
    this call is appropriate in contexts where those credentials can be safely stored, usually in a server-based 
    application
    * ```AssumeRole``` - Returns a set of temporary security credentials (consisting of an access key ID, a secret 
    access key, and a security token) that you can use to access AWS resources that you might not normally have 
    access to. Typically, you use AssumeRole for cross-account access or federation
 
#### Scenario 1 (gets user credentials from LDAP, and sends those to AWS STS)
1. Develop an Identity Broker to communicate with LDAP and AWS STS (you develop this in house)
2. Identity Broker always authenticates with **LDAP first, then with AWS STS**
3. Application then gets temporary access to AWS resources

#### Scenario 2 (gets predefined IAM role from LDAP, and uses that to work with AWS resources)
1. develop an Identity Broker to communicate with LDAP and AWS STS
2. Identity Broker always authenticates with LDAP first, gets a IAM role associated with a user (from LDAP)
3. application then authenticates with STS and assumes that IAM Role
4. application uses that IAM Role to interact with S3 (or some other AWS resource)




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



EC2
=====================================================================
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
D2 | HDD Dense Storage | File servers,Hadoop,Date warehousing,Massively Parallel Processing (MPP) 
H1 | HDD Storage | MapReduce-based workloads, distributed file systems such as HDFS
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


Simple Storage Service (S3)
=============================================================
Simple storage service provides secure, durable, highly-scalable object storage. The data is spread across multiple
devices and facilities

## S3 Basics
* (E) S3 is object based (allows you to upload files)
* (E) Files can be from **0 Bytes to 5TB**
* (E) there is unlimited storage, AWS will add more storage as needed
* (E) files are stored in buckets
* (E) S3 is a universal namespace, **names must be unique globally**
* (E) S3 will automatically creates a DNS for your bucket
    * https://s3[-us-east-1].amazonaws.com/yourbucketname
        * part in square brackets my no longer by needed
    * buckets are a universal namespace, cannot have duplicate bucket name across regions
* (E) when you successfully upload an object, you'll receive a HTTP 200 code
* (E) Built for 99.99% availability
* (E) Built for 99.999999999% durability (the eleven nines guarantee)
* Tiered storage is available
* Lifecycle management
* Versioning
* Encryption
* you control access to your buckets using:
    * Access Control Lists (ACL)
        * can be applied at the object level
        * A document that defines who can access a particular bucket or object. Each bucket and object in Amazon 
        S3 has an ACL. The document defines what each type of user can do, such as write and read permissions.
    * Bucket Policies
        * bucket policies can only be applied at the bucket level
        * A bucket policy is a resource-based AWS Identity and Access Management (IAM) policy that grants other AWS 
        accounts or IAM users access to an S3 bucket. Bucket policies supplement, and in many cases, replace 
        ACL-based access policies
* (E) by default, buckets and all objects stored in them are private

## Data Consistency Model
* (E) **read after write consistency** for PUTS of *new objects*
    * you'll be able to read the object immediately
* (E) Eventual consistency for **overwrite PUTS and DELETES (can take time to propagate)**
* (E) updates are **atomic** you'll either get the new data or else the old data

## S3 is a simple key/value store
(E) 
* Key - the name of the object
    * stored lexicographically
    * to improve performance you may want to add random letters to beginning of file name in order to spread your files
    across S3 storage
* Value - the object data (a sequence of bytes)
* VersionID
* Meta-Data
* Sub-resources
    * access control list
    * ability to Torrent the file

## S3 Storage Tiers/Classes
(E) 
* **S3** (aka S3 Standard)
    * 99.99% availability and 99.999999999% durability
    * designed to withstand loss of 2 facilities concurrently
    * **minimum object size 0KB**
* **S3-IA** (infrequently accessed)
    * for data accessed less frequently but requiring rapid access when needed
    * Lower fee than S3 but charged a retrieval fee
    * 99% Availability
    * 99.999999999% durability
    * **minimum object size 128KB**
* **S3 One Zone-IA** (S3 One Zone-Infrequent Access)
    * stores data in a single AZ
        * therefore not resilient to the loss of the AZ
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

## S3 Encryption
* two main types of encryption
    * Client Side encryption
        * data is encrypted by owner before storing on S3
    * Server-Side encryption:
        * using Amazon S3 Managed Keys (SSE-S3)
        * using KMS (SSE-KMS)
        * using customer provided keys

## Static Website Hosting (E) 
* S3 can be used to host files for a **static** website
    * no PHP,asp,servlets, etc, allowed
* S3 will create a publicly available DNS for your site
    * http://**bucketname**.s3-website-**us-east-1**.amazonaws.com
        * NOTE that the static website names have *s3-website* in the name
* What is a good scenario for static website hosting?
    * a static site that needs to be scalable and handle lots of requests
        * like a site for a major blockbuster movie release

### Cross Origin Resource Sharing (CORS) (E) 
* way of allowing resource/code in one S3 bucket to access a resource in another S3 bucket
* have to enable CORS in the **bucket that is being called** (the bucket containing the resource) and then configure
the URL for the origin that will be calling the bucket

## S3 Versioning
* (E) **Once versioning is enabled for a bucket, it cannot be disabled**
* stores all versions of an object (including writes)
* integrates with lifecycle rules
* Multi-Factor Authentication delete capability is available for extra layer of security

## Cross Region Replication (E) 
* deleting object in source bucket **does not delete** it in replicated bucket
* **versioning must be enabled on both source and destination buckets**
* Regions must be unique
* files in an **existing** bucket are not replicated automatically
    * all subsequent uploaded files will be replicated automatically
* you cannot replicate to multiple buckets or use daisy chaining (at this time)
* understand what cross region replication is at a high level

## Lifecycle management S3-IA and Glacier (E) 
* can be used in conjunction with versioning
* can be applied to current versions and previous versions
* the following actions can now be done with lifecycle management:
    1. Transition to Standard-IA
        * 128KB min file size
        * 30 days after creation
    2. Archive to Glacier storage
        * 30 days after being stored in Standard-IA
    3. permanently delete objects


## CloudFront Overview
AWS CloudFront is a Content Delivery Network (CDN):
* a system of distributed servers that deliver webpages and other web content to a user based on the geographic
locations of the user, the origin of the webpage and a content delivery server

* (E) *Edge Location*
    * the location where content will be cached. This is separate to an AWS Region/AZ
    * edge locations are not just **Read Only**, you can write to them too
    * objects are cached for the life of the Time-To-Live (TTL)
    * you can force a clear of the cached objects, but you will be charged
* (E) *Origin*
    * this is the origin of all files that the CDN will distribute. Cloudfront is optimized to work with:
        * S3 bucket
        * EC2 instance
        * Elastic Load Balancer
        * Route53
    * **origin can also be a non-AWS server** (e.g. a web-server you own on-premise)
* (E) *Distribution*
    * the name given to the CDN which consists of a collection of edge locations
        * you can have multiple origins

### CloudFront Key Terminology (E) 
* **Web Distribution** - typically used for websites
* **RTMP** - used for media streaming

### Creating a CDN (lab)  (E) 
* know that you can configure the Default TTL (it **defaults to 24 hours**), you can change it if you have content that
needs to expire quickly
* you can restrict use access to content using **Signed URLs** or **Signed Cookies**
* Geographic Restrictions
    * you can whitelist and blacklist geographic locations (by country) so that they can/cannot view your content
* Invalidations
    * removes objects from the CloudFront edge caches.
    * scenario: you need to immediately remove something from the cache and can't wait for the TTL to expire it

## S3 Security and Encryption (E) 
* all newly created buckets are private
* You can setup access control to your buckets using:
    * Bucket Policies
        * applied to entire bucket
    * Access Control Lists
* S3 buckets can be configured to create access logs which will log all requests made to the S3 bucket

### Encryption
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
            * offers more security (key envelopes)
            * managed by Amazon and also provides an audit trail (who used keys to access what)
        * **Customer Provided Keys (SSE-C)**
            * you manage encryption keys
* *Client Side Encryption*
    * you encrypt data on your client then upload it to S3

## Storage Gateway
* A service that connects an on premises software appliance with AWS cloud-based storage. This service allows you to
securely store data to the AWS cloud for scalable and cost effective storage. There are 4 types of Storage Gateway
offered.
    * File Gateway
    * Volume Gateway Stored Volumes
    * Volume Gateway Cached Volumes
    * Gateway Virtual Tape Library


### For Exam
* **File Gateway**
    * for flat files stored on S3
    * accessed through a NFS mount point
* **Volume Gateway**
    * block storage (uses iSCSI)
    * two types of volume gateway offered:
        * **Stored Volumes**
            * entire dataset is stored on-site and is asynchronously backed up to S3
        * **Cached Volumes**
            * entire dataset is in S3, and only most frequently accessed data is cached on-site
* **Gateway Virtual Tape Library (VTL)**
    * for backup, but uses popular backup appliances like NetBackup,Backup Exec, Veam, etc....
* Exam may present you with scenario questions and ask you to choose the best option from among the 4 gateway types


## Snowball
* A physical data transfer device, for transporting data to Amazon, so they can store it AWS. It looks like a cube style
PC case with handles on the top. 
* Snowball can import data to/from S3

* Replacement for Amazon's *import/export* service
    * customers would send in (mail) all manner of hard-drives and tapes for amazon to backup for them
    * all the different types of hardware was a nightmare for Amazon to manage

* (E) 3 Types of Snowballs offered:
    * Snowball
        * just onboard storage
    * Snowball Edge
        * onboard storage plus compute capacity
        * like a mini data-center
        * can run lambda from them
    * Snowmobile
        * peta byte or exo-byte levels of data
        * this is driven to your site on a 18-wheeler truck

## S3 Transfer Acceleration
* Utilizes CloudFront edge network to accelerate your uploads to S3. 
* Instead of uploading directly to an S3 bucket, you can use a distinct URL to upload to an edge location, 
which will in turn transfer to S3. 
* Remember **You get a distinct URL for uploading to your bucket**

## S3 Section Notes
* S3   website URL: http://mybucketname.**s3-website**.us-east-1.amazonaws.com
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


Databases 101
==========================================
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
* (E) Exam Scenario question: you may get a question asking how to improve DB performance for frequently accessed queries.
The answer could be to setup ElastiCache to cache those queries

### Data Migration Services (DMS)
* Service that migrates your production DB to AWS
* can migrate a legacy DB (eg Oracle) to a new DB vendor (eg MySQL)
* can be done while your production DB is running
* handles the conversion of Schemas, views, stored procedures, etc.. to the target DB format



Dynamo DB
=========================================================
A fast and flexible NoSQL database service for all applications that need consistent, single-digit millisecond latency
 at any scale. It is a fully managed DB and supports both document and key-value data models. Its flexible data
 model and reliable performance make it a great fit for mobile, gaming, web etc...

## DynamoDB 101
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

## DynamoDB Indexes
Many applications might benefit from having one or more **secondary** (or alternate) keys available to allow efficient 
access to data with attributes other than the primary key. To address this, you can create one or more secondary 
indexes on a table. Dynamo Supports two types:
* (E) Local Secondary Index (LSI)
    * has the **same** partition key as the table but different sort key
    * can **only** be created when creating a table
    * cannot be removed or modified after table creation
    * can create a maximum of 5 local secondary indexes per table
    * All scalar data types (Number, String, Binary) can be used for the sort key element of the local secondary 
    index key
    * LSIs limit the total size of all elements (tables and indexes) to **10 GB per partition key value**
* (E) Global Secondary Index (GSI)
    * an index with a partition or a partition-and-sort key that can be **different** from those already on the table
    * can be created at table creation time or added later
    * can create a maximum of 5 global secondary indexes per table
    * GSIs do not enforce data co-location, or a maximum partition size (unlike LSIs)


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
* (E) a query operation finds items in a table using only primary key attribute values. You must provide a partition
attribute name and distinct value to search for.
    * you can optionally provide a sort key attribute name and value and use a comparison operator to refine 
    search results
* (E) By default, a query returns all of the data attributes for items with the specified primary keys; however, you can
use ```ProjectionExpression``` parameter so that the query only returns some of the attributes rather than all of them
* (E) Query results are always sorted by the sort key. If the data type of the sort key is a number, the results are
returned in numeric order; otherwise, the results are returned in order of the ASCII character code values. By default
the sort order is ascending, to reverse the order, set the ```ScanIndexForward``` parameter to **false**
* by default, queries are always eventually consistent but can be changed to strongly consistent
    * in java we use: ```.withConsistentRead(true)```

### Scans
* (E) a scan operation examines every item in the table and returns all of the data attributes for every item. However,
you can use the ```ProjectionExpression``` parameter so that the scan only returns some of the attributes, rather than
all of them

### What to use? Query vs Scan?
* Generally a query is more efficient than a Scan
* A Scan always scans the entire table then filters out values to provide the desired result, eventually adding the
extra step of removing data from the result set. Avoiding using scan operation on a large table with a filter that
removes many results, if possible. Also, as a table grows, the scan slows. The scan operation examines every item for
the requested values and can use up the provisioned throughput for a large table in a single operation
* For quicker response times, design your tables in a way that can use the ```Query```,```Get```, or ```BatchGetItem``` 
APIs. Alternatively, design you application to use scan operations in a way that minimized impact on your tables 
request rate


## DynamoDB Provisioned Throughput Calculations
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
    * you want a partition key that has a large number of distinct values and that are requested fairly uniformly
* initial limit of 256 tables per region, can be upped by contacting Amazon support




Simple Queue Service (SQS)
==============================================================================================================
## Overview
* SQS is a web service that gives you access to a message queue that can be used to store messages while waiting for a 
computer to process them. 
* SQS is a distributed queue system that enables web service applications to quickly and reliably queue messages that 
component in the application generates to be consumed by another component. A queue is a temporary repository for 
messages that are awaiting processing.
* Using SQS, you can decouple the components of an application so they run independently, with Amazon SQS easing 
message management between components. Any component of a distributed application can store messages in a fail-safe 
queue
* (E) Messages can contain of to **256KB** of text in any format. Any component can later retrieve the messages 
programmatically using the SQL API.
* The queue acts as a buffer between the component producing and saving data, and the component receiving the data
for processing. This means the queue resolves issues that arise if the producer is producing work faster than 
the consumer can process it, or if the producer or consumer are only intermittently connected to the network
* SQS ensures delivery of each message at least once, and supports multiple readers and writers interacting with
the same queue
* A single queue can be used simultaneously by many distributed application components, with no need for those
components to coordinate with each other to share the queue

## Queue Types
### Standard Queues
* unlimited throughput
    * nearly unlimited number of transactions per second per API action
* At-least-once delivery 
    * a message is delivered at least once, but occasionally more than once copy of a message is delivered
* Best-Effort-Ordering
    * occasionally messages might be delivered in an order different from which they were sent
* If your system requires that order be preserved, you can place sequencing information in each message, so that
you can reorder the messages when the queue returns them. Or see if you can use a FIFO queue

### FIFO Queues
Used when the **order** of operations and events is critical. FIFO queues not available in all regions
* High Throughput
    * support **300 messages per second** (300 send,receive,or delete operations per second)
    * when you **batch 10 messages per operation**, FIFO queues can support up to **3,000 messages per second**
* **Exactly Once Processing**
    * a message is delivered once and remains available until a consumer processes and deletes it. Duplicates are
    not introduced into the queue
* **First-in-First-out delivery**
    * the order in which messages are sent and received is strictly preserved
* cannot convert existing standard queues into FIFO queues

#### Dead Letter Queues
* a type of Standard Queue or FIFO Queue that contains messages that failed a certain number of processing attempts
* you use dead-letter queues to isolate these messages for further analysis



### Example
* suppose you have a number of image files to encode. In an SQS worker queue you create an Amazon SQS message for
each file specifying the command (jpeg-encode) and the location of the file in Amazon S3
* A pool of Amazon EC2 instances running the needed image processing software does the following:
    1. Asynchronously **pulls** task messages from the queue
        * (E) The **visibility timeout clock** starts once the message is pulled by the worker
    2. Retrieves the named file
    3. Does the conversion of the image
    4. Writes the image back to Amazon S3
    5. Writes a *task complete* message to another queue
    6. Deletes the original task message
        * (E) The message is considered successfully processed once this deletion step occurs
        * If the deletion step does not happen, then the visibility timeout clock will expire and the message will
        become re-available for processing by another worker
    7. Checks for more messages in the worker queue

### Billing
* Billed at 64KB *chunks*
    * message size will be 4 * 64KB chunks
    * Each 64KB chunk of payload is billed as 1 request. For example, a single API call with a 256KB payload will be
    billed as 4 requests
* SQS Free Tier provides you with **1 million requests per month** at no charge

## SQS Basic API Calls
* ```SendMessage``` - send a message to a specified queue
    * ```SendMessageBatch```
* ```ReceiveMessage``` - call this to get a single message from a queue
* ```DeleteMessage``` - call this once your code in done processing the message
    * ```DeleteMessageBatch```
* ```ChangeMessageVisibility``` - changes the message visibility timeout of a received message
* ```PurgeQueue``` - delete all messages in a queue


## SQS Developer Exam Tips
* SQS can be auto-scaled
* SQS Standard Queues are engineered to provide **at least once** delivery of all messages in its queues. Although 
most of the time each message will be delivered to your application exactly once, you should design your system so 
that processing a message more than once does not create any errors or inconsistencies
* A SQS message can include only XML, JSON, and unformatted text
* message size is **256KB**
    * To send messages larger than 256KB, use the Amazon SQS Extended Client Library for Java
* A single Amazon SQS message queue can contain an unlimited number of messages
    * there is a **120,000 limit for the number of in-flight messages for a standard queue**
    * **20,000 for a FIFO queue**
* You can create any number of message queues
* SQS Messages can be delivered multiple times and **in any order**
    * if you need priority of one type of message over another, you would create a separate queue for those priority
    messages
* Visibility Timeout
    * a period of time during which Amazon SQS prevents other consuming components from receiving and processing a message
    * **12 hours visibility is MAXIMUM** time out (default is **30 seconds**)
        * If the default timeout is insufficient for processing a particular message, you can call 
        ```ChangeMessageVisibility``` action to specify a new timeout value. SQS will restart the timeout period using 
        the new value
* Long Polling
    * ```ReceiveMessage( WaitTimeSeconds )``` - API call to enable long polling, must set WaitTimeSeconds parameter 
        * **MAX poll wait time = 20 seconds**
    * SQS Long polling is a way to retrieve messages from your SQS queues. While the traditional SQS short polling returns
    immediately, even if the queue being polled is empty, SQL long polling doesn't return a response until a message
    arrives in the queue, or the long poll times out. SQS long polling makes it easy and inexpensive to retrieve messages
    from your SQS queue as soon as they are available.
    * Long polling is a way to save you money, especially if a queue is empty
* Fanning Out
    * Create an SNS topic first using SNS. Then create and subscribe multiple SQS queues to the SNS topic
    * Now whenever a message is sent to the SNS topic, the message will be fanned out to the SQL queues, 
        * i.e. SNS will deliver the message to all the SQS queues that are subscribed to the topic
    * fanning out is a technique to distribute a message coming in on a single queue, onto N number of queues
* Messages in queues can be encrypted using Amazon AWS SSE-KMS (but this feature not available in all regions)



Simple Notification Service (SNS)
=================================================================================
## Overview
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

## SNS Summary
(E)
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
* Instantaneous **push** based delivery (no polling)
* messages could be delivered out of order
* AWS does **NOT** guarantee delivery of messages to subscribed end-points (due to potential internet issues, or 
email restrictions)
    * SNS implements a 4-phase retry policy
        1. retries with no delays in between attempts
        2. retries with some minimum delay in between attempts
        3. retries with some back-off model (linear or exponential)
        4. retries with some maximum delay between attempts


Simple Work Flow (SWF)
==================================================================================
## Overview
* SWF is a web service that makes it easy to coordinate work across distributed application components
* SWF enables applications for a range of use cases, including media processing, web-application back ends,
business process work flows and analytics pipelines, to be designed as a coordination of tasks
* Tasks represent invocations of various processing steps in an application which can be performed by executable
code, web service calls, human actions and scripts

## SWF Workers
* (E) Workers are programs that interact with Amazon SWF to:
    * get tasks
    * process received tasks
    * return the results

## SWF Decider
* (E) The decider is a program that controls the coordination of tasks eg. their ordering,concurrency,
and scheduling according to the application logic

## SWF Workers and Deciders
* Workers and deciders can run on cloud infrastructure (such as EC2) or an machines behind firewalls. Amazon SWF
brokers the interactions between workers and the decider. It allows the decider to get consistent views into the
progress of tasks and to initiate new tasks in an ongoing manner
* At the same time, Amazon SWF stores tasks, assigns them to workers when they are ready, and monitors their progress.
**It ensures that a task is assigned only once and is never duplicated.** Since SWF maintains the application's state
durably, workers and deciders don't have to keep track of execution state. They can run independently and scale quickly

## SWF Domains
* think of domains like a container
* Your workflow and activity types and the workflow execution itself are all scoped to a domain. Domains isolate a set
of types, executions, and task lists from others within the same account
* You can register a domain by using the AWS management console or by using the ```RegisterDomain``` action in the
Amazon SWF API
    * the parameters are specified in JSON format
* 100 domains MAX per aws account

* How long for workflows?
    * (E) **maximum workflow can be 1 year and the value is always measured in seconds**

## SWF vs SQS
(E)
* SWF is a task oriented API, 
    * whereas SQS is a message-oriented API
* SWF ensures that a task is assigned only once and is never duplicated
    * With SQS you need to handle duplicated messages and may also need to ensure that a message is processed only once
* SWF keeps track of all the tasks and events in an application
    * With SQS, you need to implement your own application-level tracking, especially if your application uses 
    multiple queues.



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
* Either you entire application is one EB application OR
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
* Route53 is completely supported. This includes creating **new** hosted zones or **updating** existing ones
* You can create A Records, Aliases etc...
* IAM Role Creation and Assignment is also supported
* May get scenario question on exam comparing ElasticBeanstalk to CloudFormation



Shared Responsibility Model
======================================================================
(E) Know the difference between what AWS is responsible for and what you (the customer) are responsible for
## Infrastructure Services
e.g. EC2, EBS, VPC
* Customers are Responsible for
    * Customer Data
    * Platform and application management
    * Operating System, Network, and fire-wall configuration
        * client-side data encryption and data integrity authentication
        * Server-side encryption
            * File system and/or data
        * network traffic protection
            * encryption,integrity,identity
* AWS Responsible for
    * AWS Global infrastructure
        * Regions, AZs, Edge Locations
    * Foundation Services
        * Compute, Storage, Databases, Networking

## Container Services
e.g. AWS RDS and AWS EMR
* Customer Responsible for
    * customer data
        * client side data encryption and data integrity and authentication
        * network traffic protection
            * encryption, integrity, identity
    * customer IAM
    * **Firewall configuration**
* AWS Responsible for
    * **platform and application management**
    * **operating system and network configuration** (customer doesn't get access to the OS in this case)
        * AWS Endpoints
        * AWS Global infrastructure
            * Regions, AZs, Edge Locations
        * Foundation Services
            * Compute, Storage, Databases, Networking

## Abstracted Services
e.g. S3, DynamoDB, Lambda
* Customer responsible for:
    * Customer Data
        * client side data encryption and data integrity authentication
* AWS Responsible for
    * AWS IAM
    * Server-side encryption provided by the platform
    * network traffic protection provided by the platform
    * platform and application management
    * operating system, network, firewall configuration
        * AWS Endpoints
        * AWS Global infrastructure
            * Regions, AZs, Edge Locations
        * Foundation Services
            * Compute, Storage, Databases, Networking

## Exam Tips
* remember the different service levels:
    * infrastructure services
        * EC2, EBS, VPC
        * AWS only responsible for Global Infrastructure and Foundation Services
    * Container services
        * AWS RDS, AWS EMR
        * AWS is responsible starting at the operating system / network config. / application management level
    * Abstracted Services
        * S3, DynamoDB, Lambda
        * customer responsible for client side data encryption and data integrity authentication
            * AWS responsible for everything else


Route 53 and DNS
===========================================================================
## DNS 101
* IPV4 - 32bits
* IPV6 - 128bits
* AWS VPCs are IPv6 compatible

### Top Level Domains
* the last word in a domain name represents the *top level domain*
    * .com .edu .gov  etc...
* the second word in a domain name is known as a second level domain name (this is optional though)
    * .co.uk .gov.uk .com.au
 
### Domain Registrars
* A registrar is an authority that can assign domain names directly under one or more top-level domains.
    * GoDaddy.com    etc....
* These domains are registered with *InterNIC*, a service of ICANN, which enforces uniqueness of domain names
across the internet
* Each domain name becomes registered in a central database known as the *WhoIS* database

### Start of Authority (SOA) Records
* The SOA record stores information about
    * the name of the server that supplied data for the zone
    * the administrator of the zone
        * e.g. who onws the zone, will contain contact information
    * the number of seconds a secondary name server should wait before checking for updates
    * the number of seconds a secondary name server should wait before retrying a failed zone transfer
    * the maximum number of seconds that a secondary name server can use data before it must be either refreshed or
    expire
    * **the default number of seconds for the time-to-live file on resource records**

### Name Server (NS) Records
* NS stands for Name Server records and are used by Top Level Domain servers to direct traffic to the content DNS
server which contains the authoritative DNS records

### A Records
* An "A" record is the fundamental type of DNS record and the "A" in A record stand for *"Address"*. The A record is
used by a computer to translate the name of the domain to an IP address.
    * e.g. http://www.acloud.guru might point to http://123.10.10.80

### TTL Record
* The length that a DNS record is cached on either the Resolving server or the users own local PC is equal the value
of the "Time to Live" (TTL) in seconds. The lower the time to live, the faster changes to DNS records take to
propagate throughout the internet.

### CNames
* A Canonical Name (CName) can be used to resolve one domain name to another. For example, you may have a mobile
website with the domain name http://m.acloud.guru that is used for when users browse to your domain name on their
mobile devices. You may also want the name http://mobile.acloud.guru to resolve to this same address
    
### Alias Records
* only used by Amazon Route53
* Alias records are used to map resource record sets in your hosted zone to Elastic Load Balancers, CloudFront
distributions or S3 buckets that are configured as websites
* Alias records work like a CName record in that you can map one DNS name (www.example.com) to another 'target' DNS
name (elb1234.elb.amazonaws.com)
* key difference - a CName can't be used for naked domain names ( the domain name without www, also called the zone 
apex record) You can't have a CName for http://acloud.guru it must be either an A record or an Alias
* alias resource record sets can save you time because Amazon Route 53 automatically recognizes changes in the record
sets that the alias resource record set refers to
    * for example, suppose an alias resource record set for example.com points to an ELB load balancer at
    lb1-1234.us-east-1.elb.amazonaws.com. If the IP-address of the load balancer changes, Amazon Route 53 will
    automatically reflect those changes in DNS answers for example.com without any changes to the hosted zone that
    contains resource record sets for example.com

### Route53 routing policies
(E)
* Simple
    * This is the default routing policy when you create a new record set. This is most commonly used when you have
    a single resource that performs a given function for your domain, for example, **one web server** that serves content
    for the http://acloud.guru website
* Weighted
    * lets you assign percentages (aka weights) to routes. eg. 80% of traffic goes to one resource and 20% of traffic
    goes to another
* Latency
    * latency based routing allows you to route your traffic based on the lowest network latency for your end user (eg 
    which region will give them the lowest response time)
    * to use latency-based routing you create a latency resource record set for the Amazon EC2 (or ELB) resource in 
    each region that hosts your website. When Amazon route53 receives a query for your site, it selects the latency
    resource record set for the region that gives the user the lowest latency. Route 53 then responds with the value
    associated with that resource record set
* Failover
    * failover routing policies are used when you want to create an active/passive set-up. e.g. you may want your
    primary site to be in EU-WEST-2 and your secondary disaster recovery site in AP-SOUTHEAST-2
    * Route53 will monitor the health of your primary site using a health check
        * a health check monitors the health of your end points
* Geolocation
    * geolocation routing lets you choose where your traffic will be sent based on the geographic location of your
    users (ie the location from which DNS queries originate.) 
        * e.g. you might want all queries from Europe to be routed to a fleet of EC2 instances that are 
        specifically configured for your European customers. These servers may have the local language of 
        your European customers and all prices are displayed in Euros.
* GeoProximity (new)
    * GeoProximity routing lets Amazon Route 53 route traffic to your resources based on the geographic location of 
    your users and your resources. You can also optionally choose to route more traffic or less to a given resource
     by specifying a value, known as a bias, that expands or shrinks the size of the geographic region from which 
     traffic is routed to a resource
* Multivalue answer (new)
    * Multivalue answer routing lets you configure Amazon Route 53 to return multiple values, such as IP addresses 
    for your web servers, in response to DNS queries. You can specify multiple values for almost any record, but 
    multivalue answer routing also lets you check the health of each resource, so Route 53 returns only values for 
    healthy resources. It's not a substitute for a load balancer, but the ability to return multiple health-checkable 
    IP addresses is a way to use DNS to improve availability and load balancing.


## Exam Tips
* Elastic Load Balancers never have a predefined IPv4 address. They only have a DNS name
* Understand the difference between an Alias record and a CNAME
    * CNames can't be used for naked domain names, Alias records can resolve naked domain names to ELB's address
    * When you make a request to Route53 for a DNS record, you will be charged for that request using CNames, whereas
    if you make a request using an Alias Record, you won't be charged
* Given a choice, always choose an Alias record over a CNAME
* remember the different routing policies and their use cases
    * Simple, Weighted, Latency, Failover, Geolocation, GeoProximity, Multivalue


Virtual Private Cloud (VPC)
===========================================================================================
* (E) Think of a VPC as a virtual data center in AWS
* every region in the world has a default VPC, provide for you when you sign-up to AWS
* Amazon VPC lets you provision a logically isolated section of the AWS Cloud where you can launch resources in a 
virtual network that you define. You have complete control over your virtual networking environment, including
selection of your own IP address range, creation of subnets and configuration of route tables and network gateways
* You can easily customize the network configuration for your Amazon VPC. For example, you can create a public facing
subnet for your webservers that has access to the internet, and place your backend systems such as databases or
application servers in a private facing subnet with no internet access. You can leverage multiple layers of security,
including security groups and network access control lists, to help control access to EC2 instances in each subnet
* Additionally, you can create a Hardware Virutal Private Network (VPN) connection between your corporate datacenter
and your VPC and leverage the AWS cloud as an extension of your corporate datacenter

## What can you do with a VPC
* Launch instances into a subnet of your choosing
* assign custom IP address ranges in each subnet
* configure route tables between subnets
* create a internet gateway and attach it to our VPC
    * (E) can only have one internet gateway per VPC
* much better security control over your AWS resources
* instance security groups
    * (E) security groups can span availability zones, and multiple subnets
* subnet network access control lists (ACLS)

## Default VPC vs Custom VPC
* *Default VPC* 
    * is user friendly, allowing you to immediately deploy instances
    * all subnets in default VPC have a route out to the internet
    * each EC2 instance has both a public and private IP address

## VPC Peering    
* Allows you to connect one VPC with another via a direct network route using private IP addresses
* instances behave as if they were on the same private network
* you can peer VPC's with other AWS accounts as well as with other VPCs in the same account
* Peering is in a star configuration (aka hub and spoke)
    * ie 1 central VPC peers with 4 others. **NO TRANSITIVE PEERING**

## Custom VPCs and ELBs
* (E) Your **Application Load Balancer** must be created in at least two subnets and those subnets must be internet
 facing (won't be able to create a ALB unless you do this)

## VPC Flow Logs
* VPC flow logs is a feature that enables you to capture information about the IP traffic going to and from network
interfaces in your VPC. Flow log data is stored using Amazon CloudWatch Logs. After you've created a flow log, you 
can view and retrieve its data in Amazon CloudWatch Logs
* Flow logs can be created at 3 levels:
    * VPC
    * Subnet
    * Network Interface Level

## NATs vs Bastions
* (E) may get questions about how to make a bastion server highly available
     * may want at least two public subnets with a bastion server in each of them
     * may want to enable auto-scaling for your bastion server(s) with health checks

## VPC End Points
A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered 
by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection
* Instances in your VPC do not require public IP addresses to communicate with resources in the service. Traffic 
between your VPC and the other service does not leave the Amazon network
* There are two types of VPC Endpoints:
    * *Interface*
        * An elastic network interface with a private IP address that serves as an entry point for traffic destined
         to a supported service
        * supported services
            * Amazon EC2 API
            * AWS Systems Manager
            * Amazon CloudWatch Logs
            * Elastic Load Balancing API
            * Amazon Kinesis Data Streams
            * AWS KMS
            * AWS Service Catalog
            * Endpoint services hosted by other AWS accounts
            * Supported AWS Marketplace partner services
    * *Gateway*
        * A gateway that is a target for a specified route in your route table, used for traffic destined to a 
        supported AWS service
        * Supported Services:
            * Amazon S3
            * DynamoDB



## Exam Tips
* think of a VPC as a logical datacenter in AWS
* VPCs consist of: 
    * Internet Gateways (IGWs) (or Virtual Private Gateways)
    * Route Tables
        * contain rules that determine where traffic is routed
        * each subnet in your VPC must be associated with a route table; the table controls the routing for the subnet 
        * A subnet can only be associated with one route table at a time
            * but you can associate multiple subnets with the same route table
    * Network Access Control Lists
        * an optional layer of security for your VPC that acts as a firewall for controlling traffic in/out of one
        or more subnets 
    * Subnets
    * Security Groups
        * a virtual firewall for an **instance** that controls traffic (into/out of) it
* 1 subnet = 1 availability zone (subnets cannot span AZs)
* all subnets in an Amazon VPC have routes to other subnets (by default)
* security groups are **stateful**
    * opening an inbound port automatically opens the corresponding outbound port
* network access control lists are **stateless**
* **NO TRANSITIVE PEERING**
* you will lose 5 ip addresses every time you provision a subnet, they are reserved by AWS
    * eg. 10.0.0.0, 10.0.0.1, 10.0.0.2, 10.0.0.3, 10.0.0.255
* you can't have multiple IGWs attached to one VPC, **1 IGW per VPC**
* security groups only exist within the VPC you create them in (they don't span between VPCs)
* NAT Instances:
    * when creating a NAT Instance make sure you disable Source/Destination check on that instance
    * NAT instances must be in a public subnet
    * there must be a route out of the private subnet to the NAT instance, in order for this to work
    * the amount of traffic that NAT instances can support depends on the instance size. 
        * If you are bottle-necking, increase the instance size
    * You can create high availability using Autoscaling groups, multiple subnets in different AZs, and a script
    to automate failover
    * NAT instances are always behind a security group
* NAT Gateways:
    * preferred by the enterprise
    * scale automatically up to **10Gbps**
    * no need to patch
    * not associated with security groups
    * automatically assigned a public IP address
    * remember to update your route tables and point them to the NAT Gateway(s)
    * you'll probably want multiple NAT gateways across AZ's for redundancy
    * no need to disable source/destination checks
    * more secure than a NAT instance
    * amazon manage the NAT gateway for you, (you won't even have SSH access)
* Network ACL
    * Your VPC automatically comes with a default network ACL and by default it allows all outbound and inbound
    traffic
    * each subnet in your VPC must be associated with a network ACL. If you don't explicitly associate a subnet
    with a NACL, the subnet is automatically associated with the default NACL
    * when you create a custom NACL, all inbound/outbound traffic is DENIED by default
    * NACL rules are evaluated in numerical order
    * You can associate a NACL with multiple subnets; however, **a subnet can be associated with only one network ACL
    at a time.** When you associate a network ACL with a subnet, the subnets previous association is removed
        * NACL(s) can span AZs
    * NACLs have separate inbound and outbound rules, and each rule can either allow or deny traffic
    * NACLs are stateless; responses to allowed inbound traffic are subject to the rules for outbound traffic (and
    vice versa)
    * remember you may need to allow ephemeral ports on outbound rules for some TCP protocols (like SSH...)
    * Block IP addresses using network ACLs NOT Security Group
* Application Load Balancers (ALB)
    * you will need at least 2 **public** subnets in order to deploy an application load balancer 
* VPC Flow Log
    * you cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
    * you cannot tag a flow log
    * after you've created a flow log, you cannot change its configuration; for example, you can't associate a 
    different IAM role with the flow log
    * Not all IP Traffic is monitored:
        * traffic generated by instances when they contact the AWS DNS server
            * if you use your own DNS server, then all traffic to that DNS server is logged
        * traffic generated by a Windows instance for Amazon Windows license activation
        * traffic to and from 169.254.169.254 for instance meta-data
        * DHCP traffic
        * traffic to the reserved IP address for the default VPC router
* NATs vs Bastions
    * a NAT is used to provide internet traffic to EC2 instances in private subnets
    * a bastion is used to securely administer EC2 instances (using ssh or rdp) in private subnets