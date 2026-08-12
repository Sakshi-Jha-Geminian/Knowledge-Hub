# Control Plane vs Data Plane

## Learning Objectives

By the end of this module, you will understand:

- What the Control Plane is
- What the Data Plane is
- Why Databricks separates them
- Components of each plane
- Security boundaries
- Network communication
- Data flow
- Cluster placement
- Customer-managed networks
- AWS, Azure, and GCP deployment models
- Common interview questions

---

# Introduction

One of the most important concepts in Databricks architecture is the separation between:

```text
Control Plane
```

and

```text
Data Plane
```

Many Databricks interview questions revolve around this topic.

If you understand these two concepts well, you will understand how Databricks manages:

- Security
- Scalability
- Reliability
- Governance
- Infrastructure

This architecture is one of the reasons Databricks can support enterprise workloads securely.

---

# High-Level Architecture

```text
                 User
                   │
                   ▼
        ┌─────────────────────┐
        │   Control Plane     │
        └─────────────────────┘
                   │
                   ▼
        ┌─────────────────────┐
        │     Data Plane      │
        └─────────────────────┘
                   │
                   ▼
             Cloud Storage
```

The two planes work together but have different responsibilities.

---

# Simple Analogy

Imagine an airline.

### Control Plane

Responsible for:

```text
Ticket Booking
Scheduling
Flight Planning
Management
```

### Data Plane

Responsible for:

```text
Aircraft
Pilots
Passengers
Actual Flight Operations
```

The management system controls operations.

The aircraft performs the actual work.

Databricks follows a similar design.

---

# What is the Control Plane?

The Control Plane is the management layer of Databricks.

It is operated and maintained by Databricks.

Its purpose is to coordinate and manage the platform.

Think of it as:

```text
The Brain
```

of the Databricks environment.

---

# Responsibilities of the Control Plane

The Control Plane manages:

```text
User Interface
Authentication
Authorization
Workspace Management
Notebook Management
Job Scheduling
Cluster Management
REST APIs
Metadata Services
Monitoring
```

It coordinates operations but does not perform data processing.

---

# Control Plane Components

```text
Workspace Service
Notebook Service
Job Scheduler
Cluster Manager
Authentication Service
API Layer
Metadata Services
Unity Catalog Services
```

Each component provides a management function.

---

# Workspace Service

Manages:

```text
Users
Folders
Permissions
Workspace Objects
```

Example:

```text
Workspace
 ├── Notebook A
 ├── Notebook B
 └── Dashboard
```

---

# Notebook Service

Responsible for:

```text
Notebook Storage
Notebook Metadata
Notebook Execution Requests
```

When a user clicks Run, the request originates here.

---

# Job Scheduler

Responsible for:

```text
Scheduled Jobs
Workflows
Automation
Retries
Dependencies
```

Example:

```text
Run ETL Every Day at 2 AM
```

---

# Cluster Manager

Handles:

```text
Create Cluster
Start Cluster
Stop Cluster
Terminate Cluster
Resize Cluster
```

It provisions compute resources.

---

# Authentication Service

Responsible for:

```text
Login
SSO
MFA
Identity Validation
```

Integrates with:

```text
Azure AD
AWS IAM
Okta
Google Identity
```

---

# Metadata Services

Stores:

```text
Table Metadata
Schema Information
Permissions
Configurations
```

Metadata is not the actual data.

It describes the data.

---

# What is the Data Plane?

The Data Plane is where actual computation happens.

Think of it as:

```text
The Engine
```

of Databricks.

---

# Responsibilities of the Data Plane

The Data Plane performs:

```text
Data Processing
Spark Execution
ETL Jobs
Streaming Jobs
Machine Learning Workloads
Query Execution
Data Transformations
```

This is where your data is processed.

---

# Data Plane Components

```text
Clusters
Driver Nodes
Worker Nodes
Executors
Libraries
Running Jobs
```

Everything related to processing lives here.

---

# Data Plane Architecture

```text
              Cluster
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
 Driver Node         Worker Nodes
```

---

# Driver Node

The Driver Node:

```text
Receives Code
Creates Execution Plan
Coordinates Workers
Tracks Progress
Returns Results
```

Only one driver exists per Spark application.

---

# Worker Nodes

Workers:

```text
Process Data
Execute Tasks
Store Cached Data
Perform Computations
```

Multiple workers execute tasks in parallel.

---

# Executors

Executors run inside worker nodes.

Example:

```text
Worker 1
 └── Executor

Worker 2
 └── Executor

Worker 3
 └── Executor
```

Executors perform the actual calculations.

---

# Where Does Data Live?

Many beginners assume data is stored inside clusters.

This is incorrect.

Databricks separates:

```text
Compute
Storage
```

---

# Storage Architecture

```text
Compute
   │
   ▼
Cluster

Storage
   │
   ▼
Cloud Storage
```

Examples:

### AWS

```text
Amazon S3
```

### Azure

```text
ADLS Gen2
```

### GCP

```text
Google Cloud Storage
```

---

# Why Separate Compute and Storage?

Benefits:

