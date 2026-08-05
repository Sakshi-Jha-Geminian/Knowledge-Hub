# Metrics

## Introduction

Metrics are the foundation of modern monitoring and observability systems. Every monitoring platform, whether Dynatrace, Prometheus, Grafana, Datadog, New Relic, or Splunk, relies heavily on metrics to measure system health, performance, reliability, and user experience.

Metrics provide numerical measurements about the behavior of systems over time. They help engineers understand what is happening inside applications, infrastructure, databases, networks, containers, and cloud environments.

Without metrics, it would be nearly impossible to monitor system performance, detect anomalies, forecast future capacity requirements, or establish Service Level Objectives (SLOs).

---

# What Are Metrics?

A metric is a numerical value collected at a specific point in time that represents the state or behavior of a system.

Examples:

```text id="d3t4f9"
CPU Usage = 72%

Memory Usage = 68%

API Response Time = 245 ms

Error Rate = 1.2%

Requests Per Second = 1500
```

These values continuously change and are collected at regular intervals.

Metrics help answer questions such as:

* Is the application healthy?
* Is CPU usage increasing?
* Is memory consumption stable?
* Are users experiencing latency?
* Is traffic increasing?
* Are errors occurring?

---

# Why Metrics Matter

Metrics provide visibility into systems.

They help organizations:

* Monitor health
* Detect failures
* Identify performance bottlenecks
* Measure reliability
* Track business outcomes
* Forecast future resource needs

Metrics are the primary input for:

* Dashboards
* Alerting
* Capacity Planning
* SLO Tracking
* Predictive Monitoring
* AI-Based Anomaly Detection

---

# Characteristics of Good Metrics

A useful metric should be:

### Measurable

Must provide quantifiable values.

### Reliable

Should accurately represent system behavior.

### Timely

Must be collected frequently enough to detect issues.

### Actionable

Should help engineers make decisions.

### Consistent

Should be measured the same way over time.

---

# Time Series Data

Metrics are typically stored as time series data.

A time series consists of:

```text id="9s2b1m"
Timestamp + Value
```

Example:

| Timestamp | CPU Usage |
| --------- | --------- |
| 10:00     | 40%       |
| 10:05     | 45%       |
| 10:10     | 51%       |
| 10:15     | 55%       |

This allows engineers to observe trends over time.

---

# Components of a Metric

A metric generally contains:

## Metric Name

Example:

```text id="gr4hy0"
cpu_usage
```

## Value

Example:

```text id="y1hn7o"
72
```

## Timestamp

Example:

```text id="23mjlwm"
2026-08-05 10:00:00
```

## Labels (Dimensions)

Example:

```text id="8ywksz"
host=server01
environment=production
region=us-east
```

Together:

```text id="r6kl5u"
cpu_usage{host="server01",environment="prod"} 72
```

---

# Types of Metrics

Most observability platforms categorize metrics into four primary types.

## 1. Counter

A Counter only increases.

Examples:

* Total Requests
* Total Errors
* Total Transactions

Example:

```text id="3xrw74"
10
20
35
50
90
```

The value never decreases unless reset.

---

### Counter Use Cases

* Request Count
* Login Count
* Orders Processed
* Failed Transactions

---

## 2. Gauge

A Gauge can increase or decrease.

Examples:

* CPU Usage
* Memory Usage
* Active Sessions

Example:

```text id="n7pm3q"
65%
72%
60%
81%
55%
```

---

### Gauge Use Cases

* CPU Utilization
* Memory Consumption
* Queue Length
* Thread Count

---

## 3. Histogram

A Histogram measures value distributions.

Example:

API Response Times:

```text id="pmwxxr"
0-100ms
100-200ms
200-500ms
500ms+
```

Useful for understanding latency patterns.

---

## 4. Summary

A Summary calculates statistical measurements.

Examples:

* Average
* Median
* Percentiles

Example:

```text id="w40dlo"
P50 = 120ms

P95 = 500ms

P99 = 1.2s
```

---

# Labels and Dimensions

Labels add context to metrics.

Example:

```text id="lzjw7v"
request_count
```

Without labels:

```text id="mns8f8"
1500
```

With labels:

```text id="mw8ljm"
request_count

service=payment

environment=prod

region=us-east
```

Labels allow filtering and aggregation.

---

# Infrastructure Metrics

Infrastructure metrics monitor system resources.

Examples:

### CPU

```text id="thz5r5"
CPU Utilization
CPU Load
CPU Wait Time
```

### Memory

```text id="u0pkjq"
Memory Usage
Memory Available
Swap Usage
```

### Disk

```text id="74mk7v"
Disk Usage
Disk Latency
Disk Throughput
```

### Network

```text id="0xdyje"
Packets Sent
Packets Received
Bandwidth Usage
Network Errors
```

---

# Application Metrics

Application metrics measure software behavior.

Examples:

```text id="7lu8po"
Request Count

Response Time

Error Rate

Throughput

Success Rate
```

---

# Business Metrics

Observability is not limited to infrastructure.

Examples:

```text id="a4mkg0"
Orders Processed

Revenue Generated

Transactions Completed

Cart Abandonment Rate

Payment Success Rate
```

Business metrics help connect technical health to business outcomes.

---

# Four Golden Signals

Google SRE defines Four Golden Signals.

## Latency

How long requests take.

Example:

```text id="v54rb0"
250ms
```

---

## Traffic

How much demand exists.

Example:

```text id="1lrn7n"
Requests Per Second
```

---

## Errors

Rate of failed requests.

Example:

```text id="rm60x5"
5XX Errors
```

---

## Saturation

How close a resource is to its limit.

Example:

```text id="3wszrm"
CPU = 95%
```

---

# RED Method

Popular for microservices monitoring.

## Rate

Request volume.

## Errors

Failed requests.

## Duration

Response time.

Example:

```text id="j3jkp4"
1000 Requests/sec

1% Error Rate

200ms Latency
```

---

# USE Method

Popular for infrastructure monitoring.

## Utilization

How busy a resource is.

## Saturation

How close it is to capacity.

## Errors

Failure conditions.

Example:

CPU Monitoring:

```text id="85gmbl"
Utilization = 80%

Saturation = 10%

Errors = 0
```

---

# Metrics in Dynatrace

Dynatrace automatically collects metrics from:

* Hosts
* Virtual Machines
* Containers
* Kubernetes
* Applications
* Databases
* Cloud Services

Examples:

```text id="2mtkvr"
builtin:host.cpu.usage

builtin:host.mem.usage

builtin:service.errors.server.rate

builtin:service.response.time
```

Dynatrace stores these metrics and uses them for:

* Dashboards
* Alerting
* Anomaly Detection
* Davis AI Analysis
* Forecasting

---

# Metrics and Predictive Monitoring

Predictive Monitoring depends heavily on metrics.

Example:

```text id="zvfqg0"
Disk Usage

Day 1 = 60%

Day 2 = 65%

Day 3 = 70%

Day 4 = 75%
```

Trend Analysis:

```text id="spmruw"
+5% Daily Growth
```

Forecast:

```text id="kld4d8"
Disk Full in 5 Days
```

Without metrics, forecasting would not be possible.

---

# Common Metric Pitfalls

## Too Many Metrics

Collecting excessive metrics increases cost and complexity.

---

## Missing Context

Metrics without labels are difficult to interpret.

---

## High Cardinality

Example:

```text id="s3bdr9"
user_id=12345
user_id=12346
user_id=12347
```

Too many unique labels can overwhelm monitoring systems.

---

## Focusing Only on Infrastructure

Application and business metrics are equally important.

---

# Real-World Example

Consider an online shopping platform.

Metrics show:

```text id="wy0j3r"
CPU = 40%

Memory = 55%

Error Rate = 12%

Response Time = 4 Seconds
```

Infrastructure appears healthy.

Application metrics reveal a problem.

This demonstrates why multiple metric categories are necessary.

---

# Interview Questions

### What is a Metric?

A metric is a numerical measurement representing the state or behavior of a system at a specific point in time.

### What is Time Series Data?

A sequence of metric values recorded over time.

### Difference Between Counter and Gauge?

Counter only increases.

Gauge can increase or decrease.

### What are the Four Golden Signals?

* Latency
* Traffic
* Errors
* Saturation

### What is High Cardinality?

A situation where metrics contain too many unique label values, increasing storage and processing requirements.

### Why Are Metrics Important for Predictive Monitoring?

Metrics provide the historical and real-time data required for trend analysis, forecasting, anomaly detection, and capacity planning.

---

# Key Takeaways

* Metrics are the foundation of monitoring and observability.
* Metrics are stored as time series data.
* Counters, Gauges, Histograms, and Summaries are the primary metric types.
* Metrics enable dashboards, alerting, forecasting, and anomaly detection.
* Dynatrace uses metrics extensively for Davis AI and Predictive Monitoring.
* Metrics provide the data required for SRE practices such as SLO tracking and capacity planning.

---

# References & Further Reading
## Official Documentation
   ### Prometheus
https://prometheus.io/docs/introduction/overview/
https://prometheus.io/docs/concepts/metric_types/

  ###  OpenTelemetry Metrics
https://opentelemetry.io/docs/concepts/signals/metrics/

  ###  Dynatrace Metrics
https://docs.dynatrace.com/docs/analyze-explore-automate/metrics

  ### Google SRE
https://sre.google/sre-book/monitoring-distributed-systems/

  ### Grafana
https://grafana.com/docs/grafana/latest/fundamentals/

## Books
* Site Reliability Engineering
* The Site Reliability Workbook
* Distributed Systems Observability
