# AWS Lambda – Working, Cloud Administration & DevOps

## 1. What is AWS Lambda?

AWS Lambda is a **serverless, event-driven compute service** provided by Amazon Web Services (AWS).

It allows you to run code without managing servers, operating systems, virtual machines, or application infrastructure.

You upload your code, configure the required permissions and triggers, and AWS Lambda executes the code when an event occurs.

### Key Benefits

- No server management

- Automatic scaling

- Pay only for the compute time used

- Event-driven execution

- High availability

- Easy integration with AWS services

- Supports multiple programming languages

- Useful for automation and DevOps

---

## 2. Traditional Server vs AWS Lambda

### Traditional Server

```text

User

  |

Load Balancer

  |

EC2 Instance

  |

Application

  |

Database

```

With EC2, administrators need to manage:

- Server provisioning

- Operating system

- Patching

- CPU and memory

- Scaling

- Monitoring

- Security

- Application deployment

### AWS Lambda

```text

Event

  |

  v

AWS Lambda

  |

  v

Application Logic

  |

  +----> S3

  +----> DynamoDB

  +----> RDS

  +----> SNS

  +----> SQS

  +----> CloudWatch

```

AWS manages the underlying infrastructure.

---

# 3. How AWS Lambda Works

The basic Lambda workflow is:

```text

1. Developer writes code

        |

        v

2. Code is packaged and uploaded

        |

        v

3. Lambda function is created

        |

        v

4. IAM execution role is attached

        |

        v

5. Event source triggers Lambda

        |

        v

6. Lambda creates execution environment

        |

        v

7. Function executes

        |

        v

8. Logs/metrics are sent to CloudWatch

        |

        v

9. Execution environment may be reused

   for subsequent requests

```

---

# 4. Lambda Function

A Lambda function contains the application logic that AWS executes.

A function normally includes:

- Function name

- Runtime

- Handler

- Source code

- Memory

- Timeout

- Environment variables

- IAM execution role

- Layers

- Trigger/event source

- Concurrency settings

Example Python Lambda:

```python

def lambda_handler(event, context):

    name = event.get("name", "World")

    return {

        "statusCode": 200,

        "body": f"Hello {name}"

    }

```

---

# 5. Lambda Runtime

AWS Lambda supports several runtimes, including commonly used options such as:

- Python

- Node.js

- Java

- .NET

- Go

- Ruby

You select the runtime when creating the function.

Example:

```text

Runtime: Python

Version: Python 3.x

Handler: lambda_function.lambda_handler

```

---

# 6. Handler

The handler tells Lambda which function to execute.

For Python:

```text

lambda_function.lambda_handler

```

Where:

```text

lambda_function = Python file

lambda_handler  = Function name

```

Example:

```python

def lambda_handler(event, context):

    return "Hello AWS"

```

---

# 7. Lambda Event

Lambda is usually triggered by an event.

Examples:

- API Gateway request

- S3 object upload

- SQS message

- SNS notification

- EventBridge event

- DynamoDB Streams

- CloudWatch/EventBridge scheduled event

- ALB request

- IoT event

Example:

```text

S3 Bucket

   |

Object Uploaded

   |

   v

Lambda Function

   |

   v

Process File

```

---

# 8. Lambda Execution Role

A Lambda function needs permission to access AWS resources.

These permissions are provided through an **IAM execution role**.

Example:

```text

Lambda Function

      |

      v

IAM Execution Role

      |

      +----> S3 permissions

      +----> DynamoDB permissions

      +----> CloudWatch Logs

```

A basic execution role commonly needs permission to write logs to CloudWatch Logs.

For example, the AWS managed policy:

```text

AWSLambdaBasicExecutionRole

```

Additional permissions should be granted only when required.

---

# 9. Lambda Environment Variables

Environment variables allow configuration to be separated from application code.

Example:

```text

DB_HOST=mydatabase.example.com

DB_NAME=applicationdb

ENVIRONMENT=production

LOG_LEVEL=INFO

```

Python example:

```python

import os

environment = os.environ.get("ENVIRONMENT")

def lambda_handler(event, context):

    return {

        "environment": environment

    }

```

Do not store sensitive passwords or secrets directly in environment variables when a managed secret solution such as AWS Secrets Manager is more appropriate.

---

# 10. Lambda Layers

A Lambda Layer is a package containing reusable code, libraries, or dependencies.

Example:

```text

Lambda Function

      |

      +---- Function Code

      |

      +---- Layer

              |

              +---- Python libraries

              +---- Shared code

```

