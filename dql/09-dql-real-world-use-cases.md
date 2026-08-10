# DQL Real-World SRE Use Cases

## Overview

Learning DQL syntax is important, but an SRE needs to know how to apply DQL to real operational problems.

In production, DQL is commonly used to answer questions such as:

* Why is an application slow?
* Which service is generating errors?
* Which hosts are under pressure?
* Which Kubernetes pods are restarting?
* Did a deployment cause an incident?
* Which dependency is causing latency?
* Is traffic increasing?
* Are resources approaching capacity?
* Which services require immediate attention?

This document brings together the DQL concepts covered so far and applies them to realistic SRE scenarios.

---

# Learning Objectives

After completing this document, you should understand how DQL can be applied to:

* Incident investigation
* Error analysis
* Latency analysis
* Traffic analysis
* Infrastructure monitoring
* Kubernetes monitoring
* Deployment investigation
* Dependency analysis
* Capacity planning
* Predictive monitoring
* Service health analysis
* Root-cause analysis

---

# 1. Incident Investigation

One of the most important uses of DQL is investigating incidents.

Suppose users report:

> The payment application is failing intermittently.

The investigation should not immediately jump to a conclusion.

Start with:

```text
Incident
   │
   ▼
When did it happen?
   │
   ▼
Which service?
   │
   ▼
What changed?
   │
   ▼
What symptoms appeared?
   │
   ▼
What component caused them?
```

---

# Define the Incident Window

First identify:

```text
Start Time
End Time
```

For example:

```text
10:00 ───────────────── 11:00
        Incident
```

Then compare:

```text
Before
During
After
```

This helps determine when the abnormal behavior started.

---

# Identify the Affected Service

Suppose the architecture is:

```text
User
 │
 ▼
Web Application
 │
 ▼
API Gateway
 │
 ├── User Service
 ├── Order Service
 └── Payment Service
```

If the incident involves payment failures, investigate:

```text
payment-service
```

first.

Do not immediately query every service in the environment.

---

# Check the Error Rate

The first question is:

> Is the service actually experiencing an increase in failures?

Conceptually:

```text
Total Requests
       │
       ├── Successful
       └── Failed
```

Calculate:

```text
Error Rate =
Failed Requests / Total Requests
```

Example:

```text
Total Requests = 100,000
Failed Requests = 5,000

Error Rate = 5%
```

---

# Find the Error Types

Once a high error rate is confirmed, determine which errors are responsible.

Possible categories:

```text
HTTP 500
HTTP 502
HTTP 503
Timeout
Connection Refused
Database Error
Authentication Error
```

This narrows the investigation.

---

# Find the Affected Endpoint

A service can have many endpoints.

For example:

```text
/payment
/refund
/history
/status
```

Suppose:

```text
/payment → 12% errors
/refund  → 0.5% errors
/history → 0.1% errors
```

The `/payment` endpoint becomes the primary investigation target.

---

# Find the Affected Host

Suppose the service runs on four hosts:

```text
Host 1 → 0.2% errors
Host 2 → 0.3% errors
Host 3 → 15% errors
Host 4 → 0.1% errors
```

This strongly suggests the problem may be isolated to Host 3.

Next investigate:

```text
CPU
Memory
Disk
Network
Processes
Deployment
```

---

# Find the Affected Pod

In Kubernetes:

```text
payment-service
      │
      ├── Pod A → Normal
      ├── Pod B → Normal
      ├── Pod C → Errors
      └── Pod D → Normal
```

Now the investigation becomes much more specific.

Check Pod C for:

```text
Restarts
CPU
Memory
Logs
Container status
Health checks
```

---

# 2. Latency Investigation

Suppose the alert says:

```text
Payment API latency is high.
```

Do not stop at average latency.

Investigate:

```text
P50
P90
P95
P99
```

Example:

```text
P50 = 120 ms
P95 = 500 ms
P99 = 1500 ms
```

