# Infrastructure Engineer → DevOps Engineer

> A roadmap for someone who already runs servers and wants to automate, containerize, and ship.

## Who This Is For

You already know how to: stand up VMs, configure Windows/Linux servers, manage DNS/AD/PKI, troubleshoot networks, patch systems. You don't need to learn "what a server is."

What a DevOps engineer adds on top: **everything becomes code, everything is automated, everything is observable, and developers can self-serve.** That's the whole job. This roadmap takes you there in phases — each phase tells you WHAT to learn, WHY it matters, and where the details live.

**Important:** Do NOT read the [curriculum](CURRICULUM.md) top to bottom. Follow the phases below. Each phase points at only the sections you need.

## How Everything Fits Together (Read This First)

```
                    ┌─────────────────────────────────────────────┐
                    │           THE DEVOPS PIPELINE                │
                    └─────────────────────────────────────────────┘

  DEVELOP                BUILD                 DEPLOY                RUN                OBSERVE
  ─────────             ─────────             ─────────             ─────────            ─────────

  [Code in Git] ──> [CI pipeline] ──> [IaC provisions] ──> [App runs on] ──> [Metrics/logs/alerts]
                       runs tests      infrastructure       containers/          feed back into
                       builds image    (cloud or k8s)       VMs/serverless       the next deploy

       │                    │                  │                  │                  │
       ▼                    ▼                  ▼                  ▼                  ▼
   Phase 1              Phase 3            Phase 2 & 4        Phase 5            Phase 6
   Git                  CI/CD              Cloud + IaC        Containers         Monitoring
```

**Every tool you will learn exists to serve one of those five boxes.** When you're lost, ask: which box does this tool serve?

| Box | Tools (learn in this order) | Why it exists |
|-----|------------------------------|---------------|
| Develop | **Git**, GitHub | Code without version control doesn't exist in a team |
| Build | **Docker**, **CI (GitHub Actions)** | Turn code into a runnable artifact automatically |
| Deploy | **Cloud (AWS)**, **Terraform** | Infrastructure written as code, reproducible, no console-clicking |
| Run | **Kubernetes**, then **OpenShift** | Run containers at scale, self-healing, declarative |
| Observe | **CloudWatch**, Prometheus/Grafana | You can't fix what you can't see |

## What's Important (And What Isn't)

**Non-negotiables — the job is impossible without these:**

1. **Git** — every company, every team, every day
2. **Linux CLI** — servers you manage will be Linux, containers are Linux
3. **One cloud (AWS)** — where the infrastructure lives
4. **Docker** — how software is packaged in 2026
5. **CI/CD** — the "Dev" meets "Ops" part; this IS the job
6. **Terraform (IaC)** — clicking in consoles is what you're leaving behind
7. **Networking fundamentals** — you mostly have this; map it to cloud VPCs

**Important — expected at most DevOps roles:**

8. **Kubernetes** — the industry default for running containers
9. **Scripting (Bash + Python)** — glue for everything
10. **Monitoring basics** — metrics, logs, alerts

**Good differentiators — learn after the above:**

11. **OpenShift (OCP)** — enterprise K8s; big in SG gov/finance. Your Windows/AD background maps well
12. **Ansible** — config management; closest tool to your PowerShell/sysadmin instincts
13. **Security scanning / DevSecOps** — your PKI background is a real edge here

**Do NOT spend time on (common traps):**

- Multi-cloud depth (GCP, OCI) — one cloud deep beats three shallow
- Service meshes (Istio, Linkerd) — later, if ever
- Serverless-first architectures — know Lambda exists, don't build your learning around it
- Every AWS service — there are 200+. You need ~15

## The Phases

Each phase: **Goal → Why → Learn → Build (proof) → Move on when.** Estimates assume ~10 hrs/week.

---

### Phase 0 — Foundations (Week 1–2)

**Goal:** Git fluency + Linux comfort + one cloud account, secured.

**Why:** Everything after this is stored in Git and runs on Linux. The cloud account is your lab.

**Learn (from [CURRICULUM.md](CURRICULUM.md)):**
- Module 0 (What Is Cloud) — skim; you know most of this
- Module 1 (Core Concepts: regions/AZs) — read properly
- Module 2 (IAM) — read properly, do all checkpoints

