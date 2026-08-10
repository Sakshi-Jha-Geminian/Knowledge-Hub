# DQL Time-Series Analysis

## Overview

Time is one of the most important dimensions in observability.

SRE teams rarely ask only:

> What is the CPU usage?

They usually ask:

> How did CPU usage change over the last hour?

Or:

> When did latency start increasing?

Or:

> Is this error rate abnormal compared with the previous period?

These questions require **time-series analysis**.

A time series is essentially a sequence of measurements associated with timestamps.

```text
Time
 │
 ├── 10:00 → 40%
 ├── 10:05 → 45%
 ├── 10:10 → 50%
 ├── 10:15 → 65%
 └── 10:20 → 80%
```

The important information is not only the values, but also **how those values change over time**.

---

# Learning Objectives

After completing this document, you should understand:

* What time-series data is
* Why time matters in observability
* Timeframes in DQL
* Time intervals
* Time bucketing
* `makeTimeseries`
* Aggregating data over time
* Comparing time periods
* Detecting trends
* Detecting spikes
* Time-series analysis for SRE
* Time-series analysis for capacity planning
* Common mistakes

---

# What is a Time Series?

A time series is a sequence of values ordered by time.

Example:

```text
Time        CPU
----------------
10:00       40%
10:05       45%
10:10       48%
10:15       70%
10:20       85%
```

This tells us much more than a single CPU value.

We can identify:

```text
Trend
Spike
Drop
Stable Period
Sudden Change
```

---

# Why Time Matters in SRE

Suppose the current CPU utilization is:

```text
85%
```

That number alone does not tell us much.

But if we know:

```text
09:00 → 40%
10:00 → 45%
11:00 → 50%
12:00 → 85%
```

we can see that CPU usage increased significantly.

This may indicate:

```text
Traffic Growth
Resource Saturation
Memory Pressure
Application Change
Deployment
Unexpected Workload
```

---

# Time-Series Mental Model

Think of observability data like this:

```text
                  Time
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      10:00      10:30      11:00
        │          │          │
        ▼          ▼          ▼
       Data       Data       Data
        │          │          │
        └──────────┼──────────┘
                   ▼
                 Trend
```

---

# Time Windows

A query normally needs a relevant time window.

Examples:

```text
Last 5 minutes
Last 30 minutes
Last 1 hour
Last 6 hours
Last 24 hours
Last 7 days
```

For incident investigation, a short window may be appropriate.

For capacity planning, a longer window is usually more useful.

---

# Choosing the Right Time Window

### Incident Investigation

Use:

```text
Minutes → Hours
```

Example:

```text
Incident:
10:15 – 10:45
```

### Performance Analysis

Use:

```text
Hours → Days
```

### Capacity Planning

Use:

```text
Weeks → Months
```

The time window should match the question.

---

# Relative Time

DQL supports relative time expressions.

For example:

```dql id="a1y0k2"
fetch logs, from:now() - 30m
```

This means:

> Retrieve data from approximately the last 30 minutes.

Similarly, a longer timeframe can be specified when needed.

The exact timeframe syntax should always be checked against the Dynatrace DQL version and environment being used.

---

# Why Time Filtering Should Happen Early

Consider:

```text
All Historical Data
       │
       ▼
Millions of Records
       │
       ▼
Filter Time
```

versus:

```text
Relevant Time Window
       │
       ▼
Relevant Records
       │
       ▼
Analysis
```

The second approach is usually more efficient and easier to reason about.

---

# Time Bucketing

Suppose you have thousands of measurements.

Instead of displaying every individual measurement, you can group values into time intervals.

For example:

```text
1-minute buckets
5-minute buckets
15-minute buckets
1-hour buckets
```

Conceptually:

```text
Raw Data

10:00:01
10:00:02
10:00:03
10:00:04
...
10:04:59

        ↓

5-minute bucket

10:00 – 10:05
```

The values inside each bucket can then be aggregated.

---

# Why Time Bucketing Is Useful

