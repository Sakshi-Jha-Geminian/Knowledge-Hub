# The Four Golden Signals

## Introduction

Modern distributed systems generate enormous amounts of telemetry data.

Organizations collect:

* CPU Metrics
* Memory Metrics
* Network Metrics
* Logs
* Traces
* Application Metrics
* Infrastructure Metrics

The challenge is not collecting data.

The challenge is determining:

> Which metrics truly indicate service health?

Google's Site Reliability Engineering (SRE) team introduced the concept of the Four Golden Signals to help engineers focus on the most important indicators of user experience and service reliability.

The Four Golden Signals provide a practical framework for monitoring systems and identifying problems before they significantly impact users.

They are considered one of the most important monitoring concepts in SRE.

---

# Learning Objectives

After completing this document, you should understand:

* What the Four Golden Signals are
* Why Golden Signals matter
* How Golden Signals support SRE
* How to measure each signal
* Real-world examples
* Golden Signals in Dynatrace
* Golden Signals in cloud-native systems
* Golden Signals in trading platforms
* Best practices and common mistakes

---

# What Are the Four Golden Signals?

The Four Golden Signals are:

```text
1. Latency
2. Traffic
3. Errors
4. Saturation
```

Together they provide a high-level view of service health.

---

# Golden Signal Overview

```text
User Request
      │
      ▼
Latency
      │
      ▼
Traffic
      │
      ▼
Errors
      │
      ▼
Saturation
```

Each signal answers a critical question.

---

# Signal 1: Latency

## Definition

Latency measures how long it takes for a request to be processed.

It answers:

> How fast is the service responding?

---

## Why Latency Matters

Users care deeply about responsiveness.

Examples:

* Slow banking transactions
* Delayed API responses
* Long checkout times
* Lagging trading systems

Even if a service is technically available, excessive latency may create a poor user experience.

---

## Example

```text
Request Received

12:00:00.000
```

Response Returned:

```text
12:00:00.150
```

Latency:

```text
150ms
```

---

# Latency Percentiles

Latency is often measured using percentiles.

Common examples:

```text
P50

Median Response Time
```

```text
P95

95% Requests Faster Than Value
```

```text
P99

99% Requests Faster Than Value
```

---

# Real Example

```text
P95 = 200ms

P99 = 500ms
```

Most users receive fast responses.

A small percentage experience slower requests.

---

# Latency in Trading Systems

Trading systems are highly sensitive to latency.

Examples:

```text
Order Submission

< 10ms
```

```text
Trade Execution

< 50ms
```

Small delays can impact business outcomes.

---

# Signal 2: Traffic

## Definition

Traffic measures demand placed on a service.

It answers:

> How much work is the system handling?

---

# Examples of Traffic

Examples include:

```text
Requests Per Second
```

```text
Transactions Per Minute
```

```text
Orders Per Second
```

```text
Active Users
```

---

# Traffic Example

```text
10,000 Requests/Second
```

This indicates system workload.

---

# Why Traffic Matters

Traffic helps teams understand:

* Usage trends
* Growth patterns
* Capacity requirements
* Scaling needs

Traffic increases often precede reliability issues.

---

# Trading Platform Example

Traffic may spike during:

```text
Market Open
```

or

```text
Major Economic Events
```

Understanding traffic patterns is essential.

---

# Signal 3: Errors

## Definition

Errors measure failed requests.

It answers:

> How many requests are failing?

---

# Error Examples

Examples include:

```text
HTTP 500
```

```text
Database Failures
```

```text
Timeouts
```

```text
Authentication Failures
```

---

# Error Rate Formula

```text
Failed Requests
----------------
Total Requests
```

---

# Example

```text
100 Failures

Out of

100,000 Requests
```

Error Rate:

```text
0.1%
```

---

# Why Errors Matter

Errors directly affect users.

High error rates often indicate:

* Service outages
* Dependency failures
* Configuration issues
* Infrastructure problems

---

# Error Budget Connection

Errors contribute directly to:

```text
SLIs
```

and

```text
Error Budget Consumption
```

This is why Golden Signals and SRE concepts are closely connected.

---

# Signal 4: Saturation

## Definition

Saturation measures resource utilization.

It answers:

> How close is the system to its limits?

---

# Examples

Examples include:

```text
CPU Utilization
```

```text
Memory Utilization
```

```text
Disk Usage
```

```text
Connection Pool Usage
```

---

# Why Saturation Matters

High saturation often leads to:

* Increased latency
* Service degradation
* Failures
* Capacity shortages

