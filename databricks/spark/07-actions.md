# Spark Actions

## Learning Objectives

By the end of this module, you will understand:

- What Spark Actions Are
- Actions vs Transformations
- Lazy Evaluation
- How Actions Trigger Execution
- Common RDD Actions
- Common DataFrame Actions
- `collect()`
- `count()`
- `first()`
- `take()`
- `head()`
- `reduce()`
- `foreach()`
- `show()`
- `write()`
- Driver-Side Results
- Jobs Created by Actions
- Performance Considerations
- Common Mistakes
- Real-World Examples
- Interview Questions

---

# Introduction

Spark operations are divided into two major categories:

```text
Transformations
Actions
```

We learned that transformations create new datasets but are evaluated lazily.

Actions are different.

---

# What is an Action?

An action is an operation that:

```text
Triggers Spark Execution
```

and either:

```text
Returns a Result
```

or:

```text
Writes Data
```

to an external system.

---

# Simple Definition

Think of an action as:

```text
"Now Actually Do The Work."
```

---

# Example

```python
df.filter(df.age > 25)
```

This is a:

```text
Transformation
```

No actual computation is triggered merely by defining it.

---

# Add an Action

```python
df.filter(df.age > 25).count()
```

Now:

```text
filter() → Transformation
count()  → Action
```

Spark executes the required computation.

---

# Why Does Spark Use Lazy Evaluation?

Consider:

```python
df.filter(...)
df.select(...)
df.withColumn(...)
```

If Spark executed every line immediately, it could perform unnecessary work.

Instead, Spark builds an execution plan.

---

# Execution

```text
Transformations
       ↓
Execution Plan
       ↓
Action
       ↓
Job
       ↓
Stages
       ↓
Tasks
       ↓
Executors
```

---

# Action Creates a Job

When an action requires execution, Spark creates a:

```text
Job
```

---

# Example

```python
df.count()
```

can trigger a Spark job.

Another action:

```python
df.show()
```

can trigger another job.

---

# Important

A single Spark application can contain:

```text
Multiple Jobs
```

---

# Example

```python
df.count()
df.show()
df.collect()
```

These actions can result in separate execution jobs.

---

# Actions on RDDs

Common RDD actions include:

```text
collect()
count()
first()
take()
reduce()
fold()
aggregate()
foreach()
```

---

# `collect()`

Returns all elements of an RDD or DataFrame to the Driver.

---

# Example

```python
rdd.collect()
```

---

# Input

```text
1
2
3
4
5
```

---

# Result

```text
[1,2,3,4,5]
```

---

# Important Warning

Do not use:

```python
collect()
```

on very large datasets.

---

# Why?

All records are transferred to:

```text
Driver Memory
```

---

# Possible Result

```text
Driver OutOfMemoryError
```

---

# Safe Alternative

Instead of:

```python
df.collect()
```

use:

```python
df.show(10)
```

when you only want to inspect a small number of records.

---

# `count()`

Returns the number of records.

---

# Example

```python
df.count()
```

---

# Result

```text
1000000
```

---

# Important

`count()` is an action and requires Spark to execute the necessary computation.

---

# Example

```python
df.filter(df.age > 30).count()
```

---

# Execution

```text
Read
 ↓
Filter
 ↓
Count
```

---

# `first()`

Returns the first record.

---

# Example

```python
df.first()
```

---

# Result

Conceptually:

```text
Row(id=1, name="John")
```

---

# Use Case

Useful for:

```text
Quick Inspection
Testing
Debugging
```

---

# `take()`

Returns the first N records.

---

# Example

```python
df.take(5)
```

---

# Result

```text
First 5 Rows
```

---

# Why Is `take()` Useful?

Instead of loading the entire dataset:

```python
collect()
```

you can inspect only a small subset.

---

# `head()`

`head()` can return:

```text
One Row
```

or:

```text
Multiple Rows
```

depending on usage.

---

# Example

```python
df.head()
```

---

# `reduce()`

Combines all elements using a function.

---

# Example

```python
rdd.reduce(lambda x, y: x + y)
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

# Result

```text
10
```

---

# Important

The reduction must use an operation appropriate for distributed execution.

For example:

```text
Addition
Maximum
Minimum
```

are commonly suitable.

---

# `fold()`

`fold()` is similar to `reduce()` but includes an initial value.

---

# Example

```python
rdd.fold(
    0,
    lambda x, y: x + y
)
```

---

# Result

```text
Total
```

---

# `aggregate()`

Provides more flexibility than `reduce()` and `fold()`.

It can use:

```text
Separate Functions
```

for:

```text
Partition-Level Processing
Final Combination
```

---

# Conceptual Flow

```text
Partition 1 → Partial Result
Partition 2 → Partial Result
Partition 3 → Partial Result
        │
        ▼
   Final Result
