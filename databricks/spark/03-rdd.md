# Resilient Distributed Datasets (RDD)

## Learning Objectives

By the end of this module, you will understand:

- What an RDD Is
- Why RDD Was Created
- RDD Characteristics
- How RDD Works
- Creating RDDs
- Transformations
- Actions
- Lazy Evaluation
- Lineage
- Fault Tolerance
- Persistence and Caching
- RDD Limitations
- RDD vs DataFrame
- Real-World Examples
- Interview Questions

---

# Introduction

RDD is the fundamental data structure of Apache Spark.

When Spark was first released, RDD was the primary way to process distributed data.

---

# What is an RDD?

RDD stands for:

```text
Resilient Distributed Dataset
```

---

# Simple Definition

An RDD is:

```text
A Distributed Collection Of Data
```

that can be processed in parallel across multiple machines.

---

# Breaking Down the Name

## Resilient

Can recover from failures.

---

## Distributed

Stored across multiple machines.

---

## Dataset

A collection of records.

---

# Why Was RDD Created?

Traditional systems had limitations:

```text
Single Machine Processing
Poor Scalability
Limited Fault Tolerance
```

---

# Spark Needed

```text
Distributed Processing
Fault Recovery
Parallel Computation
```

---

# RDD Solution

RDD became Spark's core abstraction.

---

# Example

Dataset:

```text
1
2
3
4
5
```

---

# Spark May Store It As

```text
Partition 1
1
2

Partition 2
3
4

Partition 3
5
```

---

# Result

Multiple machines process data simultaneously.

---

# Key Characteristics of RDD

RDDs have five major characteristics.

---

# 1. Distributed

Data is spread across partitions.

---

# Example

```text
Machine 1 → Partition A

Machine 2 → Partition B

Machine 3 → Partition C
```

---

# Benefit

```text
Parallel Processing
```

---

# 2. Immutable

RDDs cannot be modified.

---

# Example

Original RDD:

```text
1
2
3
```

---

# Apply Transformation

```python
rdd.map(lambda x: x * 2)
```

---

# Result

Creates:

```text
New RDD
```

---

# Original RDD

Remains unchanged.

---

# Why Immutability Matters

Provides:

```text
Consistency
Fault Tolerance
Predictability
```

---

# 3. Fault Tolerant

RDDs can recover lost data.

---

# Example

Machine crashes.

---

# Spark Uses

```text
Lineage
```

to recreate lost partitions.

---

# 4. Lazy Evaluation

RDD transformations are not executed immediately.

---

# Example

```python
rdd.filter(lambda x: x > 10)
```

---

# Spark Does Not Execute Yet.

---

# Instead

Spark records the operation.

---

# Execution Starts When

An action is triggered.

Example:

```python
count()
collect()
first()
```

---

# 5. Partitioned

RDDs are divided into partitions.

---

# Why?

Partitions enable:

```text
Parallel Execution
```

---

# Creating RDDs

Several methods exist.

---

# Method 1: Parallelize Collection

```python
data = [1,2,3,4,5]

rdd = spark.sparkContext.parallelize(data)
```

---

# What Happens?

Spark distributes the data.

---

# Method 2: Read File

```python
rdd = spark.sparkContext.textFile("/data/file.txt")
```

---

# Result

File becomes an RDD.

---

# Viewing RDD Data

```python
rdd.collect()
```

---

# Example Output

```text
[1,2,3,4,5]
```

---

# RDD Operations

RDD operations are divided into:

```text
Transformations
Actions
```

---

# Transformations

Transformations create new RDDs.

---

# Examples

```python
map()
filter()
flatMap()
distinct()
union()
```

---

# Example: map()

```python
rdd.map(lambda x: x * 2)
```

---

# Input

```text
1
2
3
```

---

# Output

```text
2
4
6
```

---

# Example: filter()

```python
rdd.filter(lambda x: x > 2)
```

---

# Output

```text
3
4
5
```

---

# Example: distinct()

Removes duplicates.

```python
rdd.distinct()
```

---

# Example Input

```text
1
1
2
2
3
```

---

# Output

```text
1
2
3
```

---

# What Are Actions?

Actions trigger execution.

---

# Examples

```python
count()
collect()
take()
first()
reduce()
```

---

# Example

```python
rdd.count()
```

---

# Result

Returns:

```text
Total Number Of Records
```

---

# collect()

```python
rdd.collect()
```

---

# Result

Returns all records to Driver.

---

# Warning

Avoid collect() on huge datasets.

---

# Why?

May cause:

```text
Driver Memory Issues
```

---

# take()

```python
rdd.take(5)
```

---

# Result

Returns first five records.

---

# reduce()

```python
rdd.reduce(lambda x,y: x+y)
```

---

# Input

```text
1
2
3
4
```

