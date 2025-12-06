---
title: "• Lambda"
nav_order: 13
parent: "• Serverless (DevOps Scope)"
grand_parent: "• AWS"
grand_grand_parent: "4. Cloud Platforms"
---
- [Boto3 documentation URL](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/index.html)


# ⭐ **AWS Lambda**


---

## **📌 1. What is AWS Lambda?**

AWS Lambda is a **serverless compute service** that lets you run code **without managing servers**.

You only provide:

* **Your code**
* **Memory required**
* **Triggers (events)**

AWS handles:

* Scaling
* High availability
* Patching
* Runtime management

You pay **only for the time your code runs**.

---

## **📌 2. Key Benefits of Lambda**

* 🚀 *No servers to manage*
* 🟢 *Automatic scaling*
* 💸 *Pay-per-use* (100ms billing)
* 🔄 *Event-driven execution*
* 🔐 *Built-in security (IAM)*
* 📦 *Supports multiple languages*
* ⚡ *Highly available & fault-tolerant*

---

## **📌 3. Languages Supported by Lambda**

| Language       | Runtime                                 |
| -------------- | --------------------------------------- |
| Python         | 3.7, 3.8, 3.9                           |
| Node.js        | 16, 18                                  |
| Java           | 8, 11                                   |
| Go             | 1.x                                     |
| Ruby           | 2.7                                     |
| .NET           | Core 3.1, .NET 6                        |
| Custom Runtime | Any language via AWS Lambda Runtime API |

---

## **📌 4. Lambda Architecture Overview**

Lambda has 4 main components:

1. **Function Code**
2. **Event Source (Trigger)**
3. **Execution Environment**
4. **Permissions (IAM Role)**

---

## **📌 5. How Lambda Executes Your Code**

### Execution Phases:

1. **Cold Start**

   * New environment created
   * Function initialization happens

2. **Warm Start**

   * Reuses existing environment
   * Faster execution

---

## **📌 6. Supported Lambda Triggers (Event Sources)**

Lambda can be triggered by:

### 🔹 **Compute / Networking**

* API Gateway
* Application Load Balancer (ALB)

### 🔹 **Storage**

* S3 (file upload, delete events)

### 🔹 **Database / Data Streams**

* DynamoDB Streams
* Kinesis Streams
* MSK (Kafka)

### 🔹 **Messaging**

* SNS
* SQS
* EventBridge
* Amazon MQ

### 🔹 **Authentication**

* Cognito

### 🔹 **IoT**

* AWS IoT Core

### 🔹 **Other**

* CloudWatch Events (cron jobs)
* CloudFormation custom resources
* Step Functions

---

## **📌 7. Lambda Execution Role (IAM Role)**

Lambda needs permission to access other AWS services.

Example policy to allow Lambda to read S3:

```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject"],
  "Resource": ["arn:aws:s3:::my-bucket/*"]
}
```

Role trust policy:

```json
{
  "Effect": "Allow",
  "Principal": { "Service": "lambda.amazonaws.com" },
  "Action": "sts:AssumeRole"
}
```

---

## **📌 8. Lambda Function Configuration Parameters**

When creating a Lambda function, you must configure:

| Parameter                 | Description                 |
| ------------------------- | --------------------------- |
| **Runtime**               | Python, Node.js, Java, etc. |
| **Memory**                | 128 MB to 10 GB             |
| **Timeout**               | Max 15 minutes              |
| **Environment Variables** | key-value configuration     |
| **Layers**                | Additional libraries & SDKs |
| **VPC Access**            | For private subnet access   |
| **Concurrency**           | Reserved or on-demand       |

---

## **📌 9. Lambda Concurrency**

Defines how many functions can run simultaneously.

### Concurrency Types:

1. **Unreserved Concurrency** — default shared pool
2. **Reserved Concurrency** — guarantee execution limit
3. **Provisioned Concurrency** — eliminates cold start

---

## **📌 10. Lambda Layers (Very Important)**

Layers allow you to include:

* Dependencies
* Libraries
* Runtime packages

You can share Layers across multiple Lambda functions.

