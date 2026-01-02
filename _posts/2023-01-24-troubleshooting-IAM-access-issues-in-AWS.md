---
title: "AWS Lab Walkthrough: Troubleshooting IAM Access Issues"
excerpt: "Learn how to troubleshoot IAM role assumption failures by aligning identity-based policies and trust relationships while maintaining least privilege."
date: 2025-11-01
author_profile: true
read_time: true
categories:
  - AWS
  - IAM
  - Troubleshooting
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
header:
  overlay_image: /assets/images/aws-iam-troubleshooting.jpg
  overlay_filter: 0.4
  caption: "Hands-on troubleshooting with AWS IAM"
---

## Introduction

This walkthrough covers a practical AWS lab focused on troubleshooting IAM access issues. The scenario mirrors a common situation in cloud operations where users must assume roles to perform their tasks, rather than having direct permissions assigned. The objective is to identify why a user cannot assume an assigned role and apply a least privilege fix.

---

## Scenario Overview

In this lab, a new IAM user named **Operator-User** cannot assume a role named **Operator-Role**. As part of the cloud team, your job is to find out what is wrong and fix it.

The issue affects the user's ability to:
- Assume a role through the AWS Management Console  
- Access an EC2 instance using **AWS Systems Manager Session Manager**

The root of such problems often lies in a mismatch between:
1. **The user’s permissions** (identity-based policy)
2. **The role’s trust relationship** (trust policy)

---

## Step 1. Reproduce the Problem

To begin, log in as **Operator-User** and attempt to switch roles from the AWS Management Console.

1. Choose the **user menu** in the top-right corner and select **Switch Role**.  
2. Enter the **Account ID** and **Role Name (Operator-Role)**.  
3. Leave the display name blank and click **Switch Role**.  

If you see an error message such as *“Invalid information in one or more fields”*, you have confirmed that the role assumption is failing.

**Screenshot 1:** The “Invalid information” error displayed when attempting to switch roles.

This validation step ensures that any later fix can be verified against the same failure.

---

## Step 2. Review the User’s Permissions

Next, return to the console signed in as **AWSLabUser** (the admin account provided for troubleshooting). Navigate to **IAM > Users > Operator-User > Permissions** and review the attached policy.

You will likely find that the user does not have the **sts:AssumeRole** action included in the permissions policy. This action is required for a user to assume another role through the AWS Security Token Service (STS).

**Remediation:**
- Edit the user policy to include the `sts:AssumeRole` action.
- Limit the `Resource` to only the **Operator-Role ARN** to enforce least privilege.

Example policy snippet:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::<account-id>:role/Operator-Role"
    }
  ]
}
