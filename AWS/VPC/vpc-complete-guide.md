# 🧠 AWS VPC Complete Guide – DevOps & Interview Prep

---

## 📘 1. VPC Basics & Architecture

### ✅ What is a VPC?
Amazon Virtual Private Cloud (VPC) allows you to provision a logically isolated network in AWS, where you can launch resources like EC2, RDS, EKS, etc., and define your own IP address range, subnets, route tables, and gateways.

### 🧱 Core Components with Real-World Analogies

| Component            | Description                                 | Real-World Analogy                                  |
|---------------------|---------------------------------------------|-----------------------------------------------------|
| CIDR Block          | IP range for your VPC (e.g., `10.0.0.0/16`) | The land area of your office campus                 |
| Subnets             | Divisions within the VPC                    | Rooms or departments in a building                  |
| Route Tables        | Routing instructions for subnet traffic     | Road signs inside a campus                          |
| Internet Gateway    | Enables internet access                     | Main front gate to the internet                     |
| NAT Gateway         | Outbound internet from private subnet       | Backdoor with outbound-only access                  |
| Security Groups     | Instance-level firewalls (stateful)         | Security guards at each room                        |
| Network ACLs        | Subnet-level firewalls (stateless)          | Security guards at building entrances               |
| VPC Peering         | Connects two VPCs privately                 | A private bridge between office campuses            |
| Transit Gateway     | Centralized hub for many VPCs               | A central router connecting all buildings           |
| VPC Endpoints       | Private connection to AWS services          | Internal shortcuts to AWS service desks             |
| Flow Logs           | Monitors all traffic to/from network interfaces | CCTV cameras watching hallways                   |
| Bastion Host        | Securely access private EC2s via SSH        | Receptionist who lets you into internal rooms       |
| DHCP Option Set     | Controls DNS behavior in VPC                | Internal phone directory or name resolution         |

---

## 🌐 2. Subnets: Public vs Private

### 🟢 Public Subnet
- Associated with a Route Table that sends `0.0.0.0/0` traffic to **Internet Gateway**
- Commonly used for:
  - Load Balancers (ALB, NLB)
  - Bastion Hosts
  - NAT Gateways

### 🔒 Private Subnet
- No direct access to the internet
- Can access internet **only via NAT Gateway** (outbound-only)
- Commonly used for:
  - Application servers
  - Databases (RDS, DynamoDB)
  - Jenkins, SonarQube (in secure deployments)

---

## 📡 3. Route Tables, IGW, and NAT Gateway

### Route Table
- A set of rules (routes) that determines where network traffic from your subnet is directed.
- Each subnet must be associated with **one** route table.

### Internet Gateway (IGW)
- Provides access to the internet for public subnets
- Must be **attached to VPC** and **route added in subnet’s route table**

### NAT Gateway
- Allows **instances in private subnet** to access the internet (e.g., to pull packages, GitHub repos), **but prevents inbound access from the internet**

---

## 🔐 4. Security Groups vs NACLs

| Feature           | Security Group           | Network ACL                      |
|------------------|---------------------------|----------------------------------|
| Type             | Stateful                  | Stateless                        |
| Scope            | Attached to instances     | Attached to subnets              |
| Default Action   | Deny all inbound          | Allow all inbound/outbound       |
| Rules            | Only allow rules          | Allow and deny rules             |
| Use Cases        | EC2, RDS, ALB             | Subnet-level protection          |

---

## 🔗 5. VPC Endpoints (S3, SSM, etc.)

### Gateway Endpoint (S3/DynamoDB)
- Used for services like **S3** and **DynamoDB**
- Routes traffic **privately** without going through the internet or NAT Gateway

### Interface Endpoint
- Used for services like **SSM, EC2 API, Secrets Manager, KMS**
- Creates an **Elastic Network Interface (ENI)** inside your subnet
- Allows **private access** to AWS services

---

## 🚏 6. VPC Peering vs Transit Gateway

### VPC Peering
- Direct connection between two VPCs
- No **transitive routing** (VPC A ↔ VPC B ↔ VPC C is NOT possible)
- Must update route tables on both sides

### Transit Gateway
- Centralized router to connect **multiple VPCs and on-premises networks**
- Supports **transitive routing**
- Easier to scale and manage

---

## 🧪 7. Real-World Scenarios (In-Depth)

---

### 🔸 Scenario 1: Jenkins Pipeline Fails to Clone from GitHub

**Context:**
- Jenkins is deployed in a **private subnet**
- GitHub clone step in pipeline fails

**Root Cause:**
- No internet access from private subnet
- NAT Gateway is missing or misconfigured

**Fix:**
1. Create a NAT Gateway in a public subnet
2. Allocate and attach an Elastic IP
3. Update private subnet's route table:  
   `0.0.0.0/0 → NAT Gateway ID`

