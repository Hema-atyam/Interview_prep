# 🧠 AWS Lambda & Step Functions – DevOps & Interview-Ready Guide

---

## 🚀 What is AWS Lambda?

AWS Lambda is a **serverless compute service** that runs your code in response to events and automatically manages the underlying infrastructure.

- Pay per execution (no idle cost)
- Supports Python, Node.js, Java, Go, etc.
- Scales automatically (zero to thousands of requests/sec)
- 1M free invocations/month

---

## 🧩 Key Lambda Components

| Component        | Description |
|------------------|-------------|
| **Handler**      | Function that Lambda calls when invoked |
| **Event Source** | Triggers like S3, SQS, API Gateway, etc. |
| **Execution Role** | IAM role that Lambda assumes during execution |
| **Layers**       | Share libraries and dependencies |
| **Environment Variables** | Configuration at runtime |
| **Concurrency**  | Number of executions that can run in parallel |

---

## 📋 Common Event Sources

- **S3** – run Lambda on file upload/delete  
- **SQS** – consume and process queue messages  
- **API Gateway** – build REST APIs  
- **CloudWatch Events/Rules** – cron jobs or automation  
- **DynamoDB Streams** – process table changes  
- **EventBridge** – loosely coupled, event-driven systems  

---

## 🛠️ DevOps Use Cases

- Auto-tag newly launched EC2/EBS resources  
- Serverless cron jobs for backup snapshots  
- Auto-scale or clean-up idle resources  
- Notify via Slack/Teams on resource events  
- Integrate with GitOps workflows (e.g., Lambda on code commit)

---

## 🔁 AWS Step Functions

AWS Step Functions lets you coordinate multiple AWS services using a **visual workflow**.

- Use for **long-running workflows**, retries, and decisions
- Step = one function/task in the state machine
- Supports **parallel**, **choice**, **wait**, and **retry** states
- Integrates with Lambda, ECS, Batch, SageMaker, Glue, etc.

---

## 🧱 Step Function Key States

| State Type   | Use |
|--------------|-----|
| **Task**     | Executes a Lambda or service integration |
| **Choice**   | Conditional branching |
| **Parallel** | Run multiple steps simultaneously |
| **Wait**     | Pause before next step |
| **Retry**    | Automatically retry failed states |
| **Catch**    | Handle error and redirect to fallback |
| **Map**      | Iterate over an array of items |

---

## 🎯 Real-World Use Cases for Lambda + Step Functions

### ✅ 1. Automated Serverless ETL Pipeline  
**Trigger:** S3 file upload  
**Steps:**  
1. Parse metadata → 2. Transform data → 3. Load to DynamoDB → 4. Notify team  
**Tech:** Lambda + Step Functions + S3 + DynamoDB + SNS

---

### ✅ 2. Approval Workflow (HR/IT)  
**Trigger:** User submits request  
**Steps:**  
1. Lambda validates form  
2. Choice → If data valid, wait for approval  
3. On approval, launch EC2  
**Tech:** Step Functions + Lambda + SNS + SSM

---

### ✅ 3. Auto-Cleanup of Unused EC2 Volumes  
**Trigger:** Scheduled daily  
**Steps:**  
1. List unattached volumes  
2. Filter based on age  
3. Delete  
**Tech:** CloudWatch Event → Lambda

---

### ✅ 4. SQS Queue Processor  
**Trigger:** SQS message  
**Steps:**  
- Lambda consumes, processes, and stores result  
- Automatically scales up consumers  
**Note:** Make sure to handle retry and DLQ

---

### ✅ 5. GitOps Pipeline Orchestration  
**Trigger:** CodeCommit Push  
**Steps:**  
1. Code scan → 2. Build → 3. Deploy → 4. Notify  
**Tech:** Lambda + Step Functions + CodeBuild + SNS

---

## 🧪 Troubleshooting + Root Causes

---

### ⚠️ Scenario 1: Lambda Times Out  
**Cause:** Default timeout too low  
**Fix:** Increase timeout up to 15 minutes

---

### ⚠️ Scenario 2: Lambda Fails With `AccessDenied`  
**Cause:** Missing IAM permissions  
**Fix:** Add required actions to Lambda execution role

---

### ⚠️ Scenario 3: Step Function Retry Loop  
**Cause:** No exit condition, improper error handling  
**Fix:** Use `Catch` and `Retry` blocks with proper `MaxAttempts`

---

### ⚠️ Scenario 4: S3 Trigger Not Working  
**Cause:** No permission to invoke Lambda  
**Fix:** Add S3 permission to Lambda invoke policy

---

### ⚠️ Scenario 5: API Gateway Returns 500  
**Cause:** Lambda error not handled properly  
**Fix:** Return proper JSON structure & handle exceptions in code

---

## 📈 Monitoring

| Tool             | Purpose |
|------------------|---------|
| **CloudWatch Logs** | View logs of Lambda executions |
| **CloudWatch Metrics** | Monitor invocations, errors, duration, throttles |
| **X-Ray**           | Traces request through Lambda, API Gateway, etc. |
| **Step Function Console** | Visual history of each state execution |

---

## 🔐 Security Best Practices

- Use **least-privilege IAM roles**  
- Rotate secrets via **Secrets Manager**, not env vars  
- Enable **VPC access** only if needed (adds latency)  
- Encrypt payloads at rest/in transit  
- Avoid hardcoding sensitive values — use **Parameter Store**

---

## 🧠 Interview Questions & Answers

---

### Q1: What's the cold start in Lambda?  
**A:** Delay that occurs during function initialization. Worse for VPC-attached or Java Lambdas.  
**Mitigation:** Use provisioned concurrency or keep function warm.

---

### Q2: How is concurrency handled in Lambda?  
**A:** Lambda scales horizontally.  
- Default: 1,000 concurrent executions  
- Use reserved or provisioned concurrency if needed

---

### Q3: Difference between `Retry` and `Catch` in Step Functions?  
**A:**  
- `Retry`: Automatically retries on failure  
- `Catch`: Redirects execution to a fallback/cleanup state

---

### Q4: Lambda vs Step Functions?  
**A:**  
- Lambda = single, stateless function  
- Step Functions = orchestrates multiple Lambdas or services with logic, retries, and flow

---

### Q5: How do you handle failures in Lambda triggered from SQS?  
**A:**  
- Setup a **DLQ**  
- Handle retry in code  
- Ensure idempotency to avoid duplicate processing

---


