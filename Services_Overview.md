# 🛡️ Platinum DC–DR Plan – AWS Services Overview

This document lists all AWS services provisioned under the **Platinum Disaster Recovery (DC–DR) Plan** for the Cloud Management Portal (CMP).

Each service is mapped to its **deployment location (Primary, DR, or Global)** and includes the **reason it’s implemented** — ensuring an AI-driven, ransomware-resilient, multi-region AWS architecture.

---

## 1. Organization, Security & Governance
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Multi-Account Setup | **AWS Organizations + Control Tower** | **Global** | Establishes governed multi-account structure with landing zones and security guardrails. |
| Audit & Traceability | **AWS CloudTrail (Org Trail)** | **Global (All Regions)** | Captures every API and user action across all accounts for audit and compliance. |
| Configuration Compliance | **AWS Config + Conformance Packs** | **Primary + DR Regions** | Detects drift and enforces compliance with CIS/AWS Foundational benchmarks. |
| Threat Detection | **Amazon GuardDuty** | **Primary + DR Regions** | Detects malicious activity and unauthorized access across workloads. |
| Security Posture | **AWS Security Hub** | **Global (Aggregated)** | Centralized view of security findings and compliance scores. |
| Data Privacy | **Amazon Macie** | **Primary Region** | Scans S3 for sensitive data (PII, keys, credentials) to prevent data leaks. |
| Encryption | **AWS KMS (Multi-Region Keys)** | **Primary + DR Regions** | Encrypts all data sources (S3, RDS, EBS, logs, backups) with unified MRKs. |
| Access Control | **AWS IAM (Roles, Boundaries, SCPs)** | **All Accounts** | Ensures least-privilege access, prevents destructive actions, and isolates DR account. |
| Secret Management | **AWS Secrets Manager / SSM Parameter Store** | **Primary + DR Regions** | Stores credentials and environment parameters securely for applications and automation. |

---

## 2. Networking, Edge & Certificates
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Network Foundation | **Amazon VPC (3 AZ Design)** | **Primary + DR Regions** | Provides isolated, multi-AZ networking for application and data layers. |
| Private Access | **VPC Endpoints (S3, STS, ECR, SSM, CW, KMS)** | **Primary + DR Regions** | Keeps AWS service traffic internal, improving security and performance. |
| DNS & Failover | **Amazon Route 53** | **Global** | Provides health-based routing and automatic regional failover between regions. |
| Global Traffic Control | **AWS Global Accelerator** | **Global** | Offers sub-second failover with static Anycast IPs for seamless user redirection. |
| Web Protection | **AWS WAF + Shield Advanced** | **Global / ALB Edge** | Protects apps from DDoS and web-layer threats (L3/L7). |
| SSL/TLS Certificates | **AWS Certificate Manager (ACM)** | **Primary + DR Regions** | Manages SSL/TLS certificates for ALB, API Gateway, and Global Accelerator endpoints. |

---

## 3. Compute & Application Layer
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Container Orchestration | **Amazon EKS** | **Primary + DR Regions (Multi-AZ)** | Runs CMP microservices in a fault-tolerant, auto-scalable Kubernetes environment. |
| Container Registry | **Amazon ECR (Replicated)** | **Primary → DR Regions** | Ensures application images are available in both regions for failover. |
| Load Balancing | **Application Load Balancer (ALB)** | **Primary + DR Regions** | Distributes app traffic and integrates with Route 53 and WAF for protection. |
| Shared Storage | **Amazon EFS (Optional)** | **Primary + DR Regions** | Provides persistent shared storage across EKS nodes and AZs. |

---

## 4. Data Layer
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Relational Database | **Amazon Aurora Global Database** | **Primary (Writer) + DR (Reader)** | Enables near-zero-RPO replication and fast cross-region failover for databases. |
| NoSQL Database | **Amazon DynamoDB Global Tables** | **Primary + DR Regions** | Offers active-active data access with global replication for low latency. |
| Caching | **Amazon ElastiCache (Redis)** | **Primary + DR Regions** | Provides low-latency caching and session management, easing DB load. |

---

## 5. Object Storage & Replication
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Object Storage | **Amazon S3 (Versioning + Object Lock)** | **Primary + DR Regions** | Ensures immutable, ransomware-resistant data retention and recovery. |
| Data Replication | **S3 Cross-Region Replication (CRR)** | **Primary → DR Regions** | Maintains synchronized, validated data copies for recovery in DR region. |

---

## 6. Backup, Immutability & Recovery
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Centralized Backups | **AWS Backup** | **Primary + DR Regions** | Automates backups across RDS, EBS, EFS, DynamoDB, and EKS. |
| Immutability | **AWS Backup Vault Lock (Compliance Mode)** | **DR Account / Region** | Prevents deletion or tampering with backup data — even by admins. |
| Cross-Region Protection | **AWS Backup Cross-Region / Cross-Account Copy** | **Primary → DR** | Protects data from region or account compromise. |
| Backup Compliance | **AWS Backup Audit Manager** | **Security Account / Global** | Monitors adherence to backup policies and generates compliance evidence. |

---

## 7. Monitoring, AI/ML & Automation
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Observability | **Amazon CloudWatch (Logs, Metrics, Anomaly Detection)** | **Primary + DR Regions** | Tracks performance and detects abnormal activity automatically. |
| AIOps Analysis | **Amazon DevOps Guru** | **Primary + DR Regions** | Uses ML to identify operational anomalies and suggest remediations. |
| Business Anomaly Detection | **Amazon Lookout for Metrics** | **Primary + DR Regions** | Detects unusual database writes or S3 operations (possible ransomware). |
| Workflow Automation | **AWS Step Functions** | **Primary + DR Regions** | Automates failover, backup, and restoration workflows in sequence. |
| Event Routing | **Amazon EventBridge** | **Primary + DR Regions** | Connects anomaly events to automation and remediation actions. |
| Notifications | **Amazon SNS** | **Primary + DR Regions** | Sends alerts and recovery updates to engineering or NOC teams. |
| Systems Management | **AWS Systems Manager (Automation, Run Command)** | **Primary + DR Regions** | Provides secure, repeatable operations automation across accounts. |
| Resilience Testing | **AWS Fault Injection Simulator (FIS)** | **Primary + DR Regions** | Simulates failures to validate DR readiness and system resilience. |
| Synthetic Monitoring | **CloudWatch Synthetics (Optional)** | **Global** | Continuously validates application endpoint uptime and performance. |

---

## 8. Cost & Governance
| Area | Service We Will Implement | Where (Primary / DR / Region / Global) | Why It’s Needed |
|------|----------------------------|-----------------------------------------|-----------------|
| Budget Management | **AWS Budgets** | **Global / Per Account** | Sets and monitors monthly cost limits to avoid overruns. |
| Cost Visibility | **AWS Cost & Usage Report (CUR) + Athena / QuickSight** | **Logging / Shared Services Account** | Enables cost analysis and transparency for DR and data transfer costs. |

---

### ✅ Summary
This Platinum DC–DR configuration ensures:

- **Continuous uptime** during infrastructure, network, or regional failures  
- **Zero data loss** through global database replication and immutable backups  
- **Ransomware resilience** via Object Lock and Vault Lock immutability  
- **AI-driven anomaly detection** to stop replication of infected data  
- **Compliance readiness** with automated audits, encryption, and logging  

---

**Last Updated:** 2025-10-08  
**Prepared by:** Cloud Management Portal – Platinum DC–DR Architecture Team
