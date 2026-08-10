# DQL Advanced SRE Analysis

## Overview

The previous DQL topics covered the fundamentals:

* Fetching data
* Filtering
* Fields
* Aggregation
* Time-series analysis
* Functions and expressions
* Joins
* Lookups
* Subqueries

The next step is to combine these capabilities into **real SRE-oriented analysis**.

In production environments, an SRE rarely asks only:

> Show me the errors.

Instead, the questions are more like:

> Which service is causing the highest error rate?

> Did latency increase after a deployment?

> Which Kubernetes workload is consuming the most resources?

> Are failures concentrated on a particular host?

> Which dependency is contributing most to application latency?

> Which services are approaching capacity?

These questions require multiple DQL techniques working together.

---

# Learning Objectives

After completing this document, you should understand:

* How to structure an SRE investigation in DQL
* How to move from symptoms to evidence
* How to analyze error rates
* How to analyze latency
* How to analyze request volume
* How to correlate service and infrastructure data
* How to investigate Kubernetes workloads
* How to analyze dependencies
* How to compare time periods
* How to identify abnormal behavior
* How to support root-cause analysis
* How DQL supports predictive monitoring

---

# The SRE Investigation Mindset

A good SRE investigation usually follows this pattern:

```text
Question
   │
   ▼
Define Time Window
   │
   ▼
Identify Affected Service
   │
   ▼
Measure Symptoms
   │
   ▼
Compare Related Metrics
   │
   ▼
Correlate Components
   │
   ▼
Find Evidence
   │
   ▼
Determine Root Cause
   │
   ▼
Take Action
```

DQL is primarily the tool used to obtain and analyze the evidence.

---

# Start With a Question

Avoid starting with:

```text
"Let me query everything."
```

Instead start with a specific question.

For example:

```text
Why did payment-service become slow?
```

Then break the question down:

```text
1. When did latency increase?
2. Did errors increase?
3. Did request volume increase?
4. Did CPU increase?
5. Did memory increase?
6. Did a dependency become slow?
7. Was there a deployment?
```

This produces a much more structured investigation.

---

# Step 1: Define the Time Window

Always establish when the problem occurred.

For example:

```text
Incident
10:00 ─────────────── 11:00
```

Then investigate:

```text
Before Incident
During Incident
After Incident
```

This allows you to determine whether a metric actually changed when the incident began.

---

# Before vs During Incident

Suppose:

```text
Before:
Latency = 150 ms

During:
Latency = 900 ms
```

That is useful evidence.

But if:

```text
Before:
Latency = 850 ms

During:
Latency = 900 ms
```

then the incident may have started earlier.

Time comparison prevents incorrect conclusions.

---

# Step 2: Identify the Affected Service

Suppose the application contains:

```text
Frontend
   │
   ▼
API Gateway
   │
   ├── Payment Service
   ├── Order Service
   └── User Service
```

If users report payment failures, begin with:

```text
payment-service
```

rather than investigating every service simultaneously.

---

# Step 3: Measure the Symptoms

Typical SRE symptoms include:

```text
High Error Rate
High Latency
Low Throughput
High CPU
High Memory
Connection Failures
Dependency Failures
```

These symptoms should be converted into measurable signals.

---

# The Four Golden Signals

A practical SRE investigation often starts with:

```text
Latency
Traffic
Errors
Saturation
```

These are known as the **Four Golden Signals**.

---

# Latency

Latency answers:

> How long does the system take to respond?

Examples:

```text
Average latency
Maximum latency
Percentile latency
```

For example:

```text
Average = 150 ms
P95 = 400 ms
P99 = 900 ms
```

Average latency alone may hide severe slow requests.

---

# Why Percentiles Matter

Suppose 99 requests take:

```text
100 ms
```

but one request takes:

```text
10,000 ms
```

The average may not adequately represent the user experience.

Percentiles help answer:

```text
What latency does most of the traffic experience?
```

Common percentiles include:

```text
P50
P90
P95
P99
```

---

# P50

P50 is approximately the median.

It represents the point below which roughly half the observations fall.

Useful for understanding typical behavior.

---

# P95

P95 represents the latency below which roughly 95% of observations fall.

It is useful for identifying slower-than-normal user experiences.

---

# P99

P99 focuses on the slowest portion of requests.

It is particularly useful for detecting tail latency.

---

# Tail Latency

Tail latency refers to the slower end of the latency distribution.

Example:

```text
P50 → 100 ms
P95 → 400 ms
P99 → 1200 ms
```

This suggests that while typical requests are fast, a small percentage are significantly slower.

---

# Traffic

Traffic measures workload.

Examples:

```text
Requests per second
Requests per minute
Messages processed
Transactions
```

Traffic helps answer:

> Did the system suddenly receive more work?

---

# Traffic and Capacity

Suppose:

```text
Normal traffic = 1,000 RPS
Incident traffic = 5,000 RPS
```

At the same time:

```text
CPU = 90%
Latency = 900 ms
```

The increased traffic may have contributed to resource saturation.

---

# Errors

Errors indicate failed or unsuccessful operations.

Examples:

```text
HTTP 5xx
Exceptions
Timeouts
Failed transactions
Connection errors
```

A useful investigation asks:

```text
Which service?
Which endpoint?
Which error?
When did it start?
How frequently does it occur?
```

---

# Error Rate

Error count alone can be misleading.

Example:

```text
Service A:
1,000 errors
1,000,000 requests

Service B:
100 errors
1,000 requests
```

Error counts:

```text
A → 1,000
B → 100
```

But error rates:

```text
A → 0.1%
B → 10%
```

Service B is significantly worse proportionally.

---

# Saturation

Saturation indicates how close a resource is to its limit.

Examples:

```text
CPU utilization
Memory utilization
Disk utilization
Connection pool utilization
Queue depth
Thread pool usage
```

A service can appear healthy until saturation approaches a critical point.

---

# Combining Golden Signals

A powerful investigation combines all four.

```text
             Service
                │
      ┌─────────┼─────────┐
      ▼         ▼         ▼
   Latency    Errors    Traffic
      │         │         │
      └─────────┼─────────┘
                ▼
           Saturation
```

Example:

```text
Traffic ↑
   │
   ▼
CPU ↑
   │
   ▼
Latency ↑
   │
   ▼
Errors ↑
```

This provides a much stronger hypothesis than looking at one metric.

---

# Service-Level Analysis

Suppose multiple services exist:

```text
payment-service
order-service
user-service
inventory-service
```

You can aggregate telemetry by service to determine:

```text
Which service has the most errors?
Which service has the highest latency?
Which service receives the most traffic?
```

This helps prioritize investigation.

---

# Endpoint-Level Analysis

A service may contain multiple endpoints:

```text
/payment
/refund
/history
/status
```

Overall service latency may look normal while one endpoint is extremely slow.

Therefore:

```text
Service
   │
   ▼
Endpoint
   │
   ▼
Latency / Errors
```

can provide more precise information.

---

# Dependency Analysis

Modern applications depend on:

```text
Databases
APIs
Queues
Caches
External Services
Cloud Services
```

If the application becomes slow, the dependency may be responsible.

Example:

```text
Payment Service
      │
      ▼
Payment Database
      │
      ▼
Query latency ↑
```

Application latency may increase as a consequence.

---

# Dependency Investigation

Ask:

```text
1. Which dependency is involved?
2. Did dependency latency increase?
3. Did dependency errors increase?
4. Did traffic increase?
5. Did the application wait longer?
```

This can distinguish application problems from dependency problems.

---

# Host-Level Analysis

A service may run across multiple hosts.

Example:

```text
payment-service

Host 1 → Normal
Host 2 → Normal
Host 3 → CPU 95%
Host 4 → Normal
```

