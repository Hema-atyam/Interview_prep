# 🛠️ EC2 Patching in AWS – DevOps & Interview-Ready Guide

---

## 🔍 Why Patching Matters

- Fixes **security vulnerabilities**
- Updates **OS-level bugs**
- Ensures compliance (e.g., CIS, HIPAA, PCI-DSS)
- Helps avoid **zero-day exploits** on public-facing instances

---

## ✅ Ways to Patch EC2 Instances

| Method                      | Description |
|-----------------------------|-------------|
| **Manual SSH + Yum/Apt**    | Old-school, not scalable |
| **SSM Patch Manager**       | ✅ AWS-recommended, scalable, automation-ready |
| **Custom Ansible Scripts**  | When managing hybrid infra or specific patch needs |
| **AMIs with Pre-baked Patches** | Use golden AMIs for auto-patched infrastructure |

---

## 📦 Using AWS SSM Patch Manager (Preferred for Interviews & Prod)

### 🔐 Pre-requisites

- EC2 instances must:
  - Be **SSM managed** (SSM agent installed)
  - Have **IAM role** with `AmazonSSMManagedInstanceCore`
  - Be in a **supported OS** (Amazon Linux, Ubuntu, RHEL, Windows, etc.)

---

### ⚙️ Steps to Patch with SSM Patch Manager

1. **Create a Patch Baseline**
   - Define approved patches (auto-approve based on age/severity)
   - Choose OS & patch classifications (e.g., Security, Bugfix)

2. **Attach the Baseline to Patch Group**
   - Use `Patch Group` tag like: `Key=Patch Group, Value=Prod-Servers`

3. **Schedule Patching via Maintenance Window**
   - Create a Maintenance Window (e.g., every Sunday 2 AM)
   - Register target EC2s (based on tag)
   - Register the patch task

4. **Monitor & Report**
   - Use **SSM > Compliance** to view patch status
   - Setup CloudWatch alarms for failed patch jobs

---

## 💡 Real-World Scenario

### 🧪 Scenario: Patching Fails on Some EC2 Instances  
**Root Cause:**
- SSM agent not installed or stopped
- EC2 has no internet/NAT access
- IAM role missing required SSM policies

**Fix:**
- Reinstall/start `amazon-ssm-agent`
- Add instance to private subnet with NAT Gateway or VPC endpoint
- Ensure role has `AmazonSSMManagedInstanceCore` and `AmazonSSMPatchAssociation`

---

## 🔄 Alternatives

- 🔧 Use **Ansible playbooks** for on-prem + cloud environments
- 🚀 Automate patching in **AMI pipeline** for immutable infra (e.g., packer → bake → deploy)
- 🔁 Use **EC2 Image Builder** to patch and test AMIs periodically

---

## 🧠 Interview Questions

### Q1: How do you automate patching in EC2?  
**A:** Use AWS Systems Manager Patch Manager with a baseline + maintenance window.

---

### Q2: What happens if a patch breaks the system?  
**A:** Use rollback strategy:
- Test in lower environment
- Take snapshots before patching
- Roll back AMI or snapshot on failure

---

### Q3: How do you patch EC2 instances in a private subnet?  
**A:** Use either:
- NAT Gateway for outbound internet
- VPC endpoint for `ssm`, `ec2messages`, and `kms`

---

## ✅ Best Practices

- Use **tag-based targeting** for patch groups  
- Separate patch policies for **Prod**, **Dev**, and **Test**  
- Enable **patch compliance reporting**  
- Always **test patches** before production  
- Use **patch baselines** with auto-approval + delay (e.g., approve security patches after 3 days)

