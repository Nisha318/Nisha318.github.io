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
    title: "Automated EC2 Network Security: RMF Continuous Control Enforcement 🛡️"
    text: "This project implements a critical **Risk Management Framework (RMF)** control automation pipeline in AWS to maintain a strict security posture. Leveraging **AWS Config** for **continuous auditing (CA-7)**, it automatically detects and remediates severe configuration drift (specifically the unauthorized opening of SSH (22) or RDP (3389) ports to the public internet). This solution directly enforces **Access Control (AC)** and **System and Communications Protection (SC)** controls, minimizing public exposure and ensuring **continuous compliance** for high-risk assets."
    url: "https://github.com/Nisha318/AWS-Repo/blob/main/config-auto-revoke-sg/README.md"
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
