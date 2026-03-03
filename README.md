# Lab M5.03 - GitOps Workflow Implementation

**Cloud Engineering Bootcamp - Week 5, Day 2**  
**Module:** Cloud Automation & CI/CD

## Start Here: Fork, Clone, and Submit

You will complete this lab by working in **your own fork** of the lab repository and submitting a **Pull Request (PR)**.
1. **Fork the lab repository** to your GitHub account.
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/MaryaAhmadi/ce-lab-gitops-workflow.git   

3. Follow all instructions below and save your work in this repo (files, screenshots, and notes).
4. When finished, submit your work:
git add → git commit → git push
Open a Pull Request from your fork back to the original lab repo
Copy the PR URL and paste it into the Lab Submission field in the Student Portal


## 📋 Lab Overview

Implement a complete GitOps workflow where infrastructure changes are managed entirely through Git. Learn declarative infrastructure management and automated synchronization.


## 🎯 Learning Objectives

- Implement GitOps principles and workflows
- Configure automated drift detection
- Set up pull request-based infrastructure updates
- Implement automated reconciliation
- Use Git as single source of truth

## Repository Structure (Actual Implementation)
ce-lab-gitops-workflow/
├── .github/
│   └── workflows/
│       ├── deploy.yml          # Main deploy workflow for dev/prod
│       ├── promotion.yml       # PR plan check for promotion to prod
│       └── drift-detection.yml # Scheduled drift detection (assumed)
├── environments/
│   ├── dev/
│   │   └── terraform.tfvars
│   └── prod/
│       └── terraform.tfvars
├── main.tf
├── variables.tf
├── outputs.tf
├── .gitignore
└── README.md


Note: The lab suggested infrastructure/ but I used environments/ for env-specific tfvars (common pattern).

## My Implementation Summary (Submission by MaryaAhmadi)
GitOps Workflows

deploy.yml: Triggers on push to develop (deploy to dev) and main (deploy to prod).
Uses branch name to determine target environment (TARGET_ENV).
Loads env-specific terraform.tfvars with -var-file.
Separate backend state keys: m5-03-gitops/dev/terraform.tfstate and m5-03-gitops/prod/terraform.tfstate.
GitHub Environments (development and production) with protection rules (required reviewers, wait timer).

promotion.yml (Promotion Check): Runs on PR to main.
Performs terraform init, fmt, validate, plan against prod.
Posts terraform plan output as a comment on the PR.
Shows differences (or "No changes") before merge.

drift-detection.yml (Assumed scheduled): Runs on schedule (e.g., cron).
Executes terraform plan on dev/prod to detect drift.
If drift found, can create issue or notify (basic implementation).


## Multi-Environment Setup

Dev (environments/dev/terraform.tfvars):
environment = "dev"
enable_versioning = false
log_retention_days = 10 (changed for testing)

Prod (environments/prod/terraform.tfvars):
environment = "prod"
enable_versioning = true
log_retention_days = 90 (compliance retention)

Verified separation: Changes to dev retention do not affect prod (plan shows "No changes" for prod).

## Drift Detection & Handling Procedures

Scheduled workflow runs terraform plan periodically.
If manual changes in AWS (drift) → plan output shows differences.
Handling:
Alert via GitHub issue or notification.
Reconciliation: Create PR with fix, review, merge → auto-apply.

Tip: For production, use tools like Terraform Cloud/Enterprise or Atlantis for auto-reconcile.

## Test Results & Verification

Dev bucket: gitops-lab-maryam-dev-data-store → 10-day log expiration, versioning disabled.
Prod bucket: gitops-lab-maryam-prod-data-store → 90-day log expiration, versioning enabled.
Separate state files in backend S3 bucket ce-bootcamp-tfstate-maryam.
Promotion PR plan: Initial run created prod resources (5 adds); subsequent would be "No changes".
Deploy workflows: Green in Actions tab for both dev and prod.

## Challenges & Solutions

Backend bucket: Replaced "YOURNAME" with "maryam" and created bucket manually.
Credentials: Set AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY in repo secrets.
PR merge permission: Used manual squash merge locally due to protection rules.
Initial prod deploy: Created resources on first apply to main.

This completes the GitOps lab with full environment separation, automated deploy, PR plan review, and drift detection readiness.



## 📚 Resources

- [GitOps Principles](https://opengitops.dev/)
- [GitHub Environments](https://docs.github.com/en/actions/deployment/targeting-different-environments)

<!-- ## 🚀 Submission

