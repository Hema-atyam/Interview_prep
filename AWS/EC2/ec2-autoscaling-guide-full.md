
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

# 📘 SECTION 6: EC2 Spot Instances & Spot Fleet

## 🔹 What Are Spot Instances?

**Spot Instances** are spare EC2 capacity AWS offers at up to 90% discount compared to On-Demand pricing.

You can use them for:
- Batch jobs
- CI/CD builds
- Containers
- Fault-tolerant applications

> ⚠️ Spot instances **can be interrupted by AWS** with a 2-minute warning if AWS needs the capacity back.

---

## 🔹 How Spot Pricing Works

- Prices fluctuate based on supply/demand.
- You **set a maximum price** (optional).
- If your bid is higher than the market price → AWS allocates capacity.
- If market price rises above your bid → instance is terminated.

> ✅ As of now, AWS uses a simplified pricing model and no longer requires bidding — you only pay the **current Spot price**.

---

## 🔹 Use Cases for Spot Instances (DevOps)

| Use Case           | Reason for Spot Use                                 |
|--------------------|------------------------------------------------------|
| CI/CD Pipelines    | Cheap compute for short-lived build jobs            |
| Auto Scaling       | Add Spot to scale out cost-effectively              |
| Big Data           | Run Spark/Hadoop jobs affordably                    |
| Containers (ECS/EKS) | Easily recoverable workloads on Spot nodes         |

---

## 🔹 What is a Spot Fleet?

A **Spot Fleet** is a collection of Spot (and optionally On-Demand) instances managed as a group.

It lets you:
- Launch a fleet across multiple instance types and AZs
- Set a target capacity (in units or vCPU/RAM)
- Define allocation strategies

### 🔸 Allocation Strategies

| Strategy            | Description                                          |
|---------------------|------------------------------------------------------|
| **lowestPrice**     | Chooses instances with the lowest Spot price        |
| **diversified**     | Distributes across multiple instance pools          |
| **capacityOptimized** | Prefers pools with most available capacity         |
| **capacityOptimizedPrioritized** | Combines capacity & priority-based fallback |

> ✅ **Best for stability:** `capacityOptimized`  
> ✅ **Best for cost savings:** `lowestPrice`  
> ✅ **Best for mixed fleets:** `diversified`

---

## 🔧 Real-Time DevOps Scenario

### 📍 Use Case:
You run a Jenkins build farm with high CPU usage during working hours. You want to reduce cost using Spot.

### ✅ Solution:
- Create **Auto Scaling Group** with **mixed instance policy**
- Use **Spot for 80%** and On-Demand for 20%
- Use **capacityOptimized** allocation strategy
- Use **Launch Template** with Jenkins agent AMI

---

## ⚠️ Common Challenges & Fixes

### 1. **Spot Instance Terminated Unexpectedly**

**Root Cause:** AWS reclaimed the capacity.

**Fix:**
- Use **Spot interruption notices** to save state:
  ```
  curl http://169.254.169.254/latest/meta-data/spot/instance-action

- Gracefully shut down or move workload
- Use Auto Scaling to replace instances

### 2. **Instance Type Unavailable**
**Root Cause:** That type is temporarily out of capacity.

**Fix:**
- Use multiple instance types in your Spot Fleet
- Use diversified or capacityOptimized strategy
- Enable Capacity Rebalancing in ASG (automatically replaces at-risk instances)

### 3. Job Fails During Scale Down

**Root Cause:** Spot instance terminated mid-job.

**Fix:**
- Design jobs to be **stateless** and **checkpointed**
- Use **Amazon EventBridge** for interruption handling
- Consider **On-Demand fallback**

---

### ✅ Best Practices

- ⚙️ Use **Launch Templates**, not Launch Configs  
- ☁️ Use **ASG with Mixed Instances Policy**  
- 🔁 **Enable Capacity Rebalancing**  
- 🧠 Design workloads to handle interruptions  
- 📊 Monitor with **CloudWatch + Spot Instance Interruption metrics**

---
**📎 Sample Spot Fleet JSON (CLI)**
```
{
  "SpotFleetRequestConfig": {
    "TargetCapacity": 4,
    "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-role",
    "LaunchSpecifications": [
      {
        "InstanceType": "t3.large",
        "ImageId": "ami-0123456789abcdef0",
        "SubnetId": "subnet-0abc123456",
        "SpotPrice": "0.05"
      }
    ],
    "Type": "request",
    "AllocationStrategy": "capacityOptimized"
  }
}

