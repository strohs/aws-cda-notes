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
=====================================================================
## EC2 Instances
* *On Demand* - you pay a fixed rate, by the hour (or second) with no commitment
    * perfect for users that want low cost and flexibility of AWS EC2 without any up-front payment or long term commitment
    * applications with short term, spiky, unpredictable workloads that cannot be interrupted
    * applications being developed or tested EC2 for the first time
* *Reserved* - provide you with a capacity reservation and offer a significant discount on the hourly charge. 
1 year or 3 year terms
    * applications with steady state or predictable usage
    * applications that require reserved capacity
    * users can make up front payment costs to reduce their total computing costs even further
        * *Standard RIs* (up to 75% off on-demand)
        * *Convertible RIs* (up to 54% off on-demand) feature the ability to change the attributes of the RI as long as
        the exchange results in the creation of RIs of equal of greater value
        * *Scheduled RIs* are available to launch within the time window you reserve. This allows you to match your
        capacity reservation to a predictable recurring schedule that only requires a fraction of a day, week, or month
* *Spot* - enable you to bid whatever price you want for an instance capacity, providing for greater savings if your
applications have flexible start/end times
    * application that have flexible start and end times
    * applications that are only feasible at very low compute prices
* *Dedicated Hosts* - Physical EC2 server dedicated for your use. Dedicated hosts can help reduce costs by allowing you
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
           
> Remember the acronym **DR MCGIFT PX**
OR
FIGHTDRMCPX

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
    
### Exam Tips
* know differences between On Demand, Spot, Reserved, Dedicated Hosts
* If AWS terminates the spot instance, you get the hour for free. If **you** terminate the instance, you will be
charged for the complete hour
* FIGHT DR McPX
* SSD
    * General Purpose SSD - balances price and performance for a wide variety of workloads
    * Provisioned IOPS SSD - highest performance SSD volume for mission critical low latency or high-throughput
    workloads
* Magnetic
    * Throughput Optimized HDD - low cost HDD volume designed for frequently accessed, throughput-intensive workloads
    * Cold HDD - lowest cost HDD volume designed for less frequently accessed workloads
    * Magnetic - previous generation. can be a boot volume
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


## Elastic Load Balancers (ELB)
* Application Load Balancer
    * best suited for balancing of HTTP and HTTPS traffic
    * operate at layer 7 and are application aware
    * they are intelligent and you can create advanced request routing, sending specified requests to specific web-servers
* Network Load Balancer
    * are best suited for load balancing of TCP traffic where extreme performance is required
    * operate at connection layer (Layer 4)
    * Capable of handling millions of requests per second while maintaining ultra-low latencies
    * will cost the most out of the ELBs
    * **use these for extreme performance**
* Classic load Balancer
    * are the *legacy* load balancers
    * can balance HTTP/HTTPS applications and use Layer-7 specific features such as *X-Forwarded* and sticky sessions
        * note that it is not as intelligent as the application load balancer
    * you can also use strict layer 4 load balancing for applications that rely purely on the TCP protocol

### Load Balancer Errors
* classic load balancer
    * if your application stops responding, the classic load balancer responds with a **504 error (Gateway Timeout)**
    * this means that the application is having issues (not the LB) and the problem could be at the 
    DB Layer or Web Server layer
    * identify where the application is failing and **scale it up** not out where possible

### X-Forwarded-For Header (supported by Classic Load Balancer)
* set by the LB, and contains the IPV4 address of your end user

### Exam Tips
* know the three types of Load Balancers
* know the 504 error and how to troubleshoot
* know about X-Forwarded-For header

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


## Route53
* Route53 is Amazon's DNS service
* Allows you to map domain names to:
    * EC2 Instances
    * Load Balancers
    * S3 Buckets


Lambda
========================
* Lambda functions and event sources are the core components of AWS Lambda
* Event sources (are usually) the AWS services that can be configured to invoke a lambda function
    * this configuration is called the *event source mapping*. It enables the automatic invocation of your lambda
    function when events occur

### Event Source Mappings
* Push based event sources
    * event mappings are maintained within the event source
    * you grant the event source permission to invoke your lambda function using a resource based policy
* Poll based event sources (kinesis, dynamodb, sqs)
    * lambda function *polls* the event source and *pulls* data to the function when an event becomes available
    * event source mapping maintained with the lambda function
    * lambda needs your permission to poll the stream source:
        * You grant the permission via the *execution role*, using the permissions policy associated with a role 
        that you specify when you create your lambda function.
        * AWS Lambda does *not* need any permissions to invoke your lambda function
* Custom Applications
    * you write these applications and invoke the lambda yourself 
        * you use the `Invoke` api (via an AWS SDK or using the aws cli `invoke` command)
    * you don't need to set-up an event source mapping
    * if the application and lambda function are owned by different accounts, the account the owns the lambda function
    must allow cross-account permissions in the permission policy associated with the lambda function