This indicates that the tail of the distribution is significantly slower.

---

# Latency by Endpoint

Analyze latency separately:

```text
Endpoint       P95
-------------------
/payment       1500 ms
/refund         300 ms
/history        250 ms
/status          50 ms
```

The problem is concentrated in `/payment`.

---

# Latency by Dependency

Suppose `/payment` calls:

```text
Payment API
    │
    ├── Database
    ├── Fraud API
    └── Account Service
```

Investigate each dependency.

Example:

```text
Database       → 100 ms
Fraud API      → 1200 ms
Account API    → 150 ms
```

The Fraud API is a strong candidate for further investigation.

---

# Latency Investigation Flow

```text
High Latency
     │
     ▼
Service
     │
     ▼
Endpoint
     │
     ▼
Dependency
     │
     ▼
Infrastructure
     │
     ▼
Recent Changes
```

---

# 3. Traffic Analysis

Traffic tells you how much workload the system is receiving.

Example:

```text
09:00 → 1,000 RPS
10:00 → 1,200 RPS
11:00 → 1,300 RPS
12:00 → 5,000 RPS
```

There is a major traffic spike at 12:00.

---

# Traffic and Saturation

Now compare:

```text
Traffic:
1,300 → 5,000 RPS
```

with:

```text
CPU:
50% → 92%
```

and:

```text
Latency:
150 ms → 900 ms
```

and:

```text
Errors:
0.2% → 5%
```

This creates a possible chain:

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

This is a hypothesis that must be validated with additional evidence.

---

# 4. CPU Investigation

High CPU can have several causes.

Possible causes:

```text
Increased traffic
CPU-intensive application code
Batch processing
Infinite loops
Inefficient queries
Misconfigured workloads
Insufficient capacity
```

Therefore:

```text
High CPU ≠ Automatically Root Cause
```

It is evidence that requires correlation.

---

# CPU by Host

Example:

```text
Host 1 → 45%
Host 2 → 48%
Host 3 → 94%
Host 4 → 46%
```

Host 3 is an outlier.

Next investigate workloads running on Host 3.

---

# CPU by Kubernetes Pod

Example:

```text
Pod A → 40%
Pod B → 45%
Pod C → 95%
Pod D → 42%
```

Pod C should be investigated.

Look for:

```text
Restart count
Requests
Limits
Logs
Deployment version
Node
```

---

# 5. Memory Investigation

Memory problems often develop differently from CPU problems.

Example:

```text
Memory

40%
45%
50%
60%
70%
80%
90%
```

A sustained increase may indicate:

```text
Memory Leak
Growing Cache
Increasing Workload
Insufficient Memory
Application Behavior
```

---

# Memory Leak Pattern

A possible memory leak may look like:

```text
Memory
 │
90│                ●
80│             ●
70│          ●
60│       ●
50│    ●
40│ ●
 └────────────────────
        Time
```

If memory repeatedly grows without returning to its normal level, investigate the application behavior.

---

# 6. Kubernetes Investigation

Kubernetes adds another layer of complexity.

The hierarchy is:

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
ReplicaSet
   │
   ▼
Pod
   │
   ▼
Container
```

DQL analysis should identify where the problem exists within this hierarchy.

---

# Pod Restart Investigation

Suppose:

```text
Pod A → 0 restarts
Pod B → 0 restarts
Pod C → 15 restarts
Pod D → 0 restarts
```

Investigate Pod C.

Possible causes:

```text
OOMKilled
Application Crash
Failed Health Check
Configuration Error
Dependency Failure
```

---

# OOM Investigation

OOM means:

> Out Of Memory.

A typical sequence may be:

```text
Memory ↑
   │
   ▼
Memory Limit Reached
   │
   ▼
Container Terminated
   │
   ▼
