# 🗄️ Amazon EBS vs EFS – DevOps & Interview Prep (Basic to Pro)

---

## 📘 1. What is Amazon EBS?

Amazon Elastic Block Store (EBS) provides **block-level storage volumes** that can be attached to EC2 instances.

### Key Features:

- Persistent storage for EC2
- Supports **snapshots**, **encryption**, **IOPS provisioning**
- One volume = attached to one instance at a time (except for Multi-Attach)

---

## 📘 2. What is Amazon EFS?

Amazon Elastic File System (EFS) provides **shared, elastic NFS-based file storage** that can be mounted by multiple EC2 or container instances.

### Key Features:

- POSIX-compliant NFS storage
- **Shared access** across instances (multi-AZ)
- Automatically scales capacity
- Supports encryption, backup, lifecycle policies

---

## 🔍 3. EBS vs EFS – Comparison Table

| Feature              | EBS                                 | EFS                                 |
|----------------------|--------------------------------------|--------------------------------------|
| Type                 | Block Storage                       | File Storage (NFS)                   |
| Access               | One EC2 at a time (or Multi-Attach) | Multiple EC2 or containers           |
| Throughput           | Fixed per volume                    | Scales with usage                    |
| Backup               | Snapshots (manual or DLM)           | AWS Backup or built-in               |
| Use Case             | Boot volume, DB, apps               | Shared storage for apps, home dirs   |
| Mounting             | Attach as device                    | Mount via NFS path                   |
| Cost                 | Pay per GB provisioned              | Pay per GB used                      |
| Availability Zone    | Single AZ                           | Multi-AZ (Highly available)          |

---

## 🧰 4. Real-World DevOps Scenarios

---

### ✅ Scenario 1: EC2 app loses data after reboot

**Root Cause:**
- App wrote to ephemeral (instance store) disk, not EBS

**Fix:**
- Attach EBS volume to EC2
- Mount it on boot via `/etc/fstab`
- Use it for persistent data storage

---

### ✅ Scenario 2: Need to scale file storage for multiple EC2s

**Root Cause:**
- EBS only supports one EC2 attachment (per AZ)

**Fix:**
- Use EFS for NFS-based shared storage
- Mount from all EC2s using mount target

---

### ✅ Scenario 3: Application backup is manual and error-prone

**Root Cause:**
- No automation around snapshots or backup lifecycle

**Fix:**
- Use **Amazon Data Lifecycle Manager (DLM)** for EBS snapshots
- Or use **AWS Backup** for centralized policy management (works for both EBS and EFS)

---

### ✅ Scenario 4: Disk latency for DB is too high

**Root Cause:**
- Using standard gp2 EBS with low baseline IOPS

**Fix:**
- Use **gp3** and configure IOPS separately
- Or use **io2/io2 Block Express** for high-performance IOPS workloads

---

### ✅ Scenario 5: Application needs shared persistent storage across AZs

**Root Cause:**
- EBS is AZ-specific

**Fix:**
- Use EFS which is **multi-AZ and scalable**
- Suitable for shared app data, config, logs, user uploads

---

## 🛠️ 5. DevOps Use Cases

| Use Case | Storage Type | Why |
|----------|--------------|-----|
| EC2 root/boot volume | EBS | Required for OS disk |
| Database storage (MySQL, MongoDB) | EBS gp3/io2 | Low-latency block storage |
| Shared web content or CMS | EFS | Shared NFS mounts across EC2 or containers |
| Jenkins workspace or pipeline artifacts | EFS | Shared state across nodes |
| Container logs or config | EBS or EFS | Depends on access pattern |
| Backup/DR | EBS Snapshots or AWS Backup | For point-in-time recovery |

---

## 🧠 6. Best Practices

| Area | Best Practice |
|------|---------------|
| **Mounting** | Mount EBS with `nofail` and proper UUID for resiliency |
| **Performance** | Choose gp3 or io2 for DB or latency-sensitive apps |
| **Backup** | Automate snapshots or use AWS Backup |
| **EFS Tuning** | Use EFS access points, lifecycle management to move infrequently accessed data to IA class |
| **Monitoring** | Use CloudWatch metrics (BurstBalance, IOPS, Throughput) |
| **Security** | Use encryption at rest (KMS) and in transit (TLS/NFS v4.1) |
| **EBS Multi-Attach** | Use only with clustered apps that support shared block storage (e.g., SAP, Oracle RAC) |

