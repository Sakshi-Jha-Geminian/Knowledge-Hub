# DQL Functions and Expressions

## Overview

DQL commands control the flow of a query, while **functions and expressions** allow you to calculate, transform, and derive information from the data.

For example, an SRE may have a response-time field and want to calculate:

```text
Average latency
Maximum latency
Error rate
Request duration
Percentage of failed requests
```

Instead of storing every possible value as a separate field, DQL can calculate values during query execution.

A useful mental model is:

```text
Raw Data
   │
   ▼
Fields
   │
   ▼
Expressions / Functions
   │
   ▼
Calculated Values
   │
   ▼
Analysis
```

---

# Learning Objectives

After completing this document, you should understand:

* What DQL functions are
* What expressions are
* Arithmetic expressions
* Boolean expressions
* String expressions
* Conditional expressions
* Aggregation functions
* Conversion functions
* Null handling
* Creating calculated fields
* Reusing expressions
* Functions in filters
* Functions in `summarize`
* Functions in time-series analysis
* Common mistakes

---

# What Is a DQL Function?

A function performs a specific operation on a value or set of values.

Conceptually:

```text
function(input)
      │
      ▼
  result
```

For example:

```text
count()
avg(value)
max(value)
min(value)
sum(value)
```

Functions are building blocks used throughout DQL.

---

# What Is an Expression?

An expression is something that DQL evaluates to produce a value.

For example:

```text
response_time > 1000
```

is a Boolean expression.

Another example:

```text
cpu_usage + memory_usage
```

is an arithmetic expression.

A useful distinction is:

```text
Function
→ Performs an operation

Expression
→ Produces a value from fields, constants, operators, and functions
```

---

# Constants

A constant is a fixed value.

Examples:

```text
100
"ERROR"
true
false
```

You can use constants inside expressions.

Example:

```dql
filter response_time > 1000
```

Here:

```text
1000
```

is a constant.

---

# Arithmetic Expressions

DQL can perform arithmetic operations.

Common operators include:

```text
+
-
*
/
%
```

Example:

```dql
fieldsAdd total = requests + errors
```

Conceptually:

```text
requests = 1000
errors   = 50

total = 1050
```

---

# Multiplication

Example:

```dql
fieldsAdd bytes_kb = bytes / 1024
```

This can convert bytes into kilobytes.

Always verify the actual unit and data type before performing unit conversions.

---

# Division

Division is useful for calculating ratios.

For example:

```text
Error Rate =
Errors / Total Requests
```

Conceptually:

```dql
fieldsAdd error_rate = errors / requests
```

If:

```text
errors = 50
requests = 1000
```

then:

```text
error_rate = 0.05
```

or:

```text
5%
```

depending on how the result is presented.

---

# Percentage Calculations

A percentage can be calculated conceptually as:

```text
percentage =
(value / total) × 100
```

Example:

```dql
fieldsAdd error_percentage = (errors / requests) * 100
```

Result:

```text
Errors = 50
Requests = 1000

Error Percentage = 5
```

---

# Boolean Expressions

Boolean expressions evaluate to:

```text
true
```

or:

```text
false
```

Examples:

```dql
response_time > 1000
```

```dql
loglevel == "ERROR"
```

```dql
status_code >= 500
```

These expressions are particularly useful with `filter`.

---

# Boolean Operators

Important Boolean operators include:

```text
and
or
not
```

Example:

```dql
filter status_code >= 500
    and service.name == "payment-service"
```

This means:

```text
Server Error
     AND
Payment Service
```

---

# Conditional Expressions

Sometimes you want to assign a value based on a condition.

Conceptually:

```text
IF condition
THEN value A
ELSE value B
```

This is useful for classification.

For example:

```text
response_time < 500 ms
      │
      ├── Yes → Fast
      └── No  → Slow
```

DQL provides conditional functions for this type of logic.

---

# `if`

The `if` function can be used to return different values depending on a condition.

Conceptually:

```dql
fieldsAdd category =
    if(response_time > 1000, "Slow", "Normal")
```

This creates a classification.

Example:

```text
500 ms  → Normal
800 ms  → Normal
1200 ms → Slow
2000 ms → Slow
```

Always check the current DQL reference for the exact function signature supported by your environment.

---

# Why Conditional Logic Is Useful

Conditional logic allows raw telemetry to become operational information.

