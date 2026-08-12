# VACUUM in Delta Lake

## Learning Objectives

By the end of this module, you will understand:

- What VACUUM Is
- Why VACUUM Is Needed
- How Delta Lake Stores Data
- Deleted Files vs Active Files
- Storage Cleanup
- Retention Periods
- VACUUM Command
- VACUUM and Time Travel
- VACUUM Safety Mechanisms
- Performance Considerations
- Real-World Examples
- Best Practices
- Interview Questions

---

# Introduction

Delta Lake provides:

```text
ACID Transactions
Time Travel
Version History
```

These features require Delta Lake to keep old files.

---

# The Problem

As tables evolve:

```text
INSERT
UPDATE
DELETE
MERGE
```

operations create new file versions.

---

# Result

Storage usage grows continuously.

---

# Example

Initial Table:

```text
File A
File B
```

---

# Update Operation

Delta Lake creates:

```text
File C
File D
```

---

# Old Files

```text
File A
File B
```

are no longer active.

---

# However

Delta Lake keeps them because:

```text
Time Travel
Version History
Recovery
```

depend on historical files.

---

# Storage Growth

Over time:

```text
Active Files
+
Historical Files
```

consume significant storage.

---

# Solution

Delta Lake provides:

```text
VACUUM
```

---

# What is VACUUM?

VACUUM removes:

```text
Unused Files
```

from Delta Tables.

---

# Simple Definition

VACUUM is:

```text
Garbage Collection
For Delta Tables
```

---

# Purpose of VACUUM

Goals:

```text
Free Storage
Reduce Clutter
Improve Manageability
```

---

# Important Concept

VACUUM does NOT remove:

```text
Current Data
```

---

# VACUUM Removes

Only:

```text
Obsolete Files
```

that are no longer referenced.

---

# Delta Table Lifecycle

```text
Version 1
      │
      ▼
Version 2
      │
      ▼
Version 3
```

---

# Historical Files

Older versions remain available.

---

# Storage View

```text
Current Files
Historical Files
Temporary Files
```

---

# VACUUM Cleans

```text
Historical Files
No Longer Needed
```

---

# Basic VACUUM Command

```sql
VACUUM sales;
```

---

# What Happens?

Delta Lake scans:

```text
Table Metadata
Transaction Log
```

and identifies removable files.

---

# Retention Period

VACUUM does not immediately delete files.

---

# Default Retention

```text
7 Days
```

(168 hours)

---

# Why Keep Old Files?

To support:

```text
Time Travel
Rollback
Recovery
```

---

# Example

Today:

```text
Version 20
```

---

# Yesterday

```text
Version 19
```

still available.

---

# Retention Protection

Files newer than retention threshold remain untouched.

---

# Example Command

```sql
VACUUM sales RETAIN 168 HOURS;
```

---

# Meaning

Keep:

```text
7 Days Of History
```

before deleting obsolete files.

---

# Custom Retention

Example:

```sql
VACUUM sales RETAIN 720 HOURS;
```

---

# Result

Keep:

```text
30 Days
```

of historical data.

---

# VACUUM and Time Travel

These concepts are closely related.

---

# Time Travel Requires

```text
Historical Files
```

---

# VACUUM Removes

```text
Historical Files
```

after retention expires.

---

# Relationship

```text
More History
     │
     ▼
More Storage
```

---

# Or

```text
Less Storage
     │
     ▼
Less History
```

---

# Trade-Off

Organizations balance:

```text
Storage Cost
Audit Requirements
Recovery Needs
```

---

# Example

Version History:

```text
Version 1
Version 2
Version 3
Version 4
Version 5
```

---

# After VACUUM

Old files removed.

---

# Result

Some versions may no longer support:

```text
Time Travel
```

---

# Important Warning

Once files are deleted:

```text
Recovery Is Not Possible
```

using Time Travel.

---

# Safety Check

Delta Lake includes:

```text
Retention Safety Mechanism
```

---

# Why?

To prevent accidental deletion.

---

# Example

Trying:

```sql
VACUUM sales RETAIN 1 HOURS;
```

may fail.

---

# Reason

Retention is too short.

Could destroy recent history.

---

# Safety Benefits

Protects:

```text
Recovery
Auditing
Compliance
```

---

# When Should VACUUM Be Run?

Common scenarios:

```text
Large Tables
High Update Rates
Frequent MERGE Operations
```

---

# Data Engineering Example

Daily MERGE Job

Creates:

```text
Thousands Of Obsolete Files
```

---

# VACUUM

Removes unnecessary storage.

---

# Retail Example

Product catalog updated daily.

Historical files accumulate rapidly.

VACUUM controls storage growth.

---

# Banking Example

Transaction tables undergo continuous updates.

VACUUM helps manage long-term storage.

---

# Healthcare Example

Patient records maintain history for compliance.

Retention periods may be longer.

---

# Benefits of VACUUM

```text
Reduced Storage Costs
Cleaner Tables
Better Manageability
Improved Efficiency
```

---

# What VACUUM Does NOT Do

VACUUM does NOT:

```text
Optimize Queries
Improve File Layout
Increase Performance Directly
```

---

# Optimization vs VACUUM

Different purposes.

---

# VACUUM

Focuses on:

```text
Storage Cleanup
```

---

# OPTIMIZE

Focuses on:

```text
Performance
File Compaction
```

---

# Common Mistakes

## Running VACUUM Too Aggressively

Removes needed history.

---

## Ignoring Retention Requirements

Violates audit policies.

---

## Assuming VACUUM Improves Query Speed

Its primary goal is storage cleanup.

---

## Running Without Understanding Time Travel

Can cause unexpected data loss.

---

# Best Practices

## Understand Business Retention Needs

Determine how much history is required.

---

## Keep Sufficient Time Travel Window

Support recovery and auditing.

---

## Monitor Storage Growth

Identify when cleanup is needed.

---

## Schedule VACUUM Regularly

Especially for high-volume tables.

---

## Coordinate with Governance Teams

Ensure compliance requirements are met.

---

# VACUUM Workflow

```text
Delta Table
      │
      ▼
Identify Obsolete Files
      │
      ▼
Apply Retention Rules
      │
      ▼
Delete Eligible Files
      │
      ▼
Free Storage
```

---

# Interview Questions

### What is VACUUM?

A Delta Lake command that removes obsolete files.

---

### Why is VACUUM needed?

To reclaim storage space.

---

### Does VACUUM delete active data?

No.

It removes only unused files.

---

### What is the default retention period?

```text
7 Days
```

---

### How does VACUUM affect Time Travel?

Old versions may become unavailable after files are removed.

---

### Does VACUUM improve query performance?

Not directly.

Its primary purpose is storage cleanup.

---

### What is the relationship between VACUUM and Time Travel?

Time Travel requires historical files; VACUUM removes them after retention expires.

---

### Why does Delta Lake enforce retention safety checks?

To prevent accidental loss of historical data.

---

# Summary

VACUUM is the storage cleanup mechanism of Delta Lake.

It removes:

```text
Obsolete Files
Unused Data
Expired History
```

while preserving active table contents.

Understanding VACUUM is essential because it directly impacts:

```text
Storage Costs
Time Travel
Recovery
Compliance
```

and is a critical part of production Delta Lake maintenance.

---

# Key Takeaways

✔ VACUUM removes obsolete files

✔ Helps control storage growth

✔ Default retention is 7 days

✔ Historical files enable Time Travel

✔ VACUUM can reduce available history

✔ Safety mechanisms prevent accidental deletion

✔ VACUUM focuses on cleanup, not optimization

✔ Retention policies should align with business requirements

✔ Essential for production Delta Lake environments

---

# Next Module

➡ 08-optimize.md
