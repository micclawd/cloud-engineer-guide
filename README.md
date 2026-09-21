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
*Generated by Hermes. Last updated: 2026-09-21.*
