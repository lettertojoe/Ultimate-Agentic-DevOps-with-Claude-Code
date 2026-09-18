# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static HTML/CSS portfolio website deployed to AWS using S3 and CloudFront, provisioned with Terraform, and automated via GitHub Actions.

## Architecture

- **index.html** — single-page portfolio (Home/Hero, About, Book, Courses, Contact), linked to `style.css`
- **style.css** — all styling (~1145 lines), mobile-first responsive with breakpoints at 900px, 768px, and 600px
- **privacy.html / terms.html** — standalone legal pages with their own inline styles (not sharing `style.css`)
- **images/** — static assets (logo, profile photo, hero background, course/topic thumbnails)
- Pure HTML5 and CSS3. No JavaScript. No build step. No framework.


### Known issue
`index.html` has `onclick="goToSection(...)"` and `onclick="toggleMenu()"` handlers (nav logo, hamburger menu, mobile menu buttons), but there is no `<script>` tag or JS file anywhere in the repo defining these functions — the mobile menu and in-page nav-by-click are currently non-functional.

## Commands

There is no build/lint/test tooling in this repo. Preview by opening `index.html` directly in a browser, or serve the directory with any static file server.

terraform init, terraform plan, terraform apply

## Working notes

- `README.md` describes deploying this site to an Ubuntu VM via Nginx as a DMI (DevOps Micro Internship) Week 1 lab exercise, including a mandatory footer "deployed by" edit for ownership proof — treat this as the authoritative description of the repo's current purpose.
- Git history (not present in the current working tree) shows this repo previously had `.claude/skills/` (deploy, scaffold-terraform, tf-plan, tf-apply) and a `.github/workflows/deploy.yml` for an AWS S3 + CloudFront deployment path. Those files have since been deleted from the working directory (uncommitted deletion as of this writing) — don't assume that infrastructure exists or recreate it unless asked.

## Conventions
- All infrastructure changes go through Terraform — never modify AWS resources manually
- No JavaScript in this project
- CSS uses mobile-first approach with breakpoints at 900px, 768px, and 600px

## Safety
- Never put secrets in this file. No API keys, passwords, or AWS credentials