Layers help multiple Lambda functions reuse common dependencies.

---

# 11. Lambda Memory and CPU

Lambda memory can be configured for a function.

Increasing memory also provides more CPU capacity.

Example:

```text

128 MB

256 MB

512 MB

1024 MB

2048 MB

...

```

The appropriate memory size depends on the workload.

For CPU-intensive workloads, increasing memory can improve execution performance.

---

# 12. Lambda Timeout

Timeout specifies the maximum time a Lambda invocation can run.

Example:

```text

Timeout: 30 seconds

```

If the function does not complete before the timeout, Lambda terminates the invocation.

Choose a timeout appropriate for the workload.

---

# 13. Lambda Concurrency

Concurrency represents the number of Lambda executions running at the same time.

Example:

```text

Incoming Events

      |

      +----> Lambda #1

      +----> Lambda #2

      +----> Lambda #3

      +----> Lambda #4

```

Important concepts:

- Reserved concurrency

- Provisioned concurrency

- Account concurrency limits

### Reserved Concurrency

Reserved concurrency limits the maximum concurrent executions for a function and reserves capacity for that function.

### Provisioned Concurrency

Provisioned concurrency keeps execution environments initialized to reduce startup latency.

---

# 14. Cold Start

A cold start occurs when AWS needs to initialize a new Lambda execution environment.

Typical flow:

```text

Request

  |

  v

Create execution environment

  |

  v

Initialize runtime

  |

  v

Load application

  |

  v

Run function

```

Subsequent requests may reuse an existing environment.

Cold-start duration can depend on:

- Runtime

- Package size

- Dependencies

- Initialization code

- Configuration

Provisioned concurrency can be used when low startup latency is important.

---

# 15. Lambda Scaling

Lambda automatically scales based on incoming events.

Example:

```text

10 Requests

     |

     v

Lambda

     |

     +--> Execution 1

     +--> Execution 2

     +--> Execution 3

     ...

```

This means administrators do not normally need to manually create additional servers for increased Lambda request volume.

---

# 16. Lambda with API Gateway

A common serverless architecture is:

```text

Client

  |

  v

API Gateway

  |

  v

Lambda

  |

  +----> DynamoDB

  |

  +----> S3

  |

  +----> Other AWS Services

```

Example use case:

```text

POST /users

       |

       v

API Gateway

       |

       v

Lambda

       |

       v

DynamoDB

```

Lambda processes the request and returns a response.

---

# 17. Lambda with S3

Lambda can automatically respond to S3 events.

Example:

```text

User uploads image

       |

       v

S3 Bucket

       |

       v

S3 Event

       |

       v

Lambda

       |

       v

Image Processing

```

Common use cases:

- Image resizing

- File validation

- Metadata processing

- Data transformation

- Automated file processing

---

# 18. Lambda with SQS

Lambda can poll an SQS queue and process messages.

```text

Application

    |

    v

SQS Queue

    |

    v

Lambda

    |

    v

Process Message

```

This is useful for asynchronous processing.

Example:

```text

Order Created

     |

     v

SQS

     |

     v

Lambda

     |

     v

Process Order

```

---

# 19. Lambda with EventBridge

EventBridge can invoke Lambda based on events or schedules.

Example scheduled execution:

```text

EventBridge

    |

    | Every 10 minutes

    v

Lambda

    |

    v

Automation Task

```

Example:

```text

EventBridge Rule

       |

       v

Lambda

       |

       v

Check unused resources

       |

       v

Send notification

```

---

# 20. Lambda Monitoring with CloudWatch

AWS Lambda integrates with Amazon CloudWatch.

Important metrics include:

- Invocations

- Errors

- Duration

- Throttles

- Concurrent executions

- Iterator age for stream-based sources

Logs are available in:

```text

CloudWatch

   |

   v

Log Groups

   |

   v

/aws/lambda/<function-name>

```

Python example:

```python

import logging

logger = logging.getLogger()

logger.setLevel(logging.INFO)

def lambda_handler(event, context):

    logger.info("Lambda function started")

    return "Success"

```

---

# 21. Lambda Errors

Common Lambda problems include:

### Timeout

```text

Function execution exceeds configured timeout

```

### Permission Error

```text

Lambda does not have required IAM permissions

```

### Throttling

```text

Concurrency limit reached

```

### Dependency Error

```text

Required package/library is missing

```

### Configuration Error