For example:

```text
Raw Latency
     │
     ▼
Classification
     │
     ├── Normal
     ├── Warning
     └── Critical
```

This can make dashboards and investigations easier to interpret.

---

# Creating Calculated Fields

One of the most useful patterns is creating a new field from existing fields.

Conceptually:

```dql
fieldsAdd calculated_field = expression
```

Example:

```dql
fieldsAdd total = requests + errors
```

The new field exists in the query result and can be used by later pipeline stages.

---

# Calculated Fields in SRE

Suppose you have:

```text
successful_requests
failed_requests
```

You could derive:

```text
total_requests
```

Conceptually:

```dql
fieldsAdd total_requests =
    successful_requests + failed_requests
```

Then the calculated value can be used in further analysis.

---

# Functions Inside Filters

Functions and expressions can also be used when filtering.

For example, you may need to filter based on a calculated condition.

Conceptually:

```dql
filter response_time / 1000 > 1
```

This means:

> Keep records where the calculated value exceeds one second.

Be careful with units and data types.

---

# Functions Inside `summarize`

Aggregation functions are especially important in `summarize`.

Examples:

```dql
summarize count()
```

```dql
summarize avg(response_time)
```

```dql
summarize max(response_time)
```

```dql
summarize min(response_time)
```

```dql
summarize sum(bytes)
```

These convert many records into summarized information.

---

# `count()`

`count()` counts records.

Example:

```dql
fetch logs
| summarize count()
```

This answers:

> How many records are there?

---

# `avg()`

`avg()` calculates the average of a numeric field.

Example:

```dql
fetch ...
| summarize avg(response_time)
```

This answers:

> What is the average response time?

---

# `sum()`

`sum()` adds values.

Example:

```dql
fetch ...
| summarize sum(bytes)
```

This answers:

> How much total data was processed?

---

# `min()` and `max()`

These identify the lowest and highest values.

Example:

```dql
fetch ...
| summarize
    min(response_time),
    max(response_time)
```

This gives a quick view of the range of observed values.

---

# Conditional Counting

Conditional aggregation is useful when you need multiple categories.

Conceptually:

```dql
summarize
    total = count(),
    errors = countIf(status_code >= 500)
```

This can produce:

```text
Total Requests = 100,000
Errors         = 2,000
```

From these values, an error percentage can be calculated.

---

# Calculating Error Rate

Conceptually:

```text
error rate =
errors / total
```

For example:

```text
Total = 100,000
Errors = 2,000

Error Rate = 2%
```

This type of calculation is fundamental to SRE.

---

# Expressions with Multiple Fields

You can combine fields.

Example:

```dql
fieldsAdd total =
    successful_requests + failed_requests
```

Or:

```dql
fieldsAdd utilization_ratio =
    used_capacity / total_capacity
```

These derived values can help turn raw telemetry into operational metrics.

---

# Handling Null Values

Real observability data is not always complete.

A record may contain:

```text
response_time = 250
```

while another record may have:

```text
response_time = null
```

You must consider missing values when performing calculations.

Otherwise, your result may not behave as expected.

---

# Why Null Handling Matters

Suppose:

```text
Requests:
100
200
null
400
```

An average calculation must account for the missing value correctly.

Null values can also affect:

```text
Arithmetic
Comparisons
Aggregations
Grouping
Conditional logic
```

DQL provides functions and operators for working with null values.

---

# `isNull`

A null check can be used to determine whether a value is missing.

Conceptually:

```dql
isNull(field)
```

Example:

```dql
filter isNull(response_time)
```

This can help find records where a field is missing.

---

# `isNotNull`

The opposite concept is checking whether a field contains a value.

Conceptually:

```dql
isNotNull(response_time)
```

This can be useful when you only want records with usable data.

---

# Null Handling During Analysis

A useful pattern is:

```text
Raw Data
   │
   ▼
Check Missing Values
   │
   ▼
Filter / Handle Nulls
   │
   ▼
Calculate
   │
   ▼
Analyze
```

This is especially important when building production dashboards.

---

# String Functions

Observability data often contains strings such as:

```text
Service names
Host names
URLs
Log messages
Error messages
Environment names
```

String functions can help transform or inspect these values.

Common string-related operations include:

```text
contains
matches
lowercase/uppercase transformations
substring operations
concatenation
```

The exact function names and signatures should be checked against the current DQL function reference.