```

---

# `foreach()`

Applies a function to every element.

---

# Example

```python
rdd.foreach(
    lambda x: print(x)
)
```

---

# Important

`foreach()` executes on the executors.

Therefore:

```python
print()
```

output may appear in executor logs rather than the notebook/Driver output.

---

# `foreachPartition()`

Processes an entire partition at once.

---

# Example

```python
rdd.foreachPartition(process_partition)
```

---

# Why Is This Useful?

Useful when working with external systems where establishing one connection per record would be expensive.

For example:

```text
Database
API
External Service
```

---

# Warning

External side effects must be designed carefully because Spark tasks can be retried.

This means an operation may potentially execute more than once.

---

# DataFrame Actions

Common DataFrame actions include:

```text
show()
collect()
count()
first()
head()
take()
```

and writing data using:

```text
write
```

---

# `show()`

Displays rows in a human-readable table.

---

# Example

```python
df.show()
```

---

# Example Output

```text
+---+-----+---+
|id |name |age|
+---+-----+---+
|1  |John |25 |
|2  |Alice|30 |
+---+-----+---+
```

---

# `show(n)`

Display only N rows.

```python
df.show(10)
```

---

# `show(truncate=False)`

Prevents long strings from being truncated.

```python
df.show(
    truncate=False
)
```

---

# `count()`

Returns number of rows.

```python
df.count()
```

---

# `first()`

Returns first row.

```python
df.first()
```

---

# `take()`

Returns a specified number of rows.

```python
df.take(5)
```

---

# `collect()`

Returns all rows to the Driver.

```python
df.collect()
```

---

# DataFrame `write`

Writing data is also a terminal operation that causes Spark to execute the transformations needed to produce the output.

---

# Example

```python
df.write \
  .format("delta") \
  .mode("append") \
  .save("/data/customers")
```

---

# Execution

```text
Read
 ↓
Transform
 ↓
Transform
 ↓
Write
```

---

# Common Write Modes

```text
append
overwrite
error
ignore
```

---

# Append

Adds new data.

```python
.mode("append")
```

---

# Overwrite

Replaces existing data.

```python
.mode("overwrite")
```

---

# Important

Use overwrite carefully in production.

An incorrect overwrite operation can remove existing data.

---

# Actions and Spark Jobs

Consider:

```python
df.filter(df.age > 30).count()
```

---

# Execution Flow

```text
DataFrame
    ↓
filter()
    ↓
count()
    ↓
Job
    ↓
Stages
    ↓
Tasks
    ↓
Executors
```

---

# Another Action

```python
df.show()
```

can trigger another job.

---

# Multiple Actions

Consider:

```python
df.count()

df.show()

df.collect()
```

---

# Potential Result

```text
Job 1 → count()

Job 2 → show()

Job 3 → collect()
```

---

# Performance Problem

If the same expensive DataFrame is used repeatedly:

```text
Recomputation
```

may occur.

---

# Example

```python
filtered = df.filter(df.amount > 1000)

filtered.count()
filtered.show()
filtered.write(...)
```

---

# Possible Issue

Spark may recompute the upstream processing for each action.

---

# Solution

Cache when appropriate.

```python
filtered.cache()
```

---

# Then

```python
filtered.count()
filtered.show()
filtered.write(...)
```

can reuse cached data where applicable.

---

# Important

Caching is not automatically beneficial.

Only cache when:

```text
Computation Is Expensive
AND
Dataset Is Reused
```

---

# Action vs Transformation

| Transformation | Action |
|---|---|
| Creates a new dataset | Produces result or writes output |
| Lazy | Triggers execution |
| Builds execution plan | Starts job execution |
| Examples: `map`, `filter` | Examples: `count`, `show` |

---

# Example

```python
result = (
    df
    .filter(df.salary > 50000)
    .select("name", "salary")
)
```

No action has been called yet.

---

# Add Action

```python
result.show()
```

Now Spark executes.

---

# Action and DAG

When an action is triggered:

```text
Transformations
       ↓
DAG
       ↓
Stages
       ↓
Tasks
       ↓
Executors
```

---

# Example

```text
Read
 ↓
Filter
 ↓
Map
 ↓
GroupBy
 ↓
