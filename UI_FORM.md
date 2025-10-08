# README — Customer Input & Scope (Platinum DR for AWS)

This document is for customers who want to **one-click** deploy a **Disaster Recovery (DR)** setup for their app on AWS.  

This plan targets **near-zero data loss** and **very short downtime** during failures.

---

## 1) What this plan will create (in simple words)

We will set up:

- **Two AWS regions**: a main region (Primary) and a backup region (DR).  
- **Fast traffic switch** if the main region has a problem (Route 53 + Global Accelerator).  
- **Kubernetes (EKS)** to run your app in both regions (DR kept warm).  
- **Global Database (Aurora Global)** so the DR region can take over quickly.  
- **Storage (S3)** with versioning and cross-region copy; optional **undeletable period (WORM)**.  
- **Backups (AWS Backup)**; optional **locked backups** that cannot be changed (Vault Lock).  
- **Security and monitoring**: Web Firewall (WAF), basic threat checks, health checks, dashboards, alerts.  
- **Smart protection**: if we see suspicious activity (like mass deletes), we can **pause replication** and **block deletes** (with human approval).

> **Out of scope** by default: on-prem networking, complex private networking, custom app changes, non-AWS services. (Can be added later.)

---

## 2) What happens if I don’t know?

If you leave a field blank, we use **safe defaults** and keep going.  
We **do not** enable **irreversible protections** (like WORM/locked backups) unless you **tick “Yes, I understand.”**

**Defaults we use if blank:**
- **Primary = Mumbai (ap-south-1)**, **DR = Singapore (ap-southeast-1)**.  
- Public HTTPS with a free AWS certificate; if you don’t have a domain, we use a **temporary test name**.  
- Health checks every **5 minutes**; alerts to your email.  
- **Versioning + cross-region copy** for S3; move old files to archive after **90 days**.  
- **Backups** daily; keep 7 days **hot** (can be extended).  
- **AI guard ON** (asks you before taking strong actions).  
- **Monthly budget cap = USD 3000** with alerts at **50/80/100%**.

---

## 3) One-page intake form (copy this, fill, and return)

> If a field is unclear, leave it blank. We’ll apply safe defaults.

### A. Company & Owner
| Field | Why | Your answer |
|---|---|---|
| Company / Project name | Name resources & dashboards | |
| 24×7 alert email | Where we send critical alerts | |
| AWS setup | Single account or multi-account (Org)? | Single / Multi |

### B. Recovery Targets & Regions
| Field | Why | Your answer |
|---|---|---|
| Max downtime (RTO, minutes) | Speeds up failover | (default: 5) |
| Max data loss (RPO, seconds) | Tightens replication | (default: 30) |
| Primary region | Where app runs | (default: ap-south-1) |
| DR region | Where we recover | (default: ap-southeast-1) |
| Any data-residency rule? | Keeps data in a country/region | Yes/No + note |

### C. Internet Address (DNS & HTTPS)
| Field | Why | Your answer |
|---|---|---|
| Your domain name | Public address & HTTPS | |
| App URL you want | User-facing URL | `cmp.example.com` |
| Route 53 hosted zone? | Auto-create records | Yes (Zone ID: ____ ) / No |
| HTTPS certificate | Secure traffic | Auto-issue (recommended) / I have one (ARN) |

### D. Network Access
| Field | Why | Your answer |
|---|---|---|
| Office/Public IP (CIDR) | Restrict admin access | e.g., `203.0.113.10/32` |
| Private-only app endpoints? | Public vs private app | No (public HTTPS) / Yes (private) |

### E. Security (Edge & Baseline)
| Field | Why | Your answer |
|---|---|---|
| DDoS protection (Shield Advanced) | Stronger edge protection (extra cost) | Yes / No |
| Web firewall | Block common attacks | Managed core rules (default) / Custom |
| Security checks | Continuous best-practice checks | AWS Foundational + CIS (default) / Other |

### F. Application (EKS)
| Field | Why | Your answer |
|---|---|---|
| App container image | What to run | e.g., `public.ecr.aws/nginx:latest` |
| App port | For load balancer & health | 80 / 8080 |
| Health check path | Auto-healing & failover | `/health` |
| Min pods (Primary/DR) | Baseline capacity | `3 / 1` (default) |
| Max pods | Allow burst | `30` (default) |