```

### 🔐 Spot + ASG + Load Balancer (Best Architecture)

1. Launch Template with app AMI  
2. ASG with mixed instance policy:  
   - 80% Spot  
   - 20% On-Demand  
3. Attached to ALB  
4. Use CloudWatch for auto scale policy  
5. Capacity rebalancing enabled


# 📘 **CloudWatch Monitoring + EC2 Auto Recovery**

A critical topic for **DevOps engineers** managing EC2 health, performance, and self-healing!

---

## 🔹 What Is Amazon CloudWatch?

**Amazon CloudWatch** is AWS’s native monitoring and observability service.

It allows you to:

- Collect EC2 metrics (CPU, disk, memory, network, etc.)
- Set **alarms** on performance thresholds
- Visualize metrics on dashboards
- Trigger **automated actions** like:
  - Auto-recovery of EC2
  - Sending SNS/email alerts
  - Running Lambda functions

---

## 🔹 Default EC2 Metrics Provided by CloudWatch

| Metric                         | Description                          |
| ------------------------------ | ------------------------------------ |
| `CPUUtilization`               | % of CPU used by the instance        |
| `StatusCheckFailed`            | Combined instance + system health    |
| `StatusCheckFailed_Instance`   | Checks OS-level health               |
| `StatusCheckFailed_System`     | Checks AWS hardware/network          |
| `NetworkIn / NetworkOut`       | Bytes sent/received over the network |
| `DiskReadOps` / `DiskWriteOps` | Disk IO activity                     |

> ✅ These default metrics are collected every **5 minutes** (1 minute if detailed monitoring is enabled).

---

## 🔧 Use Case: EC2 App Is Unresponsive

**Problem:** An EC2 instance running a production app is stuck — CPU is fine, but users can’t connect.

**Root Cause:**

- EC2 system-level issue (e.g., hardware, network, hypervisor)
- App logs not accessible due to crash

**Fix:**

- Create a **CloudWatch Alarm** on `StatusCheckFailed_System`
- Set **auto-recovery** as the alarm action

---

## 🔹 Enabling EC2 Auto-Recovery

You can automatically **recover** (not replace!) the EC2 instance **without changing its IP or data**.

### ✅ Requirements:

- Instance must be **EBS-backed**
- Instance type must support recovery (e.g., `t3`, `m5`, etc.)

### 📌 Steps to Configure (Console):

1. Go to **CloudWatch > Alarms > Create Alarm**
2. Choose metric: `StatusCheckFailed_System`
3. Set condition: `>= 1 for 1 consecutive period`
4. Action: Choose EC2 Recovery

> ⚙️ This reboots EC2 on another healthy AWS host — **data, instance ID, and IP remain unchanged.**

---

## 📎 CLI Example

```bash
aws cloudwatch put-metric-alarm \
  --alarm-name "Recover-EC2-Prod" \
  --metric-name StatusCheckFailed_System \
  --namespace AWS/EC2 \
  --statistic Maximum \
  --period 60 \
  --threshold 1 \
  --comparison-operator GreaterThanOrEqualToThreshold \
  --evaluation-periods 2 \
  --alarm-actions arn:aws:automate:us-east-1:ec2:recover \
  --dimensions Name=InstanceId,Value=i-0123456789abcdef0
```
### 🔹 Monitoring EC2 Memory & Disk

> ❌ By default, CloudWatch does **NOT** collect memory or disk usage.

---

### ✅ To enable it:
Install the **CloudWatch Agent**.

#### 📌 Steps:

```bash
sudo yum install amazon-cloudwatch-agent -y

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```
This configures collection of:

- **Memory usage** (% used, free, cached)
- **Disk usage** (`/`, `/var`, etc.)
- **Custom application logs**

---

## 📂 Log Monitoring with CloudWatch Logs

CloudWatch Agent can also stream logs such as:

- `/var/log/messages`
- `/var/log/secure`
- `/var/log/nginx/access.log`
- Custom application logs

---

## 🔸 Sample Log Agent Config (`amazon-cloudwatch-agent.json`)

```
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/messages",
            "log_group_name": "ec2/messages",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}

