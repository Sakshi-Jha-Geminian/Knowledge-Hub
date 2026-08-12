# Serverless Compute in Databricks

## Learning Objectives

By the end of this module, you will understand:

- What Serverless Compute is
- Why Serverless exists
- Traditional Compute vs Serverless
- Serverless Architecture
- Serverless SQL Warehouses
- Serverless Jobs
- Serverless Notebooks
- Benefits and Limitations
- Cost Considerations
- Security Model
- Governance
- Real-world Use Cases
- Best Practices
- Interview Questions

---

# Introduction

Traditionally, Databricks users had to manage clusters.

Example:

```text
Create Cluster
Configure Cluster
Monitor Cluster
Resize Cluster
Terminate Cluster
```

Even with autoscaling, users still needed to think about infrastructure.

To simplify operations, Databricks introduced:

```text
Serverless Compute
```

Serverless allows users to focus on workloads instead of infrastructure.

---

# What Does "Serverless" Mean?

Serverless does NOT mean servers do not exist.

Servers still exist.

The difference is:

```text
Databricks Manages Them
```

instead of the customer.

Users simply run workloads.

---

# Traditional Compute Model

```text
User
  │
  ▼
Create Cluster
  │
  ▼
Manage Cluster
  │
  ▼
Run Workload
```

---

# Serverless Model

```text
User
  │
  ▼
Run Workload
```

Infrastructure management is hidden.

---

# Why Serverless Exists

Organizations faced challenges:

```text
Cluster Administration
Startup Delays
Cost Management
Operational Complexity
```

Serverless reduces these burdens.

---

# Core Idea

Instead of asking:

```text
How Many Workers?
```

or

```text
Which Node Type?
```

users focus on:

```text
What Work Needs To Be Done?
```

---

# Benefits of Serverless

```text
No Cluster Management
Faster Startup
Automatic Scaling
Simplified Operations
Improved Productivity
```

---

# High-Level Architecture

Traditional:

```text
User
  │
  ▼
Cluster
  │
  ▼
Data
```

---

Serverless:

```text
User
  │
  ▼
Databricks Managed Compute
  │
  ▼
Data
```

---

# Who Manages Infrastructure?

Traditional Compute:

```text
Customer
```

manages many settings.

---

Serverless:

```text
Databricks
```

manages infrastructure.

---

# Serverless SQL Warehouses

One of the most common serverless offerings.

Used for:

```text
Analytics
Dashboards
Reporting
Business Intelligence
```

---

# Example

Analyst runs:

```sql
SELECT *
FROM sales
LIMIT 100;
```

No cluster creation required.

The query executes immediately using serverless resources.

---

# Benefits for Analysts

Analysts typically do not want to manage clusters.

Serverless provides:

```text
Simple Experience
Fast Queries
Minimal Administration
```

---

# Serverless Jobs

Databricks can execute jobs using serverless compute.

Example:

```text
Daily ETL
```

without manually managing clusters.

---

# Workflow

```text
Job Starts
     │
     ▼
Serverless Resources Allocated
     │
     ▼
Job Executes
     │
     ▼
Resources Released
```

---

# Serverless Notebooks

Interactive notebooks can also leverage serverless compute.

Benefits:

```text
Quick Startup
No Cluster Configuration
```

Developers can focus on code.

---

# Startup Time Comparison

Traditional Cluster:

```text
Create Cluster
2–10 Minutes
```

---

Serverless:

```text
Seconds
```

in many scenarios.

---

# Automatic Scaling

Serverless platforms automatically scale resources.

Users typically do not specify:

```text
Worker Counts
```

The platform handles scaling.

---

# Scaling Example

Small Query:

```text
Minimal Resources
```

---

Large Query:

```text
Additional Resources
```

allocated automatically.

---

# Resource Allocation

Serverless platforms dynamically allocate:

```text
CPU
Memory
Executors
Compute Capacity
```

based on demand.

---

# Cost Model

Traditional:

```text
Pay For Cluster Runtime
```

---

Serverless:

```text
Pay For Consumed Compute
```

depending on workload and platform pricing.

---

# Cost Benefits

Serverless often reduces costs caused by:

```text
Idle Clusters
Forgotten Clusters
Over-Provisioned Clusters
```

---

# Cost Consideration

Serverless is not always cheaper.

Cost depends on:

```text
Workload Type
Usage Patterns
Execution Frequency
```

