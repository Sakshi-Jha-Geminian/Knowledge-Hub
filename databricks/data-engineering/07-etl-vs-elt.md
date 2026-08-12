# ETL vs ELT in Databricks

## Learning Objectives

By the end of this module, you will understand:

- What ETL is
- What ELT is
- Why ETL was created
- Why ELT became popular
- ETL Architecture
- ELT Architecture
- ETL vs ELT Comparison
- Databricks and ELT
- Modern Lakehouse Data Pipelines
- Real-World Examples
- Advantages and Disadvantages
- Best Practices
- Interview Questions

---

# Introduction

Every organization needs to move data from source systems into analytics platforms.

Examples:

```text
Databases
Applications
APIs
Files
Streams
```

However, raw data usually cannot be analyzed directly.

It must first be:

```text
Cleaned
Validated
Transformed
Standardized
```

The process used to move and prepare data is called:

```text
ETL
or
ELT
```

Understanding the difference is critical for every Data Engineer.

---

# What is ETL?

ETL stands for:

```text
Extract
Transform
Load
```

---

# ETL Process

```text
Source Data
      │
      ▼
Extract
      │
      ▼
Transform
      │
      ▼
Load
      │
      ▼
Target System
```

---

# Step 1: Extract

Retrieve data from source systems.

Examples:

```text
MySQL
Oracle
CSV Files
REST APIs
```

---

# Step 2: Transform

Clean and modify data before loading.

Examples:

```text
Remove Duplicates
Fix Data Types
Standardize Formats
Apply Business Rules
```

---

# Step 3: Load

Store transformed data in the destination.

Examples:

```text
Data Warehouse
Analytics Platform
Reporting System
```

---

# ETL Example

Source:

```text
Customer Database
```

Extract:

```text
Read Data
```

Transform:

```text
Remove Invalid Records
Standardize Names
```

Load:

```text
Data Warehouse
```

---

# Why ETL Was Popular

Historically:

```text
Storage Expensive
Compute Expensive
```

Data warehouses had limited processing power.

Therefore:

```text
Transform First
Load Later
```

became the preferred approach.

---

# Traditional ETL Architecture

```text
Source Systems
       │
       ▼
ETL Tool
       │
       ▼
Transformations
       │
       ▼
Data Warehouse
```

---

# Common ETL Tools

Examples:

```text
Informatica
Talend
SSIS
DataStage
Pentaho
```

---

# ETL Advantages

```text
Clean Data Before Loading
Strong Governance
Reduced Warehouse Load
```

---

# ETL Limitations

```text
Complex Pipelines
Slower Development
Less Flexible
High Maintenance
```

---

# What is ELT?

ELT stands for:

```text
Extract
Load
Transform
```

---

# ELT Process

```text
Source Data
      │
      ▼
Extract
      │
      ▼
Load
      │
      ▼
Transform
```

Transformations happen after data is loaded.

---

# Why ELT Emerged

Modern platforms provide:

```text
Massive Compute Power
Scalable Storage
Distributed Processing
```

making it practical to load data first.

---

# ELT Architecture

```text
Source Systems
       │
       ▼
Load Raw Data
       │
       ▼
Lakehouse
       │
       ▼
Transform Inside Platform
```

---

# ELT Example

Customer Data:

```text
Extract
```

Load into:

```text
Bronze Layer
```

Then:

```text
Clean Data
Validate Data
Transform Data
```

inside Databricks.

---

# ETL Example Flow

```text
Database
    │
    ▼
ETL Tool
    │
    ▼
Transformation
    │
    ▼
Warehouse
```

---

# ELT Example Flow

```text
Database
    │
    ▼
Lakehouse
    │
    ▼
Bronze
    │
    ▼
Silver
    │
    ▼
Gold
```

---

# Why Databricks Prefers ELT

Databricks is built for:

```text
Large-Scale Processing
Distributed Computing
Massive Storage
```

Therefore:

```text
Load First
Transform Later
```

works extremely well.

---

# Medallion Architecture and ELT

Bronze:

```text
Load Raw Data
```

---

Silver:

```text
Transform
Clean
Validate
```

---

Gold:

```text
Business Transformations
Aggregations
KPIs
```

---

# ETL vs ELT Diagram

ETL:

```text
Extract
   │
   ▼
Transform
   │
   ▼
Load
```

