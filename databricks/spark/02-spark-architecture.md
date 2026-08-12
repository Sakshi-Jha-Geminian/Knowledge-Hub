# Spark Architecture

## Learning Objectives

By the end of this module, you will understand:

- What Spark Architecture Is
- Driver Program
- Spark Session
- Cluster Manager
- Worker Nodes
- Executors
- Jobs, Stages, and Tasks
- DAG (Directed Acyclic Graph)
- Lazy Evaluation
- Spark Execution Flow
- Spark Cluster Modes
- Fault Tolerance
- Real-World Examples
- Interview Questions

---

# Introduction

Apache Spark is a:

```text
Distributed Computing Framework
```

which means it processes data across multiple machines.

---

# Why Architecture Matters

To understand:

```text
Performance Tuning
Debugging
Optimization
Spark UI
Databricks Internals
```

you must understand Spark Architecture.

---

# High-Level Architecture

```text
              Spark Application
                      │
                      ▼
                Driver Program
                      │
          ┌───────────┼───────────┐
          │           │           │
          ▼           ▼           ▼
      Executor 1  Executor 2  Executor 3
          │           │           │
          ▼           ▼           ▼
       Tasks       Tasks       Tasks
```

---

# Core Components

Spark architecture consists of:

```text
Driver Program
Cluster Manager
Worker Nodes
Executors
Tasks
```

---

# What is the Driver Program?

The Driver is the brain of the Spark application.

---

# Responsibilities

```text
Creates Spark Session
Builds Execution Plan
Schedules Tasks
Coordinates Executors
Collects Results
```

---

# Simple Analogy

Think of the Driver as:

```text
Project Manager
```

---

# Example

You submit:

```python
df.filter(df.salary > 50000)
```

The Driver decides:

```text
How To Execute
Where To Execute
What Resources To Use
```

---

# What is SparkSession?

SparkSession is the entry point to Spark.

---

# Example

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("Demo") \
    .getOrCreate()
```

---

# Why SparkSession?

Provides access to:

```text
Spark SQL
DataFrames
Configurations
Catalog
```

---

# What is a Cluster?

A cluster is a group of machines working together.

---

# Example

```text
Machine 1
Machine 2
Machine 3
Machine 4
```

working as a single system.

---

# Why Use Clusters?

To process:

```text
Large Data Volumes
```

faster than a single machine.

---

# What is a Cluster Manager?

Cluster Manager allocates resources.

---

# Responsibilities

```text
CPU Allocation
Memory Allocation
Executor Management
Resource Scheduling
```

---

# Popular Cluster Managers

```text
Standalone
YARN
Kubernetes
Mesos
```

---

# Databricks

Primarily uses:

```text
Spark + Kubernetes-Based Infrastructure
```

behind the scenes.

---

# What are Worker Nodes?

Worker Nodes are machines that perform actual processing.

---

# Responsibilities

```text
Run Executors
Execute Tasks
Store Temporary Data
```

---

# Simple Analogy

Workers are:

```text
Employees
```

doing the work assigned by the manager.

---

# What are Executors?

Executors are JVM processes running on Worker Nodes.

---

# Responsibilities

```text
Execute Tasks
Store Cached Data
Return Results
```

---

# Architecture Example

```text
Driver
  │
  ▼
Worker Node
   ├── Executor 1
   └── Executor 2
```

---

# Why Executors Matter

Executors provide:

```text
Parallel Processing
```

---

# Example

Instead of:

```text
1 Machine Processing 1 TB
```

Spark uses:

```text
10 Machines Processing 100 GB Each
```

---

# Execution Hierarchy

```text
Application
    │
    ▼
Job
    │
    ▼
Stage
    │
    ▼
Task
```

---

# Spark Application

A complete Spark program.

Example:

```python
sales_df.groupBy("region").sum()
```

---

# What is a Job?

A Job is created when Spark executes an action.

---

# Example Action

```python
df.show()
```

or

```python
df.count()
```

---

# Result

Spark creates:

```text
Job
```

---

# What is a Stage?

A Job is divided into Stages.

---

# Why?

To organize execution efficiently.

---

# Example

```python
df.filter(...)
  .groupBy(...)
  .sum()
```

may create:

```text
Stage 1
Stage 2
```

---

# What is a Task?

A Task is the smallest unit of work.

---

# Example

Data split into:

```text
10 Partitions
```

---

# Result

```text
10 Tasks
```

---

# Execution Flow

```text
Job
 │
 ▼
Stage
 │
 ▼
Tasks
```

---

# Example

1 TB Dataset

---

# Partitioned Into

```text
100 Partitions
```

---

# Result

```text
100 Tasks
```

executed in parallel.

---

# Directed Acyclic Graph (DAG)

One of Spark's most important concepts.

---

# What is a DAG?

A DAG represents the sequence of transformations.

---

# Example

```python
df.filter(...)
  .select(...)
  .groupBy(...)
  .sum()
