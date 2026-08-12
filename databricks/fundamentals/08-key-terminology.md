# Databricks Key Terminology

## Learning Objectives

By the end of this module, you will understand:

- Common Databricks terminology
- Frequently used Databricks concepts
- Core platform vocabulary
- Spark-related terminology
- Delta Lake terminology
- Unity Catalog terminology
- Compute terminology
- Governance terminology
- Cloud terminology used in Databricks
- Interview-focused definitions

---

# Introduction

Every technology platform has its own vocabulary.

Databricks is no different.

If someone says:

```text
Attach the notebook to a cluster and query a Delta table using Unity Catalog.
```

A beginner may not understand any part of that sentence.

After completing this module, you should understand the most important Databricks terms used in:

- Documentation
- Projects
- Administration
- Interviews
- Certifications

---

# Workspace

A Workspace is the primary environment where users interact with Databricks.

It contains:

```text
Notebooks
Jobs
Dashboards
Repos
Files
Clusters
```

Think of it as:

```text
Your Databricks Working Environment
```

---

# Account

A Databricks Account is the top-level organizational entity.

Example:

```text
Databricks Account
      │
      ├── Dev Workspace
      ├── Test Workspace
      └── Prod Workspace
```

One account can manage multiple workspaces.

---

# Account Console

The Account Console is the administration portal used to manage:

```text
Users
Groups
Workspaces
Billing
Governance
Networking
```

across the entire organization.

---

# User

A User is an individual who can access Databricks.

Examples:

```text
Data Engineer
Data Analyst
Data Scientist
Administrator
```

---

# Group

A Group is a collection of users.

Example:

```text
Data Engineers
```

instead of assigning permissions individually.

---

# Cluster

A Cluster is a group of virtual machines that provide compute resources.

Example:

```text
1 Driver
4 Workers
```

Clusters execute workloads.

---

# Driver Node

The Driver Node coordinates Spark execution.

Responsibilities:

```text
Create Execution Plans
Coordinate Workers
Track Jobs
Return Results
```

Every Spark application has one driver.

---

# Worker Node

Worker Nodes perform actual data processing.

Responsibilities:

```text
Read Data
Transform Data
Execute Tasks
Write Results
```

Multiple workers run in parallel.

---

# Executor

An Executor is a process running on a worker node.

Executors perform:

```text
Calculations
Transformations
Actions
```

inside Spark.

---

# Compute

Compute refers to processing resources used by Databricks.

Examples:

```text
Clusters
Serverless Compute
SQL Warehouses
```

Compute performs work.

---

# Notebook

A Notebook is an interactive development environment.

Supported languages:

```text
Python
SQL
Scala
R
```

Example:

```python
print("Hello Databricks")
```

---

# Cell

A Notebook consists of cells.

Example:

```text
Cell 1
Cell 2
Cell 3
```

Each cell can be executed independently.

---

# Repository (Repo)

A Repo connects Databricks to Git providers.

Examples:

```text
GitHub
GitLab
Bitbucket
Azure DevOps
```

Used for version control.

---

# Job

A Job automates execution of workloads.

Example:

```text
Run ETL Daily
```

instead of manually running notebooks.

---

# Workflow

A Workflow orchestrates multiple tasks.

Example:

```text
Task 1
   │
   ▼
Task 2
   │
   ▼
Task 3
```

Workflows help automate pipelines.

---

# Library

A Library is a package installed on a cluster.

Examples:

```text
pandas
numpy
scikit-learn
```

Libraries extend functionality.

---

# DBFS

DBFS stands for:

```text
Databricks File System
```

Used for storing files accessible to Databricks workloads.

---

# Apache Spark

Apache Spark is the distributed processing engine used by Databricks.

Spark enables:

```text
Parallel Processing
Distributed Computing
Large-Scale Analytics
```

---

# Spark Application

A Spark Application is a running Spark program.

Example:

```python
df.count()
```

When executed, Spark creates an application.

---

# Transformation

A Transformation modifies data.

Examples:

```python
select()
filter()
groupBy()
join()
```

Transformations build execution plans.

---

# Action

An Action triggers execution.

Examples:

```python
show()
count()
collect()
write()
```

Actions cause Spark to process data.

---

# Partition

A Partition is a logical chunk of data.

Example:

```text
Large Dataset
      │
      ▼
Partition 1
Partition 2
Partition 3
Partition 4
```

Partitions enable parallel processing.

---

# Shuffle

A Shuffle occurs when data moves between partitions.

Examples:

```python
groupBy()
join()
distinct()
```

Shuffles can impact performance.

---

# Cache

Caching stores data in memory.

Benefits:

```text
Faster Queries
Reduced Processing Time
```

---

# DataFrame

A DataFrame is Spark's primary data structure.

Think of it as:

```text
Distributed Table
```

with rows and columns.

---

# Dataset

A Dataset is a strongly typed Spark abstraction.

Most commonly used in Scala.

---

# RDD

RDD stands for:

```text
Resilient Distributed Dataset
```

It is Spark's original distributed data structure.

Today, DataFrames are more commonly used.

---

# Delta Lake

Delta Lake is the storage layer used by Databricks.

Provides:

```text
ACID Transactions
Time Travel
Schema Enforcement
```

on top of cloud storage.

---

# Delta Table

A Delta Table is a table stored using Delta Lake format.

Example:

```text
sales_delta
customer_delta
orders_delta
```

---

# ACID Transactions

ACID stands for:

