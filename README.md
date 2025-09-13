# 🚀 GitHub Enterprise Migration System

> **Self-Service, Batch-Powered Repository Migrations at Scale**

[![Migration Ready](https://img.shields.io/badge/Migration-Ready-success)](https://github.com)
[![GEI Powered](https://img.shields.io/badge/Powered%20by-GEI-blue)](https://docs.github.com/en/migrations/using-github-enterprise-importer)
[![Batch Processing](https://img.shields.io/badge/Batch-Processing-orange)](https://github.com)

Transform your GitHub migration experience with our automated, issue-driven system that makes large-scale repository transfers simple, trackable, and reliable. Perfect for organizations moving from GitHub Enterprise Server (GHES) or between GitHub Enterprise Cloud (GHEC) organizations.

## ✨ Key Features

| Feature | Description |
|---------|------------|
| 📋 **Issue-Driven Workflow** | Create an issue, list your repos, and let automation handle the rest |
| 📦 **Smart Batching** | Automatically splits large migrations into manageable chunks (250 repos/batch) |
| 🔄 **Sequential Processing** | Reliable batch-by-batch execution with progress tracking |
| 🧪 **Dry-Run Support** | Test migrations safely before production |
| 🔒 **Production Mode** | Secure migration with source repository locking |
| 💾 **Complete Data Transfer** | Includes LFS, packages, and releases |
| 💬 **Real-Time Updates** | Progress notifications via issue comments |
| 🛑 **Cancellation Support** | Stop migrations gracefully with `/cancel-migration` |
| 👥 **User Mapping** | Automatic mannequin-to-user account mapping |
| 🔧 **Self-Service** | Empower teams to run their own migrations |

## 🎯 Quick Start

### 📋 Prerequisites

- ✅ GitHub Enterprise Cloud organization (target)
- ✅ Admin access to source and target
- ✅ Personal Access Tokens (PATs)
- ✅ Storage backend (Azure Blob or AWS S3) - optional but recommended
- ✅ Self-hosted runners (for batch processing)

### 🔧 Setup Instructions

#### 1️⃣ **Fork & Configure Repository**

```bash
# Fork this repository to your organization
# Clone to your local machine
git clone https://github.com/YOUR-ORG/migration-system.git
cd migration-system
```

#### 2️⃣ **Configure Secrets** 🔐

Navigate to **Settings** → **Secrets and variables** → **Actions**

| Secret | Description | Required |
|--------|-------------|----------|
| `TARGET_ADMIN_TOKEN` | PAT for target org with `repo`, `admin:org`, `workflow` scopes | ✅ |
| `SOURCE_ADMIN_TOKEN` | PAT for source with `repo`, `admin:org` scopes | ✅ |
| `AZURE_STORAGE_CONNECTION_STRING` | Azure storage connection string | Choose one |
| `AWS_ACCESS_KEY_ID` | AWS access key | storage option |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key | (optional) |

#### 3️⃣ **Configure Variables** ⚙️

Navigate to **Settings** → **Secrets and variables** → **Actions** → **Variables**

| Variable | Description | Example |
|----------|-------------|---------|
| `TARGET_ORGANIZATION` | Target GitHub org name | `my-company` |
| `SOURCE_ORGANIZATION` | Source org (for releases) | `old-company` |
| `INSTALL_PREREQS` | Auto-install dependencies | `true` |
| `AWS_REGION` | AWS region (if using S3) | `us-east-1` |
| `AWS_BUCKET_NAME` | S3 bucket name (if using S3) | `migrations` |

#### 4️⃣ **Optional: Prepare Support Files** 📁

<details>
<summary>📦 For repositories with special requirements</summary>

Create these CSV files in your repository root:

**`lfs.csv`** - Repositories requiring LFS migration
```csv
repository
repo-with-lfs
another-lfs-repo
```

**`packages.csv`** - Repositories with packages
```csv
repository
repo-with-packages
```

**`user-mappings-gei.csv`** - Map mannequins to real users
```csv
source,target
old-username,new-username
```

**Note**: LFS/packages/releases migrations are scaffolded but need uncommenting in workflow files for production use.

</details>

## 🚀 Running Your Migration

### Step 1: Create Migration Issue 📝

1. Go to **Issues** → **New Issue**
2. Select **"🚀 Migrate Repositories to GitHub Enterprise Cloud"** template
3. Add your repositories:

```markdown
<details>
<summary>📋 Repository List (click to expand)</summary>

https://github.example.com/org/repo1
https://github.example.com/org/repo2
https://github.example.com/org/repo3
<!-- Add up to 1000 repositories -->

</details>
```

4. Select visibility: `Private`, `Internal`, or `Mirror`
5. Submit the issue

### Step 2: Review Automated Analysis 🔍

The system will automatically comment with:
- ✅ Number of repositories detected
- 📦 Batch breakdown (250 repos per batch)
- 🎯 Target organization confirmation
- 👁️ Visibility settings

### Step 3: Start Migration 🎬

Add a comment to your issue:

#### 🧪 **Test First (Recommended)**
```
/run-dry-run-migration
```
- ✅ Safe, non-destructive test
- ✅ Creates test repos (GEI handles naming)
- ✅ Source remains unlocked

#### 🚀 **Production Migration**
```
/run-production-migration
```
- ⚠️ Locks source repositories
- ✅ Creates production repos
- ✅ Run after successful dry-run

### Step 4: Monitor Progress 📊

Watch real-time updates in your issue:

```
🚀 Batch 1 of 10 Starting
📦 Repositories in this batch: 250
🔄 Migration type: dry-run
🎯 Target organization: `my-company`

➡️ Track batch progress in Actions tab

✅ Batch 1 of 10 Complete
🎉 Status: SUCCESS
⏱️ Duration: 250 minutes

📥 Next: Preparing batch 2...
```

### Step 5: Post-Migration 🎉

After successful migration:
- 📋 Review the final report
- 🔍 Verify all repositories
- 👥 Update team access
- 🔧 Configure CI/CD
- 🧹 Clean up dry-run repos with `/delete-dry-run`

## 🎛️ Advanced Configuration

### ⚙️ Customize Batch Size

Edit workflow configuration for your needs:

```yaml
# .github/workflows/trigger.yml
BATCH_SIZE: 250  # Max 256 (GitHub Actions matrix limit)
```

### 🔄 Parallel Processing

Control concurrent migrations per batch:

```yaml
# .github/workflows/batch-processor.yml
max-parallel: 10  # Max 10 (GEI concurrent limit)
```

### ⏱️ Timeout Configuration

For large repositories:

```yaml
timeout-minutes: 50400  # 35 days (GitHub Actions max)
```

### 🏃 Runner Configuration

```yaml
# .github/workflows/batch-processor.yml
runs-on: self-hosted  # Or ubuntu-latest for GitHub-hosted
```

## 🛠️ Troubleshooting

### 🚨 Common Issues & Solutions

<details>
<summary>🔴 Migration Won't Start</summary>

**Checklist:**
- ✅ Verify PAT permissions
- ✅ Check secret names match exactly
- ✅ Ensure issue has `migration` and `batch` labels
- ✅ Confirm user has write access

</details>

<details>
<summary>🟡 Batch Processing Stops</summary>

**Steps:**
1. Check Actions tab for error details
2. Verify runner availability (if self-hosted)
3. Check API rate limits
4. Re-run failed batch from Actions

</details>

<details>
<summary>🔵 Missing LFS/Packages</summary>

**Note:** These features need activation:
1. Edit workflow files (lfs.yml, packages.yml, releases.yml)
2. Uncomment the migration commands
3. Ensure CSV files exist with repo names

</details>

### 🛑 Emergency Controls

**Cancel in-progress migration:**
```
/cancel-migration
```

**Clean up test repositories:**
```
/delete-dry-run
```

**Re-run specific batch:**
1. Go to Actions tab
2. Find failed batch workflow
3. Click "Re-run failed jobs"

## 📊 Monitoring Dashboard

### Key Metrics Location

| Metric | Where to Find |
|--------|--------------|
| 📈 Overall Progress | Issue comments |
| 🔍 Detailed Logs | Actions tab → Workflow runs |
| 📋 Batch Results | Workflow summaries |
| 🗂️ Migration Artifacts | Configured storage (Azure/S3) |

## 🔒 Security Best Practices

### 🛡️ Token Security
- 🔑 Use repository secrets only
- 🔄 Rotate tokens post-migration
- 🎯 Minimum required permissions

### 👥 Access Control
- 🚪 Restrict issue creation permissions
- 🔒 Limit repository access during migration
- 👁️ Review permissions post-migration

### 💾 Data Handling
- 🗄️ Temporary storage in configured backend
- 🧹 Clean up after successful migration

## 📞 Support

Need help? 
1. 📖 Check [GEI documentation](https://docs.github.com/en/migrations/using-github-enterprise-importer)
2. 🔍 Review workflow logs
3. 💬 Open an issue with:
   - Error messages
   - Workflow run links
   - Configuration (exclude secrets!)

## 🏗️ Architecture Overview

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#1f2937', 'primaryBorderColor':'#4f46e5', 'primaryTextColor':'#fff', 'lineColor':'#6366f1', 'secondaryColor':'#7c3aed', 'tertiaryColor':'#0891b2', 'background':'#f8fafc', 'mainBkg':'#ffffff', 'secondBkg':'#f1f5f9', 'tertiaryBkg':'#e0e7ff', 'textColor':'#1e293b', 'fontSize':'16px'}}}%%
graph TB
    subgraph INIT["<b>📝 Migration Initiation</b>"]
        A["👤 User Creates Issue<br/><i>Lists repositories</i>"]:::userAction --> B["📋 Issue Template<br/><i>Structured format</i>"]:::template
        B --> C["⚙️ Prepare Workflow<br/><i>Auto-triggered</i>"]:::workflow
        C --> D["🔍 Parse & Validate<br/><i>Extract repo list</i>"]:::process
        D --> E["💬 Post Instructions<br/><i>Next steps guide</i>"]:::output
    end
    
    subgraph TRIGGER["<b>🎯 Migration Trigger</b>"]
        E --> F["💭 User Comments<br/><i>/run-command</i>"]:::userAction
        F --> G{"🤔 Command Type?"}:::decision
        G -->|"🧪 /run-dry-run"| H["Test Mode<br/><i>Safe validation</i>"]:::dryrun
        G -->|"🚀 /run-production"| I["Production Mode<br/><i>Live migration</i>"]:::production
    end
    
    subgraph ORCHESTRATE["<b>🔄 Orchestration Layer</b>"]
        H --> J["🎭 Orchestrator<br/><i>Central controller</i>"]:::orchestrator
        I --> J
        J --> K["📦 Create Batches<br/><i>250 repos each</i>"]:::batch
        K --> L["🚂 Sequential Dispatch<br/><i>One batch at a time</i>"]:::dispatch
    end
    
    subgraph PROCESS["<b>⚡ Batch Processing</b>"]
        L --> M["🏭 Batch Processor<br/><i>Self-hosted runner</i>"]:::processor
        M --> N["🔀 Parallel Migration<br/><i>10 repos concurrent</i>"]:::parallel
        N --> O["🛠️ GEI CLI<br/><i>Core migration tool</i>"]:::gei
        O --> P{"📎 Extra Data?"}:::decision
        P -->|"✅ Yes"| Q["📤 Migrate LFS<br/>Packages, Releases"]:::extra
        P -->|"❌ No"| R["📊 Update Status<br/><i>Track progress</i>"]:::status
        Q --> R
    end
    
    subgraph REPORT["<b>📊 Reporting</b>"]
        R --> S["📈 Batch Summary<br/><i>Success metrics</i>"]:::summary
        S --> T["💬 Update Issue<br/><i>Progress comment</i>"]:::comment
        T --> U{"🔄 More Batches?"}:::decision
        U -->|"✅ Yes"| L
        U -->|"🎉 No"| V["🏁 Final Report<br/><i>Complete summary</i>"]:::final
    end
    
    classDef userAction fill:#4f46e5,stroke:#312e81,stroke-width:3px,color:#fff
    classDef template fill:#06b6d4,stroke:#0891b2,stroke-width:2px,color:#fff
    classDef workflow fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#fff
    classDef process fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#fff
    classDef output fill:#10b981,stroke:#059669,stroke-width:2px,color:#fff
    classDef decision fill:#ef4444,stroke:#dc2626,stroke-width:3px,color:#fff
    classDef dryrun fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#fff
    classDef production fill:#f97316,stroke:#ea580c,stroke-width:2px,color:#fff
    classDef orchestrator fill:#a855f7,stroke:#9333ea,stroke-width:3px,color:#fff
    classDef batch fill:#14b8a6,stroke:#0d9488,stroke-width:2px,color:#fff
    classDef dispatch fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#fff
    classDef processor fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#fff
    classDef parallel fill:#f472b6,stroke:#ec4899,stroke-width:2px,color:#fff
    classDef gei fill:#0ea5e9,stroke:#0284c7,stroke-width:3px,color:#fff
    classDef extra fill:#fbbf24,stroke:#f59e0b,stroke-width:2px,color:#000
    classDef status fill:#34d399,stroke:#10b981,stroke-width:2px,color:#000
    classDef summary fill:#818cf8,stroke:#6366f1,stroke-width:2px,color:#fff
    classDef comment fill:#c084fc,stroke:#a855f7,stroke-width:2px,color:#fff
    classDef final fill:#10b981,stroke:#059669,stroke-width:4px,color:#fff
    
    style INIT fill:#f0f9ff,stroke:#3b82f6,stroke-width:2px
    style TRIGGER fill:#fef3c7,stroke:#f59e0b,stroke-width:2px
    style ORCHESTRATE fill:#ede9fe,stroke:#8b5cf6,stroke-width:2px
    style PROCESS fill:#fee2e2,stroke:#ef4444,stroke-width:2px
    style REPORT fill:#ecfdf5,stroke:#10b981,stroke-width:2px
```

## 🚦 Migration Flow

```mermaid
%%{init: {'theme':'base', 'themeVariables': { 'primaryColor':'#1f2937', 'primaryBorderColor':'#4f46e5', 'primaryTextColor':'#fff', 'lineColor':'#6366f1', 'secondaryColor':'#7c3aed', 'background':'#ffffff', 'actorBkg':'#4f46e5', 'actorBorder':'#312e81', 'actorTextColor':'#ffffff', 'actorLineColor':'#94a3b8', 'signalColor':'#1e293b', 'signalTextColor':'#1e293b', 'labelBoxBkgColor':'#f1f5f9', 'labelBoxBorderColor':'#cbd5e1', 'labelTextColor':'#1e293b', 'loopTextColor':'#7c3aed', 'noteBkgColor':'#fef3c7', 'noteBorderColor':'#f59e0b', 'noteTextColor':'#92400e', 'activationBorderColor':'#6366f1', 'activationBkgColor':'#e0e7ff', 'sequenceNumberColor':'#ffffff'}}}%%
sequenceDiagram
    participant U as 👤 User
    participant I as 📝 Issue
    participant O as 🎭 Orch
    participant B as 🏭 Batch
    participant G as 🛠️ GEI
    participant T as 🎯 Target
    
    rect rgb(239, 246, 255)
        U->>+I: Create issue + repos
        I-->>-U: ✅ Ready
    end
    
    rect rgb(254, 243, 199)
        U->>I: /run-dry-run
        I->>+O: Start
        O->>O: 📦 Batch (250 each)
    end
    
    rect rgb(237, 233, 254)
        loop Each Batch
            O->>+B: Dispatch
            
            loop Parallel x10
                B->>G: Migrate
                G->>T: Transfer
                T-->>B: ✅
            end
            
            B-->>-O: Done
            O->>I: 📊 Update
        end
    end
    
    rect rgb(236, 253, 245)
        O-->>-I: 🏁 Complete
        I-->>U: 🎉 Success!
    end
```

## ⏱️ Performance at Scale

| Scale | Repositories | Batches | Est. Time | Parallel Factor |
|-------|-------------|---------|-----------|-----------------|
| Small | 10 | 1 | ~10 min | 10x |
| Medium | 250 | 1 | ~250 min | 10x |
| Large | 1000 | 4 | ~17 hours | 10x |
| Enterprise | 8000 | 32 | ~5.5 days | 10x |

**Time Formula**: `(repos_per_batch ÷ 10 parallel) × 10 min/repo + 2 min overhead`

---

<div align="center">

**🎯 Built for Scale** | **🔧 Self-Service Ready** | **📊 Full Visibility**

Made with ❤️ for GitHub Enterprise migrations

[Documentation](https://docs.github.com/en/migrations) | [Support](https://support.github.com) | [GEI CLI](https://github.com/github/gh-gei)

</div>