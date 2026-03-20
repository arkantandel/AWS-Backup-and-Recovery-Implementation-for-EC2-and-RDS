<div align="center">

![Banner](Images/banner.png)

<br/>

[![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com)
[![EC2](https://img.shields.io/badge/Amazon_EC2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white)](https://aws.amazon.com/ec2)
[![RDS](https://img.shields.io/badge/Amazon_RDS-527FFF?style=for-the-badge&logo=amazonrds&logoColor=white)](https://aws.amazon.com/rds)
[![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)](https://mysql.com)
[![Status](https://img.shields.io/badge/Status-✅_Completed-brightgreen?style=for-the-badge)](.)
[![Batch](https://img.shields.io/badge/Batch-28_July-blueviolet?style=for-the-badge)](.)

<br/>

![Region](https://img.shields.io/badge/Region-ap--northeast--1_Tokyo-orange?style=flat-square)
![Backup](https://img.shields.io/badge/Backup-Daily_%7C_7_Days-blue?style=flat-square)
![Vault](https://img.shields.io/badge/Vault-my--backup--vault-green?style=flat-square)
![Encryption](https://img.shields.io/badge/Encryption-AWS_KMS-purple?style=flat-square)
![EC2](https://img.shields.io/badge/EC2-project--2_✅-success?style=flat-square)
![RDS](https://img.shields.io/badge/RDS-database--1_✅-success?style=flat-square)

</div>

---

## 📋 Table of Contents

| # | Section | Description |
|:---:|:---|:---|
| 1 | [😰 The Problem](#-the-problem) | Why backups are critical |
| 2 | [✅ The Solution](#-the-solution) | What AWS Backup does for you |
| 3 | [🏗️ Architecture](#️-architecture-diagram) | Full system design with flow |
| 4 | [☁️ AWS Services](#️-aws-services-used) | Every service and its role |
| 5 | [⚙️ Configuration](#️-configuration-details) | All settings used |
| 6 | [🛠️ Step-by-Step](#️-step-by-step-setup) | Complete 9-step guide |
| 7 | [📸 Screenshots](#-screenshots) | Proof of every step |
| 8 | [🚧 Difficulties](#-difficulties--solutions) | 5 problems + exact fixes |
| 9 | [💡 Key Learnings](#-key-learnings) | What this project taught |

---

## 😰 The Problem

<div align="center">

```
╔══════════════════════════════════════════════════════════════════╗
║              THE SILENT RISK EVERY AWS USER IGNORES             ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  📅  Day  1  →  You launch EC2 + RDS. Everything works.        ║
║  📅  Day  7  →  Deep in development. Features are building.    ║
║  📅  Day 14  →  EC2 instance corrupts. RDS data gets deleted.  ║
║  📅  Day 14  →  You search for backups...                       ║
║  📅  Day 14  →  ❌  THERE ARE NONE.                             ║
║  📅  Day 14  →  Weeks of work — gone. Forever.                 ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

</div>

**AWS does NOT automatically back up your EC2 instances or RDS databases by default.**

Most developers never set it up — until they lose everything. This project solves that completely.

> 💡 **Fact:** 30% of data loss incidents happen due to missing or incorrectly configured backups.

---

## ✅ The Solution

**AWS Backup Service** — a centralised, fully managed backup system that protects your cloud resources automatically.

<div align="center">

| Feature | What It Does | Benefit |
|:---:|:---|:---|
| 🔁 **Auto Daily Backup** | Creates a recovery point for EC2 and RDS every day | Never manually backup again |
| 🔐 **KMS Encryption** | All backup data is encrypted at rest using AWS KMS | Data stays secure even in storage |
| 📅 **7-Day Retention** | AWS auto-manages recovery points — keeps 7, deletes older | No manual cleanup needed |
| 📊 **Real-Time Monitoring** | Watch every job: Running → Completed → Success | Know the moment backup finishes |
| 💾 **Secure Vault** | Backup vault cannot be deleted when recovery points exist | Backups can never be accidentally removed |
| ♻️ **Fast Recovery** | Restore from any recovery point in minutes | Downtime measured in minutes not days |

</div>

---

## 🏗️ Architecture Diagram

<div align="center">

![Architecture](Images/architecture.png)

</div>

### 🔄 How the Flow Works

```
  ┌──────────────────────────────────────────────────────────────────┐
  │                       COMPLETE DATA FLOW                        │
  ├──────────────────────────────────────────────────────────────────┤
  │                                                                  │
  │  ⏰ STEP 1  →  EventBridge fires every 24 hours automatically  │
  │                          │                                      │
  │  📋 STEP 2  →  Backup Plan (daily-backup-plan) is triggered    │
  │                          │                                      │
  │  🚀 STEP 3  →  Backup Jobs created for EC2 + RDS resources    │
  │                          │                                      │
  │  📸 STEP 4  →  AWS takes snapshots of both EC2 and RDS        │
  │                          │                                      │
  │  🔒 STEP 5  →  Recovery Points stored in vault (KMS encrypted) │
  │                          │                                      │
  │  ✅ STEP 6  →  Job status: Running ⏳ → Completed ✅           │
  │                          │                                      │
  │  🛡️ STEP 7  →  Protected Resources page updated               │
  │                                                                  │
  └──────────────────────────────────────────────────────────────────┘
```

---

## ☁️ AWS Services Used

<div align="center">

| Service | Role | Configuration |
|:---|:---|:---|
| 🛡️ **AWS Backup** | Core service — manages all backup operations | Backup plan + vault created |
| 💻 **Amazon EC2** | Compute resource being protected | project-2 · t3.micro · Running |
| 🗄️ **Amazon RDS** | Database resource being protected | database-1 · MySQL Community |
| 🔑 **AWS IAM** | Permissions for AWS Backup to access resources | AWSBackupDefaultServiceRole |
| 🔐 **AWS KMS** | Encrypts all recovery points at rest | Default managed key (aws/backup) |
| ⏰ **EventBridge** | Triggers backup plan on daily schedule | Fires automatically every 24 hours |

</div>

---

## ⚙️ Configuration Details

<div align="center">

| Parameter | Value |
|:---|:---|
| **Project** | Project 2 — AWS Backup & Recovery for EC2 and RDS |
| **AWS Region** | `ap-northeast-1` (Asia Pacific — Tokyo) |
| **EC2 Instance** | `project-2` · `i-0ab241d1097418d99` · `t3.micro` |
| **EC2 Public IP** | `54.95.157.120` |
| **RDS Instance** | `database-1` · MySQL Community · Available |
| **Backup Vault** | `my-backup-vault` |
| **Backup Plan** | `daily-backup-plan` |
| **Backup Rule** | `daily-rule` |
| **Backup Frequency** | Daily |
| **Backup Window** | 5:00 AM UTC (default) |
| **Retention Period** | 7 Days |
| **Encryption** | AWS KMS — default managed key |
| **IAM Role** | `AWSBackupDefaultServiceRole` |
| **Backup Job ID** | `c00942be-4506-417d-b59e-8119647a390d` |
| **Job Status** | ✅ Completed — Success |
| **Last Backup** | March 20, 2026 · 18:06:22 UTC+05:30 |

</div>

---

## 🛠️ Step-by-Step Setup

### ✅ Step 1 — Verify EC2 Instance is Running

> 📍 `AWS Console → Search "EC2" → EC2 → Instances`

```yaml
Instance Name  : project-2
Instance ID    : i-0ab241d1097418d99
Instance Type  : t3.micro
Instance State : Running  ✅
Status Check   : 3/3 checks passed  ✅
Public IP      : 54.95.157.120
```

![](<Images/Screenshot (605).png>)

---

### ✅ Step 2 — Verify RDS Database is Available

> 📍 `AWS Console → Search "RDS" → Aurora and RDS → Databases`

```yaml
DB Identifier : database-1
Status        : Available  ✅
Engine        : MySQL Community
Role          : Instance
```

![](<Images/Screenshot (604).png>)

---

### ✅ Step 3 — Open AWS Backup Service

> 📍 `AWS Console → Search "Backup" → AWS Backup`

- You land on the **AWS Backup Dashboard**
- Click **Vaults** in the left sidebar

---

### ✅ Step 4 — Create a Backup Vault

> 📍 `AWS Backup → Vaults → Create backup vault`

```yaml
Vault Name     : my-backup-vault
Vault Type     : Backup
Encryption Key : Default AWS managed key (aws/backup)
```

Click **Create backup vault** ✅

> ⚠️ A vault cannot be deleted once it contains recovery points.

![](<Images/Screenshot (603).png>)

---

### ✅ Step 5 — Create a Backup Plan

> 📍 `AWS Backup → Backup plans → Create backup plan → Build a new plan`

```yaml
Backup Plan Name : daily-backup-plan
Rule Name        : daily-rule
Backup Vault     : my-backup-vault
Backup Frequency : Daily
Backup Window    : Default (5:00 AM UTC)
Retention Period : 7 days
```

Click **Create plan** ✅

---

### ✅ Step 6 — Assign Resources to the Plan

> 📍 `Inside daily-backup-plan → Assign resources`

```yaml
Assignment Name : ec2-rds-assignment
IAM Role        : Default role (AWSBackupDefaultServiceRole)
Resources:
  - Type: EC2  →  Instance: project-2
  - Type: RDS  →  Database: database-1
```

Click **Assign resources** ✅

> ⚠️ Resources will NOT appear in Protected Resources until the first backup job completes.

---

### ✅ Step 7 — Trigger an On-Demand Backup

> 📍 `AWS Backup → Protected resources → database-1 → Create on-demand backup`

```yaml
Backup Vault : my-backup-vault
```

Click **Create on-demand backup** → Go to **Jobs → Backup jobs** to monitor.

---

### ✅ Step 8 — Monitor Job: Running → Completed

> 📍 `AWS Backup → Jobs → Backup jobs`

![](<Images/Screenshot (599).png>)
*Backup job actively Running*

![](<Images/Screenshot (602).png>)
*Same job — Completed with Status: Success ✅*

![](<Images/Screenshot (600).png>)
*Jobs dashboard — full completion details*

---

### ✅ Step 9 — Verify Protected Resources

> 📍 `AWS Backup → Protected resources`

```yaml
Resource Name : database-1
Resource Type : RDS
Last Backup   : March 20, 2026 · 18:06:22 UTC+05:30  ✅
```

![](<Images/Screenshot (601).png>)

**🎉 Setup Complete! EC2 and RDS are now protected automatically every day.**

---

## 🚧 Difficulties & Solutions

<details>
<summary><b>❌ Problem 1 — EC2 Not Visible in Resource Assignment</b></summary><br/>

| Item | Details |
|---|---|
| **Problem** | EC2 instance `project-2` was not showing in the resource selection |
| **Root Cause** | AWS Console was on `us-east-1` but EC2 was in `ap-northeast-1` |
| **Solution** | Changed region to `ap-northeast-1 (Tokyo)` from top-right dropdown |
| **Prevention** | Always verify correct AWS region before working with resources |

</details>

<details>
<summary><b>❌ Problem 2 — Vault Name Already Exists Error</b></summary><br/>

| Item | Details |
|---|---|
| **Problem** | Error: `"Vault name already exists"` when creating `my-backup-vault` |
| **Root Cause** | A vault with same name existed from an earlier incomplete attempt |
| **Solution** | Deleted the old empty vault → created `my-backup-vault` fresh |
| **Prevention** | Check existing vaults before creating new ones |

</details>

<details>
<summary><b>❌ Problem 3 — Backup Job Stuck in Running</b></summary><br/>

| Item | Details |
|---|---|
| **Problem** | Job stayed in `Running` for several minutes |
| **Root Cause** | RDS backups create full DB snapshots — takes longer than EC2 |
| **Solution** | Refreshed Jobs page every 60 seconds. Completed after ~6 minutes. |
| **Prevention** | Allow 5-15 minutes for RDS backup completion |

</details>

<details>
<summary><b>❌ Problem 4 — IAM AccessDeniedException</b></summary><br/>

| Item | Details |
|---|---|
| **Problem** | First job failed: `AccessDeniedException` |
| **Root Cause** | IAM role missing `AWSBackupServiceRolePolicyForBackup` policy |
| **Solution** | IAM → Roles → `AWSBackupDefaultServiceRole` → Attach both required policies |
| **Prevention** | Verify IAM policies before running backup jobs |

</details>

<details>
<summary><b>❌ Problem 5 — Protected Resources Showed Empty</b></summary><br/>

| Item | Details |
|---|---|
| **Problem** | Protected Resources showed 0 resources after resource assignment |
| **Root Cause** | AWS only populates this page AFTER first backup job completes |
| **Solution** | Triggered on-demand backup for `database-1`. Appeared after completion. |
| **Prevention** | Always run an on-demand backup to confirm the setup works |

</details>

---

## 💡 Key Learnings

```
┌──────────────────────────────────────────────────────────────────────┐
│                         KEY TAKEAWAYS                                │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ✅  AWS Backup centralises EC2 + RDS backup in ONE console         │
│  ✅  Backup Vault = secure container. Cannot delete with data inside │
│  ✅  Protected Resources only show AFTER first backup completes     │
│  ✅  IAM role MUST have correct managed policies before backup runs  │
│  ✅  RDS backups take LONGER than EC2 — full DB snapshot needed     │
│  ✅  On-demand backup lets you test without waiting for schedule     │
│  ✅  KMS encryption is automatic — all data encrypted at rest       │
│  ✅  7-day retention = AWS auto-deletes old recovery points         │
│  ✅  Region must MATCH between resources and backup vault           │
│  ✅  Recovery points can restore entire instances in minutes        │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 🔮 What Can Be Added Next

- [ ] 🌍 Configure cross-region backup copy for disaster recovery
- [ ] 📲 Set up SNS notifications for backup job alerts
- [ ] 📊 Enable AWS Backup Audit Manager for compliance reports
- [ ] 🏗️ Automate full backup setup using Terraform (IaC)
- [ ] 🔁 Set up restore testing to verify recovery points work
- [ ] 📦 Add S3 buckets to the backup protection plan

---

<div align="center">

<br/>

**Arkan Tandel · 28 July Batch · Project 2**

[![EC2](https://img.shields.io/badge/EC2-Protected_✅-success?style=flat-square)](.)
[![RDS](https://img.shields.io/badge/RDS-Protected_✅-success?style=flat-square)](.)
[![Vault](https://img.shields.io/badge/Vault-Encrypted_🔐-blue?style=flat-square)](.)
[![Backup](https://img.shields.io/badge/Backup-Automated_⚡-orange?style=flat-square)](.)
[![KMS](https://img.shields.io/badge/KMS-Secured_🔒-purple?style=flat-square)](.)

<br/>

*"Your data is your most valuable asset.*
*Protect it before you need to restore it."*

</div>
