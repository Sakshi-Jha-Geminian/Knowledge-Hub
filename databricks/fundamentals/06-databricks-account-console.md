# Databricks Account Console

## Learning Objectives

By the end of this module, you will understand:

- What the Databricks Account Console is
- Difference between Account Console and Workspace
- Account Administrator responsibilities
- Workspaces management
- Users and Groups management
- Identity and Access Management
- Billing and Usage
- Network Configurations
- Metastores
- Unity Catalog Administration
- Enterprise Governance
- Real-world administration scenarios

---

# Introduction

As organizations grow, they often have:

- Multiple teams
- Multiple environments
- Multiple workspaces
- Multiple cloud accounts

Managing everything from individual workspaces becomes difficult.

To solve this problem, Databricks provides:

```text
Databricks Account Console
```

The Account Console acts as the central management layer for the entire Databricks account.

Think of it as:

```text
Organization-Level Administration Portal
```

while a Workspace is:

```text
Team-Level Working Environment
```

---

# Simple Analogy

Imagine a company office.

### Account Console

```text
Corporate Headquarters
```

Responsible for:

- Creating offices
- Managing employees
- Managing budgets
- Security policies

### Workspace

```text
Individual Office
```

Where daily work happens.

---

# Account Console vs Workspace

This is one of the most important concepts.

| Feature | Account Console | Workspace |
|----------|----------|----------|
| Scope | Entire Organization | Single Workspace |
| User Management | Yes | Limited |
| Billing | Yes | No |
| Workspace Creation | Yes | No |
| Governance | Yes | Limited |
| Daily Development | No | Yes |
| Notebook Development | No | Yes |
| Cluster Usage | No | Yes |

---

# High-Level Architecture

```text
               Databricks Account
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
 Workspace A     Workspace B     Workspace C
```

The Account Console manages all workspaces.

---

# Why Organizations Need Account Console

Without Account Console:

```text
Workspace A
Workspace B
Workspace C
```

would all need to be managed separately.

This creates:

- Duplicate administration
- Security risks
- Governance challenges

Account Console centralizes management.

---

# Major Responsibilities

The Account Console manages:

```text
Users
Groups
Workspaces
Billing
Cloud Resources
Identity Providers
Metastores
Governance
Networking
```

---

# Account Administrator

The highest level administrator is:

```text
Account Admin
```

Responsibilities include:

```text
Create Workspaces
Manage Users
Configure Identity
Manage Billing
Governance Administration
Security Policies
```

Account Admin has organization-wide control.

---

# Workspace Administration vs Account Administration

### Workspace Admin

Manages:

```text
Clusters
Jobs
Workspace Users
Permissions
```

inside a single workspace.

---

### Account Admin

Manages:

```text
All Workspaces
All Users
All Billing
All Governance
```

across the organization.

---

# Managing Workspaces

One major function is workspace management.

Example:

```text
Development Workspace
Testing Workspace
Production Workspace
Analytics Workspace
```

Each workspace serves a specific purpose.

---

# Example Enterprise Setup

```text
Databricks Account
      │
      ├── Dev Workspace
      │
      ├── Test Workspace
      │
      ├── QA Workspace
      │
      └── Production Workspace
```

This separation improves governance.

---

# Creating Workspaces

Account Administrators can:

```text
Create Workspace
Delete Workspace
Configure Workspace
Assign Permissions
```

Workspace creation is usually performed only once.

---

# User Management

The Account Console centrally manages users.

Example:

```text
Alice
Bob
Charlie
David
```

Users can then be assigned to workspaces.

---

# Why Centralized User Management Matters

Without central management:

```text
Workspace A Users
Workspace B Users
Workspace C Users
```

must be managed independently.

This becomes difficult at scale.

---

# Groups

Groups simplify administration.

Example:

```text
Data Engineers
Data Analysts
Data Scientists
Platform Engineers
```

Instead of assigning permissions individually.

---

# Group Example

Instead of:

```text
100 Individual Users
```

assign permissions to:

```text
Data Engineering Group
```

Much easier to manage.

---

# Identity Management

Most enterprises integrate with corporate identity providers.

Examples:

```text
Azure Active Directory
Okta
Ping Identity
Google Identity
```

Users log in using company credentials.

---

# Single Sign-On (SSO)

