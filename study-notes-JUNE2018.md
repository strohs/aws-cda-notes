## Lambda
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
* max execution duration **5 minutes**
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
| Amazon DynamoDB | Sync | polled by lambda,streams must be enabled |
| Amazon Kinesis Data Streams | Sync | polled by lambda once per second |
| Amazon Simple Queue Service | Sync | polled by lambda, time affected by `VisibilityTimeout` and `TimeToWait` |
| Amazon Simple Notification Service | Async | |
| Amazon S3 | Async | 
| Amazon Simple Email Service | Async | SES message in SNS event |
| Amazon Cognito | Sync  |
| AWS CloudFormation | Async | CloudFormation message in SNS Event
| Amazon CloudWatch Logs | Async |
| Amazon CloudWatch Events | Async | can create rules to send events to lambda |
| AWS CodeCommit | Async | repository events can trigger lambda |
| Scheduled Events | Async | powered by CloudWatch Events |
| AWS Config | Async |
| Amazon Alexa | Sync  | build lambdas to give new skills to Alexa |
| Amazon Lex | Sync | uses lambda to perform init., validation, fulfillment
| Amazon API Gateway | Sync |
| AWS IoT Button | Async | relies on Lambda to perform the button click operation(s)
| Amazon CloudFront | Async | uses Lambda@Edge to change cloudfront requests and responses |
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

