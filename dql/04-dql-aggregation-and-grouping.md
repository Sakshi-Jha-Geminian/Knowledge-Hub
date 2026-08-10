# DQL Aggregation and Grouping

## Overview

Filtering helps you find the records you need. **Aggregation** helps you turn large amounts of data into meaningful numbers.

For example, instead of looking at thousands of individual error records, an SRE may want to know:

```text
How many errors occurred?

What is the average response time?

Which service has the most errors?

Which host has the highest CPU utilization?

How many requests failed per service?
```

These questions require aggregation and grouping.

The core DQL command used for aggregation is:

```dql
summarize
```

A useful mental model is:

```text
Millions of Records
       │
       ▼
    Filter
       │
       ▼
   Summarize
       │
       ▼
    Group By
       │
       ▼
Useful Numbers
```

---

# Learning Objectives

After completing this document, you should understand:

* What aggregation means
* The `summarize` command
* `count()`
* `countIf()`
* `avg()`
* `sum()`
* `min()`
* `max()`
* Grouping with `by`
* Multiple grouping fields
* Conditional aggregation
* Aggregating by service
* Aggregating by host
* Aggregating by Kubernetes dimensions
* Aggregation for SRE
* Aggregation for capacity planning
* Common aggregation mistakes

---

# What is Aggregation?

Aggregation means combining many records into a smaller set of meaningful results.

Suppose you have:

```text
ERROR
ERROR
ERROR
WARN
ERROR
INFO
```

Instead of returning all six records, you might want:

```text
ERROR → 4
WARN  → 1
INFO  → 1
```

This is aggregation.

---

# Why Aggregation Matters

Modern production systems generate enormous amounts of telemetry.

For example:

```text
10,000,000 requests
       │
       ▼
1,000,000 log records
       │
       ▼
100,000 errors
```

Looking at every record manually is impractical.

Aggregation allows us to convert this into:

```text
Total Requests = 10,000,000
Errors = 100,000
Error Rate = 1%
```

This is much more useful for SRE analysis.

---

# The `summarize` Command

`summarize` creates aggregated results.

Basic structure:

```dql
fetch logs
| summarize count()
```

Conceptually:

```text
All Records
    │
    ▼
count()
    │
    ▼
Total Number of Records
```

---

# `count()`

`count()` counts records.

Example:

```dql
fetch logs
| summarize count()
```

If 50,000 records match the query:

```text
count() = 50,000
```

---

# Filtering Before Counting

You can combine filtering and aggregation.

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count()
```

Read this as:

```text
Fetch logs
     ↓
Keep ERROR logs
     ↓
Count them
```

Result:

```text
ERROR Logs = 2,450
```

---

# Grouping with `by`

Aggregation becomes more powerful when data is grouped.

Example:

```dql
fetch logs
| summarize count(), by:{service.name}
```

Conceptually:

```text
Service A → 1,200
Service B → 800
Service C → 350
```

Instead of one total count, you get a count for each service.

---

# Why Grouping Is Important

Suppose the entire environment has:

```text
Total Errors = 10,000
```

That number alone does not tell you where the problem is.

Grouping gives:

```text
Payment Service → 7,500
Order Service   → 1,500
User Service    → 700
Search Service  → 300
```

Now you immediately know which service deserves investigation.

---

# Grouping by Service

One of the most common SRE queries is:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:{service.name}
```

This answers:

> How many errors did each service generate?

---

# Grouping by Host

You can also group records by host.

Conceptually:

```dql
fetch logs
| summarize count(), by:{host.name}
```

Result:

```text
server-01 → 5,000
server-02 → 2,500
server-03 → 900
```

This can help identify problematic infrastructure.

---

# Grouping by Kubernetes Namespace

For Kubernetes environments:

```dql
fetch logs
| summarize count(), by:{k8s.namespace.name}
```

This can show how records are distributed across namespaces.

Example:

```text
production → 50,000
staging    → 10,000
dev        → 3,000
```

---

# Grouping by Kubernetes Pod

You can investigate individual workloads.

Conceptually:

```dql
fetch logs
| summarize count(), by:{k8s.pod.name}
```

This can identify pods generating unusually high volumes of logs or errors.

---

# Multiple Grouping Fields

You can group by multiple dimensions.

For example:

```dql
fetch logs
| summarize count(), by:{service.name, host.name}
```

Now the results can show:

```text
Service A + Host 1 → 1,200
Service A + Host 2 → 900
Service B + Host 1 → 700
```

This provides more detailed context.

---

# Aggregating Different Values

Aggregation is not limited to counting.

Common aggregation functions include:

```text
count()
countIf()
avg()
sum()
min()
max()
```

Each answers a different question.

---

# `avg()`

`avg()` calculates the average of a numeric field.

Conceptually:

```dql
fetch ...
| summarize avg(response_time)
```

If response times are:

```text
100 ms
200 ms
300 ms
```

Then:

```text
Average = 200 ms
```

---

# Average Response Time

A common SRE analysis is:

```text
What is the average response time of this service?
```

Conceptually:

```dql
fetch ...
| filter service.name == "payment-service"
| summarize avg(response_time)
```

The exact field name and type depend on the data source.

---

# `sum()`

`sum()` adds numeric values together.

Example concept:

```dql
fetch ...
| summarize sum(bytes)
```

If the records contain:

```text
100
200
300
```

Then:

```text
sum = 600
```

This is useful for:

```text
Network Traffic
Data Volume
Request Counts
Resource Consumption
```

---

# `min()`

`min()` returns the smallest value.

Example:

```dql
fetch ...
| summarize min(response_time)
```

If:

```text
100
250
500
```

then:

```text
min = 100
```

---

# `max()`

`max()` returns the largest value.

Example:

```dql
fetch ...
| summarize max(response_time)
```

If:

```text
100
250
500
```

then:

```text
max = 500
```

Maximum values can be particularly useful for detecting extreme behavior.

---

# Why Average Is Not Always Enough

Suppose response times are:

```text
10 ms
10 ms
10 ms
10 ms
10,000 ms
```

The average can hide the extreme value.

Therefore, SRE analysis often considers:

```text
Average
Minimum
Maximum
Percentiles
```

Percentiles will be covered separately in advanced DQL analysis.

---

# Conditional Aggregation

Sometimes you want to count only records satisfying a condition.

For example:

```text
How many ERROR records exist?
```

A conditional count can be used.

Conceptually:

```dql
fetch logs
| summarize countIf(loglevel == "ERROR")
```

This allows multiple conditions to be analyzed in one aggregation.

---

# Multiple Aggregations

You can calculate several values in the same `summarize`.

For example:

```dql
fetch ...
| summarize
    count(),
    avg(response_time),
    min(response_time),
    max(response_time)
```

Conceptually:

```text
Total Requests
Average Response Time
Minimum Response Time
Maximum Response Time
```

This is much more useful than analyzing each value separately.

---

# Aggregation by Service

Consider three services:

```text
Payment
Order
User
```

You may want:

```text
Service
Requests
Average Latency
Errors
```

Conceptually:

```text
Payment → 50,000 requests → 250 ms
Order   → 30,000 requests → 180 ms
User    → 20,000 requests → 120 ms
```

This gives an operational overview of the system.

---

# Aggregation by Environment

You may also group by environment:

```text
Production
Staging
Development
```

Conceptually:

```dql
fetch ...
| summarize count(), by:{environment}
```

This allows comparison between environments.

---

# Aggregation by Status

For HTTP requests, status codes can be grouped.

Conceptually:

```dql
fetch ...
| summarize count(), by:{http.response.status_code}
```

Possible result:

```text
200 → 98,000
400 → 1,000
404 → 500
500 → 500
```

This immediately shows the distribution of response codes.

---

# Error Rate

One of the most important SRE calculations is error rate.

Conceptually:

```text
Error Rate =
Number of Errors
----------------
Total Requests
```

For example:

```text
Errors = 500
Requests = 50,000

Error Rate = 1%
```

This can be calculated from aggregated data.

---

# Why Error Rate Matters

Error rate is commonly used for:

```text
SLIs
SLOs
Alerting
Incident Detection
Service Health
```

For example:

```text
Error Rate
     │
     ▼
SLO Threshold
     │
     ▼
Potential Incident
```

---

# Aggregation for Latency

Suppose a service receives:

```text
100,000 requests
```

You may want to understand:

```text
Average latency
Maximum latency
Latency distribution
```

Aggregation provides the first layer of this analysis.

Later, percentile functions provide a more accurate view of user experience.

---

# Aggregation for Capacity Planning

Capacity planning requires understanding resource consumption.

For example:

```text
CPU Usage
    │
    ▼
Group by Host
    │
    ▼
Average CPU
    │
    ▼
Maximum CPU
    │
    ▼
Identify Saturation
```

Example result:

```text
Host A → Avg CPU 45%
Host B → Avg CPU 60%
Host C → Avg CPU 88%
```

Host C deserves investigation.

---

# Aggregation for Kubernetes

Kubernetes environments contain many dimensions.

You might aggregate by:

```text
Cluster
Namespace
Node
Pod
Workload
Service
```

Example conceptual hierarchy:

```text
Cluster
   │
   ├── Namespace A
   │      ├── Pod 1
   │      └── Pod 2
   │
   └── Namespace B
          ├── Pod 3
          └── Pod 4
```

Aggregation lets you move between these levels.

---

# Example: Error Analysis

Suppose an incident produces many errors.

Start:

```dql
fetch logs
| filter loglevel == "ERROR"
```

Then count:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count()
```

Then identify the responsible services:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:{service.name}
```

Now you can investigate the highest-error service.

---

# Example: Host Analysis

Suppose infrastructure performance is degrading.