```

---

# Spark Creates

```text
Execution Plan
```

called:

```text
DAG
```

---

# DAG Meaning

## Directed

Operations have direction.

---

## Acyclic

No loops.

---

## Graph

Operations connected together.

---

# Example DAG

```text
Read Data
    │
    ▼
Filter
    │
    ▼
Select
    │
    ▼
GroupBy
    │
    ▼
Aggregate
```

---

# Why DAG Matters

Allows Spark to:

```text
Optimize Queries
Reduce Work
Improve Performance
```

---

# Lazy Evaluation

One of Spark's most important optimizations.

---

# What is Lazy Evaluation?

Spark delays execution until necessary.

---

# Example

```python
df.filter(...)
```

---

# Does Spark Execute Immediately?

No.

---

# Why?

Spark only records:

```text
Transformation Logic
```

---

# Execution Starts When?

When an action occurs.

Example:

```python
df.show()
df.count()
df.collect()
```

---

# Benefits

```text
Optimization
Reduced Computation
Better Performance
```

---

# Example

```python
df.filter(...)
  .select(...)
  .groupBy(...)
```

---

# Spark Builds

```text
DAG
```

but waits.

---

# Action

```python
df.count()
```

---

# Then Spark Executes.

---

# Spark Execution Flow

Step 1:

```text
User Submits Application
```

---

# Step 2

Driver Creates DAG.

---

# Step 3

DAG Scheduler Creates Stages.

---

# Step 4

Task Scheduler Creates Tasks.

---

# Step 5

Cluster Manager Allocates Resources.

---

# Step 6

Executors Execute Tasks.

---

# Step 7

Results Return To Driver.

---

# Visualization

```text
Application
      │
      ▼
Driver
      │
      ▼
DAG
      │
      ▼
Stages
      │
      ▼
Tasks
      │
      ▼
Executors
      │
      ▼
Results
```

---

# Fault Tolerance

Spark is fault tolerant.

---

# Example

Executor Fails

```text
Executor 3 Crashes
```

---

# Spark Can

```text
Recompute Lost Tasks
```

using lineage information.

---

# What is Lineage?

History of transformations.

---

# Example

```text
Read
Filter
Aggregate
```

Spark knows how data was created.

---

# Result

Automatic recovery.

---

# Cluster Modes

Spark supports:

```text
Local Mode
Cluster Mode
Client Mode
```

---

# Local Mode

Single machine.

Used for:

```text
Learning
Development
Testing
```

---

# Cluster Mode

Production workloads.

Uses:

```text
Multiple Machines
```

---

# Real-World Databricks Example

A retail company processes:

```text
500 Million Orders Daily
```

---

# Execution

Driver:

```text
Creates DAG
Schedules Tasks
```

---

# Executors

Process:

```text
Order Data
Customer Data
Payment Data
```

in parallel.

---

# Benefits

```text
Scalability
Reliability
Performance
```

---

# Common Mistakes

## Confusing Driver and Executor

Driver manages.

Executors execute.

---

## Ignoring Partitions

Partitions determine parallelism.

---

## Misunderstanding Lazy Evaluation

Transformations alone do not execute.

---

## Assuming Tasks Equal Executors

Tasks run inside Executors.

---

# Interview Questions

### What is the Driver Program?

The coordinator of a Spark application.

---

### What are Executors?

Processes that execute tasks on worker nodes.

---

### What is a DAG?

Directed Acyclic Graph representing execution logic.

---

### What is Lazy Evaluation?

Execution is delayed until an action is triggered.

---

### What creates a Job?

An action such as:

```python
count()
show()
collect()
```

---

### What is the difference between a Stage and a Task?

A Stage contains multiple Tasks.

---

### What is Lineage?

The history of transformations used for fault recovery.

---

### Why is Spark fault tolerant?

Because lineage allows recomputation of lost data.

---

# Summary

Spark Architecture is built around:

```text
Driver
Cluster Manager
Workers
Executors
Jobs
Stages
Tasks
```

and uses:

```text
DAG
Lazy Evaluation
Lineage
```

to provide scalable and fault-tolerant distributed processing.

Understanding Spark Architecture is essential for performance tuning, troubleshooting, and working effectively with Databricks.

---

# Key Takeaways

✔ Driver coordinates execution

✔ Executors perform actual work

✔ Cluster Manager allocates resources

✔ Jobs are divided into Stages and Tasks

✔ DAG represents execution logic

✔ Spark uses Lazy Evaluation

✔ Lineage provides fault tolerance

✔ Spark processes data in parallel

✔ Architecture knowledge is critical for optimization

---

# Next Module

➡ 03-rdd.md