```text
Atomicity
Consistency
Isolation
Durability
```

These ensure reliable data operations.

---

# Time Travel

Time Travel allows access to previous versions of data.

Example:

```text
View Table Yesterday
```

without restoring backups.

---

# Schema

A Schema is a logical container for database objects.

Example:

```text
sales_schema
finance_schema
```

---

# Catalog

A Catalog is a higher-level container.

Structure:

```text
Catalog
   │
   ▼
Schema
   │
   ▼
Table
```

---

# Metastore

A Metastore stores metadata.

Contains information about:

```text
Tables
Schemas
Catalogs
Permissions
```

It does not store actual data.

---

# Unity Catalog

Unity Catalog is Databricks' governance solution.

Provides:

```text
Access Control
Lineage
Auditing
Governance
```

across the organization.

---

# Data Lineage

Lineage tracks data movement.

Example:

```text
Raw Data
    │
    ▼
Transformation
    │
    ▼
Report
```

Helps understand where data came from.

---

# SQL Warehouse

SQL Warehouse is a compute resource optimized for SQL workloads.

Used for:

```text
Analytics
Dashboards
Reporting
```

---

# Dashboard

A Dashboard visualizes data.

Examples:

```text
Sales Dashboard
Customer Dashboard
Operations Dashboard
```

---

# Auto Loader

Auto Loader automatically ingests files into Databricks.

Common sources:

```text
CSV
JSON
Parquet
Logs
```

---

# ETL

ETL stands for:

```text
Extract
Transform
Load
```

Traditional data pipeline approach.

---

# ELT

ELT stands for:

```text
Extract
Load
Transform
```

Modern cloud-native approach.

---

# Bronze Layer

Raw data layer.

Example:

```text
Original Source Data
```

---

# Silver Layer

Cleaned and transformed data.

Example:

```text
Validated Data
```

---

# Gold Layer

Business-ready data.

Example:

```text
Reporting Tables
Analytics Tables
```

---

# Medallion Architecture

Data organization pattern:

```text
Bronze
   │
   ▼
Silver
   │
   ▼
Gold
```

Widely used in Databricks.

---

# Serverless Compute

Managed compute where Databricks handles infrastructure.

Benefits:

```text
No Cluster Management
Automatic Scaling
Simplified Operations
```

---

# Autoscaling

Automatically increases or decreases cluster size.

Example:

```text
2 Workers
     │
     ▼
10 Workers
```

when workload increases.

---

# VPC

Virtual Private Cloud.

Common in AWS and GCP.

Provides network isolation.

---

# VNet

Virtual Network.

Azure equivalent of VPC.

---

# IAM

Identity and Access Management.

Controls:

```text
Authentication
Authorization
Permissions
```

---

# SSO

Single Sign-On.

Allows users to log in using corporate credentials.

---

# MFA

Multi-Factor Authentication.

Adds an additional security layer.

---

# SCIM

System for Cross-domain Identity Management.

Automates:

```text
User Provisioning
Group Provisioning
```

---

# Control Plane

Management layer responsible for:

```text
Workspaces
Users
Jobs
Clusters
Metadata
```

---

# Data Plane

Execution layer responsible for:

```text
Spark Jobs
ETL
Streaming
Analytics
```

---

# Quick Reference Table

| Term | Meaning |
|--------|----------|
| Workspace | User environment |
| Account | Top-level organization |
| Cluster | Compute resources |
| Driver | Coordinates Spark |
| Worker | Processes data |
| Executor | Runs Spark tasks |
| Notebook | Interactive code environment |
| Job | Automated workload |
| DBFS | Databricks File System |
| Spark | Processing engine |
| Delta Lake | Storage layer |
| Unity Catalog | Governance layer |
| SQL Warehouse | SQL compute |
| Catalog | Container for schemas |
| Schema | Container for tables |
| Metastore | Metadata repository |
| ETL | Extract Transform Load |
| ELT | Extract Load Transform |

---

# Interview Questions

### What is a Cluster?

A collection of virtual machines used for processing workloads.

---

### What is a Driver Node?

The component that coordinates Spark execution.

---

### What is Delta Lake?

A storage layer providing ACID transactions and advanced reliability features.

---

### What is Unity Catalog?

Databricks' governance and access control solution.

---

### What is a SQL Warehouse?

Compute optimized for SQL analytics.

---

### What is the difference between ETL and ELT?

ETL transforms data before loading.

ELT loads data before transforming.

---

# Summary

Databricks contains many concepts and terms that are used daily by:

- Data Engineers
- Analysts
- Administrators
- Platform Engineers
- Data Scientists

Understanding these terms creates a strong foundation for advanced Databricks topics such as:

- Clusters
- Delta Lake
- Unity Catalog
- Data Engineering
- Spark Optimization
- Governance

and prepares you for certifications and interviews.

---

# Key Takeaways

✔ Learn the terminology before moving to advanced topics

✔ Clusters provide compute

✔ Spark provides processing

✔ Delta Lake provides storage reliability

✔ Unity Catalog provides governance

✔ Jobs automate workloads

✔ SQL Warehouses support analytics

✔ Medallion Architecture organizes data

✔ Control Plane and Data Plane have separate responsibilities

---

# Fundamentals Section Complete ✅

You now understand:

- What Databricks is
- Why it exists
- Architecture
- Workspace
- Control Plane vs Data Plane
- Account Console
- Cloud Platforms
- Key Terminology

---

# Next Section

➡ `compute/01-clusters.md`
