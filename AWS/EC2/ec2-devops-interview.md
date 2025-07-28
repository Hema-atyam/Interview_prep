
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
| Type        | Description                              | Use Case                            |
|-------------|------------------------------------------|-------------------------------------|
| On-Demand   | Pay per hour or second                   | Short-term workloads                |
| Reserved    | Commit for 1-3 years                     | Predictable workloads               |
| Spot        | Use unused AWS capacity                  | Cost-saving, fault-tolerant apps    |

---

## 📘 SECTION 2: EC2 Intermediate Topics

### 🔹 AMI (Amazon Machine Image)
Pre-configured template with OS, software, and settings used to launch instances.

### 🔹 EBS vs Instance Store
| Feature        | EBS (Elastic Block Store) | Instance Store |
|----------------|---------------------------|----------------|
| Persistence    | Persistent                | Non-persistent |
| Durability     | Across reboots            | Lost after stop|
| Use Case       | Boot & data volumes       | Temporary data |

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

---

### 2. No Internet Access
**Possible Root Causes:**
- No NAT Gateway for private subnet
- Route table misconfigured
- No public IP or IGW

**Fix:**
- Add NAT for private or IGW for public
- Verify `0.0.0.0/0` route
- Assign Elastic IP

---

### 3. EC2 Stuck in Stopping State
**Possible Root Causes:**
- Corrupted root volume
- Software hanging during shutdown

**Fix:**
```bash
aws ec2 stop-instances --instance-ids i-xxxx --force
```
Or detach volume, mount on helper instance, analyze `/var/log/`.

---

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

---

### 5. User Data Script Not Running
**Possible Root Causes:**
- Missing shebang (`#!/bin/bash`)
- Syntax errors
- Already executed on first boot

**Fix:**
- Validate `/var/log/cloud-init-output.log`
- Use `cloud-init clean` to rerun

---

### 6. Lost SSH Key
**Possible Root Cause:**
- No backup of PEM key

**Fix:**
- Detach volume
- Mount on new EC2
- Add SSH key manually to `~/.ssh/authorized_keys`

---

### 7. Application Crashes After Reboot
**Possible Root Causes:**
- App not in startup script
- Env vars missing
- Volumes not mounted

**Fix:**
- Add app to systemd
- Use `/etc/environment` for env vars
- Ensure `/etc/fstab` has correct mounts

---

### 8. High CPU or Memory
**Possible Root Causes:**
- Application memory leaks
- Too small instance type

**Fix:**
- Analyze with `top`, `htop`
- Resize instance type

---

## 🔄 What Can Be Changed Without Stopping EC2?

| Action                                 | Requires Stop? |
|----------------------------------------|----------------|
| Attach new EBS volume                  | ❌              |
| Add Elastic IP                         | ❌              |
| Modify SG rules                        | ❌              |
| Attach IAM role                        | ❌              |
| Add/Remove tags                        | ❌              |
| Change instance type                   | ✅              |
| Change user-data                       | ✅              |
| Modify root volume type                | ✅              |

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

| Tool        | Use Case |
|-------------|----------|
| Terraform   | Automate EC2 + ASG provisioning |
| Jenkins     | EC2 Jenkins agents (on-demand) |
| Ansible     | Provision EC2 with dynamic inventory |
| Prometheus  | Monitor EC2 with node exporter |
| Docker      | Run apps inside EC2 containers |

---

## 🔐 Security Tips

- Use SSM instead of SSH
- Rotate IAM keys and restrict SGs
- Use CloudTrail and VPC Flow Logs
