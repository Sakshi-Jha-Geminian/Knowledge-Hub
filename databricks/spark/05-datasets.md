# Datasets in Apache Spark

## Learning Objectives

By the end of this module, you will understand:

- What a Dataset Is
- Why Datasets Were Introduced
- Dataset Characteristics
- Type Safety
- Encoders
- Dataset Operations
- Dataset vs RDD
- Dataset vs DataFrame
- Dataset vs SQL
- Scala and Java Support
- Why PySpark Does Not Have a Typed Dataset API
- Performance Considerations
- When to Use Datasets
- Real-World Examples
- Interview Questions

---

# Introduction

Spark provides several abstractions for distributed data processing:

```text
RDD
DataFrame
Dataset
```

We have already learned that:

```text
RDD
```

is a low-level distributed collection, while:

```text
DataFrame
```

provides structured, schema-based processing.

A Dataset combines some advantages of:

```text
RDD
+
DataFrame
```

---

# What is a Dataset?

A Dataset is a distributed collection of strongly typed objects.

It combines:

```text
Type Safety
+
Structured Processing
+
Spark Optimization
```

---

# Simple Definition

Think of a Dataset as:

```text
A Distributed Collection
With Strong Type Information
```

---

# Important Language Detail

Datasets are primarily available through:

```text
Scala
Java
```

---

# What About PySpark?

PySpark provides:

```text
DataFrame API
```

but does not provide the same strongly typed Dataset API available in Scala and Java.

This distinction is important.

---

# Why Were Datasets Introduced?

RDDs provide strong control but little structure.

DataFrames provide structure and optimization but lose some compile-time type safety.

Datasets attempt to combine both.

---

# Comparison

```text
RDD
│
├── Flexible
├── Low-Level
└── Less Optimization

DataFrame
│
├── Structured
├── Highly Optimized
└── Less Compile-Time Type Safety

Dataset
│
├── Structured
├── Optimized
└── Strongly Typed
```

---

# Type Safety

One of the biggest advantages of Datasets is:

```text
Compile-Time Type Safety
```

---

# What Does Type Safety Mean?

Suppose we have:

```text
Customer
```

with:

```text
id: Int
name: String
age: Int
```

A strongly typed Dataset knows these types.

---

# Example Concept

```scala
case class Customer(
    id: Int,
    name: String,
    age: Int
)
```

---

# Creating Dataset

Conceptually:

```scala
val customers =
  Seq(
    Customer(1, "John", 25),
    Customer(2, "Alice", 30)
  ).toDS()
```

---

# Result

We now have:

```text
Dataset[Customer]
```

---

# What Does Dataset[Customer] Mean?

It means:

```text
Every Record
Is Expected To Be
A Customer Object
```

---

# Why Is This Useful?

The compiler can detect certain type-related errors before execution.

---

# Example

Suppose:

```scala
customer.age
```

is an integer.

Trying to use it as an incompatible type can be detected during compilation.

---

# DataFrame Equivalent

A DataFrame is essentially:

```text
Dataset[Row]
```

in Spark's JVM APIs.

---

# Important Concept

A DataFrame does not expose your custom business object type in the same way a typed Dataset does.

---

# Encoders

Datasets use:

```text
Encoders
```

to convert between JVM objects and Spark's internal representation.

---

# What is an Encoder?

An Encoder describes how an object can be:

```text
Serialized
Deserialized
```

for distributed processing.

---

# Simple Flow

```text
JVM Object
    │
    ▼
Encoder
    │
    ▼
Spark Internal Representation
```

and back:

```text
Spark Internal Representation
    │
    ▼
Encoder
    │
    ▼
JVM Object
```

---

# Why Are Encoders Necessary?

Spark distributes data across:

```text
Executors
```

Therefore, objects need to be efficiently represented and transferred.

---

# Dataset Operations

Datasets support many operations similar to DataFrames.

Examples:

```text
map()
filter()
select()
groupBy()
join()
```

---

# Example

```scala
val adults =
  customers.filter(_.age >= 18)
```

---

# Result

A new:

```text
Dataset[Customer]
```

is produced.

---

# Mapping

Because Dataset is typed, you can operate directly on objects.

Example:

```scala
val names =
  customers.map(_.name)
```