Example: Python Lambda with custom libraries.

---

## **📌 11. Lambda Networking Options**

Lambda can run in:

### **1. Public Mode (Default)**

* No VPC access
* Can access internet directly

### **2. Private VPC Access**

To access:

* RDS
* EC2
* ElastiCache
* VPC endpoints

It attaches Elastic Network Interfaces (ENI).

---

## **📌 12. Lambda Environment Variables**

Use key-value pairs to store:

* Database connection strings
* API keys
* Config values

Example:

```bash
DB_USER=admin
DB_PASS=xyz123
```

---

## **📌 13. Lambda Logging & Monitoring**

| Service                | Purpose                             |
| ---------------------- | ----------------------------------- |
| **CloudWatch Logs**    | Print logs (`print`, `console.log`) |
| **CloudWatch Metrics** | Invocation count, duration, errors  |
| **X-Ray**              | Tracing and performance analysis    |

In code:

```python
print("Lambda executed!")
```

---

## **📌 14. Error Handling in Lambda**

### Lambda Retry Logic:

| Event Source                   | Retry Behavior                |
| ------------------------------ | ----------------------------- |
| **S3, SNS**                    | Retries automatically         |
| **SQS**                        | Keeps retrying until removed  |
| **Kinesis / DynamoDB Streams** | Retries forever until success |
| **API Gateway**                | No retry                      |

---

## **📌 15. DLQ (Dead Letter Queue)**

Lambda can send failed events to:

* **SQS Queue**
* **SNS Topic**

Helps with debugging failed executions.

---

## **📌 16. Lambda Pricing**

Lambda pricing is based on:

1. **Number of requests**

   * First 1M requests: FREE
   * After that: $0.20 per 1M requests

2. **Duration**
   Billing = **GB-seconds**
   Example:

   * 128 MB RAM
   * Function runs for 1 second

Price = 1 second × 128MB rate

3. **Optional Costs**

* Provisioned Concurrency
* VPC networking (ENI cost)

---

## **📌 17. How to Create Lambda (Practical Steps)**

### **Step 1: Create Lambda**

* Runtime: Python 3.9
* IAM Role: BasicExecutionRole
* Function code:

```python
def lambda_handler(event, context):
    return "Hello from Lambda!"
```

### **Step 2: Add Trigger**

Example: S3

* Event: File upload (`PUT`)

### **Step 3: Test**

AWS Console → Test → Response

---

## **📌 18. Real-Time Use Cases for Lambda (DevOps & Cloud)**

### ✔ API backend (API Gateway + Lambda)

Serverless REST APIs

### ✔ S3 event processing

Resize images, process logs

### ✔ DynamoDB streams

Event-driven architecture

### ✔ Automated tasks

Stop/Start EC2
Automate backups
Rotate IAM keys

### ✔ CI/CD

Deploy Lambda using CodePipeline

### ✔ Cron Jobs

Using CloudWatch Events:

```
rate(5 minutes)
cron(0 12 * * ? *)
```

---

## **📌 19. Lambda Integration Patterns**

| Pattern            | Used For             |
| ------------------ | -------------------- |
| **Synchronous**    | API Gateway, ALB     |
| **Asynchronous**   | S3, SNS, EventBridge |
| **Stream-based**   | Kinesis, DynamoDB    |
| **Polling**        | SQS                  |
| **Step Functions** | Orchestration        |

---

## **📌 20. Lambda Limitations**

* Max execution time: **15 minutes**
* Max package size: **50 MB ZIP (uncompressed 250 MB)**
* Max environment variables: 4 KB
* No root access
* Limited networking performance
* Cold start in some cases

---

## **📌 21. Lambda Best Practices**

✔ Use environment variables
✔ Move dependencies to Lambda Layers
✔ Enable Provisioned Concurrency for production APIs
✔ Use VPC endpoints to reduce latency
✔ Keep functions small
✔ Use DLQs for failure handling
✔ Use CloudWatch Alarms
✔ Avoid long-running workloads

---

## **📌 22. Advanced Lambda Topics**

