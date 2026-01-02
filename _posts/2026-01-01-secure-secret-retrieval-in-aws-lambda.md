---
title: "Secure Secret Retrieval in AWS Lambda"
subtitle: "A Practical Lab in Eliminating Hardcoded Credentials Using AWS Secrets Manager and IAM"
date: 2026-01-01
layout: single
toc: true
toc_label: "On This Page"
toc_icon: "lock"
categories:
  - Cloud Security
  - AWS
tags:
  - AWS Lambda
  - AWS Secrets Manager
  - IAM
  - Cloud Security
  - RMF
  - DevSecOps
  - NIST-800-53
  - Zero-Trust
---

## Overview

In 2019, the Capital One breach exposed the personal information of more than 100 million customers. The root cause was not an exotic exploit. It was a combination of misconfigured IAM permissions and credential access that allowed an attacker to retrieve sensitive data from AWS resources. The incident reinforced a recurring lesson in cloud security: breaches often stem from how credentials are handled, not from failures in encryption algorithms or underlying infrastructure.

Hardcoding credentials in application code remains one of the most common and preventable cloud security failures. Even in serverless environments like AWS Lambda, embedded access keys increase blast radius, complicate rotation, and make misuse harder to detect through standard audit mechanisms.

This post walks through a guided lab that validates how **AWS Secrets Manager** can be used with AWS Lambda to retrieve credentials securely at runtime, replacing hardcoded secrets while preserving application functionality with DynamoDB. Rather than focusing on basic service configuration, the goal is to examine how small implementation choices influence security posture, auditability, and operational risk.

The focus of this write-up is **security reasoning and validation**, not step-by-step console instructions.

---

## Architecture Summary

**High-level flow:**

- A Lambda function executes using an IAM execution role  
- Credentials are retrieved securely from AWS Secrets Manager  
- The function accesses DynamoDB without embedding secrets in code  
- All access is governed by IAM and logged via CloudTrail  

{: .text-center}
![Architecture Diagram](/assets/images/aws/secrets-manager/architecture-diagram.png){: .align-center}
*Figure 1: Architecture Diagram*
{: .text-center}

---

## Assessment Scope

This lab evaluated three credential-handling approaches within a serverless workload:

1. Hardcoded AWS access keys embedded directly in Lambda code  
2. Centralized credential storage using AWS Secrets Manager  
3. Runtime secret retrieval governed by IAM permissions  

The Lambda function was used to:
- Create DynamoDB tables and insert items  
- Retrieve and return table data  

The assessment focused on functional behavior, access control boundaries, and audit visibility rather than application logic.

---

## Insecure Baseline: Hardcoded Credentials

The initial implementation embedded AWS access keys directly in the Lambda source code. While the function executed successfully, this pattern introduces several risks:

- Credentials are exposed to anyone with access to the code  
- Secrets can be unintentionally committed to version control  
- Rotation requires code changes and redeployment  
- Compromise is difficult to detect and investigate  

This pattern is routinely identified during security assessments and violates foundational cloud security best practices.

{: .text-center}
![Lambda function with hardcoded AWS credentials](/assets/images/aws/secrets-manager/lambda-hardcoded-credentials.png){: .align-center}
*Figure 2: Initial implementation with embedded access keys (credentials redacted)*
{: .text-center}

---

## Secure Implementation: AWS Secrets Manager

To mitigate these risks, credentials were stored in **AWS Secrets Manager** and retrieved dynamically by the Lambda function at runtime.

Key improvements include:
- Encryption at rest using AWS KMS  
- Centralized credential management decoupled from application code  
- IAM-governed access to secrets  
- Improved auditability and support for rotation  

The Lambda execution role was scoped to retrieve only the required secret ARN, enforcing least-privilege access.

---

## Validation Results

After updating the Lambda function to retrieve credentials from Secrets Manager:

- The function executed successfully  
- DynamoDB tables and items were created as expected  
- No credentials were embedded in code or exposed in logs  

This confirmed that security improvements did not introduce functional regressions while materially improving credential handling.

{: .text-center}
![Validation of Lambda Secrets Retrieval](/assets/images/aws/secrets-manager/dynamodb-validation-1.png){: .align-center}

{: .text-center}
![Validation of Lambda Secrets Retrieval](/assets/images/aws/secrets-manager/dynamodb-validation-2.png){: .align-center}

*Figure 3: Validation of Lambda Secrets Retrieval*
{: .text-center}

---

## Key Tradeoff Decisions

- **Secrets Manager vs IAM-only access**  
  Secrets Manager was used to eliminate hardcoded credentials while maintaining compatibility with legacy access patterns. This temporarily retains static credentials but reduces immediate exposure through encryption, IAM scoping, and auditability. The long-term objective is full migration to IAM role-based access using STS-issued temporary credentials.

- **Deferred automated rotation**  
  Automated rotation was intentionally deferred to focus this lab on validating secure retrieval and access controls. This increases credential exposure duration, which was accepted for a scoped environment and mitigated through least-privilege permissions, monitoring, and a defined production rotation strategy.

- **API-level authorization over network controls**  
  Access controls were enforced at the IAM and resource policy layer rather than through network segmentation. This prioritizes workload identity and fine-grained authorization in line with Zero Trust principles, while allowing network controls to be layered in as the environment scales.

These tradeoffs were appropriate for a constrained lab environment and are addressed through IAM hardening, monitoring, and phased migration in production.

---

## Security Controls Mapped (RMF / NIST 800-53)

This implementation aligns with the following NIST SP 800-53 control objectives:

**IA-5 – Authenticator Management**  
Credentials are centrally stored and protected using AWS Secrets Manager rather than embedded in application code.

**IA-7 – Cryptographic Module Authentication**  
Secrets are encrypted at rest using AWS Key Management Service (KMS).

**AC-6 – Least Privilege**  
The Lambda execution role is scoped to retrieve only the required secret and access the necessary DynamoDB resources.

**SC-12 – Cryptographic Key Establishment and Management**  
AWS-managed cryptographic services protect sensitive authentication material without requiring custom key handling in application code.

**AU-2 / AU-12 – Auditable Events and Audit Generation**  
Secret access and resource interactions are logged via AWS CloudTrail to support monitoring and investigation.

---

## Zero Trust Architecture Context

This implementation supports Zero Trust principles by replacing implicit trust with verified, policy-driven access controls.

### Core Alignment

**Workload identity over network trust**  
The Lambda function operates under an IAM execution role, establishing verifiable workload identity rather than relying on static credentials or network location.

**Least privilege as micro-segmentation**  
Access is restricted at the API level. The function can retrieve a single secret ARN and access a specific DynamoDB table, limiting blast radius through permission-based segmentation.

**Continuous verification through telemetry**  
All access to Secrets Manager and DynamoDB is logged via CloudTrail, enabling post-access verification and detection workflows.

---

## Path to Maturity

While this lab uses AWS Secrets Manager to eliminate hardcoded credentials, the long-term Zero Trust objective is exclusive use of IAM role-based access with STS-issued temporary credentials. This approach removes static credentials entirely and consolidates authentication and authorization within the identity and policy layer.

---

## Key Takeaways

- Hardcoded credentials materially increase cloud security risk  
- AWS Secrets Manager enables secure, auditable credential retrieval without embedding secrets in code  
- IAM design and permission scoping are critical to limiting blast radius in serverless workloads  
- Guided labs can be effective tools for validating real-world security patterns when paired with architectural reasoning  

---

*Note: This implementation was validated in a controlled lab environment to demonstrate secure credential management patterns applicable to production workloads.*
