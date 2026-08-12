# Schema Enforcement in Delta Lake

## Learning Objectives

By the end of this module, you will understand:

- What a Schema Is
- What Schema Enforcement Means
- Why Schema Enforcement Matters
- Problems Without Schema Enforcement
- How Delta Lake Enforces Schemas
- Schema Validation Process
- Data Type Validation
- Column Validation
- Write Failures
- Schema Enforcement vs Schema Evolution
- Real-World Examples
- Best Practices
- Interview Questions

---

# Introduction

One of the biggest causes of data quality issues is:

```text
Bad Data
```

entering production systems.

Examples:

```text
Wrong Data Types
Missing Columns
Unexpected Columns
Corrupted Records
```

---

# Why Data Quality Matters

Business decisions depend on:

```text
Reliable Data
```

If data quality is poor:

```text
Reports Become Wrong
Dashboards Become Unreliable
ML Models Fail
```

---

# The Solution

Delta Lake provides:

```text
Schema Enforcement
```

to prevent invalid data from being written.

---

# What is a Schema?

A schema defines:

```text
Table Structure
```

---

# Example Schema

Customer Table:

```text
Customer_ID    INTEGER
Name           STRING
Email          STRING
Age            INTEGER
```

---

# What Does a Schema Define?

A schema specifies:

```text
Column Names
Data Types
Structure
```

---

# What is Schema Enforcement?

Schema Enforcement means:

```text
Only Valid Data
Can Be Written
```

to a table.

---

# Simple Definition

Think of Schema Enforcement as:

```text
A Security Guard
For Your Data
```

---

# Why Schema Enforcement Exists

Without validation:

```text
Bad Data
Enters Production
```

and causes problems later.

---

# Example

Expected:

```text
Age = INTEGER
```

---

# Incoming Data

```text
Age = "Twenty Five"
```

---

# Problem

Analytics systems expect:

```text
Numeric Values
```

but receive:

```text
Strings
```

---

# Result

Queries fail.

Reports become inaccurate.

---

# Traditional Data Lakes

Many traditional data lakes allow:

```text
Any Data
```

to be written.

---

# Consequences

```text
Data Corruption
Unexpected Failures
Complex Debugging
```

---

# Delta Lake Approach

Delta Lake validates data before writing.

---

# Validation Flow

```text
Incoming Data
       │
       ▼
Schema Check
       │
       ▼
Valid?
   │
 ┌─┴─┐
 │   │
Yes  No
 │   │
 ▼   ▼
Write Error
```

---

# How Delta Lake Enforces Schemas

Before writing data:

```text
Delta Compares

Incoming Schema

vs

Table Schema
```

---

# If Schemas Match

```text
Write Success
```

---

# If Schemas Do Not Match

```text
Write Fails
```

---

# Example

Existing Table:

```text
Customer_ID INTEGER
Name STRING
Age INTEGER
```

---

# Incoming Data

```text
Customer_ID INTEGER
Name STRING
Age STRING
```

---

# Result

```text
Schema Validation Error
```

---

# Why This is Good

The problem is detected:

```text
Before Corrupting Data
```

---

# Data Type Validation

Delta Lake checks:

```text
INTEGER
STRING
DATE
TIMESTAMP
BOOLEAN
DOUBLE
```

and other data types.

---

# Example

Expected:

```text
Salary DOUBLE
```

---

# Incoming

```text
Salary = "High"
```

---

# Result

```text
Write Rejected
```

---

# Column Validation

Delta Lake validates:

```text
Column Names
Column Types
Column Structure
```

---

# Example

Expected:

```text
ID
Name
Email
```

---

# Incoming

```text
ID
Name
Email
Phone
```

---

# Result

```text
Schema Mismatch
```

unless schema evolution is enabled.

---

# Schema Enforcement Example

Create Table:

```sql
CREATE TABLE customers
(
    customer_id INT,
    name STRING,
    age INT
)
USING DELTA;
```

