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
**DR MCGIF PX**
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
           

### Exam
* some exam questions will present different scenarios and ask you to pick cheapest/best option