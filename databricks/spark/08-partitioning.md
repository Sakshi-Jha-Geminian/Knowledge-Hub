# Spark Partitioning

## Learning Objectives

By the end of this module, you will understand:

- What a Partition Is
- Why Spark Uses Partitions
- Distributed Data Processing
- Parallelism
- Partition Count
- Input Partitions
- Output Partitions
- `repartition()`
- `coalesce()`
- Hash Partitioning
- Range Partitioning
- Partitioning Key
- Shuffle
- Partition Pruning
- Data Skew
- Small Files Problem
- Partition Size
- Partitioning in DataFrames
- Partitioning in RDDs
- Performance Optimization
- Real-World Examples
- Interview Questions

---

# Introduction

Spark processes large datasets by dividing them into smaller logical pieces called:

```text
Partitions
```

A partition is a logical chunk of data that can be processed independently by a Spark task.

---

# Simple Definition

Think of a large dataset:

```text
1 TB of Data
```

Instead of processing the entire dataset on one machine, Spark divides it:

```text
1 TB
 │
 ├── Partition 1
 ├── Partition 2
 ├── Partition 3
 ├── Partition 4
 ├── ...
 └── Partition N
```

These partitions can be processed in parallel.

---

# Why Does Spark Use Partitions?

The primary reason is:

```text
Parallel Processing
```

Instead of:

```text
One Machine
    ↓
Process Everything
```

Spark can use:

```text
Executor 1 → Partition 1
Executor 2 → Partition 2
Executor 3 → Partition 3
Executor 4 → Partition 4
```

---

# Partition and Task

A very important relationship is:

```text
One Partition
      ↓
One Task
```

For a given stage, Spark generally creates one task for each partition that stage needs to process.

---

# Example

Suppose a stage has:

```text
100 Partitions
```

Spark can create approximately:

```text
100 Tasks
```

for that stage.

---

# Important

A task does not mean:

```text
One Executor
```

Instead:

```text
Executor
   │
   ├── Task
   ├── Task
   ├── Task
   └── Task
```

An executor can run multiple tasks over time and concurrently depending on its available cores and configuration.

---

# Parallelism

Suppose:

```text
8 Partitions
```

and the cluster has enough available task slots.

Spark can process:

```text
Partition 1 ──┐
Partition 2 ──┤
Partition 3 ──┤
Partition 4 ──┤
Partition 5 ──┼──→ Parallel Processing
Partition 6 ──┤
Partition 7 ──┤
Partition 8 ──┘
```

---

# More Partitions ≠ Always Better

It may seem that:

```text
More Partitions
=
More Performance
```

This is not always true.

Too few partitions can cause:

```text
Underutilized Cluster
```

Too many partitions can cause:

```text
Scheduling Overhead
Small Tasks
Excessive Metadata
```

---

# Finding Partition Count

For an RDD:

```python
rdd.getNumPartitions()
```

---

# Example

```python
print(rdd.getNumPartitions())
```

Output:

```text
8
```

---

# DataFrame

You can inspect the underlying RDD partition count in PySpark:

```python
df.rdd.getNumPartitions()
```

However, remember that converting through the RDD API can change how you reason about the execution plan, so use this mainly for inspection.

---

# Creating an RDD With Partitions

Example:

```python
rdd = sc.parallelize(
    [1,2,3,4,5,6,7,8],
    4
)
```

This requests:

```text
4 Partitions
```

---

# Possible Distribution

```text
Partition 1 → [1,2]
Partition 2 → [3,4]
Partition 3 → [5,6]
Partition 4 → [7,8]
```

The exact distribution can depend on the API and partitioning behavior.

---

# Input Partitions

When Spark reads data, the source determines how the input can be split into partitions.

---

# Example

Suppose a large file can be divided into:

```text
Partition 1
Partition 2
Partition 3
Partition 4
```

Spark can process these portions independently.

---

# File-Based Input

For distributed file formats such as:

```text
Parquet
Delta
ORC
```

Spark can use file and file-split information to create input partitions.

---

# Important

A:

```text
File
```

is not necessarily equal to:

```text
Partition
```

One file may be processed through one or more input partitions depending on its size and the source configuration.

Likewise, multiple small files can be combined into fewer input partitions.

---

# Partition Size

The ideal partition size depends on:

```text
Dataset
Cluster
Workload
File Format
Transformation
```

There is no universal perfect partition size.

---

# Too Few Partitions

Suppose:

```text
1 TB
```

is divided into:

```text
4 Partitions
```

Each partition is very large.

---

# Problem

Only a few tasks may be available.

Result:

```text
Cluster Resources
      ↓
Underutilized
```

---

# Too Many Partitions

Suppose:

```text
1 GB
```

is divided into:

```text
1 Million Partitions
```

---

# Problem

Spark may spend excessive time managing tiny tasks.

---

# Goal

Choose a partitioning strategy that provides:

```text
Good Parallelism
+
Reasonable Task Size
+
Acceptable Overhead
```

---

# `repartition()`

`repartition()` changes the number of partitions.

---

# Example

```python
df2 = df.repartition(20)
```

This requests:

```text
20 Partitions
```

---

# Important

`repartition()` generally involves:

```text
Shuffle
```

because data may need to move between executors.

---

# Example

Before:

```text
Partition 1
Partition 2
Partition 3
```

After:

```text
Partition 1
Partition 2
...
Partition 20
```

Data may be redistributed across the cluster.

---

# Repartition by Column

You can also repartition based on a column.

```python
df.repartition(
    "customer_id"
)
```

---

# With Number of Partitions

```python
df.repartition(
    20,
    "customer_id"
)
```

---

# Why Repartition by Key?

Suppose you frequently process:

```text
customer_id
```

Grouping records by that key can make later key-based operations more predictable.

---

# But

Repartitioning itself can be expensive because of:

```text
Shuffle
```

Therefore:

```text
Do Not Repartition Unnecessarily
```

---

# `coalesce()`

`coalesce()` is commonly used to reduce the number of partitions.

---

# Example

```python
df2 = df.coalesce(5)
```

If the original DataFrame has:

```text
20 partitions
```

the result requests:

```text
5 partitions
```

---

# Difference

```text
repartition()
```

can increase or decrease partitions and generally causes a shuffle.

```text
coalesce()
```

is designed primarily to reduce partitions and can often avoid a full shuffle by combining existing partitions.

---

# Example

```text
Before:

P1
P2
P3
P4
P5
P6
P7
P8

coalesce(4)

After:

P1+P2
P3+P4
P5+P6
P7+P8
```

The exact grouping is implementation-dependent.

---

# `repartition()` vs `coalesce()`

| Feature | `repartition()` | `coalesce()` |
|---|---|---|
| Increase partitions | Yes | Generally no |
| Decrease partitions | Yes | Yes |
| Full shuffle | Generally yes | Usually avoided |
| Data redistribution | Yes | Limited |
| Use for increasing parallelism | Yes | No |
| Use for reducing partitions | Yes | Yes |

---

# Important Rule

If you need to:

```text
Increase Partitions
```

use:

```python
repartition()
```

If you simply need to:

```text
Reduce Partitions
```

consider:

```python
coalesce()
```

---

# Partitioning and Shuffle

Partitioning is closely connected to:

```text
Shuffle
```

---

# Example

Suppose data is:

```text
Partition 1 → A, B
Partition 2 → A, C
Partition 3 → B, C
```

Now execute:

```python
groupBy("key")
```

Spark needs matching keys to be brought together.

---

# Shuffle

```text
A ─────────────→ Partition A
A ─────────────→ Partition A

B ─────────────→ Partition B
B ─────────────→ Partition B

C ─────────────→ Partition C
C ─────────────→ Partition C
```

---

# Why Is This Expensive?

Shuffle can involve:

```text
Network Transfer
Serialization
Disk Spill
Memory Usage
Task Coordination
```

---

# Hash Partitioning

Hash partitioning assigns records to partitions using a hash function.

Conceptually:

```text
partition =
hash(key) % number_of_partitions
```

---

# Example

Suppose:

```text
Number of Partitions = 4
```

For a key:

```text
customer_id = 101
```

Spark computes a hash and maps it to one partition.

---

# Result

All records with the same partitioning key can be directed to the same target partition.

---

# Why Is This Useful?

Hash partitioning is useful for operations such as:

```text
groupBy
join
reduceByKey
```

---

# Range Partitioning

Range partitioning divides data according to value ranges.

---

# Example

Suppose:

```text
Customer IDs:
1–100
101–200
201–300
301–400
```

Partitions might be:

```text
P1 → 1–100
P2 → 101–200
P3 → 201–300
P4 → 301–400
```

---

# Advantages

Range partitioning can be useful for:

```text
Ordered Data
Range Queries
Sorting
```

---

# Disadvantage

Poorly chosen ranges can cause:

```text
Data Imbalance
```

---

# Partition Key

A partition key is a column or value used to determine where records should be placed.

---

# Example

```text
customer_id
```

could be a partition key.

---

# Good Partition Key

A good partition key generally has:

```text
High Cardinality
Reasonably Even Distribution
```

---

# Bad Partition Key

Suppose:

```text
country
```

has only:

```text
5 values
```

but the dataset contains:

```text
1 Billion Records
```

This may create uneven partitions.

