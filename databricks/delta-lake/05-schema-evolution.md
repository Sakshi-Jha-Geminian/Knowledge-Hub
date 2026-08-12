# Schema Evolution in Delta Lake

## Learning Objectives

By the end of this module, you will understand:

- What Schema Evolution Is
- Why Schema Evolution Is Needed
- Schema Enforcement vs Schema Evolution
- How Delta Lake Handles Schema Changes
- Adding New Columns
- Merging Schemas
- Automatic Schema Evolution
- Schema Evolution During Streaming
- Common Use Cases
- Limitations
- Best Practices
- Interview Questions

---

# Introduction

Data is constantly changing.

Businesses evolve and new requirements appear regularly.

Example:

```text
Today:
Customer_ID
Name
Email

Tomorrow:
Customer_ID
Name
Email
Phone_Number
```

---

# The Challenge

Traditional systems often require:

```text
Table Recreation
Pipeline Changes
Downtime
Manual Migration
```

for schema modifications.

---

# Delta Lake Solution

Delta Lake provides:

```text
Schema Evolution
```

which allows schemas to evolve safely over time.

---

# What is Schema Evolution?

Schema Evolution is the ability to modify a table schema while preserving existing data.

---

# Simple Definition

Schema Evolution means:

```text
Allowing Controlled Changes
To Table Structure
```

without rebuilding the table.

---

# Why Schema Evolution Matters

Business requirements change frequently.

Examples:

```text
New Product Attributes
Additional Customer Fields
Extra Transaction Details
New Regulatory Data
```

---

# Example

Initial Schema:

```text
Customer_ID
Name
Email
```

---

# New Requirement

Add:

```text
Phone_Number
```

---

# Without Schema Evolution

You may need:

```text
Table Migration
Data Reload
Pipeline Redesign
```

---

# With Schema Evolution

Simply extend the schema.

---

# Schema Enforcement vs Schema Evolution

These concepts work together.

---

# Schema Enforcement

Purpose:

```text
Protect Data Quality
```

---

# Schema Evolution

Purpose:

```text
Allow Safe Schema Changes
```

---

# Comparison

| Feature | Schema Enforcement | Schema Evolution |
|----------|----------|----------|
| Goal | Prevent Invalid Data | Allow Valid Changes |
| Focus | Protection | Flexibility |
| Result | Reject Changes | Accept Changes |
| Use Case | Data Quality | Business Growth |

---

# Common Schema Changes

Examples:

```text
Add New Columns
Expand Data Structure
Introduce New Business Fields
```

---

# Delta Lake Supports

Most commonly:

```text
Column Additions
```

while preserving existing data.

---

# Example Table

```sql
CREATE TABLE customers
(
    customer_id INT,
    name STRING
)
USING DELTA;
```

---

# Existing Data

```text
1 John
2 Alice
3 Bob
```

---

# New Requirement

Add:

```text
email STRING
```

---

# Updated Schema

```text
customer_id
name
email
```

---

# Existing Rows

Old records remain valid.

---

# Result

```text
1 John  NULL
2 Alice NULL
3 Bob   NULL
```

---

# New Records

Can populate:

```text
Email
```

normally.

---

# Merge Schema Option

Delta Lake supports:

```text
mergeSchema
```

during writes.

---

# Example

```python
df.write \
.format("delta") \
.option("mergeSchema","true") \
.mode("append") \
.save("/delta/customers")
```

---

# What Happens?

Delta Lake compares:

```text
Current Schema

vs

Incoming Schema
```

and safely merges compatible changes.

---

# Before Merge

Table Schema:

```text
ID
Name
```

---

# Incoming Data

```text
ID
Name
Email
```

---

# After Merge

Schema Becomes:

```text
ID
Name
Email
```

---

# Existing Data

Preserved automatically.

---

# Automatic Schema Evolution

Delta Lake can automatically evolve schemas during operations such as:

```text
MERGE
Streaming Writes
ETL Pipelines
```

---

# Why This Helps

Reduces:

```text
Manual Work
Downtime
Operational Complexity
```

---

# Real-World Example

E-commerce Platform

Original Schema:

```text
Order_ID
Customer_ID
Amount
```

---

