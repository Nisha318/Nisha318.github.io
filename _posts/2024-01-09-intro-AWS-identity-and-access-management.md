---
title: "Introduction to AWS Identity and Access Management (IAM)"
excerpt: "A beginner-friendly walkthrough of managing users, groups, and permissions in AWS Identity and Access Management (IAM)."
date: 2023-01-23
categories: [AWS, Cloud Security]
tags: [IAM, AWS Security, Identity Management, Access Control]
toc: true
toc_label: "Table of Contents"
toc_icon: "key"
---

## Overview

In this lab, I explored how AWS Identity and Access Management (IAM) governs who can access specific AWS services and what actions they are allowed to perform. The lab walked through managing user identities, assigning permissions, and testing how IAM policies enforce least privilege across services like EC2 and S3.

## Objectives

- Understand how IAM organizes **users**, **groups**, and **policies**  
- Assign permissions according to a user’s job role  
- Test how policies affect service access  
- Observe the difference between managed and inline policies  

## What is AWS IAM

AWS Identity and Access Management (IAM) is the core service that controls authentication and authorization within the AWS cloud. It allows you to create and manage users, assign them to groups, and apply permission policies that dictate what actions can be performed on AWS resources.

In short, IAM answers three key questions:
1. **Who** can access AWS resources  
2. **What** actions they can perform  
3. **Which** resources they can perform those actions on  

## Exploring Users and Groups

IAM uses a hierarchy that keeps access management organized. In this lab, I examined several pre-created users and groups representing different roles within a small organization.

Each user had no permissions until added to a group. Groups, on the other hand, held the actual policies defining access levels. This structure showed how permissions can be managed collectively rather than individually.

**Screenshot suggestion:**  

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-users-list.png' | relative_url }}" alt="Pre-created IAM Users list in the AWS Console">
  <figcaption><strong>Figure 1.</strong> Pre-created IAM users (user-1, user-2, and user-3) displayed in the AWS Identity and Access Management console.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user1-permissions.png' | relative_url }}" alt="IAM user-1 permissions tab">
  <figcaption><strong>Figure 2.</strong> The Permissions tab for user-1 showing no policies attached. The red warning indicates that this user currently has no access privileges.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user1-groups.png' | relative_url }}" alt="IAM user-1 groups tab">
  <figcaption><strong>Figure 3.</strong> The Groups tab for user-1 confirming that this user is not yet a member of any IAM group.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user1-security-credentials.png' | relative_url }}" alt="IAM user-1 security credentials tab">
  <figcaption><strong>Figure 4.</strong> The Security Credentials tab for user-1 displaying console sign-in details and options to configure multi-factor authentication (MFA).</figcaption>
</figure>

- IAM Console view of **Users** list showing user-1, user-2, and user-3.  
- IAM Console view of **User Groups** showing EC2-Admin, EC2-Support, and S3-Support.





**Optional screen recording:**  
- Short clip demonstrating navigation between the **Users** and **Groups** pages in the IAM console.

## Understanding Policies

Policies in IAM are JSON-based documents that define permissions. They include three main elements:  

- **Effect** – Allow or Deny  
- **Action** – The AWS API calls that are permitted  
- **Resource** – The specific resources those actions apply to  

By reviewing attached policies, I saw the difference between **AWS managed policies**, which are maintained by AWS and can be reused across environments, and **inline policies**, which are unique to a specific user or group.

**Screenshot suggestion:**  
- Open **AmazonEC2ReadOnlyAccess** managed policy, showing its summary and permissions tab.  
- Example of the **JSON** tab displaying policy structure.


<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user-groups-list.png' | relative_url }}" alt="Pre-created IAM User Groups in AWS Console">
  <figcaption><strong>Figure 5.</strong> Pre-created IAM user groups: EC2-Admin, EC2-Support, and S3-Support. Each group is designed to manage permissions by functional role.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2support-group.png' | relative_url }}" alt="EC2-Support user group permissions">
  <figcaption><strong>Figure 6.</strong> The EC2-Support group includes the managed policy <code>AmazonEC2ReadOnlyAccess</code>, granting visibility into EC2 instances without modification rights.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2readonly-json.png' | relative_url }}" alt="AmazonEC2ReadOnlyAccess policy JSON structure">
  <figcaption><strong>Figure 7.</strong> JSON policy document for <code>AmazonEC2ReadOnlyAccess</code>. This managed policy allows Describe and List operations across EC2, Load Balancing, CloudWatch, and Auto Scaling services.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-s3support-group.png' | relative_url }}" alt="S3-Support user group permissions">
  <figcaption><strong>Figure 8.</strong> The S3-Support group uses the AWS managed policy <code>AmazonS3ReadOnlyAccess</code>, providing view-only access to S3 bucket contents.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-s3readonly-json.png' | relative_url }}" alt="AmazonS3ReadOnlyAccess policy JSON structure">
  <figcaption><strong>Figure 9.</strong> JSON policy for <code>AmazonS3ReadOnlyAccess</code>. The permissions include Get and List actions for all S3 buckets, ideal for support roles requiring non-destructive access.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2admin-group.png' | relative_url }}" alt="EC2-Admin user group permissions">
  <figcaption><strong>Figure 10.</strong> The EC2-Admin group contains a custom inline policy <code>EC2-Admin-Policy</code> rather than a managed policy. Inline policies are used for one-off or specialized permission sets.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2admin-policy-json.png' | relative_url }}" alt="EC2-Admin inline policy JSON">
  <figcaption><strong>Figure 11.</strong> JSON definition of the EC2-Admin inline policy. It grants permission to start and stop EC2 instances while maintaining the ability to describe instances and alarms in CloudWatch.</figcaption>
