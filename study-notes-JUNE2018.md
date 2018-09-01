## Lambda

### Limits
* Memory
    * **128MB** min to **3008MB** max (in 64MB increments)
* ephemeral disk space in /tmp **512MB**
* number of processes and threads combined **1024**
* max execution duration **5 minutes**
* Synchronous (RequestResponse) invoke request body payload size: **6MB**
* Asynchronous (Event) invoke request **128MB**
* Deployment package size (compressed .zip/.jar) **50MB**
* Total size of all deployment packages per region **75GB**

* Concurrency Execution Limits
    * Account Level limits:
        * **1000** by default
        * can be increased by opening a case in AWS Support Center
        * there is also an **immediate concurrency burst limit** that varies per region
        * if the burst limit is reached, AWS will increase lambda function executions by **500 per minute**
    * Function Level limits 
        * set per function
            * set in the console or via aws cli (`PutFunctionConcurrency`)
            * Use the `GetFunction` cli command to view cocurrency limits for a specific function 
        * counts towards the account level limits
        * By setting a concurrency limit on a function, Lambda guarantees that allocation will be applied 
        specifically to that function, regardless of the amount of traffic processing remaining functions
        * once concurrency limit is reached for a function, the function is throttled

#### Throttling behavior        
    

     
### Handlers (in Java)
* two approaches for creating a handler
    * load handler method directly without implementing an interface
    * implement a standard interface provided by `aws-lambda-core-library`
* general syntax for handler is `outputType handler-name(inputType input, Context context) {...}`
    * in order for handler to be invoked, input data must be serializable into the data type of the input parameter
    * inputType - can be event data published by an event source or custom input that a developer provides
    such as String or a custom object(must be serializable)
    * outputType - 
        * if you plan to invoke the lambda synchronously (using `RequestResponse` invocation type) 
        you can return the output of your function using any of the supported data types
        * if you plan to invoke asynchronously (using the `Event` invocation type), the output type should
        be void
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

### Supported Event Sources
This topic lists the supported AWS services that you can configure as event sources for AWS Lambda functions
* Amazon S3
* Amazon DynamoDB
* Amazon Kinesis Data Streams
* Amazon Simple Notification Service
* Amazon Simple Email Service
* Amazon Simple Queue Service
* Amazon Cognito
* AWS CloudFormation
* Amazon CloudWatch Logs
* Amazon CloudWatch Events
* AWS CodeCommit
* Scheduled Events (powered by Amazon CloudWatch Events)
* AWS Config
* Amazon Alexa
* Amazon Lex
* Amazon API Gateway
* AWS IoT Button
* Amazon CloudFront
* Amazon Kinesis Data Firehose

For all of the event sources listed in this topic, note the following:
* Event sources maintain the event source mapping, *except for the poll-based services*: 
  Amazon Kinesis Data Streams, Amazon DynamoDB Streams and Amazon Simple Queue Service). For the poll-based 
  services, AWS Lambda maintains the event source mapping. AWS Lambda provides the `CreateEventSourceMapping`
  operation for you to create and manage the event source mapping. For more information, see Event 
  Source Mapping.
* The invocation type that these event sources use when invoking a Lambda function is also preconfigured. 
For example, Amazon S3 always invokes a Lambda function asynchronously and Amazon Cognito invokes a Lambda 
function synchronously. The only time you can control the invocation type is when you are invoking the 
Lambda function yourself using the `invoke` operation (for example, invoking a Lambda function on demand 
from your custom application).
* In order to process AWS events, your Lambda functions may need to include additional libraries, depending 
on the programming language used to create the function. Functions written in Node.js or Python do not 
require any additional libraries. For C#, you need to include AWS Lambda for .NET Core. For Java, you need 
to include `aws-lambda-java-libs`.

