# Databricks Architecture

## Learning Objectives

By the end of this module, you will understand:

- What Databricks Architecture is
- Core Architectural Components
- Control Plane
- Data Plane
- Workspace Architecture
- Cluster Architecture
- Storage Architecture
- Network Architecture
- Security Architecture
- Request Flow
- Job Execution Flow
- Data Processing Flow
- AWS Architecture
- Azure Architecture
- GCP Architecture
- Real-World Enterprise Deployments

---

# Introduction

Architecture is one of the most important topics in Databricks.

Many beginners learn:

- Notebooks
- Clusters
- SQL
- Delta Lake

without understanding how the platform actually works.

However, almost every advanced Databricks topic depends on architecture.

Understanding architecture helps answer questions like:

```text
Where is my data stored?
Who owns the infrastructure?
How does a cluster start?
How does Spark process data?
How does security work?
How are jobs executed?
```

This chapter answers all of these questions.

---

# High-Level Databricks Architecture

At the highest level:

```text
                Users
                  │
                  ▼
           Databricks Workspace
                  │
                  ▼
              Clusters
                  │
                  ▼
            Apache Spark
                  │
                  ▼
             Delta Lake
                  │
                  ▼
            Cloud Storage
```

This is the simplified view.

In reality, the architecture is divided into two major areas:

```text
Control Plane
Data Plane
```

Understanding these is critical.

---

# The Two Major Components

Databricks Architecture consists of:

```text
1. Control Plane
2. Data Plane
```

Think of them as:

```text
Control Plane
     │
     ▼
Management Layer

Data Plane
     │
     ▼
Processing Layer
```

---

# What is the Control Plane?

The Control Plane is managed by Databricks.

It contains services responsible for:

```text
Workspace Management
Notebook Management
Cluster Management
Authentication
Job Scheduling
Metadata Management
APIs
User Interface
```

The Control Plane does NOT process your data directly.

It manages the environment.

---

# Control Plane Analogy

Think of a restaurant.

```text
Manager
Cashier
Order System
```

These coordinate operations.

They do not cook food.

Similarly:

```text
Control Plane
     │
     ▼
Coordinates Everything
```

---

# What is the Data Plane?

The Data Plane is where actual work happens.

It contains:

```text
Clusters
Spark Executors
Spark Drivers
Data Processing
Job Execution
```

This is where your data is processed.

---

# Data Plane Analogy

Continuing the restaurant example:

```text
Kitchen
Chefs
Cooking Equipment
```

Actual work happens here.

Similarly:

```text
Data Plane
     │
     ▼
Actual Processing
```

---

# Complete Architecture Overview

```text
                  USERS
                     │
                     ▼
            ┌─────────────────┐
            │ CONTROL PLANE   │
            └─────────────────┘
                     │
                     ▼
            ┌─────────────────┐
            │ DATA PLANE      │
            └─────────────────┘
                     │
                     ▼
             CLOUD STORAGE
```

---

# Why Separate Control and Data Planes?

Benefits include:

```text
Security
Scalability
Isolation
Governance
Performance
```

This separation is a major architectural design decision.

---

# Control Plane Components

The Control Plane contains several services.

```text
Workspace Service
Notebook Service
Cluster Manager
Job Scheduler
REST APIs
Authentication Service
Metadata Service
```

Let's examine each one.

---

# Workspace Service

The Workspace Service manages:

```text
Users
Folders
Permissions
Workspace Objects
```

Example:

```text
Workspace
 ├── Notebooks
 ├── Dashboards
 ├── Repositories
 └── Jobs
```

---

# Notebook Service

Responsible for:

```text
Notebook Creation
Notebook Storage
Notebook Execution Requests
Version Management
```

Supported languages:

```text
Python
SQL
Scala
R
```

---

# Cluster Manager

Responsible for:

```text
Create Cluster
Start Cluster
Stop Cluster
Restart Cluster
Terminate Cluster
```

Users request clusters through the workspace.

The Cluster Manager handles provisioning.

---

# Job Scheduler

Responsible for:

```text
Scheduled Jobs
Workflows
Task Orchestration
Automation
```

Example:

```text
Daily ETL
Hourly Pipeline
Weekly Report
```

---

# Authentication Service

Responsible for:

```text
Login
Identity Verification
Access Control
Authorization
```

Integrates with:

```text
Azure AD
AWS IAM
Google Identity
SSO Providers
```

---

# REST APIs

Databricks provides APIs for:

```text
Clusters
Jobs
Workspace
Users
Permissions
Secrets
```

Everything available in the UI can generally be automated.

---

# Metadata Services

Stores information about:

```text
Tables
Schemas
Catalogs
Permissions
Configurations
```

Not the actual data.

Only metadata.

---

# Data Plane Components

The Data Plane contains:

```text
Clusters
Driver Nodes
Worker Nodes
Spark Executors
Libraries
Running Jobs
```

This is where processing occurs.

---

# Data Plane Overview

```text
             Cluster
                 │
     ┌───────────┴───────────┐
     │                       │
     ▼                       ▼
Driver Node            Worker Nodes
```

---

# What is a Cluster?

A cluster is a collection of cloud virtual machines.

Example:

```text
1 Driver Node
4 Worker Nodes
```

Together they process data.

