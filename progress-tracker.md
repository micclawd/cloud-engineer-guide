# Progress Tracker

> Work through the roadmap phases in [README.md](README.md). Tick these as you go. Each phase has cloud and air-gapped variants — tick whichever track you did.

## Phase 0 — Foundations
- [ ] Git: 10+ commits, a branch, a PR, a merge in a practice repo
- [ ] Linux Journey "Grasshopper" track done
- [ ] Bash survival: variables, loops, pipes, grep
- [ ] (Cloud) AWS account: root MFA, IAM admin user, $10 billing alarm
- [ ] (Cloud) SSH'd into EC2: created user, vim'd a file, installed nginx, opened port 80
- [ ] (Air-gap) 3 VMs on Nutanix (Ubuntu/Rocky)
- [ ] (Air-gap) Gitea or GitLab CE installed on one VM, pushed a repo from workstation over LAN
- [ ] (Air-gap) Hostname/hosts/NTP set on all three VMs

## Phase 1 — Infrastructure Core
- [ ] (Cloud) Hand-built VPC: 2 public + 2 private subnets, 2 AZs
- [ ] (Cloud) Web server behind ALB; RDS in private subnet reachable only from app tier (proved with a failed connection)
- [ ] (Cloud) S3 bucket versioned + encrypted; everything tagged; torn down
- [ ] (Air-gap) 3-tier build on VMs: LB (nginx/HAProxy) → app → DB in separate segments
- [ ] (Air-gap) MinIO running as S3-compatible object store
- [ ] (Air-gap) Build documented as a runbook (Phase 2 input)
- [ ] Can draw the 3-tier architecture from memory in both vocabularies

## Phase 2 — Infrastructure as Code
- [ ] (Cloud) Phase 1 capstone rebuilt entirely in Terraform; apply→destroy→apply verified
- [ ] (Cloud) Remote state in S3 + DynamoDB locking
- [ ] (Air-gap) Terraform Nutanix provider OR Ansible roles provisioning the 3 tiers
- [ ] (Air-gap) From clean VMs → working stack, twice in a row
- [ ] Console-clicking now feels gross

## Phase 3 — CI/CD
- [ ] Dockerized a small app (own Dockerfile)
- [ ] (Cloud) GitHub Actions: push → test → build → push image to GHCR → deploy
- [ ] (Air-gap) GitLab CI: push → test → build → push to internal registry → deploy via runner
- [ ] (Air-gap) Practiced docker save → transfer → docker load pattern
- [ ] Broke the build on purpose and watched the pipeline catch it

## Phase 4 — Containers & Orchestration
- [ ] App on K8s: Deployment + Service + Ingress + ConfigMap + Secret
- [ ] Killed a pod, watched self-heal; scaled; rolled out and rolled back
- [ ] (Air-gap) k3s installed offline (binary + images tarball)
- [ ] (Air-gap) k3s pulls ONLY from internal registry (registries.yaml) — simulated disconnected cluster
- [ ] Same app on OpenShift Local via `oc new-app`
- [ ] Hit and fixed the SCC root-user gotcha
- [ ] Can explain Pod/Deployment/Service/Ingress + Route/BuildConfig + how images enter a sealed cluster

## Phase 5 — Observability
- [ ] (Cloud) CloudWatch alarm → SNS → email working
- [ ] (Air-gap) Prometheus + Grafana + Loki + Alertmanager running on VMs
- [ ] (Air-gap) Prometheus scraping k3s; one alert rule fired on purpose
- [ ] Dashboard for the Phase 3/4 stack

## Phase 6 — Portfolio
- [ ] Project 1: static site + CI/CD deployed
- [ ] Project 4: K8s deployment with Terraform/Ansible, README + diagram
- [ ] Project 5: OpenShift variant
- [ ] Differentiator: full air-gapped delivery documented (GitLab + registry + k3s + Prometheus + transfer-bundle script)
- [ ] Every repo has what/why/architecture/how-to-run

## Certifications (optional)
- [ ] AWS Solutions Architect Associate (after Phase 2)
- [ ] CKA (after Phase 4)
- [ ] Red Hat EX280 OpenShift (if targeting gov/finance/defence)

---
<details><summary>Old per-module checklist (curriculum reference)</summary>

## Phase 0 — Foundations
- [ ] Git: 10+ commits, a branch, a PR, a merge in a practice repo
- [ ] Linux Journey "Grasshopper" track done
- [ ] Bash survival: variables, loops, pipes, grep
- [ ] AWS account: root MFA, IAM admin user, $10 billing alarm
- [ ] SSH'd into EC2: created user, vim'd a file, installed nginx, opened port 80

## Phase 1 — Cloud Core
- [ ] Hand-built VPC: 2 public + 2 private subnets, 2 AZs
- [ ] Web server behind ALB in public subnet
- [ ] RDS in private subnet, reachable only from web tier
- [ ] S3 bucket: versioned, encrypted
- [ ] Everything tagged, then torn down
- [ ] Can draw the architecture from memory