This is an important clue.

The issue may be isolated to:

```text
Host 3
```

rather than the entire service.

---

# Container-Level Analysis

Similarly:

```text
Service
   │
   ├── Container 1 → Normal
   ├── Container 2 → Normal
   ├── Container 3 → High CPU
   └── Container 4 → Normal
```

A single unhealthy container can produce service-level symptoms.

---

# Kubernetes Investigation

For Kubernetes workloads, investigate:

```text
Cluster
   │
   ▼
Namespace
   │
   ▼
Deployment
   │
   ▼
Pod
   │
   ▼
Container
```

Then examine:

```text
CPU
Memory
Restarts
Network
Requests
Errors
```

---

# Kubernetes Restart Investigation

Suppose:

```text
Pod A → 0 restarts
Pod B → 0 restarts
Pod C → 12 restarts
Pod D → 0 restarts
```

Pod C deserves investigation.

Possible causes include:

```text
Out-of-Memory
Application Crash
Configuration Problem
Dependency Failure
Health Check Failure
```

---

# Memory Investigation

Suppose memory usage increases continuously:

```text
40%
50%
60%
70%
80%
90%
```

This may indicate:

```text
Increasing Workload
Memory Leak
Insufficient Limits
Unexpected Traffic
Caching Growth
```

A time-series view helps distinguish a temporary spike from sustained growth.

---

# CPU Investigation

Example:

```text
CPU
40%
45%
50%
65%
80%
90%
```

Questions:

```text
Did traffic increase?
Did a deployment occur?
Did workload distribution change?
Are some pods consuming more CPU?
Is autoscaling working?
```

---

# Correlation With Deployment Events

Suppose:

```text
10:00 → Normal
10:15 → Deployment
10:20 → Latency ↑
10:21 → Errors ↑
```

The deployment becomes an important event to investigate.

This does not automatically prove the deployment caused the issue.

It provides a strong temporal correlation that needs verification.

---

# Deployment Investigation Workflow

```text
Deployment
    │
    ▼
Compare Before / After
    │
    ├── Latency
    ├── Errors
    ├── Traffic
    ├── CPU
    └── Memory
```

If multiple signals change immediately after deployment, investigate the deployment.

---

# Detecting Anomalous Behavior

Anomaly analysis asks:

> Is this behavior unusual compared with what normally happens?

For example:

```text
Normal CPU:
40–60%

Current CPU:
92%
```

This is potentially anomalous.

But:

```text
Every day at 12:00:
CPU = 90%
```

may be normal if the workload has a predictable daily pattern.

---

# Baseline Comparison

A baseline represents expected behavior.

Conceptually:

```text
Historical Behavior
       │
       ▼
Baseline
       │
       ▼
Current Behavior
       │
       ▼
Deviation
```

This is a core concept in predictive monitoring.

---

# Comparing Time Periods

Useful comparisons include:

```text
Current vs Previous Hour
Current vs Previous Day
Current vs Previous Week
Current vs Historical Baseline
```

The correct comparison depends on the workload.

---

# Example: Daily Seasonality

Suppose traffic normally increases every weekday at 10 AM.

Comparing:

```text
Monday 10 AM
```

against:

```text
Sunday 10 AM
```

may be misleading.

A better comparison could be:

```text
Monday 10 AM
vs
Previous Mondays 10 AM
```

This accounts for recurring patterns.

---

# Root-Cause Analysis Pattern

A practical DQL-based investigation can follow:

```text
Symptom
  │
  ▼
Affected Service
  │
  ▼
Affected Endpoint
  │
  ▼
Error / Latency Pattern
  │
  ▼
Dependency
  │
  ▼
Infrastructure
  │
  ▼
Deployment / Configuration
  │
  ▼
Root Cause
```

This prevents jumping directly to conclusions.

---

# Example: High Latency

Suppose users report:

```text
Application is slow.
```

Investigation:

```text
1. Check service latency
2. Identify affected endpoint
3. Check traffic
4. Check errors
5. Check CPU
6. Check memory
7. Check database latency
8. Check external dependencies
9. Check recent deployments
```

Possible finding:

```text
Database latency ↑
        │
        ▼
Application latency ↑
        │
        ▼
Timeouts ↑
```

This gives a plausible root-cause chain.

---

# Example: High Error Rate

Suppose:

```text
Error Rate = 10%
```

Investigate:

```text
Which endpoint?
       │
       ▼
Which error?
       │
       ▼
Which host/pod?
       │
       ▼
Which dependency?
       │
       ▼
Did a deployment happen?
```

This turns a generic alert into a specific investigation.

---

# Example: Traffic Spike

Suppose:

```text
Traffic:
1,000 RPS → 5,000 RPS
```

Then:

```text
CPU:
50% → 90%

Latency:
150 ms → 800 ms

Errors:
0.2% → 5%
```

This suggests a possible workload-driven saturation scenario.

The next step is to determine whether the traffic increase was:

```text
Expected
Unexpected
Legitimate
Malicious
Caused by another service
```

---

# Advanced Analysis Pattern

A complex investigation can follow:

```text
                Incident
                   │
                   ▼
              Time Window
                   │
                   ▼
             Affected Service
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Traffic   Errors   Latency
          │        │        │
          └────────┼────────┘
                   ▼
              Saturation
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
        Host      Pod    Dependency
          │        │        │
          └────────┼────────┘
                   ▼
             Deployment
                   │
                   ▼
             Root Cause
```

---

# DQL as an Investigation Tool

DQL should not be treated as simply:

```text
"Write a query and get data."
```

Instead:

```text
Question
   ↓
Hypothesis
   ↓
Query
   ↓
Evidence
   ↓
Refine Hypothesis
   ↓
New Query
   ↓
Conclusion
```

This iterative process is important in SRE.

---

# Hypothesis-Driven Investigation

Example hypothesis:

> The payment service is slow because CPU saturation increased.

Test it:

```text
Check CPU
```

If CPU is normal:

```text
Hypothesis weakened
```

Next hypothesis:

> The database is slow.

Check database latency.

If database latency increased:

```text
Hypothesis strengthened
```

This is much more reliable than guessing.

---

# Evidence Chain

A strong RCA should have evidence.

For example:

```text
10:15
Deployment occurred

10:17
Database latency increased

10:18
Application latency increased

10:19
Timeout errors increased

10:20
User failures increased
```

This creates a chronological evidence chain.

---

# DQL and Root Cause Analysis

DQL can provide evidence for:

```text
When the issue began
What changed
Which service was affected
Which hosts were affected
Which dependencies were affected
How severe the issue was
Whether behavior was abnormal
```

DQL does not automatically guarantee the correct root cause.

Human reasoning and system knowledge remain important.

---

# DQL and Predictive Monitoring

Advanced analysis provides the foundation for predictive monitoring.

```text
Historical Data
      │
      ▼
DQL Analysis
      │
      ├── Trends
      ├── Baselines
      ├── Seasonality
      └── Resource Growth
      │
      ▼
Prediction
      │
      ▼
Potential Future Issue
      │
      ▼
Proactive Action
```

---

# DQL and Capacity Forecasting

Suppose:

```text
CPU utilization

Jan → 50%
Feb → 55%
Mar → 61%
Apr → 68%
May → 75%
```

DQL can help create the historical time series.

Then predictive analysis can determine:

```text
When might CPU exceed 80%?
```

This changes capacity management from:

```text
Reactive
```

to:

```text
Proactive
```

---

# DQL and Alert Investigation

Suppose an alert says:

```text
Error rate > 5%
```

The alert is only the starting point.

Use DQL to investigate:

```text
Which service?
Which endpoint?
Which errors?
Which hosts?
Which pods?
Which dependencies?
When did it start?
Was there a deployment?
```

This dramatically reduces investigation time.

---

# Query Design Principles

Good DQL queries should be:

```text
Focused
Readable
Efficient
Relevant
Reusable
```

Avoid:

```text
Huge unfiltered datasets
Unnecessary joins
Unnecessary fields
Unnecessary transformations
```

---

# Filter Early

A useful principle is:

```text
Filter
   ↓
Transform
   ↓
Aggregate
```

Instead of:

```text
Transform everything
   ↓
Aggregate everything
   ↓
Filter at the end
```

Filtering early can reduce the amount of data processed.

---

# Aggregate When Appropriate

If the question is:

> Which service has the highest error rate?

You probably do not need every individual log record in the final result.

Instead:

```text
Raw Logs
   ↓
Group by Service
   ↓
Calculate Error Rate
   ↓
Sort
```

This produces a much more useful result.

---

# Keep the Investigation Narrow

Bad approach:

```text
All Hosts
All Services
All Logs
All Metrics
All Time
```

Better:

```text
Production
Payment Service
Incident Timeframe
Relevant Signals
```

Narrowing the scope is one of the most important investigation skills.

---

# Common Mistakes

## Mistake 1: Querying Everything

More data does not automatically mean better analysis.

---

## Mistake 2: Looking at Only One Metric

CPU alone rarely proves root cause.

---

## Mistake 3: Ignoring Time

Always determine when the behavior changed.

---

## Mistake 4: Ignoring Dependencies

The problem may be downstream.

---

## Mistake 5: Ignoring Deployments

A recent deployment may provide an important clue.

---

## Mistake 6: Confusing Correlation With Causation

Two events occurring together does not prove that one caused the other.

---

## Mistake 7: Ignoring Cardinality

Joining service-level and pod-level data can unintentionally duplicate records.

---

# Practical SRE Checklist

When investigating an incident:

```text
□ Define incident timeframe
□ Identify affected service
□ Check latency
□ Check traffic
□ Check errors
□ Check saturation
□ Check affected endpoints
□ Check hosts
□ Check containers/pods
□ Check dependencies
□ Check deployments
□ Compare before and after
□ Compare against baseline
□ Validate the hypothesis
□ Document evidence
```

---

# Interview Questions

### What are the Four Golden Signals?

Latency, Traffic, Errors, and Saturation.

### Why is error rate more useful than error count?

Because it normalizes errors against total workload.

### Why are percentiles useful?

They show the behavior of different portions of the request population and help reveal tail latency.

### How would you investigate high latency?

Check the affected service and endpoint, then correlate traffic, errors, infrastructure resources, dependencies, and recent changes.

### Why should you filter early?

To reduce the amount of data processed and keep the investigation focused.

### Can CPU being high prove CPU is the root cause?

No. It is evidence that should be correlated with workload, latency, errors, and other signals.

### Why is time-series analysis important for RCA?

It shows when abnormal behavior began and allows it to be correlated with deployments, traffic changes, and other events.

### How does DQL support predictive monitoring?

It can extract and transform historical telemetry into trends, time series, baselines, and other data used for forecasting and anomaly analysis.

---

# Key Takeaways

* Advanced DQL is primarily about combining multiple analysis techniques.
* SRE investigations should be question-driven.
* Start with a clear timeframe.
* Use the Four Golden Signals as a starting point.
* Analyze services, endpoints, hosts, containers, and dependencies.
* Compare behavior before and during incidents.
* Use time-series analysis to identify trends and changes.
* Use hypothesis-driven investigation rather than guessing.
* Correlation provides evidence but does not automatically prove causation.
* Filter early and aggregate when appropriate.
* Be careful with joins and duplicate records.
* Deployment events are important investigation signals.
* DQL can provide the historical evidence required for predictive monitoring and capacity forecasting.
