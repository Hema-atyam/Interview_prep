
# 🚀 EC2 Interview Preparation for DevOps Engineers

This guide covers EC2 concepts from basic to advanced with real-time troubleshooting and DevOps use cases. It also includes what actions require stopping the instance, automation with CloudWatch, backup strategies, and Systems Manager integration.

---

## 📘 SECTION 1: EC2 Basics

### 🔹 What is EC2?
Amazon EC2 (Elastic Compute Cloud) provides scalable virtual servers (instances) to run applications in the AWS cloud.

### 🔹 Types of EC2 Instances
- General Purpose: T3, T4g, M6g
- Compute Optimized: C6g, C7g
- Memory Optimized: R6g, X2gd
- Storage Optimized: I3, D2
- Accelerated Computing: P4, Inf1

### 🔹 EC2 Purchasing Options

| Type      | Description             | Use Case                         |
| --------- | ----------------------- | -------------------------------- |
| On-Demand | Pay per hour or second  | Short-term workloads             |
| Reserved  | Commit for 1-3 years    | Predictable workloads            |
| Spot      | Use unused AWS capacity | Cost-saving, fault-tolerant apps |

---

## 📘 SECTION 2: EC2 Intermediate Topics

### 🔹 AMI (Amazon Machine Image)
Pre-configured template with OS, software, and settings used to launch instances.

### 🔹 EBS vs Instance Store

| Feature     | EBS (Elastic Block Store) | Instance Store  |
| ----------- | ------------------------- | --------------- |
| Persistence | Persistent                | Non-persistent  |
| Durability  | Across reboots            | Lost after stop |
| Use Case    | Boot & data volumes       | Temporary data  |

### 🔹 Security Groups vs NACLs
- **Security Groups** = Stateful, instance-level firewall.
- **NACLs** = Stateless, subnet-level rules (inbound & outbound must be defined).

---

## 📘 SECTION 3: Advanced EC2 Concepts

### 🔹 High Availability
- Launch in multiple AZs
- Auto Scaling Groups
- Elastic Load Balancer

### 🔹 Automation
- Use EC2 Launch Templates
- Use Terraform + Ansible for provisioning
- Bake custom AMIs with Packer

---

## 🔧 SECTION 4: Real-Time Troubleshooting Scenarios

(SSH issues, no internet access, stuck/stopping instance, disk full, lost SSH key, user data not working, etc.)

---

## 🔄 SECTION 5: What Can Be Changed Without Stopping EC2?

| Action                  | Requires Stop? |
| ----------------------- | -------------- |
| Attach new EBS volume   | ❌              |
| Add Elastic IP          | ❌              |
| Modify SG rules         | ❌              |
| Attach IAM role         | ❌              |
| Add/Remove tags         | ❌              |
| Change instance type    | ✅              |
| Change user-data        | ✅              |
| Modify root volume type | ✅              |

---

## ✅ SECTION 6: DevOps Tools with EC2

Terraform, Jenkins, Ansible, Prometheus, Docker — all with usage examples.

---

## 🔐 SECTION 7: Security Tips

Use SSM, rotate keys, restrict SGs, enable CloudTrail.

---

## 🚦 SECTION 8: Auto Scaling Groups (ASG) + Load Balancers

Launch template, min/max/desired, scaling policies, lifecycle hooks.

---

## 💰 SECTION 9: EC2 Spot Instances & Spot Fleet

Spot overview, pricing advantage, interruption handling, Spot + On-Demand in ASG.

---

## 📊 SECTION 10: CloudWatch Monitoring + EC2 Auto Recovery

Key EC2 metrics, alarm creation, auto-recovery steps, CloudWatch Agent setup, real-time alerting scenario.

---

## 📂 SECTION 11: EC2 Snapshots, AMI Backups & Lifecycle Policies

Snapshot and AMI creation, DLM automation, tagging strategy, restore and backup best practices.

---

## 🛠️ SECTION 12: AWS Systems Manager (SSM)

Session Manager, Run Command, Patch Manager, Inventory, Parameter Store, IAM roles, use cases.

---

## 🔁 SECTION 13: Best Practices Summary

Launch Templates, Auto-Recovery, SSM, Monitoring, Backup Strategy, Immutable AMIs with Packer.

---

Document prepared for DevOps engineers preparing for AWS EC2-focused interviews. Covers hands-on, architecture, automation, and troubleshooting areas.