---

# Result

Conceptually:

```text
Dataset[String]
```

---

# Filtering

```scala
val adults =
  customers.filter(_.age >= 18)
```

---

# Result

```text
Dataset[Customer]
```

---

# Transformations

Dataset transformations are lazy.

Examples:

```text
map()
filter()
flatMap()
```

do not immediately execute the distributed computation.

---

# Action

Execution begins when an action is called.

Examples:

```text
count()
collect()
first()
```

---

# Example

```scala
customers
  .filter(_.age > 25)
  .count()
```

---

# Execution

Spark builds an execution plan and then executes the required computation.

---

# Dataset and Spark SQL

Datasets can participate in Spark's structured processing engine.

This means Spark can still benefit from:

```text
Catalyst Optimizer
```

and efficient execution mechanisms.

---

# Dataset vs RDD

| Feature | RDD | Dataset |
|---|---|---|
| Distributed | Yes | Yes |
| Immutable | Yes | Yes |
| Fault Tolerant | Yes | Yes |
| Schema | No | Yes |
| Type Safety | Strong object-level typing | Strong typed API |
| Catalyst Optimization | Limited | Yes |
| Encoders | No | Yes |
| High-Level API | No | Yes |

---

# Dataset vs DataFrame

| Feature | Dataset | DataFrame |
|---|---|---|
| Distributed | Yes | Yes |
| Schema | Yes | Yes |
| Type Safety | Strong | Less compile-time type safety |
| Encoders | Yes | Yes internally |
| Custom Objects | Yes | Row-based |
| Catalyst | Yes | Yes |
| Scala | Yes | Yes |
| Java | Yes | Yes |
| Python | No typed Dataset API | Yes |

---

# Dataset vs DataFrame Example

Suppose we have:

```text
Customer
```

object.

---

# Dataset

```text
Dataset[Customer]
```

Spark knows:

```text
Customer
```

is the object type.

---

# DataFrame

```text
DataFrame
```

contains:

```text
Rows
Columns
```

and is generally represented through:

```text
Row
```

---

# Why DataFrames Are More Common

Although Datasets provide strong typing, DataFrames are often preferred for:

```text
Data Engineering
Analytics
SQL
PySpark
Databricks
```

---

# Reasons

DataFrames provide:

```text
Simple API
Strong Optimization
Cross-Language Support
SQL Integration
```

---

# Dataset in Scala

Scala is especially well suited to typed Dataset programming.

Example:

```scala
case class Employee(
    id: Int,
    name: String,
    salary: Double
)
```

---

# Dataset

```scala
val employees =
  Seq(
    Employee(1, "John", 50000),
    Employee(2, "Alice", 60000)
  ).toDS()
```

---

# Filtering

```scala
val highPaid =
  employees.filter(_.salary > 55000)
```

---

# Result

```text
Employee(2, "Alice", 60000)
```

---

# Java Dataset

Java also supports typed Datasets.

Conceptually:

```java
Dataset<Employee>
```

---

# Why This Matters

Enterprise Spark applications may use:

```text
Scala
Java
```

where typed Dataset APIs can be valuable.

---

# PySpark Equivalent

In PySpark, the normal approach is:

```python
DataFrame
```

rather than a typed Dataset.

---

# Example

```python
df.filter(df.salary > 55000)
```

---

# Important Interview Point

Do not say:

> PySpark supports Datasets exactly like Scala.

That is incorrect.

Instead:

```text
PySpark provides DataFrames,
while the typed Dataset API is primarily
available in Scala and Java.
```

---

# Performance

Datasets can benefit from Spark's optimized execution engine.

They combine:

```text
Type Safety
+
Structured Processing
+
Optimization
```

---

# However

Using Datasets does not automatically make every workload faster.

Performance depends on:

```text
Data Size
Transformations
Partitioning
Joins
Shuffles
Serialization
Cluster Resources
```

---

# When Should You Use Datasets?

Datasets are useful when:

```text
Strong Type Safety Is Important
```

and you are working with:

```text
Scala
Java
```

---

# Good Use Cases

```text
Complex Business Objects
Typed Transformations
Large JVM-Based Applications
Enterprise Scala Applications
```

---

# When Should You Use DataFrames?

