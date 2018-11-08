## CDA 2018 - Tips

### IAM
* federation allows users to authenticate with a Web Identity Provider (google, facebook, amazon.com...)
* the user authenticates first with the Web ID provider and receives an authentication token which is exchanged for
temporary AWS credentials allowing them to assume an IAM role
* Access Management Tips
    * always give users the minimum amount of access required
    * create groups:
        * assign your users to groups
        * users will automatically inherit the permissions of the group
        * group permissions are assigned using policy documents
    * Secret Access Key
        * only seen once, at the time user is created
        * can be re-generated
        * will need to run `aws configure` again
    * do not use just one access key and share it across developers
        * create one access key pair per developer
* Roles
    * assigning roles to EC2 instances is preferred from a security perspective
    * roles are controlled by policies
    * changing a policy on a role will take effect immediately
    * you can attach/detach roles to a running EC2 instance (don't have to stop the instance)
* IAM Policies
    * **Managed Policy** - aws-managed default policies
    * **Customer Managed Policy** - managed by you
    * **In-Line Policy** - managed by you, but embedded in a single user, group or role
    * in most cases, AWS recommends using Managed Policies over inline policies

### Cognito
* can serve as an Identity Broker which handles interaction between your application and the Web ID Provider
* you don't need to write code to do this
* provides sign-up, sign-in and guest user access
* syncs user data for a seamless experience across your devices
* Cognito is the AWS recommended approach for Web ID Federation, especially for mobile apps
* user pools are used to manage user sign-up and sign-in directly or via web identity providers
* cognito acts as an identity broker, handling all interaction with Web Identity Providers
* cognito uses **Push Synchronization** to send a silent push notification of user data updates to multiple devices
types associated with a user ID

### EC2
* EC2 instance types:
    * **on demand** - you pay a fixed rate for the instance (by the hour) with no commitment
        * good for applications with short term, spiky, unpredictable workloads that cannot be interrupted
    * **reserved**
        * provide a capacity reservation up front and offer significant discount on the hourly charge
        * 1 or 3 year terms
    * **spot**
        * you bid a price for instance capacity
        * provides for greater savings if your apps have flexibile start/end times
        * if AWS terminates the spot instance, you get the hour for free, if you terminate it..you still pay
    * **dedicated hosts**
        * physical EC2 server dedicated for your use
        * useful for regulatory requirements that may not support multi-tenant virtualization
        * can be purchased On-Demand (hourly)
        * can be purchased as a reservation for up to 70% off the On-Demand price
* **FIGHT Dr McPIX**

#### Elastic Block Storage (EBS)
* EBS Types
    * SSD
        * **General Purpose SSD** - balances price and performance for a wide variety of workloads
            * up to 10,000 IOPS
        * **Provisioned IOPS** - highest performance SSD Volume for mission critical low latency or high-throughput workloads
            * when you need > 10,000 IOPS
    * Magnetic
        * **Throughput Optimized HDD**
            * cannot be a boot volume
            * designed for frequently accessed, throughput-intensive workloads
        * **Cold HDD**
            * cannot be a boot volume
            * lowest cost HDD volume designed for less frequently accessed workloads
        * **Magnetic**
            * previous generation *can be a boot volume*
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

#### Elastic Load Balancers
* three types of load balancers offered:
    * **application load balancers**
        * operate at layer 7
        * best suited for balancing HTTP and HTTPS traffic
        * can intelligently route HTTP,HTTPS traffic, sending requests to specific web-servers
    * **network load balancer**
        * best suited for load balancing of TCP traffic when extreme performance is required
        * operate at connection layer 4
        * capable of handling millions of requests per second while maintaining ultra low latencies
        * will cost the most out of all ELBs
    * **classic load balancers**
        * the *legacy* load balancer type
        * can balance HTTP/HTTPS applications and use layer-7 specific features:
            * such as `X-Forwarded-For` and sticky sessions
        * not as intelligent as the application load balancer
        * can also use *strict layer 4* load balancing for applications that rely purely on the TCP protocol
        * `X-Forwarded-For` header is set by the load balancer and contains the IPV4 address of your end user
* load balancer errors
    * if your load balanced app. stops responding with a **504 error (Gateway Timeout)**:
        * this is an app. problem not a load balancer, problem could be with Database, or app server etc...
        * identify where application is failing and **scale it up** not out

### Relational Database Services (RDS)
* most commonly used for online transaction processing
* supports the following DBs:
    * SQL Server
    * ORACLE
    * MySQL
    * PostgreSQL
    * MariaDB
    * Amazon Aurora
        * amazon's flagship DB
        * fully compatible with MySQL
* if your EC2 instance can't connect to your RDS instance make sure the security group of the RDS instance is
allowing inbound connections from the security group of the EC2 instance on the DB's port
* Redshift
    * user for Online Analytics Processing (OLAP)
* Multi-AZ
    * allows you to have an exact copy of your DB in another AZ
    * AWS does the replication for you
    * in the event of a DB failure, AWS automatically fails over to a standby so that DB operations can resume quickly
    * most be manually enabled
    * **for disaster recovery only, it is not for improving performance**
* Read Replicas
    * can help improve performance of **read heavy databases**
    * achieved via asynchronous replication from the primary RDS instance to the read replica(s)
    * used for scaling....**not disaster recovery**
    * must have automatic back-ups turned on on order to deploy a read replica
    * 5 read replicas allowed per production DB
    * each read replica gets its own DNS endpoint
    * **you can have read replicas with Multi-AZ enabled**
    * you can have read replicas of Multi-AZ source databases
    * read replicas can be promoted to be their own database...this breaks the replication
    * you can have a read replica in a second region
    * available for: MySQL, Aurora, MariaDB, PostgreSQL

### ElastiCache
* in memory caching
* fully supports **Redis** and **Memcached**
* commonly used to reduce stress/load on a database
* good choice for read-heavy databases that are NOT prone to frequent changing
* *Redshift* is a good choice over elasticache if the reason for poor RDS performance is OLAP transactions
* 2 different caching strategies:
    * **Lazy Loading**
        * only caches data when it is requested
        * there is a **cache-miss penalty**
            * initial request to cache (data is missing)
            * query database (to get the data)
            * write data to cache
        * possibility for **stale data**
            * avoid this by implementing a TTL (time-to-live) policy
    * **Write Through**
        * writes data to the cache whenever there is a change to the database
            * data is never stale
            * there is a write penalty
                * each write to the DB involves a write to the cache
                * elasticache node failure means that data is missing until added or updated in the database
                    * can be mitigated by implementing a Lazy Loading policy in conjunction with write-through
                * wasted resources if most of the data is never used
#### Redis
* has replication and persistence features
* elasticache manages redis more as a relational DB that includes failover
* Choose Redis if:
    * you need multi-AZ with failover
    * you need support for data-types such as lists,maps,sets
    * you need to sort/rank datasets in memory...such as leaderboards
    * you need Pub/Sub capabilities
    * you need persistence of your key store

#### Memcached
* pure caching solution, **no persistence**
* memcached nodes are managed as a pool that can grow and shrink, like an autoscaling group
* elasticache provides the following features on top of memcached
    * automatic node replacement
    * auto-discovery
* Choose memcache if:
    * you are not concerned about multi-AZ redundancy
    * object caching is your primary goal
    * you are interested in as simple a caching model as possible
    * you need multi-threaded performance with utilization of multiple cores
    * you need the ability to scale the cache out as you grow 

## S3
* S3 is a key/value store
    * key - the name of the object
    * value - the object data (a sequence of bytes)
    * VersionID
    * Meta-Data
    * Sub-Resources
        * access control list
        * ability to torrent the object
        * Cross Origin Resource Sharing
        * Transfer Acceleration
* S3 storage tiers
    * **S3 Standard**
        * 99.99% availability and 99.999999999% durability
        * designed to withstand loss of 2 facilities concurrently
    * **S3-IA** (infrequently accessed)
        * for data accessed less frequently, but requiring rapid access when needed
        * lower fee than S3, but charged a retrieval fee
        * 99% availability / 99.999999999% durability
        * minimum object size 128KB
    * **S3 One-Zone IA** (S3 one zone infrequent access)
        * stores data in a single AZ
        * 99.5% availability
        * 20% less cost then S3-IA
    * **Reduced Redundancy Storage**
        * designed to provide **99.99% durability** and **99.99%** availability of objects over a year
        * useful for objects you can recreate
        * lower cost than above options
    * Glacier
        * very cheap, for archival only
        * objects over 250MB can take 3-5 hours to restore
* S3 Security
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
* S3 Encryption
    * in transit - using SSL/TLS
    * at rest
        * server-side encryption
            * using S3 managed keys **SSE-S3** (uses AES256)
            * using AWS KMS - **S3-KMS**
            * using customer provided keys - **SSE-C**
        * **client side encryption**
            * data is encrypted by owner before storing on S3
* Enforcing Encryption
    * use a bucket policy which denies any S3 PUT request that doesn't include the `x-amz-server-side-encryption` header
* Cross Origin Resource Sharing (CORS)
    * by default, resources in one bucket cannot access resources in another bucket
    * you have to **enable CORS in the bucket that is being called** (the bucket containing the resource) and then
    configure the URL for the origin that will be calling the bucket
* Static website hosting
    * s3 can be used to host files for a static website
    * website DNS will be of the form: `http://BUCKETNAME.s3-website-REGION.amazonaws.com`
* S3 Performance Optimization
    * if your bucket receives >100 PUT/LIST/DELETE or >300 GET request per second there are some optimizations you can make
    * **Get-Intensive Workloads**
        * use **CloudFront** to get best performance
            * it will cache your most frequently accessed objects and reduce latency
* S3 Transfer Acceleration
    * utilizes cloudfront edge network to accelerate your uploads to S3
    * **you get a distinct URL for uploading to your bucket**
    * **always use the S3 website URL to access bucket resources when running a website on S3**
* S3 Limits
    * max file size stored in S3 - **5TB**
    * max file upload size in a single operation - **5GB**
    * can upload big files to S3 using multi-part upload - **5MB to 5TB** in size
    * can create **100 buckets** per AWS account


## CloudFront
* AWS's content delivery network
* terminology
    * **Edge Location**
        * location where content will be cached, separate to an AWS Region/AZ
        * you also write to edge locations, objects will be put in S3
        * objects are cached for the life of the Time-To-Live (TTL)
        * you can force a clear of the cache, but you will be charged
    * **Origin**
        * the origin of the files that the CDN will distribute
            * **S3 Bucket**
            * **EC2 Instance**
            * **Elastic Load Balancer**
            * **Route 53**
            * an on-premise server
    * **Distribution**
        * the name given to the CDN which consists of a collection of edge locations
        * **Web Distribution**
            * typically used for websites, HTTP/HTTPS
        * **RTMP** - adobe real time messaging protocol
            * used for media streaming
* you can configure the default TTL, **it defaults to 24 hours**
* you can restrict access to content using **Signed URLs** or **Signed Cookies**
* you can manually invalidate objects in CloudFront edge caches, **you will be charged for doing this**


## Lambda
* runtimes
    * **Node.js**
    * **Java**
    * **Python**
    * **C#**
    * **Go**
    * support for PowerShell recently added
* supported event sources Triggers:

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

* lambda scales out (not up) automatically
* lambda functions are independent, 1 event = 1 function
* lambda is serverless
* lambda functions can trigger other lambda functions
* X-ray can help to debug lambdas
* lambda can do things globally, you can back up S3 buckets to other S3 buckets
    * but each lambda resides in a specific region
* you can increase a lambdas compute power by increasing its RAM
* a lambda's concurrent execution limit can be set per function
* Lambda Versioning
    * can have multiple versions of a function
    * latest version will be `$LATEST`
    * qualified version ARN will have $LATEST in it, unqualified will not have it
    * versions are immutable
    * **can split traffic using aliases to different versions (only two way split)**
    * **cannot split traffic using $LATEST, create an alias to $LATEST instead**
* Best practices
    * Separate the Lambda handler (entry point) from your core logic - helps with testing
    * Take advantage of execution context reuse to improve performance of your function
    * use Lambda environment variables to pass operational parameters to your function
    * control the dependencies in your function's deployment package
    * minimize your deployment package size to its runtime necessities
    * minimize the complexity of your dependencies
        * prefer simpler frameworks that load quickly upon execution context startup
    * avoid using recursive code in your lambda function

* Limits:
    * ephemeral disk space in /tmp is **512MB**
    * max concurrent Lambda executions (per account) **1000**
    * memory **128MB** min to **3008MB** max (in 64MB increments)
    * max execution duration **15 minutes** (used to be 5 min)
    * Deployment package size (compressed .zip/.jar) **50MB**
    * Size of code dependencies  (uncompressed zip/jar) **250MB**

### Lambda@Edge
* lets you run lambdas that customize the content CloudFront delivers
* executes the Lambdas in AWS locations closer to the viewer
* these lambdas can change CloudFront requests and responses

## Systems Manager Parameter Store
* cloud based storage for sensitive configuration data
* data can be in plaintext or encrypted
* can be used instead of Lambda's environment variables to store sensitive configurations
    * you don't have to expose un-encrpyted parameters in the web console
    * can configure fine-grained access to the parameters via IAM
    * makes it easy to share common,sensitive variables between Lambda functions

## Step Functions
* allows you to combine various AWS services in a serverless workflow (basically a state machine)
* allow you to visualize and test your serverless applications
* automatically triggers and tracks each step of your application and retries when there are errors, so your application
executes in order and as expected
* logs the state of each step, so when things do go wrong, you can diagnose what happened and where it happened


## X-Ray
* a service that gathers and collects performance data about your AWS application
* makes it easy to diagnose errors and/or any performance problems your app may be experiencing, especially when 
dealing with other services
* you instrument your code using the X-Ray SDK
    * the sdk will intercept calls to other AWS sdks used throughout your application, including: HTTP requests, 
    MySQL, PostgreSQL
* the intercepted calls are sent to the XRay service in the cloud where they are analyzed and assembled
* XRay provides a GUI and filtering tools you can use to analyze your
* Supported Languages
    * java
    * Go
    * Node.js
    * Python
    * Ruby
    * .Net
* XRay integrates with:
    * Elastic Load Balancing
    * Lambda
    * API Gateway
    * AWS Elastic Beanstalk
    * EC2

## API Gateway
* has caching capabilities to increase performance
* low cost and scales automatically
* can throttle API Gateway to prevent attacks
* can log results to CloudWatch
* if you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure you have enabled CORS on API Gateway
* CORS is enforced by the client (usually a web browser)
* API Gateway can be a SOAP webservice passthrough
* can import API's using Swagger 2.0 definition files
* Limits:
    * **10,000 RPS** or **5000 concurrent requests**
        * if you go over, you will receive **429 Too Many Requests**
    * can be increased by contacting AWS support


## Dynamo DB
* AWS's low latency NoSQL database
* consists of tables, items and attributes
* supports both document and key-value models
* supported document formats are JSON, HTML, XML
* 2 types of Primary Key
    * Partition Key
    * Partition Key + Sort Key
* 2 Consistency Models
    * eventually consistent
    * strongly consistent
        * can take up to 1 second for new writes to be reflected in your read
* Access is controlled using IAM policies
    * fine grained control can be achieved by using **IAM Condition** parameter
        * `dynamodb:LeadingKeys` - allows users to access only the items where the partition key value matches their
        UserID
* DynamoDB Indexes:
    * enable fast queries on specific data columns
    * give you a different view of your data, based on alternative Partition/Sort keys
    * Local Secondary Indexes
        * must be created when you create the table
        * same partition key as your table
        * different sort key
        * can provide strongly consistent reads or eventually consistent
    * Global Secondary Indexes
        * can create at any time
        * different partition key and different sort key
        * can only provide eventually consistent reads
* Scans and Queries
    * a query finds items in a table using only the Primary Key
        * you provide the primary key name and value to search for
    * a Scan operation examines every item in the table
        * by default it returns all data attributes
            * use a `ProjectionExpression` parameter to indicate which attributes to return
            * can also use a `FilterExpression` to perform comparisons on the results in order to filter some out
    * query results are always sorted by the sort key (if there is one)
        * sorted in ascending order
            * set `ScanIndexForward(false)` to reverse the order **queries only**
    * queries are generally almost always more efficient than a scan
    * reduce the impact of a query or scan by setting a smaller page size, which uses fewer read operations
    * Isolate scan operations to specific tables and segregate them from your mission critical traffic
    * may be able to use *parallel scans* to speed up your scans
        * not recommended for tables that are already experiencing heavy read/write activity
    * **avoid using scans if you can**
        * design your tables in such a way that you can use the Query,Get, or BatchGetItem APIs
* 1 x Write Capacity Unit = 1 x 1KB write per second
* 1 x Read Capacity Unit = 1 x 4KB strongly consistent read OR 2 x 4KB eventually consistent read
* Read Units - `(Size of Read rounded to nearest 4KB chunk / 4KB) * number of items = read throughput`
    * divide by 2 for Eventually Consistent reads
* Writes - all writes are 1KB in size - `size of each item in KB * number of items = write throughout`
* `BatchWriteItem` - inserts, replaces or deletes multiple items across multiple tables in a single request
    * supports batches **up to 25 items** to Put or Delete, **max total request size 16MB**
* `BatchGetItem` - returns attributes for multiple items across multiple tables using their primary key
    * size limit of **16MB** and returns a **maximum of 100 items**
* DAX
    * a write though caching service
    * point your API calls to DAX cluster
        * if item is in the cache it is returned, else DAX performs a eventually consistent read on the actual table
    * reduced load on DynamoDB tables, may help to reduce provisioned read capacity
    * not suitable for write-intensive applications or apps. that require Strongly Consistent reads

## KMS and Encryption
* Customer Master Key (CMK) consists of:
    * alias
    * creation date
    * description
    * key state (enable or disabled)
    * **key material**
        * customer provided
        * AWS provided
    * CMKs stored in AWS **can never be exported out of KMS unencrypted**
    * CMKs can encrypt/decrypt up yo 4KB of data
* **Data Keys**
    * encryption keys that are used to encrypt data:
        * large amounts of data
        * other data encryption keys
* Setting up a CMK:
    * create an alias and description
    * choose a **key material option**
        * **KMS**
            * kms generates the key material
        * **External**
            * your own key material
    * define key **administrative permissions**
        * IAM users/roles that can administer (but not use the key)
    * define key **usage permissions**
        * IAM users/roles that can use the key to encrypt and decrypt data
* KMS CLI (common commands)
    * `aws kms encrypt`
        * encrypts plaintext into ciphertext by using a CMK
        * can be used to move encrypted data from one region to another
    * `aws kms decrypt`
        * decrypts ciphertext
    * `aws kms re-encrypt`
        * encrypts data on the server side with a new customer master key without exposing the plaintext of the data
        * **the data is first decrypted and then re-encrypted**
        * can be used to change the encryption context of a ciphertext
    * `aws kms enable-key-rotation`
        * Enables automatic rotation of the key material for the specified customer master key (CMK)
            * AWS KMS generates new cryptographic material for the CMK every year
        * You cannot perform this operation on a CMK in a different AWS account