```

---

## 🛠 Real-Time DevOps Scenario

### 📍 Scenario:
Your EC2-based **Node.js app crashed** in production. Users complained — but your team didn’t know until they logged in.

### ✅ Solution:

- Enable **CloudWatch Agent** for logs and memory monitoring
- Create alarms on:
  - `StatusCheckFailed`
  - **High Memory Usage** (> 90%)
- Trigger actions:
  - **SNS Email Notification**
  - **EC2 Auto-Recovery** (for system-level check failures)

---

## 🧠 Best Practices

| ✅ Practice                 | Description                                                   |
|----------------------------|---------------------------------------------------------------|
| Auto-Recovery Alarms       | For `StatusCheckFailed_System`                                |
| Enable CloudWatch Agent    | For memory, disk, and custom logs                             |
| Store logs in CW Logs      | Centralized log collection from all EC2 instances             |
| Use Dashboards             | Visualize key metrics easily                                  |
| Set Notification Channels  | Use SNS to alert team via email/SMS                           |
| Test Alarms Regularly      | Ensure recovery or alerts are functioning as expected         |
| Use CW Logs Insights       | Query logs using SQL-like expressions                         |

## 🔍 Additional Interview Questions & Scenarios

---

### 1. ❓How do you monitor an EC2 instance’s memory usage in CloudWatch?

✅ **Answer**:
- CloudWatch by default **does not collect memory or disk metrics**.
- You need to **install and configure the CloudWatch Agent**.
- Use `amazon-cloudwatch-agent-config-wizard` or a config file to collect memory, disk, and custom metrics.

---

### 2. ❓Can you trigger auto-recovery based on memory or disk usage?

❌ **Answer**: No.

- Auto-recovery only works with `StatusCheckFailed_System`.
- It **cannot be triggered by memory or disk usage**.
- For high memory/disk issues:
  - Send **SNS notifications**
  - Trigger a **Lambda function**
  - Initiate **automation** (e.g., scale out, clean-up)

---

### 3. ❓How would you set up EC2 Auto Recovery for all production instances automatically?

✅ **Answer**:
- Tag instances with: `Environment=Prod`
- Use a script or tool like **Terraform**, **Ansible**, or **AWS CLI** to attach alarms to all instances with that tag.
- Example: Use `describe-instances` → loop over results → run `put-metric-alarm` for each instance.

---

### 4. ❓What happens during EC2 auto-recovery? Will the IP or volume change?

✅ **Answer**:
- **Public IP and private IP remain unchanged**
- **Instance ID remains the same**
- **EBS volumes are preserved**
- EC2 is **moved to healthy hardware**

> 🔄 This is **NOT a replacement**, it is a **reboot on different AWS hardware**.

---

### 5. ❓How would you confirm if a user-data script failed using logs?

✅ **Answer**:
Check the following log files on the EC2 instance:
- `/var/log/cloud-init-output.log`
- `/var/log/cloud-init.log`
- `/var/log/user-data.log` *(if enabled)*

---

### 6. ❓How would you monitor a critical custom app log like `/var/log/myapp/error.log` across multiple EC2s?

✅ **Answer**:
- Install **CloudWatch Agent**
- Configure `logs_collected` section to include that log file
- Use a **log group** per app or per environment
- Set up **metric filters** or use **CloudWatch Logs Insights** to monitor errors
- Create **alarms** or **dashboards** for visibility

---

### 7. ❓How to reduce noise in CloudWatch alerts for high CPU usage?

✅ **Answer**:
- Use **evaluation periods** (e.g., CPU > 80% for **5 minutes**, not instant spike)
- Avoid alerting on short-lived temporary spikes
- Use **Anomaly Detection** for variable workloads
- Combine multiple alerts using **Composite Alarms**

---

### 8. ❓Can you trigger Lambda if a CloudWatch alarm is breached?

✅ **Answer**:
Yes.

CloudWatch alarms can trigger:
- **SNS Topic**
- **Auto Scaling Action**
- **Lambda Function**

📌 **Use Case**: Auto-fix low disk space by triggering a Lambda that cleans up logs.

---

### 9. ❓What’s the difference between `StatusCheckFailed`, `StatusCheckFailed_Instance`, and `StatusCheckFailed_System`?

| Metric Name                 | What it checks                        | Auto-Recovery Supported |
|----------------------------|----------------------------------------|--------------------------|
| `StatusCheckFailed`        | Combined system + instance checks     | ❌ No                    |
| `StatusCheckFailed_Instance` | OS-level issues (e.g., sshd crash)    | ❌ No                    |
| `StatusCheckFailed_System` | AWS infra/network/hypervisor issues   | ✅ Yes                   |

---

### 10. ❓Can CloudWatch Dashboard show memory or custom app metrics?

✅ **Answer**:
Yes, **if**:

- **CloudWatch Agent** is installed
- You're sending **custom metrics** (via agent or `put-metric-data`)
- You select the correct namespace (e.g., `CWAgent`, `Custom`, etc.)

# 📘 EC2 Snapshots, AMI Backups & Lifecycle Policies

A must-know topic for **DevOps engineers** handling infrastructure **backup, restore, and disaster recovery** on EC2.

---

## 🔹 What Is an EBS Snapshot?

An **EBS Snapshot** is a **point-in-time backup** of an EBS volume. It is:

- **Stored in S3** (but not directly visible there)
- **Incremental**: only changed blocks are saved after the first snapshot
- Used to:
  - Restore volumes
  - Create new volumes
  - Build **AMIs** (Amazon Machine Images)

---

## 🔹 Creating Snapshots (Manual)

### 🧪 CLI:

```bash
aws ec2 create-snapshot \
  --volume-id vol-0123456789abcdef0 \
  --description "Daily backup on Oct-5"