</figure>

## Assigning Users to Groups

Following a simple organizational model:
- The **S3 Support user** needed read-only access to S3.  
- The **EC2 Support user** required view-only access to EC2 instances.  
- The **EC2 Administrator** needed the ability to start and stop EC2 instances.  

After adding each user to the correct group, their permissions took effect immediately. This demonstrated how IAM’s group model simplifies access management and enforces consistent security boundaries.

**Screenshot suggestion:**  
- “Add users to group” screen for one of the groups.  
- Updated group summary showing one user assigned.

**Optional screen recording:**  
- A brief clip showing how a user is added to a group and confirmed.


<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-s3support-add-user1.png' | relative_url }}" alt="user-1 added to the S3-Support Group">
  <figcaption><strong>Figure 12.</strong> user-1 added to the <code>S3-Support</code> group, inheriting read-only permissions to Amazon S3 through the attached <code>AmazonS3ReadOnlyAccess</code> policy.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2support-add-user2.png' | relative_url }}" alt="user-2 added to the EC2-Support Group">
  <figcaption><strong>Figure 13.</strong> user-2 added to the <code>EC2-Support</code> group, which provides view-only access to EC2 instances via the <code>AmazonEC2ReadOnlyAccess</code> managed policy.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-ec2admin-add-user3.png' | relative_url }}" alt="user-3 added to the EC2-Admin Group">
  <figcaption><strong>Figure 14.</strong> user-3 added to the <code>EC2-Admin</code> group. Members of this group are granted permissions defined by the inline <code>EC2-Admin-Policy</code>, allowing them to start and stop EC2 instances.</figcaption>
</figure>



## Testing Access Permissions

Next, I logged in using the IAM user credentials to test their access.  
Each user account produced distinct outcomes that illustrated how policies enforce least privilege:

- **User 1 (S3 Support)** – Could view S3 buckets but received “Access Denied” when opening EC2.  
- **User 2 (EC2 Support)** – Could list EC2 instances but not stop or modify them.  
- **User 3 (EC2 Admin)** – Could start and stop EC2 instances as expected.

This verification step confirmed that IAM was correctly applying permissions as designed.

**Screenshot suggestion:**  
- AWS Management Console as each user:
  - S3 bucket list (for S3 Support)  
  - EC2 instances view with “Access Denied” prompt (for EC2 Support)  
  - EC2 instance “Stopping” state (for EC2 Admin)

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-dashboard-signin-url.png' | relative_url }}" alt="IAM Dashboard showing sign-in URL for IAM users">
  <figcaption><strong>Figure 15.</strong> IAM Dashboard displaying the account sign-in URL, which will be used for IAM user logins. Each user signs in through this unique console URL rather than the root account login page.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user1-login.png' | relative_url }}" alt="IAM user-1 login screen">
  <figcaption><strong>Figure 16.</strong> IAM user-1 login screen using the account ID and IAM username. Logging in through a private browsing session prevents cached credentials from interfering with the test.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user1-s3buckets.png' | relative_url }}" alt="user-1 accessing Amazon S3 buckets">
  <figcaption><strong>Figure 17.</strong> user-1 successfully viewing available Amazon S3 buckets, confirming that membership in the <code>S3-Support</code> group grants read-only S3 access.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user2-ec2-unauthorized.png' | relative_url }}" alt="user-2 unauthorized EC2 access message">
  <figcaption><strong>Figure 18.</strong> user-2 receives an authorization error while attempting to describe EC2 instances. This confirms that the <code>AmazonEC2ReadOnlyAccess</code> policy is restricted by a higher-level control policy or region mismatch.</figcaption>
</figure>

<figure class="align-center">
  <img src="{{ '/assets/images/aws/iam/iam-user3-ec2instances.png' | relative_url }}" alt="user-3 viewing EC2 instances in AWS console">
  <figcaption><strong>Figure 19.</strong> user-3 verifying EC2 access through the <code>EC2-Admin</code> group. As an administrator, this user can start or stop instances according to the inline policy definition.</figcaption>
</figure>



**Optional screen recording:**  
- Short demonstration switching between IAM sign-ins to show policy impact.

## Lessons Learned

1. **Group-based permissions simplify scaling.** Instead of editing each user, assign them to the right group.  
2. **Managed policies save time.** AWS updates them automatically to reflect new service actions.  
3. **Always test IAM roles and permissions.** Assumptions can lead to overly permissive access.  
4. **Adopt least privilege by default.** Grant only what is necessary for a user’s job function.

## Conclusion

This lab reinforced the importance of IAM as the foundation of AWS security. By defining identities, grouping them logically, and attaching precise permission policies, organizations can ensure accountability and limit exposure. Learning how these pieces fit together provides a strong baseline for securing larger environments and preparing for more advanced topics such as identity federation, role-based access, and policy automation.

**Screenshot suggestion (closing image):**  
- IAM Dashboard overview showing users, groups, and roles count summary — ideal as a closing graphic.

---

