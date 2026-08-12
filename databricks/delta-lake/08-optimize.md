# OPTIMIZE in Delta Lake

## Learning Objectives

By the end of this module, you will understand:

- What OPTIMIZE Is
- Why OPTIMIZE Is Needed
- The Small Files Problem
- File Compaction
- How OPTIMIZE Works
- Benefits of OPTIMIZE
- OPTIMIZE vs VACUUM
- OPTIMIZE and Query Performance
- Auto Optimize
- Performance Best Practices
- Real-World Examples
- Interview Questions

---

# Introduction

Delta Lake tables are constantly updated through:

```text
INSERT
UPDATE
DELETE
MERGE
STREAMING WRITES
```

Over time, these operations create many small files.

---

# The Small Files Problem

Imagine a table containing:

```text
10 TB Of Data
```

stored in:

```text
1,000,000 Small Files
```

---

# Why Is This Bad?

Before reading data, Spark must:

```text
Locate Files
Open Files
Read Metadata
Schedule Tasks
```

for each file.

---

# Result

```text
Slow Queries
More CPU Usage
Longer Execution Time
Higher Costs
```

---

# Example

Bad Layout:

```text
sales_table/

part-001.parquet
part-002.parquet
part-003.parquet
part-004.parquet
...
part-500000.parquet
```

---

# What Causes Small Files?

Common causes:

```text
Streaming Jobs
Frequent Inserts
Micro-Batches
MERGE Operations
Multiple Writers
```

---

# Delta Lake Solution

Delta Lake provides:

```text
OPTIMIZE
```

---

# What is OPTIMIZE?

OPTIMIZE compacts many small files into fewer larger files.

---

# Simple Definition

OPTIMIZE is:

```text
File Compaction
For Delta Tables
```

---

# Example

Before OPTIMIZE:

```text
1000 Files
100 MB Total
```

---

# After OPTIMIZE:

```text
10 Files
100 MB Total
```

---

# Important Concept

Data volume remains:

```text
The Same
```

Only file organization changes.

---

# Why OPTIMIZE Improves Performance

Spark works more efficiently when:

```text
Fewer Files Exist
```

---

# Benefits

```text
Faster Reads
Fewer Tasks
Reduced Metadata Overhead
Lower Compute Costs
```

---

# Basic Syntax

```sql
OPTIMIZE sales;
```

---

# What Happens?

Delta Lake:

```text
Reads Small Files
Compacts Data
Writes Larger Files
Updates Metadata
```

---

# Visualization

Before:

```text
50 Files
50 MB Each
```

---

# After:

```text
5 Files
500 MB Each
```

---

# Query Performance

Suppose a query scans:

```sql
SELECT *
FROM sales;
```

---

# Without OPTIMIZE

Spark must process:

```text
Thousands Of Small Files
```

---

# With OPTIMIZE

Spark processes:

```text
Fewer Larger Files
```

more efficiently.

---

# Delta Lake File Management

Over time:

```text
MERGE
UPDATE
DELETE
```

create fragmented files.

---

# OPTIMIZE Fixes Fragmentation

By reorganizing file layout.

---

# Real Example

Daily Ingestion:

```text
100 Files Per Hour
```

---

# Monthly Result

```text
72,000 Files
```

---

# Query Performance

Begins degrading.

---

# OPTIMIZE

Compacts files and restores performance.

---

# Partitioned Tables

OPTIMIZE can target specific partitions.

Example:

```sql
OPTIMIZE sales
WHERE year = 2026;
```

---

# Benefit

Only affected partitions are optimized.

---

# Why This Matters

Reduces:

```text
Compute Cost
Execution Time
```

---

# OPTIMIZE vs VACUUM

Many beginners confuse them.

---

# OPTIMIZE

Purpose:

```text
Performance Improvement
```

---

# VACUUM

Purpose:

```text
Storage Cleanup
```

---

# Comparison

| Feature | OPTIMIZE | VACUUM |
|----------|----------|----------|
| Improves Performance | Yes | No |
| Removes Old Files | No | Yes |
| Compacts Data | Yes | No |
| Storage Cleanup | No | Yes |
| Query Optimization | Yes | No |

---

# Auto Optimize

Databricks supports:

```text
Auto Optimize
```

---

# What It Does

Automatically:

```text
Compacts Files
Improves Layout
```

during writes.

---

# Benefits

```text
Less Manual Work
Consistent Performance
```

---

# Real-World Retail Example

Daily Orders Table

Receives:

```text
Millions Of Transactions
```

---

# Result

Many small files.

---

# OPTIMIZE

Compacts files and improves dashboard performance.

---

# Banking Example

Transaction tables updated continuously.

OPTIMIZE helps maintain fast reporting.

---

# Healthcare Example

Patient records generated from multiple systems.

OPTIMIZE improves query speed for analytics teams.

---

# Data Engineering Perspective

Production pipelines often schedule:

```text
Daily OPTIMIZE Jobs
```

for critical tables.

---

# Common Mistakes

## Running OPTIMIZE Too Frequently

Consumes unnecessary resources.

---

## Ignoring Small File Growth

Causes performance degradation.

---

## Confusing OPTIMIZE with VACUUM

They solve different problems.

---

## Optimizing Tiny Tables

May provide little benefit.

---

# Best Practices

## Optimize Large Tables

Focus on high-volume datasets.

---

## Schedule Regular Optimization

Based on workload patterns.

---

## Monitor File Counts

Identify fragmentation early.

---

## Optimize Active Partitions

Reduce processing cost.

---

## Combine With Z-Ordering

For maximum query performance.

---

# Workflow

```text
Small Files
      │
      ▼
OPTIMIZE
      │
      ▼
Large Files
      │
      ▼
Faster Queries
```

---

# Interview Questions

### What is OPTIMIZE?

A Delta Lake command that compacts small files into larger files.

---

### Why is OPTIMIZE needed?

To solve the small files problem.

---

### Does OPTIMIZE reduce data volume?

No.

It reorganizes files without changing data.

---

### How does OPTIMIZE improve performance?

By reducing file overhead and improving scan efficiency.

---

### What is the small files problem?

Large numbers of tiny files slow query execution.

---

### What is the difference between OPTIMIZE and VACUUM?

OPTIMIZE improves performance; VACUUM cleans storage.

---

### Can OPTIMIZE be applied to partitions?

Yes.

Specific partitions can be optimized.

---

### What is Auto Optimize?

A Databricks feature that automatically improves file layout during writes.

---

# Summary

OPTIMIZE is one of the most important Delta Lake performance features.

It solves:

```text
Small Files
Fragmentation
Slow Reads
```

by compacting data into larger, more efficient files.

Production Databricks environments rely heavily on OPTIMIZE to maintain high-performance analytics and scalable data engineering pipelines.

---

# Key Takeaways

✔ OPTIMIZE compacts small files

✔ Improves query performance

✔ Does not delete data

✔ Reduces metadata overhead

✔ Solves file fragmentation issues

✔ Different from VACUUM

✔ Supports partition-level optimization

✔ Auto Optimize can automate compaction

✔ Essential for large Delta Lake tables

---

# Next Module

➡ 09-z-ordering.md
