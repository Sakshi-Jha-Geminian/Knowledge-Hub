# MERGE and UPSERT in Delta Lake

## Learning Objectives

By the end of this module, you will understand:

- What MERGE Is
- What UPSERT Means
- Why MERGE Matters
- Problems with Traditional Updates
- MERGE Syntax
- Insert Operations
- Update Operations
- Delete Operations
- Slowly Changing Dimensions (SCD)
- Change Data Capture (CDC)
- Real-World Examples
- Performance Considerations
- Best Practices
- Interview Questions

---

# Introduction

One of the most powerful features of Delta Lake is:

```text
MERGE
```

MERGE allows you to:

```text
Insert Data
Update Data
Delete Data
```

using a single operation.

---

# The Problem

Suppose you have:

```text
Customer Table
```

already containing records.

---

# Existing Data

```text
ID   Name

1    John
2    Alice
3    Bob
```

---

# New Data Arrives

```text
ID   Name

2    Alice Smith
4    David
```

---

# Desired Result

```text
Update Alice

Insert David
```

---

# Traditional Approach

Usually requires:

```text
Find Existing Records
Run Update
Run Insert
Handle Logic
```

which becomes complex.

---

# Delta Lake Solution

Use:

```text
MERGE
```

---

# What is MERGE?

MERGE combines:

```text
INSERT
UPDATE
DELETE
```

into one operation.

---

# What is UPSERT?

UPSERT means:

```text
UPDATE Existing Rows

OR

INSERT New Rows
```

---

# Simple Definition

UPSERT:

```text
Update If Exists

Insert If Not
```

---

# Why MERGE Matters

Benefits:

```text
Simpler Logic
Better Performance
Reliable Processing
Enterprise Scale
```

---

# MERGE Architecture

```text
Source Data
      │
      ▼
Compare Records
      │
      ▼
Matched?
   │
 ┌─┴─┐
 │   │
Yes  No
 │   │
 ▼   ▼
Update Insert
```

---

# Basic MERGE Syntax

```sql
MERGE INTO target_table t
USING source_table s
ON t.id = s.id
WHEN MATCHED THEN
UPDATE SET *
WHEN NOT MATCHED THEN
INSERT *;
```

---

# Components

```text
Target Table
Source Table
Matching Condition
Actions
```

---

# Target Table

The table being updated.

Example:

```text
customers
```

---

# Source Table

The incoming data.

Example:

```text
customers_updates
```

---

# Matching Condition

Determines whether records already exist.

Example:

```sql
t.customer_id = s.customer_id
```

---

# Example Data

Target:

```text
1 John
2 Alice
3 Bob
```

---

# Source

```text
2 Alice Smith
4 David
```

---

# MERGE Result

```text
1 John
2 Alice Smith
3 Bob
4 David
```

---

# Update Operation

Executed when:

```text
Match Found
```

---

# Example

Target:

```text
Customer_ID = 2
Name = Alice
```

---

# Source

```text
Customer_ID = 2
Name = Alice Smith
```

---

# Action

```text
UPDATE
```

---

# Insert Operation

Executed when:

```text
No Match Found
```

---

# Example

Source:

```text
Customer_ID = 4
```

does not exist.

---

# Action

```text
INSERT
```

---

# Delete Operation

MERGE can also delete rows.

---

# Example

```sql
WHEN MATCHED
AND s.status = 'inactive'
THEN DELETE
```

---

# Why This Helps

Supports:

```text
Data Cleanup
Record Removal
Lifecycle Management
```

---

# Real-World Example

Customer Management System

Daily updates arrive containing:

```text
New Customers
Updated Customers
Inactive Customers
```

---

# MERGE Handles All Cases

In one operation.

---

# Change Data Capture (CDC)

CDC tracks:

```text
INSERTS
UPDATES
DELETES
```

from source systems.

---

# Why CDC Matters

Instead of processing:

```text
Entire Table
```

process only:

```text
Changed Records
```

---

# CDC Workflow

```text
Source Database
        │
        ▼
CDC Feed
        │
        ▼
Delta MERGE
        │
        ▼
Updated Table
```

---

# Benefits

```text
Faster Processing
Lower Costs
Reduced Data Movement
```

---

# Slowly Changing Dimensions (SCD)

One of the most common MERGE use cases.

