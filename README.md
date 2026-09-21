# Cloud Engineer Foundations

> From zero to cloud engineer. One page at a time.

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
*Generated by Hermes. Last updated: 2026-09-21.*
