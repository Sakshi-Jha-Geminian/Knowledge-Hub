# DQL Joins, Lookups, and Subqueries

## Overview

In real observability environments, the information needed to answer an operational question is not always contained in a single dataset.

For example, you may want to answer:

> Which production services are generating the most errors, and what team owns them?

Your telemetry may contain:

```text
Service telemetry
      │
      ├── service.name
      ├── error count
      └── latency
```

while ownership information may exist somewhere else:

```text
Service ownership data
      │
      ├── service.name
      ├── team
      └── application owner
```

You need a way to combine or correlate these datasets.

DQL provides capabilities such as:

* Joins
* Lookups
* Subqueries
* Subquery-based filtering
* Data enrichment

These are powerful techniques, but they should be used carefully because unnecessarily complex queries can become difficult to maintain and can consume more resources.

---

# Learning Objectives

After completing this document, you should understand:

* Why datasets sometimes need to be combined
* What a join is
* What a lookup is
* What a subquery is
* Join keys
* Inner joins
* Left joins
* Lookup-based enrichment
* Subquery filtering
* When to use joins
* When to use lookups
* When to use subqueries
* Common performance considerations
* SRE use cases

---

# Why Combine Data?

Consider:

```text
Dataset A

Service
Error Count
Latency
```

and:

```text
Dataset B

Service
Team
Criticality
Environment
```

Individually, these datasets answer different questions.

Together:

```text
Service
Error Count
Latency
Team
Criticality
Environment
```

provide much more operational context.

---

# What Is a Join?

A join combines records from two datasets using a common field.

For example:

```text
Dataset A
Service A → 500 errors
Service B → 200 errors

Dataset B
Service A → Payments Team
Service B → Orders Team
```

Joining on `service.name` can produce:

```text
Service A → 500 errors → Payments Team
Service B → 200 errors → Orders Team
```

The common field is called the **join key**.

---

# Join Key

A join requires a field that exists in both datasets.

Example:

```text
Dataset A:
service.name

Dataset B:
service.name
```

Therefore:

```text
service.name
```

can act as the join key.

Good join keys should identify the same logical entity in both datasets.

---

# Conceptual Join

Think about a join like this:

```text
             Join Key
                │
      ┌─────────┴─────────┐
      ▼                   ▼
Dataset A             Dataset B
      │                   │
      └─────────┬─────────┘
                ▼
          Combined Data
```

---

# Inner Join

An inner join returns records where a matching key exists in both datasets.

Example:

```text
Dataset A:

Service A
Service B
Service C

Dataset B:

Service A
Service B
Service D
```

Inner join result:

```text
Service A
Service B
```

Because those services exist in both datasets.

---

# Left Join

A left join keeps records from the left dataset even when a match does not exist on the right.

Example:

```text
Left Dataset:

Service A
Service B
Service C

Right Dataset:

Service A
Service B
```

Result:

```text
Service A → Match
Service B → Match
Service C → No match
```

This is useful when the primary dataset must not lose records simply because enrichment data is missing.

---

# Join Types

The exact join capabilities and syntax depend on the current DQL implementation, but conceptually you should understand:

```text
Inner Join
Left Join
```

The key difference is whether unmatched records from the primary dataset are retained.

Always check the current Dynatrace DQL reference for supported join types and syntax.

---

# Why Joins Can Be Expensive

Suppose:

```text
Dataset A = 1,000,000 records
Dataset B = 2,000,000 records
```

Combining them can require significant processing.

Therefore:

```text
Do not join everything unnecessarily.
```

Instead:

```text
Filter first
      ↓
Reduce dataset
      ↓
Join relevant data
```

This is generally a better approach.

---

# Filtering Before Joining

Suppose you only care about production.

Instead of:

```text
All Data A
      +
All Data B
      ↓
Join
```

prefer conceptually:

```text
Production Data A
      +
Production Data B
      ↓
Join
```

This reduces unnecessary processing.

---

# What Is a Lookup?

A lookup enriches records using a reference dataset.

Think of it like a dictionary.

For example:

```text
Service Name → Owning Team
```

Reference data:

```text
payment-service → Payments
order-service   → Orders
user-service    → Identity
```

Telemetry:

```text
payment-service → 500 errors
order-service   → 200 errors
```

After enrichment:

```text
payment-service → 500 errors → Payments
order-service   → 200 errors → Orders
```

---

# Lookup Mental Model

```text
Telemetry
   │
   ▼
Lookup Key
   │
   ▼
Reference Data
   │
   ▼
Enriched Telemetry
```

Lookups are particularly useful when you have relatively static reference information.

---

# Lookup vs Join

These concepts are related but should not be treated as identical.

### Join

Useful when:

```text
Two datasets
   +
Need to combine matching records
```

### Lookup

Useful when:

```text
Primary telemetry
   +
Reference information
   ↓
Enrichment
```

Examples of lookup data:

```text
Service → Owner
Host → Environment
Application → Business Unit
Entity → Criticality
```

---

# Why Lookup Is Useful for SRE

Imagine an alert says:

```text
payment-service
Error Rate = 8%
```

That tells you the technical problem.

But if lookup data adds:

```text
Owner = Payments Team
Criticality = Tier 1
Business Unit = Trading
```

the alert becomes much more actionable.

---

# Operational Enrichment

Without enrichment:

```text
Service:
payment-service

Error Rate:
8%
```

With enrichment:

```text
Service:
payment-service

Error Rate:
8%

Owner:
Payments Team

Criticality:
Tier 1

Environment:
Production
```

This is much more useful during incident response.

---

# What Is a Subquery?

A subquery is a query used inside another query.

Conceptually:

```text
Outer Query
    │
    └── Inner Query
```

The inner query produces information that the outer query uses.

---

# Subquery Example

Suppose you want to find services that appear in another dataset.

Conceptually:

```text
Outer Query:
Find telemetry for services

Inner Query:
Find services matching a condition

Outer Query:
Return telemetry only for those services
```

This is useful when the result of one query determines what the next query should examine.

---

# Subqueries for Filtering

A subquery can act as a dynamic filter.

Conceptually:

```text
Main Dataset
     │
     ▼
Keep records where key
exists in subquery result
```

For example:

```text
Main data:
All services

Subquery:
Services with critical incidents

Result:
Only services involved in critical incidents
```

---

# `in` with a Subquery

DQL supports using `in` with a subquery.

Conceptually:

```dql
filter service.name in [
    subquery-result
]
```

The exact syntax should be checked against the current DQL reference because subquery syntax and supported forms can evolve.

The important idea is:

```text
Subquery
    ↓
Produces values
    ↓
Outer query
    ↓
Uses those values for filtering
```

---

# Subquery vs Join

This distinction is important.

### Join

Think:

```text
Combine records
```

### Subquery

Think:

```text
Use the result of one query
to influence another query
```

For example:

```text
Join:
Service data + Owner data

Subquery:
Services that satisfy condition X
```

---

# Example: Find Problematic Services

Suppose you first identify services with high error rates.

Conceptually:

```text
Subquery:

Services
   ↓
Error Rate > 5%
   ↓
Return Service Names
```

Then:

```text
Outer Query:

All telemetry
   ↓
Keep only those services
```

This allows you to investigate the problematic services in more detail.

---

# Subqueries and SRE Investigation

A useful workflow is:

```text
All Services
     │
     ▼
Identify High Error Services
     │
     ▼
Subquery Result
     │
     ▼
Filter Detailed Telemetry
     │
     ▼
Root Cause Investigation
```

This separates:

```text
Detection
```

from:

```text
Investigation
```

---

# Example: Incident Investigation

Suppose:

```text
Payment Service
Error Rate = 8%

Order Service
Error Rate = 1%

User Service
Error Rate = 0.2%
```

You identify:

```text
payment-service
```

as problematic.

Then you can investigate its:

```text
Logs
Traces
Host
Kubernetes Pods
Dependencies
```

A subquery can help dynamically identify the relevant service set.

---

# Lookup-Based Ownership

Suppose a service ownership reference contains:

```text
service.name       owner
-------------------------
payment-service    Payments
order-service      Orders
user-service       Identity
```

Telemetry:

```text
service.name       errors
-------------------------
payment-service    500
order-service      100
```

After lookup enrichment:

```text
service.name       errors     owner
------------------------------------
payment-service    500        Payments
order-service      100        Orders
```

Now the SRE immediately knows who should investigate.

---

# Lookup-Based Criticality

Another useful reference table may contain:

```text
service.name       criticality
------------------------------
payment-service    Tier 1
order-service      Tier 1
internal-tool      Tier 3
```

This allows incidents to be prioritized.

For example:

```text
Error Rate = 5%
+
Tier 1 Service
```

is generally more urgent than:

```text
Error Rate = 5%
+
Tier 3 Service
```

---

# Lookup-Based Environment

Reference data may also contain:

```text
Host       Environment
----------------------
host-01    Production
host-02    Staging
host-03    Development
```

Telemetry can then be enriched with environment information.

This is useful when the telemetry itself does not contain the desired business or organizational metadata.

---

# Joins and Distributed Systems

Modern applications generate telemetry across:

```text
Services
Hosts
Containers
Kubernetes
Databases
Queues
Cloud Services
```

Relationships between these entities are essential for root-cause analysis.

Combining datasets can help answer:

```text
Which service?
Which host?
Which pod?
Which team?
Which environment?
Which dependency?
```

---

# Joining Metrics and Metadata

Suppose you have:

```text
Metric Dataset
CPU = 90%
service = payment-service
```

and:

```text
Metadata Dataset
service = payment-service
owner = Payments
criticality = Tier 1
```

Combining them gives:

```text
Payment Service
CPU = 90%
Owner = Payments
Criticality = Tier 1
```

This is much more actionable.

---

# Joins and Capacity Planning

Capacity planning may require combining:

```text
Resource Usage
+
Infrastructure Metadata
```

For example:

```text
Host
CPU
Memory
Environment
Instance Type
```

Then you can ask:

> Which production instance types are approaching capacity?

---

# Joins and Predictive Monitoring

Predictive monitoring can also benefit from enrichment.

For example:

```text
Time-Series Data
       │
       ▼
High Growth Workload
       │
       ▼
Lookup Ownership
       │
       ▼
Lookup Criticality
       │
       ▼
Prioritize Proactive Action
```

This moves predictive monitoring from:

```text
Technical prediction
```

toward:

```text
Business-aware prediction
```

---

# Data Enrichment

Data enrichment means adding useful context to existing telemetry.

For example:

```text
Raw Event
   │
   ▼
Service Name
   │
   ▼
Owner
   │
   ▼
Environment
   │
   ▼
Criticality
   │
   ▼
Business Context
```

Enriched telemetry is more useful for:

```text
Dashboards
Alerts
Incident Response
Root Cause Analysis
Capacity Planning
```

---

# Join Key Quality

The quality of the join depends heavily on the join key.

A poor key can cause:

```text
Missing Matches
Incorrect Matches
Duplicate Results
Unexpected Results
```

For example:

```text
payment-service
```

and:

```text
Payment-Service
```

may not necessarily behave as the same value depending on the comparison being performed.

Always normalize and verify identifiers when appropriate.

---

# Duplicate Records

Suppose:

```text
Dataset A

payment-service
```

and:

```text
Dataset B

payment-service → Team A
payment-service → Team B
```

A join may produce multiple matches.

Result:

```text
payment-service → Team A
payment-service → Team B
```

This may be correct or may indicate bad reference data.

Always understand the cardinality of your relationship.

---

# Join Cardinality

Cardinality describes how records relate.

Common patterns:

```text
One-to-One
One-to-Many
Many-to-One
Many-to-Many
```

For example:

```text
Service → Owner
```

might ideally be:

```text
One Service → One Owner
```

while:

```text
Service → Pods
```

is naturally:

```text
One Service → Many Pods
```

Understanding this helps avoid unexpected results.

---

# One-to-Many Example

```text
Payment Service
      │
      ├── Pod 1
      ├── Pod 2
      └── Pod 3
```

If you join service-level data with pod-level data, one service may appear multiple times.

This can unintentionally inflate counts.

---

# Avoiding Double Counting

