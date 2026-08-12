# Databricks Clusters

## Learning Objectives

By the end of this module, you will understand:

- What a Databricks Cluster is
- Why Clusters are needed
- Cluster Architecture
- Driver Nodes
- Worker Nodes
- Executors
- Resource Allocation
- Spark Execution Flow
- Cluster Creation
- Cluster Configuration
- Cluster Sizing
- Cluster Monitoring
- Cluster Best Practices
- Real-world Cluster Usage

---

# Introduction

A Cluster is one of the most important concepts in Databricks.

Almost everything you do in Databricks requires compute power.

Examples:

- Running notebooks
- Processing data
- Executing SQL queries
- Building machine learning models
- Running ETL pipelines
- Streaming analytics

The component that provides this compute power is called a:

```text
Cluster
```

Without clusters, Databricks cannot process data.

---

# What is a Cluster?

A Cluster is a collection of virtual machines working together to process data.

Think of it as:

```text
A Team of Computers
```

instead of a single computer.

Example:

```text
Laptop
   │
   ▼
4 CPU Cores

Databricks Cluster
   │
   ▼
100+ CPU Cores
```

This allows Databricks to process large datasets much faster.

---

# Why Do We Need Clusters?

Imagine you have:

```text
1 TB Data
```

A single machine might take hours to process it.

Instead:

```text
10 Machines
```

can process different parts of the data simultaneously.

This is called:

```text
Distributed Computing
```

---

# Distributed Computing

Distributed Computing means:

```text
One Large Problem
       │
       ▼
Split Into Smaller Problems
       │
       ▼
Multiple Machines Process Them
```

Example:

```text
100 GB Data

Machine 1 → 25 GB
Machine 2 → 25 GB
Machine 3 → 25 GB
Machine 4 → 25 GB
```

Each machine works in parallel.

---

# Cluster Architecture

Basic architecture:

```text
             Cluster
                 │
      ┌──────────┴──────────┐
      │                     │
      ▼                     ▼
 Driver Node         Worker Nodes
```

This architecture is used in Apache Spark and Databricks.

---

# Main Cluster Components

Every cluster contains:

```text
Driver Node
Worker Nodes
Executors
Memory
CPU
Storage
```

Let's understand each component.

---

# Driver Node

The Driver Node is the brain of the cluster.

Responsibilities:

```text
Receive User Code
Create Execution Plan
Coordinate Workers
Track Job Progress
Return Results
```

There is usually:

```text
1 Driver Node
```

per cluster.

---

# Driver Node Example

Suppose you execute:

```python
df.groupBy("country").count()
```

The Driver:

```text
Receives Command
Creates Execution Plan
Distributes Tasks
Collects Results
```

The Driver itself usually does not process all data.

---

# Worker Nodes

Worker Nodes perform the actual computation.

Responsibilities:

```text
Read Data
Transform Data
Execute Tasks
Store Intermediate Results
```

A cluster can have:

```text
1 Worker
5 Workers
20 Workers
100 Workers
```

depending on workload size.

---

# Worker Node Example

Dataset:

```text
100 Million Rows
```

Split across:

```text
Worker 1
Worker 2
Worker 3
Worker 4
```

Each worker processes a portion of the data.

---

# Executors

Executors run inside worker nodes.

Example:

```text
Worker Node
     │
     └── Executor
```

Executors perform:

```text
Transformations
Actions
Calculations
Caching
```

---

# Executor Example

```text
Worker 1
   ├── Executor A
   └── Executor B

Worker 2
   ├── Executor C
   └── Executor D
```

Each executor processes Spark tasks.

---

# Cluster Visualization

```text
                    Driver
                       │
      ┌────────────────┼────────────────┐
      │                │                │
      ▼                ▼                ▼
 Worker 1         Worker 2         Worker 3
      │                │                │
 Executor         Executor         Executor
```

---

# How Spark Uses Clusters

Spark distributes work across workers.

Example:

```python
df.count()
```

Execution:

```text
Driver
   │
   ▼
Task Distribution
   │
   ▼
Workers
   │
   ▼
Results
```

This enables parallel processing.

---

# Cluster Resources

Clusters provide resources such as:

```text
CPU
Memory
Disk
Network
```

These resources determine performance.

---

# CPU

CPU performs computations.

Example:

```text
4 Core
8 Core
16 Core
32 Core
64 Core
```

More cores generally mean more parallelism.

---

# Memory

Memory stores:

```text
Data
Cache
Intermediate Results
```

Example:

```text
16 GB
32 GB
64 GB
256 GB
```

Memory is critical for Spark performance.

---

# Storage

Clusters may use local storage for:

```text
Temporary Files
Shuffle Data
Logs
```

Permanent data is usually stored in:

```text
S3
ADLS
GCS
```

not on the cluster.

---

# Cluster Creation

Users create clusters through Databricks.

Example:

```text
Compute
   │
   ▼
Create Cluster
```

Configuration options are selected during creation.

---

# Typical Cluster Configuration

Example:

```text
Cluster Name:
sales-etl-cluster

Workers:
4

Runtime:
Databricks Runtime 16.x

Autoscaling:
Enabled
```

---

# Databricks Runtime

Databricks Runtime is the software package installed on a cluster.

Includes:

```text
Apache Spark
Libraries
Optimizations
Utilities
```

It is the operating environment of the cluster.

---

# Runtime Versions

Examples:

```text
Databricks Runtime 15.x
Databricks Runtime 16.x
ML Runtime
Photon Runtime
```

Each version contains specific features.

---

# Cluster Startup Process

```text
Create Cluster
       │
       ▼
Provision Virtual Machines
       │
       ▼
Install Runtime
       │
       ▼
Start Driver
       │
       ▼
Start Workers
       │
       ▼
Cluster Ready
```

---

# Cluster States

Clusters move through different states.

```text
Pending
Running
Restarting
Terminating
Terminated
```

---

# Pending State

Cluster resources are being provisioned.

Example:

```text
Starting EC2 Instances
Starting Azure VMs
```

---

# Running State

Cluster is available for use.

You can:

```text
Run Notebooks
Execute Jobs
Query Data
```

---

# Restarting State

Cluster is rebooting.

Usually occurs after:

```text
Configuration Changes
Runtime Updates
```

---

# Terminating State

Cluster shutdown has started.

Resources are being released.

---

# Terminated State

Cluster is completely stopped.

No compute charges occur after termination.

---

# Attaching a Notebook

Before running code:

```text
Notebook
     │
     ▼
Attach Cluster
```

The notebook uses that cluster's compute resources.

---

# Example Notebook Flow

```python
df = spark.read.csv("sales.csv")
df.show()
```

Flow:

```text
Notebook
   │
   ▼
Cluster
   │
   ▼
Spark Execution
   │
   ▼
Results
```

---

# Cluster Sizing

Cluster size depends on workload.

Small workload:

```text
1 Driver
2 Workers
```

Large workload:

```text
1 Driver
20 Workers
```

Very large workload:

```text
1 Driver
100+ Workers
```

---

# Small Cluster Example

Use for:

```text
Learning
Development
Testing
```

Example:

```text
1 Driver
1 Worker
```

---

# Medium Cluster Example

Use for:

```text
ETL Pipelines
Analytics
Reporting
```

Example:

```text
1 Driver
4 Workers
```

---

# Large Cluster Example

Use for:

```text
Massive Data Processing
Streaming
Machine Learning
```

Example:

```text
1 Driver
50 Workers
```

---

# Cluster Monitoring

Databricks provides cluster monitoring.

Metrics include:

```text
CPU Usage
Memory Usage
Executor Activity
Job Performance
```

Monitoring helps identify bottlenecks.

---

# Common Cluster Problems

## Insufficient Memory

Symptoms:

```text
OutOfMemory Errors
Slow Performance
```

---

## Too Few Workers

Symptoms:

```text
Long Execution Times
Task Backlogs
```

---

## Too Many Workers

Symptoms:

```text
Higher Costs
Unused Resources
```

---

# Cluster Best Practices

## Use Autoscaling

Allow clusters to grow and shrink automatically.

---

## Terminate Idle Clusters

Avoid unnecessary costs.

---

## Use Appropriate Runtime Versions

Stay updated with supported runtimes.

---

## Monitor Resource Usage

Track:

```text
CPU
Memory
Storage
```

regularly.

---

## Separate Dev and Production Clusters

Avoid running production jobs on development clusters.

---

# Real-World Example

An e-commerce company processes:

```text
Customer Orders
Payments
Inventory
Website Logs
```

every night.

Workflow:

```text
Job Starts
    │
    ▼
Cluster Starts
    │
    ▼
Data Processed
    │
    ▼
Results Stored
    │
    ▼
Cluster Terminates
```

This saves cost and improves efficiency.

---

# Common Interview Questions

### What is a Databricks Cluster?

A collection of virtual machines used to process workloads.

---

### What is the Driver Node?

The component that coordinates Spark execution.

---

### What are Worker Nodes?

Machines that perform actual data processing.

---

### What are Executors?

Processes running on worker nodes that execute Spark tasks.

---

### Does a Cluster Store Data Permanently?

No.

Data is usually stored in cloud storage such as:

- S3
- ADLS
- GCS

---

### Why Use Multiple Workers?

To process data in parallel and improve performance.

---

# Summary

Clusters are the compute foundation of Databricks.

They consist of:

```text
Driver Node
Worker Nodes
Executors
```

and provide:

```text
CPU
Memory
Storage
Network Resources
```

for running notebooks, jobs, analytics, machine learning, and streaming workloads.

Understanding clusters is essential because almost every Databricks workload depends on them.

---

# Key Takeaways

✔ Clusters provide compute resources

✔ Driver coordinates execution

✔ Workers process data

✔ Executors perform Spark tasks

✔ Clusters support distributed computing

✔ More workers increase parallelism

✔ Data is stored separately from clusters

✔ Monitoring is critical for performance

✔ Cluster sizing affects both cost and performance

---

# Next Module

➡ 02-cluster-types.md