**DevOps Insight:**
Even though the Jenkins server is secure in a private subnet, it still requires outbound access to fetch code from public repositories.

---

### 🔸 Scenario 2: EC2 Instance in Public Subnet Not Accessible via SSH

**Context:**
- EC2 instance is launched in a **public subnet**
- Security Group allows port 22 from your IP

**Root Cause:**
- One or more of the following is missing:
  - No Internet Gateway attached to VPC
  - Route Table doesn't point to IGW
  - EC2 doesn’t have a public IP
  - NACL blocking inbound SSH

**Fix:**
- Attach IGW to VPC
- Add `0.0.0.0/0 → IGW` in route table
- Ensure EC2 has a public IP or Elastic IP
- Allow port 22 in SG and NACL

---

### 🔸 Scenario 3: High AWS Bill Due to Data Transfer

**Context:**
- Unexpectedly high AWS bill
- Suspect network data transfer costs

**Root Cause:**
- EC2 in private subnet is using NAT Gateway to access S3
- Heavy data transfer = High NAT charges

**Fix:**
- Add **Gateway VPC Endpoint for S3**
- Route all S3 traffic internally (free inside AWS network)

**DevOps Insight:**
VPC Endpoints save significant cost when dealing with large-scale transfers to S3 or DynamoDB.

---

### 🔸 Scenario 4: Can’t Communicate Between Two VPCs

**Context:**
- Two VPCs (App VPC ↔ Logging VPC) should be connected
- Peering is configured, but instances can't talk

**Root Cause:**
- Missing route entries
- Security Groups not allowing peered CIDR
- NACLs blocking access

**Fix:**
- Add correct routes in both VPC route tables
- Update SGs to allow traffic from peered CIDR blocks
- Check NACLs for port rules

---

### 🔸 Scenario 5: Partner Requires Private Access to Your API

**Context:**
- Third-party partner needs access to your internal API
- You don’t want to expose the service to the public internet

**Root Cause:**
- VPC Peering not viable (overlapping CIDRs)
- No direct connectivity

**Fix:**
- Use **AWS PrivateLink**
  - Create Endpoint Service in your VPC
  - Partner creates Interface Endpoint in their VPC
  - Provides secure, private access without internet

---

### 🔸 Scenario 6: Flow Logs Reveal Malware-Infected Instance

**Context:**
- Sudden outbound spike in network usage
- You enable VPC Flow Logs to investigate

**Findings:**
- EC2 instance sending traffic to multiple unknown IPs
- Traffic patterns indicate possible crypto mining malware

**Fix:**
- Terminate compromised instance
- Rebuild with hardened AMI
- Implement stricter SG, NACL, and IAM roles
- Use VPC Flow Logs for future proactive monitoring

---

## ❓ 8. Advanced VPC Interview Questions

---

### Q1. How does Transit Gateway scale better than VPC Peering?

**Answer:**
- TGW uses hub-and-spoke model
- Supports **transitive routing**
- Easy to manage 100s of VPCs
- Peering is point-to-point, complex to scale, no transitive routing

---

### Q2. How do VPC Flow Logs help with cost optimization or detecting malicious activity?

**Answer:**
- Reveal excessive traffic to/from public endpoints
- Show access patterns to reduce NAT Gateway usage
- Detect unauthorized IP scans or brute force attacks

---

### Q3. Can a subnet be both public and private?

**Answer:**
Technically, yes (based on route table), but **strongly discouraged**. Mixing routes to both IGW and NAT creates confusion and security risks.

---

### Q4. What happens if you associate two route tables with one subnet?

**Answer:**
You **can’t** — each subnet can only have **one** route table at a time. However, one route table can be shared across multiple subnets.

---

### Q5. How do you reduce NAT Gateway cost?

**Answer:**
- Use **Gateway Endpoints** for S3/DynamoDB
- Reduce internet-dependent traffic from private subnet
- Cache third-party packages internally (e.g., Nexus)

---

### Q6. Does VPC Peering allow overlapping CIDRs?

**Answer:**
No, VPC Peering **requires non-overlapping** CIDR blocks. For overlapping CIDRs, use **PrivateLink**.

---

### Q7. How does PrivateLink differ from VPC Peering?

**Answer:**

| Feature         | VPC Peering           | PrivateLink                         |
|----------------|------------------------|-------------------------------------|
| Connection Type| Full VPC-to-VPC        | Service-specific (one-way)          |
| CIDR Overlap   | ❌ Not allowed         | ✅ Allowed                          |
| Transitive     | ❌ Not supported       | ✅ Works for single service access  |

---

## 🧠 Final Summary

- Know the difference between public/private subnets, IGW, and NAT
- Practice troubleshooting connectivity, SSH, internet access issues
- Use VPC Endpoints to reduce cost and improve security
- Understand real-world patterns like Jenkins in private subnet, or multi-VPC EKS
- Be able to **diagram**, **explain**, and **justify trade-offs**

---
