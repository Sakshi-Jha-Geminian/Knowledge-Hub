# DataFrames in Apache Spark

## Learning Objectives

By the end of this module, you will understand:

- What a DataFrame Is
- Why DataFrames Were Introduced
- DataFrame Structure
- Rows and Columns
- Schema
- Creating DataFrames
- Reading Data
- Selecting Columns
- Filtering Rows
- Adding and Renaming Columns
- Sorting Data
- Grouping and Aggregation
- Joins
- Handling Null Values
- DataFrame Transformations
- DataFrame Actions
- Lazy Evaluation
- Catalyst Optimizer
- Tungsten
- DataFrame vs RDD
- DataFrame vs SQL
- Performance Considerations
- Real-World Examples
- Interview Questions

---

# Introduction

RDDs provide low-level distributed data processing.

However, working directly with RDDs can require a lot of code.

For example:

```text
RDD
 ↓
Custom Logic
 ↓
Manual Processing
 ↓
Result
```

Spark introduced:

```text
DataFrames
```

to provide a higher-level and more optimized way to work with structured data.

---

# What is a DataFrame?

A DataFrame is a distributed collection of data organized into:

```text
Named Columns
```

It is conceptually similar to:

```text
Database Table
```

or:

```text
Spreadsheet
```

but it is distributed across a Spark cluster.

---

# Simple Definition

Think of a DataFrame as:

```text
A Distributed Table
```

that Spark can process across multiple machines.

---

# Example

A customer DataFrame might look like:

```text
+----+-------+---+-------+
| id | name  |age| city  |
+----+-------+---+-------+
| 1  | John  |25 | Delhi |
| 2  | Alice |30 | Mumbai|
| 3  | Bob   |28 | Pune  |
+----+-------+---+-------+
```

---

# DataFrame Structure

A DataFrame consists of:

```text
Rows
Columns
Schema
```

---

# Rows

Each row represents a record.

Example:

```text
1 | John | 25 | Delhi
```

represents one customer.

---

# Columns

Columns represent attributes.

Example:

```text
id
name
age
city
```

---

# Schema

Schema describes:

```text
Column Names
Data Types
Nullable Information
```

---

# Example Schema

```text
root
 |-- id: integer
 |-- name: string
 |-- age: integer
 |-- city: string
```

---

# Why Schema Matters

Spark knows:

```text
What Type Of Data
Each Column Contains
```

This allows Spark to optimize processing.

---

# Creating a DataFrame

A DataFrame can be created from:

```text
Python Collections
RDDs
Files
Tables
SQL Queries
External Databases
```

---

# Creating from Python Data

Example:

```python
data = [
    (1, "John", 25),
    (2, "Alice", 30),
    (3, "Bob", 28)
]

columns = ["id", "name", "age"]

df = spark.createDataFrame(data, columns)
```

---

# Displaying a DataFrame

Use:

```python
df.show()
```

---

# Example Output

```text
+---+-----+---+
| id| name|age|
+---+-----+---+
|  1| John| 25|
|  2|Alice| 30|
|  3|  Bob| 28|
+---+-----+---+
```

---

# Viewing Schema

Use:

```python
df.printSchema()
```

---

# Example

```text
root
 |-- id: long
 |-- name: string
 |-- age: long
```

---

# Reading Data

Spark can read many formats.

Examples:

```text
CSV
JSON
Parquet
ORC
Delta
JDBC
```

---

# Reading CSV

```python
df = spark.read.csv(
    "/data/customers.csv",
    header=True,
    inferSchema=True
)
```

---

# Reading JSON

```python
df = spark.read.json("/data/customers.json")
```

---

# Reading Parquet

```python
df = spark.read.parquet("/data/customers.parquet")
```

---

# Reading Delta

```python
df = spark.read.format("delta").load(
    "/data/customers"
)
```

---

# DataFrame API

Spark provides many operations for working with DataFrames.

Common operations include:

```text
select()
filter()
where()
withColumn()
drop()
groupBy()
agg()
join()
orderBy()
```

---

# Selecting Columns

Use:

```python
df.select("name", "age")
```

---

# Result

```text
name
age
```

---

# Selecting Multiple Columns

```python
df.select(
    "id",
    "name",
    "city"
)
```

---

# Selecting Expressions

```python
df.select(
    "name",
    df.age + 1
)
```

---

# Filtering Data

Use:

```python
df.filter(df.age > 25)
```

---

# Example

Input:

```text
John   25
Alice  30
Bob    28
```

---

# Filter

```python
df.filter(df.age > 25)
```

---

# Result

```text
Alice  30
Bob    28
```

---

# where()

`where()` is another way to filter.

```python
df.where(df.age > 25)
```

---

# filter() vs where()

Both can perform filtering.

```python
df.filter(...)
```

and:

```python
df.where(...)
```

are commonly interchangeable.

---

# Multiple Conditions

```python
df.filter(
    (df.age > 25) &
    (df.city == "Delhi")
)
```

---

# Important

Use:

```python
&
```

for AND.

Use:

```python
|
```

for OR.

