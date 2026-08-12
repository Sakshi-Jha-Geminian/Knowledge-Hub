# Databricks Cluster Types

## Learning Objectives

By the end of this module, you will understand:

- Why multiple cluster types exist
- All-Purpose Clusters
- Job Clusters
- Shared Clusters
- Single User Clusters
- Personal Compute
- Serverless Compute
- Classic Compute
- Cluster Access Modes
- Cost Differences
- Security Differences
- Performance Considerations
- Real-world Usage Scenarios
- Best Practices
- Interview Questions

---

# Introduction

Not all workloads are the same.

Consider the following scenarios:

```text
Developer Testing Code
```

```text
Nightly ETL Pipeline
```

```text
Interactive SQL Analytics
```

```text
Machine Learning Training
```

Should all of these use the same cluster?

The answer is:

```text
No
```

Different workloads require different cluster types.

Databricks provides multiple cluster options to optimize:

- Cost
- Performance
- Security
- Scalability
- User Experience

---

# Why Different Cluster Types Exist

Imagine a company with:

```text
Data Engineers
Data Analysts
Data Scientists
Platform Engineers
```

Each team works differently.

For example:

### Data Engineers

```text
ETL Pipelines
Batch Jobs
```

### Data Scientists

```text
Experiments
Model Training
```

### Analysts

```text
SQL Queries
Dashboards
```

Different cluster types serve different needs.

---

# Cluster Classification

Databricks clusters are commonly categorized as:

```text
All-Purpose Clusters

Job Clusters

Serverless Compute

Personal Compute

Shared Compute
```

In modern Databricks environments, access mode is also very important.

---

# High-Level View

```text
                  Compute
                      │
      ┌───────────────┼───────────────┐
      │               │               │
      ▼               ▼               ▼
 All-Purpose      Job Cluster     Serverless
```

---

# All-Purpose Clusters

All-Purpose Clusters are designed for:

```text
Interactive Workloads
```

These are the most commonly used clusters during development.

---

# Typical Use Cases

Examples:

```text
Notebook Development
Data Exploration
Testing
Learning
Debugging
Ad-Hoc Analysis
```

---

# All-Purpose Cluster Workflow

```text
User Opens Notebook
         │
         ▼
Attach Cluster
         │
         ▼
Run Code
```

The cluster remains available for additional work.

---

# Advantages of All-Purpose Clusters

```text
Interactive
Reusable
Easy Development
Fast Iteration
```

Ideal for engineers and analysts.

---

# Disadvantages

```text
Can Remain Idle
Higher Cost
Manual Management
```

Many organizations spend money on idle clusters.

---

# Example

```text
Cluster Name:
dev-cluster

Workers:
4

Runtime:
Databricks Runtime 16.x
```

Used throughout the day by developers.

---

# Job Clusters

Job Clusters are designed for:

```text
Automated Workloads
```

Unlike All-Purpose Clusters, they are created only when needed.

---

# Typical Use Cases

Examples:

```text
Nightly ETL
Scheduled Reports
Production Pipelines
Batch Processing
```

---

# Job Cluster Workflow

```text
Job Starts
     │
     ▼
Cluster Created
     │
     ▼
Execute Job
     │
     ▼
Job Complete
     │
     ▼
Cluster Deleted
```

---

# Benefits of Job Clusters

```text
Lower Cost
Automatic Cleanup
Isolation
Production Friendly
```

This is why many organizations prefer them.

---

# Why Job Clusters Save Money

Instead of:

```text
24 Hours Running
```

a cluster may run only:

```text
20 Minutes
```

for the workload.

Huge cost savings.

---

# Job Cluster Example

```text
Daily Customer ETL

Start:
2:00 AM

Duration:
15 Minutes

Cluster:
Created Automatically

After Completion:
Terminated
```

---

# All-Purpose vs Job Clusters

| Feature | All-Purpose | Job Cluster |
|----------|----------|----------|
| Interactive | Yes | No |
| Development | Excellent | Poor |
| Automation | Limited | Excellent |
| Cost Efficiency | Lower | Higher |
| Reusability | High | Low |
| Production Usage | Moderate | Excellent |

---

# Shared Clusters

Shared Clusters allow multiple users to use the same cluster.

Example:

```text
Analyst A
Analyst B
Analyst C
```

all connect to:

```text
Shared Cluster
```

---

# Benefits

```text
Lower Cost
Centralized Resources
Easy Collaboration
```

---

# Challenges

```text
Resource Contention
Security Considerations
Performance Variability
```

One user may impact others.

---

# Single User Clusters

A Single User Cluster is assigned to:

```text
One User
```

only.

---

# Benefits

```text
Strong Isolation
Improved Security
Predictable Performance
```

Widely used for:

```text
Machine Learning
Sensitive Data
```

---

# Shared vs Single User

