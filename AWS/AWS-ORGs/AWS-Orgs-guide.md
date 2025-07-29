
---

## 🔐 What Are SCPs?

**SCPs** are JSON-based policies that specify the **maximum allowed permissions** for accounts in your organization.

> 🛡️ SCPs DO NOT GRANT PERMISSIONS — they only restrict them.

They apply to:
- All **IAM users & roles** within an account
- Even the **root user**

---

## 🧪 Real-World Use Cases for SCPs

| Use Case | SCP Example |
|----------|-------------|
| Deny use of unapproved regions | Deny all except `us-east-1`, `ap-south-1` |
| Deny root user activity | Explicit deny for `aws:PrincipalAccount=account-root` |
| Deny deleting CloudTrail or S3 logs | Protect security infrastructure |
| Enforce tagging policies | Deny API calls unless specific tags are present |
| Restrict certain services in Dev accounts | Allow only EC2, S3, IAM, etc. |

---

## 📎 Real-Time Scenarios & Troubleshooting

---

### ✅ Scenario 1: User Has Full IAM Permission But Still Can't Launch EC2  
**Root Cause:** SCP at OU level denies `ec2:*`  
**Fix:**  
- Review effective policies using **"Policy Simulator"**  
- Modify or remove the denying SCP

---

### ✅ Scenario 2: Developer Cannot Use `us-west-1` Even with Full Admin  
**Root Cause:** Org SCP limits regions  
**Fix:**  
- Modify SCP to allow required regions  
- Or move user/account to less restricted OU

---

### ✅ Scenario 3: CloudTrail Was Deleted From Logging Account  
**Root Cause:** No SCP guarding CloudTrail  
**Fix:**  
- Apply SCP that explicitly denies `cloudtrail:DeleteTrail` for all users including root

---

### ✅ Scenario 4: IAM Role Works in One Account but Not Another  
**Root Cause:** SCPs differ per OU  
**Fix:**  
- Compare SCPs across OUs  
- Move account or adjust SCP to match requirements

---

## ✅ SCP Best Practices

- Always **test SCPs on a test OU** before applying org-wide  
- Use **"Deny only" policies** (avoid allow-all unless necessary)  
- Deny critical actions like deleting CloudTrail, S3 log buckets  
- Explicitly deny unused AWS regions to reduce blast radius  
- Use `aws:PrincipalOrgID` to enforce org-only access in IAM policies  
- Combine with **Service Catalog + Control Tower** for complete governance

---

## 🧠 Interview Questions & Answers

---

### Q1: What’s the difference between SCPs and IAM policies?  
**A:**  
- **IAM policies** grant or deny permissions inside a single account  
- **SCPs** set global boundaries at the Org or OU level; IAM cannot override them

---

### Q2: Can SCPs restrict the root user?  
**A:**  
✅ Yes. Unlike IAM, SCPs apply even to the root user.

---

### Q3: What happens if you attach an SCP that denies `ec2:*` to an OU?  
**A:**  
All accounts in the OU will be blocked from using EC2, regardless of IAM policies.

---

### Q4: Do SCPs apply to newly created accounts?  
**A:**  
✅ Yes, if they’re in an OU with existing SCPs, they inherit those restrictions.

---

### Q5: Can SCPs grant permissions?  
**A:**  
❌ No. SCPs only set the maximum permission boundary. IAM policies still control access.

---

### Q6: What happens if no SCP is attached to an OU/account?  
**A:**  
The default **"FullAWSAccess"** SCP is applied, allowing all actions (unless overridden).

---

## 🔄 Common Gotchas

- SCPs apply org-wide: even full Admin IAM roles won’t override them  
- IAM permissions must exist **within SCP limits** to work  
- SCPs are evaluated along with all attached policies, **no short-circuiting**  
- Combining **SCP + Permissions Boundary + IAM Policy** creates effective access control

---



