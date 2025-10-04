# BCP/DR Quick Vulnerability List — Cloud Management Portal

**Date:** 2025-10-03

For each item below: **Where is the problem**, **How it can be exploited / fail**, **How to resolve it**.

---

## 1. Route 53 DNS Failover
**Where:** Route 53 health checks / DNS TTL settings (failover records).  
**How exploited / fail:** Misconfigured health checks or long TTLs cause slow or no DNS failover; clients keep resolving to the failed primary.  
**Resolve:** Use low TTL (60–120s), configure robust health checks (ALB `/healthz`), add CloudWatch alarms + automation (Lambda) to update routing, consider Global Accelerator or CloudFront origin-failover for faster network-level switching.

---

## 2. Warm-Standby Ambiguity
**Where:** DR region resource state (unclear hot/warm/cold definitions).  
**How exploited / fail:** If DR is cold or under-provisioned, automated spin-up exceeds RTO; autoscaling not pre-warmed under load.  
**Resolve:** Classify workloads (Critical → Hot/Warm; Non-critical → Cold); document per-workload DR mode; pre-bake AMIs/launch templates; automate provisioning with Terraform/CloudFormation and test warm capacity regularly.

---

## 3. Database Replication Lag
**Where:** Cross-region DB replication (async) with no lag monitoring.  
**How exploited / fail:** High write volume or replication issues cause lag > RPO; logical corruption can replicate to DR.  
**Resolve:** Use appropriate tech (Aurora Global DB or properly monitored read-replicas), enable CloudWatch replica-lag alarms, enable PITR and delayed/immutable snapshots, test recovery and measure actual RPO.

---

## 4. Backup Hardening Gaps
**Where:** S3 backup buckets (no versioning / no object lock / weak KMS policies).  
**How exploited / fail:** Ransomware or accidental deletion removes backups; weak KMS allows unauthorized overwrite/decrypt.  
**Resolve:** Enable S3 Versioning, enable Object Lock (for new buckets) or create immutable buckets, use dedicated KMS CMKs with strict key policies, enable CRR for versioned objects, and test restoration.

---

## 5. Direct Admin SSH/RDP from Internet
**Where:** Admin access path (SSH/RDP open to Internet).  
**How exploited / fail:** Brute-force, credential theft, or lateral movement leading to full compromise, malicious changes, or backup deletion.  
**Resolve:** Remove direct Internet access; use SSM Session Manager or VPN + bastion with restricted source IPs; enforce MFA, JIT access, and session logging via CloudTrail/CloudWatch.

---

## 6. Ephemeral / Local Application State
**Where:** EC2 local disks or instance-local session state not centralized.  
**How exploited / fail:** Instance failures or region failover lose local state (uploads, sessions, temp files), causing app errors and data loss.  
**Resolve:** Move shared/ephemeral state to S3/EFS/managed DB; use ElastiCache/Multi-AZ for sessions or store sessions in DB; centralize logs to CloudWatch/Kinesis.

---

## 7. Missing Failback Procedure
**Where:** No documented failback (DR→Primary) steps or preconditions.  
**How exploited / fail:** Manual, error-prone failback causes data divergence, accidental overwrites, or prolonged downtime.  
**Resolve:** Create a detailed Failback Runbook: preconditions, sync steps, validation checksums, DNS switch steps, rollback criteria; practice via drills.

---

## 8. Monitoring & Alerting Gaps
**Where:** No comprehensive CloudWatch/alerts/incident escalation tied to RTO/RPO metrics.  
**How exploited / fail:** Failures go unnoticed or un-escalated; lead to missed RTO/RPO and delayed recovery.  
**Resolve:** Define key metrics (ALB 5xx, unhealthy hosts, DB lag, S3 replication failures), add CloudWatch alarms → SNS → on-call (PagerDuty/Slack), add synthetic health checks, and document escalation matrix.

---

## 9. Third-Party Dependency Risks
**Where:** External services (payment, auth, email) have no fallback plan.  
**How exploited / fail:** Third-party outage or compromise breaks business flows even if infra is up.  
**Resolve:** Inventory all third-party services, capture SLAs, implement fallbacks (alternate providers or degraded-mode behavior), and include third-party outage scenarios in DR drills.

---

## 10. Insider Credentials & Supply-Chain Risk
**Where:** Break-glass credentials, long-lived keys, CI/CD artifact verification gaps.  
**How exploited / fail:** Compromised credentials or malicious artifacts can delete backups, change routing, or deploy malicious code.  
**Resolve:** Enforce least-privilege IAM, short-lived credentials, MFA, artifact signing and dependency scanning in CI/CD, maintain immutable audit logs (CloudTrail + S3 Object Lock), and implement an auditable break-glass approval process.

---
