---
layout: single
title: "Portfolio"
permalink: /portfolio/
header:
  overlay_image: /assets/images/ctf_hero_header_image.png
  caption: "Photo credit: [AI](https://chatgpt.com/)"
author_profile: true
classes: wide
date: April 30, 2023

feature_row0-1:
  - image_path: assets/images/aws/aws-three-tier-vpc.png
    alt: "AWS Networking"
    title: "Use Terraform to Build an AWS Three-Tier VPC Network Architecture"
    text: "I built and deployed a scalable three-tier AWS network architecture using Terraform modules. This project included configuring a VPC with route tables, subnets, and an internet gateway. I automated the provisioning of EC2 instances for application hosting within the tiers and integrated S3 for secure data storage. Additionally, I deployed Application Load Balancers to ensure high availability. This project enhanced my understanding of cloud infrastructure automation and AWS networking best practices."
    url: "https://github.com/Nisha318/Terraform-Modules/blob/main/README.md"
    btn_label: "View Code"
    btn_class: "btn--primary"
    tags:
      - AWS
      - VPC
      - Terraform
      - EC2
      - S3

feature_row0-2:
  - image_path: /assets/images/aws/aws-dev-environment.jpg
    alt: "AWS DevOps"
    title: "Build a Dev Environment with Terraform and AWS"
    text: "I built a development environment on AWS using Terraform for infrastructure as code. The project included configuring a VPC with a public subnet, an internet gateway, and a public route table. I deployed an EC2 instance within the public subnet and configured security groups to manage access. Terraform templates were utilized to automate resource provisioning and setup."
    url: "https://github.com/Nisha318/Terraform-AWS-Configs/blob/main/README.md"
    btn_label: "View Code"
    btn_class: "btn--primary"
    tags:
      - AWS
      - EC2
      - Terraform
      - Security Groups
      - Subnets

---

## Projects in Cloud Computing

{% include feature_row id="feature_row0-1" type="left" %}
{% include feature_row id="feature_row0-2" type="left" %}