### Limits
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


#### Concurrency Limits
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


### Supported Event Sources
* For poll-based sources, the **lambda function maintains the event source mapping**
* For all others, the event source mapping is maintained by the source


| Event Source | (A)Synchronous | Notes |
|:------------:|:--------------:|:------|
| Amazon DynamoDB | Sync | **polled by lambda**,streams must be enabled |
| Amazon Kinesis Data Streams | Sync | **polled by lambda** once per second |
| Amazon Simple Queue Service | Sync | **polled by lambda**, time affected by `VisibilityTimeout` and `TimeToWait` |
| Amazon Simple Notification Service | Async | |
| Amazon S3 | Async | 
| Amazon Simple Email Service | Async | SES message in SNS event |
| AWS CloudFormation | Async | CloudFormation message in SNS Event
| Amazon CloudWatch Logs | Async |
| Amazon CloudWatch Events | Async | can create rules to send events to lambda |
| AWS CodeCommit | Async | repository events can trigger lambda |
| Scheduled Events | Async | powered by CloudWatch Events |
| AWS Config | Async |
| AWS IoT Button | Async | relies on Lambda to perform the button click operation(s)
| Amazon CloudFront | Async | uses Lambda@Edge to change cloudfront requests and responses |
| Amazon Cognito | Sync  |
| Amazon Alexa | Sync  | build lambdas to give new skills to Alexa |
| Amazon Lex | Sync | uses lambda to perform init., validation, fulfillment
| Amazon API Gateway | Sync |
| Amazon Kinesis Data Firehose | Sync | lambda can process a stream before it is sent downstream
| On Demand | Sync or Async | you invoke the lambda (from cli using `invoke` or programmatically)


* AWS Lambda provides the `CreateEventSourceMapping` operation for you to create and manage the event source mapping. 


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
        * function returns **429** error and the invoking service is responsible for retries
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



### Monitoring concurrency usage
uses cloudwatch metrics
* ConcurrentExecutions - concurrent executions at the account level, and for any function with a custom concurrency limit
* UnreservedConcurrentExecutions - shows total concurrent executions for functions assigned to the default 
"unreserved" concurrency pool 
    

     
### Handlers (in Java)
* two approaches for creating a handler
    * load handler method directly without implementing an interface
    * implement a standard interface provided by `aws-lambda-core-library`
* general syntax for handler is `OutputType handler-name(InputType input, Context context) {...}`
    * in order for handler to be invoked, input data must be serializable into the data type of the input parameter
    * InputType - can be event data published by an event source or custom input that a developer provides
    such as String or a custom object(must be serializable)
    * outputType - 
        * if you plan to invoke the lambda synchronously (using `RequestResponse` invocation type) 
        you can return the output of your function using any of the supported data types
        * if you plan to invoke asynchronously (using the `Event` invocation type), the output type should
        be `void`
            * Example: S3 and SNS use the `Event` invocation type
* inputType and outputType can be:
    * Simple Java types (AWS Lambda supports the String, Integer, Boolean, Map, and List types) 
    * Pre-defined AWS event types defined in `aws-lambda-java-events`
    * POJO (Plain Old Java Object) type
        * AWS will automatically serialize and deserialize input and output JSON based on the POJO type
        * AWS Lambda serializes based on standard bean naming conventions (see The Java EE 6 Tutorial). 
        You should use mutable POJOs with public getters and setters
        * If you use POJOs for input and output, you need to provide implementation of the `RequestClass` and 
        `ResponseClass` types
    * Stream type (If you do not want to use POJOs or if Lambda's serialization approach does not meet your needs,
     you can use the byte stream implementation
* you can omit the `Context` parameter if it isn't needed



### AWS Lambda CLI 
common command examples:
* creating a lambda `aws lambda create-function`

        aws lambda create-function \
        --region <region> \
        --function-name CreateThumbnail \
        --zip-file fileb://file-path/CreateThumbnail.zip \
        --role <role-arn> \
        --handler CreateThumbnail.handler \
        --runtime <runtime> \
        --timeout 10 \
        --memory-size 1024

* updating existing lambda config

        aws lambda update-function-configuration \
           --function-name CreateThumbnail  \
           --region <region> \
           --timeout <timeout-in-seconds>

* adding permissions to a lambda's access policy

        aws lambda add-permission \
        --function-name CreateThumbnail \
        --region region \
        --statement-id some-unique-id \
        --action "lambda:InvokeFunction" \
        --principal s3.amazonaws.com \
        --source-arn arn:aws:s3:::sourcebucket \
        --source-account bucket-owner-account-id
 

* manually invoking a lambda

        aws lambda invoke \
        --invocation-type Event \
        --function-name CreateThumbnail \
        --region region \
        --payload file://file-path/inputfile.txt \
        --profile adminuser \
        outputfile.txt





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

