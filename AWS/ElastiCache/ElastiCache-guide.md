# ⚡️ AWS ElastiCache – DevOps, Design & Interview Prep

---

## 🚀 What is Amazon ElastiCache?

Amazon ElastiCache is a **fully managed, in-memory caching service** supporting:
- **Redis** (more features, persistence, clustering)
- **Memcached** (simple, multi-threaded cache)

It helps reduce load on databases by caching **frequently accessed data**, improving performance and scalability.

---

## 🧱 ElastiCache Core Components

| Component             | Description |
|----------------------|-------------|
| **Node**             | A single cache instance |
| **Cluster**          | One or more nodes in a group (used in Redis with clustering) |
| **Replication Group**| Primary + replicas (Redis only) |
| **Subnet Group**     | Group of subnets for ElastiCache deployment inside a VPC |
| **Parameter Group**  | Runtime config for Redis/Memcached |
| **Security Groups**  | Control access via VPC |

---

## 🔍 Redis vs Memcached – When to Use

| Feature                | Redis                            | Memcached                |
|------------------------|----------------------------------|--------------------------|
| **Persistence**        | ✅ Yes (Snapshots / AOF)         | ❌ No persistence        |
| **Replication**        | ✅ Supported                     | ❌ Not supported         |
| **Clustering**         | ✅ Native sharding               | ✅ Manual sharding only  |
| **Data Types**         | Strings, Lists, Sets, Hashes... | Strings only             |
| **Pub/Sub**            | ✅ Yes                           | ❌ No                    |
| **Multithreaded**      | ❌ Single-threaded               | ✅ Yes                   |
| **Use Case**           | Session mgmt, counters, TTL, pub/sub | Simple caching        |

---

## 🌍 Real-World Use Cases

- **Web Session Management** – fast user session storage
- **Database Query Caching** – reduce load on RDS or DynamoDB
- **Leaderboard/Ranking System** – Redis sorted sets
- **Rate Limiting / Throttling** – Redis counters + TTL
- **Pub/Sub Notifications** – Real-time chat or messaging
- **ML Preprocessed Feature Storage** – Low-latency feature serving

---

## 🧪 Real-World Scenarios & Root-Cause Fixes

---

### ✅ Scenario 1: High Read Load Slowing Down Database  
**Root Cause:** No caching layer for repeated queries  
**Fix:**  
- Implement **Redis as a read cache**  
- Use lazy loading (cache-aside)  
- Monitor cache hit ratio in CloudWatch

---

### ✅ Scenario 2: Redis Latency Spikes During Load Test  
**Root Cause:** Single-node under pressure, swap usage  
**Fix:**  
- Enable **clustering and sharding**  
- Use larger instances with more memory  
- Avoid SWAP by choosing cache-optimized instance types

---

### ✅ Scenario 3: Cache Evictions Happening Frequently  
**Root Cause:** Low memory or wrong TTL settings  
**Fix:**  
- Scale up cache node size or count  
- Set **appropriate TTLs**  
- Adjust eviction policy (e.g., `volatile-lru`, `volatile-ttl`)

---

### ✅ Scenario 4: ElastiCache not reachable from EC2  
**Root Cause:** Security Group or subnet misconfiguration  
**Fix:**  
- Attach ElastiCache and EC2 to same VPC/subnet group  
- Allow port 6379 (Redis) or 11211 (Memcached) in SG  
- Ensure no NACL is blocking traffic

---

### ✅ Scenario 5: Data Not Updating After DB Change  
**Root Cause:** Cache holds stale data  
**Fix:**  
- Invalidate cache manually on write  
- Or use **write-through caching** strategy

---

### ✅ Scenario 6: High Eviction and Low Hit Ratio  
**Root Cause:** Overloaded node with short TTL or large dataset  
**Fix:**  
- Monitor `Evictions`, `BytesUsedForCache` in CloudWatch  
- Shard across multiple nodes  
- Fine-tune data TTLs

---

### ✅ Scenario 7: Inconsistent Redis Replica Reads  
**Root Cause:** Asynchronous replication lag  
**Fix:**  
- Always read from **primary** if strong consistency is required  
- Monitor replication lag in metrics  
- Use fewer replicas for faster sync

---

### ✅ Scenario 8: Redis Cluster Failover Not Working  
**Root Cause:** No multi-AZ enabled or missing automatic failover  
**Fix:**  
- Enable **Multi-AZ with Auto Failover**  
- Use Redis 6+ with `ClusterMode=enabled`

---

## 🔒 Security Best Practices

- Deploy ElastiCache **in private subnets**
- Use **Security Groups** to restrict access (only to trusted EC2, Lambda, etc.)
- Use **Redis AUTH** for password protection
- Enable **TLS encryption in transit**
- Enable **Encryption at rest** (Redis only)
- Never expose to public internet!

---

## 📊 Monitoring (CloudWatch Metrics)

| Metric                    | Description |
|---------------------------|-------------|
| `CacheHitRate`            | % of cache hits |
| `Evictions`               | Items removed due to memory limits |
| `CurrConnections`         | Active client connections |
| `CPUUtilization`          | CPU usage |
| `SwapUsage`               | Should ideally be 0 |
| `ReplicationLag`          | Replication delay (Redis only) |

---

## 🧠 Interview Questions & Answers

### Q1: How do you decide between Redis and Memcached?
**A:**  
- Redis: Feature-rich, persistence, clustering, pub/sub  
- Memcached: Lightweight, multithreaded, simple cache

---

### Q2: What are common caching strategies?
- **Lazy Loading (Cache-Aside)**: App checks cache, loads from DB if not found  
- **Write-Through**: App writes to cache and DB together  
- **Write-Behind**: Write to cache first, then to DB asynchronously

---

### Q3: What causes frequent evictions in ElastiCache?
**A:**  
- Not enough memory  
- Large datasets with low TTLs  
- Wrong eviction policy (e.g., `noeviction`)

---

### Q4: How to protect ElastiCache from unauthorized access?
**A:**  
- Use **Security Groups + VPC**  
- Enable **AUTH + TLS**  
- Do not expose to internet

---

### Q5: How do you scale ElastiCache?
**A:**  
- Vertical: Increase node size  
- Horizontal: Enable Redis clustering (sharding)  
- Use **Multi-AZ Replication Group** for HA

---

### Q6: Redis vs DynamoDB DAX?
**A:**  
- **Redis**: General-purpose in-memory store  
- **DAX**: Fully managed caching for DynamoDB only, integrates natively

---

## ✅ Design Tips & Best Practices

- Use **replication groups** for Redis HA  
- For large-scale apps, enable **clustering** for sharding  
- Avoid using ElastiCache as a primary DB (no durability guarantees)  
- For session caching, use TTL + sliding expiration  
- Combine with **Route 53 health checks** for multi-region failover

---

## 📌 Summary

You now understand:
- Redis vs Memcached engine choices
- Real-world DevOps use cases: caching, session, pub/sub, rate limiting
- High availability, sharding, failover
- Troubleshooting: evictions, latency, auth, TTLs
- Secure configuration and monitoring
- Common interview questions with confident answers

✅ You’re ready to **architect, troubleshoot, and defend ElastiCache in any interview or real-world setup**!
