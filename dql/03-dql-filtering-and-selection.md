# DQL Filtering and Selection

## Overview

Filtering and selecting data are two of the most important skills in DQL.

When Dynatrace contains thousands or millions of records, you usually do not want to analyze everything. You want to narrow the data down to the records and fields relevant to your question.

For example:

```text
All Logs
   │
   ▼
Filter ERROR logs
   │
   ▼
Select important fields
   │
   ▼
Sort results
   │
   ▼
Analyze
```

DQL provides commands and operators that allow you to control exactly which records and fields are returned.

Dynatrace's current DQL documentation describes queries as pipeline-based data flows, where each command passes its result to the next command through the `|` operator.

---

# Learning Objectives

After completing this document, you should understand:

* `filter`
* `search`
* `fields`
* `fieldsKeep`
* `fieldsRemove`
* `fieldsAdd`
* `sort`
* `limit`
* Comparison operators
* Logical operators
* String matching
* Multiple conditions
* Filtering by time
* Filtering by entities
* Efficient filtering
* Common filtering mistakes

---

# Why Filtering Is Important

Consider an environment containing:

```text
10,000,000 Logs
500 Services
200 Hosts
50 Kubernetes Namespaces
```

If an incident affects only one service, analyzing every record is inefficient.

Instead:

```text
10,000,000 Logs
       │
       ▼
Payment Service
       │
       ▼
ERROR Logs
       │
       ▼
Last 30 Minutes
       │
       ▼
Relevant Results
```

Filtering reduces the amount of information you need to analyze.

It can also reduce the amount of data processed by the query. Dynatrace recommends filtering early when possible.

---

# The `filter` Command

The `filter` command keeps records that satisfy a condition.

Basic syntax:

```dql
fetch logs
| filter condition
```

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
```

Conceptually:

```text
All Logs
   │
   ▼
loglevel == ERROR
   │
   ▼
ERROR Logs
```

Dynatrace documents `filter` as the command used to reduce a record set to records matching a specified condition.

---

# Equality Operator

The equality operator is:

```text
==
```

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
```

This means:

> Keep records where `loglevel` exactly equals `"ERROR"`.

---

# Not Equal

The not-equal operator is:

```text
!=
```

Example:

```dql
fetch logs
| filter loglevel != "INFO"
```

This excludes records where the log level is exactly `INFO`.

---

# Numeric Comparisons

DQL supports comparison operators such as:

```text
<
<=
>
>=
==
!=
```

These are useful with numeric fields.

Example:

```dql
fetch logs
| filter response_time > 1000
```

Conceptually:

```text
Response Time
     │
     ├── 500 ms   ❌
     ├── 800 ms   ❌
     ├── 1200 ms  ✅
     └── 2000 ms  ✅
```

DQL's comparison operators include equality, inequality, and greater-than/less-than comparisons.

---

# Logical AND

`and` requires both conditions to be true.

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
    and service.name == "payment-service"
```

Meaning:

```text
ERROR
  AND
Payment Service
```

Only records satisfying both conditions are retained.

---

# Logical OR

`or` allows either condition to be true.

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
    or loglevel == "WARN"
```

Meaning:

```text
ERROR
   OR
WARN
```

Both types of records are returned.

---

# Logical NOT

`not` negates a condition.

Example:

```dql
fetch logs
| filter not loglevel == "INFO"
```

This excludes records where the log level is `INFO`.

DQL supports `not`, `and`, `or`, and `xor` as logical operators.

---

# Combining Conditions

Multiple conditions can be combined.

Example:

```dql
fetch logs
| filter loglevel == "ERROR"
    and service.name == "payment-service"
    and response_time > 1000
```

This asks for:

```text
ERROR
+
Payment Service
+
Response Time > 1000 ms
```

This is useful during incident investigations.

---

# Parentheses

Parentheses help control the logic of complex conditions.

Example:

```dql
fetch logs
| filter service.name == "payment-service"
    and (loglevel == "ERROR" or loglevel == "WARN")
```

Meaning:

```text
Payment Service
       AND
(ERROR OR WARN)
```

Without grouping, complex expressions can be difficult to reason about.

