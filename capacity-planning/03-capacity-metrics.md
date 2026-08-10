# Capacity Metrics

## Overview

Capacity Metrics are measurements used to evaluate current resource consumption, system performance, workload growth, and future infrastructure requirements.

They provide the data required for:

* Capacity Planning
* Resource Optimization
* Scalability Analysis
* Performance Engineering
* Site Reliability Engineering (SRE)
* Cloud Operations
* Predictive Monitoring

Without accurate metrics, capacity planning becomes guesswork rather than data-driven decision making.

---

# Learning Objectives

After completing this document, you should understand:

* What Capacity Metrics are
* Types of Capacity Metrics
* Resource Metrics
* Performance Metrics
* Application Metrics
* Business Metrics
* SLI/SLO Capacity Indicators
* Kubernetes Capacity Metrics
* Cloud Capacity Metrics
* Forecasting Metrics
* Dynatrace Capacity Analytics

---

# What Are Capacity Metrics?

Capacity metrics are measurements that indicate how resources are being consumed and whether sufficient capacity exists to meet demand.

They help answer questions such as:

```text
How much capacity is available?
How quickly are resources growing?
When will resources be exhausted?
Can future workloads be supported?
```

---

# Capacity Metrics Categories

Capacity metrics can be grouped into:

```text
Resource Metrics
Performance Metrics
Application Metrics
Business Metrics
Forecasting Metrics
Reliability Metrics
```

Each category contributes to capacity planning.

---

# Resource Metrics

Resource metrics measure infrastructure utilization.

Examples:

```text
CPU
Memory
Storage
Network
```

These are the most commonly used capacity indicators.

---

# CPU Metrics

CPU metrics help identify processing bottlenecks.

Important metrics:

```text
CPU Utilization
CPU Saturation
Load Average
CPU Wait Time
CPU Ready Time
```

---

## CPU Utilization

Measures the percentage of CPU resources being consumed.

Example:

```text
Available CPU = 16 Cores
Used CPU = 12 Cores

Utilization = 75%
```

---

## CPU Saturation

Measures demand exceeding available CPU capacity.

Example:

```text
CPU Utilization = 70%
Run Queue Length = High
```

This indicates CPU contention.

---

## Load Average

Represents the number of processes waiting for CPU execution.

Example:

```text
Load Average = 20
CPU Cores = 8
```

This suggests CPU saturation.

---

# Memory Metrics

Memory metrics help identify memory-related constraints.

Important metrics:

```text
Memory Utilization
Heap Usage
Swap Usage
Memory Pressure
Page Faults
```

---

## Memory Utilization

Measures RAM consumption.

Example:

```text
Total RAM = 128 GB
Used RAM = 96 GB

Utilization = 75%
```

---

## Memory Pressure

Indicates that workloads require more memory than available.

Symptoms:

```text
Swapping
OOM Events
Application Restarts
```

---

# Storage Metrics

Storage metrics measure disk and database capacity.

Examples:

```text
Disk Utilization
Free Space
Storage Growth Rate
IOPS
Storage Latency
```

---

## Disk Utilization

Example:

```text
Storage Capacity = 20 TB
Used = 16 TB

Utilization = 80%
```

---

## Storage Growth Rate

Measures how quickly storage consumption increases.

Example:

```text
January = 10 TB
February = 12 TB
March = 14 TB
```

Growth Rate:

```text
2 TB Per Month
```

---

# Network Metrics

Network metrics evaluate communication capacity.

Examples:

```text
Bandwidth Utilization
Throughput
Latency
Packet Loss
Connection Count
```

---

## Throughput

Measures data transfer rates.

Example:

```text
800 Mbps
```

Higher throughput generally indicates higher workload demand.

---

## Network Latency

Measures communication delay.

Example:

```text
20 ms
```

Latency growth may indicate network saturation.

---

# Performance Metrics

Performance metrics measure system responsiveness.

Examples:

```text
Response Time
Latency
Throughput
Transaction Time
Error Rates
```

---

## Response Time

Measures how long a system takes to respond.

Example:

```text
Average Response Time = 250 ms
```

Capacity issues often increase response times.

---

## Throughput

Measures work completed over time.

Examples:

```text
Requests Per Second
Transactions Per Second
Orders Per Second
```

Throughput is a critical capacity indicator.

---

# Application Metrics

Application metrics measure workload demand.

Examples:

```text
Active Users
Concurrent Sessions
Request Volume
API Calls
Transaction Counts
```

---

## Concurrent Users

Example:

```text
Current Users = 10,000
Peak Users = 50,000
```

Capacity planning must consider peak demand.

---

## Request Rate

Example:

```text
5,000 Requests Per Second
```

Higher request rates generally require more resources.

---

# Business Metrics

Business metrics connect infrastructure capacity with business growth.

Examples:

```text
Customer Count
Orders Per Day
Revenue Transactions
Market Trades
```

---

## Example

```text
January = 1 Million Transactions
February = 1.5 Million Transactions
March = 2 Million Transactions
```

Infrastructure must scale accordingly.

---

# Reliability Metrics

Reliability metrics support SRE capacity planning.

Examples:

```text
Availability
Error Rate
Incident Count
MTTR
MTBF
```

---

## Availability

Example:

```text
99.95%
```

Capacity shortages often reduce availability.

---

