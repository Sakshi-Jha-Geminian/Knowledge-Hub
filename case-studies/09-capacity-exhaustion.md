# Case Study 09: Capacity Exhaustion and Proactive Scaling

## Overview

This case study demonstrates how an SRE team identifies a production system approaching its capacity limits before it causes a major outage.

Unlike incidents caused by sudden failures, this incident develops gradually:

```text
Traffic Growth
      ↓
Resource Utilization ↑
      ↓
Saturation ↑
      ↓
Latency ↑
      ↓
Errors ↑
      ↓
Potential Outage
```

The investigation combines:

* SRE principles
* Capacity planning
* Infrastructure monitoring
* Kubernetes
* Metrics
* Golden Signals
* Saturation analysis
* Forecasting
* Predictive monitoring
* Autoscaling
* Load testing
* DQL
* Incident prevention

The central lesson is:

> **Capacity problems are often predictable. Monitoring the trend is more valuable than waiting for the limit to be reached.**

---

# 1. Problem Statement

An e-commerce application has been operating normally for several months.

Traffic has steadily increased.

Initially:

```text
Average Traffic:
2,000 requests/sec
```

After several months:

```text
Average Traffic:
3,800 requests/sec
```

The infrastructure has not been scaled proportionally.

The SRE team notices that resource utilization is consistently increasing.

---

# 2. Application Architecture

The application runs on Kubernetes.

```text
                         Users
                           │
                           ▼
                    Load Balancer
                           │
                           ▼
                     API Gateway
                           │
                           ▼
                    Kubernetes Service
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           Pod 1         Pod 2         Pod 3
              │            │            │
              └────────────┼────────────┘
                           ▼
                       Database
```

The application currently runs:

```text
Desired Replicas: 3
```

---

# 3. Initial System State

At the beginning of the observation period:

```text
Traffic:          2,000 RPS
CPU:              40%
Memory:           50%
P95 Latency:      250 ms
Error Rate:       0.1%
```

Everything appears healthy.

---

# 4. Traffic Growth

Over time:

```text
Month 1:     2,000 RPS
Month 2:     2,300 RPS
Month 3:     2,700 RPS
Month 4:     3,000 RPS
Month 5:     3,400 RPS
Month 6:     3,800 RPS
```

The trend is clearly upward.

---

# 5. Resource Utilization

CPU utilization follows the traffic growth.

```text
Month 1:     40%
Month 2:     47%
Month 3:     55%
Month 4:     63%
Month 5:     72%
Month 6:     82%
```

The system is still functioning.

However:

```text
82% CPU
```

means the remaining headroom is becoming smaller.

---

# 6. What Is Saturation?

Saturation describes how close a resource is to its usable capacity.

Examples include:

```text
CPU saturation
Memory saturation
Disk saturation
Network saturation
Connection-pool saturation
Thread-pool saturation
Database connection saturation
```

For example:

```text
Capacity = 100 units
Current usage = 90 units

Utilization = 90%
```

As utilization approaches the practical limit, performance can degrade.

---

# 7. Golden Signals

The SRE team monitors the four Golden Signals.

### Traffic

```text
Traffic ↑
```

### Latency

```text
P95:
250 ms → 450 ms
```

### Errors

```text
0.1% → 0.6%
```

### Saturation

```text
CPU:
40% → 82%
```

The combination is concerning.

```text
Traffic       ↑
Latency       ↑
Errors        ↑
Saturation    ↑
```

---

# 8. Why Average CPU Is Not Enough

Suppose the average CPU is:

```text
82%
```

That does not mean every pod is at 82%.

The actual distribution might be:

```text
Pod 1: 65%
Pod 2: 78%
Pod 3: 95%
```

One pod may already be approaching its practical limit.

Therefore, SRE monitoring should examine:

```text
Average
Maximum
Percentiles
Per-pod utilization
Per-node utilization
```

---

# 9. Latency Relationship

As the system approaches saturation:

```text
CPU utilization ↑
        │
        ▼
Available capacity ↓
        │
        ▼
Requests wait longer
        │
        ▼
Latency ↑
```

This can produce a nonlinear performance degradation.

A system at 70% utilization may appear healthy.

At 90–95%, small traffic increases can cause much larger latency increases.

---

# 10. Queueing Effects

Suppose the application can process:

```text
4,000 requests/sec
```

