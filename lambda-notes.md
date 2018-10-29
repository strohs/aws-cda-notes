Lambda Notes
======================================================
My own personal notes from reading the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)


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
