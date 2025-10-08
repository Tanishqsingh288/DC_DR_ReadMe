# 🛡️ Platinum DC–DR Plan – AWS Services Overview

This document lists all AWS services that will be provisioned under the **Platinum Disaster Recovery Plan** for the Cloud Management Portal (CMP).

Each service is carefully selected to ensure **zero data loss (RPO ≈ 0)**, **minimal downtime (RTO < 5 min)**, and **ransomware-resilient recovery** through automation and AI-driven anomaly detection.

---

## 1. Organization, Security & Governance
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Multi-Account Setup | **AWS Organizations + Control Tower** | Establishes a governed multi-account structure with landing zones and guardrails. |
| Audit & Traceability | **AWS CloudTrail (Org Trail)** | Records every user and API action across all accounts for audit and compliance. |
| Configuration Compliance | **AWS Config + Conformance Packs** | Detects misconfigurations and enforces compliance baselines (CIS/AWS standards). |
| Threat Detection | **Amazon GuardDuty** | Continuously monitors for malicious activity or unauthorized access. |
| Security Posture Management | **AWS Security Hub** | Aggregates security findings and compliance scores across all regions and accounts. |
| Data Privacy | **Amazon Macie** | Identifies and protects sensitive data stored in S3. |
| Encryption | **AWS KMS (Multi-Region Keys)** | Encrypts data across regions for EBS, S3, RDS, and backups with unified keys. |
| Access Management | **AWS IAM (Roles, Boundaries, SCPs)** | Enforces least-privilege, cross-account access, and deletion protections. |
| Secret Management | **AWS Secrets Manager / SSM Parameter Store** | Stores and rotates credentials and configuration values securely. |

---

## 2. Networking, Edge & Certificates
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Network Foundation | **Amazon VPC (3 AZ Design)** | Creates isolated, fault-tolerant networks for workloads in each region. |
| Private Access | **VPC Endpoints (S3, STS, ECR, SSM, CW, KMS)** | Ensures private AWS service connectivity without internet exposure. |
| DNS & Health Checks | **Amazon Route 53** | Provides health-based routing and automatic regional failover. |
| Global Traffic Control | **AWS Global Accelerator** | Enables sub-second user rerouting between regions via Anycast IPs. |
| Web Protection | **AWS WAF + Shield Advanced** | Protects web apps and APIs from DDoS and L7 threats. |
| TLS Certificates | **AWS Certificate Manager (ACM)** | Manages SSL/TLS certificates for ALB and Global Accelerator. |

---

## 3. Compute & Application Layer
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Container Orchestration | **Amazon EKS (Primary & DR)** | Runs resilient microservices across multiple AZs with managed Kubernetes. |
| Container Registry | **Amazon ECR (Replicated)** | Stores application images with automatic cross-region replication. |
| Application Entry | **Application Load Balancer (ALB)** | Balances traffic and integrates with WAF and Route 53 health checks. |
| Shared Storage | **Amazon EFS (Optional)** | Provides persistent, shared file storage for application pods. |

---

## 4. Data Layer
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Relational Database | **Amazon Aurora Global Database** | Enables near-zero-RPO replication and instant regional promotion. |
| NoSQL Database | **Amazon DynamoDB Global Tables** | Offers active-active data availability and sub-second replication. |
| In-Memory Cache | **Amazon ElastiCache (Redis)** | Reduces latency and improves performance for session and cache data. |

---

## 5. Object Storage & Replication
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Object Storage | **Amazon S3 (Versioning + Object Lock)** | Provides immutable, ransomware-proof data storage with WORM compliance. |
| Cross-Region Copy | **S3 Cross-Region Replication (CRR)** | Maintains synchronized, isolated data copies in DR region. |

---

## 6. Backup, Immutability & Recovery
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Centralized Backups | **AWS Backup** | Automates consistent backup plans for all supported AWS services. |
| Immutability | **AWS Backup Vault Lock (Compliance Mode)** | Prevents deletion or modification of backups, even by admins. |
| Cross-Account Copies | **AWS Backup Cross-Region & Cross-Account Copy** | Protects from regional failure or account compromise. |
| Backup Compliance | **AWS Backup Audit Manager** | Provides evidence of backup integrity and policy compliance. |

---

## 7. Monitoring, AI/ML & Automation
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Observability | **Amazon CloudWatch (Logs, Metrics, Anomaly Detection)** | Detects spikes, latency, and unusual operations automatically. |
| AIOps | **Amazon DevOps Guru** | Uses ML to detect and diagnose anomalies in EKS/RDS environments. |
| Business Anomalies | **Amazon Lookout for Metrics** | Identifies abnormal access or data patterns indicating ransomware. |
| Workflow Orchestration | **AWS Step Functions** | Automates recovery, failover, and restoration workflows. |
| Event Routing | **Amazon EventBridge** | Triggers automated responses based on monitoring and anomalies. |
| Notifications | **Amazon SNS** | Sends incident alerts and recovery notifications. |
| Systems Management | **AWS Systems Manager (Run Command/Automation)** | Executes repeatable operational tasks securely and at scale. |
| Resilience Testing | **AWS Fault Injection Simulator (FIS)** | Validates system behavior and RTO/RPO targets through controlled chaos tests. |
| Synthetic Monitoring | **CloudWatch Synthetics (Optional)** | Continuously tests endpoint health and availability. |

---

## 8. Cost & Governance
| Area | Service We Will Implement | Why It’s Needed |
|------|----------------------------|-----------------|
| Budget Control | **AWS Budgets** | Sets cost thresholds and sends alerts to avoid overruns. |
| Cost Transparency | **AWS Cost & Usage Report (CUR) + Athena/QuickSight** | Provides detailed cost analytics for DR and replication workloads. |

---

### ✅ Summary

This service stack ensures:
- **Continuous uptime** during failures or attacks  
- **Zero data loss** with immutable backups and multi-region replication  
- **AI-driven anomaly detection** to prevent ransomware spread  
- **Full compliance and auditability** with minimal manual intervention  

---

**Last Updated:** 2025-10-08  
**Prepared for:** Enterprise Platinum DC–DR Implementation