---

# Data Skew

Data skew occurs when some partitions contain significantly more data than others.

---

# Example

```text
Partition 1 → 10 GB
Partition 2 → 10 GB
Partition 3 → 10 GB
Partition 4 → 500 GB
```

---

# Problem

Most tasks finish quickly:

```text
P1 ✓
P2 ✓
P3 ✓
```

But:

```text
P4
 │
 └── Still Running
```

---

# Result

The entire stage may wait for the slow task.

This is sometimes called:

```text
Straggler Task
```

---

# Causes of Data Skew

Common causes include:

```text
Highly Popular Key
Uneven Data Distribution
Hot Partition
Poor Partitioning Strategy
```

---

# Example

Social media data:

```text
user_id
```

Suppose one celebrity account has:

```text
500 Million Records
```

while normal users have:

```text
1000 Records
```

A key-based aggregation can become highly skewed.

---

# Handling Data Skew

Common strategies include:

```text
Salting
Broadcast Joins
Better Partitioning
Adaptive Query Execution
Pre-Aggregation
```

---

# Salting

Salting adds an additional value to a heavily skewed key.

---

# Example

Instead of:

```text
celebrity
```

use:

```text
celebrity_1
celebrity_2
celebrity_3
celebrity_4
```

---

# Result

The large key can be distributed across multiple partitions.

---

# Partition Pruning

Partition pruning is different from Spark's in-memory partitioning concept.

In data storage systems, partition pruning means:

```text
Read Only Relevant Data Partitions
```

---

# Example

Suppose data is stored by:

```text
year
month
```

Directory structure:

```text
year=2024/
year=2025/
year=2026/
```

Query:

```sql
SELECT *
FROM sales
WHERE year = 2026
```

Spark can potentially avoid reading:

```text
year=2024
year=2025
```

and scan only:

```text
year=2026
```

---

# Important Distinction

Do not confuse:

```text
Spark Execution Partitions
```

with:

```text
Storage/Table Partitions
```

They are related concepts but are not the same thing.

---

# Spark Execution Partition

Used for:

```text
Parallel Processing
```

---

# Storage Partition

Used for:

```text
Data Organization
Data Skipping
Partition Pruning
```

---

# Small Files Problem

A large number of tiny files can create inefficient workloads.

---

# Example

Instead of:

```text
100 files × 1 GB
```

you may have:

```text
10,000,000 files × 10 KB
```

---

# Problems

Small files can cause:

```text
Metadata Overhead
Many Tasks
Slow File Listing
Poor I/O Efficiency
```

---

# Solution

Use appropriate:

```text
Compaction
OPTIMIZE
Coalescing
File Management
```

For Delta Lake, `OPTIMIZE` can help compact small files.

---

# Partitioning in RDDs

RDDs expose partition-related APIs.

Examples:

```python
rdd.getNumPartitions()
```

and:

```python
rdd.repartition(10)
```

---

# Pair RDD Partitioning

Pair RDDs can use partitioners.

Examples include:

```text
HashPartitioner
RangePartitioner
```

---

# HashPartitioner

Conceptually:

```text
key
 ↓
hash()
 ↓
partition number
```

---

# RangePartitioner

Conceptually:

```text
key
 ↓
range
 ↓
partition
```

---

# Partitioning in DataFrames

DataFrames can be repartitioned using:

```python
df.repartition(10)
```

or:

```python
df.repartition(
    10,
    "customer_id"
)
```

---

# Reducing Partitions

```python
df.coalesce(5)
```

---

# Inspecting Partitions

For debugging:

```python
df.rdd.getNumPartitions()
```

---

# Important

The number of partitions can change throughout a Spark query.

For example:

```text
Input
 ↓
Filter
 ↓
Join
 ↓
GroupBy
 ↓
Output
```

Different stages may have different partition layouts.

---

# AQE and Partition Optimization

Modern Spark includes:

```text
Adaptive Query Execution
```

AQE can dynamically adjust certain aspects of query execution based on runtime statistics.

---

# AQE Can Help With

Depending on Spark version and configuration, AQE can help with:

```text
Coalescing Post-Shuffle Partitions
Handling Certain Skewed Joins
Changing Join Strategies
```

---

# Why This Matters

You do not always need to manually tune every partition count.

Spark can sometimes optimize execution dynamically.

---

# Partitioning Best Practices

## Avoid Arbitrary Partition Counts

Do not blindly use:

```python
repartition(1000)
```

without understanding the workload.

---

## Consider Data Size

Partition strategy should reflect:

```text
Data Volume
Task Size
Cluster Capacity
```

---

## Watch for Skew

Look for:

```text
One Very Large Partition
```

---