### 1️⃣ **Provisioned Concurrency**

Reduces cold start
Used for:

* High-traffic APIs
* Financial systems

### 2️⃣ **Lambda with Docker**

You can package Lambda in a **Docker container**:

* Up to 10 GB container size
* Use custom runtimes

### 3️⃣ **Lambda + Step Functions**

Used to build serverless workflows.

### 4️⃣ **Lambda@Edge (CloudFront)**

Runs Lambda at AWS edge locations:

* Modify HTTP headers
* Authentication at edge
* A/B testing

---


## ⭐ **AWS Lambda Interview Cheat-Sheet (Most Important 50 Q&A)**

---

## **🔥 SECTION 1: BASICS (MUST KNOW)**

### **1. What is AWS Lambda?**

Serverless compute service that runs code without provisioning servers.

### **2. What languages does Lambda support?**

Python, Node.js, Java, Go, Ruby, .NET, Custom Runtime, Container Images.

### **3. How much is maximum execution time?**

**15 minutes per invocation**.

### **4. What is a cold start?**

Initial delay when Lambda creates a new execution environment.

### **5. What is a warm start?**

Reused environment → fast execution.

### Q. Max execution time of Lambda?

**15 minutes**

### Q. How Lambda integrates with VPC?

Creates ENIs inside the VPC.

### Q. What is Lambda Layer?

Package to store shared dependencies/libraries.

### Q. Does Lambda need a server?

No — AWS manages everything (serverless).

### Q. When do you use Provisioned Concurrency?

For low-latency, high-traffic workloads.

### Q. How Lambda handles retries?

Depends on trigger type.

### Q. Pricing model of Lambda?

Requests + compute duration.

---

## **🔥 SECTION 2: TRIGGERS**

### **6. Common Lambda triggers?**

✔ S3
✔ API Gateway
✔ SNS
✔ SQS
✔ DynamoDB Streams
✔ Kinesis
✔ EventBridge (cron)
✔ ALB
✔ IoT
✔ CloudWatch Logs

### **7. Difference between Lambda synchronous & asynchronous invocation?**

* Sync → immediate response (API Gateway)
* Async → retries & DLQ (S3, SNS)

| Type             | Example     | Behavior             |
| ---------------- | ----------- | -------------------- |
| **Synchronous**  | API Gateway | Immediate response   |
| **Asynchronous** | S3, SNS     | Retries, DLQ support |

### **8. What triggers retry automatically?**

Asynchronous: SNS, S3, EventBridge.

---

## **🔥 SECTION 3: LAMBDA ARCHITECTURE & FLOW**

### **9. Lambda execution environment contains:**

✔ Runtime (Python, Node, etc.)
✔ `/tmp` storage (512 MB or 10 GB)
✔ Environment variables
✔ Handler code

### **10. What is handler?**

Main function Lambda executes.

Example:

```python
def lambda_handler(event, context):
    pass
```

### **11. What is `/tmp` directory?**

Temporary storage inside Lambda (persistent across warm starts).

---

## **🔥 SECTION 4: CONCURRENCY & SCALING**

### **12. What is concurrency?**

Number of Lambda executions happening at same time.

### **13. Concurrency Types?**

| Type            | Use                 |
| --------------- | ------------------- |
| **Unreserved**  | Default pool        |
| **Reserved**    | Guarantees capacity |
| **Provisioned** | No cold start       |

### **14. Does Lambda scale automatically?**

Yes — instantly (except cold starts).

### **15. Max concurrency limit?**

Default: **1000** (can increase via AWS support).

---

## **🔥 SECTION 5: NETWORKING**

### **16. Can Lambda access VPC resources?**

Yes — using VPC configuration.

### **17. What happens when Lambda is placed inside VPC?**

It creates **ENIs** → might cause cold start delays.

### **18. How to avoid slow VPC Lambdas?**

✔ Use VPC Endpoints
✔ Use Provisioned Concurrency
✔ Keep high-traffic functions warm

---

## **🔥 SECTION 6: ERROR HANDLING & DLQ**

### **19. What happens on Lambda failure (async)?**