```text

Incorrect environment variable or configuration

```

---

# 22. Dead-Letter Queue and Destinations

For asynchronous Lambda processing, failed events can be routed for further investigation.

Common options include:

- SQS

- SNS

- Lambda destinations

Example:

```text

Event

  |

  v

Lambda

  |

  X Failure

  |

  v

SQS Dead-Letter Queue

```

This helps administrators troubleshoot failed processing.

---

# 23. AWS Lambda in Cloud Administration

Cloud administrators commonly use Lambda for automation.

### Common Cloud Admin Tasks

- Start/stop EC2 instances

- Detect unused resources

- Clean old S3 objects

- Rotate resources

- Automate backups

- Check security configurations

- Process CloudTrail events

- Send alerts

- Schedule maintenance tasks

- Automate AWS resource tagging

Example:

```text

EventBridge Schedule

       |

       v

Lambda

       |

       v

Find stopped/unused resources

       |

       v

Send SNS Notification

```

---

# 24. AWS Lambda in DevOps

Lambda is useful for DevOps automation and event-driven workflows.

### Common DevOps Use Cases

- CI/CD automation

- Deployment automation

- Infrastructure automation

- Monitoring automation

- Security remediation

- Notification automation

- Resource cleanup

- Scheduled maintenance

- Event processing

- Integration between AWS services

Example:

```text

GitHub / CI Pipeline

        |

        v

AWS Service

        |

        v

Lambda

        |

        v

Automation

```

---

# 25. Lambda and CI/CD

Lambda code can be deployed through CI/CD pipelines.

Example:

```text

Developer

   |

   v

Git Repository

   |

   v

CI Pipeline

   |

   +---- Build

   |

   +---- Test

   |

   +---- Package

   |

   +---- Deploy

   |

   v

AWS Lambda

```

Common DevOps tools include:

- GitHub Actions

- Jenkins

- AWS CodePipeline

- AWS CodeBuild

- Terraform

- AWS SAM

- AWS CDK

---

# 26. Deploy Lambda Using AWS CLI

Create a deployment package:

```bash

zip function.zip lambda_function.py

```

Create Lambda:

```bash

aws lambda create-function \

  --function-name my-lambda \

  --runtime python3.x \

  --handler lambda_function.lambda_handler \

  --role arn:aws:iam::<ACCOUNT_ID>:role/lambda-execution-role \

  --zip-file fileb://function.zip

```

Update function code:

```bash

aws lambda update-function-code \

  --function-name my-lambda \

  --zip-file fileb://function.zip

```

Invoke the function:

```bash

aws lambda invoke \

  --function-name my-lambda \

  response.json

```

View response:

```bash

cat response.json

```

---

# 27. Lambda with Terraform

Example Terraform configuration:

```hcl

resource "aws_lambda_function" "example" {

  function_name = "my-lambda"

  filename         = "function.zip"

  source_code_hash = filebase64sha256("function.zip")

  handler = "lambda_function.lambda_handler"

  runtime = "python3.x"

  role = aws_iam_role.lambda_role.arn

}

```

Typical Terraform flow:

```text

Terraform Code

      |

      v

terraform init

      |

      v

terraform plan

      |

      v

terraform apply

      |

      v

AWS Lambda

```

---

# 28. Lambda Security Best Practices

### IAM

Follow the principle of least privilege.

Give the Lambda execution role only the permissions required.

### Secrets

Use:

- AWS Secrets Manager

- AWS Systems Manager Parameter Store

instead of hardcoding passwords.

### Networking

If Lambda requires private resources, configure it with the appropriate VPC subnets and security groups.

### Logging

Enable CloudWatch logging and monitor errors.

### Encryption

Use encryption for sensitive data and appropriate AWS KMS keys where required.

### Code Security

Scan dependencies and source code for vulnerabilities.

---

# 29. Lambda Versions

Lambda supports versions of a function.

Example:

```text

Function

 |

 +-- Version 1

 +-- Version 2

 +-- Version 3

```

Versions are immutable.

This helps DevOps teams maintain stable releases.

---

# 30. Lambda Aliases

Aliases provide a stable name for a Lambda version.

Example:

```text

prod  ---> Version 10

test  ---> Version 11

dev   ---> Version 12

```

A deployment can update the `prod` alias to point to a new version.

---

# 31. Lambda Deployment Strategies

Common deployment strategies include:

### All-at-once

```text

100% Traffic

      |

      v

New Version

```

### Canary