# New Requirement

Track:

```text
Discount_Code
```

---

# Schema Evolution

Adds new column without rebuilding the table.

---

# Streaming Example

IoT Sensors

Original Data:

```text
Device_ID
Temperature
Timestamp
```

---

# New Sensor Firmware

Adds:

```text
Humidity
```

---

# Delta Lake

Can evolve schema to include the new field.

---

# Data Engineering Perspective

Schema Evolution is essential because:

```text
Source Systems Change Frequently
```

---

# Typical Changes

```text
New API Fields
Additional Columns
Regulatory Requirements
Business Expansion
```

---

# How Delta Handles Existing Data

Existing rows:

```text
Remain Unchanged
```

---

# New Columns

Receive:

```text
NULL Values
```

for older records.

---

# Example

Before:

```text
ID  Name

1   John
2   Alice
```

---

# After Evolution

```text
ID  Name   Email

1   John   NULL
2   Alice  NULL
```

---

# New Data

```text
3   Bob    bob@email.com
```

---

# Benefits

```text
Flexibility
Scalability
Business Agility
Reduced Maintenance
```

---

# Limitations

Schema Evolution should be used carefully.

---

# Potential Risks

```text
Unexpected Columns
Poor Governance
Schema Drift
```

---

# What is Schema Drift?

Continuous uncontrolled schema changes.

Example:

```text
New Columns Every Day
```

creating complexity.

---

# Governance Importance

Organizations should review:

```text
Schema Changes
Naming Standards
Data Ownership
```

---

# Common Mistakes

## Enabling Evolution Everywhere

May introduce unwanted columns.

---

## Ignoring Governance

Leads to inconsistent schemas.

---

## Not Reviewing New Fields

Can create downstream issues.

---

## Confusing Evolution with Validation

Evolution does not replace data quality checks.

---

# Best Practices

## Enable Evolution Only When Needed

Avoid unnecessary schema growth.

---

## Use Explicit Schemas

Do not rely entirely on inference.

---

## Review Schema Changes

Maintain governance processes.

---

## Monitor New Columns

Track changes over time.

---

## Combine with Schema Enforcement

Balance flexibility and protection.

---

# Real-World Banking Example

Transaction Table

Original Schema:

```text
Account_ID
Amount
Timestamp
```

---

# New Compliance Requirement

Add:

```text
Risk_Score
```

---

# Delta Lake

Supports adding the column without rebuilding the table.

---

# Healthcare Example

Patient Records

New regulatory field:

```text
Insurance_Category
```

added safely through schema evolution.

---

# Retail Example

Products table gains:

```text
Supplier_Rating
```

without affecting existing data.

---

# Interview Questions

### What is Schema Evolution?

The ability to modify a table schema while preserving existing data.

---

### Why is Schema Evolution important?

Business requirements change over time.

---

### What is the difference between Schema Enforcement and Schema Evolution?

Enforcement prevents invalid data.

Evolution allows controlled schema changes.

---

### What happens to old records when a new column is added?

They receive NULL values for the new column.

---

### What option enables schema merging?

```python
.option("mergeSchema","true")
```

---

### Does Schema Evolution replace Schema Enforcement?

No.

They solve different problems.

---

### What is Schema Drift?

Uncontrolled schema changes over time.

---

### Why should Schema Evolution be governed?

To maintain consistency and prevent unnecessary complexity.

---

# Summary

Schema Evolution allows Delta Lake tables to adapt to changing business requirements without requiring table rebuilds or major migrations.

It enables:

```text
Flexible Data Models
Safe Schema Growth
Reduced Operational Overhead
```

while preserving existing data.

When combined with Schema Enforcement, organizations gain both:

```text
Data Quality
+
Flexibility
```

which is essential for modern data engineering.

---

# Key Takeaways

✔ Business requirements change frequently

✔ Schema Evolution allows safe schema modifications

✔ Existing data remains intact

✔ New columns receive NULL values for older records

✔ mergeSchema enables schema merging

✔ Works well with ETL and streaming workloads

✔ Must be governed carefully

✔ Schema Drift should be avoided

✔ Schema Evolution complements Schema Enforcement

---

# Next Module

➡ 06-merge-upsert.md
