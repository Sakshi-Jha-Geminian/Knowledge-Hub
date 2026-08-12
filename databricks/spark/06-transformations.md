# Spark Transformations

## Learning Objectives

By the end of this module, you will understand:

- What Transformations Are
- Lazy Evaluation
- Narrow Transformations
- Wide Transformations
- `map()`
- `flatMap()`
- `filter()`
- `mapValues()`
- `distinct()`
- `union()`
- `groupByKey()`
- `reduceByKey()`
- `sortByKey()`
- Joins
- Shuffles
- Transformation and Stage Boundaries
- DataFrame Transformations
- Performance Considerations
- Common Mistakes
- Real-World Examples
- Interview Questions

---

# Introduction

Spark transformations are operations that create a new dataset from an existing dataset.

Examples:

```text
map()
filter()
flatMap()
select()
groupBy()
join()
```

---

# Simple Definition

A transformation:

```text
Takes Existing Data
        ↓
Applies Logic
        ↓
Creates New Dataset
```

---

# Important Property

Transformations are:

```text
Lazy
```

This means Spark does not immediately execute them.

---

# Example

```python
df.filter(df.age > 25)
```

Spark does not immediately process all records.

Instead, it records:

```text
Filter age > 25
```

---

# When Does Execution Start?

When an action is called.

Example:

```python
df.filter(df.age > 25).count()
```

Here:

```text
filter() → Transformation
count()  → Action
```

---

# Why Lazy Evaluation?

Lazy evaluation allows Spark to optimize the complete execution plan.

---

# Example

```text
Read Data
   ↓
Filter
   ↓
Select
   ↓
Aggregate
```

Spark can analyze the complete pipeline before executing it.

---

# Two Major Types of Transformations

Spark transformations can broadly be categorized as:

```text
Narrow Transformations
Wide Transformations
```

---

# Narrow Transformations

A narrow transformation is one where each output partition depends on a small number of input partitions, typically one.

---

# Example

```text
Input Partition 1
       ↓
Output Partition 1
```

---

# Examples

```text
map()
filter()
flatMap()
```

---

# Why Are Narrow Transformations Fast?

They generally do not require data to move between partitions.

Therefore:

```text
No Major Shuffle
```

is required.

---

# Example

Suppose:

```text
Partition 1 → [1,2,3]
Partition 2 → [4,5,6]
```

Apply:

```python
map(lambda x: x * 2)
```

---

# Result

```text
Partition 1 → [2,4,6]
Partition 2 → [8,10,12]
```

Data stays within its existing partition structure.

---

# Wide Transformations

A wide transformation is one where an output partition may depend on multiple input partitions.

---

# Example

```text
Partition 1 ─┐
Partition 2 ─┼──→ Output Partition
Partition 3 ─┘
```

---

# Wide transformations generally require:

```text
Shuffle
```

---

# Examples

```text
groupByKey()
reduceByKey()
sortByKey()
join()
distinct()
```

Depending on the operation and implementation.

---

# What is a Shuffle?

A shuffle redistributes data across partitions.

---

# Example

Input:

```text
Partition 1
A,10
B,20

Partition 2
A,30
B,40
```

Suppose we want:

```text
Total By Key
```

Spark needs all `A` records together and all `B` records together.

---

# Shuffle

```text
A,10 ───────┐
A,30 ───────┼──→ Partition A
             │
B,20 ───────┐
B,40 ───────┼──→ Partition B
```

---

# Why Are Shuffles Expensive?

They can involve:

```text
Network I/O
Disk I/O
Serialization
Memory Usage
```

---

# Important Performance Rule

Try to:

```text
Minimize Unnecessary Shuffles
```

---

# `map()`

`map()` applies a function to every record.

---

# Example

```python
rdd.map(lambda x: x * 2)
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
2
4
6
8
```

---

# Characteristics

```text
Narrow Transformation
One Input → One Output
```

---

# DataFrame Example

```python
df.selectExpr(
    "salary * 2 AS salary"
)
```

---

# `filter()`

Filters records based on a condition.

---

# Example

```python
rdd.filter(lambda x: x > 10)
```

---

# Input

```text
5
12
20
8
30
```

---

# Output

```text
12
20
30
```

---

# Characteristics

```text
Narrow Transformation
```

---

# DataFrame Example

```python
df.filter(df.salary > 50000)
```

---

# `flatMap()`

`flatMap()` is similar to `map()` but can produce zero, one, or multiple output records for each input record.

---

# Example

Suppose:

```text
Hello World
Spark Is Fast
```

---

# Code

```python
rdd.flatMap(
    lambda line: line.split(" ")
)
```

---

# Output

```text
Hello
World
Spark
Is
Fast
```

---

# map() vs flatMap()

With:

```text
map()
```

one input produces one output object.

With:

```text
flatMap()
```

one input can produce multiple output elements.

---

# `distinct()`

Removes duplicate records.

---

# Example

```python
rdd.distinct()
```

---

# Input

```text
A
B
A
C
B
```

---

