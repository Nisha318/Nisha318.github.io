---
layout: single
title: "Portfolio"
permalink: /portfolio/
header:
  overlay_image: /assets/images/00-hero.jpg
  caption: "Photo credit: Unsplash"
author_profile: true
classes: wide
date: April 30, 2023
last_modified_at: 2025-09-24
excerpt: "A Senior Cybersecurity Engineer focused on achieving Cloud Security Compliance and implementing Zero Trust architectures in AWS and Azure. Expertise includes NIST RMF, policy development, and Infrastructure as Code (IaC) security."

feature_row_projects:
  - image_path: assets/images/aws/aws-three-tier-vpc.png
    alt: "AWS Networking"
    title: "Three-Tier AWS VPC (Terraform): Compliance-Driven Network"
    text: "Scalable VPC provisioned with Terraform, designed to align with NIST SP 800-53 controls (e.g., SC-7 Boundary Protection and AC-4 Information Flow Enforcement). Demonstrates Zero Trust principles through network segmentation and least privilege IAM policies."
    url: "https://github.com/Nisha318/Terraform-Modules"
    btn_label: "View on GitHub"
    btn_class: "btn--primary"
 
- image_path: assets/images/aws/aws-config-automated-enforcement.png
   alt: "Automated RMF Enforcement"
   title: "Automated RMF Control: Hybrid Remediation of Public SSH/RDP (AC-4, SC-7, CA-7) 🛡️"
   text: "This project implements a critical **Risk Management Framework (RMF) security control** by deploying a **hybrid automation pipeline** (Config + SSM + Lambda). It provides **continuous, auditable enforcement** (CA-7) by automatically detecting and instantaneously **revoking** the severe security violation of unauthorized public access (0.0.0.0/0) to administrative ports (SSH/RDP), ensuring strict **Boundary Protection (SC-7)** and **Access Control (AC-4)**."
   url: "https://github.com/Nisha318/AWS-Repo/config-ssm-auto-revoke-sg/"
   btn_label: "View on GitHub"
   btn_class: "btn--primary"

  - image_path: /assets/images/aws/aws-dev-environment.jpg
    alt: "AWS Dev Environment"
    title: "AWS Dev Environment (Terraform): Security Baseline Implementation"
    text: "Automated provisioning of a secure AWS environment using Terraform. Implemented security baselines and configuration management (CM) best practices to ensure least privilege access and a compliant development environment."
    url: "https://github.com/Nisha318/Terraform-AWS-Configs"
    btn_label: "View on GitHub"
    btn_class: "btn--primary"
---

## Quick Links
- [LinkedIn](https://www.linkedin.com/in/nishapmcd)
- [GitHub](https://github.com/Nisha318)
- [Certifications (Credly)](https://www.credly.com/users/nishapmcd/badges#credly)

## Projects
{% include feature_row id="feature_row_projects" type="left" %}

## Writing Highlights
- [ Auto-Remediation as an RMF Security Control: Enforcing Zero-Trust Ingress with AWS Config and Custom SSM](#)
- [Investigating Web Attacks](https://notesbynisha.com/blog/investigate-web-attacks-lets-defend-walkthrough/)

## Contact
- Best way to connect: [LinkedIn](https://www.linkedin.com/in/nishapmcd)
