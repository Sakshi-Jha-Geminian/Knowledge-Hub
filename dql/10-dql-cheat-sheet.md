# DQL Cheat Sheet

## Overview

This document is a practical DQL reference for day-to-day Dynatrace and SRE investigations.

Instead of explaining DQL concepts individually, this guide focuses on:

* Common query patterns
* Frequently used commands
* Filtering
* Aggregation
* Sorting
* Time-series analysis
* Field manipulation
* Conditional logic
* Lookups
* Joins
* Parsing
* SRE investigations
* Troubleshooting patterns
* Query-writing best practices

Use this document as a quick reference while writing DQL queries.

---

# 1. DQL Query Structure

A DQL query is generally built as a pipeline.

```text
Data
  │
  ▼
Filter
  │
  ▼
Transform
  │
  ▼
Aggregate
  │
  ▼
Sort
  │
  ▼
Limit
```

Example structure:

```text
fetch logs
| filter ...
| fields ...
| summarize ...
| sort ...
| limit ...
```

Each command processes the result of the previous command.

---

# 2. fetch

`fetch` is commonly used to retrieve a particular type of Dynatrace data.

Conceptually:

```text
fetch logs
```

Other commonly queried data types can include:

```text
logs
events
spans
metrics
business events
```

The exact available data depends on the Dynatrace environment and telemetry configuration.

---

# 3. filter

`filter` limits the data to records matching a condition.

Conceptual example:

```text
fetch logs
| filter loglevel == "ERROR"
```

This means:

```text
Get logs
   ↓
Keep only ERROR logs
```

---

# 4. Multiple Filters

You can combine conditions.

```text
fetch logs
| filter loglevel == "ERROR"
| filter status == "500"
```

Conceptually:

```text
All Logs
   ↓
ERROR Logs
   ↓
HTTP 500 Logs
```

---

# 5. AND Condition

Use logical conditions when multiple conditions must be satisfied.

```text
filter condition1 AND condition2
```

Example:

```text
filter loglevel == "ERROR" AND service.name == "payment-service"
```

This means both conditions must be true.

---

# 6. OR Condition

Use `OR` when either condition can match.

```text
filter condition1 OR condition2
```

Example:

```text
filter status == "500" OR status == "503"
```

This finds either HTTP 500 or HTTP 503 responses.

---

# 7. NOT Condition

Use negation when you want to exclude something.

Conceptually:

```text
filter NOT condition
```

Example:

```text
filter loglevel != "INFO"
```

This excludes INFO logs.

---

# 8. fields

`fields` controls which fields are returned.

Conceptually:

```text
fields timestamp, service.name, status
```

Instead of displaying every available field, the result focuses on the selected fields.

---

# 9. fieldsAdd

Use `fieldsAdd` when you want to create an additional field while preserving the existing fields.

Conceptually:

```text
fieldsAdd error = status >= 500
```

Now the result can contain:

```text
status
error
```

---

# 10. fieldsRename

Use field renaming when a field needs a more useful or readable name.

Conceptually:

```text
fieldsRename service = service.name
```

This can make query results easier to understand.

---

# 11. summarize

`summarize` is one of the most important commands for analytics.

It is used to calculate aggregated results.

Examples of aggregation concepts:

```text
count
average
minimum
maximum
percentiles
distinct count
```

Conceptually:

```text
summarize count()
```

This answers:

> How many records are there?

---

# 12. Count

Count records to determine volume.

Conceptually:

```text
summarize count()
```

Useful for:

```text
Number of requests
Number of errors
Number of logs
Number of events
```

---

# 13. Count by Service

Group data by service.

Conceptually:

```text
summarize count(), by:service.name
```

This produces a result similar to:

```text
Service              Count
---------------------------
payment-service      15,000
order-service        12,500
user-service          8,900
```

---

# 14. Average

Calculate an average value.

Conceptually:

```text
summarize avg(duration)
```

Useful for:

```text
Average response time
Average CPU
Average memory
Average processing time
```

---

# 15. Minimum and Maximum

Use minimum and maximum to understand the range of values.

Conceptually:

```text
summarize min(duration), max(duration)
```

This helps identify:

```text
Lowest observed value
Highest observed value
```

---

# 16. Percentiles

Percentiles are useful for latency analysis.

Common percentiles:

```text
P50
P90
P95
P99
```