You can aggregate records by host:

```dql
fetch ...
| summarize count(), by:{host.name}
```

Then compare:

```text
Host A → 1,000
Host B → 1,200
Host C → 8,500
```

Host C may require investigation.

---

# Example: Kubernetes Analysis

Suppose one namespace appears unhealthy.

You can aggregate:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:{k8s.namespace.name}
```

Then:

```text
production → 10,000
staging    → 500
development → 100
```

The production namespace is clearly the primary area to investigate.

---

# Aggregation Pipeline

A common pattern is:

```text
FETCH
  │
  ▼
FILTER
  │
  ▼
SUMMARIZE
  │
  ▼
GROUP
  │
  ▼
SORT
  │
  ▼
LIMIT
```

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:{service.name}
| sort count() desc
| limit 10
```

This answers:

> Which 10 services generated the most errors?

---

# Top-N Analysis

Top-N analysis is extremely useful.

Example questions:

```text
Top 10 services by errors

Top 10 hosts by CPU

Top 10 endpoints by latency

Top 10 pods by log volume
```

The general pattern is:

```text
Aggregate
   ↓
Group
   ↓
Sort descending
   ↓
Limit N
```

---

# Aggregation and `limit`

Be careful with the order of operations.

This:

```dql
fetch logs
| limit 100
| summarize count()
```

does **not** count all logs.

It only counts the records allowed through the `limit`.

If your intention is to count all matching records, aggregate first:

```dql
fetch logs
| summarize count()
```

Then apply a limit to the aggregated result if necessary.

---

# Grouping vs Filtering

These are different concepts.

### Filtering

Answers:

> Which records should I keep?

Example:

```dql
filter loglevel == "ERROR"
```

### Grouping

Answers:

> How should I divide the records for analysis?

Example:

```dql
by:{service.name}
```

Together:

```text
Filter
  ↓
Keep Errors
  ↓
Group by Service
  ↓
Count
```

---

# Aggregation vs Raw Data

Raw data:

```text
ERROR
ERROR
ERROR
ERROR
ERROR
```

Aggregated data:

```text
ERROR = 5
```

Raw data is useful for:

```text
Root Cause Analysis
Detailed Investigation
Individual Events
```

Aggregated data is useful for:

```text
Dashboards
Trends
Comparisons
SLOs
Capacity Planning
```

Both are important.

---

# Common Beginner Mistakes

## Mistake 1: Counting Before Filtering

If you need only errors:

```dql
filter loglevel == "ERROR"
```

should logically happen before the count.

---

## Mistake 2: Grouping by Too Many Fields

Grouping by many fields can produce extremely granular results.

Start with the dimension that answers your question.

---

## Mistake 3: Using Average Alone

Average latency can hide extreme values.

Consider percentiles and maximums when analyzing latency.

---

## Mistake 4: Limiting Before Aggregation

This can produce incomplete aggregates.

---

## Mistake 5: Forgetting Null or Missing Values

Some records may not contain the field you are grouping or aggregating.

Always consider whether the field is populated consistently.

---

# SRE Investigation Pattern

A practical SRE workflow is:

```text
Incident
   │
   ▼
Identify Time Window
   │
   ▼
Filter Relevant Data
   │
   ▼
Aggregate
   │
   ▼
Group by Service/Host/Pod
   │
   ▼
Find Outliers
   │
   ▼
Investigate Detailed Records
```

This is a very common observability workflow.

---

# Interview Questions

### What is aggregation?

Aggregation combines multiple records into summarized values.

### What command is commonly used for aggregation?

`summarize`.

### What does `count()` do?

It counts records.

### What does `avg()` do?

It calculates the average of a numeric field.

### What does `sum()` do?

It calculates the total of numeric values.

### What do `min()` and `max()` do?

They return the smallest and largest values respectively.

### What does `by` do?

It groups the aggregation according to one or more dimensions.

### Why is grouping useful?

It allows you to identify how values are distributed across services, hosts, namespaces, pods, or other dimensions.

### Why is `limit` dangerous before aggregation?

Because it restricts the records being aggregated and may produce an incomplete result.

---

# Key Takeaways

* Aggregation converts large datasets into meaningful summaries.
* `summarize` is the primary DQL command for aggregation.
* `count()` counts records.
* `countIf()` performs conditional counting.
* `avg()` calculates averages.
* `sum()` calculates totals.
* `min()` finds minimum values.
* `max()` finds maximum values.
* `by:{...}` groups results by dimensions.
* Grouping by service is extremely useful for SRE.
* Grouping by host helps identify infrastructure problems.
* Kubernetes data can be grouped by cluster, namespace, node, pod, and workload.
* Aggregation is essential for dashboards, SLO analysis, incident investigation, and capacity planning.
* Use the pattern **filter → summarize → group → sort → limit** for many operational queries.
* Do not use `limit` before aggregation unless you intentionally want to aggregate only the limited records.
