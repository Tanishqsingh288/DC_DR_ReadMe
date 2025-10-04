# High-Level DC/DR Plan – Risk & Improvement Review

**Date:** 2025-10-03  
**Note:** This is a *conceptual / high-level design plan* for the CMP Disaster Recovery setup.  
The points below highlight **where risks exist in the proposed design**, **how they could impact real-world implementation**, and **what refinements are recommended** before moving to detailed build.

---

## 1. Route 53 DNS Failover
- **Where:** DNS-based failover with Route 53.  
- **Risk:** DNS TTLs and health-check misconfigurations may delay failover.  
- **Impact:** Failover might not meet RTO (≤30 min).  
- **Refinement:** Document TTL values and health-check endpoints now; consider adding Global Accelerator in detailed design.

---

## 2. Warm-Standby Ambiguity
- **Where:** DR region design (shown as passive site).  
- **Risk:** Unclear if DR is cold, warm, or hot → uncertainty in cost vs RTO tradeoff.  
- **Impact:** Could fail to meet stated recovery objectives in practice.  
- **Refinement:** In detailed plan, classify workloads as Critical / High / Low and define per-mode (hot/warm/cold).

---

## 3. Database Replication Lag
- **Where:** Cross-region DB replication.  
- **Risk:** Async replication may exceed 15 min RPO.  
- **Impact:** Possible data loss during DR switch.  
- **Refinement:** Evaluate Aurora Global DB vs standard RDS replication; add lag monitoring and alerting in detailed plan.

---

## 4. Backup Hardening
- **Where:** S3 cross-region replication.  
- **Risk:** Without versioning/immutability, backups could be deleted or corrupted.  
- **Impact:** Loss of recovery data.  
- **Refinement:** Plan for S3 Versioning + Object Lock + KMS upfront, so bucket design supports it from day one.

---

## 5. Direct Admin Access
- **Where:** Diagram shows Internet-based SSH/RDP.  
- **Risk:** Insecure if directly exposed.  
- **Impact:** Potential breach or misuse during DR.  
- **Refinement:** Replace with SSM Session Manager or VPN+bastion in the final detailed plan.

---

## 6. Application Local State
- **Where:** EC2/VMs in diagram.  
- **Risk:** Local/temp state not replicated across sites.  
- **Impact:** Session loss, user disruption during failover.  
- **Refinement:** Plan shared-state architecture (S3/EFS/ElastiCache) in detailed design.

---

## 7. Failback Not Defined
- **Where:** Plan covers failover but not return to primary.  
- **Risk:** Lack of documented failback creates uncertainty.  
- **Impact:** Extended downtime when primary recovers.  
- **Refinement:** Add a failback strategy section in detailed design (data sync, DNS return, validation).

---

## 8. Monitoring & Alerting
- **Where:** Not shown in high-level diagram.  
- **Risk:** Failures may not be detected in time.  
- **Impact:** RTO/RPO breach.  
- **Refinement:** Include CloudWatch/GuardDuty/SNS in the next design phase.

---

## 9. Third-Party Dependencies
- **Where:** Not mentioned in scope.  
- **Risk:** External services (auth, payment, email) may fail independently.  
- **Impact:** DR site is up but business still impacted.  
- **Refinement:** Inventory and add contingency/fallbacks in final plan.

---

## 10. Insider & Supply-Chain Risks
- **Where:** IAM / access / CI-CD not detailed yet.  
- **Risk:** Keys, privileged access, or pipeline compromises could undermine DR.  
- **Impact:** Backup deletion or malicious failover.  
- **Refinement:** Add IAM/MFA, break-glass accounts, CI/CD signing in final plan.

---

# Key Takeaway
Before moving to implementation:  
- Decide **DR modes per workload**.  
- Add **failback strategy**.  
- Design for **monitoring, secure access, and immutable backups**.  
- Document **business processes and third-party contingencies**.

This way the detailed plan will be **audit-ready** and execution can reliably achieve stated RTO/RPO.
