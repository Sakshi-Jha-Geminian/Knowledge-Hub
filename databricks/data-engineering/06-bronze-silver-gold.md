# Bronze, Silver, and Gold Layers in Databricks

## Learning Objectives

By the end of this module, you will understand:

- Deep dive into Bronze Layer
- Deep dive into Silver Layer
- Deep dive into Gold Layer
- Layer Responsibilities
- Table Design Strategies
- Schema Management
- Data Quality Across Layers
- CDC (Change Data Capture)
- Streaming Data Flow
- Batch Data Flow
- Partitioning Strategies
- Retention Strategies
- Real-World Implementations
- Best Practices
- Interview Questions

---

# Introduction

In the previous chapter, we learned about:

```text
Medallion Architecture
```

and its three layers:

```text
Bronze
Silver
Gold
```

Now we will go deeper into how each layer is implemented in real-world Databricks environments.

---

# Medallion Architecture Refresher

```text
Source Systems
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

Each layer serves a specific purpose.

---

# Layer Philosophy

Think of the layers as a manufacturing process.

Raw Material:

```text
Bronze
```

Processed Material:

```text
Silver
```

Finished Product:

```text
Gold
```

---

# Bronze Layer Deep Dive

Bronze is the:

```text
Raw Data Layer
```

It is the entry point into the Lakehouse.

---

# Bronze Layer Objectives

```text
Store Original Data
Preserve History
Enable Reprocessing
Provide Auditability
```

---

# What Happens in Bronze?

Typical activities:

```text
Ingestion
Metadata Collection
Basic Validation
Raw Storage
```

---

# What Should NOT Happen in Bronze?

Avoid:

```text
Complex Business Logic
Heavy Aggregations
Business Calculations
```

---

# Bronze Data Example

Raw CSV:

```csv
1001,JOHN,,USA
```

Bronze Table:

```csv
1001,JOHN,,USA
```

Stored almost unchanged.

---

# Why Keep Raw Data?

Imagine a bug occurs in Silver processing.

Without Bronze:

```text
Original Data Lost
```

With Bronze:

```text
Reprocess Anytime
```

---

# Bronze Layer Characteristics

```text
High Volume
Low Quality
Minimal Transformations
Historical Storage
```

---

# Bronze Table Design

Common naming examples:

```text
bronze_orders
bronze_customers
bronze_payments
```

---

# Bronze Storage Pattern

Typical structure:

```text
/raw/orders/
/raw/customers/
/raw/payments/
```

---

# Auto Loader and Bronze

Most Auto Loader pipelines write directly into:

```text
Bronze Tables
```

Architecture:

```text
Cloud Storage
      │
      ▼
Auto Loader
      │
      ▼
Bronze Layer
```

---

# Bronze Schema Evolution

New columns often appear.

Example:

Yesterday:

```text
id
name
```

Today:

```text
id
name
email
```

Bronze should capture changes safely.

---

# Bronze and CDC

CDC stands for:

```text
Change Data Capture
```

---

# What is CDC?

Capturing changes such as:

```text
Insert
Update
Delete
```

from source systems.

---

# Example

Customer Table:

```text
John
```

Updated to:

```text
John Smith
```

CDC records the change.

---

# Why Store CDC in Bronze?

Benefits:

```text
Full History
Auditability
Reprocessing
Compliance
```

---

# Silver Layer Deep Dive

Silver is the:

```text
Trusted Data Layer
```

---

# Silver Layer Objectives

```text
Clean Data
Validate Data
Standardize Data
Enrich Data
```

---

# Silver Layer Responsibilities

Examples:

```text
Deduplication
Null Handling
Data Validation
Business Rules
Reference Data Joins
```

---

# Data Cleansing Example

Bronze:

```text
JOHN
john
John
```

Silver:

```text
John
```

Standardized.

---

# Null Handling Example

Bronze:

```text
customer_id = NULL
```

Silver:

```text
Reject
Fix
Default
```

depending on business rules.

---

# Deduplication Example

Bronze:

```text
Order 1001
Order 1001
Order 1001
```

Silver:

```text
Order 1001
```

Single trusted record.

---

# Silver Layer Characteristics

```text
Validated
Consistent
Reusable
High Quality
```

---

# Silver Table Design

Examples:

```text
silver_orders
silver_customers
silver_products
```

---

# Silver Enrichment

Additional information can be added.

Example:

Orders:

```text
Customer ID
```

Join with:

```text
Customer Table
```

to enrich data.

---

# Silver Layer Consumers

Common users:

```text
Data Scientists
Machine Learning Teams
Data Engineers
Analysts
```

---

# Silver and Streaming

Streaming data commonly flows:

```text
Bronze
   │
   ▼