**Plus (not in curriculum, do these):**
- Git: `clone / add / commit / push / pull / branch / merge / pull request`. Resource: [learn git branching](https://learngitbranching.js.org/) (interactive, free, ~4 hrs)
- Linux: [Linux Journey](https://linuxjourney.com/) — "Grasshopper" track only (~6 hrs). You're Windows-strong; this closes the gap
- Bash basics: variables, loops, pipes, `grep/awk/sed` survival level

**Build (proof you're done):**
- A GitHub repo where you've made 10+ commits on a branch, opened a PR, merged it
- AWS account with: root MFA on, an IAM admin user (not root) for daily use, billing alarm at $10
- SSH into a free-tier Linux EC2 and: create a user, edit a file with vim, install nginx, open port 80 via security group

**Move on when:** you can do the three builds above without a tutorial open.

---

### Phase 1 — Cloud Core (Week 3–6)

**Goal:** You can design and build a basic, secure, multi-tier environment in AWS.

**Why:** This is the "infrastructure" part of your job moved to cloud. Most of it maps directly onto what you already know — the skill is learning the cloud's *names* for things and its *failure modes* (everything is an API, everything is billable).

**Learn (from curriculum):**
- Module 3 (Compute) — EC2, auto scaling, Lambda
- Module 4 (Storage) — S3/EBS/EFS
- Module 5 (Networking) — **spend the most time here.** VPC/subnets/route tables/security groups = your existing networking knowledge, new vocabulary
- Module 6 (Databases) — RDS vs DynamoDB, when each
- Module 10 (Cost) — read early, set billing alarms, or the labs will teach you expensively

**Your transfer map (you already know the left column):**

| You know | Cloud equivalent |
|----------|------------------|
| Physical/VLAN networks | VPC + subnets |
| Firewall rules | Security groups + NACLs |
| SAN/local disks | EBS (block), EFS (file), S3 (object) |
| AD users/groups | IAM users/groups/roles |
| Group Policy | IAM policies + AWS Organizations SCPs |
| Hyper-V/Nutanix VMs | EC2 instances |
| WSUS/patching | SSM Patch Manager |
| DNS (your PKI lab) | Route 53 |

**Build (capstone for this phase):**
- A VPC you built by hand (not the default): 2 public + 2 private subnets across 2 AZs
- A web server in a public subnet behind an Application Load Balancer
- A database (RDS free tier) in a private subnet, reachable only from the web tier
- An S3 bucket for static assets, versioned, encrypted
- Everything tagged, billing alarm active, then **tear it all down** (termination is a skill)

**Move on when:** you can draw your VPC architecture from memory and explain every arrow.

---

### Phase 2 — Infrastructure as Code (Week 7–9)

**Goal:** Rebuild your Phase 1 capstone with Terraform. No console clicking.

**Why:** This is the single biggest mindset shift from infra engineer to DevOps engineer. If it isn't in code, it doesn't exist. Code means: reviewable, repeatable, versioned, testable, and recoverable. A server you can rebuild in 5 minutes from code is worth ten servers you lovingly hand-crafted.

**Learn:**
- Terraform: init → plan → apply → destroy. State files. Variables. Outputs. Modules (light touch).
- Curriculum Module 8 (DevOps & IaC section)
- Resource: [Terraform AWS Get Started track](https://developer.hashicorp.com/terraform/tutorials/aws-get-started) (free, official, ~4 hrs)

**Build:**
- `main.tf` that creates: the VPC, subnets, route tables, security groups, ALB, EC2, RDS from Phase 1
- Parameterize: environment name, instance sizes as variables
- `terraform apply` → verify the app works → `terraform destroy` → `apply` again → still works. That cycle IS the lesson
- State in S3 + locking with DynamoDB (search: "terraform s3 backend")

**Move on when:** the idea of clicking "Create VPC" in the console feels gross.

---

### Phase 3 — CI/CD (Week 10–12)

**Goal:** Push code → tests run → artifact builds → deploys itself. This is the heartbeat of DevOps.

**Why:** Everything before this was "Ops with code." CI/CD is where "Dev" enters. When a developer asks "can you set up a pipeline?", this phase is what they mean.

**Learn:**
- GitHub Actions: workflows, jobs, steps, secrets, triggers
- What a pipeline stages look like: **lint → test → build → package → deploy → verify**
- Docker basics first (needed for the build stage): images, containers, Dockerfile, `build/run/push`
- Curriculum Module 8 (CI/CD section) + Module 3 (compute spectrum)

**Build:**
- Repo with a tiny app (e.g., a Flask or Node "hello" API)
- A Dockerfile that packages it
- GitHub Actions workflow: on push to `main` → run tests → build image → push to registry (GitHub Container Registry, free)
- Second workflow: deploy — start simple (SSH to your EC2 and `docker pull && restart`), sophisticate later
- Break the build on purpose. Watch the pipeline catch it. That's the product.

**Move on when:** you've demoed to yourself: edit code on your phone's GitHub app → merge → running app updates without you touching a server.

---

### Phase 4 — Containers & Orchestration (Week 13–17)

**Goal:** Run containers properly: Kubernetes first, then OpenShift.

**Why:** Docker alone runs one container on one host. Real systems are dozens of containers across many hosts that must survive failures. Kubernetes is the industry's answer; OpenShift is the enterprise (and SG gov/finance) flavor. **Learn vanilla K8s first** — OCP makes zero sense without it.

**Learn:**
- Docker deeper: multi-stage builds, volumes, networks, compose
- Curriculum **Module 8A (Kubernetes Deep Dive)** — all of it, all checkpoints
- Curriculum **Module 8B (OpenShift)** — after K8s clicks
- Local clusters are free: `kind` or `minikube` for K8s, `crc` (OpenShift Local) for OCP

**Build:**
- Take your Phase 3 app. Deploy it to a local K8s cluster: Deployment (3 replicas), Service, Ingress, ConfigMap for config, Secret for a fake DB password
- Kill a pod. Watch it come back. Scale to 5. Roll out a new image version. Roll it back
- Then: deploy the same app on OpenShift Local with `oc new-app` from source — no Dockerfile. Feel the difference. Hit the SCC/root gotcha and fix it (curriculum explains it)

**Move on when:** you can explain, without notes: Pod vs Deployment vs Service vs Ingress — and Route vs Ingress, BuildConfig vs CI build.

---

### Phase 5 — Observability & Reliability (Week 18–19)

**Goal:** Know when things break before users do.

**Why:** Junior engineers build things; DevOps engineers *run* things. Running = metrics, logs, alerts, and knowing your golden signals.

**Learn:**
- Curriculum Module 9 (Monitoring) + Module 7 (Security skim: KMS, secrets, CloudTrail)
- Golden signals: latency, traffic, errors, saturation

**Build:**
- CloudWatch alarm on your EC2/ALB (CPU, 5xx errors) → SNS → your email
- A dashboard for your Phase 2/3 stack
- (Stretch) Prometheus + Grafana on your local K8s cluster — the K8s-world standard

---

### Phase 6 — Portfolio & Job Assets (Week 20–22)

**Goal:** Prove it publicly.

**Build (curriculum Module 12 has architectures):**
- Project 1: static site + CI/CD (fast win, week 20)
- Project 4: your app on Kubernetes, Terraform-provisioned, with a README containing an architecture diagram
- Project 5: the OpenShift variant
- Every repo: README with what/why/architecture/how-to-run + a cost note

**Cert targets (optional but they get interviews in SG):**
1. **AWS Solutions Architect Associate** — after Phase 2
2. **CKA** — after Phase 4 (skip if budget-constrained; the portfolio matters more)
3. **RH OpenShift EX280** — only if targeting gov/finance/enterprise

## Milestones — How You Know It's Working

| Milestone | You're ready to... |
|-----------|--------------------|
| Phase 1 done | Talk cloud in an interview without bluffing |
| Phase 2 done | Call yourself "infrastructure as code" literate |
| Phase 3 done | Apply for **junior DevOps / cloud engineer** roles — seriously |
| Phase 4 done | Hold a technical conversation about containers with a senior |
| Phase 5+6 done | Interview as a credible DevOps engineer, portfolio in hand |

## The Rules

1. **Order matters.** Don't touch Kubernetes before Terraform. Don't touch Terraform before you can build a VPC by hand. Each phase's build depends on the previous one.
2. **Every phase ends with something built, not something read.** If you can't show it, you haven't done it.
3. **Free tier discipline:** billing alarm on day one, tear down after every lab, NAT Gateways are the enemy (~$32/mo each).
4. **One cloud.** AWS for everything in this roadmap. Azure/GCP knowledge transfers later in days, not weeks.
5. **Struggling for more than 2 hours on one thing = ask/search/paste the error.** DevOps is 50% reading error messages. That never changes.

## Where To Go Next

- Details, diagrams, and reading lists per topic → [CURRICULUM.md](CURRICULUM.md)
- Tick boxes as you go → [progress-tracker.md](progress-tracker.md)
- Stuck or want your capstone reviewed → ping Hermes

---
*Roadmap for Michael — infra engineer → DevOps engineer. Last updated: 2026-09-21.*