---

# What is a Dimension?

Example:

```text
Customer
Product
Store
Employee
```

---

# Slowly Changing Dimension

A dimension whose values change over time.

Example:

```text
Customer Address
```

---

# Example

Customer:

```text
John
New York
```

---

# Later

```text
John
Chicago
```

---

# Need

Update historical information properly.

---

# SCD Type 1

Overwrite old value.

Example:

```text
New York
→
Chicago
```

---

# MERGE Supports SCD Type 1

Directly through updates.

---

# Example MERGE

```sql
MERGE INTO customers t
USING updates s
ON t.id = s.id

WHEN MATCHED THEN
UPDATE SET
t.city = s.city

WHEN NOT MATCHED THEN
INSERT *
```

---

# Why MERGE is Popular

It solves:

```text
Incremental Loads
CDC
Dimension Updates
Pipeline Synchronization
```

---

# Data Engineering Example

Bronze Layer:

```text
Raw Customer Data
```

---

# Silver Layer

Use:

```text
MERGE
```

to create clean, updated records.

---

# Retail Example

Product Catalog

Updates include:

```text
Price Changes
New Products
Discontinued Products
```

---

# MERGE Handles

```text
Updates
Inserts
Deletes
```

efficiently.

---

# Banking Example

Account Data

Daily changes:

```text
Balance Updates
Account Closures
New Accounts
```

processed using MERGE.

---

# Healthcare Example

Patient Records

Updates arrive from multiple systems.

MERGE maintains a unified patient view.

---

# Performance Considerations

MERGE is powerful but can be expensive on very large datasets.

---

# Optimization Strategies

```text
Partition Tables
Optimize Files
Use Z-Ordering
Filter Source Data
```

---

# Why Optimization Matters

Reduces:

```text
Scan Time
Shuffle Operations
Compute Costs
```

---

# Common Mistakes

## Full Table MERGE

Processing all records unnecessarily.

---

## Poor Match Conditions

Can create duplicates.

---

## Missing Primary Keys

Makes matching difficult.

---

## Ignoring Optimization

Causes slow performance.

---

# Best Practices

## Use Incremental Data

Avoid processing entire datasets.

---

## Define Clear Match Keys

Ensure accurate updates.

---

## Optimize Delta Tables

Improve MERGE performance.

---

## Monitor Execution Time

Identify bottlenecks early.

---

## Test CDC Logic

Prevent incorrect updates.

---

# Delta MERGE vs Traditional SQL

| Feature | Traditional SQL | Delta MERGE |
|----------|----------|----------|
| Update | Separate Statement | Integrated |
| Insert | Separate Statement | Integrated |
| Delete | Separate Statement | Integrated |
| CDC Support | Complex | Easy |
| SCD Support | Complex | Easy |
| ETL Simplicity | Lower | Higher |

---

# Interview Questions

### What is MERGE in Delta Lake?

A command that combines insert, update, and delete operations.

---

### What is an UPSERT?

Update existing rows or insert new rows.

---

### Why is MERGE important?

It simplifies incremental data processing.

---

### What are common MERGE use cases?

```text
CDC
SCD
Data Synchronization
Incremental Loads
```

---

### What happens when a match is found?

The update or delete logic executes.

---

### What happens when no match is found?

The insert logic executes.

---

### How is MERGE used in CDC?

Changed records are merged into target tables.

---

### How can MERGE performance be improved?

```text
Partitioning
Optimization
Z-Ordering
Incremental Processing
```

---

# Summary

MERGE is one of Delta Lake's most valuable capabilities.

It enables:

```text
UPSERT Operations
CDC Processing
SCD Management
Incremental Data Loads
```

through a single, scalable operation.

Modern enterprise data pipelines rely heavily on MERGE because it simplifies data synchronization while maintaining performance and reliability.

---

# Key Takeaways

✔ MERGE combines INSERT, UPDATE, and DELETE

✔ UPSERT means update-or-insert

✔ MERGE simplifies ETL pipelines

✔ Widely used for CDC processing

✔ Supports Slowly Changing Dimensions

✔ Reduces operational complexity

✔ Essential for incremental data loading

✔ Performance improves with optimization techniques

✔ One of the most important Delta Lake interview topics

---

# Next Module

➡ 07-vacuum.md
