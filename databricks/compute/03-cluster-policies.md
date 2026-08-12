# Databricks Cluster Policies

## Learning Objectives

By the end of this module, you will understand:

- What Cluster Policies are
- Why Cluster Policies are important
- Governance in Databricks
- Cost Control using Policies
- Security Enforcement
- Standardization
- Policy Components
- Policy Rules
- Policy Permissions
- Policy Assignment
- Enterprise Use Cases
- Best Practices
- Interview Questions

---

# Introduction

Imagine giving every employee in a company complete freedom to create any cluster they want.

Users could create:

```text
500 Worker Clusters
High-Memory Clusters
Expensive GPU Clusters
Clusters Without Auto-Termination
```

This would create:

```text
High Costs
Security Risks
Compliance Problems
Resource Waste
```

To solve these problems, Databricks provides:

```text
Cluster Policies
```

Cluster Policies help organizations control how clusters are created and used.

---

# What is a Cluster Policy?

A Cluster Policy is a set of rules that govern cluster creation and configuration.

Think of it as:

```text
A Rule Book
```

for clusters.

Instead of allowing users to choose any configuration, administrators define approved configurations.

---

# Simple Analogy

Imagine a company issues laptops.

Without rules:

```text
Employee A → Gaming Laptop
Employee B → Server Hardware
Employee C → Random Configuration
```

With policies:

```text
Standard Company Laptop
```

Everyone follows approved configurations.

Cluster Policies work similarly.

---

# Why Cluster Policies Exist

Organizations use Cluster Policies to enforce:

```text
Cost Control
Security
Governance
Compliance
Standardization
```

---

# Problems Without Policies

Example:

```text
Developer Creates
50 Worker Cluster
```

for a small notebook.

Result:

```text
Massive Waste
```

Another user creates:

```text
GPU Cluster
```

without approval.

Result:

```text
Unexpected Costs
```

Policies prevent these situations.

---

# Major Benefits

Cluster Policies provide:

```text
Consistency
Cost Reduction
Security Enforcement
Operational Simplicity
Governance
```

---

# High-Level Architecture

```text
Administrator
       │
       ▼
Cluster Policy
       │
       ▼
User Creates Cluster
       │
       ▼
Policy Applied
```

The policy acts as a gatekeeper.

---

# Governance in Databricks

Governance means:

```text
Controlling Resources
Managing Access
Enforcing Standards
```

Cluster Policies are one part of governance.

Other governance tools include:

```text
Unity Catalog
Access Controls
Audit Logs
```

---

# Policy Enforcement

When a user creates a cluster:

```text
User Request
      │
      ▼
Policy Validation
      │
      ▼
Allowed?
      │
   Yes/No
```

If the configuration violates policy rules, creation is blocked.

---

# Common Policy Controls

Administrators can control:

```text
Cluster Size
Runtime Version
Node Types
Auto-Termination
Tags
Access Modes
```

---

# Restricting Cluster Size

Example:

Allowed:

```text
1 - 10 Workers
```

Blocked:

```text
50 Workers
```

This prevents excessive spending.

---

# Restricting Runtime Versions

Organizations often standardize runtimes.

Allowed:

```text
Databricks Runtime 16.x
```

Blocked:

```text
Unsupported Versions
```

Benefits:

```text
Consistency
Security
Supportability
```

---

# Restricting Node Types

Example:

Allowed:

```text
Standard Compute Nodes
```

Blocked:

```text
Large GPU Nodes
```

unless specifically approved.

---

# Auto-Termination Policies

One of the most common policy settings.

Example:

```text
Terminate After
30 Minutes Idle
```

Benefits:

```text
Reduced Costs
```

---

# Without Auto-Termination

```text
Developer Leaves For Weekend
Cluster Remains Running
```

Result:

```text
Unnecessary Charges
```

---

# With Auto-Termination

```text
Idle Cluster
     │
     ▼
Automatically Stops
```

No wasted resources.

---

# Enforcing Tags

Organizations often require tags.

Example:

```text
Department=Finance
Environment=Production
Owner=DataEngineering
```

Tags help with:

```text
Cost Tracking
Auditing
Reporting
```

---

# Access Mode Policies

Policies can enforce:

```text
Single User

Shared

Serverless
```

depending on organizational standards.

---

# Security Enforcement

Some organizations require:

```text
Single User Clusters
```

