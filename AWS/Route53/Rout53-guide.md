# 🌍 AWS Route 53 – DevOps, Architecture & Interview Prep

---

## ✅ Overview

**Amazon Route 53** is a highly available and scalable DNS (Domain Name System) web service. It connects user requests to:

- AWS services (e.g., ELB, EC2, S3)
- On-prem servers
- External domain resources

Also provides:
- Domain registration
- DNS-based health checks
- Traffic routing across AWS/global infrastructure
- Hybrid DNS integration via Resolver Endpoints

---

## 🧱 Key Concepts

| Component             | Description |
|----------------------|-------------|
| **Hosted Zone**      | A container for DNS records (public or private) |
| **Record Set**       | DNS entry: A, CNAME, MX, Alias, etc. |
| **Alias Record**     | AWS-specific pointer to ELB, CloudFront, etc. |
| **TTL (Time to Live)**| Caching time for DNS clients |
| **Health Checks**    | Monitor resource health and enable failover |
| **Private Hosted Zone** | DNS resolvable only inside a VPC |
| **Resolver Endpoints** | Hybrid DNS setup between on-prem and AWS |

---

## 🚦 Routing Policies

| Routing Type             | Use Case |
|--------------------------|----------|
| **Simple**               | One static response |
| **Weighted**             | Load balancing, A/B testing |
| **Latency-Based**        | Route to the region with least latency |
| **Geolocation**          | Route users based on their location |
| **Geoproximity**         | Weighted routing + geographic bias |
| **Failover**             | Primary/secondary failover setup |
| **Multi-Value Answer**   | Return multiple healthy IPs (like round robin) |

---

## 🔒 Route 53 with VPC Integration

| Scenario | Solution |
|----------|----------|
| Internal DNS resolution | Use **Private Hosted Zones** |
| On-prem to AWS DNS | Use **Inbound Resolver Endpoint** |
| AWS to on-prem DNS | Use **Outbound Resolver Endpoint** |
| Split-horizon DNS | Use both public and private zones for same domain (internal vs external resolution) |

---

## 🔥 Real-World Scenarios & Troubleshooting

---

### ✅ Scenario 1: Route 53 Health Check Marking ALB as Unhealthy  
**Problem:** ALB is healthy but health check fails  
**Root Cause:**  
- Health check doesn’t follow redirects  
- Security group blocks health check IPs  
**Fix:**  
- Whitelist Route 53 health check IPs  
- Match response code (`200 OK`)  
- Increase `FailureThreshold` for reliability

---

### ✅ Scenario 2: DNS Change Taking Too Long  
**Problem:** Updated IP or record takes minutes to reflect  
**Root Cause:** Long TTL (Time-To-Live) caching on clients  
**Fix:**  
- Set shorter TTL (e.g., 60 seconds) during planned change windows  
- Adjust TTL strategy: short for failover, long for static sites

---

### ✅ Scenario 3: Global Latency for Users Outside Primary Region  
**Problem:** All users routed to a single region, increasing latency  
**Root Cause:** Simple routing policy  
**Fix:**  
- Use **Latency-Based Routing**  
- Deploy endpoints in multiple AWS regions  
- Optionally use CloudFront for caching

---

### ✅ Scenario 4: DNS Failover Not Triggering  
**Problem:** Primary service is down, but no failover occurs  
**Root Cause:**  
- Missing or misconfigured health check on primary record  
- TTL too high  
**Fix:**  
- Attach health check to primary record  
- Set routing policy to **Failover**  
- Reduce TTL to improve response time

---

### ✅ Scenario 5: Internal DNS Fails in VPC  
**Problem:** EC2 instances can’t resolve internal service names  
**Root Cause:**  
- Private hosted zone not associated with VPC  
- Missing AmazonProvidedDNS in DHCP options  
**Fix:**  
- Associate hosted zone with correct VPC(s)  
- Confirm DHCP option set includes Amazon DNS

---

### ✅ Scenario 6: On-Prem Servers Can’t Resolve AWS DNS  
**Problem:** Corporate network can’t resolve internal AWS services  
**Root Cause:** No hybrid DNS setup  
**Fix:**  
- Create **Route 53 Inbound Resolver Endpoint**  
- Forward AWS-internal domains from on-prem DNS to this endpoint

---

### ✅ Scenario 7: Route 53 Resolver Fails with Split-Horizon DNS  
**Problem:** Same domain resolves differently inside and outside VPC, causing confusion  
**Root Cause:** Both public and private hosted zones exist  
**Fix:**  
- Ensure correct record sets in each hosted zone  
- Test resolution using `dig`/`nslookup` inside and outside the VPC  
- Use **split-horizon DNS** consciously

---

## 🧠 Best Practices

- 🧪 Use **health checks** with failover to increase HA
- 🧱 Deploy **private hosted zones** for internal microservices
- 🌐 Use **Latency-based + Failover routing** for global and resilient apps
- 🧭 Set TTLs based on frequency of change (shorter for apps, longer for infra)
- 🔒 Always allow **Route 53 health check IPs** in NACLs/SGs when applicable
- 📊 Monitor with CloudWatch: Health check status, DNS queries, TTL behavior

---

## ❓ Interview Questions & Answers

### Q1: What is the difference between CNAME and Alias record?
**A:** CNAME is a DNS standard for mapping one domain to another. Alias is AWS-specific and can be used at the root domain (zone apex) and supports routing to AWS resources.

---

### Q2: Can Route 53 detect service outages and reroute traffic?
**A:** Yes, via health checks and **failover routing**.

---

### Q3: How do you configure internal DNS resolution within AWS?
**A:** Use **Private Hosted Zones** associated with your VPC.

---

### Q4: Can you use Route 53 for hybrid DNS with on-prem systems?
**A:** Yes. Use **Resolver Inbound/Outbound Endpoints** and forwarding rules.

---

### Q5: What happens if TTL is too long during failover?
**A:** DNS clients continue using cached records → failover delay.

---

### Q6: What if both public and private zones exist for the same domain?
**A:** Route 53 uses the **private zone inside the VPC**, allowing split-horizon DNS.

---

### Q7: How to prevent false health check failures?
**A:** Use correct ports, paths, status codes; whitelist Route 53 IPs; test endpoints manually with curl or browser.

---

## ✅ Summary

You can now confidently:

- Use and explain all Route 53 routing policies  
- Architect DNS failover, latency-based routing, and hybrid DNS  
- Troubleshoot TTL, health checks, and VPC-related issues  
- Ace any Route 53-related question in DevOps/cloud interviews  
- Implement best practices for DNS, availability, and security