---

# Valid Insert

```sql
INSERT INTO customers
VALUES (1,'John',25);
```

---

# Result

```text
Success
```

---

# Invalid Insert

```sql
INSERT INTO customers
VALUES (2,'Alice','Twenty');
```

---

# Result

```text
Schema Validation Error
```

---

# Why This Matters

Prevents:

```text
Bad Reports
Broken Dashboards
Failed Pipelines
```

---

# Data Engineering Perspective

Schema Enforcement acts as:

```text
First Line Of Defense
```

for data quality.

---

# Medallion Architecture Example

Bronze Layer:

```text
Raw Data
```

---

# Silver Layer

Apply:

```text
Validation
Schema Enforcement
Data Cleaning
```

---

# Gold Layer

Receives:

```text
Trusted Data
```

---

# Schema Enforcement vs Schema Evolution

These concepts are related but different.

---

# Schema Enforcement

Goal:

```text
Reject Invalid Data
```

---

# Schema Evolution

Goal:

```text
Allow Safe Schema Changes
```

---

# Example

Current Schema:

```text
ID
Name
```

---

# New Requirement

Add:

```text
Email
```

---

# Schema Enforcement

Would reject the change.

---

# Schema Evolution

Can safely accept the new column.

---

# Real-World Retail Example

Sales table:

```text
Product_ID
Quantity
Price
```

---

# Bad Data

```text
Quantity = "Many"
```

---

# Delta Lake

Rejects invalid records.

---

# Banking Example

Account balances:

```text
DOUBLE
```

---

# Invalid Value

```text
Balance = "Unknown"
```

---

# Result

Rejected before reaching production.

---

# Healthcare Example

Patient Age:

```text
INTEGER
```

---

# Invalid Value

```text
Age = "Adult"
```

---

# Result

Write blocked.

---

# Benefits of Schema Enforcement

```text
Improved Data Quality
Reliable Analytics
Reduced Errors
Faster Debugging
Safer Pipelines
```

---

# Common Mistakes

## Disabling Validation

Allows poor-quality data.

---

## Assuming Raw Data Is Clean

External systems often contain errors.

---

## Ignoring Write Errors

Schema validation failures indicate real issues.

---

## Mixing Data Types

Creates downstream problems.

---

# Best Practices

## Validate Early

Catch problems during ingestion.

---

## Define Schemas Explicitly

Avoid relying solely on inference.

---

## Monitor Validation Failures

Investigate root causes.

---

## Use Delta Tables

Leverage built-in protection.

---

# Interview Questions

### What is Schema Enforcement?

A mechanism that prevents invalid data from being written to a table.

---

### Why is Schema Enforcement important?

It improves data quality and reliability.

---

### What does Delta Lake validate?

```text
Columns
Data Types
Structure
```

---

### What happens when schemas do not match?

The write operation fails.

---

### What problems does Schema Enforcement prevent?

```text
Data Corruption
Broken Reports
Pipeline Failures
```

---

### Is Schema Enforcement the same as Schema Evolution?

No.

Enforcement rejects invalid changes.

Evolution allows controlled changes.

---

### Why is Schema Enforcement valuable in production?

Because it prevents bad data from reaching downstream systems.

---

# Summary

Schema Enforcement is one of Delta Lake's most important data quality features.

It ensures:

```text
Correct Structure
Correct Data Types
Reliable Data
```

before information is written.

By validating data at write time, Delta Lake helps organizations maintain trustworthy analytics and production-grade pipelines.

---

# Key Takeaways

✔ A schema defines table structure

✔ Schema Enforcement validates incoming data

✔ Invalid writes are rejected

✔ Data types are checked automatically

✔ Column structures are validated

✔ Data quality improves significantly

✔ Production pipelines become safer

✔ Schema Enforcement and Schema Evolution are different

✔ Schema Enforcement is critical for enterprise data platforms

---

# Next Module

➡ 05-schema-evolution.md