AWS automatically retries.

### **20. What is DLQ (Dead Letter Queue)?**

Stores failed events in:

* SQS
* SNS

### **21. What is Lambda Retry Policy?**

Async → 2 retries
Stream-based → retries until success
Synchronous → no retry

---

## **🔥 SECTION 7: SECURITY**

### **22. How does Lambda get permissions?**

IAM Execution Role.

### **23. How to secure Lambda environment variables?**

Use **KMS encryption**.

### **24. How to allow only specific S3 bucket?**

```json
{
  "Effect": "Allow",
  "Action": "s3:*",
  "Resource": ["arn:aws:s3:::bucket/*"]
}
```

---

## **🔥 SECTION 8: LOGGING & MONITORING**

### **25. Where does Lambda log output go?**

CloudWatch Logs.

### **26. Key Lambda metrics:**

* Invocations
* Duration
* Throttles
* Errors
* Iterator Age (stream-based)

---

## **🔥 SECTION 9: COST OPTIMIZATION**

### **27. Lambda billing model?**

Requests + Duration + Memory.

### **28. Free tier?**

1M requests + 400,000 GB-seconds per month.

### **29. How to reduce Lambda cost?**

✔ Right-size memory
✔ Move shared code to Layers
✔ Optimize execution time
✔ Use reserved/provisioned concurrency only when needed

---

## **🔥 SECTION 10: LAMBDA LAYERS**

### **30. What are Lambda Layers?**

Packages that contain libraries or dependencies shared across functions.

### **31. Why use Layers?**

✔ Reduce deployment package size
✔ Standardize dependencies
✔ Multiple functions use same layer

---

## **🔥 SECTION 11: BEST PRACTICES**

### **32. Keep Lambda small & single-purpose**

Follow microservice design.

### **33. Use environment variables for configs**

Use Secrets Manager for passwords.

### **34. Use async patterns for long work**

Or use Step Functions.

### **35. Always set timeout**

Avoid infinite loops.

### **36. Use CloudWatch Alarms**

Monitor failures and throttles.

---

## **🔥 SECTION 12: ADVANCED TOPICS**

### **37. What is Lambda@Edge?**

Runs Lambda functions **near users** at CloudFront edge locations.

Used for:

* Global redirects
* Header processing
* Authentication

### **38. What is Container Image Lambda?**

Lambda packaged as **Docker image (up to 10 GB)**.

### **39. What is Step Functions?**

Serverless workflow to orchestrate Lambda functions.

### **40. What is Lambda Event Filtering?**

Used with DynamoDB Streams, Kinesis to filter events at source → cheaper & faster.

---

## **🔥 SECTION 13: COMMON INTERVIEW SCENARIOS**

### **41. Lambda keeps timing out — why?**

* Wrong VPC configuration
* No internet/NAT gateway
* RDS in private subnet
* External API slow

### **42. Lambda cold start happens often — solution?**

* Use Provisioned Concurrency
* Increase memory
* Use global variables
* Avoid initializing heavy modules

### **43. Lambda cannot access S3 — why?**

* Wrong IAM role
* Wrong bucket policy
* VPC endpoint missing

### **44. Lambda connected to RDS is slow — reason?**

* Wrong connection pooling
* Too many connections
* Use RDS Proxy

### **45. Should Lambda be used for heavy computation?**

No — use ECS/EKS/Batch.

---

## **🔥 SECTION 14: Lambda Limits (Important)**

| Resource        | Limit              |
| --------------- | ------------------ |
| Memory          | 128 MB – 10,240 MB |
| Timeout         | 15 minutes         |
| Deployment ZIP  | 50 MB              |
| Container Image | 10 GB              |
| Env Variables   | 4 KB               |
| `/tmp` storage  | 512 MB or 10 GB    |
| Concurrency     | 1000 default       |

---

## **🔥 SECTION 15: Hands-On Interview Task Examples**

### **46. Write a Lambda function to resize S3 images**

→ Trigger S3 PUT event
→ Lambda resizes using PIL library
→ Save processed image to another bucket

