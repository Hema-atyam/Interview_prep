# ☸️ AWS EKS – Kubernetes on AWS (DevOps & Interview Prep)

---

## 📘 1. What is Amazon EKS?

Amazon EKS (Elastic Kubernetes Service) is a **fully managed Kubernetes service** that offloads the responsibility of running, maintaining, and scaling the control plane, while you manage worker nodes and application deployments.

---

## 🧱 2. EKS Core Components

- **Control Plane** – Managed by AWS (high availability built-in)
- **Node Groups** – Worker nodes (EC2 or Fargate) where workloads run
- **Fargate Profiles** – Serverless pods (no EC2 management)
- **CNI Plugin** – Assigns VPC IPs to pods via ENIs
- **IAM Roles for Service Accounts (IRSA)** – Assign AWS permissions to pods
- **Cluster Autoscaler** – Scales EC2 nodes based on pod scheduling demands

---

## ⚙️ 3. Node Types in EKS

- **Managed Node Groups** – Fully managed EC2 node groups
- **Self-managed Nodes** – You manage updates, scaling, lifecycle
- **Fargate** – AWS-managed serverless compute for pods

---

## 🔧 4. AWS Integrations in EKS

| AWS Service       | Role in EKS |
|-------------------|-------------|
| **IAM**           | RBAC integration, pod-level IAM via IRSA |
| **VPC**           | Networking, pod IPs via ENIs |
| **CloudWatch**    | Logging and metrics |
| **ALB/NLB**       | Expose services externally via Ingress |
| **EBS, EFS**      | Persistent storage for workloads |
| **SSM**           | Secure command access and automation |
| **EventBridge**   | Trigger alerts and automation on cluster events |

---

## 🚀 5. Real-World DevOps Scenarios (with Root Cause & Fix)

---

### ✅ Scenario 1: Deploy Jenkins on EKS with Persistent Storage

**Root Cause:**
- Jenkins requires persistent storage, and ephemeral storage is lost on pod rescheduling.
- Default PVCs may not bind correctly if storage class is missing or incompatible.

**Fix:**
- Use Helm chart with PersistentVolumeClaim using EBS CSI Driver.
- Ensure correct `storageClassName` (e.g., `gp2` or `gp3`) is specified.

---

### ✅ Scenario 2: Pods can’t access the internet

**Root Cause:**
- Private subnets lack NAT Gateway or route to 0.0.0.0/0
- CNI DaemonSet (`aws-node`) may not be functional

**Fix:**
- Verify NAT Gateway + route tables
- Check if ENIs are attached and CNI is healthy

---

### ✅ Scenario 3: Pods stuck in “Pending”

**Root Cause:**
- No nodes with enough CPU/memory
- NodeGroup doesn’t auto-scale
- Taints prevent scheduling

**Fix:**
- Enable Cluster Autoscaler
- Add tolerations and/or scale node groups

---

### ✅ Scenario 4: Pods require access to S3

**Root Cause:**
- Pods don’t have AWS credentials, and no IRSA setup
- Using default service account with no IAM policies

**Fix:**
- Create IAM role and Kubernetes service account
- Bind using IRSA with OIDC + trust policy

---

### ✅ Scenario 5: Ingress controller not routing traffic

**Root Cause:**
- ALB Ingress annotations missing or incorrect
- Ingress controller not running or not associated with right IngressClass
- Health check path is invalid

**Fix:**
- Check ALB/NLB annotations in Ingress YAML
- Validate controller logs
- Update health check path and security groups

---

### ✅ Scenario 6: Deploy app in private EKS cluster with no internet

**Root Cause:**
- No NAT Gateway, hence no outbound internet
- Missing VPC endpoints (e.g., S3/SSM) for AWS API access

**Fix:**
- Use NAT Gateway for egress or configure VPC endpoints
- Use IRSA for AWS SDK access inside pods

---

### ✅ Scenario 7: Cluster Autoscaler is not scaling

**Root Cause:**
- ASG not tagged properly
- IAM role lacks required autoscaling permissions
- Node group is not discoverable