for sensitive workloads.

Policies ensure users cannot bypass requirements.

---

# Policy Types

Common enterprise policies include:

```text
Development Policy

Production Policy

Analytics Policy

Machine Learning Policy
```

Each serves a different purpose.

---

# Development Policy Example

```text
Small Cluster
Auto-Termination Enabled
Limited Worker Count
```

Designed for experimentation.

---

# Production Policy Example

```text
Approved Runtime
Monitoring Enabled
Restricted Access
Standard Node Types
```

Designed for reliability.

---

# Machine Learning Policy Example

```text
GPU Access
ML Runtime
Single User Access
```

for data scientists.

---

# Cluster Policy Assignment

Policies can be assigned to:

```text
Users
Groups
Teams
```

Example:

```text
Data Engineers
```

receive one policy.

```text
Data Scientists
```

receive another.

---

# Group-Based Governance

Example:

```text
Data Engineers
      │
      ▼
ETL Policy

Data Scientists
      │
      ▼
ML Policy
```

This simplifies administration.

---

# Policy Permissions

Administrators can control:

```text
Who Can Use Policies
Who Can Edit Policies
Who Can Create Policies
```

Not every user should modify policies.

---

# Policy Hierarchy

Typical enterprise flow:

```text
Admin
   │
   ▼
Policy
   │
   ▼
User
   │
   ▼
Cluster
```

The policy controls cluster creation.

---

# Example Scenario

Without Policy:

```text
Worker Count = 100
```

Allowed.

With Policy:

```text
Maximum = 10
```

Cluster creation fails.

---

# Cost Governance Example

Company Rule:

```text
Development Clusters
Maximum 4 Workers
```

Benefits:

```text
Predictable Costs
```

across all teams.

---

# Security Governance Example

Requirement:

```text
PII Data Processing
```

must use:

```text
Single User Clusters
```

Policy enforces compliance.

---

# Standardization Example

All production workloads must use:

```text
Databricks Runtime 16.x
```

Policy ensures consistency.

---

# Enterprise Architecture Example

```text
Account Admin
        │
        ▼
Cluster Policies
        │
        ▼
Engineering Teams
        │
        ▼
Approved Clusters
```

This creates a governed environment.

---

# Policy Lifecycle

Typical lifecycle:

```text
Create Policy
      │
      ▼
Assign Policy
      │
      ▼
Users Create Clusters
      │
      ▼
Monitor Usage
      │
      ▼
Update Policy
```

Policies evolve with organizational needs.

---

# Monitoring Policy Usage

Administrators often review:

```text
Cluster Creation
Policy Compliance
Cost Trends
Resource Usage
```

to optimize governance.

---

# Best Practices

## Use Policies Everywhere

Avoid unrestricted cluster creation.

---

## Enable Auto-Termination

One of the easiest ways to reduce costs.

---

## Separate Dev and Production Policies

Different environments require different controls.

---

## Restrict Expensive Node Types

Prevent accidental overspending.

---

## Standardize Runtime Versions

Simplifies support and maintenance.

---

## Use Group-Based Assignment

Easier to manage than individual assignments.

---

# Common Interview Questions

### What is a Cluster Policy?

A governance mechanism that controls cluster configuration and creation.

---

### Why are Cluster Policies important?

They help enforce cost, security, and operational standards.

---

### Can policies limit cluster size?

Yes.

Administrators can define minimum and maximum worker counts.

---

### Why enforce auto-termination?

To reduce unnecessary compute costs.

---

### Can policies restrict runtime versions?

Yes.

Organizations commonly standardize approved runtimes.

---

### Who typically creates Cluster Policies?

Administrators and platform engineering teams.

---

# Summary

Cluster Policies are one of the most important governance tools in Databricks.

They help organizations control:

```text
Cost
Security
Compliance
Standardization
Operations
```

by enforcing approved cluster configurations.

Large enterprises rely heavily on Cluster Policies to maintain secure, efficient, and predictable Databricks environments.

---

# Key Takeaways

✔ Cluster Policies govern cluster creation

✔ Policies improve governance and security

✔ Policies reduce costs

✔ Auto-termination is commonly enforced

✔ Runtime versions can be standardized

✔ Node types can be restricted

✔ Policies can be assigned to groups

✔ Essential for enterprise Databricks administration

---

# Next Module

➡ 04-autoscaling.md