Action
```

---

# Spark

Creates an execution plan.

A shuffle caused by `groupBy` may separate stages.

---

# Driver Role

The Driver:

```text
Receives Application
Builds Execution Plan
Schedules Tasks
Coordinates Execution
```

---

# Executor Role

Executors:

```text
Run Tasks
Process Data
Return Results
```

---

# Driver-Side Result

Actions such as:

```text
collect()
first()
take()
```

return data to the Driver.

---

# Important Distinction

Not every action returns a large result to the Driver.

For example:

```text
count()
```

returns a small numeric result.

---

# `show()`

Displays a limited representation rather than returning the entire dataset to the Driver as a Python collection.

---

# Real-World Example

## E-Commerce

Suppose:

```text
1 Billion Orders
```

---

# Need

Find:

```text
Number Of Orders
```

---

# Use

```python
orders.count()
```

---

# Good

The result is only:

```text
One Number
```

---

# Bad

```python
orders.collect()
```

This attempts to bring all records to the Driver.

---

# Banking Example

Find:

```text
Total Transaction Amount
```

A distributed aggregation can calculate the result, while only the final aggregate needs to be returned.

---

# Data Pipeline Example

```python
result = (
    orders
    .filter(orders.status == "COMPLETED")
    .groupBy("customer_id")
    .sum("amount")
)

result.write \
    .format("delta") \
    .mode("append") \
    .save("/data/customer_sales")
```

---

# What Happens?

```text
Read
 ↓
Filter
 ↓
GroupBy
 ↓
Shuffle
 ↓
Aggregation
 ↓
Write
```

The write operation causes the pipeline to execute.

---

# Common Mistakes

## Using `collect()` on Large Data

Can cause Driver memory exhaustion.

---

## Calling Many Actions Unnecessarily

Can cause repeated computation.

---

## Printing Inside `foreach()`

Output occurs on executors and may not appear where expected.

---

## Assuming `show()` Means All Data Was Collected

`show()` displays a limited number of rows.

---

## Ignoring Caching

Repeated expensive computations may be unnecessarily repeated.

---

## Caching Everything

Consumes cluster resources.

---

# Performance Best Practices

## Use `show()` for Inspection

Instead of:

```python
collect()
```

---

## Use `take()` for Small Samples

```python
df.take(10)
```

---

## Cache Reused Data

Only when justified.

---

## Avoid Unnecessary Actions

Do not repeatedly call:

```text
count()
show()
collect()
```

just for debugging in production pipelines.

---

## Write Directly to Storage

Instead of collecting data to the Driver and manually writing it.

Prefer:

```python
df.write.format("delta").save(path)
```

---

# Interview Questions

### What is an action in Spark?

An operation that triggers execution and returns a result or writes output.

---

### Give examples of Spark actions.

```text
count()
collect()
first()
take()
reduce()
show()
```

and write operations.

---

### What happens when an action is called?

Spark builds or uses the execution plan, creates a job, divides it into stages and tasks, and executes those tasks on executors.

---

### Does every action create a Spark job?

An action that requires execution generally triggers a job. Internal execution details can vary, but conceptually actions are job-triggering operations.

---

### Why is `collect()` dangerous?

Because it can transfer a very large amount of data to the Driver and exhaust its memory.

---

### What is the difference between `count()` and `collect()`?

`count()` returns the number of records, while `collect()` returns all records to the Driver.

---

### What does `show()` do?

Displays a limited number of rows for inspection.

---

### What is `foreach()`?

An action that applies a function to each element, typically executing the function on Spark executors.

---

### Why can `foreach()` be dangerous with external systems?

Spark may retry tasks, potentially causing side effects more than once if the operation is not designed to be idempotent.

---

### How can repeated actions be optimized?

Cache or persist a reused intermediate dataset when the cost of recomputation justifies the resource usage.

---

# Summary

Actions are the operations that make Spark actually execute a computation.

The fundamental flow is:

```text
Transformations
       ↓
Lazy Execution
       ↓
Action
       ↓
Job
       ↓
Stages
       ↓
Tasks
       ↓
Executors
```

Actions allow Spark applications to:

```text
Return Results
Display Data
Calculate Aggregates
Write Data
```

Understanding actions is essential for understanding Spark execution and performance.

---

# Key Takeaways

✔ Transformations are lazy

✔ Actions trigger execution

✔ Actions can create Spark jobs

✔ `count()` returns record count

✔ `collect()` returns all records to Driver

✔ `show()` is useful for inspection

✔ `take()` retrieves a limited number of records

✔ `reduce()` performs distributed reduction

✔ `foreach()` executes logic across records

✔ Writes trigger execution

✔ Repeated actions can cause recomputation

✔ Cache reused data when appropriate

✔ Never blindly use `collect()` on large datasets

---

# Transformation + Action Example

```python
result = (
    df
    .filter(df.amount > 1000)
    .select("customer_id", "amount")
)

# Nothing substantial has been executed yet.

result.show()

# Action triggers execution.
```

---

# Complete Execution Model

```text
             Spark Application
                    │
                    ▼
             Transformations
                    │
                    ▼
              Lazy Evaluation
                    │
                    ▼
                 Action
                    │
                    ▼
                  Job
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
             Result / Output
```

---

# Next Module

➡ `08-partitioning.md`
