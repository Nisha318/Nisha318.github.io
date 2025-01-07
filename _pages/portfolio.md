---
layout: single
title: "Portfolio"
permalink: /portfolio/
header:
  teaser: /assets/images/ctf_hero_header_image.png
  overlay_filter: rgba(0, 0, 0, 0.5)
  overlay_image: /assets/images/ctf_hero_header_image.png
  caption: "Photo credit: [**AI**](https://chatgpt.com/)"
author_profile: true
classes: wide
date: April 30, 2023

feature_row0-1:
  - image_path: assets/images/aws/aws-three-tier-vpc.png
    alt: "AWS networking"
    title: "Use Terraform to Build an AWS Three-Tier VPC Network Architecture"
    text: "I built and deployed a scalable three-tier AWS network architecture using Terraform modules. This project included configuring a Virtual Private Cloud (VPC) with route tables, subnets, NAT gateways, and an internet gateway. I automated the provisioning of EC2 instances for application hosting within the tiers and integrated S3 for secure data storage. Additionally, I deployed Application Load Balancers to ensure high availability and used AWS Certificate Manager to manage SSL/TLS certificates. This project enhanced my understanding of cloud infrastructure automation and AWS networking best practices."
    url: "https://github.com/Nisha318/Terraform-Modules/blob/main/README.md"
  btn_label: "AWS"
  btn_class: "btn--primary"
  tags:
      - AWS Networking
      - VPC
      - Internet Gateway
      - Route Table
      - NAT Gateway
      - Subnets
      - S3
      - AWS Certificate Manager
      - Application Load Balancer
      - Terraform Modules


feature_row0-2:
  - image_path:  /assets/images/aws/aws-dev-environment.jpg
    alt: "AWS Devops "
    title: "Build a Dev Environment with Terraform and AWS "
    text: "I built a development environment on AWS using Terraform for infrastructure as code. Configured a Virtual Private Cloud (VPC) with a public subnet, internet gateway, and a public route table. Deployed an EC2 instance within the public subnet and configured security groups to manage access. Utilized Terraform templates to automate resource provisioning and setup. Gained practical knowledge in designing and deploying cloud-based development environments."
    url: "https://github.com/Nisha318/Terraform-AWS-Configs/blob/main/Build%20a%20Dev%20Environment%20with%20Terraform%20and%20AWS/README.md"
    btn_label: "AWS"
    btn_class: "btn--primary"
    url2: "https://github.com/Nisha318/Terraform-AWS-Configs/blob/main/Build%20a%20Dev%20Environment%20with%20Terraform%20and%20AWS/README.md "
    btn_label2: "DevOps "
    btn_class: "btn--primary"
    tags:
        - AWS
        - Internet Gateway
        - Route Table
        - EC2
        - Subnet
        - Security Group   
        
---

## Projects in Cloud Computing

{% include feature_row id="feature_row0-1" type="left" %}
<a name="Use Terraform to Build an AWS Three-Tier VPC Network Architecture"></a>

{% include feature_row id="feature_row0-2" type="left" %}
<a name="Buid a Dev Environment with Terraform and AWS"></a>