## Phase 2 — Infrastructure as Code
- [ ] Phase 1 capstone rebuilt entirely in Terraform
- [ ] Variables for environment/instance sizes
- [ ] apply → destroy → apply cycle verified working
- [ ] Remote state in S3 + DynamoDB locking
- [ ] Console-clicking now feels gross

## Phase 3 — CI/CD
- [ ] Dockerized a small app (own Dockerfile)
- [ ] GitHub Actions: push → test → build → push image to registry
- [ ] Auto-deploy on merge to main
- [ ] Broke the build on purpose and watched the pipeline catch it

## Phase 4 — Containers & Orchestration
- [ ] App on local K8s: Deployment + Service + Ingress + ConfigMap + Secret
- [ ] Killed a pod, watched self-heal; scaled; rolled out and rolled back
- [ ] Same app on OpenShift Local via `oc new-app`
- [ ] Hit and fixed the SCC root-user gotcha
- [ ] Can explain Pod/Deployment/Service/Ingress + Route/BuildConfig without notes

## Phase 5 — Observability
- [ ] CloudWatch alarm → SNS → email working
- [ ] Dashboard for the Phase 2/3 stack
- [ ] (Stretch) Prometheus + Grafana on local K8s

## Phase 6 — Portfolio
- [ ] Project 1: static site + CI/CD deployed
- [ ] Project 4: K8s deployment with Terraform, README + diagram
- [ ] Project 5: OpenShift variant
- [ ] Every repo has what/why/architecture/how-to-run

## Certifications (optional)
- [ ] AWS Solutions Architect Associate (after Phase 2)
- [ ] CKA (after Phase 4)
- [ ] Red Hat EX280 OpenShift (if targeting gov/finance)


## Module 0: What Is Cloud?
- [ ] Read the module
- [ ] Drew IaaS/PaaS/SaaS from memory
- [ ] Signed up for AWS, Azure, GCP free tiers

## Module 1: Core Concepts
- [ ] Read the module
- [ ] Listed 3 regions and their AZs
- [ ] Explained AZ replication to rubber duck

## Module 2: IAM
- [ ] Created IAM user
- [ ] Enabled MFA on root
- [ ] Wrote a custom policy JSON

## Module 3: Compute
- [ ] Launched and terminated EC2
- [ ] Created Auto Scaling Group
- [ ] Wrote a Lambda function

## Module 4: Storage
- [ ] Created S3 bucket with versioning
- [ ] Uploaded, deleted, restored a file
- [ ] Set lifecycle rule

## Module 5: Networking
- [ ] Created VPC with public/private subnets
- [ ] Launched EC2 in both subnets
- [ ] Added NAT Gateway
- [ ] Configured security group

## Module 6: Databases
- [ ] Created RDS instance
- [ ] Connected from EC2
- [ ] Created DynamoDB table

## Module 7: Security
- [ ] Created KMS key
- [ ] Stored secret in Secrets Manager
- [ ] Enabled CloudTrail

## Module 8: DevOps
- [ ] Installed Terraform
- [ ] Created S3 bucket with Terraform
- [ ] Wrote GitHub Actions workflow
- [ ] Built and pushed Docker image

## Module 8A: Kubernetes
- [ ] Installed kubectl + local cluster (kind/minikube)
- [ ] Deployed hello app, scaled it, killed a pod, watched self-heal
- [ ] Exposed with Service + Ingress
- [ ] Created ConfigMap + Secret, mounted into pod
- [ ] Installed/upgraded/rolled back a Helm chart
- [ ] Wrote a NetworkPolicy

## Module 8B: OpenShift
- [ ] Installed OpenShift Local or joined Developer Sandbox
- [ ] Deployed an app with `oc new-app` from GitHub source
- [ ] Exposed a Route and hit the URL
- [ ] Triggered a rebuild with `oc start-build`
- [ ] Hit the SCC root-user gotcha and fixed it
- [ ] Installed an Operator from OperatorHub

## Module 9: Monitoring
- [ ] Created CloudWatch alarm
- [ ] Sent test SNS notification
- [ ] Wrote Log Insights query

## Module 10: Cost
- [ ] Set billing alarm
- [ ] Used pricing calculator
- [ ] Tagged resources

## Module 11: Primary Cloud
- [ ] Picked primary cloud
- [ ] Launched same app in 2 clouds

## Module 12: Portfolio
- [ ] Project 1 deployed (static site + CI/CD)
- [ ] Project 2 deployed (three-tier app)
- [ ] Project 3 deployed (serverless API)
- [ ] Project 4 deployed (Kubernetes)
- [ ] Project 5 deployed (OpenShift)
- [ ] All repos have README + diagram

## Certifications
- [ ] AWS Cloud Practitioner (optional)
- [ ] AWS Solutions Architect Associate
- [ ] AWS SysOps Administrator Associate
- [ ] CKA (optional, after Module 8A)
- [ ] Red Hat EX280 OpenShift (optional, after Module 8B)
</details>
