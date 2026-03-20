<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:185FA5,100:1D9E75&height=200&section=header&text=AWS%20Backup%20%26%20Recovery&fontSize=40&fontColor=ffffff&animation=fadeIn&fontAlignY=35&desc=EC2%20%26%20RDS%20Automated%20Backup%20Implementation&descAlignY=55&descSize=16&descColor=e0f0ff"/>

<br/>

[![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com)
[![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)](https://mysql.com)
[![EC2](https://img.shields.io/badge/Amazon_EC2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white)](https://aws.amazon.com/ec2)
[![RDS](https://img.shields.io/badge/Amazon_RDS-527FFF?style=for-the-badge&logo=amazonrds&logoColor=white)](https://aws.amazon.com/rds)
[![Status](https://img.shields.io/badge/Status-✅%20Completed-brightgreen?style=for-the-badge)](.)
[![Batch](https://img.shields.io/badge/Batch-28%20July-blueviolet?style=for-the-badge)](.)

<br/>

# 🛡️ AWS Backup & Recovery Implementation
### *for EC2 and RDS — Automated · Secure · Always On*

<br/>

> **"Data loss isn't a question of IF — it's a question of WHEN.**
> **This project makes sure WHEN never destroys everything."**

<br/>

**👤 Arkan Tandel &nbsp;·&nbsp; 28 July Batch**

<br/>

![Visitors](https://img.shields.io/badge/Project-2-blue?style=flat-square)
![Region](https://img.shields.io/badge/Region-ap--northeast--1-orange?style=flat-square)
![Vault](https://img.shields.io/badge/Vault-my--backup--vault-green?style=flat-square)
![Backup](https://img.shields.io/badge/Backup-Daily%20%7C%207%20Days-purple?style=flat-square)
![Encryption](https://img.shields.io/badge/Encryption-AWS%20KMS-red?style=flat-square)

</div>

---

## 📋 Table of Contents

| # | Section |
|---|---|
| 1 | [😰 The Problem](#-the-problem) |
| 2 | [✅ The Solution](#-the-solution) |
| 3 | [🏗️ Architecture](#️-architecture) |
| 4 | [☁️ AWS Services Used](#️-aws-services-used) |
| 5 | [⚙️ Configuration](#️-configuration-details) |
| 6 | [🛠️ Step-by-Step Setup](#️-step-by-step-setup) |
| 7 | [📸 Screenshots](#-screenshots) |
| 8 | [🚧 Difficulties & Solutions](#-difficulties--solutions) |
| 9 | [💡 Key Learnings](#-key-learnings) |

---

## 😰 The Problem

<div align="center">

```
╔═══════════════════════════════════════════════════════════════╗
║           THE SILENT RISK EVERY AWS USER IGNORES             ║
╠═══════════════════════════════════════════════════════════════╣
║                                                               ║
║  📅 Day 1   → You launch EC2 + RDS. Everything is working.   ║
║  📅 Day 7   → Deep in development. Code is building fast.    ║
║  📅 Day 14  → EC2 crashes. Database gets corrupted.          ║
║  📅 Day 14  → You search for backups...                       ║
║  📅 Day 14  → ❌ THERE ARE NONE.                              ║
║  📅 Day 14  → Weeks of work — gone forever.                  ║
║                                                               ║
╚═══════════════════════════════════════════════════════════════╝
```

</div>

AWS does **NOT** automatically back up your EC2 instances or RDS databases by default.
Most developers don't set it up — until they lose everything.

**30% of data loss incidents happen due to missing or broken backups.**

---

## ✅ The Solution

<div align="center">

| Feature | What It Does |
|:---:|:---|
| 🔁 **Automated Daily Backup** | Creates a recovery point for EC2 and RDS every single day automatically |
| 🔐 **KMS Encryption** | All backup data is encrypted at rest using AWS Key Management Service |
| 📅 **7-Day Retention** | AWS auto-manages recovery points — keeps 7 days, deletes older ones |
| 📊 **Real-Time Monitoring** | Watch every job: Running → Completed → Success in the Jobs dashboard |
| 💾 **Secure Vault Storage** | Backup vault cannot be accidentally deleted if recovery points exist |
| ♻️ **Fast Recovery** | If anything goes wrong, restore from any recovery point in minutes |

</div>

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                                                                      │
│   ┌─────────────────┐     triggers every 24h                        │
│   │  EventBridge    │ ──────────────────────────────────────┐        │
│   │   Scheduler     │                                       │        │
│   └─────────────────┘                                       ▼        │
│                                                  ┌──────────────────┐│
│                                                  │   Backup Plan    ││
│                                                  │ daily-backup-plan││
│                                                  │                  ││
│                                                  │ • Freq: Daily    ││
│                                                  │ • Retain: 7 days ││
│                                                  └────────┬─────────┘│
│                                                           │           │
│                                                           ▼           │
│                                                  ┌──────────────────┐│
│                                                  │   Backup Vault   ││
│                                                  │ my-backup-vault  ││
│                                                  │  (KMS Encrypted) ││
│                                                  └────────┬─────────┘│
│                                                           │           │
│                                          ┌────────────────┴──────┐   │
│                                          ▼                       ▼   │
│                                 ┌──────────────┐      ┌──────────────┐│
│                                 │     EC2      │      │     RDS      ││
│                                 │  project-2   │      │  database-1  ││
│                                 │  t3.micro    │      │    MySQL     ││
│                                 │  Running ✅  │      │ Available ✅  ││
│                                 └──────┬───────┘      └──────┬───────┘│
│                                        │                     │        │
│                                        └──────────┬──────────┘        │
│                                                   ▼                   │
│                                       ┌─────────────────────┐         │
│                                       │  Recovery Points    │         │
│                                       │  stored in vault    │         │
│                                       │  ✅ Completed        │         │
│                                       └─────────────────────┘         │
│                                                                        │
│              Region: ap-northeast-1 (Asia Pacific — Tokyo)            │
└──────────────────────────────────────────────────────────────────────┘
```

### 🔄 Data Flow

```
  You Push to AWS              AWS Backup Runs               You Are Protected
       │                             │                              │
       ▼                             ▼                              ▼
  ┌─────────┐   assigns to   ┌─────────────┐   creates    ┌──────────────────┐
  │  EC2 +  │ ─────────────► │ Backup Plan │ ───────────► │ Recovery Points  │
  │  RDS    │                │ (scheduled) │              │ in Vault (7 days)│
  └─────────┘                └─────────────┘              └──────────────────┘
```

---

## ☁️ AWS Services Used

<div align="center">

| Service | Role | Configured Value |
|:---:|:---|:---|
| 🟠 **AWS Backup** | Core service — manages all backup operations | Backup plan + vault created |
| 🟠 **Amazon EC2** | Compute resource being protected | project-2 · t3.micro · Running |
| 🟣 **Amazon RDS** | Database resource being protected | database-1 · MySQL Community |
| 🔑 **AWS IAM** | Access control — gives Backup permission to access resources | AWSBackupDefaultServiceRole |
| 🔒 **AWS KMS** | Encrypts all recovery points at rest | Default managed key (aws/backup) |
| ⏰ **EventBridge** | Automatically triggers the backup plan on schedule | Daily schedule |

</div>

---

## ⚙️ Configuration Details

<div align="center">

| Parameter | Value |
|:---|:---|
| **Project** | Project 2 — AWS Backup Plan |
| **Region** | `ap-northeast-1` (Asia Pacific — Tokyo) |
| **EC2 Instance** | `project-2` · `i-0ab241d1097418d99` · `t3.micro` |
| **EC2 Public IP** | `54.95.157.120` |
| **RDS Instance** | `database-1` · MySQL Community · Available |
| **Backup Vault** | `my-backup-vault` |
| **Backup Plan** | `daily-backup-plan` |
| **Backup Rule** | `daily-rule` |
| **Backup Frequency** | Daily |
| **Retention Period** | 7 Days |
| **Backup Window** | 5:00 AM UTC (default) |
| **Encryption** | AWS KMS — default managed key |
| **IAM Role** | `AWSBackupDefaultServiceRole` |
| **Job Status** | ✅ Completed — Success |
| **Last Backup** | March 20, 2026 · 18:06:22 UTC+05:30 |

</div>

---

## 🛠️ Step-by-Step Setup

### ✅ Step 1 — Verify EC2 Instance is Running

> **Path:** `AWS Console → Search "EC2" → EC2 → Instances`

- Confirm `project-2` shows **Instance State = Running** 🟢
- Confirm **Status check = 3/3 checks passed**
- Note the **Public IP: 54.95.157.120**

```
Instance ID   : i-0ab241d1097418d99
Instance type : t3.micro
State         : Running ✅
Status check  : 3/3 passed ✅
```

---

### ✅ Step 2 — Verify RDS Database is Available

> **Path:** `AWS Console → Search "RDS" → Aurora and RDS → Databases`

- Confirm `database-1` shows **Status = Available** 🟢
- Engine = **MySQL Community**

---

### ✅ Step 3 — Open AWS Backup

> **Path:** `AWS Console → Search "Backup" → AWS Backup`

- You land on the **AWS Backup Dashboard**
- Click **Vaults** in the left sidebar

---

### ✅ Step 4 — Create a Backup Vault

> **Path:** `Vaults → Create backup vault`

```yaml
Vault name     : my-backup-vault
Encryption key : Default AWS managed key (aws/backup)
```

- Click **Create backup vault** ✅
- Vault appears in list with **Vault type = Backup**

> ⚠️ **Important:** A vault cannot be deleted once it contains recovery points. Name it clearly.

---

### ✅ Step 5 — Create a Backup Plan

> **Path:** `Backup plans → Create backup plan → Build a new plan`

```yaml
Backup plan name  : daily-backup-plan
Rule name         : daily-rule
Backup vault      : my-backup-vault
Backup frequency  : Daily
Backup window     : Default (5:00 AM UTC)
Retention period  : 7 days
```

- Click **Create plan** ✅

---

### ✅ Step 6 — Assign Resources to the Plan

> **Path:** `Inside daily-backup-plan → Assign resources`

```yaml
Resource assignment name : ec2-rds-assignment
IAM role                 : Default role (AWSBackupDefaultServiceRole)
Resources                :
  - EC2 → project-2
  - RDS → database-1
```

- Click **Assign resources** ✅

> ⚠️ Resources will **NOT** appear in Protected Resources until the first backup job completes.

---

### ✅ Step 7 — Trigger On-Demand Backup (Test)

> **Path:** `Protected resources → database-1 → Create on-demand backup`

```yaml
Backup vault : my-backup-vault
```

- Click **Create on-demand backup**
- Go to **Jobs → Backup jobs** → watch: **Running ⏳ → Completed ✅**

---

### ✅ Step 8 — Verify Backup Job Completed

> **Path:** `Jobs → Backup jobs`

```
Status           : Completed ✅
Message category : Success ✅
Resource name    : database-1
```

---

### ✅ Step 9 — Verify Protected Resources

> **Path:** `Protected resources`

```
Resource name : database-1
Resource type : RDS
Last backup   : March 20, 2026 · 18:06:22 UTC+05:30 ✅
```

🎉 **Setup complete! Your EC2 and RDS are now protected automatically every day.**

---

## 📸 Screenshots

### 📌 Step 1 — EC2 Instance Running

> `project-2` (t3.micro) is live with all status checks passed | Public IP: 54.95.157.120

![](<Images/Screenshot (605).png>)

---

### 📌 Step 2 — RDS Databases Available

> Both `database-1` instances showing Available | MySQL Community engine

![](<Images/Screenshot (604).png>)

---

### 📌 Step 4 — Backup Vault Created

> `my-backup-vault` and `Default` vault — both KMS encrypted and active

![](<Images/Screenshot (603).png>)

---

### 📌 Step 7 — Backup Job Running

> Backup job for `database-1` actively running | Job ID: c00942be-4506-417d-b59e-8119647a390d

![](<Images/Screenshot (599).png>)

---

### 📌 Step 8 — Backup Job Completed

> Same job now Completed with Status: Success ✅

![](<Images/Screenshot (602).png>)

---

### 📌 Step 8 — Jobs Dashboard

> Full jobs dashboard showing completed backup | Resource: database-1

![](<Images/Screenshot (600).png>)

---

### 📌 Step 9 — Protected Resources Verified

> `database-1` appearing in Protected Resources with confirmed last backup timestamp

![](<Images/Screenshot (601).png>)

---

## 🚧 Difficulties & Solutions

<details>
<summary><b>❌ Problem 1 — EC2 Not Visible in Resource Assignment</b></summary>

| | |
|---|---|
| **Problem** | EC2 instance was not visible when trying to assign it to the backup plan |
| **Root Cause** | Wrong AWS region — console was on `us-east-1` but EC2 was in `ap-northeast-1` |
| **Solution** | Changed region to `ap-northeast-1` (Tokyo) from top-right dropdown. Instance appeared immediately. |
| **Lesson** | Always verify you are in the correct region before working with resources |

</details>

<details>
<summary><b>❌ Problem 2 — Vault Name Already Exists Error</b></summary>

| | |
|---|---|
| **Problem** | Error: `"Vault name already exists"` when creating `my-backup-vault` |
| **Root Cause** | A vault with the same name was created during an earlier incomplete attempt |
| **Solution** | Opened Vaults page → deleted the old empty vault → created fresh vault with same name |
| **Lesson** | Check for existing vaults before creating a new one |

</details>

<details>
<summary><b>❌ Problem 3 — Backup Job Stuck in Running</b></summary>

| | |
|---|---|
| **Problem** | Backup job stayed in `Running` status for several minutes |
| **Root Cause** | RDS backups take longer — a full database snapshot must be created including all data |
| **Solution** | Waited patiently and refreshed Jobs page every 60 seconds. Completed after ~6 minutes. |
| **Lesson** | RDS backup duration depends on database size. Patience is required. |

</details>

<details>
<summary><b>❌ Problem 4 — IAM AccessDeniedException</b></summary>

| | |
|---|---|
| **Problem** | First job failed: `AccessDeniedException — AWS Backup cannot create backup` |
| **Root Cause** | IAM role was missing `AWSBackupServiceRolePolicyForBackup` managed policy |
| **Solution** | IAM Console → Roles → `AWSBackupDefaultServiceRole` → Attach `AWSBackupServiceRolePolicyForBackup` + `AWSBackupServiceRolePolicyForRestores` |
| **Lesson** | Always check IAM permissions before running backup jobs |

</details>

<details>
<summary><b>❌ Problem 5 — Protected Resources Showed Empty</b></summary>

| | |
|---|---|
| **Problem** | Protected Resources showed `0 resources` even after assigning EC2 and RDS |
| **Root Cause** | AWS only lists a resource here after the FIRST backup job completes. Assignment alone is insufficient. |
| **Solution** | Triggered an on-demand backup for `database-1`. After completion, it appeared with correct timestamp. |
| **Lesson** | Protected Resources ≠ Assigned Resources. Run at least one backup first. |

</details>

---

## 💡 Key Learnings

```
┌─────────────────────────────────────────────────────────────────┐
│                      KEY TAKEAWAYS                              │
├─────────────────────────────────────────────────────────────────┤
│  ✅  AWS Backup centralises EC2 + RDS backup in one console     │
│  ✅  Vault = secure container, cannot delete with data inside   │
│  ✅  Protected Resources only populate after first job done     │
│  ✅  IAM role must have correct policies before backup runs     │
│  ✅  RDS backups take longer than EC2 — full snapshot needed    │
│  ✅  On-demand backup lets you test without waiting for schedule │
│  ✅  KMS encryption is automatic — all data encrypted at rest   │
│  ✅  7-day retention = AWS auto-deletes old recovery points     │
│  ✅  Region must match between resources and backup vault       │
│  ✅  Recovery points can restore entire instances in minutes    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔮 What's Next

- [ ] 🌍 Add cross-region backup copy for disaster recovery
- [ ] 📲 Configure SNS notifications for backup completion alerts
- [ ] 📊 Enable AWS Backup Audit Manager for compliance reports
- [ ] 🏗️ Automate backup setup using Terraform (Infrastructure as Code)
- [ ] 🔁 Set up restore testing to verify recovery points actually work
- [ ] 📦 Add S3 buckets to the backup plan

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1D9E75,100:185FA5&height=120&section=footer&animation=fadeIn"/>

**Arkan Tandel · 28 July Batch**

[![EC2](https://img.shields.io/badge/EC2-Protected_✅-success?style=flat-square)](.)
[![RDS](https://img.shields.io/badge/RDS-Protected_✅-success?style=flat-square)](.)
[![Vault](https://img.shields.io/badge/Vault-Encrypted_🔐-blue?style=flat-square)](.)
[![Backup](https://img.shields.io/badge/Backup-Automated_⚡-orange?style=flat-square)](.)

*"Your data is your most valuable asset. Protect it before you need to restore it."*

</div>
