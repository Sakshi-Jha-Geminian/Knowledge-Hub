# Baselining in Predictive Monitoring

## Overview

Predictive monitoring depends on one fundamental concept:

```text
Knowing What "Normal" Looks Like
```

Before a monitoring platform can identify anomalies, forecast future behavior, or predict incidents, it must first understand the normal operating behavior of the system.

This understanding is known as a **Baseline**.

Baselining is the process of analyzing historical telemetry data to establish expected patterns, performance characteristics, and behavioral trends for applications, infrastructure, services, and business transactions.

Without baselines:

* Anomaly detection becomes unreliable
* Forecasting becomes inaccurate
* Alert noise increases
* False positives become common
* Predictive monitoring loses effectiveness

For this reason, baselining is considered the foundation of predictive monitoring.

---

# Learning Objectives

After completing this document, you should understand:

* What a baseline is
* Why baselines are important
* Static vs Dynamic Baselines
* How baselines are created
* Types of baselines
* Seasonal behavior analysis
* Adaptive baselines
* Dynatrace Smart Baselines
* Baselining in Kubernetes
* Financial trading system baselines
* Best practices

---

# What is a Baseline?

## Definition

A baseline is a representation of normal system behavior over time.

It defines what is expected under normal operating conditions.

Examples:

```text
Normal CPU Usage = 45%
Normal Memory Usage = 60%
Normal Response Time = 200ms
Normal Error Rate = 0.2%
```

These values become reference points.

Future behavior is compared against them.

---

# Why Baselining Matters

Consider a CPU utilization graph.

Scenario A:

```text
CPU = 80%
```

Is this good or bad?

The answer depends on the baseline.

Example 1:

```text
Normal CPU = 30%
Current CPU = 80%
```

Potential anomaly.

Example 2:

```text
Normal CPU = 75%
Current CPU = 80%
```

Probably normal.

Without a baseline, monitoring systems lack context.

---

# Role of Baselining in Predictive Monitoring

Baselines support:

```text
Anomaly Detection
Forecasting
Capacity Planning
Trend Analysis
Performance Monitoring
AI Models
```

Relationship:

```text
Telemetry
    │
    ▼
Baselining
    │
    ▼
Anomaly Detection
    │
    ▼
Forecasting
    │
    ▼
Predictive Monitoring
```

---

# Static Baselines

A static baseline is manually defined.

Example:

```text
CPU Threshold = 80%
Memory Threshold = 85%
Response Time = 500ms
```

Advantages:

* Simple
* Easy to configure

Disadvantages:

* Ignores changing workloads
* Generates false alerts
* Cannot adapt

---

# Dynamic Baselines

Dynamic baselines automatically learn normal behavior.

Example:

```text
Weekday Traffic = High
Weekend Traffic = Low
```

The baseline adjusts accordingly.

Benefits:

* More accurate
* Adapts to workload changes
* Reduces alert fatigue

Modern observability platforms prefer dynamic baselines.

---

# Historical Data Analysis

Baselines are created using historical data.

Typical data sources:

```text
Metrics
Logs
Traces
Events
Business Transactions
```

Example:

```text
30 Days CPU Data
60 Days Memory Data
90 Days Transaction Data
```

The more high-quality data available, the better the baseline.

---

# Components of a Baseline

A baseline typically includes:

### Average

Example:

```text
Average CPU = 45%
```

### Minimum

Example:

```text
Minimum CPU = 15%
```

### Maximum

Example:

```text
Maximum CPU = 75%
```

### Standard Deviation

Represents expected variation.

### Trend Information

Shows whether values are increasing or decreasing.

---

# Understanding Variability

Not all systems behave consistently.

Example:

```text
Response Time:
200ms
210ms
205ms
220ms
215ms
```

This variation is normal.

Baselines must account for expected fluctuations.

---

# Time-Based Baselining

System behavior changes throughout the day.

Example:

```text
09:00 AM - High Traffic
01:00 PM - Moderate Traffic
11:00 PM - Low Traffic
```

A single baseline may not be sufficient.

Time-aware baselines improve accuracy.

---

# Daily Patterns

Many applications exhibit daily usage cycles.

Example:

```text
Business Hours = High Activity
Night Hours = Low Activity
```

Monitoring systems learn these patterns automatically.

---

# Weekly Patterns

Example:

```text
Monday = High Usage
Saturday = Low Usage
Sunday = Lowest Usage
```

A dynamic baseline accounts for these differences.

---

# Monthly Patterns

Examples:

```text
Month-End Reporting
Payroll Processing
Financial Closing Activities
```

These create predictable spikes.

Baselines should incorporate recurring business events.

---

# Seasonal Patterns

Many businesses experience seasonal demand.

Examples:

```text
Holiday Shopping
Tax Season
Black Friday
Market Open Activity
```

Predictive monitoring must understand these patterns.

---

# Business-Aware Baselining

Baselines should include business metrics.

Examples:

```text
Orders Per Minute
Transactions Per Second
Trades Executed
Customer Sessions
```

Technical metrics alone are insufficient.

---

# Service-Level Baselining

Applications consist of multiple services.

Each service requires its own baseline.

Examples:

```text
Authentication Service
Payment Service
Trading Service
Notification Service
```

Each behaves differently.

---

# Infrastructure Baselining

Infrastructure metrics include:

```text
CPU
Memory
Disk
Network
Storage
```

Infrastructure baselines help identify resource exhaustion risks.

---

# Application Baselining

Application baselines focus on:

```text
Response Time
Error Rate
Throughput
Transaction Volume
```

These metrics directly affect user experience.

---

# Kubernetes Baselining

Kubernetes environments are highly dynamic.

Important metrics include:

```text
Pod Count
Node Utilization
CPU Requests
Memory Requests
Container Restarts
```

Baselines help predict cluster growth and resource shortages.

---

# Financial Trading System Baselining

Financial systems often exhibit predictable patterns.

Examples:

```text
Market Open Surge
Market Close Surge
End-of-Day Processing
Monthly Reporting
```

Trading latency baselines are particularly important.

Example:

```text
Normal Order Processing = 15ms
Current Latency = 45ms
```

Potential issue detected.

---

# Adaptive Baselining

Adaptive baselines continuously evolve.

Workflow:

```text
Collect Data
      │
      ▼
Learn Behavior
      │
      ▼
Adjust Baseline
      │
      ▼
Improve Accuracy
```

This is how modern AI-driven platforms operate.

---

# Dynatrace Smart Baselines

Dynatrace automatically creates dynamic baselines.

Capabilities include:

```text
Behavior Learning
Automatic Thresholds
Seasonality Detection
Anomaly Detection Support
```

Advantages:

* No manual tuning
* Continuous learning
* Reduced alert fatigue

---

# Example: Dynatrace Smart Baseline

Traditional Threshold:

```text
CPU > 80%
```

Dynatrace Baseline:

```text
Expected CPU = 35%
Current CPU = 65%
Deviation Detected
```

An alert may be generated even though CPU is below 80%.

This provides earlier warning.

---

# Baselining and Anomaly Detection

Anomaly detection depends entirely on baselines.

Example:

```text
Baseline Latency = 150ms
Current Latency = 450ms
```

Deviation identified.

Without a baseline, the platform cannot determine whether behavior is abnormal.

---

# Baselining and Forecasting

Forecasting uses baseline trends.

Example:

```text
Storage Growth = 5GB/Day
Current Usage = 900GB
Capacity = 1TB
```

Forecast:

```text
Storage Exhaustion In 20 Days
```

---

# Common Baselining Challenges

### Insufficient Data

Limited history reduces accuracy.

### Rapidly Changing Systems

Frequent changes can distort baselines.

### Seasonal Events

Unexpected events may create unusual patterns.

### Data Quality Issues

Incorrect telemetry affects predictions.

### Business Changes

New features may alter usage behavior.

---

# Best Practices

### Use Dynamic Baselines

Avoid static thresholds where possible.

### Collect Long-Term Data

More historical data improves accuracy.

### Include Business Metrics

Technical metrics alone are not enough.

### Monitor Seasonal Trends

Account for recurring patterns.

### Continuously Validate Baselines

Ensure they still reflect reality.

### Combine Human Expertise and AI

Engineers should validate automated insights.

---

# Interview Questions

### What is a Baseline?

A representation of normal system behavior used as a reference for monitoring and analysis.

### Why is Baselining Important?

Because anomaly detection and forecasting require an understanding of normal behavior.

### What is the Difference Between Static and Dynamic Baselines?

Static baselines are manually defined; dynamic baselines automatically adapt to changing behavior.

### How Does Dynatrace Create Baselines?

Using smart baselines that continuously learn from historical telemetry data.

### Why Are Seasonal Patterns Important?

Because normal behavior can vary by day, week, month, or business event.

### How Does Baselining Support Predictive Monitoring?

It provides the foundation for anomaly detection, forecasting, and AI-driven insights.

---

# Key Takeaways

* Baselining is the foundation of predictive monitoring.
* Baselines define normal system behavior.
* Dynamic baselines are more effective than static thresholds.
* Historical data is essential for accurate baselines.
* Time-based and seasonal patterns must be considered.
* Dynatrace Smart Baselines automatically learn system behavior.
* Forecasting and anomaly detection depend on baselines.
* Accurate baselines improve reliability and reduce alert noise.

---

# References

## Dynatrace Documentation

https://docs.dynatrace.com

## Dynatrace Davis AI Documentation

https://docs.dynatrace.com/docs/discover-dynatrace/platform/davis-ai

## Google SRE Book

https://sre.google/sre-book/

## OpenTelemetry Documentation

https://opentelemetry.io/docs/

## Kubernetes Documentation

https://kubernetes.io/docs/