---

# Driver Node

The Driver Node controls the Spark application.

Responsibilities:

```text
Receive User Code
Create Execution Plan
Coordinate Workers
Track Progress
Return Results
```

Think of it as the manager.

---

# Worker Nodes

Workers perform the actual processing.

Responsibilities:

```text
Read Data
Execute Tasks
Transform Data
Write Results
```

Multiple workers run in parallel.

---

# Spark Executors

Each worker contains Spark Executors.

Example:

```text
Worker 1
 └── Executor

Worker 2
 └── Executor

Worker 3
 └── Executor
```

Executors perform computations.

---

# Spark Job Execution Flow

Example:

```python
df.groupBy("country").count()
```

Execution flow:

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
Results
```

---

# Storage Architecture

Databricks separates:

```text
Compute
Storage
```

This is extremely important.

---

# Traditional Architecture

```text
Server
 ├── Compute
 └── Storage
```

Both reside together.

---

# Databricks Architecture

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

Completely separate.

---

# Benefits of Separation

```text
Independent Scaling
Lower Cost
Flexibility
Better Performance
```

You can scale compute without moving data.

---

# Cloud Storage Options

Depending on cloud provider:

### AWS

```text
Amazon S3
```

### Azure

```text
Azure Data Lake Storage (ADLS)
```

### GCP

```text
Google Cloud Storage (GCS)
```

---

# Delta Lake in Architecture

Delta Lake sits on top of cloud storage.

```text
Cloud Storage
      │
      ▼
Delta Lake
      │
      ▼
Reliable Tables
```

Provides:

```text
ACID Transactions
Time Travel
Schema Enforcement
```

---

# Network Architecture

Communication occurs between:

```text
Users
Control Plane
Data Plane
Cloud Storage
```

---

# Network Flow

```text
User
  │
  ▼
Workspace
  │
  ▼
Cluster
  │
  ▼
Storage
```

This flow occurs continuously during execution.

---

# Security Architecture

Security exists at multiple layers.

```text
Identity Security
Network Security
Storage Security
Cluster Security
Data Security
```

---

# Identity Security

Examples:

```text
SSO
MFA
Azure AD
IAM Roles
Service Principals
```

---

# Access Control

Controls who can:

```text
Read
Write
Modify
Delete
Execute
```

resources.

---

# Data Encryption

Databricks encrypts:

### Data at Rest

```text
Stored Data
```

### Data in Transit

```text
Network Communication
```

---

# Request Flow Example

Suppose a user executes:

```python
display(df)
```

Flow:

```text
User
 │
 ▼
Notebook
 │
 ▼
Control Plane
 │
 ▼
Cluster
 │
 ▼
Spark
 │
 ▼
Storage
 │
 ▼
Results
```

---

# Job Execution Flow

```text
Job Trigger
     │
     ▼
Job Scheduler
     │
     ▼
Cluster Creation
     │
     ▼
Spark Execution
     │
     ▼
Store Results
```

---

# AWS Databricks Architecture

```text
AWS Account
      │
      ▼
Databricks Workspace
      │
      ▼
EC2 Clusters
      │
      ▼
Amazon S3
```

---

# Azure Databricks Architecture

```text
Azure Subscription
        │
        ▼
Azure Databricks
        │
        ▼
Virtual Machines
        │
        ▼
ADLS
```

---

# GCP Databricks Architecture

```text
Google Cloud
      │
      ▼
Databricks Workspace
      │
      ▼
Compute Engine
      │
      ▼
GCS
```

---

# Enterprise Architecture Example

Large enterprise:

```text
Users
 │
 ▼
Unity Catalog
 │
 ▼
Databricks Workspace
 │
 ▼
Job Clusters
 │
 ▼
Delta Lake
 │
 ▼
Cloud Storage
```

Used by:

```text
Data Engineers
Data Analysts
Data Scientists
ML Engineers
```

simultaneously.

---

# Common Interview Questions

### What is the Control Plane?

Management layer maintained by Databricks.

### What is the Data Plane?

Processing layer where Spark workloads execute.

### Where is data stored?

Cloud storage such as:

- S3
- ADLS
- GCS

### What is a Driver Node?

Coordinates Spark execution.

### What are Worker Nodes?

Perform distributed processing.

### Why separate compute and storage?

Independent scalability and cost optimization.

---

# Summary

Databricks architecture is built around two core layers:

```text
Control Plane
Data Plane
```

The Control Plane manages:

- Workspaces
- Users
- Jobs
- Clusters
- APIs

The Data Plane performs:

- Spark Processing
- Data Transformation
- Job Execution

Data is stored separately in cloud storage and typically managed through Delta Lake.

This architecture provides:

- Scalability
- Performance
- Security
- Reliability
- Cloud-native flexibility

and forms the foundation for all Databricks workloads.

---

# Key Takeaways

✔ Control Plane manages resources

✔ Data Plane performs processing

✔ Spark runs inside clusters

✔ Driver coordinates execution

✔ Workers process data

✔ Storage and compute are separate

✔ Delta Lake sits on cloud storage

✔ Architecture is cloud-native

✔ Security exists at multiple layers

✔ Understanding architecture is essential for advanced Databricks topics

---

# Next Module

➡ 04-workspace-overview.md
