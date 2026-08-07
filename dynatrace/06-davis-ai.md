# Davis AI

## Introduction

Modern IT environments generate enormous amounts of telemetry data every second.

Examples include:

* Metrics
* Logs
* Traces
* Events
* Topology Changes
* Infrastructure Alerts
* Application Errors

In large enterprises, thousands of alerts may be generated daily.

The challenge is not collecting data.

The challenge is identifying:

* What happened?
* Why did it happen?
* Which systems are affected?
* What is the root cause?
* What should be fixed first?

Traditional monitoring tools often rely on static thresholds and produce large volumes of alerts that require manual investigation.

Dynatrace addresses this challenge through Davis AI.

Davis AI is the intelligence engine of the Dynatrace platform that continuously analyzes telemetry data, topology relationships, dependencies, and historical behavior to detect problems, identify root causes, and reduce alert noise.

---

# Learning Objectives

After completing this document, you should understand:

* What Davis AI is
* How Davis AI works
* Causal AI
* Predictive AI
* Event Correlation
* Root Cause Analysis
* Problem Detection
* Impact Analysis
* Anomaly Detection
* Davis and Smartscape
* Davis and Service Flow
* Real-world use cases

---

# What is Davis AI?

## Definition

Davis AI is Dynatrace's artificial intelligence engine.

It continuously analyzes:

* Metrics
* Logs
* Traces
* Events
* Dependencies
* Topology Data

to automatically:

* Detect Problems
* Identify Root Causes
* Correlate Events
* Predict Future Issues
* Reduce Alert Noise

---

# Why Davis AI is Needed

Without AI:

```text
CPU Alert
Memory Alert
Database Alert
API Alert
Network Alert
```

Engineers receive many independent alerts.

The actual issue may be hidden.

---

With Davis AI:

```text
Database Failure
       │
       ▼
API Latency
       │
       ▼
Checkout Failure
       │
       ▼
Customer Impact
```

Davis groups related symptoms and identifies the root cause.

---

# Evolution of Monitoring Intelligence

## Traditional Monitoring

Focus:

```text
Thresholds
Static Rules
Manual Analysis
```

Example:

```text
CPU > 90%
Send Alert
```

Limitations:

* Too many alerts
* No context
* No dependency awareness

---

## Modern AI-Driven Monitoring

Focus:

```text
Behavior Analysis
Dependencies
Historical Trends
Root Cause Detection
```

Davis follows this approach.

---

# How Davis AI Works

Davis continuously analyzes telemetry from:

```text
OneAgent
   │
   ▼
Metrics
Logs
Traces
Events
Topology
```

and combines it with:

```text
Smartscape
Service Flow
Historical Baselines
```

to determine:

* What changed
* What failed
* Why it failed
* What is affected

---

# Core Components of Davis AI

## Problem Detection

Detects issues automatically.

Examples:

* Service Slowdown
* Increased Error Rate
* Host Failure
* Database Latency
* Resource Saturation

---

## Root Cause Analysis

Determines the underlying cause of a problem.

Example:

```text
Users Report Slowness
        │
        ▼
Checkout Service Slow
        │
        ▼
Database Latency
        │
        ▼
Storage Exhaustion
```

Davis identifies:

> Storage Exhaustion

as the root cause.

---

## Event Correlation

Davis groups related events.

Without Correlation:

```text
Alert 1
Alert 2
Alert 3
Alert 4
Alert 5
```

---

With Correlation:

```text
Single Problem
       │
       ▼
Root Cause
       │
       ▼
Affected Components
```

This significantly reduces alert fatigue.

---

## Impact Analysis

Davis determines:

* Which services are affected
* Which applications are affected
* Which users are affected
* Business impact

Example:

```text
Database Failure
      │
      ▼
Payment Service
      │
      ▼
Checkout Service
      │
      ▼
Revenue Impact
```

---

# Causal AI

One of the most important concepts in Dynatrace.

## What is Causal AI?

Causal AI identifies cause-and-effect relationships.

Instead of saying:

> These alerts happened together

Davis attempts to answer:

> Which event caused the problem?

Example:

```text
CPU Spike
     │
     ▼
Application Slowdown
     │
     ▼
User Complaints
```

Davis recognizes the chain of causation.

---

# Predictive AI

Davis does not only analyze current problems.

It can also identify trends.

Examples:

* Storage Growth
* Memory Growth
* Capacity Consumption
* Performance Degradation

This enables predictive monitoring.

---

# Davis and Baselines

Davis continuously learns normal behavior.

Examples:

```text
CPU Usually = 40%
Memory Usually = 55%
Response Time = 250 ms
```

These become baselines.

When behavior changes significantly:

```text
Response Time
250 ms
   │
   ▼
2.5 sec
```

Davis detects an anomaly.

---

# Anomaly Detection

Anomaly detection identifies unusual behavior.

Examples:

* Sudden Traffic Increase
* Unexpected CPU Spike
* Error Rate Surge
* Memory Leak

Anomalies often trigger further investigation.

---

