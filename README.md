# aws-usage-reporting

# AWS Usage Reporting & GitHub Access Automation

This repo contains two automation scripts:

1. **aws_usage_report.sh**  
   Collects a daily AWS usage snapshot (EC2, S3, IAM, optional Cost Explorer), and writes clean CSVs + a summary Markdown file.

2. **github_access_audit.sh**  
   Audits GitHub collaborators across repositories and (optionally) revokes access. Defaults to **dry-run** for safety.

---

## Features

### AWS Usage Reporting
- EC2: running instances inventory (ID, Name, type, state, AZ, IPs, launch time)
- S3: per-bucket object counts and total size (simple scan method)
- IAM: users and their access keys (ID + status)
- Cost Explorer: optional **daily** cost (if CE is enabled)
- Output: CSVs in `reports/YYYY-MM-DD/` plus a `aws_summary.md`

### GitHub Access Automation
- List collaborators per repo for a user/org
- Export CSV of repo → collaborator → permission
- Revoke collaborator access (per repo) **with `--confirm`**
- Uses `gh` CLI when available; falls back to `curl` + PAT

---

## Prerequisites

- **AWS**
  - AWS CLI v2 (`aws --version`)
  - An IAM principal with read-only permissions:
    - `AmazonEC2ReadOnlyAccess`
    - `AmazonS3ReadOnlyAccess`
    - `IAMReadOnlyAccess`
    - (Optional for cost) `ce:GetCostAndUsage`  
  - Configure credentials: `aws configure` (or use a named profile)

- **GitHub**
  - Either:
    - `gh` CLI (`gh auth login`)  
    - **OR** a GitHub Personal Access Token (classic) with `repo` scope for user repos (and `read:org`, `admin:org` if auditing an org). Store it in `.env` as `GITHUB_TOKEN`.

- **Utilities**
  - `bash`, `date`, `awk`, `grep`
  - `jq` (recommended; the scripts work without it, but formatting is cleaner with `jq`)

---

## Quick Start

```bash
# 1) Clone and enter the repo
git clone https://github.com/<your-username>/aws-usage-reporting.git
cd aws-usage-reporting

# 2) Create your config
cp .env.example .env
# Edit .env and set AWS_REGION, AWS_PROFILE (optional), and GH_OWNER (org or username)

# 3) (Optional) install helpers on Ubuntu/Debian
bash setup.sh

# 4) Test AWS script
bash aws_usage_report.sh

# 5) Test GitHub audit
bash github_access_audit.sh --list --owner "$GH_OWNER"  # uses gh if available

# 6) Commit and push your repo
git add .
git commit -m "Add AWS usage reporting & GitHub access automation"
git push -u origin main
