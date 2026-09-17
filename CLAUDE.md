# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static HTML/CSS portfolio website deployed to AWS (S3 + CloudFront) via GitHub Actions. Terraform provisioning is planned but not yet scaffolded in this repo — see "Infrastructure" below.

## Architecture

### Application (Static Site)
- **index.html** — Single-page portfolio (About, Services, Courses, Books, Community, Contact)
- **style.css** — All styling (~1145 lines), mobile-first responsive (breakpoints: 900px, 768px, 600px)
- **privacy.html / terms.html** — Standalone pages with inline styles
- **images/** — Static assets (logo, profile, course thumbnails, hero background)
- Pure HTML5 + CSS3, no JavaScript, no build step

### Infrastructure
- No `terraform/` directory exists yet. Infrastructure must be generated first via the `/scaffold-terraform` skill, which targets S3 (private, OAC-based access) + CloudFront.
- The live AWS resources (S3 bucket, CloudFront distribution, GitHub OIDC role) already exist and are referenced directly in `.github/workflows/deploy.yml` — they were not provisioned from a Terraform config currently in this repo. If/when Terraform is scaffolded, reconcile it against the real resource identifiers in the workflow file rather than assuming a fresh deploy.

### CI/CD (`.github/workflows/deploy.yml`)
- Triggers on push to `main`
- Authenticates to AWS via GitHub OIDC (`aws-actions/configure-aws-credentials`) — no stored access keys
- Syncs the repo to S3 with `aws s3 sync --delete`, excluding `.git`, `.github`, `.claude`, `terraform`, `.mcp.json`, and `*.md`
- Invalidates the CloudFront distribution (`/*`) after sync
- Bucket name, IAM role ARN, region, and CloudFront distribution ID are hardcoded in this file rather than sourced from Terraform outputs — update them here if the underlying infra changes

## Skills (`.claude/skills/`)

Infrastructure and deployment tasks are handled via skills rather than hand-written Terraform/CI code. All current skills are action skills (`disable-model-invocation: true`, manual/slash-command only):

```
/scaffold-terraform [aws-region] [project-name]  → Generate terraform/ for S3 + CloudFront (default: ap-south-1, portfolio-site)
/tf-plan                                          → Run terraform plan + risk/blast-radius summary
/tf-apply                                         → Run terraform apply + verify outputs
/deploy                                           → Sync site to S3 + invalidate CloudFront (reads terraform outputs for bucket/dist-id)
```

`/deploy` and `/tf-apply` currently assume a `terraform/` directory with valid outputs, which won't exist until `/scaffold-terraform` has been run — for routine content deploys before then, the GitHub Actions workflow (push to `main`) is the actual deploy path, not these skills.

## Commands

```bash
# Local preview
open index.html

# Terraform (only after /scaffold-terraform has generated terraform/)
cd terraform && terraform init
cd terraform && terraform plan
cd terraform && terraform apply
```

## Conventions
- Don't hand-write Terraform or CI/CD YAML — use the skills above so infra generation stays consistent.
- GitHub Actions uses OIDC — no stored AWS access keys.
- Site content changes deploy automatically via GitHub Actions on push to `main`; this is independent of the Terraform skills above.

## Note on README.md
`README.md` describes a different exercise (deploying this same site to an Ubuntu VM via Nginx as a DMI Week 1 lab, with a mandatory footer ownership-edit). That is not how this repo is actually deployed — actual deployment is AWS S3 + CloudFront via the GitHub Actions workflow above. Treat the README as stale/unrelated boilerplate, not as instructions for this repo's real CI/CD.