---

# String Matching

For simple token-based matching, DQL's `~` operator is often useful.

Example:

```dql
filter content ~ "timeout"
```

This is generally preferable to unnecessarily transforming the string first.

---

# String Classification

Suppose log messages contain:

```text
database timeout
connection refused
authentication failed
payment completed
```

You can conceptually classify them:

```text
database timeout
      ↓
Database Issue

connection refused
      ↓
Network / Dependency Issue
```

This can be useful for incident analysis.

---

# Date and Time Functions

Time-related functions are extremely important in DQL.

Examples of concepts you may work with include:

```text
Current time
Time differences
Time conversion
Time intervals
Time bucketing
```

For example:

```dql
now()
```

represents the current time.

---

# `now()`

`now()` is commonly used when defining relative time windows.

For example:

```dql
fetch logs, from:now() - 30m
```

Conceptually:

```text
Current Time
     │
     └── minus 30 minutes
              │
              ▼
        Query Start Time
```

---

# Duration Expressions

Durations can be expressed using time units such as:

```text
ms
s
m
h
d
```

Examples:

```text
500ms
30m
2h
7d
```

These are useful when defining time windows and comparing durations.

---

# Time Difference

Suppose you have:

```text
request_start
request_end
```

A duration can be derived conceptually as:

```text
request_end - request_start
```

This can help calculate request duration when the appropriate timestamp fields are available.

---

# Functions and Time-Series

Functions are also useful when constructing time-series analysis.

Conceptually:

```text
Raw Records
     │
     ▼
Time Buckets
     │
     ▼
Aggregation Function
     │
     ▼
Time Series
```

For example:

```text
Average CPU
per 5-minute interval
```

or:

```text
Maximum latency
per 1-minute interval
```

---

# Functions and Predictive Monitoring

Predictive monitoring depends heavily on derived values.

For example:

```text
Raw CPU
   │
   ▼
Average CPU per interval
   │
   ▼
Historical Trend
   │
   ▼
Baseline
   │
   ▼
Forecast
```

Functions and expressions help prepare telemetry for this analysis.

---

# Functions and Capacity Planning

Suppose you have:

```text
Used Capacity
Total Capacity
```

You can derive:

```text
Utilization =
Used Capacity / Total Capacity
```

Then:

```text
Utilization
     │
     ▼
Historical Time Series
     │
     ▼
Growth Trend
     │
     ▼
Forecast
```

This is the foundation of capacity forecasting.

---

# Expressions for SRE

An SRE commonly needs derived operational values such as:

```text
Error Rate
Success Rate
Average Latency
Maximum Latency
Request Rate
Resource Utilization
Traffic Volume
```

Many of these are calculated from raw telemetry.

---

# Example: Success Rate

Suppose:

```text
total_requests = 10000
failed_requests = 100
```

Then:

```text
successful_requests = 9900
```

And:

```text
success_rate = 9900 / 10000
             = 99%
```

This is much more meaningful than simply knowing that 100 requests failed.

---

# Example: Resource Utilization

Suppose:

```text
used_cpu = 7 cores
available_cpu = 8 cores
```

Then:

```text
utilization = 7 / 8
            = 87.5%
```

This can be compared against an operational threshold.

---

# Example: Traffic Rate

Suppose:

```text
requests = 60,000
duration = 60 seconds
```

Then:

```text
requests_per_second =
60,000 / 60

= 1,000 RPS
```

This is useful for:

```text
Capacity Planning
Autoscaling
Performance Analysis
Load Testing
```

---

# Expressions and Thresholds

Expressions can help identify threshold violations.

Example:

```text
CPU > 80%
```

or:

```text
Error Rate > 1%
```

or:

```text
Latency > 500 ms
```

This leads to a simple operational model:

```text
Measurement
    │
    ▼
Expression
    │
    ▼
Threshold
    │
    ▼
Normal / Abnormal
```

---

# Combining Multiple Conditions

You can combine expressions.

Example:

```dql
filter cpu > 80
    and memory > 85
```

This means:

```text
CPU is high
     AND
Memory is high
```

Another example:

```dql
filter status_code >= 500
    or response_time > 2000
```

This identifies records with either a server error or very high latency.

---

# Nested Expressions

Complex analysis may require expressions inside expressions.

Conceptually:

```text
Percentage =
    errors /
    (successful + errors)
```