Organizations should evaluate actual usage.

---

# Security Model

Serverless environments still support:

```text
Authentication
Authorization
Encryption
Governance
```

---

# Integration with Unity Catalog

Serverless works with:

```text
Unity Catalog
```

for governance and access control.

Benefits include:

```text
Centralized Permissions
Data Governance
Lineage
```

---

# Governance Benefits

Organizations can enforce:

```text
Access Controls
Auditing
Compliance Policies
```

without exposing infrastructure complexity.

---

# Operational Benefits

Operations teams spend less time managing:

```text
Clusters
Scaling
Runtime Maintenance
```

and more time delivering value.

---

# Traditional Cluster Challenges

Common issues:

```text
Idle Clusters
Wrong Sizing
Manual Scaling
Slow Startup
```

Serverless helps address these problems.

---

# Real-World Example

Business Intelligence Team:

Needs:

```text
Dashboards
Reports
Ad-Hoc Queries
```

Using serverless:

```text
No Cluster Administration
Fast Query Access
```

Improves productivity.

---

# Data Engineering Example

Pipeline:

```text
Read Data
Transform Data
Write Delta Tables
```

executed using:

```text
Serverless Jobs
```

instead of dedicated clusters.

---

# Data Science Example

Scientists:

```text
Explore Data
Build Features
Test Models
```

without creating clusters manually.

---

# Enterprise Adoption

Many organizations are moving toward:

```text
Serverless Analytics
Serverless ETL
Serverless SQL
```

to reduce operational overhead.

---

# Limitations

Serverless is powerful but may have limitations depending on:

```text
Cloud Provider
Region
Feature Availability
Compliance Requirements
```

Organizations should review current platform capabilities.

---

# Traditional vs Serverless

| Feature | Traditional Cluster | Serverless |
|----------|----------|----------|
| Cluster Management | Required | Not Required |
| Startup Time | Slower | Faster |
| Infrastructure Visibility | High | Low |
| Administration | Higher | Lower |
| User Simplicity | Moderate | High |
| Operational Overhead | Higher | Lower |

---

# When to Use Traditional Compute

Examples:

```text
Specialized Configurations
Custom Libraries
Advanced Infrastructure Control
Specific Compliance Requirements
```

---

# When to Use Serverless

Examples:

```text
Analytics
SQL Workloads
Standard ETL
Interactive Exploration
Rapid Development
```

---

# Common Misconception

### Serverless Means No Servers

Incorrect.

Servers still exist.

The difference:

```text
Users Do Not Manage Them
```

---

# Common Misconception

### Serverless Eliminates Costs

Incorrect.

Compute resources still cost money.

Serverless changes how resources are managed and billed.

---

# Best Practices

## Use Serverless for Simplicity

Reduce infrastructure administration.

---

## Monitor Costs

Review usage regularly.

---

## Use Unity Catalog

Strengthen governance and security.

---

## Evaluate Workloads

Not every workload requires the same compute model.

---

## Stay Current

Serverless capabilities continue to evolve.

---

# Common Interview Questions

### What is Serverless Compute?

A compute model where infrastructure is managed by Databricks rather than the customer.

---

### Does Serverless Mean No Servers Exist?

No.

Servers still exist but are managed by Databricks.

---

### What are the benefits?

```text
Faster Startup
Automatic Scaling
Reduced Administration
```

---

### What is Serverless SQL?

A serverless compute option optimized for analytics and SQL workloads.

---

### Why do organizations adopt Serverless?

To reduce operational complexity and improve productivity.

---

### Can Serverless scale automatically?

Yes.

Resource allocation is handled dynamically.

---

# Summary

Serverless Compute is a major evolution in Databricks.

Instead of managing clusters directly, users focus on:

```text
Data
Analytics
ETL
Machine Learning
```

while Databricks manages:

```text
Infrastructure
Scaling
Provisioning
Operations
```

This simplifies development, reduces operational overhead, and accelerates workload execution.

---

# Key Takeaways

✔ Serverless removes cluster management responsibilities

✔ Servers still exist but are managed by Databricks

✔ Startup times are typically faster

✔ Scaling is automatic

✔ Serverless SQL is widely used for analytics

✔ Governance remains important

✔ Costs still exist and should be monitored

✔ Many enterprises are moving toward serverless architectures

---

# Next Module

➡ 08-cost-optimization.md
