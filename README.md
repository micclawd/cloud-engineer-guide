# Infrastructure Engineer → DevOps Engineer

> A roadmap for someone who runs servers and wants to automate, containerize, and ship — built to work in **both** public-cloud and **air-gapped** environments.

## Who This Is For

You already know how to: stand up VMs, configure Windows/Linux servers, manage DNS/AD/PKI, troubleshoot networks, patch systems, and you've run air-gapped estates (WSUS, offline media, Nutanix). You don't need to learn "what a server is."

What a DevOps engineer adds on top: **everything becomes code, everything is automated, everything is observable, and developers can self-serve.** That's the whole job — and it is 100% achievable without internet access. In fact, air-gapped DevOps is a premium skill: gov, defence, banking, and healthcare all need people who can build this inside a sealed network.

## How Everything Pieces Together (The Big Picture — Read This First)

DevOps feels like 50 random tools until you see the one thing they're all doing: **moving a change from a human's head to a running system, safely and repeatably.** Here's the whole story end to end:

```
 1. A developer changes a text file.
            │
            ▼
 2. GIT records the change.                     ── Git is a database of "what changed, who, why"
            │                                      It gives you: history, review, rollback.
            ▼
 3. CI (continuous integration) notices the     ── A robot that wakes up on every commit:
    commit and runs the pipeline:                  "compile it, test it, package it."
    lint → test → build → package                  Output: an ARTIFACT (a versioned, runnable thing).
            │
            ▼
 4. The artifact is a CONTAINER IMAGE           ── Docker packages app + its dependencies into
    stored in a REGISTRY.                          one file. The image runs identically everywhere.
            │                                      The registry is the warehouse where images live.
            ▼
 5. DEPLOY: something declares "version 1.4     ── Two things happen here:
    of the app should now be running."               a) INFRASTRUCTURE AS CODE (Terraform/Ansible)
            │                                         builds/updates the servers & networks
            ▼                                         b) the ORCHESTRATOR (Kubernetes) pulls the
 6. KUBERNETES reads the desired state               image from the registry and runs it
    and makes reality match: pulls the               across many machines, restarts it if it dies,
    image, starts containers, wires up               spreads it across failure zones.
    networking, storage, secrets.
            │
            ▼
 7. OBSERVABILITY watches the running system:   ── Metrics (numbers), logs (text), traces (paths).
    metrics, logs, alerts.                         Alerts wake humans BEFORE users complain.
            │
            ▼
 8. Humans see the dashboards and alerts,
    learn something, and go change step 1.      ── The loop is the point. DevOps = shortening
                                                     and de-risking this loop.
```

**The five boxes and their tools:**

| Box | Question it answers | Cloud tools | Air-gapped tools |
|-----|--------------------:|-------------|------------------|
| **Develop** | What changed? | Git + GitHub | Git + **GitLab CE / Gitea** (self-hosted) |
| **Build** | Does it work? Package it. | GitHub Actions, ECR | **GitLab CI / Jenkins**, Harbor (registry) |
| **Deploy** | Build the environment. | Terraform + AWS APIs | Terraform + **vSphere/Nutanix providers**, or **Ansible** direct |
| **Run** | Keep it running. | EKS / EC2 / Lambda | **Kubernetes (k3s/RKE2)** or **OpenShift disconnected install** |
| **Observe** | Is it healthy? | CloudWatch, X-Ray | **Prometheus + Grafana + Loki** |

**Key insight for air-gapped thinking:** every cloud service is just someone else's computer running software you can often run yourself. GitHub → GitLab. ECR → Harbor. CloudWatch → Prometheus. EKS → k3s. The *concepts are identical*; only the hosting changes. This roadmap teaches the concepts, with both implementations.

### The Dependency Order (Why You Learn In This Sequence)

```
Git ──► Linux ──► Networking/DNS ──► Docker ──► CI/CD ──► Kubernetes ──► OpenShift
 │                                  │            │            │
 │                                  │            │            └── needs images, needs a pipeline
 │                                  │            └── needs something to package (Docker)
 │                                  └── needs processes/networking to make sense
 └── everything is stored in git ───┘

Terraform/IaC branches off after Linux+Networking and rejoins at Deploy.
Monitoring can start any time after you have something running.
```

