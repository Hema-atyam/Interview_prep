# Amazon S3 Interview Questions and Answers

This document contains comprehensive Amazon S3 interview questions and answers, including follow-up questions, real-world scenarios, troubleshooting issues, root causes, and fixes.

---

## 🧱 Level 1: Basics

### Q1. What is Amazon S3?
**A:** Amazon S3 (Simple Storage Service) is a highly scalable, durable, and secure object storage service provided by AWS. It stores data as objects within buckets using a flat namespace.

---

### Q2. What is the maximum size of an object in S3?
**A:** 5 TB. For objects larger than 100 MB, AWS recommends using multipart upload.

---

### Q3. What is the difference between a bucket and an object?
**A:** A bucket is a container for storing objects. An object is the actual data stored in the bucket, identified by a unique key.

---

### Q4. How is data organized in S3?
**A:** S3 uses a flat namespace. Folder-like structures are simulated using prefixes in object keys (e.g., `folder1/file.txt`).

---

## 🧰 Level 2: Intermediate

### Q5. What are S3 storage classes?
**A:** Storage classes define cost and access frequency:
- Standard
- Intelligent-Tiering
- Standard-IA
- One Zone-IA
- Glacier
- Glacier Deep Archive

---

### Q6. How does S3 ensure durability and availability?
**A:** S3 provides 99.999999999% durability by redundantly storing data across multiple devices and facilities.

---

### Q7. What is versioning in S3?
**A:** Versioning allows you to preserve, retrieve, and restore every version of every object stored in a bucket.

---

### Q8. What is a pre-signed URL?
**A:** A URL that grants temporary access to a private object in S3.

---

## 🚀 Level 3: Advanced

### Q9. How does S3 handle consistency?
**A:** S3 provides strong read-after-write consistency for PUTs and DELETEs of objects in all regions.

---

### Q10. What is Cross-Region Replication (CRR)?
**A:** Automatically replicates objects from one bucket to another in a different AWS region.

---

### Q11. How do you secure data in S3?
**A:** Use IAM policies, bucket policies, encryption (SSE-S3, SSE-KMS, SSE-C), MFA Delete, and access logs.

---

### Q12. What is S3 Transfer Acceleration?
**A:** Speeds up uploads to S3 using Amazon CloudFront edge locations.

---

## 🧠 Real-World Scenarios

### Q13. How would you host a static website on S3?
**A:** Enable static website hosting, upload files, set public read permissions, and optionally use Route 53 + CloudFront.

---

### Q14. How do you implement a data lake using S3?
**A:** Use S3 as the storage layer, AWS Glue for cataloging, and Athena or Redshift Spectrum for querying.

---

### Q15. How would you prevent accidental deletion of critical data?
**A:** Enable versioning, MFA Delete, and restrict delete permissions via IAM.

---

## 🔍 Follow-Up Challenges

### Q16. How do you design object keys for performance?
**A:** Avoid sequential prefixes. Use hashed or random prefixes to distribute load evenly.

---

### Q17. What happens if you delete a delete marker?
**A:** The object becomes visible again (the latest version is restored).

---

### Q18. How do you enforce encryption at upload time?
**A:** Use a bucket policy that denies `PutObject` unless the request includes `x-amz-server-side-encryption`.

---

### Q19. How do you list only recently modified objects?
**A:** Use S3 Inventory + Athena or maintain metadata in DynamoDB.

---

### Q20. What happens if the destination bucket in CRR has versioning disabled?
**A:** Replication fails. Both source and destination must have versioning enabled.

---

## 🛠️ Troubleshooting

### Issue: Access Denied
**Root Cause:** Missing permissions, explicit deny, or incorrect bucket policy.  
**Fix:** Use IAM Policy Simulator, check bucket policy and ACLs.

---

### Issue: Multipart upload fails
**Root Cause:** Network issues or incorrect part size.  
**Fix:** Use retry logic, ensure minimum part size (5 MB), and complete the upload.

---

### Issue: Lifecycle rule not deleting objects
**Root Cause:** Rule misconfiguration or versioning not considered.  
**Fix:** Ensure rules target correct prefix/tags and handle non-current versions.

---

### Issue: Pre-signed URL not working
**Root Cause:** Expired URL, wrong credentials, or missing permissions.  
**Fix:** Regenerate with correct credentials and permissions.

---

### Issue: Replication not working
**Root Cause:** Versioning not enabled or missing permissions.  
**Fix:** Enable versioning on both buckets and check IAM roles.

---