Do not use Python's:

```python
and
or
```

inside Spark column expressions.

---

# Adding Columns

Use:

```python
withColumn()
```

---

# Example

```python
df = df.withColumn(
    "age_next_year",
    df.age + 1
)
```

---

# Result

```text
age | age_next_year
25  | 26
30  | 31
```

---

# Renaming Columns

Use:

```python
df.withColumnRenamed(
    "name",
    "customer_name"
)
```

---

# Dropping Columns

Use:

```python
df.drop("age")
```

---

# Sorting Data

Use:

```python
df.orderBy("age")
```

---

# Descending Order

```python
from pyspark.sql.functions import desc

df.orderBy(desc("age"))
```

---

# Grouping Data

Use:

```python
groupBy()
```

---

# Example

Suppose:

```text
city     salary

Delhi    50000
Delhi    60000
Mumbai   70000
Mumbai   80000
```

---

# Group By City

```python
df.groupBy("city").avg("salary")
```

---

# Result

```text
Delhi    55000
Mumbai   75000
```

---

# Aggregation Functions

Common functions include:

```text
count()
sum()
avg()
min()
max()
```

---

# Example

```python
df.groupBy("city").agg(
    avg("salary"),
    max("salary"),
    min("salary")
)
```

---

# Joins

DataFrames support:

```text
Inner Join
Left Join
Right Join
Full Join
Cross Join
```

---

# Example

Customers:

```text
customer_id
name
```

Orders:

```text
customer_id
order_amount
```

---

# Join

```python
customers.join(
    orders,
    customers.customer_id ==
    orders.customer_id,
    "inner"
)
```

---

# Why Joins Matter

Most enterprise data pipelines combine information from multiple datasets.

---

# Null Handling

Data often contains missing values.

Example:

```text
name
John
NULL
Alice
```

---

# Detect Nulls

```python
df.filter(
    df.name.isNull()
)
```

---

# Remove Null Rows

```python
df.dropna()
```

---

# Fill Null Values

```python
df.fillna("Unknown")
```

---

# Important

Null handling should be based on business rules.

Do not blindly replace every null.

---

# DataFrame Transformations

DataFrame operations such as:

```text
select()
filter()
withColumn()
groupBy()
join()
```

are transformations.

---

# Are They Immediately Executed?

No.

Spark uses:

```text
Lazy Evaluation
```

---

# Example

```python
df.filter(df.age > 25)
```

does not immediately process the entire dataset.

---

# Spark Builds an Execution Plan

```text
Read
 ↓
Filter
 ↓
Select
 ↓
Aggregate
```

---

# Action

Execution occurs when an action is called.

Examples:

```python
df.show()
df.count()
df.collect()
df.write.save(...)
```

---

# DataFrame Actions

Common actions include:

```text
show()
count()
collect()
first()
take()
write()
```

---

# collect()

Example:

```python
df.collect()
```

returns records to the Driver.

---

# Warning

Never use:

```python
collect()
```

carelessly on a very large DataFrame.

---

# Why?

All returned records must fit in Driver memory.

This can cause:

```text
OutOfMemoryError
```

---

# Catalyst Optimizer

One of Spark SQL's most important components is:

```text
Catalyst Optimizer
```

---

# What Does Catalyst Do?

It optimizes logical and physical query plans.

---

# Example

Suppose you write:

```python
df.filter(df.age > 30) \
  .select("name")
```

---

# Spark Can Optimize

Instead of reading everything first, Spark may push the filter closer to the data source.

This is called:

```text
Predicate Pushdown
```

---

# Logical Plan

Spark first creates a:

```text
Logical Plan
```

representing what the query wants to accomplish.

---

# Optimized Logical Plan

Catalyst applies optimization rules.

---

# Physical Plan

Spark determines:

```text
How The Query Should Actually Execute
```

---

# Simplified Flow

```text
DataFrame / SQL
       │
       ▼
Logical Plan
       │
       ▼
Catalyst Optimizer
       │
       ▼
Optimized Plan
       │
       ▼
Physical Plan
       │
       ▼
Execution
```

---

# Tungsten

Another important Spark optimization project is:

```text
Tungsten
```

Tungsten focuses on improving execution efficiency through better:

```text
Memory Management
CPU Utilization
Binary Data Processing
```

---

# Why Catalyst and Tungsten Matter

Together they help Spark execute structured workloads efficiently.

---

# DataFrame vs RDD

| Feature | RDD | DataFrame |
|---|---|---|
| Structure | Unstructured objects | Rows + Columns |
| Schema | No | Yes |
| Optimization | Limited | Catalyst |
| Performance | Lower in many workloads | Generally better |
| SQL Support | No direct support | Yes |
| Ease of Use | Lower | Higher |
| Type Safety | Object-level | Schema-level |

---

# Why DataFrames Are Preferred

Modern Spark applications generally prefer:

```text
DataFrames
Spark SQL
```

over manually manipulating RDDs.

---

# When Should You Use RDDs?

RDDs may still be appropriate when:

```text
Very Low-Level Control Is Needed
Custom Object Processing Is Required
Legacy Code Uses RDDs
```

---

# DataFrame vs SQL

DataFrame API:

```python
df.filter(df.age > 30)
```

SQL:

```sql
SELECT *
FROM customers
WHERE age > 30;
```

---

# Important

Both can be optimized by Spark's SQL engine.

---

# Same Processing Engine

```text
DataFrame API
       │
       ▼
Spark SQL Engine
       │
       ▼
Catalyst Optimizer
       │
       ▼
Execution
```

---

# Performance Best Practices

## Select Only Required Columns

Instead of:

```python
df.select("*")
```

prefer:

```python
df.select("id", "name")
```

when you need only those columns.

---

# Why?

Reduces:

```text
Data Read
Network Transfer
Memory Usage
```

---

# Filter Early

Apply selective filters as early as possible.

Example:

```python
df.filter(df.country == "India")
```

before expensive operations.

---

# Avoid collect()

Use:

```python
show()
take()
limit()
```

for inspection when possible.

---

# Avoid Unnecessary Shuffles

Operations such as:

```text
groupBy()
join()
distinct()
orderBy()
```

may cause data movement across partitions.

---

# Use Appropriate Joins

If one dataset is small enough, a:

```text
Broadcast Join
```

may be beneficial.

---

# Use Explicit Schemas

Instead of relying on:

```python
inferSchema=True
```

for important production pipelines, define schemas explicitly when practical.

---

# Why?

Provides:

```text
Consistency
Faster Reads
Better Data Quality
```

---

# Cache Carefully

Cache DataFrames only when they are:

```text
Expensive To Recompute
Used Multiple Times
```

---

# Example

```python
df.cache()
```

---

# Do Not Cache Everything

Caching consumes:

```text
Memory
Storage
Resources
```

---

# Real-World Example

## E-Commerce

Orders DataFrame:

```text
order_id
customer_id
product_id
amount
order_date
```

---

# Processing

```python
orders \
    .filter(orders.amount > 1000) \
    .groupBy("product_id") \
    .sum("amount")
```

---

# Spark

Can optimize the operation through:

```text
Catalyst
Partitioning
Predicate Pushdown
Efficient Execution
```

---

# Banking Example

Transaction DataFrame:

```text
account_id
transaction_id
amount
timestamp
```

---

# Query

Find:

```text
High-Value Transactions
```

---

# DataFrame

```python
transactions.filter(
    transactions.amount > 100000
)
```

---

# Healthcare Example

Patient DataFrame:

```text
patient_id
age
hospital
diagnosis
```

---

# Query

Find patients from:

```text
Specific Hospital
```

using DataFrame filtering.

---

# Common Mistakes

## Using collect() on huge datasets

Can crash the Driver.

---

## Selecting all columns unnecessarily

Increases data processing.

---

## Excessive caching

Consumes resources.

---

## Ignoring schema

Can cause data quality issues.

---

## Performing large joins without planning

Can cause expensive shuffles.

---

# Interview Questions

### What is a DataFrame?

A distributed collection of structured data organized into named columns.

---

### Why are DataFrames faster than RDDs?

Spark can optimize DataFrame operations using the SQL engine and Catalyst optimizer.

---

### What is a schema?

A definition of column names, data types, and related metadata.

---

### What is Catalyst?

Spark's query optimization framework.

---

### What is Tungsten?

A Spark execution optimization project focused on efficient CPU and memory usage.

---

### What is lazy evaluation?

Spark delays execution until an action is called.

---

### What is the difference between `filter()` and `where()`?

Both can be used to filter DataFrame rows.

---

### Why is `collect()` dangerous?

It moves all returned records to the Driver and can exhaust Driver memory.

---

### What is predicate pushdown?

Moving filtering closer to the data source so unnecessary data is not read.

---

### What is a DataFrame join?

Combining rows from two DataFrames using related columns or expressions.

---

# Summary

DataFrames are the primary structured data abstraction used in modern Spark.

They provide:

```text
Rows
Columns
Schema
Distributed Processing
SQL Integration
Query Optimization
```

DataFrames allow Spark to understand the structure of data and optimize how operations are executed.

---

# Key Takeaways

✔ DataFrame = distributed structured data

✔ DataFrames contain rows and named columns

✔ Schema describes the structure and types

✔ DataFrames support SQL and programmatic APIs

✔ Transformations are lazily evaluated

✔ Actions trigger execution

✔ Catalyst optimizes query plans

✔ Tungsten improves execution efficiency

✔ DataFrames are generally preferred over RDDs

✔ Avoid unnecessary `collect()`

✔ Filter and select data efficiently

✔ Cache only when reuse justifies it

---

# Spark DataFrame Learning Flow

```text
Data Source
    │
    ▼
DataFrame
    │
    ▼
Transformations
    │
    ▼
Logical Plan
    │
    ▼
Catalyst Optimizer
    │
    ▼
Physical Plan
    │
    ▼
Spark Execution
    │
    ▼
Result
```

---

# Next Module

➡ `05-datasets.md`