Suppose:

```text
Payment Service
Errors = 100
```

and it has:

```text
3 Pods
```

A naive join could make the error count appear as:

```text
100 × 3 = 300
```

even though only 100 errors occurred.

This is a major issue when combining aggregated data.

Always understand whether your join changes the number of records.

---

# Aggregation Before Join

Sometimes it is better to aggregate first.

Instead of:

```text
Millions of Raw Records
        +
Metadata
        ↓
Join
```

consider:

```text
Millions of Raw Records
        ↓
Aggregate by Service
        ↓
Small Dataset
        +
Metadata
        ↓
Join / Enrich
```

This can be more efficient and easier to reason about.

---

# Filtering Before Join

Similarly:

```text
All Telemetry
     ↓
Production Only
     ↓
Relevant Services
     ↓
Join
```

is usually preferable to joining the entire dataset unnecessarily.

---

# Join vs Lookup vs Subquery

Use this mental model:

| Technique | Main Purpose                          |
| --------- | ------------------------------------- |
| Join      | Combine related datasets              |
| Lookup    | Enrich records with reference data    |
| Subquery  | Use one query's result inside another |
| Filter    | Reduce records                        |
| Summarize | Aggregate records                     |

---

# Decision Guide

Ask yourself:

### Do I need fields from another dataset?

Consider:

```text
Join
```

### Do I need to add static/reference information?

Consider:

```text
Lookup
```

### Do I need the result of one query to filter another?

Consider:

```text
Subquery
```

### Do I only need to remove unwanted records?

Use:

```text
Filter
```

### Do I need totals or statistics?

Use:

```text
Summarize
```

---

# Performance Considerations

Joins and subqueries can become expensive.

Good practices include:

```text
1. Restrict the timeframe
2. Filter early
3. Select only required fields
4. Aggregate when appropriate
5. Use the smallest useful dataset
6. Avoid unnecessary joins
7. Validate join cardinality
```

---

# Example: Efficient Investigation

Suppose you need to find Tier 1 services with high error rates.

A good conceptual workflow is:

```text
Telemetry
   │
   ▼
Relevant Time Window
   │
   ▼
Production
   │
   ▼
Aggregate Errors by Service
   │
   ▼
Enrich with Criticality
   │
   ▼
Keep Tier 1
   │
   ▼
Keep High Error Rate
```

This is much more efficient than joining large raw datasets first.

---

# SRE Use Cases

Joins, lookups, and subqueries are useful for:

```text
Incident Investigation
Service Ownership
Application Inventory
Business Context
Criticality Classification
Kubernetes Analysis
Capacity Planning
Root Cause Analysis
Alert Enrichment
```

---

# Interview Questions

### What is a join?

A join combines records from two datasets using a common key.

### What is a join key?

A field used to match related records between datasets.

### What is an inner join?

It returns records where matching keys exist in both datasets.

### What is a left join?

It preserves records from the left dataset even when no matching record exists on the right.

### What is a lookup?

A mechanism for enriching records using reference data.

### What is a subquery?

A query nested inside another query whose result is used by the outer query.

### When would you use a lookup?

When you need to enrich telemetry with relatively static reference information such as ownership, environment, or criticality.

### Why can joins be dangerous?

They can create duplicate records or unintentionally inflate aggregated values.

### What is join cardinality?

It describes how records relate between datasets, such as one-to-one or one-to-many.

### Why should filtering happen before a join?

It can reduce the amount of data that needs to be combined and processed.

---

# Key Takeaways

* Not all operational information exists in one dataset.
* Joins combine related datasets using common keys.
* Lookups enrich telemetry with reference information.
* Subqueries allow one query's results to influence another query.
* Join keys must be carefully chosen.
* Join cardinality matters.
* One-to-many relationships can cause duplicate rows and double counting.
* Filtering before joining can reduce unnecessary processing.
* Aggregating before joining can sometimes simplify the query and reduce data volume.
* Use joins for dataset combination.
* Use lookups for reference-data enrichment.
* Use subqueries when one query needs to drive another.
* These techniques are especially useful for SRE, incident response, capacity planning, and predictive monitoring.