**Fix:**
- Add `k8s.io/cluster-autoscaler/<cluster-name>` and `k8s.io/cluster-autoscaler/enabled` tags to ASG
- Attach IAM policy for autoscaling API access

---

### ✅ Scenario 8: Pods evicted due to ephemeral storage

**Root Cause:**
- Pod exceeds default container storage limits (e.g., `/tmp`)
- No storage limits defined → pod consumes node storage

**Fix:**
- Define `ephemeral-storage` requests/limits in pod spec
- Mount EBS if long-lived data is needed

---

### ✅ Scenario 9: Cross-account team needs access to EKS

**Root Cause:**
- IAM role not shared or not assumable by external account
- No `eks:DescribeCluster` or `eks:List*` permissions

**Fix:**
- Create IAM role with proper permissions
- Allow assume-role from other account
- Map user/role to `aws-auth` config map if needed

---

### ✅ Scenario 10: GitOps deployment via ArgoCD fails

**Root Cause:**
- ArgoCD uses default service account with no AWS permissions
- GitOps agent can't access secrets, S3, or ECR

**Fix:**
- Create a Kubernetes service account + IAM role
- Use IRSA to assign permissions to that service account

---

### ✅ Scenario 11: Logs not reaching CloudWatch

**Root Cause:**
- FluentBit/Fluentd misconfigured
- IAM policy doesn’t allow `logs:PutLogEvents`
- Wrong log group or region configured

**Fix:**
- Check and update FluentBit configMap
- Ensure IAM role has proper permissions
- Restart logging daemon if needed

---


## 🧠 6. EKS Best Practices

| Area | Practice |
|------|----------|
| IAM | Use IRSA for pod-level access, never hardcode AWS keys |
| Networking | Separate public/private subnets for control + app layers |
| Security | Use RBAC, OPA/Gatekeeper, and restrict `kube-system` access |
| Observability | Use Prometheus + Grafana + CloudWatch Logs |
| GitOps | Use ArgoCD or Flux for Git-based deployment |
| Upgrades | Regularly upgrade EKS version (supports N-2 versions only) |
| Storage | Use EBS for fast, reliable PVCs; EFS for shared storage |
| Cost | Mix On-Demand + Spot nodes in managed node groups |

---

## 💬 7. EKS Interview Questions – Advanced

---

### Q1: How does pod networking work in EKS?

- Each pod is assigned an ENI and private IP from the VPC via the **CNI plugin**, allowing native VPC networking.

---

### Q2: How do you give pod-specific AWS permissions?

- Use **IAM Roles for Service Accounts (IRSA)**, bind IAM role to service account using OIDC.

---

### Q3: How do you autoscale workloads?

- Use:
  - **HPA (Horizontal Pod Autoscaler)** for scaling pods
  - **VPA (Vertical Pod Autoscaler)** for adjusting resources
  - **Cluster Autoscaler** for adding/removing nodes

---

### Q4: What is the difference between Ingress and LoadBalancer service?

- `LoadBalancer`: Creates a dedicated NLB/ALB per service
- `Ingress`: Routes traffic to multiple services based on hostname/path

---

### Q5: What are common causes for pod scheduling failures?

- Insufficient resources (CPU, memory)
- Taints without matching tolerations
- Node affinity rules not met

---

### Q6: How do you handle log aggregation in EKS?

- Use **FluentBit/Fluentd** DaemonSet
- Send logs to CloudWatch Logs, Elasticsearch, or external log sinks

---

### Q7: What’s the lifecycle of a managed node group?

- AWS provisions, updates, scales, and maintains EC2 instances
- You control desired capacity and scaling policies

---

### Q8: How do you monitor and secure an EKS cluster?

- **Monitor**: Prometheus, Grafana, CloudWatch Container Insights
- **Secure**: Use IRSA, network policies (Calico), and audit logs

---

## ✅ Summary

- Amazon EKS provides scalable, secure, production-ready Kubernetes
- Deep integration with IAM, ALB, VPC, CloudWatch, and storage
- Master IRSA, networking, scaling, and Ingress for real-world readiness
- Be ready to troubleshoot: Pending pods, IAM access, ALB routing, logs
- EKS is now a **must-have** in modern DevOps interviews — you're ready!

---
