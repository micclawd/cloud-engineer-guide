# Curriculum — Topic Reference

> Detailed topic-by-topic reference (diagrams, reading lists, labs). Start with the roadmap in [README.md](README.md), come here when a phase tells you to.

## How to Use This Guide

1. Work through the modules in order.
2. Type out every command and draw every diagram by hand once.
3. Check off items in `progress-tracker.md`.
4. Do the labs. Reading without doing is not learning.

## Module Index

- [Module 0: What Is Cloud?](#module-0-what-is-cloud)
- [Module 1: Core Concepts](#module-1-core-concepts)
- [Module 2: Identity & Access Management](#module-2-identity--access-management)
- [Module 3: Compute](#module-3-compute)
- [Module 4: Storage](#module-4-storage)
- [Module 5: Networking](#module-5-networking)
- [Module 6: Databases](#module-6-databases)
- [Module 7: Security & Compliance](#module-7-security--compliance)
- [Module 8: DevOps & Infrastructure as Code](#module-8-devops--infrastructure-as-code)
- [Module 8A: Kubernetes Deep Dive](#module-8a-kubernetes-deep-dive)
- [Module 8B: OpenShift (OCP)](#module-8b-openshift-ocp)
- [Module 9: Monitoring & Observability](#module-9-monitoring--observability)
- [Module 10: Cost Optimization](#module-10-cost-optimization)
- [Module 11: Pick a Primary Cloud](#module-11-pick-a-primary-cloud)
- [Module 12: Real-Project Portfolio](#module-12-real-project-portfolio)

## Reading List (Start Here)

- [AWS Getting Started](https://aws.amazon.com/getting-started/)
- [Azure Fundamentals](https://learn.microsoft.com/en-us/training/paths/azure-fundamentals/)
- [GCP Free Tier](https://cloud.google.com/free)

---

## Module 0: What Is Cloud?

Cloud computing = renting computing power, storage, and networking over the internet instead of buying physical servers.

### The Pizza Analogy

| On-Premises | Cloud |
|-------------|-------|
| Buy oven, flour, cheese, make pizza at home | Order pizza delivered |
| High upfront cost, full control | Pay per slice, someone else manages kitchen |

### ASCII Illustration: Traditional vs Cloud

```
Traditional On-Premises:
[Your Laptop] ---> [Your Router] ---> [Your Server Rack]
                                         |
                                    [You maintain:
                                     power, cooling,
                                     hardware, OS,
                                     patches, security]

Cloud:
[Your Laptop] ---> [Internet] ---> [Cloud Provider Data Center]
                                         |
                                    [They maintain:
                                     hardware, power,
                                     cooling, physical security]
                                    [You manage:
                                     OS, apps, data, access]
```

### Service Models (IaaS, PaaS, SaaS)

| Model | You Manage | Provider Manages | Example |
|-------|-----------|------------------|---------|
| IaaS  | OS, apps, data | Hardware, hypervisor | AWS EC2, Azure VM |
| PaaS  | Apps, data | OS, hardware, runtime | AWS Elastic Beanstalk, Azure App Service |
| SaaS  | Nothing | Everything | Gmail, Office 365 |

### Reading / Watching

- [AWS: What is Cloud Computing?](https://aws.amazon.com/what-is-cloud-computing/)
- [Azure: What is cloud computing?](https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-cloud-computing/)

### Hands-On Checkpoint

- [ ] Sign up for AWS Free Tier (12 months free)
- [ ] Sign up for Azure Free Account ($200 credit)
- [ ] Sign up for GCP Free Tier ($300 credit)
- [ ] Draw the IaaS/PaaS/SaaS diagram from memory

---

## Module 1: Core Concepts

### Regions, Availability Zones (AZs), Edge Locations

```
[Region: us-east-1]
  |
  +-- [AZ a] (Data Center 1)
  +-- [AZ b] (Data Center 2)
  +-- [AZ c] (Data Center 3)

[Edge Location] = CDN cache closer to users (CloudFront, Azure CDN)
```

- **Region:** Geographic area (e.g., Singapore = ap-southeast-1).
- **AZ:** Isolated data center within a region. Replicate across AZs for high availability.
- **Edge:** Cache content close to users for low latency.

### Shared Responsibility Model

| Layer | On-Prem | IaaS | PaaS | SaaS |
|-------|---------|------|------|------|
| Data | You | You | You | Provider |
| App | You | You | Provider | Provider |
| OS | You | You | Provider | Provider |
| Virtualization | You | Provider | Provider | Provider |
| Hardware | You | Provider | Provider | Provider |

### Reading

- [AWS Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model/)
- [Azure Shared Responsibility](https://learn.microsoft.com/en-us/azure/security/fundamentals/shared-responsibility)

### Hands-On Checkpoint

- [ ] List 3 AWS regions and their AZ counts from the AWS docs
- [ ] Explain to a rubber duck why you would put a database in a different AZ than your web server

---

## Module 2: Identity & Access Management (IAM)

IAM = who can do what in your cloud account.

### Core Objects

```
[User]  = A person or app (e.g., michael, ci-cd-bot)
[Group] = Collection of users (e.g., Admins, Developers)
[Role]  = Temporary permissions for a service or user (e.g., EC2 instance role)
[Policy]= JSON document defining permissions (attached to user/group/role)
```

### Policy JSON Anatomy

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::my-bucket/*"
  }]
}
```

### Best Practices

1. **Never use root account** for daily work.
2. **Enable MFA** on root and all IAM users.
3. **Least privilege:** Only grant the permissions needed.
4. **Use roles** for EC2/Lambda, not access keys.

### ASCII: IAM Flow

```
[User: michael] --(assumes)--> [Role: Developer] --(has policy)--> [Allow: s3:GetObject on my-bucket]
         |
         +-- MFA required
```

### Reading

- [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
- [Azure RBAC](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview)

### Hands-On Checkpoint

- [ ] Create an IAM user with programmatic access and console access
- [ ] Attach a policy allowing read-only S3 access
- [ ] Enable MFA on root and your new user
- [ ] Write a policy JSON that allows only `ec2:DescribeInstances`

---

## Module 3: Compute

### The Compute Spectrum

```
[Most control] <----------------------------------------> [Least control]

EC2 / Azure VM    |    ECS / AKS    |    Lambda / Azure Functions
Virtual Machines  |    Containers   |    Serverless Functions
You patch OS      |    You patch    |    You write code only
                  |    container    |
                  |    image        |
```

### When to Use What

| Use Case | Service | Why |
|----------|---------|-----|
| Legacy app, full OS control | EC2 / Azure VM | You need Windows or specific kernel modules |
| Microservices, portable | ECS / AKS | Docker containers, orchestrated |
| Event-driven, short jobs | Lambda / Functions | No server management, pay per invocation |

### ASCII: Auto Scaling

```
[Load Balancer]
      |
  +---+---+---+
  |   |   |   |
[EC2][EC2][EC2]  <-- scales out when CPU > 70%
  |   |   |
  +---+---+
      |
[Auto Scaling Group]
```

### Reading

- [AWS EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [Azure VM Overview](https://learn.microsoft.com/en-us/azure/virtual-machines/overview)
- [AWS Lambda](https://aws.amazon.com/lambda/)

### Hands-On Checkpoint

- [ ] Launch a t2.micro EC2 instance, SSH in, install nginx
- [ ] Create an Auto Scaling Group with min=1, max=3
- [ ] Write a Lambda function that returns "Hello World"
- [ ] Terminate the EC2 instance (avoid charges)

---

## Module 4: Storage

### Storage Types

| Type | Use Case | AWS | Azure | GCP |
|------|----------|-----|-------|-----|
| Object | Files, backups, static websites | S3 | Blob Storage | Cloud Storage |
| Block | VM hard drives | EBS | Managed Disks | Persistent Disk |
| File | Shared folders | EFS | Azure Files | Filestore |

### S3 Bucket Anatomy

```
[Bucket: my-app-backups]
  |
  +-- /2026/09/21/backup.tar.gz  (object)
  +-- /2026/09/20/backup.tar.gz
  +-- /logs/access.log
```

- **Bucket:** Global namespace, unique name.
- **Object:** File + metadata (max 5TB).
- **Versioning:** Keep old versions.
- **Lifecycle:** Auto-delete or archive after X days.

### ASCII: S3 Durability

```
Your file ---> [S3] ---> Copied to 3+ AZs
                    |
                    +-- 99.999999999% durability (11 nines)
                    +-- You lose it only if 3 data centers burn simultaneously
```

### Reading

- [AWS S3 User Guide](https://docs.aws.amazon.com/s3/)
- [Azure Blob Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/)

### Hands-On Checkpoint

- [ ] Create an S3 bucket with versioning enabled
- [ ] Upload a file, download it, delete it, restore the version
- [ ] Set a lifecycle rule to move files to Glacier after 30 days
- [ ] Make a static website from an S3 bucket

---

## Module 5: Networking

### VPC = Your Private Cloud

```
[Region: ap-southeast-1]
  |
  +-- [VPC: 10.0.0.0/16]
        |
        +-- [Public Subnet: 10.0.1.0/24]  <-- Internet Gateway
        |     |
        |     +-- [EC2: Web Server] (public IP)
        |
        +-- [Private Subnet: 10.0.2.0/24] <-- NAT Gateway
              |
              +-- [EC2: Database] (no public IP)
```

### Key Components

| Component | What It Does |
|-----------|-------------|
| VPC | Your isolated network in the cloud |
| Subnet | Slice of VPC IP range (public or private) |
| Route Table | Rules for where traffic goes |
| Security Group | Firewall at the instance level (stateful) |
| NACL | Firewall at the subnet level (stateless) |
| Internet Gateway | Allows public internet access |
| NAT Gateway | Lets private instances reach internet (outbound only) |

### Security Group vs NACL

```
Security Group (instance level):
  - Stateful: return traffic automatically allowed
  - Allow rules only
  - Applied to ENI (network interface)

NACL (subnet level):
  - Stateless: must explicitly allow return traffic
  - Allow + Deny rules
  - Applied to subnet
```

### Reading

- [AWS VPC User Guide](https://docs.aws.amazon.com/vpc/)
- [Azure Virtual Network](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)

### Hands-On Checkpoint

- [ ] Create a VPC with public and private subnets
- [ ] Launch an EC2 in public subnet, verify internet access
- [ ] Launch an EC2 in private subnet, verify no direct internet
- [ ] Add a NAT Gateway, verify private instance can now reach internet
- [ ] Create a security group that allows only port 80 from your IP

---

## Module 6: Databases

### SQL vs NoSQL

| SQL (Relational) | NoSQL (Non-relational) |
|------------------|------------------------|
| Tables with rows/columns | Key-value, document, graph |
| ACID transactions | Eventual consistency |
| Vertical scaling | Horizontal scaling |
| MySQL, PostgreSQL | DynamoDB, MongoDB, Redis |

### AWS Database Services

```
[EC2 App] ---> [RDS MySQL]  (managed relational)
     |
     +---> [DynamoDB]      (managed NoSQL, serverless)
     |
     +---> [ElastiCache]   (Redis/Memcached, in-memory)
```

### RDS vs DynamoDB

| Feature | RDS | DynamoDB |
|---------|-----|----------|
| Schema | Fixed | Flexible |
| Scaling | Vertical (bigger instance) | Horizontal (more partitions) |
| Pricing | Per hour + storage | Per request + storage |
| Use case | Complex queries, joins | High-throughput, simple lookups |

### Reading

- [AWS RDS User Guide](https://docs.aws.amazon.com/rds/)
- [AWS DynamoDB Developer Guide](https://docs.aws.amazon.com/dynamodb/)

### Hands-On Checkpoint

- [ ] Create an RDS MySQL instance in a private subnet
- [ ] Connect from an EC2 instance using mysql client
- [ ] Create a DynamoDB table with a partition key
- [ ] Write a Lambda function that reads from DynamoDB

---

## Module 7: Security & Compliance

### Encryption Everywhere

| State | How | Service |
|-------|-----|---------|
| At rest | Encrypt disk | EBS encryption, S3 SSE |
| In transit | TLS/SSL | ACM, Load Balancer |
| In use | Application-level | KMS, Azure Key Vault |

### KMS = Key Management Service

```
[Your Data] --(encrypt with KMS key)--> [Encrypted Data]
                     |
              [KMS Key Policy]
                     |
              [Who can decrypt?]
```

### Secrets Management

- **Never** hardcode passwords in code.
- Use AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault.
- Rotate secrets automatically.

### Compliance Frameworks

| Framework | What It Covers |
|-----------|---------------|
| SOC 2 | Security, availability, confidentiality |
| ISO 27001 | Information security management |
| PCI DSS | Credit card data |
| HIPAA | Healthcare data |
| GDPR | EU data privacy |

### Reading

- [AWS KMS Developer Guide](https://docs.aws.amazon.com/kms/)
- [Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/)

### Hands-On Checkpoint

- [ ] Create a KMS key and encrypt an S3 object
- [ ] Store a database password in Secrets Manager
- [ ] Retrieve the secret from a Lambda function
- [ ] Enable CloudTrail to log all API calls

---

## Module 8: DevOps & Infrastructure as Code

### Infrastructure as Code (IaC)

Instead of clicking in the console, write code:

```hcl
# main.tf (Terraform)
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "WebServer"
  }
}
```

### CI/CD Pipeline

```
[Git Push] ---> [CodeBuild / GitHub Actions] ---> [Tests] ---> [Deploy to EC2/Lambda]
```

### Container Orchestration (teaser — full deep dive in Module 8A)

```
[Docker Container] ---> [Kubernetes / OpenShift] ---> [Auto-scaled, load-balanced, self-healing]
```

Managed K8s services: AWS EKS, Azure AKS, GCP GKE, Red Hat OpenShift (OCP). Covered in Modules 8A and 8B.

### Reading

- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [AWS CloudFormation User Guide](https://docs.aws.amazon.com/cloudformation/)
- [Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)

### Hands-On Checkpoint

- [ ] Install Terraform and write a .tf file that creates an S3 bucket
- [ ] Run `terraform plan` and `terraform apply`
- [ ] Destroy with `terraform destroy`
- [ ] Write a GitHub Actions workflow that runs tests on push
- [ ] Build a Docker image and push to ECR (Elastic Container Registry)

---

## Module 8A: Kubernetes Deep Dive

Kubernetes (K8s) = a scheduler for containers. You declare desired state in YAML; K8s keeps the cluster matching that state.

### The Mental Model

```
[kubectl apply -f app.yaml]
         |
         v
[API Server] ---> [etcd: desired state]
         |
         v
[Scheduler] ---> picks a Node
         |
         v
[kubelet on Node] ---> pulls image, starts container
         |
         v
[Controller Manager] ---> watches and self-heals
```

### Core Objects

| Object | What It Is | Analogy |
|--------|-----------|---------|
| Pod | Smallest unit. 1+ containers sharing network/storage | One apartment |
| Deployment | Manages N identical pods, rolling updates | Property manager |
| ReplicaSet | Keeps N pods running | Security guard replacing tenants |
| Service | Stable IP/DNS for a set of pods | Building front desk |
| Ingress | HTTP routing into the cluster | Mail room |
| ConfigMap | Non-secret config key-values | Bulletin board |
| Secret | Base64-encoded secrets | Locked mailbox |
| Namespace | Virtual cluster inside the cluster | Building floor |
| PersistentVolume (PV) | Cluster storage resource | Storage unit |
| PersistentVolumeClaim (PVC) | Request for storage | Tenant renting the unit |
| StatefulSet | Pods with stable identity (DBs) | Named, reserved apartments |
| DaemonSet | One pod per node (agents, log shippers) | One janitor per floor |

### ASCII: A Real Request Path

```
[User] ---> [Ingress: myapp.example.com]
                  |
                  v
            [Service: myapp-svc (ClusterIP)]
                  |
        +---------+---------+
        v         v         v
     [Pod A]   [Pod B]   [Pod C]     <-- managed by Deployment "myapp"
        |
     [Container: nginx]
```

### Your First Deployment (copy-paste)

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
      - name: hello
        image: nginxdemos/hello:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: hello-svc
spec:
  selector:
    app: hello
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

```bash
kubectl apply -f deployment.yaml
kubectl get pods -o wide        # expect 3 running pods
kubectl get svc hello-svc       # expect ClusterIP
kubectl rollout status deployment/hello
kubectl delete -f deployment.yaml
```

### ConfigMap + Secret

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: "production"
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
stringData:
  DB_PASSWORD: "hunter2"
```

Mount into a pod via `envFrom` or as volumes. **Never commit real secrets to git** — use External Secrets Operator or sealed-secrets in real setups.

### RBAC — Who Can Do What in the Cluster

```
[ServiceAccount: deploy-bot]
        |
   bound by [RoleBinding]
        |
        v
   [Role: can get/list/update deployments in namespace "dev"]
```

Rule: pods run as a ServiceAccount, not default. Grant least privilege, same as IAM.

### Helm — Package Manager for K8s

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-nginx bitnami/nginx
helm list
helm upgrade my-nginx bitnami/nginx --set replicaCount=2
helm uninstall my-nginx
```

A Helm chart = templated YAML + values.yaml. Think "npm for Kubernetes".

### Local Cluster Options (all free on macOS)

| Tool | Command | Notes |
|------|---------|-------|
| Docker Desktop K8s | Enable in settings | Simplest |
| kind | `kind create cluster` | K8s-in-Docker, great for CI |
| minikube | `minikube start` | Most features, addons |
| k3d | `k3d cluster create` | Lightweight k3s |

### Reading

- [Kubernetes Basics Tutorial](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Kubernetes Concepts](https://kubernetes.io/docs/concepts/)
- [Helm Docs](https://helm.sh/docs/)
- [Learn Kubernetes the Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way) (advanced, do after basics)

### Hands-On Checkpoint

- [ ] Install kubectl + one local cluster (kind or minikube)
- [ ] Deploy the hello Deployment above, scale to 5, watch pods appear
- [ ] Kill a pod (`kubectl delete pod <name>`) and watch it self-heal
- [ ] Expose it with a Service, then with an Ingress (minikube: `minikube addons enable ingress`)
- [ ] Create a ConfigMap and Secret, mount into a pod, `kubectl exec` in and verify
- [ ] `helm install` a chart, upgrade it, roll it back
- [ ] Write a NetworkPolicy that allows traffic only from pods with label `app=frontend`

---

## Module 8B: OpenShift (OCP)

OpenShift = Red Hat's enterprise distribution of Kubernetes. It **is** Kubernetes, plus a curated set of additions for security, developer experience, and operations.

### OpenShift vs Vanilla Kubernetes

| Area | Vanilla K8s | OpenShift |
|------|------------|-----------|
| Container runtime | Any CRI runtime | CRI-O only (curated) |
| HTTP entry | Ingress | **Route** (Ingress also works) |
| Builds | External CI builds images | **BuildConfig + ImageStream** build images in-cluster from source |
| Pod security | PodSecurity standards | **SecurityContextConstraints (SCC)** — stricter by default (no root!) |
| Extensions | Operators (community) | **OperatorHub** — curated, certified operators |
| Updates | You manage | Cluster Version Operator handles upgrades |
| Console | Dashboard (optional) | Full web console built in |
| Registry | Bring your own | Internal image registry built in |
| Multi-tenancy | DIY namespaces | Projects (namespace + RBAC + quotas baked in) |

### The OCP-Only Objects

```
[Git repo] --(BuildConfig: source-to-image)--> [ImageStream: myapp:latest]
                                                      |
                                                      v
[Route: myapp.apps.cluster.example.com] <--- [Service] <--- [DeploymentConfig / Deployment]
```

- **Route:** Like Ingress but older and richer (TLS edge/reencrypt/passthrough built in).
- **BuildConfig:** "Build this Git repo into a container image when code changes" — CI inside the cluster.
- **ImageStream:** Versioned pointer to images; triggers redeploys on new image.
- **Operator:** A controller that manages an application (e.g., an Operator that installs and manages PostgreSQL). OCP's OperatorHub is the curated app store.
- **SCC:** Defines what pods *may* do (run as root? host networking? privileged?). Default `restricted-v2` blocks root containers — the #1 gotcha when porting vanilla K8s apps to OCP.

### ASCII: SCC Gotcha

```
Vanilla K8s:  pod runs as root by default     ---> works
OpenShift:    pod gets random non-root UID    ---> app crashes writing to /root-owned dirs

Fix: design containers to run as arbitrary UID (chmod g+rw, use /tmp, no hardcoded UID)
```

### Ways to Run OpenShift

| Option | Cost | Notes |
|--------|------|-------|
| **OpenShift Local** (formerly CodeReady Containers) | Free | Single-node OCP on your Mac, needs ~9GB RAM. Best for learning. |
| Developer Sandbox | Free | Red Hat hosted shared cluster, 30-day rotating access, no install |
| Self-managed OCP | Paid license | Full install on bare metal / VMware / Nutanix |
| **ROSA** | AWS bill + OCP fee | Red Hat OpenShift Service on AWS (managed) |
| **ARO** | Azure bill + OCP fee | Azure Red Hat OpenShift (managed) |

For learning: **OpenShift Local** or the **Developer Sandbox**. No cloud target needed.

### Your First OCP App (from source, no Dockerfile)

```bash
# With OpenShift Local running:
oc login -u developer https://api.crc.testing:6443

oc new-project demo
oc new-app https://github.com/sclorg/nodejs-ex --name=hello
oc expose service hello
oc get route hello          # gives you a public URL on the cluster
oc logs -f bc/hello         # watch the source-to-image build
oc delete project demo
```

That's it — OCP cloned the repo, built a container image from source, deployed it, and gave it a route. No Dockerfile, no external CI.

### CLI Mapping (kubectl vs oc)

```bash
kubectl get pods          ==  oc get pods          # oc is a superset
                            oc new-app ...          # OCP only
                            oc new-project x        # namespace + RBAC in one
                            oc start-build hello    # trigger BuildConfig
```

Everything you know from Module 8A transfers. Learn K8s first, then OCP's deltas.

### Reading

- [OpenShift Documentation](https://docs.openshift.com/)
- [OpenShift Interactive Learning Portal](https://learn.openshift.com/) (free browser labs)
- [OpenShift Local](https://developers.redhat.com/products/openshift-local/overview)
- [Developer Sandbox](https://developers.redhat.com/developer-sandbox)

### Hands-On Checkpoint

- [ ] Install OpenShift Local (`brew install crc`, `crc setup`, `crc start`) or sign up for the Developer Sandbox
- [ ] `oc new-app` a sample app from GitHub, expose a Route, hit the URL
- [ ] Trigger a rebuild after editing nothing (`oc start-build`) and watch a new rollout
- [ ] Try deploying a container that runs as root — observe the SCC failure, then fix the image/config
- [ ] Install an Operator from OperatorHub (e.g., a database operator) and create an instance of its CRD
- [ ] Explain out loud: Route vs Ingress, BuildConfig vs external CI, SCC vs PodSecurity

---

## Module 9: Monitoring & Observability

### The Three Pillars

1. **Metrics** — Numbers over time (CPU, disk, latency)
2. **Logs** — Text records of events (app logs, access logs)
3. **Traces** — Request path through microservices

### AWS CloudWatch

```
[EC2 / Lambda / RDS] ---> [CloudWatch Metrics] ---> [Alarms] ---> [SNS Email/Slack]
                              |
                              +---> [CloudWatch Logs] ---> [Log Insights queries]
```

### Golden Signals

| Signal | What It Means |
|--------|---------------|
| Latency | How long requests take |
| Traffic | How many requests |
| Errors | Failure rate |
| Saturation | How full the system is |

### Reading

- [AWS CloudWatch User Guide](https://docs.aws.amazon.com/cloudwatch/)
- [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/)

### Hands-On Checkpoint

- [ ] Create a CloudWatch alarm for EC2 CPU > 80%
- [ ] Send a test email via SNS
- [ ] Install the CloudWatch agent on EC2 to collect memory metrics
- [ ] Write a Log Insights query to find 500 errors in the last hour

---

## Module 10: Cost Optimization

### The Cloud Bill Shock

```
Month 1: $5  (learning, free tier)
Month 2: $50 (forgot to stop EC2)
Month 3: $500 (left NAT Gateway running 24/7)
```

### Cost Optimization Levers

| Lever | How Much You Save | Effort |
|-------|-------------------|--------|
| Stop unused instances | 100% of that instance | Low |
| Right-size (t2.large -> t2.small) | 50% | Medium |
| Spot instances | 70-90% | Medium |
| Reserved Instances | 30-60% | Low (commitment) |
| S3 Intelligent-Tiering | 20-40% on storage | Low |

### Free Tier Traps

- **NAT Gateway** is NOT free (~$0.045/hour = ~$32/month)
- **Elastic IPs** are free only when attached to a running instance
- **EBS volumes** persist and charge even when EC2 is stopped

### Reading

- [AWS Pricing Calculator](https://calculator.aws/)
- [AWS Cost Optimization](https://aws.amazon.com/pricing/cost-optimization/)

### Hands-On Checkpoint

- [ ] Set a billing alarm for $10
- [ ] Use the AWS Pricing Calculator to estimate a t2.micro + 20GB EBS
- [ ] Tag all resources with `Project: Learning` and enable cost allocation tags
- [ ] Review the AWS Cost Explorer after 1 week

---

## Module 11: Pick a Primary Cloud

### Market Share (2026)

| Provider | Market Share | Best For |
|----------|-------------|----------|
| AWS | ~31% | Broadest services, most jobs |
| Azure | ~25% | Microsoft shops, hybrid cloud |
| GCP | ~11% | Data, ML, Kubernetes |

### Recommendation

**Primary: AWS.** Most job postings, most community content, most free-tier friendly.

**Secondary: Azure.** A Windows/AD/PKI background maps directly to Entra ID (formerly Azure AD). Many enterprises run hybrid.

**Skip for now:** GCP. Come back after 6 months of AWS.

### Service Mapping Cheat Sheet

| Concept | AWS | Azure | GCP |
|---------|-----|-------|-----|
| VM | EC2 | Virtual Machines | Compute Engine |
| Object Storage | S3 | Blob Storage | Cloud Storage |
| NoSQL | DynamoDB | Cosmos DB | Firestore |
| Serverless | Lambda | Functions | Cloud Functions |
| Kubernetes | EKS | AKS | GKE |
| OpenShift | ROSA | ARO | OCP on GCP |
| IAM | IAM | Entra ID / RBAC | IAM |
| Network | VPC | VNet | VPC |
| DNS | Route 53 | Azure DNS | Cloud DNS |
| Monitoring | CloudWatch | Azure Monitor | Cloud Monitoring |
| IaC | CloudFormation / Terraform | ARM / Bicep / Terraform | Deployment Manager / Terraform |

### Certification Path

1. **AWS Certified Cloud Practitioner** (foundational, optional)
2. **AWS Certified Solutions Architect – Associate** (the standard)
3. **AWS Certified SysOps Administrator – Associate** (ops-heavy, fits a sysadmin background)
4. **Azure Administrator Associate (AZ-104)** (after AWS, if going hybrid)
5. **Certified Kubernetes Administrator (CKA)** — pairs with Module 8A
6. **Red Hat Certified Specialist in OpenShift Administration (EX280)** — pairs with Module 8B

### Reading

- [AWS Certification Paths](https://aws.amazon.com/certification/)
- [Azure Certification Paths](https://learn.microsoft.com/en-us/credentials/)
- [CNCF Certification (CKA/CKAD)](https://www.cncf.io/training/certification/)
- [Red Hat OpenShift Certifications](https://www.redhat.com/en/services/certification/rhocs)

### Hands-On Checkpoint

- [ ] Create an AWS account and secure it (MFA, billing alarm, IAM user)
- [ ] Create an Azure account and secure it
- [ ] Launch the same simple app (nginx on VM) in both clouds
- [ ] Compare the consoles: which feels more intuitive to you?

---

## Module 12: Real-Project Portfolio

### Project 1: Static Website with CI/CD

**Goal:** Host a resume site on S3 + CloudFront, auto-deploy on git push.

**Architecture:**
```
[GitHub] --(push)--> [GitHub Actions] --(sync)--> [S3 Bucket] --(CDN)--> [CloudFront] --> [Users]
```

**Skills Proven:** S3, CloudFront, IAM, CI/CD, DNS (Route 53)

### Project 2: Three-Tier Web App

**Goal:** Deploy a simple todo app (React frontend, Node backend, RDS database).

**Architecture:**
```
[Users] --> [CloudFront] --> [S3: React App]
                |
                +--> [ALB] --> [EC2: Node API] --> [RDS MySQL]
```

**Skills Proven:** VPC, EC2, RDS, ALB, Security Groups, Secrets Manager

### Project 3: Serverless API with Monitoring

**Goal:** Build a URL shortener API using Lambda + DynamoDB, with full observability.

**Architecture:**
```
[Users] --> [API Gateway] --> [Lambda] --> [DynamoDB]
                |
                +--> [CloudWatch Logs + Metrics + X-Ray]
```

**Skills Proven:** Lambda, DynamoDB, API Gateway, IAM, CloudWatch, X-Ray

### Project 4: Containerized App on Kubernetes

**Goal:** Take the Project 2 app, containerize it, and deploy to a local K8s cluster, then to a managed service.

**Architecture:**
```
[GitHub] --(CI builds image)--> [ECR / Docker Hub]
                                      |
                                      v
              [Helm Chart] ---> [K8s Cluster: kind -> EKS]
                                      |
                          [Ingress] -> [Service] -> [Pods x3]
```

**Skills Proven:** Docker, Kubernetes, Helm, Ingress, managed K8s

### Project 5: Same App on OpenShift

**Goal:** Redeploy Project 4 to OpenShift Local using `oc new-app` source-to-image, and compare the workflow.

**Skills Proven:** OCP, Routes, BuildConfigs, ImageStreams, SCC troubleshooting, Operators

### Portfolio Checklist

- [ ] All 5 projects deployed and (where applicable) accessible via public URL
- [ ] GitHub repos with README, architecture diagram, and cost breakdown
- [ ] Terraform code for at least one project
- [ ] Blog post or LinkedIn article explaining what you built

### Reading

- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/)

---
*Generated by Hermes. Last updated: 2026-09-21.*