```
## ✅ Best Practice

**Tag snapshots for identification**
```
 --tag-specifications 'ResourceType=snapshot,Tags=[{Key=Env,Value=Prod}]'
```
---

## 🔹 Real-Time Use Case

**Scenario:**  
Your nightly Jenkins job is running backups of application data volumes.

### ✅ Solution:
- Tag all critical volumes as: `Backup=true`
- Use automation (AWS CLI, Lambda, or DLM) to snapshot all volumes with this tag

---

## 🔹 Creating AMIs (Amazon Machine Images)

**AMIs** are full system images (OS + root volume + metadata).  
Used to launch pre-baked EC2s.

### 🧪 CLI:

```bash
aws ec2 create-image \
  --instance-id i-0abc123456 \
  --name "webapp-prod-ami-2024-10-05" \
  --no-reboot
```
✅ **Use `--no-reboot` to avoid instance downtime.**

---

## 📦 AMIs are used for:

- Auto Scaling Groups  
- Blue/Green deployments  
- Quick disaster recovery

---

## 🔹 Automating Backups with DLM (Data Lifecycle Manager)

**Data Lifecycle Manager (DLM)** helps schedule automatic backups of EBS volumes and cleanup old ones.

### 🧭 Steps:

1. Go to **EC2 Console → Lifecycle Manager**
2. Create a **Snapshot Policy**
3. Select volumes by tag (e.g., `Backup=true`)
4. Set:
   - **Schedule** (daily, weekly)
   - **Retention** (e.g., last 7 snapshots)
   - **IAM Role** (e.g., `AWSDataLifecycleManagerDefaultRole`)

✅ **DLM handles:**

- Snapshot creation  
- Expiration (auto-delete old ones)  
- No need for custom scripts or cronjobs

---

## 🔄 Real-Time DevOps Scenario

### 📍 Scenario:
A volume in production was deleted by accident. You need to restore it quickly.

### ✅ Fix:

1. Identify the latest snapshot of that volume
2. Create a new EBS volume from the snapshot:
```bash
aws ec2 create-volume \
  --availability-zone us-east-1a \
  --snapshot-id snap-0123456789abcdef0
```
3. Attach the volume to EC2:
```
aws ec2 attach-volume \
  --instance-id i-0abc123456 \
  --volume-id vol-0987654321abcdef0 \
  --device /dev/xvdf