```text

95% ---> Old Version

 5% ---> New Version

```

If the new version works correctly:

```text

100% ---> New Version

```

### Linear

Traffic gradually moves to the new version.

---

# 32. Lambda Architecture Example

A practical serverless application:

```text

                 Internet

                    |

                    v

              API Gateway

                    |

                    v

                Lambda

                    |

          +---------+---------+

          |                   |

          v                   v

      DynamoDB               S3

          |

          v

     Application Data

Lambda

   |

   v

CloudWatch

   |

   +---- Logs

   +---- Metrics

   +---- Alarms

```

---

# 33. Real-Time DevOps Example

Suppose an EC2 instance becomes unhealthy.

A monitoring system generates an event:

```text

CloudWatch Alarm

       |

       v

EventBridge

       |

       v

Lambda

       |

       +---- Check EC2

       |

       +---- Restart instance

       |

       +---- Send SNS notification

```

This provides automated remediation.

---

# 34. Lambda Cost Concept

Lambda pricing is generally based on factors such as:

- Number of requests

- Compute duration

- Allocated memory

Always check current AWS pricing for exact rates before estimating production costs.

Cost optimization techniques:

- Reduce unnecessary executions

- Optimize function duration

- Optimize memory configuration

- Avoid excessive logging

- Batch events where appropriate

- Remove unused functions and resources

---

# 35. Troubleshooting Checklist

When Lambda fails, check:

```text

1. CloudWatch Logs

2. Lambda Metrics

3. IAM permissions

4. Runtime

5. Handler

6. Environment variables

7. Timeout

8. Memory

9. VPC networking

10. Security groups

11. Event source

12. Concurrency

13. Dependencies/layers

14. Dead-letter queue/destination

```

Useful CLI commands:

```bash

aws lambda get-function \

  --function-name my-lambda

```

```bash

aws lambda get-function-configuration \

  --function-name my-lambda

```

```bash

aws lambda list-functions

```

---

# 36. Important AWS Lambda Interview Questions

### What is AWS Lambda?

AWS Lambda is a serverless, event-driven compute service that runs code without requiring users to manage servers.

### What triggers Lambda?

Examples include:

- API Gateway

- S3

- SQS

- SNS

- EventBridge

- DynamoDB Streams

- ALB

### What is a Lambda execution role?

An IAM role that defines what AWS resources the Lambda function can access.

### What is a cold start?

The initialization of a new Lambda execution environment before processing an invocation.

### How does Lambda scale?

Lambda creates additional execution environments to handle concurrent invocations, subject to account and function concurrency limits.

### How do you monitor Lambda?

Use CloudWatch metrics, logs, alarms, and tracing/observability services where appropriate.

### How do you deploy Lambda?

Common methods include:

- AWS Console

- AWS CLI

- Terraform

- AWS SAM

- AWS CDK

- CI/CD pipelines

---

# 37. Cloud Admin + DevOps Summary

```text

                    AWS Lambda

                         |

        +----------------+----------------+

        |                |                |

   Cloud Admin        DevOps          Developers

        |                |                |

   Automation         CI/CD           Code

   Monitoring         Terraform       Testing

   Remediation        Jenkins         APIs

   Security           GitHub          Functions

        |                |                |

        +----------------+----------------+

                         |

                         v

                  AWS Infrastructure

```

## Key Takeaways

- Lambda is a serverless compute service.

- You don't manage the underlying servers.

- Lambda is event-driven.

- IAM controls access to AWS resources.

- CloudWatch provides monitoring and logs.

- Lambda can automatically scale based on workload.

- It is widely used for cloud administration automation.

- It integrates well with DevOps and CI/CD pipelines.

- Terraform can be used to provision Lambda infrastructure.

- EventBridge, S3, SQS, SNS, API Gateway, and CloudWatch are common integrations.

- Least-privilege IAM and proper monitoring are important for production workloads.

---

# 38. End-to-End DevOps Flow

```text

Developer

   |

   v

GitHub

   |

   v

CI/CD Pipeline

   |

   +---- Code Validation

   +---- Unit Tests

   +---- Security Scan

   +---- Package

   |

   v

Terraform / AWS SAM / CDK

   |

   v

AWS Lambda

   |

   +---- API Gateway

   +---- S3

   +---- SQS

   +---- DynamoDB

   +---- EventBridge

   |

   v

CloudWatch

   |

   +---- Logs

   +---- Metrics

   +---- Alarms

   |

   v

SNS / Notification

```


