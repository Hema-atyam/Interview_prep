# 📊 AWS CloudWatch & Monitoring – DevOps & Interview Prep (Basic to Pro)

---

## 📘 1. What is Amazon CloudWatch?

Amazon CloudWatch is a **monitoring and observability service** used to collect metrics, logs, events, and alarms from AWS services, resources, and custom applications.

### Key Features:

- Metrics (default + custom)
- Logs & Log Groups
- Alarms
- Dashboards
- Events (EventBridge)
- CloudWatch Agent
- Insights (for querying logs)
- Contributor Insights (for top talkers)
- Anomaly Detection (ML-based metrics)

---

## 🧱 2. Core CloudWatch Components

| Component       | Purpose                                                   |
|-----------------|------------------------------------------------------------|
| **Metrics**     | Numeric data from AWS services (e.g., CPU, memory)         |
| **Logs**        | Raw logs sent from EC2, Lambda, custom apps                |
| **Alarms**      | Triggered based on thresholds (e.g., CPU > 80%)            |
| **Dashboards**  | Visualize metrics, logs, alarms                            |
| **Agent**       | Installed on EC2 to push OS-level metrics/logs             |
| **Events (EventBridge)** | Automates responses to changes or patterns       |
| **Insights**    | Powerful queries over logs (e.g., error rates, latency)    |

---

## 🔧 3. CloudWatch Metrics

### Types of Metrics:

- **Default Metrics** (automatically provided by AWS services)
- **Custom Metrics** (user-pushed metrics via SDK/CLI/agent)
- **High-Resolution Metrics** (granularity up to 1 second)

### Common EC2 Metrics:

- `CPUUtilization`
- `DiskReadOps`, `DiskWriteOps`
- `NetworkIn`, `NetworkOut`
- `StatusCheckFailed`

---

## 📄 4. CloudWatch Logs

- Logs from EC2 (via agent), Lambda, ECS, API Gateway, etc.
- Logs are stored in **Log Groups**
- Each group has multiple **Log Streams**
- You can set **retention periods**, **metric filters**, and **subscriptions** (to S3, Lambda, Kinesis)

---

## 🚨 5. CloudWatch Alarms

- Watch a metric and **trigger action** when threshold is crossed
- States: `OK`, `ALARM`, `INSUFFICIENT_DATA`
- Integrated with SNS for notifications

### Types of Alarms:

- **Static Threshold** (e.g., CPU > 80%)
- **Anomaly Detection** (predictive model based on past values)
- **Composite Alarms** (triggered when multiple alarms combine)

---

## 📊 6. Dashboards

- Custom, real-time monitoring views
- Combine metrics across services and regions
- Use for NOC screens, DevOps monitoring boards

---

## 🔁 7. EventBridge (CloudWatch Events)

- Triggered by AWS service actions (e.g., EC2 start, S3 upload)
- Can invoke:
  - Lambda
  - SSM Automation
  - SNS/SQS
  - EC2 actions

### Example Use Cases:

- Auto-recover EC2 on failure
- Trigger Lambda when RDS snapshot is created
- Schedule a backup every day at 2 AM

---

## 🧰 8. Real-World DevOps Scenarios

---

### ✅ Scenario 1: Alert when EC2 CPU exceeds 80% for 5 minutes

**Fix:**
- Create a **CloudWatch Alarm** on `CPUUtilization`
- Set threshold > 80% for 300 seconds
- Action: send SNS email or auto scale

---

### ✅ Scenario 2: Get notified if a log contains “ERROR” or “OutOfMemory”

**Fix:**
- Create a **Metric Filter** in CloudWatch Logs
- Filter pattern: `"ERROR"` or `"OutOfMemory"`
- Create alarm to notify via SNS when count > 0

---

### ✅ Scenario 3: Lambda errors spike unexpectedly

**Fix:**
- Use **CloudWatch Logs Insights** to query by `@loglevel` or `errorType`
- Create alarm based on `Errors` metric from Lambda
- Add dashboards to monitor concurrency and invocation count

---

### ✅ Scenario 4: EC2 becomes unreachable but still “running”

**Fix:**
- Monitor `StatusCheckFailed` metric
- Alarm triggers SSM automation or recovery

---

### ✅ Scenario 5: You need OS-level metrics (disk, memory) for EC2

**Fix:**
- Install **CloudWatch Agent** on EC2
- Configure to push memory, disk usage, etc.
- Create dashboards/alarms for these custom metrics

---

## 🧠 9. CloudWatch Best Practices

| Area | Best Practice |
|------|----------------|
| **Alarms** | Use static + anomaly detection for robust alerting |
| **Retention** | Set log retention to avoid unnecessary charges |
| **Agent** | Use unified CloudWatch agent for EC2 and hybrid systems |
| **Custom Metrics** | Use namespaces and dimensions logically |
| **Dashboards** | Use JSON templates to reuse across teams |
| **EventBridge** | Automate incident response and ops workflows |
| **Insights** | Use for root cause analysis of performance or error issues |

---

## ❗ 10. Common CloudWatch Issues + Fixes

| Issue | Root Cause | Fix |
|-------|------------|-----|
| No metrics in CloudWatch | Agent not installed or configured | Install + configure CloudWatch agent |
| Alarm not triggering | Threshold or duration misconfigured | Adjust evaluation period and threshold |
| Logs not visible | Wrong IAM role or log group name | Check policies, restart agent |
| High CloudWatch bill | Long retention or high frequency metrics | Set proper retention + use basic resolution |
| Delayed metrics | Using high-resolution metrics improperly | Set correct granularity and aggregation |

---

## 💬 11. CloudWatch Interview Questions – Advanced

---

### Q1: How do CloudWatch Alarms work?

- Alarms monitor metrics and change state based on thresholds. Actions can be triggered based on those states.

---

### Q2: What are common EC2 metrics?

- CPUUtilization, NetworkIn/Out, DiskRead/WriteOps, StatusCheckFailed.

---

### Q3: Difference between CloudWatch Logs and Logs Insights?

- Logs: Raw data from services and apps.
- Insights: Query language to filter, aggregate, and analyze logs.

---

### Q4: How would you detect anomalies in traffic patterns?

- Use **Anomaly Detection** in CloudWatch Alarms to create predictive thresholds based on past data.

---

### Q5: What are composite alarms?

- Alarms that combine multiple other alarms using AND/OR logic.
- Used to reduce noise or group related conditions.

---

### Q6: How do you monitor disk or memory usage in EC2?

- Install and configure **CloudWatch Agent** to collect OS-level metrics.
- Push to CloudWatch as custom metrics.

---

### Q7: How would you react to an S3 upload or EC2 state change?

- Use **EventBridge rules** to match the event and trigger a target (e.g., Lambda, SNS).

---

### Q8: How do you prevent high CloudWatch costs?

- Avoid high-resolution metrics unless needed.
- Set log retention and avoid "store forever".
- Use filters and dashboards wisely.

---

## 🧠 Final CloudWatch Takeaways

- Master metrics + logs + alarms = full observability
- Use **Logs Insights** for fast debugging
- Automate responses via **EventBridge**
- Tune retention and resolution to balance **cost vs insight**
- Combine with **SNS, Lambda, SSM** for full-stack automation

---