Conceptually:

```text
summarize percentile(duration, 95)
```

The exact function signature should be verified against the current Dynatrace DQL documentation when writing production queries.

---

# 17. Grouping

Grouping is essential for SRE analysis.

Example dimensions:

```text
service
host
endpoint
namespace
pod
status
error type
```

Conceptually:

```text
summarize count(), by:service.name
```

This answers:

> How many records does each service have?

---

# 18. sort

Use `sort` to order results.

Conceptually:

```text
sort count desc
```

This can be used to find the largest values first.

Example:

```text
Service A → 10,000
Service B → 8,000
Service C → 5,000
```

---

# 19. limit

Use `limit` to restrict the number of returned records.

Conceptually:

```text
limit 10
```

This is useful when you only want the top results.

Example:

```text
sort count desc
| limit 10
```

means:

> Show the top 10 results.

---

# 20. Top Error-Producing Services

A common SRE question is:

> Which services are generating the most errors?

Conceptual pattern:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
| limit 10
```

The important pattern is:

```text
Filter
   ↓
Group
   ↓
Count
   ↓
Sort
   ↓
Limit
```

---

# 21. Top Error Types

Conceptually:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:error.type
| sort count desc
```

This can reveal the dominant error categories.

---

# 22. Error Analysis by Service

You can combine dimensions.

Conceptually:

```text
summarize count(), by:{service.name, error.type}
```

This helps answer:

> Which error is affecting which service?

---

# 23. Time-Series Analysis

Time-series analysis is essential for monitoring.

Instead of asking:

> How many errors occurred?

you may ask:

> How did errors change over time?

Conceptually:

```text
makeTimeseries count()
```

This can produce a time-based series.

---

# 24. Time Series by Service

Conceptually:

```text
makeTimeseries count(), by:service.name
```

This allows you to compare service behavior over time.

---

# 25. Time-Series Error Investigation

A useful pattern is:

```text
Errors
 │
 ├── 10:00 → Low
 ├── 10:05 → Low
 ├── 10:10 → Low
 ├── 10:15 → High
 └── 10:20 → High
```

Then investigate what changed around 10:15.

---

# 26. Time-Series Latency

Latency can also be analyzed as a time series.

Conceptually:

```text
makeTimeseries avg(duration)
```

You can then observe:

```text
Normal
   ↓
Normal
   ↓
Latency Spike
   ↓
High Latency
```

---

# 27. Filtering by Time

Always ensure the investigation uses the correct timeframe.

For incidents, narrow the analysis to:

```text
Before incident
During incident
After incident
```

This makes investigations much easier.

---

# 28. parse

Logs often contain information inside an unstructured message.

Example:

```text
"payment failed for transaction 12345"
```

Parsing can extract useful fields.

Conceptually:

```text
parse content ...
```

After parsing, you might have:

```text
transaction_id = 12345
```

---

# 29. Why Parsing Matters

Without parsing:

```text
message
```

With parsing:

```text
transaction_id
customer
error_type
endpoint
status
```

Structured fields make analysis significantly easier.

---

# 30. String Operations

String manipulation is useful when fields contain embedded information.

Common operations include:

```text
Contains
Starts with
Ends with
Split
Extract
Replace
Convert case
```

Use these when the data needs normalization before analysis.

---

# 31. Conditional Logic

Conditional expressions can classify records.

Conceptually:

```text
if condition
then value
else value
```

Example classification:

```text
CPU < 70% → Healthy
CPU 70–90% → Warning
CPU > 90% → Critical
```

This can be useful for dashboards and operational analysis.

---

# 32. Creating Severity Categories

Conceptually:

```text
CPU
 │
 ├── <70 → Healthy
 ├── 70–90 → Warning
 └── >90 → Critical
```

The thresholds should be adapted to the organization's actual operational standards.

---

# 33. Conditional Error Classification

You can similarly classify HTTP responses:

```text
2xx → Success
4xx → Client Error
5xx → Server Error
```

This makes aggregation easier.

---

# 34. Lookup

A lookup can enrich telemetry with additional information.

Example:

```text
Service ID
    │
    ▼
Lookup Table
    │
    ▼
Service Owner
```

A result might become:

```text
Service
Owner
Criticality
Team
Environment
```

---

# 35. Why Lookups Are Useful

Telemetry often tells you:

```text
What happened?
```

Enrichment can tell you:

```text
Who owns it?
How important is it?
Which business function is affected?
```

This is extremely useful during incidents.

---

# 36. Join

A join combines related datasets.

Conceptually:

```text
Dataset A
    │
    │ Join
    ▼
Dataset B
```

For example:

```text
Service Telemetry
       +
Service Ownership
```

can provide a richer investigation result.

---

# 37. Join Caution

Joins can produce duplicate records if relationships are not one-to-one.

For example:

```text
1 service
×
10 telemetry records
```

may produce multiple rows.

Always understand the relationship between datasets before joining.

---

# 38. Deduplication

When analyzing event-like data, duplicate records can distort counts.

If the same event appears multiple times:

```text
Actual Events = 100
Observed Records = 150
```

then:

```text
count() = 150
```

would produce an incorrect conclusion.

Always understand whether the dataset contains duplicates.

---

# 39. Calculated Metrics

You can derive operational values from existing fields.

Examples:

```text
Error Rate
Success Rate
Utilization
Growth Rate
Latency Difference
Capacity Remaining
```

---

# 40. Error Rate Pattern

Conceptually:

```text
Error Rate =
Errors / Total Requests × 100
```

Example:

```text
Errors = 500
Requests = 10,000

Error Rate = 5%
```

This is often more useful than error count.

---

# 41. Success Rate

Conceptually:

```text
Success Rate =
Successful Requests / Total Requests × 100
```

If:

```text
Success = 9,950
Total = 10,000
```

then:

```text
Success Rate = 99.5%
```

---

# 42. Availability

Availability can be represented conceptually as:

```text
Availability =
Successful Requests / Total Requests × 100
```

The exact availability definition should match the organization's SLI/SLO definition.

---

# 43. Capacity Remaining

Suppose:

```text
CPU Capacity = 100%
Current CPU = 75%
```

Then conceptually:

```text
Remaining Capacity = 25%
```

This can support capacity analysis.

---

# 44. Growth Analysis

Suppose:

```text
Current Traffic = 1,500 RPS
Previous Traffic = 1,000 RPS
```

Growth can be represented as:

```text
(Current - Previous) / Previous × 100
```

Result:

```text
50% growth
```

This is useful for capacity planning.

---

# 45. Outlier Investigation

A simple workflow:

```text
Aggregate
   ↓
Sort
   ↓
Identify unusual values
   ↓
Drill down
```

Example:

```text
Host 1 → 40%
Host 2 → 42%
Host 3 → 95%
Host 4 → 41%
```

Host 3 is an obvious investigation candidate.

---

# 46. Top N Analysis

Common questions:

```text
Top 10 services by errors
Top 10 endpoints by latency
Top 10 hosts by CPU
Top 10 pods by memory
Top 10 error types
```

Pattern:

```text
summarize ...
| sort ... desc
| limit 10
```

---

# 47. SRE Golden Signals

A DQL investigation should often consider:

```text
Latency
Traffic
Errors
Saturation
```

Use them together rather than looking at only one.

---

# 48. Golden Signal Investigation

```text
Traffic ↑
   │
   ▼
Saturation ↑
   │
   ▼
Latency ↑
   │
   ▼
Errors ↑
```

This can indicate a capacity or workload-related problem.

But alternative explanations must still be considered.

---

# 49. Kubernetes Query Pattern

For Kubernetes investigations, think in terms of:

```text
Cluster
   ↓
Namespace
   ↓
Workload
   ↓
Pod
   ↓
Container
```

Then investigate:

```text
CPU
Memory
Restarts
Network
Logs
Events
```

---

# 50. Pod Restart Investigation

Useful dimensions include:

```text
Pod Name
Namespace
Workload
Container
Restart Count
Reason
Node
```

Then identify the highest restart counts.

---

# 51. Kubernetes Memory Investigation

Workflow:

```text
Memory Usage
    ↓
Pod
    ↓
Container
    ↓
Memory Limit
    ↓
Restart
    ↓
OOM Evidence
```

This can help identify memory-related failures.

---

# 52. Kubernetes CPU Investigation

Workflow:

```text
CPU Usage
   ↓
Pod
   ↓
Deployment
   ↓
Node
```

Then compare:

```text
Requests
Limits
Actual Usage
```

to understand resource pressure.

---