### **47. EC2 automatic stop at 7 PM daily**

→ CloudWatch cron → Lambda → StopInstances API

### **48. DynamoDB Stream → Lambda → SQS**

→ Event-driven architecture

### **49. Lambda + API Gateway → CRUD app**

→ Standard serverless API

### **50. Lambda to send emails via SES**

→ Process event
→ SES send email

---


==================================


## ⭐ **1. API Gateway → Lambda Architecture Diagram**



              ┌───────────────────────┐
              │       Client          │
              │  (Browser / Mobile)   │
              └───────────┬───────────┘
                          │  HTTPS
                          ▼
               ┌─────────────────────┐
               │     API Gateway     │
               │  - REST / HTTP API  │
               │  - Auth / Routing   │
               └───────────┬────────┘
                           │ Invoke
                           ▼
               ┌─────────────────────┐
               │      Lambda         │
               │  Business Logic     │
               └───────────┬────────┘
                           │
                           ▼
               ┌─────────────────────┐
               │   DynamoDB / S3     │
               │   RDS / SNS / SQS   │
               └─────────────────────┘


---

## **📘 ASCII Architecture Diagram**

```
          ┌──────────────────────┐
          │      Clients         │
          │  (Web / Mobile App)  │
          └─────────┬────────────┘
                    │  HTTPS Request
                    ▼
        ┌────────────────────────────┐
        │       API Gateway          │
        │   - REST API / HTTP API    │
        │   - Routing & Validation   │
        └─────────┬──────────────────┘
                  │ Invoke
                  ▼
        ┌────────────────────────────┐
        │        AWS Lambda          │
        │ - Execute backend logic    │
        │ - Reads/Writes to DB/S3    │
        └─────────┬──────────────────┘
                  │ Response
                  ▼
        ┌────────────────────────────┐
        │        API Gateway         │
        └─────────┬──────────────────┘
                  │
                  ▼
          ┌──────────────────────┐
          │       Client         │
          └──────────────────────┘
```

---

# # ⭐ **2. S3 → Lambda (Event-Based Trigger) Architecture Diagram**

---

## **📘 ASCII Diagram**

```
         ┌────────────────────────┐
         │       S3 Bucket        │
         │ (Upload / Delete File) │
         └──────────┬────────────┘
                    │ Event Trigger
                    ▼
         ┌────────────────────────┐
         │      AWS Lambda        │
         │ - Process File         │
         │ - Resize Images        │
         │ - Parse Logs           │
         └──────────┬────────────┘
                    │ Writes Output
                    ▼
   ┌─────────────────────────────────────────┐
   │    DynamoDB / S3 / SNS / SQS / RDS      │
   └─────────────────────────────────────────┘
```

**Use cases:**
✔ Image resize
✔ CSV processing
✔ Log processing
✔ Data transformation
✔ Notifications (SNS/SQS)

---

# # ⭐ **3. CloudWatch Cron → Lambda (Scheduled Task) Architecture Diagram**

## **📘 ASCII Diagram**

```
         ┌──────────────────────────┐
         │ Amazon CloudWatch Events │
         │    (Cron Schedule)       │
         │  rate(5 mins) / cron(*)  │
         └───────────┬─────────────┘
                     │ Invoke
                     ▼
         ┌──────────────────────────┐
         │       AWS Lambda         │
         │ - Automated cleanup      │
         │ - Start/stop EC2         │
         │ - Daily backup tasks     │
         │ - Email notifications    │
         └───────────┬─────────────┘
                     │ Logs
                     ▼
         ┌──────────────────────────┐
         │    CloudWatch Logs       │
         └──────────────────────────┘
```

---

# # ⭐ **BONUS: All 3 Diagrams in One Combined Architecture**

```
                  ┌─────────────────────────────┐
                  │          API Gateway        │
                  └──────────────┬──────────────┘
                                 │
                                 ▼
                       ┌────────────────┐
                       │    Lambda      │◀──────────────────────────┐
                       └────────────────┘                           │
                           ▲          ▲                              │
            S3 Event───────┘          └───────────CloudWatch Cron───┘
```
