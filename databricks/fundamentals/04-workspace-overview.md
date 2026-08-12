# Databricks Workspace Overview

## Learning Objectives

By the end of this module, you will understand:

- What a Databricks Workspace is
- Why Workspaces are important
- Workspace Architecture
- Workspace Components
- Workspace Navigation
- Managing Users and Permissions
- Workspace Objects
- Repositories
- Notebooks
- Dashboards
- Jobs
- Workspace Best Practices
- Real-world Workspace Usage

---

# Introduction

The Databricks Workspace is the primary interface where users interact with Databricks.

Think of the Workspace as:

```text
Your Databricks Home
```

Everything you do inside Databricks usually starts from the Workspace.

Examples:

- Create notebooks
- Run SQL queries
- Build dashboards
- Create jobs
- Manage files
- Access repositories
- Monitor workloads

Without the Workspace, users would have no central location to work.

---

# Simple Analogy

Imagine working in an office building.

```text
Office Building
    │
    ├── Meeting Rooms
    ├── Workstations
    ├── Conference Rooms
    ├── Storage Rooms
    └── Administration Area
```

A Databricks Workspace is similar.

```text
Workspace
    │
    ├── Notebooks
    ├── Dashboards
    ├── Jobs
    ├── Repositories
    ├── Libraries
    └── Files
```

Everything is organized in one place.

---

# What is a Workspace?

A Workspace is a collaborative environment where users create and manage Databricks resources.

A workspace contains:

```text
Users
Groups
Notebooks
Jobs
Dashboards
Clusters
Repositories
Libraries
Files
```

Multiple teams can work together within a workspace.

---

# Workspace Architecture

High-level view:

```text
Users
   │
   ▼
Workspace
   │
   ├── Notebooks
   ├── Jobs
   ├── Dashboards
   ├── Repos
   ├── Files
   └── Clusters
```

The workspace acts as a management layer.

---

# Why Workspaces Exist

Workspaces provide:

## Centralization

Everything is managed in one place.

---

## Collaboration

Multiple users can work together.

---

## Security

Access controls can be applied.

---

## Organization

Resources are grouped logically.

---

## Productivity

Developers and analysts work faster.

---

# Workspace URL

Each workspace has its own URL.

Example:

```text
https://company.cloud.databricks.com
```

Users log in through this URL.

---

# Workspace Home Page

After login, users typically see:

```text
Workspace
Catalog
Compute
Jobs
SQL
Dashboards
Files
Repos
Settings
```

The exact layout may vary by cloud provider and Databricks version.

---

# Main Workspace Components

The Workspace contains several major areas.

```text
Workspace Browser
Notebooks
Compute
Jobs
SQL
Dashboards
Repos
Catalog
Files
```

Let's understand each one.

---

# Workspace Browser

The Workspace Browser acts like a file explorer.

Example:

```text
Workspace
│
├── Engineering
│   ├── Notebook A
│   └── Notebook B
│
├── Analytics
│   └── Reports
│
└── ML
    └── Models
```

Folders help organize resources.

---

# Notebooks

Notebooks are the most commonly used workspace objects.

They allow users to write code.

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

# Why Notebooks Are Important

Notebooks allow:

```text
Development
Testing
Exploration
Data Analysis
Visualization
```

They are heavily used by:

- Data Engineers
- Analysts
- Data Scientists

---

# Notebook Structure

Example:

```text
Notebook
│
├── Cell 1
├── Cell 2
├── Cell 3
└── Cell 4
```

Each cell executes independently.

---

# Compute Section

The Compute section manages clusters.

Example:

```text
Cluster A
Cluster B
Cluster C
```

Users can:

- Create clusters
- Start clusters
- Stop clusters
- Monitor clusters

---

# Jobs Section

Jobs automate workloads.

Example:

```text
Daily ETL Job
Hourly Report
Streaming Pipeline
```

Instead of manually running notebooks.

---

# Dashboards

Dashboards display business insights.

Example:

```text
Revenue Dashboard
Customer Dashboard
Operations Dashboard
```

Used by business users and analysts.

---

# SQL Section

The SQL section supports:

```text
Queries
Dashboards
Alerts
Warehouses
```

Primarily used by analysts.

---

# Repositories (Repos)

Repos integrate Databricks with Git.

Supported providers:

```text
GitHub
GitLab
Bitbucket
Azure DevOps
```

Benefits:

```text
Version Control
Collaboration
CI/CD Integration
```

---

# Why Repos Matter

Without Git:

```text
Manual Changes
No Version History
Risk of Overwriting Code
```

With Repos:

```text
Branches
Commits
Pull Requests
Version Tracking
```

---

# Catalog

Catalog provides access to:

```text
Databases
Tables
Views
Functions
```

With Unity Catalog:

```text
Catalog
    └── Schema
           └── Table
```

---

# Files Section

Stores:

```text
CSV Files
JSON Files
Configurations
Logs
Artifacts
```

Used by workloads and notebooks.

---

# Workspace Permissions

Permissions control access.

Examples:

```text
View
Edit
Run
Manage
Delete
```

Permissions can be assigned to:

```text
Users
Groups
Service Principals
```

---

# Workspace Collaboration

Multiple users can work together.

Example:

```text
Data Engineer
     │
     ▼
Notebook

Data Analyst
     │
     ▼
Dashboard

Data Scientist
     │
     ▼
ML Model
```

All inside one workspace.

---

# Workspace Folder Structure Example

A common enterprise structure:

```text
Workspace
│
├── DataEngineering
│
├── Analytics
│
├── MachineLearning
│
├── Shared
│
├── Production
│
└── Sandbox
```

---

# Shared Folder

Used for:

```text
Team Assets
Common Notebooks
Utilities
Reusable Code
```

---

# Sandbox Area

Used for:

```text
Testing
Experimentation
Learning
Development
```

Avoid running production workloads here.

---

# Production Area

Contains:

```text
Approved Code
Production Jobs
Production Dashboards
```

Access is usually restricted.

---

# Workspace Search

Users can search for:

```text
Notebooks
Tables
Jobs
Dashboards
Files
Users
```

Large organizations rely heavily on search.

---

# Workspace Settings

Workspace settings may include:

```text
User Preferences
Permissions
Git Integration
Notifications
Security Settings
```

Administrators often manage these.

---

# Workspace Security

Security controls include:

```text
Authentication
Authorization
Role-Based Access
SSO
MFA
Audit Logs
```

Enterprise environments heavily rely on these controls.

---

# Example Workflow

A Data Engineer may:

```text
Login
   │
   ▼
Open Workspace
   │
   ▼
Open Notebook
   │
   ▼
Attach Cluster
   │
   ▼
Run Code
   │
   ▼
Create Job
```

Everything happens through the workspace.

---

# Workspace Best Practices

## Organize Folders

Use clear folder structures.

Example:

```text
Dev
Test
Prod
```

---

## Use Repositories

Always use Git integration.

Avoid storing important code only in notebooks.

---

## Apply Permissions

Grant minimum required access.

---

## Separate Environments

Maintain separate areas for:

```text
Development
Testing
Production
```

---

## Use Naming Standards

Example:

```text
etl_customer_load
sales_dashboard
prod_cluster
```

instead of:

```text
test1
newfile
cluster123
```

---

# Common Interview Questions

### What is a Databricks Workspace?

A collaborative environment used to manage Databricks resources.

---

### What can be stored in a Workspace?

- Notebooks
- Dashboards
- Jobs
- Repositories
- Files

---

### What is the purpose of Repos?

To integrate Git-based version control.

---

### Why are Workspaces important?

They provide a centralized environment for development and collaboration.

---

### Can multiple users use the same Workspace?

Yes.

Workspaces are designed for collaboration.

---

# Summary

A Databricks Workspace is the central environment where users interact with Databricks.

It provides access to:

- Notebooks
- Jobs
- Compute
- Dashboards
- SQL
- Repositories
- Catalogs
- Files

Workspaces support collaboration, governance, development, and analytics from a single interface.

Understanding the Workspace is essential before learning clusters, jobs, notebooks, and advanced Databricks administration.

---

# Key Takeaways

✔ Workspace is the main user interface

✔ Stores notebooks, jobs, dashboards, repos, and files

✔ Supports collaboration among teams

✔ Integrates with Git repositories

✔ Provides security and access control

✔ Organizes resources using folders

✔ Forms the foundation of daily Databricks work

---

# Next Module

➡ 05-control-plane-vs-data-plane.md
