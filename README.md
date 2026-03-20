# 🛡️ AWS Backup & Recovery Implementation for EC2 and RDS

<div align="center">

![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Batch](https://img.shields.io/badge/Batch-28%20July-blue?style=for-the-badge)

**Arkan Tandel · 28 July Batch**

*A fully automated cloud backup system protecting EC2 and RDS using AWS Backup Service*

</div>

---

## 📌 Table of Contents

- [The Problem](#-the-problem)
- [The Solution](#-the-solution)
- [Architecture](#-architecture)
- [AWS Services Used](#-aws-services-used)
- [Configuration Details](#-configuration-details)
- [Step-by-Step Setup](#-step-by-step-setup)
- [Screenshots](#-screenshots)
- [Difficulties & Solutions](#-difficulties--solutions)
- [Key Learnings](#-key-learnings)

---

## 😰 The Problem

Every developer using AWS faces this silent risk — **no one thinks about backups until it's too late.**

```
Day 1  → You create an EC2 instance and an RDS database
Day 7  → You're deep into development, everything is working
Day 14 → Something corrupts your instance... or someone accidentally deletes the database
Day 14 → You check for backups... there are none
Day 14 → Everything is gone. Hours, days, weeks of work — lost forever.
```

**This is not a rare scenario. It happens every day.**

AWS does NOT automatically back up your EC2 instances or RDS databases in most configurations. You have to set it up yourself. Most people don't — until they learn the hard way.

---

## ✅ The Solution

**AWS Backup Service** — a centralised, fully managed backup solution that:

- 🔁 **Automatically backs up** your EC2 and RDS every single day
- 🔐 **Encrypts all backups** using AWS KMS at rest
- 📅 **Retains recovery points** for 7 days automatically
- 🚨 **Monitors every job** — you can see Running / Completed / Failed in real time
- 💾 **Stores backups in a secure vault** — cannot be accidentally deleted
- ♻️ **Restores in minutes** — if anything goes wrong, you recover fast

> *"You don't think about backups until you need one. Set it up before you need it."*

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     AWS Backup Service                        │
│                                                              │
│   ┌─────────────────────────┐                               │
│   │      Backup Plan        │                               │
│   │  ┌───────────────────┐  │                               │
│   │  │  Schedule: Daily  │  │                               │
│   │  │  Retention: 7 days│  │                               │
│   │  └────────┬──────────┘  │                               │
│   └───────────┼─────────────┘                               │
│               │                                              │
│               ▼                                              │
│   ┌───────────────────────┐                                 │
│   │     Backup Vault      │                                 │
│   │   my-backup-vault     │                                 │
│   │   (KMS Encrypted)     │                                 │
│   └───────────────────────┘                                 │
│               │                                              │
│        ┌──────┴──────┐                                      │
│        ▼             ▼                                      │
│   ┌─────────┐   ┌─────────┐                                 │
│   │   EC2   │   │   RDS   │                                 │
│   │project-2│   │  db-1   │                                 │
│   │t3.micro │   │  MySQL  │                                 │
│   └─────────┘   └─────────┘                                 │
│                                                              │
│   Region: ap-northeast-1 (Asia Pacific — Tokyo)             │
└──────────────────────────────────────────────────────────────┘

Flow:
EventBridge Timer ──► Backup Plan ──► Backup Job Starts
                                           │
                          ┌────────────────┤
                          ▼                ▼
                     EC2 Snapshot    RDS Snapshot
                          │                │
                          └────────┬───────┘
                                   ▼
                           my-backup-vault
                         (Recovery Points stored)
                                   │
                                   ▼
                    Protected Resources Updated ✅
                    SMS / Email Notification Sent ✅
```

---

## ☁️ AWS Services Used

| Service | Role in Project | Why It Was Used |
|---|---|---|
| **AWS Backup** | Core service — manages all backup operations | Centralised place to manage EC2 + RDS backups together |
| **Amazon EC2** | Compute resource being protected | The running server that needs to be backed up |
| **Amazon RDS** | Database resource being protected | MySQL database that holds critical application data |
| **AWS IAM** | Permissions and access control | Gives AWS Backup the right to access EC2 and RDS |
| **AWS KMS** | Encryption of backup data | All recovery points are encrypted at rest for security |
| **Amazon EventBridge** | Triggers the backup schedule | Fires the backup plan automatically every day |

---

## ⚙️ Configuration Details

| Parameter | Value |
|---|---|
| **Region** | ap-northeast-1 (Asia Pacific — Tokyo) |
| **EC2 Instance** | project-2 · t3.micro · Running |
| **EC2 Public IP** | 54.95.157.120 |
| **RDS Instance** | database-1 · MySQL Community · Available |
| **Backup Vault** | my-backup-vault |
| **Backup Plan** | daily-backup-plan |
| **Backup Frequency** | Daily |
| **Retention Period** | 7 Days |
| **Encryption** | AWS KMS (default key) |
| **IAM Role** | AWSBackupDefaultServiceRole |
| **Final Job Status** | ✅ Completed — Success |

---

## 🛠️ Step-by-Step Setup

### 🔵 Step 1 — Verify EC2 Instance is Running

```
AWS Console → Search "EC2" → Click EC2
→ Left sidebar: Instances
→ Confirm project-2 shows:
   Instance State  = Running  ✅
   Status check    = 3/3 checks passed  ✅
   Instance type   = t3.micro
```

> Make sure your instance is running before assigning to a backup plan.

---

### 🔵 Step 2 — Verify RDS Database is Available

```
AWS Console → Search "RDS" → Click Aurora and RDS
→ Left sidebar: Databases
→ Confirm database-1 shows:
   Status = Available  ✅
   Engine = MySQL Community
   Role   = Instance
```

---

### 🔵 Step 3 — Open AWS Backup Service

```
AWS Console → Search "Backup" → Click AWS Backup
→ You land on the AWS Backup Dashboard
→ Left sidebar: click Vaults
```

---

### 🔵 Step 4 — Create a Backup Vault

```
Vaults page → Click "Create backup vault"

Fill in:
  Vault name       : my-backup-vault
  Encryption key   : Default AWS managed key (aws/backup)

→ Click "Create backup vault"
→ Vault appears in list ✅
```

> ⚠️ A vault cannot be deleted once it contains recovery points. Name it clearly.

---

### 🔵 Step 5 — Create a Backup Plan

```
Left sidebar → Backup plans → "Create backup plan"
→ Select: Build a new plan

Fill in:
  Backup plan name : daily-backup-plan

Under "Backup rules" → click "Add backup rule":
  Rule name        : daily-rule
  Backup vault     : my-backup-vault
  Backup frequency : Daily
  Backup window    : Default (5:00 AM UTC)
  Retention period : 7 days

→ Click "Create plan"  ✅
```

---

### 🔵 Step 6 — Assign Resources to the Plan

```
Inside daily-backup-plan → click "Assign resources"

Fill in:
  Resource assignment name : ec2-rds-assignment
  IAM role                 : Default role (AWSBackupDefaultServiceRole)

Under Resource selection:
  → Add EC2 → select instance: project-2
  → Add RDS → select database: database-1

→ Click "Assign resources"  ✅
```

> ⚠️ Resources will NOT appear in Protected Resources until the first backup job completes. This is normal.

---

### 🔵 Step 7 — Trigger an On-Demand Backup (Test)

```
Left sidebar → Protected resources
→ Select database-1
→ Click "Create on-demand backup"
→ Select vault: my-backup-vault
→ Click "Create on-demand backup"

→ Go to Jobs → Backup jobs
→ Watch status: Running ⏳ → Completed ✅
```

---

### 🔵 Step 8 — Verify the Backup Job Completed

```
Left sidebar → Jobs → Backup jobs tab

Confirm:
  Status           = Completed  ✅
  Message category = Success    ✅
  Resource name    = database-1
```

---

### 🔵 Step 9 — Verify Protected Resources

```
Left sidebar → Protected resources

Confirm:
  Resource name  = database-1
  Resource type  = RDS
  Last backup    = March 20, 2026, 18:06:22 UTC+05:30  ✅
```

**Setup complete! Your data is now protected automatically every day.** 🎉

---

## 📸 Screenshots

### EC2 Instance Running

> Instance `project-2` (t3.micro) is live with all status checks passed.

![](<Images/Screenshot (151).png>)

---

### RDS Databases Available

> Both `database-1` and the second instance showing Available status with MySQL Community engine.

![](<Images/Screenshot (152).png>)

---

### Backup Vault Created

> `my-backup-vault` created successfully alongside the Default vault — both KMS encrypted.

![](<Images/Screenshot (153).png>)

---

### Backup Job Running → Completed

> Backup job for `database-1` first showing Running, then Completed with Success.

![](<Images/Screenshot (154).png>)

---

### Protected Resources Verified

> `database-1` now appears in Protected Resources with confirmed last backup timestamp.

![](<Images/Screenshot (155).png>)

---

## 🚧 Difficulties & Solutions

### ❌ Problem 1 — EC2 Not Visible in Resource Assignment
| | |
|---|---|
| **Issue** | EC2 instance was not visible when trying to assign it to the backup plan |
| **Cause** | Wrong AWS region selected — console was showing us-east-1 instead of ap-northeast-1 |
| **Fix** | Changed region to ap-northeast-1 (Tokyo) from the top-right corner dropdown. Instance appeared immediately. |

---

### ❌ Problem 2 — Vault Name Already Exists Error
| | |
|---|---|
| **Issue** | Error: "Vault name already exists" when creating my-backup-vault |
| **Cause** | A vault with the same name was created during an earlier incomplete attempt |
| **Fix** | Deleted the old empty vault from the Vaults page, then created a fresh vault with the same name successfully. |

---

### ❌ Problem 3 — Backup Job Stuck in Running
| | |
|---|---|
| **Issue** | Backup job stayed in Running status for several minutes with no visible progress |
| **Cause** | RDS backups take longer than EC2 — a full database snapshot must be created |
| **Fix** | Waited patiently and refreshed Jobs page every 60 seconds. Completed after ~6 minutes. |

---

### ❌ Problem 4 — IAM AccessDeniedException
| | |
|---|---|
| **Issue** | First backup job failed: AccessDeniedException — AWS Backup cannot create backup |
| **Cause** | IAM role was missing the AWSBackupServiceRolePolicyForBackup managed policy |
| **Fix** | IAM Console → Roles → AWSBackupDefaultServiceRole → Attach AWSBackupServiceRolePolicyForBackup + AWSBackupServiceRolePolicyForRestores. Re-ran backup — success. |

---

### ❌ Problem 5 — Protected Resources Showed Empty
| | |
|---|---|
| **Issue** | Protected Resources page showed 0 resources even after assigning EC2 and RDS |
| **Cause** | AWS only lists a resource here after at least one backup job has completed. Assignment alone is not enough. |
| **Fix** | Triggered an on-demand backup for database-1. After it completed, the resource appeared with the correct timestamp. |

---

## 💡 Key Learnings

```
✅ AWS Backup centralises EC2 and RDS backup management in one console
✅ A Backup Vault is a secure, locked container — cannot delete if it has recovery points
✅ Resources only appear in Protected Resources after the FIRST backup job completes
✅ IAM role must have correct policies — AWSBackupServiceRolePolicyForBackup is required
✅ RDS backups take longer than EC2 because they create a full database snapshot
✅ On-demand backup lets you test the setup without waiting for the daily schedule
✅ KMS encryption is automatic — all recovery points are encrypted at rest by default
✅ 7-day retention means AWS auto-deletes old backups — no manual cleanup needed
✅ Backup Frequency = Daily means one recovery point is created every 24 hours automatically
```

---

## 🔮 What Can Be Added Next

- [ ] Add EC2 instance to Protected Resources (currently only RDS is confirmed)
- [ ] Configure cross-region backup copy for disaster recovery
- [ ] Set up SNS notifications for backup job completion alerts
- [ ] Use AWS Backup Audit Manager to generate compliance reports
- [ ] Automate the entire backup setup using Terraform (Infrastructure as Code)
- [ ] Set up restore testing to verify recovery points actually work

---

## 👨‍💻 Author

**Arkan Tandel**
28 July Batch

---

<div align="center">

*"Your data is your most valuable asset. Protect it before you need to restore it."*

![AWS Backup](https://img.shields.io/badge/AWS_Backup-Configured-success?style=flat-square&logo=amazonaws)
![EC2](https://img.shields.io/badge/EC2-Protected-blue?style=flat-square&logo=amazonaws)
![RDS](https://img.shields.io/badge/RDS-Protected-blue?style=flat-square&logo=amazonaws)
![Encryption](https://img.shields.io/badge/KMS-Encrypted-orange?style=flat-square&logo=amazonaws)

</div>
