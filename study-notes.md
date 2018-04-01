AWS Certified Developer Associate
==============================================================

## AWS 10,000ft overview
* understand difference between a Region and Availability Zone (AZ), Edge Location
  * Region - physical location in the world consisting of 2 or more availability zones
  * Availability Zone (AZ) - one or more discrete data centers, with redundancy, housed in separate facilities
  * Edge Locations - endpoints for AWS used for caching content. These are 50+ edge locations, in data-centers,
   spread around the world.

## Identity Access Management (IAM)
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


## Security Token Service (STS)
* grants users limited (and/or temporary) access to AWS resources
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

### EXAM
* **IAM is global, not tied to any AWS Region**
* **Access Key ID and Secret Access Key are only used for programmatic access to AWS**
* **new users have no permissions when first created**

Scenario 1 (using user credentials)
1. Develop an Identity Broker to communicate with LDAP and AWS STS (you develop this in house)
2. Identity Broker always authenticates with **LDAP first, then with AWS STS**
3. Application then gets temporary access to AWS resources

Scenario 2 (using roles rather than user credentials)
1. develop an Identity Broker to communicate with LDAP and AWS STS
2. Identity Broker always authenticates with LDAP first, gets a IAM role associated with a user (from LDAP)
3. application then authenticates with STS and assumes that IAM Role
4. application uses that IAM Role to interact with S3 (or some other AWS resource)

Active Directory Federation (with SAML)
1. You can authenticate with Active Directory using SAML
2. Authenticate with Active Directory first
3. then get a temporary security credential

Web Identity Federation with Mobile Applications
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



## EC2
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