# 🔐 AWS KMS & Encryption – DevOps & Interview-Ready Guide

---

## 🔍 What Is AWS KMS?

AWS Key Management Service (KMS) is a fully managed service to **create, manage, and control encryption keys** used across AWS.

It allows:
- Secure storage & management of **Customer Master Keys (CMKs)**
- Encryption/decryption of data using **envelope encryption**
- Integration with AWS services (S3, EBS, RDS, Lambda, etc.)
- Granular control using **Key Policies**, **IAM**, and **Grants**

---

## 🔐 Types of Encryption in AWS

| Type                      | Description |
|---------------------------|-------------|
| **At Rest**               | Encrypts data stored on disk (S3, EBS, RDS) |
| **In Transit**            | TLS/SSL used when data moves between services |
| **In Use**                | Rare; uses techniques like enclaves or Nitro Confidential Computing |

---

## 🔑 Key Types in KMS

| Key Type         | Description |
|------------------|-------------|
| **AWS Managed Keys** | Created & managed by AWS (e.g., `aws/s3`) |
| **Customer Managed Keys (CMKs)** | You create and manage lifecycle, policies, rotation |
| **AWS Owned Keys**   | Shared across accounts, not visible |
| **Symmetric Keys**   | Same key used for encrypt/decrypt (most common) |
| **Asymmetric Keys**  | Public/private key pair for digital signatures or encryption |

---

## 🔁 Envelope Encryption

AWS uses envelope encryption for performance & security:

1. Generate a **Data Encryption Key (DEK)** locally
2. Encrypt DEK with a KMS key (CMK)
3. Store encrypted DEK with the data

🔐 KMS only encrypts/decrypts small payloads (4 KB max)

---

## 🔗 KMS Integration with AWS Services

| Service   | Encryption Options |
|-----------|--------------------|
| **S3**    | SSE-S3, SSE-KMS, SSE-C |
| **EBS**   | Encrypt root/secondary volumes with KMS key |
| **RDS**   | Encrypt storage + snapshots |
| **Lambda**| Environment variables can be encrypted using CMKs |
| **SQS/SNS** | KMS-encrypted queues or topics |
| **CloudTrail** | Log file encryption using KMS |

---

## 🧪 Real-World Scenarios + Root Cause & Fix

---

### ✅ Scenario 1: S3 Upload Fails with Access Denied  
**Root Cause:** KMS key policy doesn’t allow S3 to use the CMK  
**Fix:** Add required permissions in key policy (`kms:Encrypt`, `kms:GenerateDataKey` for S3 principal)

---

### ✅ Scenario 2: Lambda Can't Decrypt Environment Variable  
**Root Cause:** Execution role lacks permission to `kms:Decrypt`  
**Fix:** Add `kms:Decrypt` to Lambda IAM role for the CMK

---

### ✅ Scenario 3: Cross-Account Decryption Fails  
**Root Cause:** KMS key policy or grant missing in target account  
**Fix:**  
- Add cross-account IAM principal in CMK key policy  
- Optionally use **KMS Grants** for temporary, scoped access

---

### ✅ Scenario 4: RDS Snapshot Copy Fails Across Regions  
**Root Cause:** Source region CMK not accessible in target region  
**Fix:** Use a **replica CMK** in target region and enable cross-region copy permissions

---

## 🎓 Key Concepts for Interview

---

### 🔐 Key Rotation

| Type         | Description |
|--------------|-------------|
| **Auto-Rotation** | CMKs can rotate automatically every 365 days |
| **Manual Rotation** | You create new key and re-encrypt data manually |

---

### 🔐 Grants

Grants allow **temporary access** to use a CMK without changing the key policy.

Use when:
- A Lambda or application needs short-term access  
- You want fine-grained control over specific operations (`Encrypt`, `Decrypt`, etc.)

---

### 🔐 Key Policies vs IAM Policies

| Policy Type     | Applies To |
|------------------|------------|
| **Key Policy**   | Attached to the CMK, primary way to control access |
| **IAM Policy**   | Controls who can use `kms:*` actions |
| **Grants**       | Fine-grained, temporary access to a CMK |

---

## 🧠 Interview Questions & Answers

---

### Q1: What is envelope encryption and why is it used?  
**A:** It's a method where data is encrypted using a DEK (data key), and that key is encrypted using a CMK in KMS. Improves performance and security by not overloading KMS with large payloads.

---

### Q2: What’s the difference between AWS-managed and customer-managed keys?  
**A:**  
- **AWS-managed**: Handled by AWS, automatically used for services  
- **Customer-managed**: Full control over usage, rotation, key policy

---

### Q3: How do you share a KMS key across accounts?  
**A:** Add the target account’s IAM user/role to the key policy or use a grant.

---

### Q4: Can KMS be used for encrypting files manually?  
**A:** Yes, by using the `GenerateDataKey` API and encrypting data with the DEK on your own (client-side encryption pattern).

---

### Q5: How is data encrypted in S3 with KMS?  
**A:**  
- Data is encrypted using a DEK  
- DEK is encrypted using a CMK  
- CMK can be AWS-managed or customer-managed

---

### Q6: What happens if a CMK is disabled or deleted?  
**A:**  
- If **disabled**: You can’t decrypt data until it’s re-enabled  
- If **deleted**: Encrypted data becomes **irrecoverable**

---

## ✅ Best Practices

- Use **Customer Managed CMKs** for sensitive workloads  
- Enable **auto key rotation** for long-term usage  
- Use **grants** instead of bloating key policies  
- Monitor usage with **CloudTrail logs**  
- Tag CMKs for tracking cost and ownership  
- Do **not use the same CMK** for every service — separate keys by purpose/scope

---

## 📊 Monitoring & Auditing

- **CloudTrail** logs every `kms:Encrypt`, `kms:Decrypt`, `kms:GenerateDataKey`, etc.  
- **CloudWatch metrics** show key usage stats  
- Set **alarms** for unusual KMS activity  
- Use **AWS Config** to ensure all services use encryption

---