## Avoid Excessive Shuffles

Every unnecessary repartition can increase:

```text
Network Traffic
Execution Time
```

---

## Use `coalesce()` Carefully

It can reduce partition count efficiently, but excessive coalescing can create oversized partitions.

---

## Use Storage Partitioning Strategically

Choose columns commonly used for filtering, while avoiding extremely high-cardinality or excessively fragmented layouts without a reason.

---

# Real-World Example

## E-Commerce Orders

Suppose:

```text
5 TB Orders
```

stored across many files.

---

# Processing

```text
Read Orders
      ↓
Filter Date
      ↓
Group By Customer
      ↓
Aggregate
```

---

# Important Considerations

```text
Input Partition Count
        ↓
Filtering
        ↓
Shuffle
        ↓
Aggregation Partitions
```

---

# Data Skew

Suppose:

```text
Customer A → 500 Million Orders
Customer B → 1,000 Orders
Customer C → 2,000 Orders
```

Customer A can create a hot key.

---

# Possible Solutions

```text
Salting
AQE
Pre-Aggregation
Better Join Strategy
```

---

# Interview Questions

### What is a partition in Spark?

A logical chunk of distributed data processed by a task.

---

### Why does Spark partition data?

To enable distributed and parallel processing.

---

### What is the relationship between partitions and tasks?

For a given stage, Spark generally creates one task per partition that the stage processes.

---

### What is repartition()?

An operation that changes the number of partitions and generally performs a shuffle.

---

### What is coalesce()?

An operation primarily used to reduce the number of partitions, often without a full shuffle.

---

### What is the difference between repartition() and coalesce()?

`repartition()` can increase or decrease partitions and generally shuffles data.

`coalesce()` primarily reduces partitions while attempting to avoid a full shuffle.

---

### What is a shuffle?

Redistribution of data between partitions, usually involving network transfer.

---

### Why is shuffle expensive?

Because it can involve:

```text
Network I/O
Disk I/O
Serialization
Memory
```

---

### What is data skew?

Uneven distribution of records across partitions.

---

### What is a skewed partition?

A partition containing substantially more data than others.

---

### How can data skew be handled?

Possible techniques include:

```text
Salting
Broadcast Joins
Pre-Aggregation
AQE
Better Partitioning
```

---

### What is partition pruning?

Skipping irrelevant storage partitions when a query filter allows Spark to determine they cannot contain matching data.

---

### Are storage partitions and Spark execution partitions the same?

No.

Storage partitions organize data on storage, while execution partitions represent chunks processed by Spark tasks.

---

### What is hash partitioning?

Partitioning records based on a hash of their key.

---

### What is range partitioning?

Partitioning records according to ranges of key values.

---

### Does increasing the number of partitions always improve performance?

No.

Too few partitions can limit parallelism, while too many can introduce scheduling and task overhead.

---

# Summary

Partitioning is one of the most important concepts in Spark performance.

The fundamental relationship is:

```text
Data
 ↓
Partitions
 ↓
Tasks
 ↓
Executors
 ↓
Parallel Processing
```

Partitioning determines how data is distributed across the cluster.

---

# Key Takeaways

✔ Spark divides data into partitions

✔ Partitions enable parallel processing

✔ A stage generally has one task per partition

✔ Too few partitions can underutilize the cluster

✔ Too many partitions can create overhead

✔ `repartition()` generally causes a shuffle

✔ `coalesce()` is primarily used to reduce partitions

✔ Hash partitioning distributes records using a hash of the key

✔ Range partitioning distributes records based on value ranges

✔ Poor partitioning can cause data skew

✔ Shuffles are expensive

✔ Partition pruning reduces unnecessary storage reads

✔ Storage partitions and execution partitions are different concepts

✔ AQE can dynamically optimize certain partition-related decisions

---

# Complete Partitioning Model

```text
                    Dataset
                       │
                       ▼
                Input Partitions
                       │
                       ▼
                    Tasks
                       │
                       ▼
                  Executors
                       │
                       ▼
              Transformation
                       │
             ┌─────────┴─────────┐
             │                   │
          Narrow               Wide
             │                   │
             │                Shuffle
             │                   │
             │                   ▼
             │            New Partitions
             │                   │
             └─────────┬─────────┘
                       ▼
                    Output
```

---

# Final Mental Model

Remember:

```text
Partition
   =
Unit of Parallel Processing

Task
   =
Work performed on a partition

Executor
   =
Process that runs tasks

Shuffle
   =
Redistribution of data

Repartition
   =
Explicitly redistribute/change partitions

Coalesce
   =
Reduce partitions with less redistribution

Skew
   =
Uneven data distribution
```

---

# Next Module

➡ `09-caching.md`