Prefer DataFrames when:

```text
Working in PySpark
Using SQL
Performing Analytics
Building ETL Pipelines
```

---

# When Should You Use RDDs?

Use RDDs when:

```text
Low-Level Control Is Required
Custom Processing Cannot Be Expressed Easily
Legacy Applications Require Them
```

---

# Evolution of Spark APIs

Spark's APIs have evolved toward higher-level abstractions.

```text
RDD
 │
 ▼
DataFrame
 │
 ▼
Dataset
```

---

# Why Higher-Level APIs?

Higher-level APIs allow Spark to understand more about the computation.

---

# Result

Spark can perform:

```text
Query Optimization
Predicate Pushdown
Efficient Execution
Memory Optimization
```

---

# Real-World Example

## Banking

Suppose a bank has:

```text
Transaction
```

objects.

---

# Typed Dataset

```text
Dataset[Transaction]
```

can provide strong typing in a Scala/Java application.

---

# Processing

```text
Filter Transactions
Calculate Risk
Aggregate Amounts
```

---

# Benefit

Business logic can operate directly on typed objects.

---

# E-Commerce Example

Objects:

```text
Customer
Product
Order
```

could be represented as typed Dataset records.

---

# Processing

```text
Customer
   │
   ▼
Orders
   │
   ▼
Products
```

---

# Result

Strongly typed application logic combined with Spark's distributed execution.

---

# Common Mistakes

## Saying DataFrame and Dataset Are Exactly the Same

They are related, but not identical.

---

## Saying PySpark Has Typed Datasets

PySpark does not expose the same typed Dataset API as Scala/Java.

---

## Assuming Dataset Is Always Faster

Performance depends on workload and execution plan.

---

## Ignoring Encoders

Encoders are important to Dataset internals.

---

## Using Dataset When DataFrame Is Simpler

Choose the abstraction appropriate for the workload.

---

# Interview Questions

### What is a Dataset?

A distributed collection of strongly typed objects.

---

### Which languages support the typed Dataset API?

Primarily:

```text
Scala
Java
```

---

### Does PySpark have a typed Dataset API?

No. PySpark primarily uses DataFrames for structured processing.

---

### What is an Encoder?

A mechanism used to convert JVM objects to and from Spark's internal representation.

---

### What is the relationship between DataFrame and Dataset?

A DataFrame is essentially:

```text
Dataset[Row]
```

in Spark's JVM APIs.

---

### Why use Datasets?

For:

```text
Type Safety
Structured Processing
Object-Oriented APIs
```

---

### Dataset vs RDD?

RDD is low-level and untyped from Spark's schema perspective, while Dataset provides structured, strongly typed processing.

---

### Dataset vs DataFrame?

Dataset provides stronger compile-time typing; DataFrame provides a schema-based Row API and is more commonly used across PySpark, SQL, and analytics workloads.

---

### Why are DataFrames more popular in PySpark?

Because PySpark provides the DataFrame API rather than the typed Scala/Java Dataset API.

---

# Summary

Datasets provide a higher-level abstraction that combines:

```text
Distributed Processing
Type Safety
Structured Data
Spark Optimization
```

They are particularly useful in:

```text
Scala
Java
```

applications.

In modern Databricks and PySpark development, however, DataFrames and Spark SQL are generally the primary APIs.

---

# Key Takeaways

✔ Dataset = distributed collection of strongly typed objects

✔ Primarily available in Scala and Java

✔ Uses Encoders

✔ Supports compile-time type safety

✔ Supports Spark's structured execution engine

✔ DataFrame is essentially Dataset[Row] in JVM APIs

✔ PySpark uses DataFrames rather than typed Datasets

✔ Datasets are useful for typed JVM applications

✔ DataFrames are generally preferred for PySpark and analytics

✔ RDDs remain useful for low-level processing

---

# Spark Abstraction Comparison

```text
                    Spark Data APIs
                          │
          ┌───────────────┼───────────────┐
          │               │               │
         RDD          DataFrame        Dataset
          │               │               │
     Low Level       Structured       Strongly Typed
          │               │               │
      Flexible        Optimized       Optimized
          │               │               │
       Python         Python/SQL       Scala/Java
```

---

# Next Module

➡ `06-transformations.md`
