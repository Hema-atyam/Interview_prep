# 🚀 AWS Auto Scaling Group (ASG) & Load Balancer (ELB) – DevOps & Interview Prep

---

## 📘 1. Auto Scaling Group (ASG) – Basics

### What is an ASG?

An **Auto Scaling Group (ASG)** automatically launches or terminates EC2 instances based on:

- Desired capacity
- Scaling policies
- Health checks

### Key ASG Concepts:

- **Launch Template/Configuration**: Defines AMI, instance type, security groups, etc.
- **Minimum, Maximum, Desired Capacity**: Controls how many EC2 instances are running.
- **Scaling Policy**: Rules for increasing/decreasing capacity.
- **Health Check**: Detects unhealthy instances (EC2 or ELB-based).
- **Lifecycle Hooks**: Lets you perform actions (e.g., bootstrap) before/after instance launch/termination.

---

## 📘 2. Elastic Load Balancer (ELB) – Basics

### Types of ELBs:

- **ALB (Application Load Balancer)**:
  - Layer 7 (HTTP/HTTPS)
  - Path/host-based routing
  - WebSocket support

- **NLB (Network Load Balancer)**:
  - Layer 4 (TCP/UDP)
  - High-performance, static IP support
  - Best for low-latency apps

- **CLB (Classic Load Balancer)**:
  - Legacy Layer 4/7
  - Avoid using for new architectures

---

## 🔀 3. ASG + ELB Integration

- ELB distributes incoming traffic to ASG-managed EC2 instances.
- ASG uses ELB health checks (in addition to EC2 health) for better resilience.
- When ASG terminates or launches instances, ELB target group is automatically updated.

---

## ⚙️ 4. Scaling Policies

### Common Scaling Strategies:

- **Target Tracking**: Maintain a metric value (e.g., CPU at 50%)
- **Step Scaling**: Scale based on specific metric thresholds
- **Scheduled Scaling**: Scale based on time (e.g., weekends or business hours)

### Metrics You Can Scale On:

- CPUUtilization
- ALBRequestCountPerTarget
- Custom CloudWatch metrics (e.g., queue length)

---

## 🔥 5. Real-World DevOps Scenarios

---

### ✅ Scenario 1: App traffic increases during office hours

**Problem:** App crashes due to sudden traffic spike at 10 AM.

**Fix:**
- Use **Target Tracking Scaling** based on `CPUUtilization` or `ALBRequestCountPerTarget`.
- Set desired threshold (e.g., maintain CPU around 60%).

---

### ✅ Scenario 2: Rolling deployment across ASG

**Problem:** Want to deploy app update to all ASG EC2s with zero downtime.

**Fix:**
- Use **Launch Template Versioning**.
- Create new version → update ASG → enable **Rolling Update** or **Blue/Green** with **lifecycle hooks**.

---

### ✅ Scenario 3: Health checks fail but EC2 is running

**Problem:** ALB shows instance as unhealthy even though EC2 is running.

**Fix:**
- Check health check **path/port** in ALB Target Group.
- Ensure your app responds to ALB health checks within the timeout period.
- Use **ELB health checks** in ASG instead of EC2 health check alone.

---

### ✅ Scenario 4: ASG doesn’t replace unhealthy instances

**Problem:** Instance is terminated, but not replaced.

**Fix:**
- Check ASG **Desired Capacity** — if it's already met, no replacement will happen.
- Ensure proper **Health Check Type** and **grace period** are configured.

---

### ✅ Scenario 5: Traffic not distributed evenly across AZs

**Problem:** Some EC2s are overloaded while others are idle.

**Fix:**
- Ensure **ELB is deployed in multiple AZs**.
- Enable **Cross-Zone Load Balancing** in ALB/NLB settings.

---

## 🛡️ 6. ASG & ELB Best Practices

### ASG Best Practices:

- Use **Launch Templates** over Launch Configs.
- Enable **termination protection** for critical instances during debugging.
- Use **lifecycle hooks** to run scripts before full instance registration.
- Always set **Min/Max/Desired** values carefully for cost vs. availability balance.
- Monitor scaling events via CloudWatch and SNS.

### ELB Best Practices:

- Use **ALB** for modern apps (microservices, HTTP/HTTPS).
- Use **NLB** for TCP-heavy workloads (e.g., game servers).
- Always configure **health checks** for your target group.
- Enable **access logs** for ALB for audit/troubleshooting.
- Use **listener rules** to route to multiple target groups.

---

## ❗ 7. Common Errors + Troubleshooting

| Problem | Root Cause | Fix |
|--------|-------------|-----|
| ASG not launching instances | Missing permissions or no launch template | Check IAM role and launch configuration |
| ELB shows instance as unhealthy | Wrong health check path or app not ready | Fix health check config or increase grace period |
| Traffic only going to one AZ | Cross-zone balancing disabled | Enable in ELB settings |
| Rolling update fails | Launch config/template not updated | Create new version and update ASG |
| Scaling up doesn’t happen | Scaling policy not triggered | Check CloudWatch alarms and metric threshold |

---

## 💬 8. Interview Questions – ASG & ELB

### Q1. What happens when an instance fails a health check in an ASG?

- ASG marks it as unhealthy and **replaces it automatically** (if Desired Capacity is not already met).

---

### Q2. How do you ensure zero-downtime deployment with ASG?

- Use **Launch Template Versioning**.
- Perform **Rolling Update** or **Blue/Green Deployment** using lifecycle hooks.

---

### Q3. Difference between ALB and NLB?

- ALB: Layer 7, HTTP-aware, path-based routing
- NLB: Layer 4, TCP/UDP, high throughput, static IP

---

### Q4. What is Cross-Zone Load Balancing?

- Allows ELB to distribute traffic **evenly across AZs**, regardless of number of instances per AZ.

---

### Q5. Can ASG work without a Load Balancer?

- Yes, but recommended to use an ELB for high availability and routing.

---

### Q6. How would you handle predictable traffic patterns?

- Use **Scheduled Scaling** to scale ahead of time during peak usage hours.

---

### Q7. How does ASG handle spot instance interruptions?

- Use **mixed instance policy** to replace interrupted spot instances with on-demand.
- Monitor spot interruption notices via CloudWatch.

---

## 🧠 Final Takeaways

- Always use **Launch Templates** (not legacy configs).
- Choose **ALB for HTTP/HTTPS**, NLB for TCP-heavy apps.
- Tune your **health checks** and **grace periods** for stability.
- Implement **scaling policies** (target, step, scheduled) based on metrics.
- Enable **monitoring and logging** using CloudWatch, access logs, and SNS.

---