# 53. Dependency Investigation

For service dependencies, analyze:

```text
Dependency
Request Count
Latency
Errors
Timeouts
```

A dependency that shows:

```text
Latency ↑
Errors ↑
```

at the same time as upstream failures deserves investigation.

---

# 54. Deployment Investigation

Always ask:

```text
Was there a recent deployment?
```

Then compare:

```text
Before Deployment
vs
After Deployment
```

Signals:

```text
Latency
Errors
CPU
Memory
Traffic
Dependency behavior
```

---

# 55. Incident Timeline

DQL can support reconstruction of:

```text
Change
   ↓
Metric Change
   ↓
Error Change
   ↓
User Impact
```

This is useful during post-incident analysis.

---

# 56. DQL and Dashboards

DQL queries can support dashboard visualizations.

Useful dashboard panels include:

```text
Request Rate
Error Rate
Latency
CPU
Memory
Top Errors
Top Services
Top Endpoints
Kubernetes Restarts
Dependency Latency
```

---

# 57. DQL and Alerting

DQL-based analysis can help identify conditions such as:

```text
High Error Rate
High Latency
Abnormal Traffic
Resource Saturation
Repeated Pod Restarts
Dependency Failure
```

The exact alerting mechanism should be designed according to the monitoring architecture.

---

# 58. DQL and Predictive Monitoring

DQL can provide historical data for:

```text
Trend Analysis
Baseline Creation
Anomaly Investigation
Forecasting
Capacity Planning
```

Conceptually:

```text
Historical Telemetry
        ↓
       DQL
        ↓
 Time-Series Data
        ↓
Trend / Baseline
        ↓
Prediction
```

---

# 59. Query Optimization

When writing production queries:

### Filter early

Reduce irrelevant data as early as practical.

### Select necessary fields

Avoid carrying unnecessary fields through every stage.

### Aggregate appropriately

Do not process millions of individual records if the question only requires a count by service.

### Narrow the timeframe

Incident investigations should normally use a focused time window.

### Avoid unnecessary joins

Joins can increase complexity and may produce unexpected duplication.

---

# 60. Readability

A query should be understandable by another engineer.

Bad:

```text
fetch ...
| ...
| ...
| ...
| ...
```

Better:

```text
fetch ...
| filter ...
| fields ...
| summarize ...
| sort ...
```

Readable queries are easier to troubleshoot and maintain.

---

# 61. Reusable Query Templates

Maintain reusable queries for common tasks.

Examples:

```text
Top Errors
High Latency
Traffic Trend
CPU Hotspots
Memory Hotspots
Pod Restarts
Deployment Analysis
Dependency Errors
Capacity Trend
```

This reduces investigation time during incidents.

---

# 62. Common Investigation Templates

## Top Errors

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
| limit 10
```

---

## Error Trend

```text
fetch logs
| filter loglevel == "ERROR"
| makeTimeseries count()
```

---

## Errors by Service

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
```

---

## Latency Trend

Conceptual pattern:

```text
fetch spans
| makeTimeseries avg(duration)
```

---

## Latency by Service

```text
fetch spans
| summarize avg(duration), by:service.name
| sort avg(duration) desc
```

---

## Top Slow Services

Conceptual pattern:

```text
fetch spans
| summarize avg(duration), by:service.name
| sort avg(duration) desc
| limit 10
```

---

# 63. Query Development Process

When creating a new query:

```text
1. Define the question
2. Identify the data source
3. Start with a small query
4. Add filters
5. Inspect fields
6. Add aggregation
7. Add grouping
8. Sort results
9. Validate the result
10. Optimize the query
```

---

# 64. Validate the Data

Before trusting a result, ask:

```text
Is the timeframe correct?
Is the environment correct?
Are records duplicated?
Are important fields missing?
Are filters correct?
Is the aggregation correct?
```

A technically valid query can still produce an operationally incorrect conclusion.

---

# 65. DQL Troubleshooting

If a query returns no results:

```text
Check timeframe
      ↓
Check data source
      ↓
Check field name
      ↓
Check filter
      ↓
Check environment
      ↓
Check telemetry availability
```

---

# 66. If Results Are Too Large

Try:

```text
Narrow timeframe
Filter earlier
Select fewer fields
Aggregate data
Limit results
```

---

# 67. If Results Look Incorrect

Check:

```text
Aggregation
Grouping
Duplicate records
Join behavior
Field types
Null values
Filter logic
```

---

# 68. Null and Missing Data

A field may not exist on every record.

Example:

```text
Record A → service.name exists
Record B → service.name missing
Record C → service.name exists
```

Always consider missing fields when interpreting results.

---

# 69. Field Names

Dynatrace telemetry may contain many fields.

Before writing a complex query, understand:

```text
Field name
Field type
Field meaning
Example values
```

Do not assume that two datasets use identical field names.

---

# 70. DQL and Data Modeling

Understanding the underlying data model is just as important as knowing syntax.

Think:

```text
What data do I have?
        ↓
What does each record represent?
        ↓
Which fields identify the entity?
        ↓
Which fields contain the measurement?
        ↓
How should records be grouped?
```

---

# 71. DQL Mental Model

Remember:

```text
FETCH
  ↓
FILTER
  ↓
TRANSFORM
  ↓
AGGREGATE
  ↓
SORT
  ↓
LIMIT
```

Not every query requires every stage.

---

# 72. SRE Mental Model

For incidents:

```text
SYMPTOM
   ↓
SCOPE
   ↓
SIGNAL
   ↓
CORRELATION
   ↓
HYPOTHESIS
   ↓
VALIDATION
   ↓
ROOT CAUSE
   ↓
ACTION
```

DQL primarily supports the evidence-gathering and analysis stages.

---

# 73. DQL + Observability

```text
Logs
 │
 ├─────────┐
 ▼         │
DQL        │
 ▲         │
 │         │
Metrics ───┤
           │
Traces ────┘
     │
     ▼
Operational Understanding
```

---

# 74. DQL + Predictive Monitoring

```text
Historical Data
      │
      ▼
     DQL
      │
      ▼
Trend Analysis
      │
      ├── Baseline
      ├── Seasonality
      └── Growth
      │
      ▼
Prediction
      │
      ▼
Risk
      │
      ▼
Proactive Action
```

---

# 75. Most Important DQL Concepts

Before moving forward, make sure you understand:

```text
✓ fetch
✓ filter
✓ fields
✓ fieldsAdd
✓ summarize
✓ Aggregations
✓ Grouping
✓ sort
✓ limit
✓ makeTimeseries
✓ Parsing
✓ String operations
✓ Conditional expressions
✓ Lookup
✓ Join
✓ Time-based analysis
✓ Error-rate calculation
✓ Percentile analysis
✓ Outlier analysis
✓ Query optimization
```

---

# 76. SRE Use Cases to Practice

You should be able to design a DQL investigation for:

### Use Case 1

Find the services generating the most errors.

### Use Case 2

Find the slowest endpoints.

### Use Case 3

Find hosts with unusually high CPU.

### Use Case 4

Find pods with repeated restarts.

### Use Case 5

Identify the busiest services.

### Use Case 6

Investigate a latency spike.

### Use Case 7

Investigate a deployment regression.

### Use Case 8

Find a failing dependency.

### Use Case 9

Analyze historical resource growth.

### Use Case 10

Identify potential future capacity problems.

---

# 77. Final DQL Workflow

A mature DQL investigation looks like:

```text
                User Problem
                     │
                     ▼
              Define Question
                     │
                     ▼
               Select Data
                     │
                     ▼
                  Filter
                     │
                     ▼
                Aggregate
                     │
                     ▼
                 Compare
                     │
                     ▼
                Correlate
                     │
                     ▼
              Drill Down
                     │
                     ▼
              Validate RCA
                     │
                     ▼
             Take Corrective Action
```

---

# Key Takeaways

* DQL is both a query language and an investigation tool.
* Learn patterns rather than memorizing isolated queries.
* Start every query with a clear operational question.
* Use `filter` to narrow the investigation.
* Use `summarize` for aggregation.
* Use grouping to compare entities.
* Use time series to understand behavior over time.
* Use parsing to convert unstructured data into useful fields.
* Use lookups for contextual enrichment.
* Use joins carefully.
* Use calculated values for SRE indicators such as error rate and utilization.
* Always validate the result before drawing conclusions.
* DQL can support incident response, RCA, dashboards, alert investigation, capacity planning, and predictive monitoring.
* The most valuable skill is not simply knowing DQL syntax—it is knowing **what question to ask of the telemetry**.