Suppose you have:

```text
1 million measurements
```

Displaying every measurement is not useful.

Instead:

```text
1 million measurements
       │
       ▼
5-minute buckets
       │
       ▼
Average / Max / Min
       │
       ▼
Time Series
```

This produces a much clearer trend.

---

# `makeTimeseries`

`makeTimeseries` is a DQL command used to create time-series data.

Conceptually:

```dql id="3j4w6d"
...
| makeTimeseries ...
```

It converts records into a time-series representation.

A time series can then be used for:

```text
Trend Analysis
Charts
Anomaly Analysis
Capacity Planning
Performance Analysis
```

---

# Basic Concept

Suppose we have CPU measurements:

```text
10:00 → 40%
10:01 → 42%
10:02 → 44%
10:03 → 50%
```

`makeTimeseries` can organize these measurements into time buckets.

Conceptually:

```text
Time
 │
 ▼
10:00 ───── 40%
10:01 ───── 42%
10:02 ───── 44%
10:03 ───── 50%
```

---

# Aggregating Time-Series Values

Time-series data can be aggregated.

Common aggregation functions include:

```text
avg
sum
min
max
count
```

For example:

```text
Average CPU per 5 minutes
```

Conceptually:

```text
10:00–10:05 → 45%
10:05–10:10 → 50%
10:10–10:15 → 65%
```

---

# Average Over Time

Average is useful when you want to understand the typical behavior during each interval.

Example:

```text
10:00 → 40%
10:05 → 45%
10:10 → 50%
```

The average smooths individual fluctuations.

---

# Maximum Over Time

Maximum values are useful when detecting saturation.

Example:

```text
10:00 → Max CPU 60%
10:05 → Max CPU 70%
10:10 → Max CPU 95%
```

The 95% value may indicate resource pressure.

---

# Minimum Over Time

Minimum values can help understand:

```text
Lowest utilization
Availability gaps
Traffic drops
Unexpected inactivity
```

For example:

```text
10:00 → 40%
10:05 → 38%
10:10 → 5%
```

The sudden drop may deserve investigation.

---

# Count Over Time

Counting records per time interval can reveal traffic patterns.

Example:

```text
10:00 → 1,000 requests
10:05 → 1,500 requests
10:10 → 3,000 requests
```

This may indicate a sudden increase in workload.

---

# Time-Series Visualization

A time series is often displayed as a line chart.

Conceptually:

```text
Value
  │
90│                    ●
80│                 ●
70│              ●
60│           ●
50│       ●
40│   ●
  └────────────────────────
      Time →
```

The shape makes trends and spikes easier to recognize.

---

# Detecting Trends

A trend is a persistent movement in one direction.

Example:

```text
40%
45%
50%
55%
60%
65%
```

This is an increasing trend.

Possible causes:

```text
Traffic Growth
Memory Leak
Increasing Workload
Capacity Constraint
```

---

# Detecting Spikes

A spike is a sudden increase.

Example:

```text
40%
42%
45%
90%
45%
43%
```

This is different from a long-term trend.

Possible causes:

```text
Traffic Burst
Deployment
Batch Job
External Dependency
Attack
Unexpected Workload
```

---

# Detecting Drops

Example:

```text
80%
82%
85%
20%
18%
20%
```

A sudden drop may indicate:

```text
Service Failure
Traffic Loss
Dependency Failure
Network Issue
Deployment Problem
```

---

# Trend vs Spike

This distinction is important.

### Trend

```text
40 → 45 → 50 → 55 → 60
```

Gradual change.

### Spike

```text
40 → 42 → 90 → 43 → 41
```

Short-lived change.

SREs investigate these differently.

---

# Time-Series Analysis During an Incident

Suppose users report slow application performance.

The investigation might be:

```text
Incident
   │
   ▼
Check Latency Trend
   │
   ▼
Check Error Rate
   │
   ▼
Check CPU
   │
   ▼
Check Memory
   │
   ▼
Check Dependency Latency
   │
   ▼
Correlate Timelines
```