# Output

```text
A
B
C
```

---

# Important

`distinct()` generally requires redistribution of data.

Therefore it can involve:

```text
Shuffle
```

---

# `union()`

Combines two datasets.

---

# Example

```python
rdd1.union(rdd2)
```

---

# Input

```text
RDD 1:
1
2
3

RDD 2:
4
5
6
```

---

# Output

```text
1
2
3
4
5
6
```

---

# Important

`union()` does not automatically remove duplicates.

---

# To Remove Duplicates

Use:

```python
rdd1.union(rdd2).distinct()
```

---

# `groupByKey()`

Groups values according to their key.

---

# Example

Input:

```text
(A,10)
(B,20)
(A,30)
(B,40)
```

---

# Code

```python
rdd.groupByKey()
```

---

# Result

```text
A → [10,30]

B → [20,40]
```

---

# Problem

`groupByKey()` can generate large amounts of shuffled data.

---

# Better Alternative

Often use:

```text
reduceByKey()
```

when aggregation is the actual goal.

---

# `reduceByKey()`

Combines values for each key.

---

# Example

```python
rdd.reduceByKey(
    lambda x, y: x + y
)
```

---

# Input

```text
(A,10)
(B,20)
(A,30)
(B,40)
```

---

# Output

```text
A → 40
B → 60
```

---

# Why Is reduceByKey() Usually Better?

Spark can perform:

```text
Map-Side Aggregation
```

before sending data across the network.

---

# Example

Instead of sending:

```text
A,10
A,30
```

separately, Spark can partially combine them first.

---

# Result

```text
Less Shuffle Data
```

---

# groupByKey() vs reduceByKey()

| Feature | groupByKey() | reduceByKey() |
|---|---|---|
| Groups Values | Yes | Aggregates Values |
| Shuffle | Yes | Yes |
| Map-Side Combine | No | Yes |
| Data Transfer | Higher | Usually Lower |
| Preferred for Aggregation | No | Yes |

---

# `sortByKey()`

Sorts key-value pairs by key.

---

# Example

Input:

```text
(3,"C")
(1,"A")
(2,"B")
```

---

# Code

```python
rdd.sortByKey()
```

---

# Result

```text
(1,"A")
(2,"B")
(3,"C")
```

---

# Important

Sorting distributed data generally requires:

```text
Shuffle
```

---

# Joins

Spark supports joins between key-value datasets.

---

# Example

Customers:

```text
(1,"John")
(2,"Alice")
```

Orders:

```text
(1,500)
(2,700)
```

---

# Join

```python
customers.join(orders)
```

---

# Result

```text
1 → (John,500)
2 → (Alice,700)
```

---

# Join Performance

Joins can be expensive because they may require:

```text
Shuffle
```

---

# Broadcast Join

If one dataset is small enough, a broadcast join can avoid a large shuffle.

This is covered in:

```text
10-broadcast-joins.md
```

---

# Transformation Pipeline

Consider:

```python
rdd.filter(lambda x: x > 10) \
   .map(lambda x: x * 2)
```

---

# Execution

```text
Input
  ↓
filter()
  ↓
map()
  ↓
Action
```

---

# Both Are Narrow

Therefore Spark can often execute them within the same stage.

---

# Adding a Wide Transformation

```python
rdd.filter(...) \
   .map(...) \
   .reduceByKey(...)
```

---

# Execution

```text
filter
  ↓
map
  ↓
SHUFFLE
  ↓
reduceByKey
```

---

# Stage Boundary

The shuffle often creates a stage boundary.

---

# Simplified Execution

```text
Stage 1
────────────
filter
map
   │
   │ Shuffle
   ▼
Stage 2
────────────
reduceByKey
```

---

# Why Stages Matter

Spark divides jobs into stages around shuffle boundaries.

This is important for:

```text
Performance
Debugging
Spark UI
Optimization
```

---

# DataFrame Transformations

Modern Spark applications often use DataFrames.

Common transformations include:

```text
select()
filter()
where()
withColumn()
drop()
groupBy()
join()
orderBy()
distinct()
```

---

# Example

```python
result = (
    df
    .filter(df.amount > 1000)
    .select("customer_id", "amount")
)
```

Both operations are transformations.

---

# Lazy Execution

Nothing substantial is executed until:

```python
result.show()
```

or another action is invoked.

---

# Transformation Chaining

Spark allows transformations to be chained.

Example:

```python
result = (
    df
    .filter(df.age > 18)
    .select("name", "age")
    .orderBy("age")
)
```

---

# Why Chaining Is Useful

It creates a clear:

```text
Data Processing Pipeline
```

and allows Spark's optimizer to analyze the complete operation.

---

# Narrow vs Wide Visualization

```text
             Transformations

                 │
       ┌─────────┴─────────┐
       │                   │
     Narrow               Wide
       │                   │
       ▼                   ▼
   map()              groupByKey()
   filter()            reduceByKey()
   flatMap()           sortByKey()
                       join()
                       distinct()
       │                   │
       ▼                   ▼
No Major Shuffle       Shuffle
       │                   │
       ▼                   ▼
Same Stage          Stage Boundary
```