Pod Restart
```

If this pattern repeats, investigate memory allocation and application behavior.

---

# Kubernetes Capacity Analysis

For Kubernetes, capacity analysis can include:

```text
Node CPU
Node Memory
Pod CPU
Pod Memory
Requests
Limits
Replica Count
Pod Distribution
```

Example:

```text
Node 1 → CPU 60%
Node 2 → CPU 65%
Node 3 → CPU 92%
```

Node 3 may require investigation.

---

# 7. Deployment Investigation

One of the most important incident questions is:

> What changed?

Possible changes include:

```text
Deployment
Configuration
Infrastructure
Dependency
Traffic
Feature Flag
Database
```

A deployment is often an important event to correlate with telemetry.

---

# Before and After Deployment

Example:

```text
10:00 → Latency 150 ms
10:10 → Latency 160 ms
10:15 → Deployment
10:20 → Latency 700 ms
10:25 → Latency 900 ms
```

This is strong temporal evidence.

Next investigate:

```text
Deployment Version
Code Changes
Configuration Changes
Affected Endpoints
Error Messages
```

---

# Deployment Comparison

Compare:

```text
Before Deployment
```

against:

```text
After Deployment
```

For:

```text
Latency
Errors
Traffic
CPU
Memory
Dependency Calls
```

This can identify regressions.

---

# 8. Dependency Failure

Suppose:

```text
Order Service
     │
     ├── Database
     ├── Payment Service
     └── Inventory Service
```

The Order Service may appear unhealthy because one dependency is failing.

---

# Dependency Error Pattern

Example:

```text
Inventory API Errors
       ↑
       │
Order Service Timeouts
       ↑
       │
User Request Failures
```

This indicates a possible downstream failure propagation.

---

# Dependency Latency Pattern

Example:

```text
Inventory API
Latency:
100 ms → 150 ms → 800 ms
```

At approximately the same time:

```text
Order Service
Latency:
200 ms → 300 ms → 1200 ms
```

The correlation should be investigated.

---

# 9. Service Dependency Mapping

A useful conceptual model is:

```text
Frontend
   │
   ▼
API Gateway
   │
   ├──────────────┐
   ▼              ▼
Order          User
Service        Service
   │
   ├──────┐
   ▼      ▼
Payment  Inventory
Service  Service
```

When a downstream service fails, upstream services may also become unhealthy.

---

# 10. Error Pattern Analysis

Sometimes the error message itself reveals useful information.

Examples:

```text
timeout
connection refused
out of memory
authentication failed
database unavailable
rate limit exceeded
```

Group errors by:

```text
Error Type
Service
Endpoint
Host
Pod
Time
```

This helps identify patterns.

---

# 11. Error Burst Analysis

Suppose:

```text
10:00 → 5 errors
10:05 → 7 errors
10:10 → 8 errors
10:15 → 500 errors
10:20 → 700 errors
```

This indicates a sudden error burst.

Compare this with:

```text
Deployment
Traffic
CPU
Memory
Dependency health
```

around 10:15.

---

# 12. Capacity Planning

DQL is useful for extracting historical data required for capacity planning.

Suppose CPU utilization is:

```text
January → 50%
February → 55%
March → 60%
April → 67%
May → 74%
June → 81%
```

The trend suggests increasing resource demand.

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
Current Utilization
      │
      ▼
Forecast
      │
      ▼
Capacity Decision
```

---

# Capacity Thresholds

Suppose the organization defines:

```text
< 70% → Healthy
70–80% → Monitor
80–90% → Warning
> 90% → Critical
```

A service currently at:

```text
87%
```

may require proactive capacity planning.

The exact thresholds should be based on the system's SLOs, workload characteristics, and operational policy.

---

# 13. Detecting Resource Hotspots

Suppose:

```text
Host 1 → 40%
Host 2 → 42%
Host 3 → 91%
Host 4 → 45%
Host 5 → 43%
```

Host 3 is a resource hotspot.

Questions:

```text
Why is Host 3 different?
Which workloads are running there?
Is traffic unevenly distributed?
Is the host undersized?
```

---

# 14. Predictive Monitoring

Historical DQL analysis can support predictive monitoring.

Example:

```text
CPU Usage
50%
55%
61%
68%
75%
82%
```

A trend is visible.

The next question becomes:

> When might resource utilization become unacceptable?

This is where forecasting becomes valuable.

---

# Predictive Monitoring Workflow

```text
Telemetry
   │
   ▼
DQL
   │
   ▼
Historical Time Series
   │
   ▼
Baseline
   │
   ▼
Trend
   │
   ▼
Forecast
   │
   ▼
Prediction
   │
   ▼
Proactive Action
```

---

# 15. Anomaly Detection

Suppose normal CPU is:

```text
40–60%
```

but suddenly:

```text
CPU = 95%
```

The system may flag this as abnormal.

However, always ask:

```text
Is this expected?
Does it happen every day?
Was there a deployment?
Did traffic increase?
```

Context matters.

---

# 16. Baseline Analysis

A baseline represents expected system behavior.

For example:

```text
Normal weekday traffic:
1,000–1,500 RPS
```

Current traffic:

```text
5,000 RPS
```

The deviation is significant.

---

# Baseline and Seasonality

Suppose traffic is always high between:

```text
12:00–13:00
```

Then:

```text
12:30 → 5,000 RPS
```

may be completely normal.

A good monitoring strategy understands recurring patterns rather than treating every high value as an anomaly.

---

# 17. Business-Aware Analysis

Technical metrics become more useful when combined with business context.

Example:

```text
Payment Service
Error Rate = 3%
```

Add:

```text
Criticality = Tier 1
Owner = Payments Team
Business Function = Transactions
```

Now the operational significance is much clearer.

---

# Enriched Incident Analysis

```text
Technical Signal
      │
      ▼
Service
      │
      ▼
Owner
      │
      ▼
Criticality
      │
      ▼
Business Impact
```

This is where lookups and enrichment become especially valuable.

---

# 18. SLO Investigation

Suppose the SLO is:

```text
Availability Target = 99.9%
```

You can investigate:

```text
Successful Requests
Total Requests
Failed Requests
Error Rate
```

Then determine whether the service is operating within its reliability target.

---

# Error Budget

An error budget represents the amount of unreliability allowed by an SLO.

For example:

```text
SLO = 99.9%
```

means:

```text
Allowed failure = 0.1%
```

If failures consume the error budget rapidly, engineering teams may need to prioritize reliability work.

---

# 19. Multi-Signal Correlation

One of the strongest DQL use cases is comparing several signals.

Example:

```text
Traffic      ↑
CPU          ↑
Memory       ↑
Latency      ↑
Errors       ↑
```

If all change together, investigate the common event.

Possible causes:

```text
Traffic Surge
Deployment
Infrastructure Failure
Dependency Failure
Configuration Change
```

---

# 20. Timeline Reconstruction

During RCA, reconstruct the incident timeline.

Example:

```text
10:00
Normal

10:10
Deployment

10:12
CPU begins increasing

10:15
Latency increases

10:17
Database latency increases

10:18
Timeout errors begin

10:20
User failures increase
```

This timeline can be more valuable than a single dashboard screenshot.

---

# Timeline-Based RCA

```text
Change
 │
 ▼
System Behavior Changes
 │
 ▼
Performance Degrades
 │
 ▼
Errors Increase
 │
 ▼
User Impact
```

This helps establish a logical sequence.

---

# 21. Investigating Intermittent Problems

Some problems do not happen continuously.

Example:

```text
10:00 → Normal
10:05 → Normal
10:10 → Failure
10:15 → Normal
10:20 → Failure
```

These problems are more difficult to investigate.

Look for patterns such as:

```text
Specific Host
Specific Pod
Specific Endpoint
Specific Dependency
Specific Time
Specific Request Type
```