```text
Independent Scaling
Cost Optimization
Flexibility
High Availability
```

You can delete a cluster without losing data.

---

# Control Plane vs Data Plane

| Feature | Control Plane | Data Plane |
|----------|----------|----------|
| Purpose | Management | Processing |
| Owned By | Databricks | Customer Cloud Account |
| Runs Jobs | No | Yes |
| Stores Metadata | Yes | No |
| Executes Spark | No | Yes |
| Hosts Clusters | No | Yes |
| User Interface | Yes | No |

---

# Security Boundary

One major reason for separation is security.

```text
Control Plane
      │
      ▼
Management Layer

-----------------------
Security Boundary
-----------------------

Data Plane
      │
      ▼
Customer Workloads
```

This creates isolation.

---

# Customer Data

Customer data is generally processed within the Data Plane.

Example:

```text
ETL
Streaming
Analytics
Machine Learning
```

All happen inside the Data Plane.

---

# Control Plane Communication

The Control Plane sends instructions.

Example:

```text
Start Cluster
Run Job
Execute Notebook
```

The Data Plane executes those instructions.

---

# Request Flow Example

User executes:

```python
df.count()
```

Flow:

```text
User
 │
 ▼
Workspace
 │
 ▼
Control Plane
 │
 ▼
Cluster
 │
 ▼
Spark Execution
 │
 ▼
Result
```

---

# Cluster Startup Flow

Example:

```text
User Creates Cluster
          │
          ▼
Cluster Manager
          │
          ▼
Cloud Resources Provisioned
          │
          ▼
Driver Starts
          │
          ▼
Workers Start
          │
          ▼
Cluster Ready
```

---

# Data Processing Flow

```text
Notebook
    │
    ▼
Driver Node
    │
    ▼
Spark Scheduler
    │
    ▼
Executors
    │
    ▼
Cloud Storage
    │
    ▼
Results
```

---

# Customer-Managed Networks

Many enterprises require network isolation.

Examples:

### AWS

```text
Customer VPC
```

### Azure

```text
Customer VNet
```

### GCP

```text
Customer VPC
```

The Data Plane often resides within customer-controlled networks.

---

# AWS Deployment Example

```text
Databricks Control Plane
            │
            ▼
Customer AWS Account
            │
            ▼
EC2 Clusters
            │
            ▼
Amazon S3
```

---

# Azure Deployment Example

```text
Databricks Control Plane
            │
            ▼
Azure Subscription
            │
            ▼
Virtual Machines
            │
            ▼
ADLS Gen2
```

---

# GCP Deployment Example

```text
Databricks Control Plane
            │
            ▼
Google Cloud Project
            │
            ▼
Compute Instances
            │
            ▼
GCS
```

---

# Why Enterprises Like This Architecture

Benefits include:

```text
Strong Security
Network Isolation
Data Protection
Scalability
Cloud Flexibility
Governance
```

This model satisfies many enterprise compliance requirements.

---

# Common Misconceptions

### Misconception 1

```text
Control Plane Processes Data
```

False.

The Data Plane processes data.

---

### Misconception 2

```text
Clusters Store Data Permanently
```

False.

Clusters are compute resources.

Data typically resides in cloud storage.

---

### Misconception 3

```text
Control Plane and Data Plane Are The Same
```

False.

They serve different purposes.

---

# Real-World Example

Suppose a company runs:

```text
Daily Sales ETL
```

Process:

```text
Scheduler
    │
    ▼
Control Plane
    │
    ▼
Cluster Created
    │
    ▼
Data Processed
    │
    ▼
Results Stored
    │
    ▼
Cluster Terminated
```

This demonstrates cooperation between both planes.

---

# Interview Questions

### What is the Control Plane?

The management layer of Databricks responsible for orchestration and administration.

---

### What is the Data Plane?

The execution layer where Spark workloads run.

---

### Where are clusters located?

Inside the Data Plane.

---

### Where is data processed?

Inside the Data Plane.

---

### Why separate Control Plane and Data Plane?

For security, scalability, governance, and isolation.

---

### Does the Control Plane execute Spark jobs?

No.

Spark jobs run in the Data Plane.

---

### Does deleting a cluster delete data?

No.

Data is stored separately in cloud storage.

---

# Summary

Databricks uses a two-layer architecture:

```text
Control Plane
Data Plane
```

The Control Plane manages:

- Users
- Workspaces
- Jobs
- Clusters
- Metadata
- APIs

The Data Plane performs:

- Spark Processing
- ETL
- Streaming
- Machine Learning
- Analytics

This separation provides:

- Security
- Scalability
- Governance
- Reliability
- Enterprise-grade architecture

Understanding this model is essential for every Databricks professional.

---

# Key Takeaways

✔ Control Plane = Management Layer

✔ Data Plane = Processing Layer

✔ Clusters run in the Data Plane

✔ Data processing happens in the Data Plane

✔ Storage is separate from compute

✔ Databricks manages the Control Plane

✔ Customers typically own cloud resources in the Data Plane

✔ Security and isolation are major benefits

✔ Frequently asked interview topic

---

# Next Module

➡ 06-databricks-account-console.md