| Feature | Shared | Single User |
|----------|----------|----------|
| Multiple Users | Yes | No |
| Isolation | Lower | Higher |
| Security | Moderate | High |
| Performance Predictability | Moderate | High |

---

# Personal Compute

Personal Compute is designed for:

```text
Individual Users
```

Examples:

```text
Learning
Development
Testing
```

Typically small and cost-effective.

---

# Serverless Compute

Serverless Compute removes cluster management.

Users focus on workloads rather than infrastructure.

---

# Traditional Cluster Model

```text
Create Cluster
Configure Cluster
Monitor Cluster
Resize Cluster
Delete Cluster
```

Many operational tasks exist.

---

# Serverless Model

```text
Run Workload
```

Databricks manages the infrastructure automatically.

---

# Benefits of Serverless

```text
No Cluster Management
Fast Startup
Automatic Scaling
Reduced Administration
```

---

# Common Serverless Use Cases

```text
SQL Analytics
Data Exploration
Dashboards
Interactive Queries
```

---

# Classic Compute

Classic Compute refers to traditional customer-managed cluster environments.

Users manage:

```text
Cluster Configuration
Scaling
Policies
Libraries
```

directly.

---

# Access Modes

Modern Databricks uses access modes instead of older cluster security models.

Common modes:

```text
Single User

Shared

No Isolation Shared
```

(Some legacy environments still use older modes.)

---

# Single User Access Mode

Designed for:

```text
One User
```

Benefits:

```text
Better Governance
Improved Security
Unity Catalog Compatibility
```

---

# Shared Access Mode

Multiple users share the cluster.

Benefits:

```text
Resource Efficiency
Lower Costs
```

Challenges:

```text
Shared Resources
```

---

# No Isolation Shared Mode

Legacy mode.

Generally discouraged in modern deployments.

Reasons:

```text
Reduced Security
Governance Limitations
```

---

# Choosing the Right Cluster

Decision guide:

```text
Development
      │
      ▼
All-Purpose

Production ETL
      │
      ▼
Job Cluster

Analytics
      │
      ▼
Serverless

Machine Learning
      │
      ▼
Single User Cluster
```

---

# Real-World Example

Suppose a company has:

### Data Engineers

Use:

```text
Job Clusters
```

for ETL pipelines.

---

### Analysts

Use:

```text
Serverless SQL
```

for dashboards.

---

### Data Scientists

Use:

```text
Single User Clusters
```

for experimentation.

---

### Developers

Use:

```text
All-Purpose Clusters
```

for testing.

---

# Cost Comparison

| Cluster Type | Cost Efficiency |
|-------------|-------------|
| Job Cluster | Excellent |
| Serverless | Excellent |
| Shared Cluster | Good |
| All-Purpose | Moderate |
| Idle All-Purpose | Poor |

---

# Security Comparison

| Cluster Type | Security |
|-------------|-------------|
| Single User | Highest |
| Serverless | High |
| Shared | Moderate |
| Legacy Shared | Lower |

---

# Performance Considerations

### All-Purpose

```text
Good Interactive Performance
```

---

### Job Cluster

```text
Excellent Batch Performance
```

---

### Serverless

```text
Fast Startup
```

---

### Shared

```text
May Experience Resource Competition
```

---

# Best Practices

## Use Job Clusters for Production

Avoid long-running interactive clusters.

---

## Enable Auto-Termination

Terminate idle clusters automatically.

---

## Use Single User for Sensitive Workloads

Improves security and governance.

---

## Prefer Serverless When Available

Reduces administrative effort.

---

## Avoid Idle Clusters

Idle clusters generate unnecessary costs.

---

# Common Interview Questions

### What is an All-Purpose Cluster?

An interactive cluster used for development and analysis.

---

### What is a Job Cluster?

A temporary cluster created specifically for job execution.

---

### Why are Job Clusters cheaper?

They run only during workload execution.

---

### What is Serverless Compute?

Databricks-managed compute that eliminates cluster administration.

---

### What is a Single User Cluster?

A cluster dedicated to a single user.

---

### Which cluster type is best for production ETL?

Job Clusters.

---

### Which cluster type is best for interactive development?

All-Purpose Clusters.

---

# Summary

Databricks provides multiple cluster types because different workloads have different requirements.

The most common cluster types are:

```text
All-Purpose
Job Clusters
Serverless
Shared
Single User
```

Each offers unique advantages related to:

- Cost
- Security
- Performance
- Administration

Selecting the correct cluster type is critical for building efficient and cost-effective Databricks environments.

---

# Key Takeaways

✔ All-Purpose Clusters support interactive work

✔ Job Clusters support automation

✔ Serverless removes infrastructure management

✔ Shared Clusters reduce costs

✔ Single User Clusters improve security

✔ Production ETL commonly uses Job Clusters

✔ Development commonly uses All-Purpose Clusters

✔ Cluster selection impacts cost and performance

---

# Next Module

➡ 03-cluster-policies.md