# Davis and Smartscape

Smartscape provides:

```text
Topology
Dependencies
Relationships
```

Davis uses this information to understand:

* Upstream Services
* Downstream Services
* Impact Chains

Without Smartscape, Davis would have less context.

---

# Davis and Service Flow

Service Flow provides:

```text
Request Paths
Latency Information
Communication Flows
```

Davis uses this information to:

* Identify Bottlenecks
* Detect Slow Dependencies
* Locate Failing Services

---

# Davis and Kubernetes

Davis analyzes:

* Cluster Health
* Node Health
* Pod Failures
* Container Restarts
* Resource Saturation

Example:

```text
Node Failure
      │
      ▼
Pod Failures
      │
      ▼
Service Degradation
```

Davis identifies the relationship automatically.

---

# Davis and Cloud Monitoring

Supports:

* AWS
* Azure
* Google Cloud

Examples:

```text
EC2
RDS
Lambda
EKS
AKS
GKE
```

Cloud events become part of Davis analysis.

---

# Davis and Security

Davis can analyze:

* Security Events
* Vulnerabilities
* Misconfigurations
* Runtime Risks

when supported data sources are available.

---

# Real-World Example

An e-commerce platform experiences slow checkout performance.

Telemetry shows:

```text
Database Latency
      │
      ▼
Payment Service Slow
      │
      ▼
Checkout Slow
      │
      ▼
Customer Complaints
```

Without Davis:

Engineers investigate multiple systems.

---

With Davis:

```text
Problem Detected
       │
       ▼
Root Cause Identified
       │
       ▼
Affected Services Listed
       │
       ▼
Business Impact Calculated
```

Investigation time is dramatically reduced.

---

# Benefits of Davis AI

## Reduced Alert Noise

Related alerts are grouped.

---

## Faster Root Cause Analysis

Engineers spend less time investigating.

---

## Better Incident Response

Problems are prioritized automatically.

---

## Improved Reliability

Issues are detected earlier.

---

## Predictive Monitoring

Trends help identify future risks.

---

## Better Business Context

Technical issues are linked to business impact.

---

# Common Use Cases

## Incident Investigation

Identify the root cause quickly.

---

## Capacity Planning

Detect growth trends.

---

## Predictive Monitoring

Forecast resource exhaustion.

---

## Kubernetes Monitoring

Identify cluster issues.

---

## Cloud Operations

Analyze cloud service dependencies.

---

## Service Reliability

Monitor critical business services.

---

# Common Challenges

## Incomplete Monitoring Coverage

Davis relies on telemetry quality.

Missing data reduces effectiveness.

---

## Complex Environments

Large enterprises may contain:

* Thousands of Hosts
* Thousands of Services
* Millions of Transactions

Understanding AI outputs requires experience.

---

## False Assumptions

AI is powerful but not infallible.

Human validation remains important.

---

# Best Practices

### Deploy OneAgent Broadly

More visibility improves analysis.

---

### Maintain Accurate Topology

Smartscape quality affects Davis results.

---

### Review Davis Problems Regularly

Monitor trends and recurring issues.

---

### Investigate Root Causes

Focus on causes rather than symptoms.

---

### Combine Davis with SRE Practices

Use Davis insights during:

* Incident Response
* Capacity Planning
* Postmortems

---

# Interview Questions

### What is Davis AI?

Dynatrace's artificial intelligence engine for problem detection, root cause analysis, event correlation, and predictive monitoring.

---

### What Data Does Davis Analyze?

Metrics, logs, traces, events, topology data, and dependencies.

---

### What is Causal AI?

AI that identifies cause-and-effect relationships rather than simple correlations.

---

### How Does Davis Reduce Alert Noise?

By correlating related events into a single problem.

---

### What is Root Cause Analysis?

The process of identifying the underlying cause of an issue.

---

### Can Davis Perform Predictive Monitoring?

Yes.

It can analyze trends and identify potential future issues.

---

### How Does Davis Use Smartscape?

Smartscape provides topology and dependency context.

---

### How Does Davis Help SRE Teams?

It improves reliability, incident response, root cause analysis, and capacity planning.

---

# Key Takeaways

* Davis AI is the intelligence engine of Dynatrace.
* It analyzes metrics, logs, traces, events, and topology.
* It performs problem detection, event correlation, and root cause analysis.
* Causal AI helps identify cause-and-effect relationships.
* Davis supports predictive monitoring and capacity forecasting.
* Smartscape and Service Flow provide critical context for Davis analysis.
* Davis significantly reduces Mean Time To Resolution (MTTR).
* It is one of the most valuable capabilities of the Dynatrace platform.

---

# References

## Official Documentation

https://docs.dynatrace.com/

## Davis AI Documentation

https://docs.dynatrace.com/docs/discover-dynatrace/platform/davis-ai

## Dynatrace University

https://university.dynatrace.com/

## Dynatrace Community

https://community.dynatrace.com/

## Gartner AIOps Research

https://www.gartner.com/en/information-technology/glossary/aiops
