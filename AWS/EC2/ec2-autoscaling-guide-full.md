
# 🚀 EC2 Interview Preparation for DevOps Engineers

This guide covers EC2 concepts from basic to advanced with real-time troubleshooting and DevOps use cases. Also includes what actions require stopping the instance.

---

## 📘 SECTION 1: EC2 Basics

### 🔹 What is EC2?

Amazon EC2 (Elastic Compute Cloud) provides scalable virtual servers (instances) to run applications in the AWS cloud.

### 🔹 Types of EC2 Instances:

- General Purpose: T3, T4g, M6g
- Compute Optimized: C6g, C7g
- Memory Optimized: R6g, X2gd
- Storage Optimized: I3, D2
- Accelerated Computing: P4, Inf1

### 🔹 EC2 Purchasing Options:

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

## 🔧 SECTION 4: Real-Time Troubleshooting Scenarios (Root Cause + Fix)

### 1. SSH Not Working

**Possible Root Causes:**

- Invalid key
- Wrong user (`ec2-user` vs `ubuntu`)
- Port 22 blocked in SG/NACL

**Fix:**

- Check permissions on PEM: `chmod 400 key.pem`
- Ensure SG has port 22 open
- Use correct SSH command: `ssh -i key.pem ec2-user@<public-ip>`

### 2. No Internet Access

**Possible Root Causes:**

- No NAT Gateway for private subnet
- Route table misconfigured
- No public IP or IGW

**Fix:**

- Add NAT for private or IGW for public
- Verify `0.0.0.0/0` route
- Assign Elastic IP

### 3. EC2 Stuck in Stopping State

**Possible Root Causes:**

- Corrupted root volume
- Software hanging during shutdown

**Fix:**

```bash
aws ec2 stop-instances --instance-ids i-xxxx --force
```

Or detach volume, mount on helper instance, analyze `/var/log/`.

### 4. Disk Full

**Possible Root Causes:**

- Log files not rotated
- Docker consuming space

**Fix:**

```bash
df -h
sudo journalctl --vacuum-time=7d
```

Expand volume, then resize in OS.

### 5. User Data Script Not Running

**Possible Root Causes:**

- Missing shebang (`#!/bin/bash`)
- Syntax errors
- Already executed on first boot

**Fix:**

- Validate `/var/log/cloud-init-output.log`
- Use `cloud-init clean` to rerun

### 6. Lost SSH Key

**Possible Root Cause:**

- No backup of PEM key

**Fix:**

- Detach volume
- Mount on new EC2
- Add SSH key manually to `~/.ssh/authorized_keys`

### 7. Application Crashes After Reboot

**Possible Root Causes:**

- App not in startup script
- Env vars missing
- Volumes not mounted

**Fix:**

- Add app to systemd
- Use `/etc/environment` for env vars
- Ensure `/etc/fstab` has correct mounts

### 8. High CPU or Memory

**Possible Root Causes:**

- Application memory leaks
- Too small instance type

**Fix:**

- Analyze with `top`, `htop`
- Resize instance type

---

## 🔄 What Can Be Changed Without Stopping EC2?

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

## 📎 Sample User Data Script

```bash
#!/bin/bash
yum update -y
amazon-linux-extras install docker -y
systemctl start docker
systemctl enable docker
```

---

## ✅ DevOps Tools with EC2

| Tool       | Use Case                             |
| ---------- | ------------------------------------ |
| Terraform  | Automate EC2 + ASG provisioning      |
| Jenkins    | EC2 Jenkins agents (on-demand)       |
| Ansible    | Provision EC2 with dynamic inventory |
| Prometheus | Monitor EC2 with node exporter       |
| Docker     | Run apps inside EC2 containers       |

---

## 🔐 Security Tips

- Use SSM instead of SSH
- Rotate IAM keys and restrict SGs
- Use CloudTrail and VPC Flow Logs

---

## 🚦 SECTION 5: Auto Scaling Groups (ASG) + Load Balancers

### 🔹 What is an Auto Scaling Group?