Saturation helps predict future problems.

---

# Example

```text
CPU Utilization

95%
```

System approaching limits.

---

# Saturation and Predictive Monitoring

Example:

```text
Month 1 = 50%

Month 2 = 65%

Month 3 = 80%

Month 4 = 92%
```

AI systems can predict:

```text
Resource Exhaustion
```

before service failure occurs.

---

# Golden Signals in Microservices

Example:

```text
User
 │
 ▼
Gateway
 │
 ▼
Order Service
 │
 ▼
Payment Service
 │
 ▼
Database
```

Each component should be monitored using all four signals.

---

# Golden Signals and Observability

Golden Signals rely on observability data.

Sources include:

```text
Metrics
Logs
Traces
```

Examples:

Latency:

```text
Trace Data
```

Errors:

```text
Logs + Metrics
```

Traffic:

```text
Metrics
```

Saturation:

```text
Infrastructure Metrics
```

---

# Golden Signals and OpenTelemetry

OpenTelemetry provides telemetry required to measure Golden Signals.

Examples:

* Request Duration
* Request Count
* Error Count
* Resource Utilization

This telemetry supports SRE monitoring.

---

# Golden Signals in Dynatrace

Dynatrace automatically collects many Golden Signal metrics.

Examples:

### Latency

* Response Time
* Service Response Time
* Trace Duration

### Traffic

* Request Volume
* Throughput

### Errors

* Failure Rate
* Error Rate
* Exceptions

### Saturation

* CPU Usage
* Memory Usage
* Resource Consumption

---

# Golden Signals and Davis AI

Dynatrace Davis AI uses Golden Signal data for:

* Anomaly Detection
* Root Cause Analysis
* Capacity Forecasting
* Predictive Monitoring

---

# Real-World Banking Example

Online Banking Application:

### Latency

```text
P95 < 300ms
```

### Traffic

```text
50,000 Requests/Minute
```

### Errors

```text
< 0.05%
```

### Saturation

```text
CPU < 75%
```

Monitoring these indicators provides a strong view of system health.

---

# Golden Signals Dashboard

Typical dashboard:

```text
Latency
Traffic
Errors
Saturation
```

Many organizations place these metrics at the top of operational dashboards.

---

# Common Mistakes

## Monitoring Only Infrastructure

CPU and Memory alone are insufficient.

---

## Ignoring Latency

Services may be available but unusably slow.

---

## Focusing Only on Error Counts

Traffic context is required.

---

## Ignoring Saturation Trends

Capacity issues often emerge gradually.

---

# Best Practices

1. Monitor all four Golden Signals.
2. Use percentiles for latency.
3. Track traffic trends.
4. Alert on error spikes.
5. Monitor saturation proactively.
6. Correlate Golden Signals with traces.
7. Integrate Golden Signals into SLO reporting.

---

# Interview Questions

### What Are the Four Golden Signals?

* Latency
* Traffic
* Errors
* Saturation

---

### Why Are Golden Signals Important?

They provide a simple but effective framework for monitoring service health.

---

### Which Golden Signal Measures User Experience Most Directly?

Latency.

---

### Which Golden Signal Helps Capacity Planning?

Saturation.

---

### How Do Golden Signals Support SRE?

They provide measurable indicators of reliability and service health.

---

### How Are Golden Signals Related to Observability?

Golden Signals are derived from observability telemetry such as metrics, logs, and traces.

---

### How Does Dynatrace Help Monitor Golden Signals?

Dynatrace automatically collects, analyzes, visualizes, and alerts on Golden Signal metrics.

---

# Key Takeaways

* Golden Signals are a foundational SRE monitoring framework.
* The four signals are Latency, Traffic, Errors, and Saturation.
* They focus on user experience and service reliability.
* Golden Signals support SLO management and Error Budget tracking.
* OpenTelemetry and Dynatrace provide telemetry required to measure them.
* Golden Signals are widely used in cloud-native, enterprise, banking, and trading environments.
* They are essential for proactive monitoring and predictive operations.

---

# References

## Official Sources

Google SRE Book

https://sre.google/sre-book/

Google SRE Workbook

https://sre.google/workbook/

Google Cloud SRE Documentation

https://cloud.google.com/architecture/devops/devops-sre

Dynatrace Documentation

https://docs.dynatrace.com/

OpenTelemetry Documentation

https://opentelemetry.io/docs/

## Further Reading

Cloud Native Computing Foundation

https://www.cncf.io/

Dynatrace Observability Guide

https://www.dynatrace.com/platform/observability/