---

## 💬 7. Interview Questions – EBS & EFS

---

### Q1: When would you choose EBS over EFS?

- EBS is ideal for **single-instance, block-level storage** like boot volumes or databases.

---

### Q2: Can EBS be attached to multiple EC2 instances?

- Yes, **Multi-Attach** is supported on **io1/io2** volumes in the same AZ.
- But it’s only safe for **cluster-aware applications**.

---

### Q3: What are some use cases for EFS?

- Shared app configs, WordPress storage, user home directories, Jenkins artifacts, Lambda extensions.

---

### Q4: How is EFS billed differently from EBS?

- EBS charges **per GB provisioned**, even if unused.
- EFS charges **per GB used**, and has **Standard + Infrequent Access** pricing.

---

### Q5: How would you backup an EBS volume?

- Take **manual snapshots**, or use **Data Lifecycle Manager (DLM)**.
- You can also use **AWS Backup** for central management.

---

### Q6: What’s the difference between gp2, gp3, and io2 volumes?

- `gp2`: Baseline IOPS with burst
- `gp3`: Baseline + configurable IOPS/throughput
- `io2`: High performance, durable, best for critical DBs

---

### Q7: How do you migrate EBS data to another AZ or region?

- Create a snapshot → Copy to another region → Create volume → Attach to EC2

---

### Q8: How do you secure EBS and EFS?

- Use KMS for encryption at rest
- Use IAM and security groups/NFS mount options for EFS access

---

## ✅ Summary

- **EBS** = High-performance block storage, AZ-specific
- **EFS** = Shared file storage, scalable, multi-AZ
- Use **EBS** for EC2 boot + databases, **EFS** for shared data
- Automate backups using **DLM** or **AWS Backup**
- Optimize costs by using **gp3** or EFS lifecycle policies
- Ensure security with encryption, mount validation, and IAM/NFS access control

---

## 🔄 Cross-AZ and Cross-Account Support – EBS vs EFS

---

### 📦 Amazon EBS

| Feature | Supported? | Notes |
|--------|-------------|-------|
| Attach EBS across AZs | ❌ | EBS volumes are **AZ-specific**; you **cannot directly attach** to an EC2 in another AZ. Must **create snapshot → restore in target AZ**. |
| Attach EBS across accounts | 🔄* (Yes, indirectly) | You can **share EBS snapshots** with another AWS account (unencrypted or encrypted with KMS grants). Recipient can then create a new volume in their own account. |

#### ✅ Real-World Use Case: Cross-AZ Migration
- Snapshot your EBS volume.
- Copy the snapshot to another AZ or region.
- Create a volume from the snapshot.
- Attach to EC2 in target AZ.

---

### 🗂️ Amazon EFS

| Feature | Supported? | Notes |
|--------|-------------|-------|
| Mount EFS across AZs | ✅ | **Fully supported** — EFS is **multi-AZ** by default. That’s its strength. You can mount it from any AZ in the region. |
| Mount EFS across accounts | ❌ (Not directly) | **Cross-account mounting is not supported natively**. You’d need to use **VPC Peering + NFS + Security Group + mount target config** — not secure or recommended in most cases. |

#### ✅ Real-World Use Case: Shared CMS, Jenkins, or logs
- Create EFS in one VPC.
- Attach mount targets in each AZ.
- EC2 or EKS pods in any AZ can mount using the NFS mount path.

---

## 🧠 Interview Tip

> **Q: Can I attach an EBS volume from one AZ to an EC2 instance in another AZ?**  
> ❌ No, EBS is AZ-scoped. You need to snapshot and restore it in the desired AZ.

> **Q: Can I share an EBS volume with another AWS account?**  
> ✅ Not the volume itself — but you can **share the snapshot**, and they can create their own volume from it.

> **Q: Is EFS accessible across AZs?**  
> ✅ Yes, EFS is region-wide and automatically replicates across AZs.

> **Q: Is EFS cross-account mount supported?**  
> ❌ Not directly. Complex VPC peering + security rules would be needed, and it's not ideal.

---