Silver
```

continuously.

---

# Silver Validation Rules

Examples:

```text
No Null Customer IDs
Positive Order Amounts
Valid Dates
Valid Status Values
```

---

# Gold Layer Deep Dive

Gold is the:

```text
Business Consumption Layer
```

---

# Gold Objectives

```text
Business Metrics
Reporting
Dashboards
KPIs
Executive Analytics
```

---

# Gold Characteristics

```text
Curated
Aggregated
Business Ready
Optimized
```

---

# Gold Example

Silver Orders:

```text
10 Million Transactions
```

Gold Table:

```text
Revenue By Region
```

---

# Gold Transformations

Examples:

```text
SUM()
COUNT()
AVG()
Business KPIs
```

---

# Gold Table Examples

```text
gold_daily_sales
gold_customer_metrics
gold_inventory_summary
```

---

# Dashboard Example

Gold table:

```text
Revenue By Day
```

used directly by:

```text
Power BI
Tableau
Looker
Databricks SQL
```

---

# Data Quality Across Layers

Bronze:

```text
Minimal Validation
```

---

Silver:

```text
Strong Validation
```

---

Gold:

```text
Business Validation
```

---

# Data Volume Trend

Generally:

```text
Bronze > Silver > Gold
```

because aggregation reduces data volume.

---

Example:

Bronze:

```text
100 Million Records
```

Silver:

```text
90 Million Records
```

Gold:

```text
1 Million Aggregated Records
```

---

# Partitioning Strategy

Partitioning improves performance.

---

# Bronze Partitioning

Commonly:

```text
Ingestion Date
```

Example:

```text
2026-08-01
2026-08-02
2026-08-03
```

---

# Silver Partitioning

Often:

```text
Business Date
Region
Country
```

---

# Gold Partitioning

Based on query patterns.

Examples:

```text
Month
Year
Region
```

---

# Retention Strategy

Different layers may have different retention periods.

---

# Bronze Retention

Often longest retention.

Example:

```text
1–7 Years
```

depending on compliance.

---

# Silver Retention

Typically medium-term.

---

# Gold Retention

May store only required business summaries.

---

# Streaming Architecture Example

```text
Kafka
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

updated continuously.

---

# Batch Architecture Example

```text
Files
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

executed by scheduled jobs.

---

# Real-World Retail Example

Source Systems:

```text
Orders
Inventory
Payments
Customers
```

---

Bronze:

```text
Raw Data
```

---

Silver:

```text
Validated Transactions
```

---

Gold:

```text
Revenue KPIs
Inventory Metrics
Customer Analytics
```

---

# Banking Example

Bronze:

```text
Raw Transactions
```

---

Silver:

```text
Fraud-Checked Transactions
```

---

Gold:

```text
Risk Reports
Executive Dashboards
```

---

# Common Mistakes

## Over-Transforming Bronze

Bronze should remain close to source data.

---

## Skipping Silver

Leads to duplicated logic.

---

## Storing Raw Data in Gold

Violates layer responsibilities.

---

## No Data Quality Checks

Creates unreliable analytics.

---

# Best Practices

## Keep Bronze Immutable

Preserve original data.

---

## Centralize Cleansing in Silver

Avoid repeated transformations.

---

## Build Metrics in Gold

Gold should serve business users.

---

## Use Delta Tables

Across all layers.

---

## Implement Data Quality Rules

Validate early and consistently.

---

## Automate Pipelines

Reduce manual operations.

---

# Interview Questions

### What is stored in Bronze?

Raw source data with minimal transformation.

---

### What is stored in Silver?

Cleaned, validated, and enriched data.

---

### What is stored in Gold?

Aggregated business-ready data.

---

### Why keep Bronze immutable?

For auditing, compliance, and reprocessing.

---

### Where should deduplication occur?

Typically in the Silver layer.

---

### Which layer is used by dashboards?

Gold.

---

### What is CDC?

Change Data Capture, used to track inserts, updates, and deletes.

---

### Why is Silver important?

It creates trusted datasets for downstream consumption.

---

# Summary

Bronze, Silver, and Gold layers are the practical implementation of Medallion Architecture.

Each layer has a specific responsibility:

```text
Bronze → Raw Data

Silver → Trusted Data

Gold → Business Data
```

This separation improves:

```text
Data Quality
Governance
Maintainability
Scalability
Performance
```

and is considered a best practice in modern Databricks Lakehouse implementations.

---

# Key Takeaways

✔ Bronze stores raw source data

✔ Silver performs cleansing and validation

✔ Gold serves business users

✔ CDC is commonly captured in Bronze

✔ Data quality improves across layers

✔ Data volume generally decreases toward Gold

✔ Batch and streaming pipelines both use these layers

✔ Proper layer design improves maintainability

---

# Next Module

➡ 07-etl-vs-elt.md