This is a nested expression because one calculation depends on another.

Keep complex expressions readable.

---

# Readability Matters

Instead of writing one extremely complicated expression, consider creating intermediate fields.

For example:

```dql
fieldsAdd total = successful + failed
| fieldsAdd error_rate = failed / total
```

This is easier to understand than putting the entire calculation into one expression.

---

# Functions and Query Pipelines

Functions fit naturally into the DQL pipeline.

Example:

```text
FETCH
  ↓
FILTER
  ↓
CALCULATE
  ↓
SUMMARIZE
  ↓
SORT
```

Conceptually:

```dql
fetch ...
| filter ...
| fieldsAdd calculated_value = ...
| summarize avg(calculated_value), by:{...}
| sort ...
```

Each stage has a specific responsibility.

---

# A Practical SRE Example

Question:

> Which services have an error rate above 2%?

Conceptually:

```text
Requests
   │
   ▼
Group by Service
   │
   ▼
Count Total
   │
   ▼
Count Errors
   │
   ▼
Calculate Error Rate
   │
   ▼
Filter > 2%
```

This is an excellent example of combining:

```text
Filtering
Aggregation
Grouping
Functions
Expressions
```

---

# A Practical Capacity Example

Question:

> Which hosts are consistently using more than 80% CPU?

Workflow:

```text
CPU Metrics
    │
    ▼
Create Time Series
    │
    ▼
Calculate Average / Maximum
    │
    ▼
Group by Host
    │
    ▼
Compare with 80%
```

The result can identify hosts approaching saturation.

---

# A Practical Predictive Monitoring Example

Question:

> Which workloads are showing sustained resource growth?

Workflow:

```text
Historical Metrics
       │
       ▼
Time-Series Aggregation
       │
       ▼
Calculate Average
       │
       ▼
Analyze Trend
       │
       ▼
Compare with Baseline
       │
       ▼
Forecast
```

This connects DQL analysis directly to predictive monitoring.

---

# Common Beginner Mistakes

## Mistake 1: Ignoring Data Types

Arithmetic requires compatible data types.

Do not assume that every field is numeric.

---

## Mistake 2: Ignoring Null Values

Missing values can affect calculations and filters.

---

## Mistake 3: Creating Extremely Complex Expressions

Break complex calculations into understandable steps.

---

## Mistake 4: Ignoring Units

Be careful when comparing:

```text
milliseconds
seconds
bytes
kilobytes
percentages
ratios
```

For example:

```text
500 ms
```

is not the same as:

```text
500 seconds
```

---

## Mistake 5: Confusing Ratio and Percentage

A ratio:

```text
0.05
```

represents:

```text
5%
```

Do not multiply by 100 twice.

---

## Mistake 6: Assuming Function Names

DQL has a large and evolving function library.

Always verify the current Dynatrace DQL reference when using a less familiar function.

---

# Interview Questions

### What is a DQL function?

A reusable operation that accepts values and produces a result.

### What is an expression?

A combination of fields, constants, operators, and functions that evaluates to a value.

### Why are expressions useful?

They allow you to calculate derived values directly from telemetry.

### What is a Boolean expression?

An expression that evaluates to `true` or `false`.

### Why are calculated fields useful?

They allow raw telemetry fields to be transformed into meaningful operational values.

### Why is null handling important?

Missing values can affect calculations, comparisons, aggregations, and filters.

### How are functions useful in SRE?

They help calculate error rates, latency statistics, utilization, traffic rates, and other operational indicators.

### How are expressions useful in capacity planning?

They allow resource utilization, growth rates, and capacity ratios to be derived from raw telemetry.

---

# Key Takeaways

* Functions perform reusable operations on data.
* Expressions produce calculated values.
* Arithmetic expressions can calculate ratios, percentages, totals, and rates.
* Boolean expressions are useful for filtering.
* Conditional expressions can classify telemetry.
* `fieldsAdd` can create calculated fields.
* Aggregation functions such as `count()`, `avg()`, `sum()`, `min()`, and `max()` summarize data.
* Time functions are important for observability and time-series analysis.
* Null values must be considered when performing calculations.
* Units must be handled carefully.
* Derived values are fundamental to SRE metrics.
* Functions and expressions support capacity planning and predictive monitoring.
* Keep complex expressions readable by breaking calculations into logical stages.