---

# Performance Optimization

## Prefer reduceByKey() Over groupByKey()

When performing aggregation.

---

## Filter Early

Reduce the amount of data before expensive operations.

---

## Avoid Unnecessary distinct()

Deduplication can trigger expensive shuffles.

---

## Minimize Large Joins

Use:

```text
Broadcast Join
```

when appropriate.

---

## Select Required Columns

Avoid processing unnecessary data.

---

## Understand Partitioning

Good partitioning can reduce shuffle overhead.

---

# Common Mistakes

## Thinking All Transformations Are Cheap

Some transformations trigger expensive shuffles.

---

## Assuming map() Is Always Faster

Performance depends on the workload and data layout.

---

## Using groupByKey() for Aggregation

Often less efficient than `reduceByKey()`.

---

## Ignoring Shuffle

Shuffle is one of the biggest causes of Spark performance problems.

---

## Calling collect() After Large Transformations

Can overload Driver memory.

---

# Real-World Example

## E-Commerce

Suppose we have:

```text
100 Million Orders
```

and want:

```text
Total Sales By Product
```

---

# Pipeline

```text
Read Orders
    ↓
Filter Valid Orders
    ↓
Select Product + Amount
    ↓
Aggregate By Product
```

---

# Spark

Could use:

```python
orders \
    .filter(...) \
    .select("product_id", "amount") \
    .groupBy("product_id") \
    .sum("amount")
```

---

# Important

The aggregation may require:

```text
Shuffle
```

---

# Banking Example

Find:

```text
Total Transactions By Account
```

---

# Processing

```text
Transaction Data
      ↓
Filter Valid Records
      ↓
Group By Account
      ↓
Aggregate Amount
```

---

# Healthcare Example

Find:

```text
Number Of Patients By Hospital
```

---

# Processing

```text
Patient Data
      ↓
Filter Valid Records
      ↓
Group By Hospital
      ↓
Count
```

---

# Interview Questions

### What is a transformation?

An operation that creates a new dataset from an existing dataset.

---

### Are transformations immediately executed?

No. They are lazily evaluated.

---

### What are narrow transformations?

Transformations where each output partition depends on a limited number of input partitions, typically one.

---

### What are wide transformations?

Transformations where output partitions depend on multiple input partitions and data generally needs to be redistributed.

---

### What is a shuffle?

Redistribution of data across partitions.

---

### Why are shuffles expensive?

They can require network transfer, serialization, disk I/O, and additional memory.

---

### Give examples of narrow transformations.

```text
map()
filter()
flatMap()
```

---

### Give examples of wide transformations.

Common examples include:

```text
groupByKey()
reduceByKey()
sortByKey()
join()
distinct()
```

The exact execution depends on the operation and Spark's implementation.

---

### Why is reduceByKey() preferred over groupByKey() for aggregation?

Because `reduceByKey()` can perform map-side aggregation and therefore often transfers less data during the shuffle.

---

### What is the difference between map() and flatMap()?

`map()` produces one output element per input element, while `flatMap()` can produce zero, one, or multiple output elements.

---

### Does union() remove duplicates?

No.

Use:

```python
union().distinct()
```

if deduplication is required.

---

### What creates a Spark stage boundary?

A shuffle dependency generally creates a boundary between stages.

---

### Why should shuffles be minimized?

Because they can significantly increase:

```text
Network I/O
Disk I/O
Memory Usage
Execution Time
```

---

# Summary

Spark transformations are the building blocks of distributed data processing.

The most important concepts are:

```text
Transformations
      ↓
Lazy Evaluation
      ↓
Narrow / Wide Dependencies
      ↓
Shuffle
      ↓
Stages
```

Understanding this flow is essential for Spark performance tuning.

---

# Key Takeaways

✔ Transformations create new datasets

✔ Transformations are lazily evaluated

✔ Narrow transformations generally avoid major shuffles

✔ Wide transformations generally require data redistribution

✔ `map()` transforms each element

✔ `flatMap()` can produce multiple elements

✔ `filter()` removes records that do not satisfy a condition

✔ `reduceByKey()` is usually preferable to `groupByKey()` for aggregation

✔ `distinct()`, joins, and sorting can require shuffles

✔ Shuffles can create stage boundaries

✔ Reducing unnecessary shuffles is critical for performance

✔ Modern Spark code commonly uses DataFrame transformations

---

# Transformation Execution Model

```text
                Spark Application
                       │
                       ▼
               Transformations
                       │
          ┌────────────┴────────────┐
          │                         │
        Narrow                    Wide
          │                         │
          ▼                         ▼
     No Major Shuffle            Shuffle
          │                         │
          ▼                         ▼
      Same Stage              New Stage
          │                         │
          └────────────┬────────────┘
                       ▼
                    Action
                       │
                       ▼
                   Execution
```

---

# Next Module

➡ `07-actions.md`
