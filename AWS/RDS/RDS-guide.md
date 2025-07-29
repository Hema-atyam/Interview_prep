# 🆚 AWS SQL vs NoSQL Databases – DevOps & Interview Prep

---

## 1. Overview: SQL (Relational) vs NoSQL

| Type         | AWS Service         | Use Case                          | Strengths                          | Limitations                        |
|--------------|---------------------|-----------------------------------|------------------------------------|------------------------------------|
| SQL          | RDS / Aurora        | Transactions, relational data     | Joins, complex queries, strong ACID | Scaling vertically, AZ-bound       |
| NoSQL        | DynamoDB            | Key–value, JSON, wide‑column      | Massive scale, low latency, serverless | Requires access‑pattern design; limited querying |

---

## 2. DynamoDB – When to Use & When to Avoid

### ✅ Best Fit:
- Millisecond latency at scale  
- Serverless, fully managed workloads  
- Predictable access patterns (e.g. IoT, gaming, user profiles)  
- High write throughput  

### ❌ Not Ideal When:
- Complex ad-hoc querying or analytics is needed  
- Access patterns are unknown or evolving  
- Applications depend on SQL joins, aggregations, or schema migrations  

> ❗ As one engineer shared:  
> _“Relational data is extremely hard to manage in DynamoDB… cost savings nullified by extra dev hours.”_ :contentReference[oaicite:5]{index=5}

---

## 3. Real-World Design & Challenges in DynamoDB

---

### 🔥 Hot Partition / Write Bottleneck
**Issue:** All writes go to same partition key → throttling  
**Fix:**
- Use randomized suffix/prefix to spread writes  
- Switch to **on-demand capacity** for unpredictable spikes :contentReference[oaicite:6]{index=6}

---

### 📊 Query Limitations
**Issue:** No SQL-like joins, filtering, or sorting beyond sort key  
**Fix:**
- Precompute and denormalize data  
- Use **GSIs** or consider integrating search with OpenSearch :contentReference[oaicite:7]{index=7}

---

### 💸 Unpredictable Cost
**Issue:** Provisioned throughput over/under provisioning hurts cost  
**Fix:**
- Use on-demand mode  
- Monitor using auto scaling or steady-state reservations :contentReference[oaicite:8]{index=8}

---

### 📦 Item Size & Storage Constraints
- Max item size is **400 KB**  
- Large objects should be stored in **S3** and referenced in DynamoDB :contentReference[oaicite:9]{index=9}

---

### 🧠 Schema Evolution
**Issue:** No enforced schema → hard to maintain consistency  
**Fix:**
- Version attribution in items; migration using scripts  
- Avoid overloading single-table design if schema will evolve :contentReference[oaicite:10]{index=10}

---

## 4. SQL Use Cases: Aurora & RDS Scenarios

---

### ✅ Use Case: Multi‑step transactions or ACID needs
- Choose **Aurora** or **RDS** for strong consistency and atomicity

### ✅ Use Case: Analytics or business reporting
- Use SQL with joins, grouping, ad-hoc queries easily

### ✅ Use Case: Schema flexibility over time?
- RDS supports JSON columns for semi-structured data without complexity :contentReference[oaicite:11]{index=11}

---

## 5. Architecture Scenarios & Troubleshooting

---

### Scenario: Gaming leaderboard with millions of concurrent players  
**Solution:**  
- Use DynamoDB global tables  
- Autoscale throughput & use DAX caching for microsecond reads :contentReference[oaicite:12]{index=12}

---

### Scenario: IoT telemetry ingestion at scale  
**Solution:**  
- Use DynamoDB with on-demand scaling, TTL for old entries, stream to Lambda for analytics  
- Apply denormalized single-table design for high write volume :contentReference[oaicite:13]{index=13}

---

### Scenario: Unexpected query requirements in prod  
**Problem:** SQL queries suddenly required on Dynamo with undefined patterns  
**Fix:**  
- Move analytical workloads to Redshift / Athena  
- Maintain DynamoDB for hot path, ETL historical data externally :contentReference[oaicite:14]{index=14}

---

### Scenario: Developer wants to fetch inactive user accounts or data filtering by non-key columns  
**Problem:** DynamoDB does not support such filters efficiently  
**Fix:** Add additional GSIs or maintain data separately in RDS or analytics store :contentReference[oaicite:15]{index=15}

---

## 6. Interview-Style Questions & Answers

### Q1: Why choose SQL over DynamoDB?
- Need flexibility in queries, transactional integrity, ease of schema evolution

### Q2: DynamoDB throughput throttled—how do you fix it?
- Add suffix/prefix to partition key, spread write across partitions, use on-demand or sharding :contentReference[oaicite:16]{index=16}

### Q3: How do you enable strongly consistent reads?
- Set `ConsistentRead=True` or use synchronous replicas

### Q4: DynamoDB table costs spiking—what are options?
- Move to on-demand, implement autoscaling or Reserved capacity based on load :contentReference[oaicite:17]{index=17}

### Q5: Favorite pattern for multi-table relationships?
- Denormalize or use application-level joins; use external tools (Athena, EMR) for analytics

---

## 7. Best Practices & Design Considerations

- 💡 Know access patterns upfront  
- 🧪 Design single‑table schema if using DynamoDB, with GSIs as needed  
- 📦 Backup using PITR and export to S3 for archival  
- 🔐 Use IAM, VPC endpoints, TLS for secure access  
- 🧠 Monitor DynamoDB using CloudWatch, use metrics like ThrottledRequests, ConsumedCapacity  
- ❗ Test and validate, avoid schema-design changes in-production  

---

## ✅ Final Takeaway

- DynamoDB = **fast, scalable NoSQL**, but demands upfront design  
- SQL (Aurora, RDS) = **flexible, transactional, easy analytics**  
- Choose based on use case, data patterns, and team capability  
- You’re now equipped to discuss modeling, migrations, performance tuning, cost control, and real-world trade-offs confidently  

---