DQL operator precedence determines how expressions are evaluated, so parentheses are useful when you want the intended logic to be explicit.

---

# The `in` Operator

`in` is useful when a field may contain one of several values.

Conceptually:

```dql
fetch logs
| filter service.name in [
    "payment-service",
    "order-service",
    "checkout-service"
]
```

This allows multiple acceptable values to be specified.

DQL also supports `in` for comparison with values returned by a subquery.

---

# String Matching

Sometimes you do not know the exact value of a field.

For example:

```text
payment-service-v1
payment-service-v2
payment-service-canary
```

An exact comparison may not be enough.

DQL provides search capabilities for partial matching.

---

# The `~` Search Operator

The `~` operator performs token-based string matching.

Example:

```dql
fetch logs
| filter content ~ "error"
```

This searches the `content` field for the term `error`.

The search is case-insensitive and supports wildcard patterns.

---

# Exact Comparison vs Search

This distinction is important.

### Exact comparison

```dql
filter service.name == "payment-service"
```

Use this when you know the exact value.

### Search

```dql
filter service.name ~ "payment"
```

Use this when you are searching for a matching term.

Dynatrace recommends using `==` or `!=` when the exact value is known and `~` when the value is only partially known.

---

# Wildcards

The `~` operator supports wildcard patterns.

Example:

```dql
fetch logs
| filter service.name ~ "payment*"
```

This can match values beginning with the `payment` pattern.

Wildcards are useful when naming conventions create multiple related values.

---

# The `search` Command

DQL also provides a `search` command.

Example:

```dql
fetch logs
| search "timeout"
```

This searches for the term across the available record data.

You can also specify a field:

```dql
fetch logs
| search content ~ "timeout"
```

The second form is more targeted because the search is restricted to a known field.

Dynatrace recommends specifying the field when you know where the relevant text is located.

---

# `filter` vs `search`

A useful mental model is:

```text
filter
   │
   └── General Boolean filtering

search
   │
   └── Text/search-oriented matching
```

Use `filter` for conditions such as:

```text
status == 500
CPU > 80
service.name == "checkout"
```

Use `search` for text-oriented searches such as:

```text
timeout
connection refused
database unavailable
```

---

# Filtering Log Messages

Suppose logs contain:

```text
Database connection failed
Payment completed
Database timeout
User authenticated
```

You can search for database-related failures.

Example:

```dql
fetch logs
| filter content ~ "database"
```

Or use a phrase search where appropriate.

---

# Filtering by Service

Example:

```dql
fetch logs
| filter service.name == "payment-service"
```

This limits the investigation to one service.

This is particularly useful in microservice environments.

---

# Filtering by Kubernetes Namespace

If the relevant field exists in the records:

```dql
fetch logs
| filter k8s.namespace.name == "production"
```

This focuses the query on the production namespace.

---

# Filtering by Kubernetes Pod

Example:

```dql
fetch logs
| filter k8s.pod.name == "payment-api"
```

This can help investigate a specific workload.

Always verify the actual field names present in your environment before using them.

---

# Filtering by Host

Example:

```dql
fetch logs
| filter host.name == "server-01"
```

This isolates telemetry associated with a particular host.

---

# Filtering by HTTP Status

If the field is available:

```dql
fetch logs
| filter http.response.status_code >= 500
```

This can identify server-side HTTP errors.

---

# Filtering by Response Time

Suppose slow requests are defined as requests above one second.

Conceptually:

```dql
fetch logs
| filter response_time > 1s
```

The exact field and type depend on the data being queried.

The important idea is:

```text
Response Time > Threshold
```

---

# Filtering by Time

Observability investigations almost always involve a time window.

For example:

```text
Incident:
10:00 AM – 10:30 AM
```

A query can be restricted to the relevant timeframe using the `fetch` command's time parameters.

Example:

```dql
fetch logs, from:now() - 30m
```

This retrieves logs from the recent 30-minute period.

Dynatrace's DQL documentation provides `from`/timeframe parameters for restricting the queried data.

---

# Why Time Filtering Matters

Without a suitable time window:

```text
Historical Data
       │
       ▼
Large Dataset
       │
       ▼
More Processing
       │
       ▼
Harder Investigation
```

With a focused time window:

```text
Relevant Time
       │
       ▼
Relevant Data
       │
       ▼
Faster Analysis
```

Time filtering is especially important during incidents.

---

# The `fields` Command

`fields` controls which fields appear in the output.

Example:

```dql
fetch logs
| fields timestamp, loglevel, service.name, content
```

Instead of displaying every available field, the query focuses on the selected fields.

Dynatrace documents `fields` as the command for keeping specified fields and controlling their output order.

---

# Why Select Fields?

A record can contain many fields.

For example:

```text
timestamp
loglevel
content
host.name
service.name
k8s.namespace.name
k8s.pod.name
trace.id
...
```

You may only need:

```text
timestamp
service.name
loglevel
content
```

This makes results easier to read.

---

# `fieldsKeep`

`fieldsKeep` can be used when you want to retain specific fields while working through a pipeline.

Conceptually:

```dql
fetch logs
| fieldsKeep timestamp, service.name, content
```

The result focuses on the selected fields.

---

# `fieldsRemove`

`fieldsRemove` removes fields you do not need.

Conceptually:

```dql
fetch logs
| fieldsRemove unnecessary_field
```

This can be useful when you want to remove sensitive, unnecessary, or bulky fields from the working dataset.

---

# `fieldsAdd`

`fieldsAdd` creates an additional field based on an expression.

For example:

```dql
...
| fieldsAdd calculated_value = some_numeric_field * 2
```

This is useful for:

```text
Derived Metrics
Calculations
Transformations
Analysis
```

Dynatrace's DQL reference documents `fieldsAdd` as a way to add calculated fields.

---

# Sorting Results

The `sort` command orders records.

Example:

```dql
fetch logs
| sort timestamp desc
```

This places newer timestamps first.

Use:

```text
asc
```

for ascending order.

Use:

```text
desc
```

for descending order.

DQL sorts ascending by default unless another order is specified.

---

# Sorting by Multiple Fields

You can sort using multiple fields.

Conceptually:

```dql
...
| sort service.name asc, timestamp desc
```

This first sorts by service and then by timestamp within the service.

---

# Limiting Results

The `limit` command restricts how many records are returned.

Example:

```dql
fetch logs
| limit 10
```

This returns up to 10 records.

Dynatrace notes that a default result limit is applied when no explicit `limit` is specified, and changing the limit can affect query execution and data-unit consumption.

---

# `limit` and Investigation

For initial exploration:

```dql
fetch logs
| limit 20
```

is useful because you can inspect the structure without returning a huge result set.

However, do not use `limit` before an aggregation when you need an accurate aggregate across the complete dataset.

Dynatrace specifically recommends avoiding `limit` before aggregation unless intentionally desired.

---

# Combining Selection Commands

A practical query can combine multiple operations:

```dql
fetch logs
| filter loglevel == "ERROR"
| fields timestamp, service.name, content
| sort timestamp desc
| limit 20
```

Read it as:

```text
Fetch logs
   ↓
Keep ERROR logs
   ↓
Keep important fields
   ↓
Newest first
   ↓
Show 20
```

This is the basic DQL pipeline mindset.

---

# Complete Example: Incident Investigation

Suppose the payment service started failing.

You want to investigate recent errors.

Start:

```dql
fetch logs, from:now() - 30m
```

Then filter:

```dql
fetch logs, from:now() - 30m
| filter service.name == "payment-service"
```

Then errors:

```dql
fetch logs, from:now() - 30m
| filter service.name == "payment-service"
| filter loglevel == "ERROR"
```

Select useful fields:

```dql
fetch logs, from:now() - 30m
| filter service.name == "payment-service"
| filter loglevel == "ERROR"
| fields timestamp, service.name, content
```

Sort:

```dql
fetch logs, from:now() - 30m
| filter service.name == "payment-service"
| filter loglevel == "ERROR"
| fields timestamp, service.name, content
| sort timestamp desc
```

Limit:

```dql
fetch logs, from:now() - 30m
| filter service.name == "payment-service"
| filter loglevel == "ERROR"
| fields timestamp, service.name, content
| sort timestamp desc
| limit 20
```

This progression is much easier to understand than starting with a complex query.

---

# Filtering Strategy

A good beginner strategy is:

```text
1. Identify the data source
        ↓
2. Restrict the timeframe
        ↓
3. Apply broad filters
        ↓
4. Apply specific filters
        ↓
5. Select useful fields
        ↓
6. Transform if necessary
        ↓
7. Sort
        ↓
8. Limit
```

For aggregation queries, aggregate before applying a final result limit.

---

# Performance Best Practices

## Filter Early

Reduce the dataset as early as practical.

```dql
fetch logs
| filter service.name == "payment-service"
```

This is generally preferable to processing unnecessary records first.

---

## Use Targeted Search

If you know which field contains the text:

```dql
search content ~ "timeout"
```

is more targeted than searching all fields.

---

## Select Necessary Fields

Use field-selection commands when appropriate.

```dql
| fields timestamp, service.name, content
```

This keeps the working dataset focused.

---

## Avoid Unnecessary Transformations

Do not add complex transformations when a direct filter can answer the question.

Prefer:

```dql
filter k8s.namespace.name ~ "production"
```

over unnecessary transformations of the same field when possible. Dynatrace recommends direct field filtering where appropriate.

---

# Common Beginner Mistakes

## Mistake 1: Using `=` Instead of `==`

For DQL equality comparisons, use:

```dql
==
```

Example:

```dql
filter loglevel == "ERROR"
```

---

## Mistake 2: Confusing Exact Matching and Search

Use:

```dql
==
```

when the value is known exactly.

Use:

```dql
~
```

when searching for a matching term or pattern.

---

## Mistake 3: Filtering After Unnecessary Processing

Try to narrow the data early.

---

## Mistake 4: Limiting Before Aggregation

This can produce incorrect aggregate results when the intention was to aggregate the full dataset.

---

## Mistake 5: Assuming Field Names

Never assume a field exists.

Inspect your actual data first.

---

## Mistake 6: Ignoring Time

Always consider whether the query needs a specific timeframe.

---

# SRE Example

Suppose users report slow checkout.

An SRE may investigate:

```text
Checkout Service
       │
       ▼
Slow Requests
       │
       ▼
High Latency
       │
       ▼
Database Calls
       │
       ▼
Database Errors
```

DQL filtering can progressively isolate the affected records.

---

# Capacity Planning Example

Suppose CPU utilization is increasing.

The investigation may follow:

```text
All Metrics
     │
     ▼
Production
     │
     ▼
Specific Cluster
     │
     ▼
Specific Namespace
     │
     ▼
Specific Workload
```

Filtering makes the analysis progressively more focused.

---

# Interview Questions

### What does `filter` do?

It keeps only records satisfying a specified Boolean condition.

### What is the difference between `==` and `~`?

`==` performs an equality comparison, while `~` performs token-based search matching and can support wildcards.

### What does `fields` do?

It selects the fields included in the query result and controls their order.

### What does `sort` do?

It orders records according to one or more fields.

### What does `limit` do?

It restricts the number of returned records.

### Why should filtering generally happen early?

It reduces the amount of data that subsequent operations need to process.

### Why should `limit` generally not be used before aggregation?

Because it can restrict the records being aggregated and therefore produce an incomplete result.

---

# Key Takeaways

* `filter` is one of the most important DQL commands.
* Use `==` for known exact values.
* Use `!=` to exclude exact values.
* Use `<`, `<=`, `>`, and `>=` for numeric comparisons.
* Use `and`, `or`, and `not` to combine conditions.
* Use `in` when comparing against multiple possible values.
* Use `~` for token-based string matching and wildcard searches.
* `search` is useful for text-oriented searches.
* `fields` controls which fields are returned.
* `fieldsAdd` creates calculated fields.
* `sort` orders results.
* `limit` restricts result size.
* Time filtering is essential for incident analysis.
* Filter early when possible.
* Inspect your actual data before assuming field names.
* Do not limit records before aggregation unless you intentionally want to aggregate only those records.