```

## 🔐 Backup Strategy – Best Practices

| ✅ Practice                     | 💡 Reason                                           |
| ----------------------------- | -------------------------------------------------- |
| Tag critical volumes           | Enables backup automation via DLM or Lambda        |
| Use DLM for all environments   | Avoid manual effort & mistakes                     |
| Store AMI per release          | Ensure rollback is possible                        |
| Monitor snapshot creation events | Alert if any snapshot fails                      |
| Use version naming             | e.g., `webapp-prod-ami-v1.2.3`                     |
| Test restore processes         | Practice restoring from snapshot/AMI in non-prod   |
| Enforce least access to snapshots | Use IAM policies to protect backup data         |

---

## ❌ Common Pitfalls & Troubleshooting

### ❗ Snapshots Not Getting Created

**Root Cause:**

- Incorrect tag filtering in DLM  
- IAM role missing permissions  

**Fix:**

- Ensure volumes are tagged correctly  
- Attach `AmazonDLMFullAccess` or use default DLM role  

---

### ❗ Snapshots Taking Too Long

**Root Cause:**

- Large volumes or high I/O during snapshot  
- Taking snapshot of live production database  

**Fix:**

- Snapshot during low-traffic windows  
- Use `--no-reboot` AMI only when safe  
- Consider application-level quiescing  

---

### ❗ Too Many Snapshots = High Cost

**Root Cause:**

- Manual snapshots not cleaned up  
- No retention policy in place  

**Fix:**

- Use DLM or Lambda to delete old snapshots  
- Monitor with **AWS Cost Explorer**  

## ❌ Common Pitfalls & Troubleshooting

### ❗ Snapshots Not Getting Created

**Root Cause:**

- Incorrect tag filtering in DLM  
- IAM role missing permissions  

**Fix:**

- Ensure volumes are tagged correctly  
- Attach `AmazonDLMFullAccess` or use the default DLM role  

---

### ❗ Snapshots Taking Too Long

**Root Cause:**

- Large volumes or high IO during snapshot  
- Taking snapshot of live production database  

**Fix:**

- Snapshot during low-traffic windows  
- Use `--no-reboot` AMI only when safe  
- Consider application-level quiescing  

---

### ❗ Too Many Snapshots = High Cost

**Root Cause:**

- Manual snapshots not cleaned up  
- No retention policy in place  

**Fix:**

- Use DLM or Lambda to delete old snapshots  
- Monitor with **AWS Cost Explorer**  

---

## 🧪 Interview Questions & Scenarios

### 1. ❓ How do EBS Snapshots work?

- Point-in-time backups stored in S3  
- **Incremental**: Only changed blocks after the first full snapshot  

---

### 2. ❓ What is the difference between Snapshot and AMI?

| Feature | Snapshot | AMI |
|--------|----------|-----|
| Scope | Volume-level backup | Full instance image (OS, settings, data) |
| Used for | Restore volumes | Launch new EC2 |
| Type | Incremental | Complete image |

---

### 3. ❓ How would you automate daily snapshots?

- Tag volumes with `Backup=true`  
- Use **DLM** (Data Lifecycle Manager)  
- Or automate with **Lambda + CloudWatch Events**  

---

### 4. ❓ How would you rollback EC2 to a previous working version?

- Use the **AMI** from the earlier deployment  
- Replace instance (or ASG) with that AMI  
- Ensure application config is **decoupled** from EC2 image

# 📘 AWS Systems Manager (SSM) with EC2

A powerful tool that lets you **manage EC2 instances without SSH** or bastion hosts — ideal for secure, scalable, and automated environments.

---

## 🔹 What Is AWS Systems Manager (SSM)?

| Feature           | Description                                                                  |
|------------------|------------------------------------------------------------------------------|
| Session Manager  | Connect to EC2 **without SSH**, key pair, or port 22                         |
| Run Command      | Execute shell commands/scripts on EC2 from console/CLI                       |
| Patch Manager    | Automate patching of OS packages                                             |
| Inventory        | Gather software/hardware info from EC2                                       |
| Parameter Store  | Store config values & secrets securely                                       |
| Automation       | Run predefined workflows (e.g., AMI creation, restart services, backups)     |

---

## 🔹 Why Use SSM in DevOps?

- ✅ Replace SSH key login with **IAM-based access**
- ✅ Centralized management of fleet-wide commands
- ✅ Full audit logs of every session/command
- ✅ Works in **private subnet** too (via VPC endpoints)
- ✅ No need for bastion hosts or port 22 open
- ✅ Integrates with CloudWatch, IAM, and CloudTrail

---

## 🔹 Enabling SSM on EC2

### ✅ Steps to Enable:

1. **IAM Role**  
   Attach to EC2 instance:  
   `AmazonSSMManagedInstanceCore` policy

2. **SSM Agent**  
   - Amazon Linux 2 & 2023 → Preinstalled ✅  
   - Ubuntu/Debian → Install manually if not present

3. **Networking**  
   Instance must have:
   - Internet access via NAT Gateway  
   - Or VPC Interface Endpoints:
     - `com.amazonaws.region.ssm`
     - `ssmmessages`
     - `ec2messages`

---

## 🔧 Real-World Use Cases (DevOps)

### 🔹 1. Connect to EC2 Without SSH
``` aws ssm start-session --target i-xxxxxxxxxxxxxx ```

- ✅ No PEM key
- ✅ IAM-based access
- ✅ Works on private EC2 instances too!

---

### 🔹 2. Run Commands Across Fleet
```
aws ssm send-command \
  --document-name "AWS-RunShellScript" \
  --targets '[{"Key":"tag:Environment","Values":["Staging"]}]' \
  --parameters '{"commands":["yum update -y"]}' \
  --comment "Patch staging EC2s"