## Error Rate

Example:

```text
Errors = 0.5%
```

Increasing error rates may indicate capacity limitations.

---

# SLI-Based Capacity Metrics

Service Level Indicators provide valuable capacity insights.

Examples:

```text
Latency
Availability
Success Rate
Throughput
```

---

## Latency SLI

Example:

```text
95th Percentile Latency
```

Capacity issues frequently appear first in latency metrics.

---

## Throughput SLI

Example:

```text
Transactions Per Second
```

Used to estimate future resource needs.

---

# Golden Signals and Capacity Metrics

Google's Four Golden Signals are closely related to capacity planning.

## Latency

Measures service responsiveness.

## Traffic

Measures demand.

## Errors

Measures failures.

## Saturation

Measures resource exhaustion.

These signals help identify capacity risks.

---

# Kubernetes Capacity Metrics

Kubernetes environments introduce additional metrics.

Examples:

```text
Node Utilization
Pod Utilization
Container Utilization
Resource Requests
Resource Limits
```

---

## Node Metrics

Examples:

```text
Node CPU Usage
Node Memory Usage
Node Disk Usage
```

---

## Pod Metrics

Examples:

```text
CPU Requests
Memory Requests
CPU Limits
Memory Limits
```

Capacity planning relies heavily on these metrics.

---

# Autoscaling Metrics

Used for Kubernetes and cloud capacity management.

Examples:

```text
CPU Thresholds
Memory Thresholds
Request Rates
Custom Metrics
```

Example:

```text
CPU > 70%
```

Triggers:

```text
Add More Pods
```

---

# Cloud Capacity Metrics

Cloud environments provide additional metrics.

Examples:

```text
Compute Utilization
Storage Consumption
Database Utilization
Network Traffic
```

---

## Cost Metrics

Examples:

```text
Cost Per Request
Cost Per Transaction
Cost Per User
```

These metrics help optimize cloud spending.

---

# Forecasting Metrics

Forecasting metrics help predict future demand.

Examples:

```text
Growth Rate
Trend Lines
Seasonality
Forecast Accuracy
```

---

## Growth Rate

Example:

```text
Month 1 = 1,000 Users
Month 2 = 1,200 Users
Month 3 = 1,500 Users
```

Growth:

```text
50% Increase
```

---

## Trend Analysis

Historical metrics reveal growth patterns.

Example:

```text
Traffic Increases 10% Monthly
```

Future capacity can be estimated.

---

# Predictive Capacity Metrics

Modern observability platforms generate predictive metrics.

Examples:

```text
Forecasted CPU Usage
Forecasted Storage Usage
Predicted Resource Exhaustion
Predicted Bottlenecks
```

These metrics support proactive operations.

---

# Capacity Thresholds

Organizations define thresholds to identify risks.

Example:

| Metric              | Warning | Critical |
| ------------------- | ------- | -------- |
| CPU Utilization     | 70%     | 90%      |
| Memory Utilization  | 75%     | 90%      |
| Storage Utilization | 80%     | 95%      |
| Network Utilization | 70%     | 90%      |

Thresholds help prioritize capacity actions.

---

# Capacity Dashboards

Capacity dashboards typically include:

```text
CPU Trends
Memory Trends
Storage Trends
Traffic Trends
Forecast Charts
```

Benefits:

```text
Visibility
Trend Analysis
Forecasting
Decision Support
```

---

# Dynatrace Capacity Metrics

Dynatrace automatically collects:

```text
Infrastructure Metrics
Application Metrics
Cloud Metrics
Kubernetes Metrics
Business Metrics
```

Capabilities:

```text
Trend Analysis
Forecasting
Anomaly Detection
Capacity Insights
```

---

# Davis AI Capacity Analytics

Davis AI continuously evaluates:

```text
Resource Consumption
Historical Trends
Future Capacity Risks
```

Outputs include:

```text
Forecasts
Recommendations
Risk Indicators
```

This supports predictive capacity planning.

---

# Financial Trading Example

A trading platform monitors:

```text
Orders Per Second
Trade Volume
CPU Usage
Database Throughput
Latency
```

Example:

```text
Market Open
     │
     ▼
Order Volume Increases
     │
     ▼
CPU Usage Rises
     │
     ▼
Latency Increases
```

Capacity metrics help identify scaling requirements before customer impact occurs.

---

# Common Interview Questions

### What are capacity metrics?

Measurements used to evaluate resource consumption and future capacity requirements.

### Which metrics are most important for capacity planning?

CPU, memory, storage, network, throughput, latency, and growth rate metrics.

### What is CPU saturation?

A condition where workloads are waiting for CPU resources due to insufficient capacity.

### How do SLIs support capacity planning?

SLIs such as latency and throughput reveal when capacity limits are being approached.

### Why are forecasting metrics important?

They help predict future demand and prevent capacity shortages.

---

# Key Takeaways

* Capacity metrics provide the foundation for data-driven capacity planning.
* Resource, performance, application, and business metrics must all be considered.
* SLI and Golden Signal metrics are valuable capacity indicators.
* Kubernetes and cloud environments introduce additional capacity metrics.
* Forecasting metrics help organizations scale proactively.
* Dynatrace and Davis AI provide advanced capacity analytics and predictive insights.
* Effective capacity planning depends on continuous measurement and trend analysis.