---

# 22. Finding Outliers

An outlier is a value significantly different from the rest.

Example:

```text
Host 1 → 40%
Host 2 → 42%
Host 3 → 43%
Host 4 → 95%
Host 5 → 41%
```

Host 4 is an obvious outlier.

DQL can help identify and investigate such patterns.

---

# 23. DQL Investigation Strategy

A practical investigation can be summarized as:

```text
1. Scope
2. Filter
3. Aggregate
4. Compare
5. Correlate
6. Investigate
7. Validate
8. Conclude
```

---

# Step 1 — Scope

Define:

```text
Environment
Service
Timeframe
```

Example:

```text
Production
Payment Service
10:00–11:00
```

---

# Step 2 — Filter

Remove irrelevant data.

```text
Production only
Relevant service
Relevant timeframe
```

---

# Step 3 — Aggregate

Calculate:

```text
Error Rate
Average Latency
P95
Request Rate
CPU
Memory
```

---

# Step 4 — Compare

Compare:

```text
Before vs During
Current vs Historical
Service A vs Service B
Host A vs Host B
```

---

# Step 5 — Correlate

Look for relationships between:

```text
Traffic
Errors
Latency
CPU
Memory
Dependencies
Deployments
```

---

# Step 6 — Investigate

Drill down:

```text
Service
   ↓
Endpoint
   ↓
Host
   ↓
Pod
   ↓
Container
   ↓
Dependency
```

---

# Step 7 — Validate

Test your hypothesis.

For example:

> The database caused the latency increase.

Validate by checking:

```text
Database latency
Database errors
Connection pool
Query behavior
Application latency
Timing
```

---

# Step 8 — Conclude

Document:

```text
What happened?
When?
Why?
Which component?
What was the impact?
What action is required?
```

---

# Real-World Scenario: Payment Failure

Consider this incident:

```text
Users report payment failures.
```

Investigation:

```text
Payment Service
      │
      ▼
Error Rate = 8%
      │
      ▼
Most errors = Timeout
      │
      ▼
Endpoint = /payment
      │
      ▼
Database latency = High
      │
      ▼
Database connection pool = Saturated
```

Possible root cause:

```text
Database connection saturation
```

But this must be confirmed with additional evidence.

---

# Real-World Scenario: Kubernetes Memory Problem

Incident:

```text
Payment pods repeatedly restart.
```

Investigation:

```text
Pod Restarts
     │
     ▼
Memory Usage
     │
     ▼
Memory steadily increases
     │
     ▼
Container reaches memory limit
     │
     ▼
OOM termination
     │
     ▼
Pod restart
```

This provides a strong evidence chain.

---

# Real-World Scenario: Capacity Risk

Historical data:

```text
CPU

Jan → 45%
Feb → 52%
Mar → 59%
Apr → 67%
May → 74%
Jun → 81%
```

Analysis:

```text
Increasing trend
       │
       ▼
Capacity threshold approaching
       │
       ▼
Forecast
       │
       ▼
Potential future saturation
```

Action:

```text
Scale resources
Optimize workload
Increase capacity
Review autoscaling
```

---

# Real-World Scenario: Deployment Regression

Timeline:

```text
10:00 → Normal
10:15 → Deployment
10:17 → Latency ↑
10:18 → Errors ↑
10:20 → User failures ↑
```

Investigation:

```text
Deployment Version
       │
       ▼
Affected Endpoint
       │
       ▼
Error Pattern
       │
       ▼
Dependency Calls
       │
       ▼
Application Behavior
```

Possible result:

```text
New deployment introduced regression.
```

Again, the conclusion should be supported by evidence.

---

# DQL's Role in SRE

DQL can be thought of as the investigation layer between telemetry and operational decisions.

```text
Telemetry
   │
   ▼
DQL Analysis
   │
   ├── Metrics
   ├── Logs
   ├── Events
   └── Traces
   │
   ▼
Evidence
   │
   ▼
SRE Decision
```