Traffic reaches:

```text
3,800 requests/sec
```

There is only limited remaining capacity.

If traffic suddenly reaches:

```text
4,200 requests/sec
```

the incoming rate exceeds processing capacity.

Requests begin waiting.

Conceptually:

```text
Incoming Requests
       │
       ▼
    Queue
       │
       ▼
Application
       │
       ▼
Response
```

If the queue continues growing:

```text
Queue Length ↑
Latency ↑
Timeouts ↑
Errors ↑
```

---

# 11. Database Capacity

The application is not the only potential bottleneck.

The database shows:

```text
Connections:
65% → 82%

CPU:
55% → 75%

Query Latency:
20 ms → 45 ms
```

This indicates that database capacity is also being consumed.

The complete architecture must therefore be analyzed.

---

# 12. Connection Pool Saturation

Suppose the application has:

```text
Maximum DB Connections = 100
```

Current usage:

```text
82 connections
```

At higher traffic:

```text
Connection Requests
        ↓
Connection Pool
        ↓
Pool Saturation
        ↓
Requests Wait
        ↓
Latency ↑
```

Increasing application pods without considering database capacity could make the situation worse.

---

# 13. Scaling Investigation

The SRE team evaluates horizontal scaling.

Current:

```text
3 Pods
```

Possible future configuration:

```text
6 Pods
```

But the team asks:

> Can the database handle the additional traffic generated by six pods?

This is an important SRE capacity-planning question.

---

# 14. Horizontal Scaling

Horizontal scaling means adding more instances.

```text
Before:

        Service
       /   |   \
     Pod  Pod  Pod


After:

        Service
   /  /  |  \  \  \
 Pod Pod Pod Pod Pod Pod
```

Advantages:

```text
More processing capacity
Better availability
Traffic distribution
Failure tolerance
```

---

# 15. Vertical Scaling

Vertical scaling means increasing resources for existing instances.

Example:

```text
Before:
CPU = 1 vCPU
Memory = 2 GB

After:
CPU = 2 vCPU
Memory = 4 GB
```

Vertical scaling can help, but it has practical limits.

---

# 16. Horizontal vs Vertical Scaling

| Approach   | Meaning                | Advantage              | Limitation               |
| ---------- | ---------------------- | ---------------------- | ------------------------ |
| Horizontal | Add instances          | Scalable and resilient | More infrastructure      |
| Vertical   | Increase instance size | Simple                 | Hardware/resource limits |
| Both       | Combine strategies     | Flexible               | More complexity          |

For cloud-native applications, horizontal scaling is often preferred when the workload supports it.

---

# 17. Kubernetes HPA

A Horizontal Pod Autoscaler can adjust replica count based on metrics.

Conceptually:

```text
CPU ↑
  │
  ▼
HPA
  │
  ▼
Replicas ↑
  │
  ▼
Available Capacity ↑
```

For example:

```text
CPU < 60%  → 3 Pods
CPU ~70%   → 4 Pods
CPU ~80%   → 5 Pods
CPU ~90%   → 6 Pods
```

The exact thresholds should be determined through testing and workload characteristics.

---

# 18. Problem With Reactive Autoscaling

Suppose traffic suddenly increases:

```text
3,000 RPS
    ↓
5,000 RPS
```

CPU increases:

```text
60%
 ↓
90%
```

HPA detects the increase.

Then:

```text
HPA
 ↓
Create Pods
 ↓
Schedule Pods
 ↓
Start Containers
 ↓
Readiness Checks
 ↓
Receive Traffic
```

This process takes time.

During that time:

```text
Latency ↑
Errors ↑
```

Therefore, purely reactive scaling may not be enough for predictable traffic growth.

---

# 19. Predictive Scaling

If historical traffic shows:

```text
Monday morning:
Traffic increases around 09:00
```

the system can prepare capacity before the expected increase.

Conceptually:

```text
Historical Data
      ↓
Forecast
      ↓
Expected Traffic Increase
      ↓
Scale Before Peak
      ↓
Peak Arrives
      ↓
Sufficient Capacity
```

This reduces scaling delay.

---

# 20. Forecasting

Historical traffic:

```text
Week 1:  2,500 RPS
Week 2:  2,700 RPS
Week 3:  2,900 RPS
Week 4:  3,100 RPS
```

Trend:

```text
Traffic
  │
  │          /
  │        /
  │      /
  │    /
  │  /
  └──────────── Time
```

Forecast:

```text
Next period:
3,300–3,500 RPS
```

The organization can plan capacity accordingly.

---

# 21. Capacity Headroom

Suppose:

```text
Maximum sustainable capacity:
4,000 RPS
```

Current traffic:

```text
3,800 RPS
```

Headroom:

```text
4,000 - 3,800
= 200 RPS
```

Only 5% headroom remains.

This is risky.

---

# 22. Why Headroom Matters

Real production traffic is not perfectly stable.

Traffic can change because of:

```text
Marketing campaigns
Product launches
Seasonality
Business events
Unexpected popularity
External integrations
Batch jobs
Retries
Traffic spikes
```

Therefore, capacity planning should not target exactly 100% utilization.

---

# 23. Capacity Forecast

Suppose traffic grows approximately 10% per month.

Current:

```text
3,800 RPS
```

Expected next month:

```text
~4,180 RPS
```

But current sustainable capacity is:

```text
~4,000 RPS
```

The forecast indicates:

```text
Expected Traffic > Current Capacity
```

This means action is required before the next peak.

---

# 24. DQL Analysis

The SRE team can use DQL to analyze historical resource usage.

A conceptual example:

```text id="x2t7zq"
timeseries cpu_usage = avg(dt.host.cpu.usage), by:{dt.entity.host}
| fieldsAdd timeframe = timeframe
```

Another conceptual analysis could compare request volume and latency:

```text id="m7q2j4"
timeseries {
  requests = sum(dt.service.request.count),
  latency = avg(dt.service.request.duration)
}
```

Exact metric names depend on the telemetry schema and Dynatrace environment.

---

# 25. Correlation Analysis

The team compares:

```text
Traffic
CPU
Latency
Errors
Database Connections
```

Example:

```text
Traffic ↑
     │
     ▼
CPU ↑
     │
     ▼
DB Connections ↑
     │
     ▼
Latency ↑
     │
     ▼
Errors ↑
```

This establishes a relationship between workload growth and resource saturation.

---

# 26. Load Testing

Before increasing production capacity, the team performs controlled load testing.

For example:

```text
2,000 RPS → Healthy
3,000 RPS → Healthy
4,000 RPS → Acceptable
4,500 RPS → High latency
5,000 RPS → Errors increase
```

The results help estimate sustainable capacity.

---

# 27. Capacity Threshold

Based on load testing:

```text
Safe operating range:
< 4,000 RPS

Warning range:
4,000–4,500 RPS

Critical range:
> 4,500 RPS
```

These values are examples.

Real thresholds must be derived from actual system behavior and SLO requirements.

---

# 28. Proactive Action

The team decides to increase capacity before the predicted traffic peak.

Current:

```text
3 Pods
```

Planned:

```text
6 Pods
```

Database capacity is also reviewed.

The team confirms that the database can support the increased workload.

---

# 29. Capacity Expansion

After scaling:

```text
Pods:
3 → 6

CPU:
82% → 52%

P95 Latency:
450 ms → 270 ms

Error Rate:
0.6% → 0.1%
```

The system has regained sufficient headroom.

---

# 30. Capacity Dashboard

A useful capacity dashboard could show:

```text
┌─────────────────────────────────────────┐
│ Capacity Overview                       │
├─────────────────────────────────────────┤
│ Current Traffic            3,800 RPS    │
│ Forecast Traffic           4,200 RPS    │
│ Sustainable Capacity       5,000 RPS    │
│ CPU Utilization             52%         │
│ Memory Utilization          58%         │
│ DB Connections              60%         │
│ Capacity Headroom            24%        │
│                                         │
│ Forecast Status:        HEALTHY         │
└─────────────────────────────────────────┘
```

---

# 31. Predictive Monitoring Workflow

The organization establishes:

```text
Historical Metrics
       │
       ▼
Trend Detection
       │
       ▼
Forecast
       │
       ▼
Capacity Threshold
       │
       ▼
Early Warning
       │
       ▼
Scaling Decision
       │
       ▼
Capacity Expansion
       │
       ▼
Prevented Incident
```

---

# 32. Alerting Strategy

Instead of waiting for:

```text
CPU > 95%
```

the organization can alert when:

