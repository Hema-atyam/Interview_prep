# 🔐 AWS IAM – DevOps & Interview Prep (Basic to Pro)

---

## 📘 1. IAM Basics

**IAM (Identity and Access Management)** is AWS’s service to securely control access to AWS resources.

### Key IAM Entities:

- **User**: Represents a person or application with long-term credentials.
- **Group**: A collection of IAM users.
- **Role**: A set of permissions assumed temporarily by AWS services or users.
- **Policy**: A document that defines what actions are allowed or denied.
- **Principal**: The identity (user, role, service) that is making a request.

---

## 📄 2. Policies

### Types of IAM Policies:

- **Managed Policies**: AWS-provided or customer-created reusable policies.
- **Inline Policies**: Policies embedded directly within a user, group, or role.
- **Resource-Based Policies**: Policies attached directly to AWS resources (e.g., S3 buckets).

### Key Elements of a Policy:

- Version
- Statement
  - Effect (Allow or Deny)
  - Action (e.g., `s3:PutObject`)
  - Resource (e.g., specific bucket ARN)
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::mybucket/*"
    }
  ]
}
```
---

## 🔁 3. IAM Roles & AssumeRole

- IAM Roles do not have long-term credentials.
- A role is assumed using AWS STS (Security Token Service).
- The **trust policy** defines who is allowed to assume the role.
- Useful for cross-account access, EC2/Lambda roles, and federated access.

**Trust Policy Example**

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "AWS": "arn:aws:iam::222222222222:root" },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

---

## 🏢 4. Cross-Account Access – Step-by-Step

### Use Case:

You want Account A (DevOps account) to access resources in Account B (App Team account).

### Step-by-Step Process:

1. In **Account B** (target):
   - Create an IAM Role.
   - Set the trust policy to allow Account A to assume it.
   - Attach permissions required for Account A to access resources in Account B.

**Trust policy:**
```
{
  "Effect": "Allow",
  "Principal": { "AWS": "arn:aws:iam::111111111111:root" },
  "Action": "sts:AssumeRole"
}
```

2. In **Account A** (source):
   - Create an IAM policy allowing the user or service to assume the role in Account B.
   - Attach the policy to the IAM user or role in Account A.
  
**Policy for user in Account A:**
```
{
  "Effect": "Allow",
  "Action": "sts:AssumeRole",
  "Resource": "arn:aws:iam::222222222222:role/CrossAccountAccessRole"
}
```

3. Assume the role from Account A (e.g., via CLI or SDK) using temporary credentials.
**🚀 Step 3: Assume role from CLI:**

```
aws sts assume-role \
  --role-arn arn:aws:iam::222222222222:role/CrossAccountAccessRole \
  --role-session-name crossaccount-session

```
---

## 🎯 5. Real-World IAM Scenarios

### Scenario 1: Jenkins in EC2 Needs to Upload Files to S3

**Problem:** Jenkins pipeline fails when trying to upload build artifacts.

**Fix:**
- Attach an IAM Role to the EC2 instance with permission to write to S3.
- Avoid using access keys by using instance roles.

---

### Scenario 2: Lambda Needs to Read from Secrets Manager

**Problem:** Lambda throws permission errors while fetching a secret.

**Fix:**
- Assign an IAM Role to the Lambda function.
- Attach permissions to read from Secrets Manager.

---

### Scenario 3: Developer Gets AccessDenied Trying to Launch EC2

**Problem:** Developer can’t launch EC2 instances even though they have login access.

**Fix:**
- Attach a policy with `ec2:RunInstances` and related permissions.
- If using a launch role, ensure `iam:PassRole` permission is also included.

---

### Scenario 4: sts:AssumeRole Fails with Not Authorized Error

**Problem:** User in Account A cannot assume the role in Account B.

**Fix:**
- Trust policy in Account B may be missing or incorrect.
- IAM user in Account A must have `sts:AssumeRole` permission.

---

### Scenario 5: Cross-Account Access to S3 Bucket Fails

**Problem:** Role is assumed successfully, but S3 access is denied.

**Fix:**
- Add a resource-based bucket policy in the target account.
- Ensure assumed role has permissions to access the S3 bucket.

---

## 🔐 6. IAM Security Best Practices

- Use IAM Roles for EC2, Lambda, and other services (avoid long-term keys).
- Enforce MFA for all console users.
- Follow the principle of least privilege.
- Enable IAM Access Analyzer to monitor external access.
- Use resource-based policies for external access when needed.
- Rotate access keys regularly and avoid hardcoding them.

---

## ❗ 7. Common IAM Errors – Root Causes and Fixes

| Error Message | Root Cause | Recommended Fix |
|---------------|------------|------------------|
| AccessDenied | Missing permissions | Attach the correct IAM policy |
| Not authorized to perform sts:AssumeRole | Policy or trust relationship missing | Add `sts:AssumeRole` and fix trust policy |
| Role assumed, but still denied | Role doesn’t have required permissions | Attach correct permissions to the role |
| iam:PassRole is denied | Attempting to assign a role without PassRole | Grant `iam:PassRole` permission |
| S3 access denied | Missing bucket policy or role permission | Add S3 bucket policy and verify permissions |

---

## 💬 8. IAM Interview Questions – Advanced Level

### Q1: What is the difference between IAM User and IAM Role?

- Users have long-term credentials; roles are assumed for temporary access.

### Q2: How would you implement cross-account access securely?

- Create a role in the target account with a trust policy allowing the source account.
- Allow users in the source account to assume the role using `sts:AssumeRole`.

### Q3: How can you restrict a user to only access a specific path in an S3 bucket?

- Use a policy with a resource ARN that includes the bucket and folder prefix.

  `"Resource": "arn:aws:s3:::my-bucket/my-folder/*"`

### Q4: Why do you need `iam:PassRole`?

- Required when an AWS service (like EC2 or Lambda) needs to use a role.

### Q5: How are IAM policies evaluated?

- Explicit Deny > Allow > Implicit Deny.

### Q6: How can you detect unintended IAM access?

- Use IAM Access Analyzer.
- Review CloudTrail logs for access patterns.

### Q7: Can an IAM user in Account A access resources in Account B?

- Yes, via:
  - Cross-account IAM Role with trust policy.
  - Or via resource-based policy (e.g., on S3).

### Q8: What’s the difference between identity-based and resource-based policies?

- Identity-based: attached to IAM users/roles.
- Resource-based: attached to AWS resources (S3, Lambda, etc.).

---

## 🧠 Final IAM Takeaways

- Use roles instead of access keys whenever possible.
- Cross-account access = trust policy + `sts:AssumeRole`.
- Follow least privilege and audit regularly.
- IAM powers **security and automation** — essential for every DevOps engineer.