---

ELT:

```text
Extract
   │
   ▼
Load
   │
   ▼
Transform
```

---

# Key Difference

ETL:

```text
Transform Before Loading
```

---

ELT:

```text
Transform After Loading
```

---

# ETL vs ELT Comparison

| Feature | ETL | ELT |
|----------|----------|----------|
| Transformation Location | Before Load | After Load |
| Raw Data Retained | Usually No | Yes |
| Scalability | Moderate | High |
| Flexibility | Lower | Higher |
| Development Speed | Slower | Faster |
| Modern Cloud Support | Limited | Excellent |
| Databricks Alignment | Moderate | Excellent |

---

# Data Retention

ETL:

```text
Raw Data Often Discarded
```

---

ELT:

```text
Raw Data Preserved
```

This is a major advantage.

---

# Why Raw Data Matters

Benefits:

```text
Reprocessing
Auditing
Compliance
Debugging
```

---

# Example

Suppose a transformation contains a bug.

ETL:

```text
Raw Data May Be Lost
```

---

ELT:

```text
Reload From Bronze
```

---

# Performance Considerations

Modern platforms like Databricks provide:

```text
Spark
Distributed Compute
Elastic Scaling
```

making ELT highly efficient.

---

# ETL in Data Warehouses

Traditional systems:

```text
Oracle
Teradata
Legacy Warehouses
```

commonly used ETL.

---

# ELT in Lakehouses

Modern systems:

```text
Databricks
Snowflake
BigQuery
Redshift
```

commonly use ELT.

---

# Real-World Retail Example

Sources:

```text
Orders
Customers
Inventory
```

---

# ETL Approach

```text
Extract
Transform Externally
Load Warehouse
```

---

# ELT Approach

```text
Extract
Load Bronze
Transform To Silver
Transform To Gold
```

---

# Banking Example

Transactions:

```text
Millions Per Day
```

ELT allows:

```text
Store Everything
Transform As Needed
```

---

# Machine Learning Example

Data Scientists often require:

```text
Raw Data
Historical Data
Feature Engineering
```

ELT supports these needs better.

---

# Why ELT Fits Databricks

Databricks provides:

```text
Delta Lake
Spark
Auto Loader
Streaming
Scalable Storage
```

All designed around ELT workflows.

---

# Common Mistakes

## Confusing ETL and ELT

Transformation order is the key difference.

---

## Deleting Raw Data

Prevents reprocessing.

---

## Over-Transforming During Ingestion

Makes pipelines difficult to maintain.

---

## Ignoring Medallion Architecture

Reduces flexibility.

---

# Best Practices

## Preserve Raw Data

Store in Bronze.

---

## Transform Incrementally

Move data through Silver and Gold.

---

## Use Delta Tables

Improve reliability.

---

## Separate Ingestion and Transformation

Creates cleaner architectures.

---

## Design for Reprocessing

Expect requirements to change.

---

# Interview Questions

### What does ETL stand for?

Extract, Transform, Load.

---

### What does ELT stand for?

Extract, Load, Transform.

---

### What is the main difference?

ETL transforms before loading.

ELT transforms after loading.

---

### Why is ELT popular in Databricks?

Because Databricks provides scalable storage and distributed compute.

---

### Which approach aligns with Medallion Architecture?

ELT.

---

### Why preserve raw data?

For auditing, debugging, compliance, and reprocessing.

---

### Which is more common in modern cloud platforms?

ELT.

---

# Summary

ETL and ELT are two approaches to data movement and transformation.

ETL:

```text
Extract
Transform
Load
```

ELT:

```text
Extract
Load
Transform
```

Modern Lakehouse platforms such as Databricks favor ELT because it:

```text
Scales Better
Preserves Raw Data
Supports Reprocessing
Improves Flexibility
```

and integrates naturally with:

```text
Bronze
Silver
Gold
```

architectures.

---

# Key Takeaways

✔ ETL transforms before loading

✔ ELT transforms after loading

✔ Databricks primarily follows ELT patterns

✔ ELT preserves raw data

✔ Medallion Architecture is an ELT implementation

✔ ELT improves flexibility and scalability

✔ Modern cloud platforms strongly favor ELT

✔ Understanding ETL vs ELT is a common interview topic

---

# Next Module

➡ 08-workflows.md