SSO allows:

```text
One Login
Multiple Systems
```

Benefits:

- Better user experience
- Improved security
- Easier administration

---

# Multi-Factor Authentication (MFA)

Many organizations require:

```text
Password
+
Verification Code
```

This increases security.

---

# SCIM Provisioning

SCIM enables automatic user synchronization.

Example:

```text
New Employee Created
        │
        ▼
Automatically Added to Databricks
```

No manual creation required.

---

# Billing Management

Account Console provides billing visibility.

Examples:

```text
Compute Usage
Storage Usage
SQL Usage
Job Costs
```

Administrators can monitor spending.

---

# Usage Monitoring

Organizations often track:

```text
Cost Per Team
Cost Per Workspace
Cost Per Project
```

to control expenses.

---

# Networking Configuration

Networking settings are configured at the account level.

Examples:

```text
VPC
VNet
Private Connectivity
IP Access Lists
```

These settings improve security.

---

# Private Connectivity

Many enterprises avoid public internet traffic.

Examples:

```text
AWS PrivateLink
Azure Private Link
Private Endpoints
```

Used in regulated industries.

---

# Unity Catalog Integration

The Account Console manages:

```text
Unity Catalog
```

which provides:

```text
Data Governance
Access Control
Data Lineage
Auditing
```

for the entire organization.

---

# What is a Metastore?

A metastore is a governance layer.

It contains metadata about:

```text
Catalogs
Schemas
Tables
Permissions
```

It does not store actual data.

---

# Metastore Architecture

```text
Metastore
     │
     ▼
Catalog
     │
     ▼
Schema
     │
     ▼
Table
```

We will study this in detail later.

---

# Unity Catalog Administration

Account Admins often:

```text
Create Metastores
Assign Workspaces
Manage Governance
```

at the account level.

---

# Data Governance

Governance controls:

```text
Who Can Access Data
What Data Can Be Accessed
When Access Is Allowed
```

This is critical for enterprise environments.

---

# Audit Logging

Organizations need visibility into actions.

Examples:

```text
Who Logged In
Who Accessed Data
Who Modified Permissions
Who Created Clusters
```

Audit logs provide this information.

---

# Security Benefits

The Account Console helps enforce:

```text
Identity Management
Access Control
Compliance
Governance
Monitoring
```

across all workspaces.

---

# Example Enterprise Scenario

Suppose a company has:

```text
500 Data Engineers
300 Analysts
100 Scientists
20 Admins
```

Managing users separately in every workspace would be difficult.

Instead:

```text
Account Console
      │
      ▼
Centralized Administration
```

simplifies management.

---

# Real-World Environment Example

```text
Databricks Account
        │
        ├── Dev Workspace
        ├── Test Workspace
        ├── QA Workspace
        └── Prod Workspace
                │
                ▼
         Unity Catalog
                │
                ▼
         Shared Governance
```

This is a common enterprise design.

---

# Common Interview Questions

### What is the Databricks Account Console?

The central administration portal for managing an entire Databricks account.

---

### What is the difference between Account Console and Workspace?

The Account Console manages the organization.

A Workspace is where daily work is performed.

---

### Who manages workspaces?

Account Administrators.

---

### Can Account Admins manage users?

Yes.

User management is a core responsibility.

---

### What is SCIM?

A standard for automatically provisioning users and groups.

---

### What is a Metastore?

A metadata repository used by Unity Catalog.

---

### Does the Account Console store data?

No.

It manages administration and governance.

---

# Summary

The Databricks Account Console is the centralized administration layer for an entire Databricks organization.

It manages:

- Workspaces
- Users
- Groups
- Billing
- Networking
- Governance
- Unity Catalog
- Security

while individual workspaces provide environments for development and analytics.

Understanding the Account Console is important for administrators, platform engineers, and enterprise Databricks deployments.

---

# Key Takeaways

✔ Account Console manages the entire organization

✔ Workspaces are individual working environments

✔ Account Admins manage users, workspaces, and governance

✔ Supports SSO, MFA, and SCIM

✔ Provides billing and usage visibility

✔ Manages Unity Catalog and Metastores

✔ Centralizes security and governance

✔ Essential for enterprise Databricks administration

---

# Next Module

➡ 07-cloud-platforms-supported.md