```text
Forecast indicates CPU > 85%
within the next planning window.
```

Similarly:

```text
Forecast DB connections > safe threshold
```

can trigger a capacity review.

Predictive alerts should be treated as early-warning signals rather than guaranteed predictions.

---

# 33. Incident Scenario Without Proactive Scaling

If no action is taken:

```text
Traffic
  ↓
4,000 RPS
  ↓
Capacity Limit
  ↓
Queue Growth
  ↓
Latency ↑
  ↓
Timeouts
  ↓
Retries
  ↓
More Traffic
  ↓
Further Saturation
  ↓
Outage
```

---

# 34. Incident Scenario With Predictive Monitoring

With predictive monitoring:

```text
Traffic Trend
      ↓
Forecast
      ↓
Capacity Risk Detected
      ↓
SRE Review
      ↓
Scale Infrastructure
      ↓
Traffic Peak
      ↓
Capacity Available
      ↓
Stable Service
```

The best incident is the one that never occurs.

---

# 35. Root Cause Analysis

### Immediate Risk

The system was approaching its sustainable processing capacity.

### Underlying Cause

Traffic growth outpaced infrastructure capacity growth.

### Contributing Factors

```text
Insufficient capacity headroom
Delayed capacity planning
Database capacity not considered initially
Reactive rather than proactive scaling
Increasing traffic trend not acted upon
```

### Business Impact Risk

If left unresolved:

```text
Higher latency
Request failures
Reduced availability
Poor user experience
Potential revenue loss
```

---

# 36. What Went Well

```text
Historical metrics were available
Traffic growth was measurable
Resource utilization trends were visible
Load testing established capacity limits
Predictive analysis identified future risk
Scaling restored sufficient headroom
```

---

# 37. What Went Wrong

```text
Capacity planning lagged behind business growth
Initial scaling focused primarily on application pods
Database capacity was not considered early enough
Reactive scaling could have introduced avoidable latency
```

---

# 38. Preventive Actions

| Action                       | Purpose                                   |
| ---------------------------- | ----------------------------------------- |
| Capacity forecasting         | Predict future resource requirements      |
| Traffic forecasting          | Anticipate workload growth                |
| HPA                          | Automatically scale workloads             |
| Load testing                 | Determine sustainable capacity            |
| Headroom monitoring          | Maintain safety margin                    |
| Database capacity monitoring | Prevent downstream bottlenecks            |
| Predictive alerts            | Detect future saturation                  |
| Regular capacity reviews     | Align infrastructure with business growth |

---

# 39. SRE Concepts Demonstrated

This case study demonstrates:

```text
SRE
Capacity Planning
Capacity Forecasting
Resource Utilization
Saturation
Horizontal Scaling
Vertical Scaling
Kubernetes HPA
Autoscaling
Load Testing
Predictive Monitoring
Forecasting
Golden Signals
DQL
Database Capacity
Headroom
Incident Prevention
```

---

# 40. Final Capacity Flow

```text
                    TRAFFIC GROWTH
                          │
                          ▼
                  RESOURCE UTILIZATION ↑
                          │
                          ▼
                     HEADROOM ↓
                          │
                          ▼
                     SATURATION ↑
                          │
                          ▼
                     LATENCY ↑
                          │
                          ▼
                      ERRORS ↑
                          │
                          ▼
                    OUTAGE RISK
                          │
                          │
               ┌──────────┴──────────┐
               │                     │
               ▼                     ▼
        Reactive Approach     Predictive Approach
               │                     │
               ▼                     ▼
        Wait for saturation      Forecast risk
               │                     │
               ▼                     ▼
        Scale after impact       Scale before impact
               │                     │
               ▼                     ▼
        Possible degradation    Stable service
```

---

# Final Takeaway

The most important lesson from this case study is:

> **Capacity planning should be proactive rather than reactive.**

SRE teams should continuously answer:

```text
How much capacity do we have?
How much are we currently using?
How fast is usage growing?
How much headroom remains?
When will we reach the safe limit?
What should we scale before that happens?
```

The complete approach is:

```text
Observe
   ↓
Measure
   ↓
Understand Trends
   ↓
Forecast
   ↓
Plan Capacity
   ↓
Scale
   ↓
Validate
   ↓
Monitor
```

This turns capacity management from a reactive firefighting activity into a **predictive SRE practice**.
