---
title: "Container Security Starts Before the Cloud"
excerpt: "How I established a container image security baseline using distroless, Trivy, and GitHub Actions before touching any cloud infrastructure."
date: 2026-05-31
categories:
  - Cloud Security
  - DevSecOps
tags:
  - Docker
  - Containers
  - Trivy
  - GitHub Actions
  - Distroless
  - FastAPI
  - NIST 800-53
  - DevSecOps
  - AWS
  - CIS Benchmark
header:
  image: /assets/images/container-security-progression/stage-1/featured-image.png
  teaser: /assets/images/container-security-progression/stage-1/featured-image.png
toc: true
toc_label: "In This Post"
toc_icon: "shield-alt"
---

# Container Security Starts Before the Cloud
## From Docker to EKS: Stage 1 | Container Image Security Baseline

*Series: From Docker to EKS: A Security-First Progression*

![Trivy scan results: python:3.12-slim (105 findings, 2 CRITICAL) vs gcr.io/distroless/python3-debian12 (7 findings, 0 CRITICAL)](../docs/images/stage-1/featured-image.png)

---

Many companies have already made the push towards containerizing applications in their production environments, with Kubernetes as the prevalent platform for cloud-native deployments. The real challenge involves ensuring that they're secure from the start.

I started this container security project series to fill that gap for myself. I wanted to learn not just how containers work, but how to deploy them securely at every step. Every decision about container image security controls, every tradeoff, every pipeline control, and every CVE became part of my process.

With this project, I document my approach throughout this progression. I start out with a secure Docker image, which will next be deployed to AWS Elastic Container Service (ECS) via Fargate mode, and eventually to AWS Elastic Kubernetes Service (EKS). I aim to show how my security decisions evolved as the project environment grew.

The container image is where much of that foundation gets built, and security problems introduced at the image layer become harder to address through orchestration controls alone. These may include unpatched packages, a root process, a secret that got baked into the build, to name a few.

---

## The Workload

