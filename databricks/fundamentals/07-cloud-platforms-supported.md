# Cloud Platforms Supported by Databricks

## Learning Objectives

By the end of this module, you will understand:

- Why Databricks is cloud-native
- Supported cloud platforms
- Databricks on AWS
- Databricks on Azure
- Databricks on Google Cloud (GCP)
- Architecture differences across clouds
- Storage services used by Databricks
- Identity and Access Management
- Networking differences
- Compute services
- Multi-cloud strategy
- Real-world deployment scenarios
- Cloud comparison for interviews

---

# Introduction

Databricks is a cloud-native platform.

Unlike traditional on-premise software, Databricks is designed to run in cloud environments.

Currently, Databricks supports:

```text
AWS
Microsoft Azure
Google Cloud Platform (GCP)
```

This allows organizations to choose the cloud that best fits their requirements.

---

# What Does Cloud-Native Mean?

Cloud-native means the platform is designed to use cloud services such as:

```text
Compute
Storage
Networking
Security
Identity Management
Monitoring
```

instead of requiring physical servers.

---

# High-Level Multi-Cloud Architecture

```text
                  Databricks
                       │
       ┌───────────────┼───────────────┐
       │               │               │
       ▼               ▼               ▼
      AWS           Azure            GCP
```

Databricks provides a similar user experience across all clouds.

---

# Why Multi-Cloud Support Matters

Different organizations have different cloud strategies.

Examples:

### Company A

```text
Uses AWS
```

### Company B

```text
Uses Azure
```

### Company C

```text
Uses Google Cloud
```

Databricks supports all of them.

---

# Benefits of Multi-Cloud Support

```text
Flexibility
Vendor Choice
Global Expansion
Disaster Recovery
Cloud Strategy Alignment
```

Organizations are not locked to a single provider.

---

# Databricks on AWS

AWS was the first cloud platform supported by Databricks.

It remains one of the most widely used deployments.

---

# AWS Architecture

```text
Databricks Workspace
          │
          ▼
      EC2 Cluster
          │
          ▼
       Amazon S3
```

---

# AWS Services Used by Databricks

Common AWS services include:

```text
Amazon EC2
Amazon S3
AWS IAM
VPC
CloudWatch
PrivateLink
```

---

# Amazon EC2

EC2 provides compute resources.

Databricks clusters are created using EC2 instances.

Example:

```text
Driver Node
Worker Nodes
```

run as EC2 virtual machines.

---

# Amazon S3

S3 provides object storage.

Databricks commonly stores:

```text
Delta Tables
Raw Data
Logs
Artifacts
Backups
```

inside S3 buckets.

---

# AWS IAM

IAM provides:

```text
Authentication
Authorization
Role-Based Access
```

for AWS resources.

---

# AWS Networking

Common networking components:

```text
VPC
Subnets
Security Groups
PrivateLink
```

These provide isolation and security.

---

# Databricks on Azure

Microsoft Azure offers a fully managed Databricks service called:

```text
Azure Databricks
```

It is deeply integrated with Azure services.

---

# Azure Architecture

```text
Azure Databricks
        │
        ▼
Azure Virtual Machines
        │
        ▼
ADLS Gen2
```

---

# Azure Services Used by Databricks

Common services include:

```text
Azure Virtual Machines
ADLS Gen2
Azure Active Directory
Virtual Networks
Private Link
Azure Monitor
```

---

# Azure Active Directory (AAD)

AAD provides:

```text
User Management
SSO
Authentication
Authorization
```

Most enterprises use Azure AD integration.

---

# ADLS Gen2

ADLS stands for:

```text
Azure Data Lake Storage Gen2
```

It is the primary storage service for Azure Databricks.

Used for:

```text
Data Lakes
Delta Tables
ETL Storage
Analytics
```

---

# Azure Networking

Key services:

```text
Virtual Networks (VNet)
Private Link
Network Security Groups
Firewalls
```

Used for secure deployments.

---

# Databricks on Google Cloud

Databricks also supports Google Cloud Platform (GCP).

This deployment is often used by organizations heavily invested in Google's ecosystem.

---

# GCP Architecture

```text
Databricks Workspace
          │
          ▼
Compute Engine
          │
          ▼
Google Cloud Storage
```

---

# GCP Services Used by Databricks

Examples:

```text
Compute Engine
Google Cloud Storage
Cloud IAM
VPC Networks
Cloud Monitoring
```

---

# Compute Engine

Provides virtual machines used for:

```text
Driver Nodes
Worker Nodes
```

similar to AWS EC2 and Azure VMs.

---

# Google Cloud Storage (GCS)

GCS provides object storage.

Used for:

```text
Raw Data
Delta Tables
Backups
Logs
```

---

# Cloud IAM

Provides:

```text
User Management
Access Control
Permissions
```

for GCP environments.

---

# Storage Comparison

| Cloud | Storage Service |
|---------|---------|
| AWS | Amazon S3 |
| Azure | ADLS Gen2 |
| GCP | Google Cloud Storage |

These services store data outside clusters.

---

# Compute Comparison

| Cloud | Compute Service |
|---------|---------|
| AWS | EC2 |
| Azure | Azure Virtual Machines |
| GCP | Compute Engine |

Clusters are built on these services.

---

# Identity Comparison

| Cloud | Identity Service |
|---------|---------|
| AWS | IAM |
| Azure | Azure AD |
| GCP | Cloud IAM |

Used for authentication and authorization.

---

# Networking Comparison

| Cloud | Networking Service |
|---------|---------|
| AWS | VPC |
| Azure | VNet |
| GCP | VPC Network |

Provides network isolation.

---

# Cloud-Neutral Databricks Experience

One major advantage:

Most Databricks features remain identical.

Example:

```python
df.show()
```

works the same on:

```text
AWS
Azure
GCP
```

Users generally do not need to change Spark code.

---

# What Changes Across Clouds?

Primarily:

```text
Storage
Networking
Identity
Billing
Infrastructure Services
```

The Databricks experience remains largely consistent.

---

# Multi-Cloud Strategy

Some organizations use multiple clouds.

Example:

```text
Production -> AWS

Analytics -> Azure

Research -> GCP
```

Databricks supports such strategies.

---

# Why Organizations Choose AWS

Common reasons:

```text
Largest Cloud Ecosystem
Mature Services
Global Reach
Strong Enterprise Adoption
```

---

# Why Organizations Choose Azure

Common reasons:

```text
Microsoft Integration
Azure AD
Office 365 Ecosystem
Enterprise Agreements
```

---

# Why Organizations Choose GCP

Common reasons:

```text
Data Analytics Focus
AI Services
BigQuery Ecosystem
Google Infrastructure
```

---

# Cloud Migration Example

A company currently running on-premises:

```text
Physical Servers
      │
      ▼
Data Center
```

may migrate to:

```text
Databricks + Cloud Storage
```

for better scalability and lower maintenance.

---

# Real-World Enterprise Example

```text
Data Sources
      │
      ▼
Databricks
      │
      ▼
Delta Lake
      │
      ▼
Cloud Storage
```

This architecture works similarly on:

- AWS
- Azure
- GCP

---

# Common Interview Questions

### Which cloud platforms does Databricks support?

- AWS
- Azure
- GCP

---

### What storage service is used on AWS?

Amazon S3.

---

### What storage service is commonly used on Azure?

ADLS Gen2.

---

### What storage service is used on GCP?

Google Cloud Storage.

---

### Does Spark code change between clouds?

Generally no.

Databricks provides a consistent experience.

---

### What compute service does AWS use?

Amazon EC2.

---

### What compute service does Azure use?

Azure Virtual Machines.

---

### What compute service does GCP use?

Compute Engine.

---

# Summary

Databricks is a cloud-native platform that supports:

```text
AWS
Azure
GCP
```

While each cloud has different:

- Storage services
- Networking services
- Identity systems
- Compute resources

the Databricks experience remains largely consistent.

This allows organizations to leverage Databricks regardless of their cloud strategy.

---

# Key Takeaways

✔ Databricks supports AWS, Azure, and GCP

✔ Storage is cloud-specific (S3, ADLS, GCS)

✔ Compute is cloud-specific (EC2, Azure VMs, Compute Engine)

✔ Identity services differ by cloud

✔ Networking services differ by cloud

✔ Databricks provides a consistent user experience

✔ Multi-cloud deployments are supported

✔ Cloud knowledge is important for enterprise Databricks environments

---

# Next Module

➡ 08-key-terminology.md