The goal is to find when the abnormal behavior started and what changed around that time.

---

# Correlating Multiple Time Series

Suppose:

```text
CPU
10:00 → 40%
10:10 → 45%
10:20 → 90%
```

and:

```text
Latency
10:00 → 100 ms
10:10 → 120 ms
10:20 → 800 ms
```

and:

```text
Errors
10:00 → 5
10:10 → 10
10:20 → 500
```

All three changed around the same time.

This correlation is valuable.

---

# Example Correlation

```text
10:20
   │
   ├── CPU ↑
   ├── Latency ↑
   └── Errors ↑
```

This suggests a relationship worth investigating.

However, correlation alone does not prove causation.

You must investigate the underlying system behavior.

---

# Time-Series Analysis and Deployments

Deployment events can be compared against performance changes.

Example:

```text
10:00 ────────────────
10:15 Deployment
10:20 Latency ↑
10:21 Errors ↑
```

This temporal relationship may suggest that the deployment contributed to the issue.

---

# Time-Series Analysis and SLOs

SLOs often depend on measurements over time.

Examples:

```text
Availability
Error Rate
Latency
Successful Requests
```

A time series can show whether the service remained within its target.

Example:

```text
Error Rate
   │
SLO ─────────────────
   │
   │      ●
   │   ●     ●
   │ ●
   └──────────────────
          Time
```

---

# Time-Series Analysis and Capacity Planning

Capacity planning is heavily dependent on historical trends.

Example:

```text
CPU Utilization

Jan → 45%
Feb → 50%
Mar → 55%
Apr → 62%
May → 68%
Jun → 75%
```

This indicates increasing utilization.

The next step may be forecasting future demand.

---

# Historical vs Forecast Data

Historical:

```text
Past
 │
 ├── 40%
 ├── 45%
 ├── 50%
 └── 55%
```

Forecast:

```text
Future
 │
 ├── 60%
 ├── 65%
 ├── 70%
 └── 75%
```

This is the foundation of predictive monitoring.

---

# Time-Series Analysis and Predictive Monitoring

Predictive monitoring builds on historical time-series data.

Conceptually:

```text
Historical Data
      │
      ▼
Time-Series Analysis
      │
      ▼
Trend Detection
      │
      ▼
Forecasting
      │
      ▼
Predicted Future State
      │
      ▼
Proactive Action
```

This is one of the major connections between DQL and predictive monitoring.

---

# Example: Predicting Resource Saturation

Suppose:

```text
CPU
January → 50%
February → 55%
March → 62%
April → 70%
May → 78%
```

If the trend continues:

```text
Future
   │
   ▼
CPU approaching 90%
   │
   ▼
Potential saturation
```

An SRE can investigate capacity before the system reaches the critical point.

---

# Time-Series Analysis and Seasonality

Some workloads change according to predictable patterns.

Example:

```text
Monday → High
Tuesday → High
Wednesday → High
...
Weekend → Low
```

Or:

```text
Daytime → High traffic
Night → Low traffic
```

These recurring patterns are called **seasonality**.

Understanding seasonality prevents normal behavior from being mistaken for anomalies.

---

# Example of Seasonality

Suppose traffic is:

```text
09:00 → 2,000
12:00 → 5,000
15:00 → 4,500
18:00 → 2,000
00:00 → 500
```

If this pattern repeats every day, it may be normal.

A monitoring system should understand the expected baseline before declaring an anomaly.

---

# Time-Series Granularity

Granularity refers to how detailed the time buckets are.

Examples:

```text
1 minute
5 minutes
15 minutes
1 hour
1 day
```

Choosing the correct granularity is important.

---

# Small Time Buckets

Example:

```text
1 minute
```

Useful for:

```text
Incident Investigation
Sudden Spikes
Short-Lived Failures
Real-Time Monitoring
```

---

# Large Time Buckets

Example:

```text
1 day
```

Useful for:

```text
Capacity Planning
Long-Term Trends
Business Analysis
Seasonality
```

---

# Choosing Granularity

Use:

```text
Short incident
     ↓
Fine granularity
```

Use:

```text
Long-term analysis
     ↓
Coarser granularity
```

The goal is to preserve useful information without creating unnecessary noise.

---

# Time-Series Query Pattern

A common conceptual pattern is:

```text
FETCH
  │
  ▼
TIME FILTER
  │
  ▼
FILTER DIMENSIONS
  │
  ▼
MAKE TIMESERIES
  │
  ▼
AGGREGATE
  │
  ▼
ANALYZE
```

For example:

```text
Logs / Metrics
      │
      ▼
Production
      │
      ▼
Payment Service
      │
      ▼
5-minute intervals
      │
      ▼
Average / Count / Max
```

---

# Example Investigation

Question:

> Did the payment service experience increasing latency during the incident?

Approach:

```text
1. Select incident timeframe
        ↓
2. Select payment service
        ↓
3. Create latency time series
        ↓
4. Analyze the trend
        ↓
5. Compare with errors
        ↓
6. Compare with infrastructure metrics
```

This is much more informative than looking at a single latency number.

---

# Common Beginner Mistakes

## Mistake 1: Looking at a Single Value

One value cannot tell you the trend.

Always ask:

> How did this value change over time?

---

## Mistake 2: Choosing an Inappropriate Time Window

A five-minute window is not sufficient for a six-month capacity trend.

---

## Mistake 3: Using Excessively Fine Granularity

Very small intervals can create noisy results.

---

## Mistake 4: Ignoring Seasonality

A recurring daily or weekly pattern may be normal rather than anomalous.

---

## Mistake 5: Confusing Correlation with Causation

Two metrics changing at the same time does not automatically mean one caused the other.

---

# SRE Time-Series Workflow

```text
                    Incident
                       │
                       ▼
                Select Timeframe
                       │
                       ▼
                 Analyze Trends
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Latency    Errors      CPU
             │         │         │
             └─────────┼─────────┘
                       ▼
                  Correlate
                       │
                       ▼
                Find Root Cause
```

---

# Capacity Planning Workflow

```text
Historical Data
      │
      ▼
Time-Series Analysis
      │
      ▼
Growth Trend
      │
      ▼
Seasonality
      │
      ▼
Forecast
      │
      ▼
Capacity Requirement
```

---

# Interview Questions

### What is a time series?

A sequence of measurements associated with timestamps.

### Why is time important in observability?

It allows engineers to identify trends, spikes, drops, correlations, and the exact period during which an incident occurred.

### What is time bucketing?

Grouping records into fixed time intervals so that values can be aggregated and analyzed as a time series.

### What is `makeTimeseries`?

A DQL command used to create time-series representations from records.

### What is a trend?

A sustained increase or decrease in a value over time.

### What is a spike?

A sudden temporary increase or change in a value.

### What is seasonality?

A recurring pattern that occurs at predictable time intervals.

### How does time-series analysis support capacity planning?

It reveals historical resource trends and helps identify future capacity requirements.

### How does time-series analysis support predictive monitoring?

Historical time-series data provides the foundation for baselining, trend detection, forecasting, and proactive capacity decisions.

---

# Key Takeaways

* Time is fundamental to observability.
* A time series shows how values change over time.
* Time windows should match the investigation objective.
* Time bucketing reduces large datasets into useful intervals.
* `makeTimeseries` is used to create time-series data.
* Average, minimum, maximum, sum, and count can be used to analyze time-series values.
* Trends represent sustained changes.
* Spikes represent sudden changes.
* Drops can indicate failures or traffic changes.
* Seasonality represents recurring patterns.
* Fine granularity is useful for incidents.
* Coarser granularity is useful for long-term analysis.
* Correlating multiple time series can reveal relationships between system behaviors.
* Time-series analysis is a foundation for predictive monitoring and capacity forecasting.