You cannot understand Kubernetes without Docker. You cannot understand CI/CD without Git. You cannot understand Terraform's value until you've hand-built infrastructure once. **The order is not arbitrary — each tool answers a pain the previous one creates.**

## What's Important (And What Isn't)

**Non-negotiables — the job is impossible without these:**

1. **Git** — every company, every team, every day
2. **Linux CLI** — servers you manage will be Linux, containers are Linux
3. **Infrastructure as Code (Terraform, then Ansible)** — clicking in consoles is what you're leaving behind
4. **Docker** — how software is packaged
5. **CI/CD** — the "Dev" meets "Ops" part; this IS the job
6. **Networking fundamentals** — you have this; it transfers 1:1
7. **Kubernetes** — the industry default for running containers

**Important — expected at most DevOps roles:**

8. **Scripting (Bash + Python)** — glue for everything
9. **Monitoring basics** — metrics, logs, alerts (Prometheus/Grafana in air-gap, CloudWatch in cloud)
10. **One cloud's core services** (AWS EC2/S3/IAM/VPC/RDS) — even air-gapped shops hire for cloud literacy, and interviews assume it

**Good differentiators:**

11. **OpenShift (OCP)** — enterprise K8s; dominant in SG gov/finance/defence, runs fully air-gapped (that's half its market). Your Windows/AD/PKI background maps well.
12. **Air-gapped delivery patterns** — disconnected registries, offline package mirrors, image scanning on transfer. Almost nobody can do this well; you already have the instincts.
13. **Security/DevSecOps** — your PKI background is a real edge (cert management, mTLS, signing).

**Do NOT spend time on (common traps):**

- Multi-cloud depth (GCP, OCI) — one cloud deep beats three shallow
- Service meshes (Istio, Linkerd) — later, if ever
- Serverless-first architectures — know what Lambda is, don't build your learning around it (it barely exists air-gapped anyway)
- Every AWS service — there are 200+. You need ~15
- Kubernetes certifications before you've run a cluster for a month

## Your Lab: Two Tracks

Every phase below has a **Cloud track** (AWS free tier) and an **Air-gapped track** (your own hardware/VMs, no internet dependency). Do whichever matches your situation — or both; the overlap is ~80%.

**Air-gapped lab bill of materials** (all free/open-source, all installable from offline media):

| Role | Tool | Notes |
|------|------|-------|
| Hypervisor | Your Nutanix cluster | You have this |
| Git + CI + registry | **GitLab CE** (one VM, 4 vCPU/8GB) | The all-in-one: repos, pipelines, container registry |
| Lighter alternative | **Gitea + Woodpecker CI** | If GitLab is too heavy |
| Container registry | GitLab's built-in, or **Harbor** | Harbor if you want scanning/replication |
| K8s distro | **k3s** (single binary!) or **RKE2** | Air-gap install = copy a tarball + images tar. OpenShift later via disconnected install |
| OS | Ubuntu Server LTS or RHEL/Rocky | RHEL-family prepares you for OCP |
| Packages | Your WSUS experience transfers to apt/yum mirrors (`apt-mirror`, `reposync`) | |
| Observability | Prometheus + Grafana + Loki | Static binaries / helm charts you can tar up |

**Golden rule of air-gapped DevOps:** the discipline is identical, you just *bring your own everything* — images, packages, charts, docs. You'll learn to build an "airgap bundle" (a tarball of a working dependency set), which is literally a sellable job skill.

## The Phases

Each phase: **Goal → Why → Learn → Build (proof) → Move on when.** Estimates assume ~10 hrs/week. Curriculum links point to [CURRICULUM.md](CURRICULUM.md).

---

### Phase 0 — Foundations (Week 1–2)

**Goal:** Git fluency + Linux comfort + a working lab (cloud account OR air-gapped VMs).

**Why:** Everything after this is stored in Git and runs on Linux. The lab is where you break things safely.

**Learn:**
- Git: `clone / add / commit / push / pull / branch / merge / pull request`. Resource: [Learn Git Branching](https://learngitbranching.js.org/) (interactive, ~4 hrs)
- Linux: [Linux Journey](https://linuxjourney.com/) "Grasshopper" track (~6 hrs). You're Windows-strong; this closes the gap
- Bash survival: variables, loops, pipes, `grep/awk/sed`
- Curriculum: Module 1 (regions/AZs — cloud track), Module 2 (IAM)

**Build:**
- **Both tracks:** a git repo with 10+ commits, a branch, a PR/code review, a merge
- **Cloud track:** AWS account with root MFA, IAM admin user, $10 billing alarm. SSH to a free-tier EC2: create a user, edit a file in vim, install nginx, open port 80
- **Air-gapped track:** 3 VMs on Nutanix (Ubuntu/Rocky). Install **Gitea or GitLab CE** on one. Push a repo to it from your workstation over the LAN. SSH into all three, set hostname/hosts file/NTP (you know why NTP matters — your PKI lab taught you)

**Move on when:** the builds are done without a tutorial open.

---

### Phase 1 — Infrastructure Core (Week 3–6)

**Goal:** Design and build a secure multi-tier environment. Cloud track = AWS. Air-gapped track = same architecture on your VMs.

**Why:** This is your existing job, re-expressed. Most of it maps 1:1 onto what you know — the new parts are the vocabulary and the failure modes (in cloud, everything is an API and everything is billable; in air-gap, everything is a VM and everything is your problem).

**Learn:**
- Curriculum: Module 5 (Networking — **spend the most time here**), Module 3 (Compute), Module 4 (Storage), Module 6 (Databases), Module 10 (Cost — cloud track)
- Air-gapped track reads the same modules for *concepts*, then maps them: VPC → your VLANs, EC2 → Nutanix VMs, ALB → an nginx/HAProxy VM, RDS → a PostgreSQL VM

**Your transfer map (you already know the left column):**

| You know | Cloud equivalent | Air-gapped equivalent |
|----------|------------------|----------------------|
| VLANs/subnets | VPC + subnets | Your existing VLANs |
| Firewall rules | Security groups + NACLs | nftables/firewalld/NSX |
| SAN/local disks | EBS, EFS, S3 | NFS server, MinIO (S3-compatible!) |
| AD users/groups | IAM users/roles | LDAP / FreeIPA / local |
| Group Policy | IAM policies, SCPs | Ansible (Phase 2) |
| VMs on Nutanix | EC2 | …VMs on Nutanix |
| WSUS/patching | SSM Patch Manager | apt-mirror/reposync + Ansible |
| DNS (your PKI lab) | Route 53 | Your existing DNS / PowerDNS |
| Hardware load balancer | ALB/NLB | HAProxy / nginx |

**Build (capstone — same architecture, both tracks):**
- A 3-tier setup: load balancer → app tier → database tier, in separate network segments
- DB reachable ONLY from app tier (security group or firewall rule — prove it with a failed connection from elsewhere)
- Object storage: S3 bucket (cloud) or **MinIO** (air-gapped — it's S3 API-compatible, so skills transfer both ways)
- **Cloud track:** hand-build the VPC (2 public + 2 private subnets, 2 AZs), then tear it all down
- **Air-gapped track:** document the build as a runbook — the thing Phase 2 will automate

**Move on when:** you can draw your 3-tier architecture from memory and explain every arrow, in both vocabularies.

---

### Phase 2 — Infrastructure as Code (Week 7–9)

**Goal:** Rebuild your Phase 1 capstone from code. No clicking.

**Why:** The single biggest mindset shift from infra engineer to DevOps engineer. If it isn't in code, it doesn't exist. Code means reviewable, repeatable, versioned, and recoverable. A server you can rebuild in 5 minutes from code beats ten hand-crafted ones.

**Learn:**
- Terraform: init → plan → apply → destroy. State, variables, outputs
- Curriculum: Module 8 (IaC section)
- **Air-gapped track addition:** Terraform works fine offline — providers are just binaries you copy over. Target the **Nutanix provider** (`nutanix/nutanix`) or practice against a local provider. Also start **Ansible**: inventory, playbooks, roles — the natural air-gap config tool; it'll feel like PowerShell's cousin
- Resource: [Terraform Get Started](https://developer.hashicorp.com/terraform/tutorials/aws-get-started) (cloud) / [Ansible community docs](https://docs.ansible.com/) (air-gap)

**Build:**
- **Cloud track:** `main.tf` creating the Phase 1 VPC + ALB + EC2 + RDS. `apply` → works → `destroy` → `apply` → still works. State in S3 + DynamoDB lock
- **Air-gapped track:** Terraform (or Ansible playbooks) that provision/configure your 3 VM tiers: one role for "app server" (installs nginx, drops config, opens firewall), one for "db server" (installs PostgreSQL, binds to the app segment only). Run from clean VMs → working stack, twice in a row

**Move on when:** clicking "create VM" in a console feels gross, and your air-gap runbook from Phase 1 is now code.

---

### Phase 3 — CI/CD (Week 10–12)

**Goal:** Push code → tests run → artifact builds → deploys itself. The heartbeat of DevOps.

**Why:** Everything before was "Ops with code." CI/CD is where "Dev" enters. When someone asks "can you set up a pipeline?", this phase is what they mean.

**Learn:**
- Pipeline stages: **lint → test → build → package → deploy → verify**
- Docker basics (needed for the build stage): images, containers, Dockerfile, `build/run/push`
- **Cloud track:** GitHub Actions (workflows, jobs, secrets, triggers) + push to a registry
- **Air-gapped track:** **GitLab CI** (`.gitlab-ci.yml` — nearly identical concepts to Actions) + a runner VM + GitLab's built-in container registry. The Docker-in-air-gap pattern: build on an online machine, `docker save` → transfer → `docker load`, or run your own registry and mirror images through your transfer process
- Curriculum: Module 8 (CI/CD section), Module 3 (compute spectrum)

**Build:**
- A tiny app (Flask/Node "hello" API) + its Dockerfile
- **Cloud track:** GitHub Actions: push to main → test → build image → push to GHCR → deploy (SSH to EC2, `docker pull && restart`)
- **Air-gapped track:** GitLab CI: push to main → test → build image → push to internal registry → deploy to your app VM via the runner
- Break the build on purpose. Watch the pipeline catch it. That red X is the product.

**Move on when:** you've watched a commit travel from your keyboard to a running container without touching a server, on your chosen track.

---

### Phase 4 — Containers & Orchestration (Week 13–17)

**Goal:** Run containers properly: Kubernetes, then OpenShift.

**Why:** Docker alone runs one container on one host. Real systems are dozens of containers across many hosts that must survive failures. Kubernetes is the industry answer; OpenShift is the enterprise/gov flavor — and **OCP's disconnected/air-gapped install is a first-class, documented workflow**, which is exactly why it's everywhere in SG gov and finance.

**Learn:**
- Docker deeper: multi-stage builds, volumes, networks, compose
- Curriculum **Module 8A (Kubernetes Deep Dive)** — all of it, all checkpoints
- Curriculum **Module 8B (OpenShift)** — after K8s clicks
- **Air-gapped track specifics:** install **k3s** (single binary + an images tarball — the friendliest offline K8s that exists) or RKE2 (the one US gov uses). OpenShift later via its disconnected install (mirror registry + `oc mirror`) — advanced, flag it for month 5+
- Local/easy options: `kind`/`minikube` for K8s, `crc` (OpenShift Local) for OCP

**Build:**
- Your Phase 3 app on K8s: Deployment (3 replicas), Service, Ingress, ConfigMap, Secret
- Kill a pod → it comes back. Scale to 5. Roll out a new image. Roll back
- **Air-gapped extra credit:** stand up an internal registry (Harbor or GitLab's), have k3s pull ONLY from it (configure `registries.yaml`), simulating a true disconnected cluster
- Then OpenShift Local: `oc new-app` from source, expose a Route, hit the SCC root-user gotcha, fix it (curriculum explains)

**Move on when:** you can explain without notes: Pod vs Deployment vs Service vs Ingress — and Route vs Ingress, BuildConfig vs CI build — and how an image gets from "the internet" into a sealed cluster.

---

### Phase 5 — Observability & Reliability (Week 18–19)

**Goal:** Know when things break before users do.

**Why:** Junior engineers build things; DevOps engineers *run* things. Running = metrics, logs, alerts, golden signals.

**Learn:**
- Golden signals: latency, traffic, errors, saturation
- Curriculum: Module 9 (Monitoring concepts), Module 7 (Security skim: KMS/secrets — in air-gap think: Vault or sealed-secrets)
- **Cloud track:** CloudWatch metrics/alarms/SNS
- **Air-gapped track:** **Prometheus + Grafana + Loki + Alertmanager** — the standard stack, fully offline-friendly, and what most on-prem shops actually run

**Build:**
- Dashboard + alert on your Phase 3/4 stack (CPU, app 5xx rate, disk) → alert reaches you (email/webhook/Teams)
- Air-gapped track: Prometheus scraping your k3s cluster, Grafana dashboard, one alert rule firing on purpose

---

### Phase 6 — Portfolio & Job Assets (Week 20–22)

**Goal:** Prove it publicly (or internally, if your work is sensitive).

**Build (curriculum Module 12 has architectures):**
- Project 1: static site + CI/CD (fast win)
- Project 4: app on Kubernetes, Terraform/Ansible-provisioned, README with architecture diagram
- Project 5: the OpenShift variant
- **Your differentiator project:** document a full **air-gapped delivery** — GitLab + internal registry + k3s + Prometheus, with a `make airgap-bundle` style script that produces the transfer tarball. Almost no junior candidate can show this. You can.
- Every repo: README with what/why/architecture/how-to-run + cost/resource note

**Cert targets (optional, they get interviews in SG):**
1. **AWS Solutions Architect Associate** — after Phase 2 (cloud literacy proof)
2. **CKA** — after Phase 4
3. **RH OpenShift EX280** — if targeting gov/finance/defence (where air-gapped OCP lives)

## Milestones — How You Know It's Working

| Milestone | You're ready to... |
|-----------|--------------------|
| Phase 1 done | Talk infrastructure-as-concept in an interview without bluffing |
| Phase 2 done | Call yourself "infrastructure as code" literate |
| Phase 3 done | Apply for **junior DevOps / cloud engineer** roles — seriously |
| Phase 4 done | Hold a technical conversation about containers with a senior; air-gapped K8s shops will notice you |
| Phase 5+6 done | Interview as a credible DevOps engineer, portfolio in hand — including the niche "secure/air-gapped platform" roles nobody else can fill |

## The Rules

1. **Order matters.** Git before CI/CD. Docker before Kubernetes. Hand-built infrastructure before Terraform. Each phase's build depends on the previous one.
2. **Every phase ends with something built, not something read.** If you can't show it, you haven't done it.
3. **Pick a track per phase and finish it.** Cloud track teaches vocabulary employers screen for; air-gapped track teaches depth almost nobody has. Doing Phase 1 cloud + Phase 2–4 air-gapped is a strong combination.
4. **Free tier discipline (cloud track):** billing alarm on day one, tear down after every lab, NAT Gateways are the enemy (~$32/mo each).
5. **Air-gap discipline (air-gapped track):** everything you need enters through a documented transfer process. Practice the bundle → transfer → load loop; that loop is the job.
6. **Struggling >2 hours on one thing = ask/search/paste the error.** DevOps is 50% reading error messages. That never changes.

## Where To Go Next

- Details, diagrams, and reading lists per topic → [CURRICULUM.md](CURRICULUM.md)
- Tick boxes as you go → [progress-tracker.md](progress-tracker.md)
- Stuck or want your capstone reviewed → ping Hermes

---
*Roadmap for Michael — infra engineer → DevOps engineer, cloud + air-gapped tracks. Last updated: 2026-09-21.*