---

# Output

```text
10
```

---

# Transformation vs Action

| Transformation | Action |
|---------------|---------|
| Creates New RDD | Returns Result |
| Lazy | Triggers Execution |
| No Immediate Processing | Executes DAG |
| Example: map() | Example: count() |

---

# Lazy Evaluation

One of Spark's biggest optimizations.

---

# Example

```python
rdd.filter(...)
   .map(...)
   .distinct()
```

---

# Spark Does Not Execute Yet.

---

# Why?

Spark builds:

```text
Execution Plan
```

---

# Action

```python
count()
```

---

# Then Execution Starts.

---

# Lineage

RDDs maintain lineage information.

---

# What is Lineage?

History of transformations.

---

# Example

```python
rdd1
  ↓
filter()
  ↓
map()
  ↓
distinct()
```

---

# Spark Remembers

Every step.

---

# Why?

For fault recovery.

---

# Fault Tolerance

Suppose:

```text
Partition 3 Lost
```

---

# Spark Uses Lineage

To rebuild:

```text
Partition 3
```

instead of entire dataset.

---

# Persistence

Repeated computations can be expensive.

---

# Example

Same RDD used:

```text
10 Times
```

---

# Spark Would Recompute

Every time.

---

# Solution

Persist RDD.

---

# Cache

```python
rdd.cache()
```

---

# What Happens?

RDD stored in memory.

---

# Benefit

```text
Faster Reuse
```

---

# Persist

```python
rdd.persist()
```

---

# Difference

Persist supports multiple storage levels.

---

# Storage Levels

Examples:

```text
MEMORY_ONLY
MEMORY_AND_DISK
DISK_ONLY
```

---

# RDD Limitations

RDDs are powerful but have drawbacks.

---

# No Schema

RDD stores raw objects.

---

# Example

Spark does not know:

```text
Name
Age
Salary
```

relationships.

---

# Result

Optimization becomes difficult.

---

# More Code

RDD operations often require:

```text
Complex Programming Logic
```

---

# Lower Performance

Compared to:

```text
DataFrames
Datasets
```

---

# Why?

Spark cannot optimize RDD operations as effectively.

---

# Evolution of Spark

```text
RDD
  ↓
DataFrame
  ↓
Dataset
```

---

# RDD vs DataFrame

| Feature | RDD | DataFrame |
|----------|----------|----------|
| Schema | No | Yes |
| Optimization | Limited | Advanced |
| Performance | Lower | Higher |
| Ease of Use | Harder | Easier |
| SQL Support | No | Yes |

---

# When Are RDDs Used Today?

Mostly for:

```text
Low-Level Processing
Custom Logic
Legacy Applications
```

---

# Modern Spark Development

Primarily uses:

```text
DataFrames
Spark SQL
Datasets
```

---

# Real-World Example

Log Processing

Input:

```text
Application Logs
```

---

# RDD Workflow

```python
Read Logs
Filter Errors
Count Failures
Generate Metrics
```

---

# Banking Example

Process:

```text
Transactions
Fraud Indicators
Risk Events
```

using distributed RDD operations.

---

# Common Mistakes

## Using collect() On Large Data

Can crash the Driver.

---

## Ignoring Partitions

Reduces parallelism.

---

## Excessive Caching

Consumes memory.

---

## Using RDD When DataFrame Is Better

Loses performance benefits.

---

# Interview Questions

### What does RDD stand for?

Resilient Distributed Dataset.

---

### Why is RDD called resilient?

Because it can recover using lineage.

---

### What is immutability?

RDDs cannot be modified after creation.

---

### What is the difference between Transformation and Action?

Transformations create new RDDs.

Actions trigger execution.

---

### What is Lazy Evaluation?

Execution is delayed until an action occurs.

---

### What is Lineage?

History of transformations used for recovery.

---

### Why is RDD fault tolerant?

Because Spark can recreate lost partitions.

---

### Why are DataFrames preferred over RDDs?

They provide schema awareness and better optimization.

---

# Summary

RDD is the foundational data abstraction of Apache Spark.

It introduced:

```text
Distributed Processing
Fault Tolerance
Lazy Evaluation
Lineage
Parallel Execution
```

and made large-scale data processing practical.

Although modern Spark development primarily uses DataFrames and Spark SQL, understanding RDDs is essential because many Spark concepts originated from them.

---

# Key Takeaways

✔ RDD = Resilient Distributed Dataset

✔ Distributed across partitions

✔ Immutable and fault tolerant

✔ Uses lineage for recovery

✔ Supports transformations and actions

✔ Lazy evaluation improves efficiency

✔ Cache and persist improve performance

✔ Foundation of Spark architecture

✔ DataFrames evolved from RDD concepts

---

# Next Module

➡ 04-dataframes.md