An **Auto Scaling Group (ASG)** in AWS is like a manager that keeps your EC2 instances running smoothly. It automatically increases (scales out) or decreases (scales in) the number of EC2 instances based on the demand.

For example, if your website traffic increases, ASG will launch more EC2 instances. When traffic goes down, ASG can terminate extra instances to save costs.

---

### 🔹 Key Components of ASG (Explained Simply):

- **Launch Template**: A blueprint that tells AWS how to create an EC2 instance (AMI ID, instance type, key pair, etc.).
- **Minimum Size**: The least number of instances the group should run.
- **Maximum Size**: The maximum number of instances allowed.
- **Desired Capacity**: The target number of instances you want running normally.
- **Scaling Policies**:
  - **Target Tracking**: Keeps CPU/memory usage around a set target.
  - **Step Scaling**: Add/remove instances based on thresholds.
  - **Scheduled Scaling**: Scale at specific times (e.g., business hours).
- **Lifecycle Hooks**: Let you run custom actions (e.g., installing software) **before** the instance becomes active.

---

### 🔹 Load Balancers in ASG (Simple View)

A **Load Balancer** works like a traffic cop. It spreads incoming traffic evenly across multiple EC2 instances in your ASG.

You can use:

- **Application Load Balancer (ALB)**: Works at HTTP level. Best for web apps.
- **Network Load Balancer (NLB)**: Faster and works at the TCP level.

**Benefits:**

- Health checks: If one instance fails, traffic is automatically routed to healthy ones.
- Auto-registration: New EC2s added by ASG are automatically linked to the Load Balancer.

---

### 🔹 Real-Time Use Case (Step-by-Step):

Let’s say you’re deploying a high-traffic web app. Here’s how you’d use ASG and ALB:

1. **Create an AMI** with your app pre-installed.
2. **Set up a Launch Template** using that AMI.
3. **Create a Target Group** for the app (used by ALB).
4. **Set up an ALB** and attach the target group.
5. **Create an ASG**:
   - Use the Launch Template
   - Set min = 2, max = 5, desired = 2
   - Attach the target group
   - Add scaling policy: CPU > 70% = add 1 instance

---

### 🔹 Lifecycle Hook Example (Beginner Explanation)

A **lifecycle hook** is like a “pause” button during EC2 instance launch. It gives you time to run custom scripts **before** the instance starts receiving traffic.

> ⚠️ **Note**: Lifecycle hooks require manual intervention or automation using Lambda or Step Functions. From a DevOps best practices perspective, **manual steps should be avoided** in autoscaling flows because instances can launch or terminate anytime (e.g., due to failures or traffic spikes). If you're not available to respond, it can cause delays or service disruption.

✅ Instead, automate all provisioning inside **user data** or through **baked AMIs** created using **Packer**, so instances are ready-to-serve immediately when they launch.

Example lifecycle hook command (use only when automation is in place):

```bash
aws autoscaling put-lifecycle-hook   --lifecycle-hook-name InstallApp   --auto-scaling-group-name WebApp-ASG   --lifecycle-transition autoscaling:EC2_INSTANCE_LAUNCHING   --heartbeat-timeout 300
```

Use with care and automate wherever possible. Avoid manual lifecycle interventions in production workflows.

---

### 🔹 Health Checks in ASG

Health checks help ASG know when to replace a bad instance.

- **EC2 Health Check**: Checks if the instance is running at the OS level.
- **ELB Health Check**: Checks if the app/website is reachable. If the Load Balancer says an instance is unhealthy, ASG will terminate and replace it.

---

### 🔹 Best Practices for ASG (Simplified):

- ✅ Use **multiple Availability Zones** for high availability
- ✅ Use **Launch Templates**, not older Launch Configs
- ✅ Attach an **ALB** for traffic distribution
- ✅ Use **CloudWatch alarms** to auto-scale based on load
- ✅ Add **Lifecycle Hooks** for setup tasks
- ✅ Use **termination policies** to control which instance gets removed first

---