### G. Database (Aurora Global)
| Field | Why | Your answer |
|---|---|---|
| Engine | DB flavor | Aurora PostgreSQL (default) / Aurora MySQL |
| Size today (GB, rough) | Right-size compute | e.g., `50` |
| DB username | App access (password auto) | e.g., `cmp_admin` |
| Point-in-time restore | Fix mistakes fast | On (default) / Off |

### H. Storage (S3)
| Field | Why | Your answer |
|---|---|---|
| Bucket name prefix | Easy naming | e.g., `acme-cmp` |
| Copy data to DR | Near-zero data loss | On (RTC) (default) / Off |
| Archive old files after (days) | Control cost | 90 (default) |
| **Object Lock WORM** (days) | Make files undeletable for X days | (leave blank unless approved) |

### I. Backups (AWS Backup)
| Field | Why | Your answer |
|---|---|---|
| Keep backups for (years) | Compliance need | e.g., `7` |
| Copy backups to DR | Extra safety | Yes (default) / No |
| **Vault Lock (WORM)** | Backups cannot be changed | Yes — I understand / No |

### J. Monitoring, Alerts & AI Guard
| Field | Why | Your answer |
|---|---|---|
| Health URL | Continuous checks | e.g., `https://cmp.example.com/health` |
| Alerts to (email/Slack/Teams) | Who gets notified | e.g., `noc@acme.com`, `#cmp-dr-alerts` |
| Pause replication if suspicious? | Ransomware safety | Yes (asks first) (default) / No |

### K. Budget & Cost Safety
| Field | Why | Your answer |
|---|---|---|
| Monthly budget cap (USD) | Spend guardrail | 3000 (default) |
| Alert thresholds | Early warnings | 50% / 80% / 100% (default) |
| NAT strategy | Cost vs resilience | Single NAT (cheaper) / Per-AZ (default) |

### L. DR Testing (GameDay)
| Field | Why | Your answer |
|---|---|---|
| How often to test | Confidence in recovery | Monthly (default) / Quarterly / Twice a year |
| Allow short live flip (~5 min) off-peak? | Full end-to-end proof | Yes / No (default: No) |

### M. Approvals (Irreversible or Sensitive)
Tick only if you agree:

- **Object Lock WORM (S3)**: files **cannot** be deleted/changed for the set days.  
  ☐ **Yes — I understand** (days: ____)   ☐ No

- **Vault Lock (Backups)**: backups **cannot** be changed/removed.  
  ☐ **Yes — I understand**   ☐ No

- **AI Guard** may **pause replication** and **block deletes** when there is risk (will ask first).  
  ☐ Yes (default)   ☐ No

- **DDoS Shield Advanced** (extra cost).  
  ☐ Yes   ☐ No (default)

---

## 4) What we will do with your answers

- **Create or use** the right AWS regions.  
- Set up **traffic switch** (Route 53 + Global Accelerator).  
- Create **EKS** in both regions (DR warm).  
- Create **Aurora Global** (writer in Primary, reader in DR).  
- Create **S3** with versioning and cross-region copy; set **archive rules**.  
- Create **Backups** and (if approved) **Locked Backups**.  
- Turn on **Web Firewall**, health checks, **dashboards**, and **alerts**.  
- Enable **AI Guard** to reduce blast radius (asks before acting).

We also do **pre-checks**: region support, limits/quotas, unique names, DNS/cert, and safe permissions.

---

## 5) Simple glossary (no jargon)

- **Primary**: your main AWS region where the app runs today.  
- **DR region**: the backup AWS region we switch to during problems.  
- **Failover**: the automatic switch from Primary to DR.  
- **RTO**: how much **downtime** you can accept.  
- **RPO**: how much **data loss** (in seconds) you can accept.  
- **WORM / Object Lock**: makes files undeletable for a set time.  
- **Vault Lock**: makes backups unchangeable.  
- **Canary**: a simple test that hits your app page to check it’s healthy.

---

## 6) Safety notes

- **WORM / Vault Lock** cannot be shortened once set. Only tick **“Yes — I understand”** if your legal/compliance team agrees.  
- We keep a **break-glass** path (human approval) for actions that pause replication or block deletes.  
- We set a **budget** and alerts so you’re not surprised by costs.

---

## 7) Ready? Final checklist before we deploy

- You shared an **AWS Account ID** and **alert email**.  
- You confirmed your **domain** or accepted a **temporary test name**.  
- You ticked approvals for any **irreversible settings** (if needed).  
- You agreed to the **budget cap** and **alert thresholds**.

Once this form is returned, we can proceed with the one-click deployment.

---