---

# DQL and Observability

DQL can help correlate the three major observability signals:

```text
Metrics
Logs
Traces
```

Example:

```text
Metric:
Latency ↑

      ↓

Trace:
Database span slow

      ↓

Log:
Database timeout
```

Together, these signals provide much stronger evidence.

---

# DQL and Smartscape

Dynatrace's topology information can provide context about relationships between entities.

Conceptually:

```text
Service
   │
   ├── Process
   ├── Host
   ├── Database
   └── Dependency
```

DQL analysis can then be used alongside topology information to investigate where abnormal behavior occurs.

---

# DQL and Davis AI

Davis AI can detect problems and provide contextual analysis.

DQL remains valuable when an engineer wants to:

```text
Investigate
Validate
Customize
Correlate
Extract historical data
Build specialized analysis
```

A useful mental model is:

```text
Davis AI
   ↓
Problem Detection
   ↓
DQL
   ↓
Detailed Investigation
   ↓
Evidence
```

---

# DQL and Predictive Monitoring

The complete relationship is:

```text
Telemetry
   │
   ▼
DQL
   │
   ▼
Historical Analysis
   │
   ├── Baselines
   ├── Trends
   ├── Seasonality
   └── Resource Usage
   │
   ▼
Predictive Analysis
   │
   ▼
Future Risk
   │
   ▼
Proactive Action
```

---

# Final SRE Investigation Checklist

```text
□ What happened?
□ When did it happen?
□ Which service was affected?
□ Which endpoint was affected?
□ What was the error rate?
□ What happened to latency?
□ What happened to traffic?
□ Was the system saturated?
□ Which host was affected?
□ Which pod/container was affected?
□ Did a dependency fail?
□ Did a deployment occur?
□ Is the behavior anomalous?
□ Does historical data show the same pattern?
□ Can the hypothesis be validated?
□ What was the business impact?
□ What action should be taken?
```

---

# Interview Questions

### How would you investigate a production incident using DQL?

Define the incident timeframe, identify the affected service, analyze errors and latency, correlate traffic and saturation, investigate hosts/pods/dependencies, compare recent changes, and validate the root-cause hypothesis.

### Why should you analyze multiple signals?

A single metric rarely proves a root cause. Correlating latency, traffic, errors, saturation, logs, traces, and events provides stronger evidence.

### How would you investigate a Kubernetes pod restart?

Check restart counts, container state, memory and CPU usage, logs, health checks, node conditions, and deployment changes.

### How would you investigate a deployment regression?

Compare telemetry before and after the deployment and identify changes in latency, errors, traffic, resource usage, and dependency behavior.

### How does DQL support capacity planning?

It can extract historical resource usage, create time series, identify growth trends, and provide data for forecasting future capacity requirements.

### How does DQL support predictive monitoring?

It provides the historical and analytical foundation needed for baselines, trend analysis, anomaly investigation, and forecasting.

### What is the most important principle during RCA?

Do not assume the first abnormal metric is the root cause. Build and validate an evidence chain.

---

# Key Takeaways

* DQL becomes most valuable when applied to real operational questions.
* Start investigations with a clear question and timeframe.
* Use the Four Golden Signals as an initial framework.
* Analyze services, endpoints, hosts, pods, containers, and dependencies.
* Correlate metrics, logs, traces, and events.
* Always investigate what changed around the incident.
* Use before-and-after comparisons for deployments.
* Use time-series analysis to identify trends and anomalies.
* Use hypothesis-driven investigation instead of guessing.
* Validate conclusions with multiple pieces of evidence.
* DQL supports both reactive incident investigation and proactive capacity planning.
* Historical DQL analysis can provide the foundation for predictive monitoring.
* The ultimate goal is not simply to query data, but to turn telemetry into reliable operational decisions.
