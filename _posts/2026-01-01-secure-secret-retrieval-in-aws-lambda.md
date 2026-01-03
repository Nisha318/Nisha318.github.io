---
title: "Secure Secret Retrieval in AWS Lambda"
author: "Notes By Nisha"
 
excerpt: "A Practical Lab in Eliminating Hardcoded Credentials Using AWS Secrets Manager and IAM"
date: 2026-01-01
read_time: true
comments: no
share: true
related: true
layout: single
classes: wide
author_profile: true
toc: true
toc_label: "Table of Contents"
toc_sticky: true
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

header:
  teaser: /assets/images/aws/secrets-manager/lambda-secrets-01.png
  overlay_image: /assets/images/00-hero.jpg
  overlay_filter: 0.3
  caption: "Secure credential handling in AWS serverless workloads"
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
*Figure 1: Architecture diagram showing removal of embedded credentials and enforcement of IAM-governed secret retrieval, reducing credential exposure and blast radius.*
 
{: .text-center}

---

## Assessment Scope

This lab evaluated three credential-handling approaches within a serverless workload:

1. Hardcoded AWS access keys embedded directly in Lambda code  
2. Centralized credential storage using AWS Secrets Manager  
3. Runtime secret retrieval governed by IAM permissions  

The Lambda workload was exercised to:
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

{: .text-center}
![Lambda execution role permissions](/assets/images/aws/secrets-manager/iam-execution-role-permissions.png){: .align-center}
*Figure 3: Lambda execution role scoped to retrieve a single Secrets Manager ARN and required DynamoDB resources.*
{: .text-center}

---

## Validation Results

After updating the Lambda function to retrieve credentials from Secrets Manager:

- The function executed successfully  
- DynamoDB tables and items were created as expected  
- No credentials were embedded in code or exposed in logs  

This validated that the security control change did not introduce functional regressions while materially improving credential handling.

{: .text-center}
![Lambda execution with secure secret retrieval](/assets/images/aws/secrets-manager/successful-lambda-execution-1.png){: .align-center}
![Lambda execution with secure secret retrieval](/assets/images/aws/secrets-manager/successful-lambda-execution-2.png){: .align-center}

*Figure 4: Successful Lambda execution retrieving secrets at runtime. Ownership attribution included for portfolio traceability.*
{: .text-center}


{: .text-center}
![CloudTrail Secrets Manager access event](/assets/images/aws/secrets-manager/cloudtrail-evidence-1.png){: .align-center}
![CloudTrail Secrets Manager access event](/assets/images/aws/secrets-manager/cloudtrail-evidence-2.png){: .align-center}
*Figure 5: CloudTrail records Secrets Manager access by the Lambda execution role, enabling audit and forensic visibility.*
{: .text-center}

{: .text-center}
![CloudWatch Logs for Lambda execution](/assets/images/aws/secrets-manager/cloudwatch-lambda-runtime-logs-1.png){: .align-center}
![CloudWatch Logs for Lambda execution](/assets/images/aws/secrets-manager/cloudwatch-lambda-runtime-logs-2.png){: .align-center}
![CloudWatch Logs for Lambda execution](/assets/images/aws/secrets-manager/cloudwatch-lambda-runtime-logs-3.png){: .align-center}
*Figure 6: CloudWatch logs showing successful Lambda execution without exposure of secret material.*
{: .text-center}

---
## Optional: SOC Visibility and Threat Hunting Pattern

While this lab focused on secure secret retrieval and IAM enforcement, the same telemetry can be leveraged by SOC teams for detection and investigation.

In environments where CloudTrail logs are forwarded to CloudWatch Logs or a SIEM, analysts can hunt for Secrets Manager access using a simple CloudWatch Logs Insights query.

Example hunting query:

fields eventTime, eventSource, eventName, userIdentity.arn
| filter eventSource = "secretsmanager.amazonaws.com"
| sort eventTime desc

This query allows analysts to identify:

Which principals accessed Secrets Manager

When secrets were retrieved

Whether access patterns align with expected application behavior

In this lab environment, CloudTrail logs were validated directly via CloudTrail event history rather than CloudWatch Logs forwarding. In production environments, this query pattern can be operationalized for continuous monitoring and alerting.

*In production, this telemetry would typically be forwarded to a centralized SIEM where Secrets Manager access could be correlated with application identity, source context, and anomaly detection workflows.*
---

## Key Tradeoff Decisions

- **Secrets Manager vs IAM-only access**  
  Secrets Manager was used to eliminate hardcoded credentials while maintaining compatibility with legacy access patterns. This temporarily retains static credentials but reduces immediate exposure through encryption, IAM scoping, and auditability. The long-term objective is full migration to IAM role-based access using STS-issued temporary credentials.

- **Deferred automated rotation**  
  Automated rotation was intentionally deferred to focus this lab on validating secure retrieval and access controls. This increases credential exposure duration, which was accepted for a scoped environment and mitigated through least-privilege permissions, monitoring, and a defined production rotation strategy.

- **Access is enforced through IAM and API-level authorization, not network segmentation.**  
  Access controls were enforced at the IAM and resource policy layer rather than through network segmentation. This prioritizes workload identity and fine-grained authorization in line with Zero Trust principles, while allowing network controls to be layered in as the environment scales.

These tradeoffs were appropriate for a constrained lab environment and are addressed through IAM hardening, monitoring, and phased migration in production.

---
## Security Tradeoffs and Migration Path

AWS Secrets Manager was intentionally used as a **transitional control**, not the end state.

This lab addressed the most immediate risk: hardcoded credentials embedded in application code. Secrets Manager removes credentials from the codebase, encrypts them at rest, scopes access through IAM, and provides audit visibility through CloudTrail. This materially reduces exposure while preserving compatibility with legacy access patterns that still rely on static credentials.

However, Secrets Manager does not eliminate static credentials. It centralizes and protects them.

The long-term secure architecture replaces stored credentials entirely with **IAM role-based access using STS-issued temporary credentials**. In that model, the Lambda function authenticates through its execution role, receives short-lived credentials automatically, and never retrieves or handles secrets directly.

**Migration path:**
1. Eliminate hardcoded credentials using Secrets Manager
2. Validate functionality, IAM scoping, and audit visibility
3. Remove static credentials and rely exclusively on IAM roles and STS
4. Use Secrets Manager only for non-AWS secrets or external integrations

Secrets Manager is therefore a risk-reduction step, not the final destination, in a Zero Trust workload identity model.
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

---
*Note: This implementation was validated in a controlled lab environment to demonstrate secure credential management patterns applicable to production workloads.*

## Appendix: Evidence Index

- Figure 1: Architecture Diagram
- Figure 2: Lambda function with hardcoded credentials
- Figure 3: IAM execution role permissions
- Figure 4: Successful Lambda execution retrieving secrets
- Figure 5: CloudTrail Secrets Manager access event
- Figure 6: CloudWatch Lambda runtime logs