I needed a reliable application for all three stages of this project, so I could focus on security controls rather than application logic. I chose Python FastAPI because I had already used it during the [Learn to Cloud Capstone project](https://learntocloud.guide), so it was familiar enough that I could move quickly without getting distracted by application development. It's also lightweight, realistic enough to simulate a real workload, and it generates API documentation automatically, making it easier to use.

Three endpoints. That's it:

- `/health` -- returns service health status
- `/status` -- returns app name, version, and current stage
- `/docs` -- FastAPI-generated interactive API documentation endpoint

The application remains the same at every stage of this project. What changes are in the infrastructure and security around it. That is the point.

![FastAPI /health endpoint returning service health status](../docs/images/stage-1/fastapi-health-endpoint.png)

![FastAPI /status endpoint returning app name, version, and current stage](../docs/images/stage-1/fastapi-status-endpoint.png)

![FastAPI container running locally on Docker Desktop](../docs/images/stage-1/docker-desktop-running-container.png)

![FastAPI auto-generated interactive API documentation](../docs/images/stage-1/fastapi-docs-ui.png)

---

## The Dockerfile Decisions

Most Docker tutorials I've completed only showed how to get an app running. My goal was to learn how to run it securely, with security built into the image from the start. Here's my Dockerfile with the main security decisions explained:

While working through this project and learning more about application container security, I noticed that many of these decisions aligned with principles from the [12 Factor App methodology](https://12factor.net), a set of guidelines for building modern, cloud-native web applications.

```dockerfile
# ---- Build Stage ----
# python:3.11-slim is used ONLY for building -- it never ships to production
FROM python:3.11-slim AS builder

WORKDIR /build

COPY app/requirements.txt .

# Install dependencies into an isolated folder
# They get copied to the runtime stage -- build tools stay behind
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -r requirements.txt --target /build/deps

# ---- Runtime Stage ----
# Distroless: no shell, no package manager, no perl, no bash
# Only the Python runtime and what your app actually needs
FROM gcr.io/distroless/python3-debian12 AS runtime

WORKDIR /app

# Copy only the installed dependencies -- not pip, not build tools
COPY --from=builder /build/deps /app/deps

# Copy application source
COPY app/ .

# Tell Python where to find the dependencies
ENV PYTHONPATH=/app/deps

# Distroless ships with a built-in nonroot user at UID 65532
# Drop to it here -- if the container is compromised, the attacker is not root
USER nonroot

EXPOSE 8000

# Health check so ECS and EKS have something to probe
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD ["python3", "-c", "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"]

# No shell in distroless -- run uvicorn directly via the Python module flag
CMD ["-m", "uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000", "--no-access-log"]
```

While going through the Dockerfile decisions during this project and reading through Liz Rice's *Container Security*, I came to understand the security reasoning behind each one. The multi-stage build keeps build tools and package managers out of the production image. The distroless runtime reduces the attack surface by removing shells, package managers, and extra utilities that have no place in a production container. Running as a non-root user limits what an attacker can do if the container is ever compromised. The HEALTHCHECK gives orchestration platforms a way to detect and respond to unhealthy containers automatically.

One thing that stood out to me was how the no-secrets-in-image pattern connects to Factor III of the 12 Factor App methodology, strengthening the idea that configuration that varies between deployments should live in the environment, not directly in the code or the image. The `.dockerignore` file, which excludes `.env` files, and the environment variable pattern in the Dockerfile are direct implementations of that principle. I didn't set out to implement 12 Factor, but I learned some of these patterns as I built the project. Identifying the connection afterward was one of those moments where concepts from different disciplines started to click together. Having worked previously in RMF and ATO processes, I have seen these principles documented as security controls: least privilege, configuration management, and attack surface minimization. However, this was the first time I had implemented them at the container layer rather than assessed them. That shift from evaluator to builder gave me a different appreciation for why these controls exist.

While these decisions do not make a container fully secure on their own, when used together, they reduce the attack surface and create a more defensible baseline.

---

## The First Scan: 105 CVEs

Before switching to distroless, I was using `python:3.12-slim` as the base image. I ran Trivy against it and got this:

```
Total: 105 (UNKNOWN: 4, LOW: 63, MEDIUM: 29, HIGH: 7, CRITICAL: 2)
```

![Trivy scan of python:3.12-slim -- 105 findings including 2 CRITICAL](../docs/images/stage-1/trivy-scan-python-slim.png)

Two CRITICAL severity level vulnerabilities. Both in `perl-base`.

![Trivy CRITICAL findings in python:3.12-slim -- both in perl-base](../docs/images/stage-1/trivy-scan-python-slim-critical.png)

My app is a Python API, not a Perl one. However, `python:3.12-slim` included it anyway because slim images, while smaller than full OS images, still carry packages your app never requested. This created an issue because I ended up with a bigger attack surface than I wanted and could not remove it without rebuilding the image or changing base images entirely.

The HIGH findings were spread across `ncurses`, `libpython`, and several `util-linux` packages. Most of these weren't directly related to running this application and just made the runtime footprint bigger than needed.

This is what a so-called "minimal" base image can look like if you're not careful. There were 105 findings, most from packages that were unnecessary for this workload.

This issue is not theoretical for me. I recall having worked through Log4j remediation actions on systems where the lesson was the same: bundled software that teams did not intentionally choose still became software they had to secure.

That experience shaped how I approached this container image design. Decreasing unnecessary dependencies earlier in the build process means there is less software to patch, track, and defend later.

---

## The Distroless Decision

Switching to `gcr.io/distroless/python3-debian12` immediately removed most of the extra packages I did not need. The result was 7 remaining OS findings and 0 vulnerabilities at the CRITICAL severity level. No Perl, no bash, no package manager, and no shell.

Distroless images remove many of the components that are unnecessary at runtime while keeping the dependencies your application actually needs.

However, there are tradeoffs.

Without a shell and package manager, these images are intentionally more restrictive than traditional container images. In my case, the biggest tradeoff showed up during vulnerability management. When fixes for base image packages became available upstream, I had to wait for updated distroless image releases before those fixes were available to me. That is exactly the situation I ran into, which I cover in the accepted risk section below.

For production workloads, the tradeoff seemed worth it. Reducing the number of unneeded components means reducing the amount of software that can become your responsibility to patch, monitor, and defend later.

---

## The Dependency Treadmill

After switching to distroless, my Python package scan still showed a HIGH-severity CVE in starlette. My first thought was to pin the package directly in `requirements.txt`. That failed because FastAPI manages starlette as a transitive dependency -- a package I never installed directly, but one that FastAPI pulled in automatically as part of its own requirements -- and wouldn't accept my pin:

```
ERROR: Cannot install -r requirements.txt (line 1) and starlette==0.40.0
because these package versions have conflicting dependencies.
fastapi 0.115.0 depends on starlette<0.39.0 and >=0.37.2
```

I was able to resolve this by upgrading FastAPI to version 0.136.3, which uses a patched version of starlette. That resolved the remaining Python package findings at that time.

The lesson here is that you can't always remediate transitive dependencies directly. If a vulnerable package is not something you explicitly installed, look at which dependency introduced it and upgrade there first.

The harder lesson is that vulnerability management never really stops. The day after I resolved the starlette HIGH finding, another CVE appeared with a different identifier.

At some point, you have to decide when to stop chasing every new finding and start documenting the risk you are willing to accept.

---

## Accepted Risk and the .trivyignore Approach

After all the remediation work, two HIGH findings remained. Both existed in the distroless base image, and both already had upstream fixes available. The problem was that updated distroless image digests containing those fixes had not yet been released.

At that point, I had two options:

1. Lower the pipeline severity threshold so the findings no longer fail the build
2. Keep the threshold and formally document the accepted risk

I chose the second option.

Lowering the threshold hides risk and makes pipeline results less meaningful. Therefore, I created a `.trivyignore` file to document the CVE identifiers, justification, and remediation status:

```
# CVE-2026-40356 - krb5 DoS via integer underflow
# Fix available in 1.20.1-2+deb12u5 but not yet included in distroless base image
# Tracked: pending distroless image update
CVE-2026-40356

# CVE-2025-13836 - cpython excessive read buffering DoS in http.client
# Fix available in 3.11.2-6+deb12u7 but not yet included in distroless base image
# Tracked: pending distroless image update
CVE-2025-13836
```

This creates an auditable record. Anyone reviewing the repository can see exactly what was accepted, why it was accepted, and what the intended remediation path looks like. This approach is consistent with how accepted risk is handled in formal RMF processes. Risk that cannot be immediately mitigated is documented, assigned an owner, and tracked for remediation rather than ignored or obscured. The `.trivyignore` file applies that same discipline at the pipeline level.

After applying the ignore file, the scan passed with all remaining findings documented as accepted risk.

![Trivy scan of distroless image -- 0 HIGH or CRITICAL findings](../docs/images/stage-1/trivy-scan-clean.png)

---

## The Pipeline

The GitHub Actions pipeline does four things whenever changes are pushed to `app/` or `stage-1-docker/`:

1. Checks out the repository code
2. Builds the Docker image
3. Runs Trivy with `--ignore-unfixed` and the `.trivyignore` file applied
4. Uploads the scan results as a pipeline artifact

The `--ignore-unfixed` flag is important because it tells Trivy to fail builds only when remediation exists. Failing builds for vulnerabilities without available fixes creates alert fatigue, and eventually people stop paying attention to pipeline failures.

Publishing the scan results as artifacts means that every scan is retained and auditable. You can download reports from previous runs and see what vulnerabilities existed when a particular image was built.

![GitHub Actions pipeline passing after .trivyignore applied](../docs/images/stage-1/github-actions-pipeline-success.png)

---

## NIST 800-53 Controls Implemented

Part of my goal for this project was to map security decisions to NIST 800-53 controls. I wanted to connect implementation choices to the compliance standard that I work with professionally. The implementation decisions in this project are grounded in **NIST SP 800-190 (Application Container Security Guide)**, which is the NIST publication specifically focused on container security. DoD environments have additional requirements governed by DISA STIGs and the Container Platform SRG. Here's what Stage 1 covers:

| Control | Implementation |
|---|---|
| AC-6 Least Privilege | Non-root user (UID 65532) enforced at runtime |
| CM-6 Configuration Settings | Dockerfile enforces a standardized, repeatable configuration |
| CM-7 Least Functionality | Distroless base with no shell, no package manager, and only port 8000 exposed |
| RA-5 Vulnerability Scanning | Trivy scans on every pipeline run |
| SA-11 Developer Testing | Automated security testing gates every pipeline run, results stored as artifacts |
| SI-2 Flaw Remediation | CVE threshold gates pipeline success, accepted risks documented in .trivyignore |

The full control mapping is maintained in [`compliance/nist-800-53-mapping.md`](https://github.com/nisha318/container-security-progression/blob/main/compliance/nist-800-53-mapping.md) in the project repo.

---

## What I Learned

A few honest takeaways from Stage 1:

**Choose distroless early if your workload supports it.** Keeping `python:3.11-slim` in the build stage still made sense for dependency installation, but using it as my runtime image introduced unnecessary rework and additional vulnerabilities I later had to remove.

**CVE remediation is a process, not a one-time goal.** New CVEs are found all the time. The goal isn't zero findings, but to have a mature, documented response to the ones you do have.

**The pipeline becomes the enforcement mechanism.** Writing a secure Dockerfile is important, but without a pipeline that checks it on every push, you're relying on people to always make the right security choices. Automated, secure defaults are more reliable than expecting people to get it right every time.

Working through this project also helped me see how the 12 Factor App concepts and container security reinforce each other. Factors like explicit dependencies and environment-based configuration contribute to both good application design and a smaller attack surface.

---

## What's Next

Stage 2 uses this secured image and deploys it to Amazon ECS in Fargate mode using OpenTofu for Infrastructure as Code (IaC). I'll add task role and execution role separation, integrate with Secrets Manager, set up a private VPC, and expand the pipeline to include Checkov and Gitleaks.

The NIST control mapping is extended with each stage. By Stage 3 (EKS), I plan to have Kyverno admission policies, Falco runtime detection, and GuardDuty all mapped to controls.

The full project is on GitHub: [github.com/nisha318/container-security-progression](https://github.com/nisha318/container-security-progression)

---

*Tags: Docker, Container Security, DevSecOps, NIST 800-53, Trivy, GitHub Actions, Distroless, FastAPI, Cloud Engineering, CIS Benchmark*