```
- ✅ Use tags to target multiple EC2s
- ✅ No login needed

---

### 🔹 3. Store Secrets in Parameter Store
```
aws ssm put-parameter \
  --name "/dev/db/password" \
  --value "SuperSecret123" \
  --type "SecureString"

```
- ✅ Use KMS to encrypt secrets
- ✅ Version-controlled

---

### 🔹 4. Pull Secrets in EC2 via User-Data
```
#!/bin/bash
DB_PASS=$(aws ssm get-parameter --name "/dev/db/password" --with-decryption --query "Parameter.Value" --output text)
```
- ✅ Avoid hardcoding passwords
---

## 🔎 Real-Time Scenarios + Fixes

### 📍 Scenario 1: Cannot connect to EC2 via SSM

**Root Causes:**
- IAM role not attached or missing permissions
- No internet/VPC endpoint access
- SSM agent not running

**Fix:**
- Attach `AmazonSSMManagedInstanceCore` policy
- Ensure instance can reach SSM endpoints
- Restart SSM agent
`sudo systemctl restart amazon-ssm-agent`

---

### 📍 Scenario 2: Command failed across EC2 fleet

**Root Causes:**
- One instance not managed/online
- Wrong tag in `--targets`
- Misconfigured shell command

**Fix:**
- Check instance info with `describe-instance-information`
- Use `list-command-invocations` for debug output

---

## 🧠 Best Practices

| Practice                            | Reason                                               |
|------------------------------------|------------------------------------------------------|
| ✅ Use IAM roles                    | More secure than PEM keys                            |
| ✅ Enable CloudWatch logging        | For auditing all sessions and commands               |
| ✅ Use Parameter Store              | Store configs/secrets securely                       |
| ✅ Target EC2 by tags               | Easier fleet-wide operations                         |
| ✅ Close SSH port 22                | Replace with Session Manager                         |
| ✅ Use SSM documents for automation | For repeatable, no-login workflows                   |

---

## 🧪 Interview Questions & Scenarios

### ❓ Q1: What does Session Manager do?
> Allows secure EC2 access without SSH, PEM key, or open ports using IAM-based auth.

---

### ❓ Q2: Benefits of Run Command?
> Centralized command execution across EC2s with tagging and audit trail.

---

### ❓ Q3: How to rotate DB passwords with SSM?
> Use Parameter Store + automation (e.g., Lambda/CI pipeline) to update & distribute secrets.

---

### ❓ Q4: Why is Parameter Store better than env vars?
> Version-controlled, encrypted, and centralized config management.

---

### ❓ Q5: Can SSM work in private subnet?
> ✅ Yes, using VPC interface endpoints (`ssm`, `ssmmessages`, `ec2messages`).

---


