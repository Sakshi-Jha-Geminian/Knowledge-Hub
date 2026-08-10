# Case Study 06: Application Memory Leak

## Overview

This case study demonstrates how an SRE team investigates an application whose memory usage continuously increases over time.

The application initially operates normally. However, memory consumption gradually grows, garbage collection becomes more frequent, response times increase, and eventually the application container is terminated because it exceeds its memory limit.

The investigation covers:

* Memory utilization
* Heap usage
* Garbage collection
* Kubernetes memory limits
* Container restarts
* OOMKilled events
* Application metrics
* Distributed tracing
* Logs
* DQL
* Memory leaks
* Root cause analysis
* Capacity planning
* Preventive monitoring

The central lesson is:

> **A memory leak is often a gradual problem. Monitoring the trend is more valuable than looking only at the current memory percentage.**

---

# 1. Problem Statement

A production Java application begins experiencing intermittent slowdowns.

Users report:

> "The application becomes slower after running for several hours."

The application initially appears healthy.

```text
CPU:       45%
Memory:    55%
Errors:    0.1%
Latency:   200 ms
```

Several hours later:

```text
CPU:       65%
Memory:    88%
Errors:    1.5%
Latency:   1.2 sec
```

Eventually, the container restarts.

---

# 2. System Architecture

The application runs inside Kubernetes.

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
                    Order Service
                           │
                           ▼
                    Kubernetes Pod
                           │
                    ┌──────┴──────┐
                    │ Java App    │
                    │ JVM         │
                    └──────┬──────┘
                           │
                           ▼
                       Database
```

The application uses:

```text
Java
JVM
Spring Boot
Kubernetes
Database
```

---

# 3. Kubernetes Configuration

The application container has a memory limit.

Conceptually:

```yaml
resources:
  requests:
    memory: "1Gi"
  limits:
    memory: "2Gi"
```

This means the container is allowed to use up to approximately:

```text
2 GiB
```

If it exceeds the configured limit, Kubernetes may terminate the container.

---

# 4. Initial Application Health

Immediately after deployment:

```text
Memory Usage:     800 MB
Memory Limit:       2 GB
CPU Usage:          40%
P95 Latency:       200 ms
Restarts:            0
```

Everything appears healthy.

---

# 5. Memory Trend

After several hours:

```text
Time        Memory
----------------------
09:00       800 MB
10:00       950 MB
11:00       1.1 GB
12:00       1.3 GB
13:00       1.5 GB
14:00       1.7 GB
15:00       1.9 GB
```

The important observation is not simply:

> Memory is 1.9 GB.

The important observation is:

> **Memory continuously increases without returning to its previous baseline.**

---

# 6. Normal Memory Behavior

Memory usage naturally changes with workload.

For example:

```text
Traffic ↑
   ↓
Memory ↑
   ↓
Traffic ↓
   ↓
Memory ↓
```

This can be normal.

A potential memory leak looks different:

```text
Traffic →
   │
   ▼
Memory ↑
   │
   ▼
Memory ↑
   │
   ▼
Memory ↑
   │
   ▼
Memory ↑
```

Memory continues increasing despite relatively stable workload.

---

# 7. Golden Signals

The SRE team checks the four golden signals.

### Traffic

```text
2,000 RPS → 2,050 RPS
```

Traffic is stable.

### Latency

```text
200 ms → 1.2 sec
```

Latency is increasing.

### Errors

```text
0.1% → 1.5%
```

Errors are increasing.

### Saturation

Memory:

```text
55% → 95%
```

Memory is clearly becoming saturated.

CPU:

```text
45% → 65%
```

CPU has increased but is not yet critical.

---

# 8. Garbage Collection

Because the application runs on Java, the SRE team investigates JVM garbage collection.

The JVM uses garbage collection to reclaim memory occupied by objects that are no longer needed.

Conceptually:

```text
Application
    │
    ▼
Creates Objects
    │
    ▼
Heap Usage ↑
    │
    ▼
Garbage Collection
    │
    ▼
Unused Objects Removed
```

In a healthy application:

```text
Heap ↑
   ↓
GC
   ↓
Heap ↓
```

With a potential memory leak:

```text
Heap ↑
   ↓
GC
   ↓
Heap decreases slightly
   ↓
Heap ↑ again
   ↓
GC
   ↓
Heap decreases slightly
   ↓
Baseline keeps increasing
```

---

# 9. Heap Usage

The JVM heap is monitored.

Example:

```text
Heap Before GC:    1.6 GB
Heap After GC:     1.4 GB
```

Later:

```text
Heap Before GC:    1.8 GB
Heap After GC:     1.7 GB
```

Later:

```text
Heap Before GC:    1.95 GB
Heap After GC:     1.9 GB
```

The post-GC baseline continues increasing.

This is an important indicator.

---

# 10. Garbage Collection Frequency

The team notices:

```text
GC frequency:
Normal → Increasing → Very Frequent
```

Eventually:

```text
GC Pause Time ↑
```

The JVM is spending more time trying to reclaim memory.

This can contribute to application latency.

---

# 11. Relationship Between Memory and Latency

The observed pattern is:

```text
Memory ↑
   │
   ▼
GC Frequency ↑
   │
   ▼
GC Pause Time ↑
   │
   ▼
Application Processing ↓
   │
   ▼
Request Latency ↑
```

This explains why the application becomes slower before it actually crashes.

---

# 12. Kubernetes Monitoring

The SRE team checks pod-level metrics.

```text
Pod                    Memory
--------------------------------
order-service-1         1.9 GB
order-service-2         850 MB
order-service-3         820 MB
```

Only one pod is heavily affected.

This is a major clue.

---

# 13. Compare Pod Behavior

The affected pod:

```text
Pod 1
Memory:
800 MB
 ↓
1.1 GB
 ↓
1.4 GB
 ↓
1.7 GB
 ↓
1.9 GB
```

Other pods:

```text
Pod 2
800 MB → 850 MB

Pod 3
780 MB → 820 MB
```

The problem is isolated to a specific workload instance.

---

# 14. Pod Restart

Eventually:

```text
Memory Usage
     │
     ▼
  2.0 GB
     │
     ▼
Memory Limit
     │
     ▼
  OOMKilled
     │
     ▼
Container Restart
```

Kubernetes reports an OOM-related termination.

---

# 15. What Is OOMKilled?

OOM stands for:

> Out Of Memory

An `OOMKilled` event means the container was terminated because it exceeded the memory available to it under the relevant memory constraints.

Conceptually:

```text
Container
    │
    ▼
Memory ↑
    │
    ▼
Memory Limit Reached
    │
    ▼
OOM
    │
    ▼
Container Terminated
```

---

# 16. Restart Count

The pod status shows:

```text
Restarts:
0
 ↓
1
 ↓
2
 ↓
3
```

Repeated restarts indicate a serious stability issue.

However, restarting the application does not fix the underlying problem.

It only resets memory usage temporarily.

---

# 17. Why Restarts Hide the Problem

After restart:

```text
Memory:
1.95 GB
   ↓
Restart
   ↓
800 MB
```

The graph appears healthy again.

But after several hours:

```text
800 MB
 ↓
1.1 GB
 ↓
1.4 GB
 ↓
1.7 GB
 ↓
1.95 GB
```

The cycle repeats.

This is a strong indicator of a recurring memory-growth problem.

---

# 18. Application Logs

The logs show:

```text
WARN: Long GC pause detected
WARN: Request processing delayed
ERROR: Database operation timeout
```

Near the restart:

```text
Container terminated
```

These events correlate with the memory increase.

---

# 19. DQL Investigation

The SRE team can investigate application logs using DQL.

For example:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
```

The team can then compare error patterns with:

```text
Memory utilization
GC activity
Pod restarts
Latency
```

The exact fields depend on the telemetry configuration.

---

# 20. Deployment Correlation

The team checks whether the memory problem began after a deployment.

```text
Version 4.2.1
    │
    ▼
Normal Memory
```

New deployment:

```text
Version 4.3.0
    │
    ▼
Memory gradually increases
```

This creates a strong correlation.

---

# 21. Version Comparison

Example:

```text
Version      Memory After 6 Hours
----------------------------------
4.2.1             850 MB
4.3.0            1.8 GB
```

The new version is clearly behaving differently.

---

# 22. Suspected Code Change

The development team recently added an in-memory cache.

Conceptually:

```text
Request
   │
   ▼
Cache
   │
   ▼
Store Object
```

The cache stores objects for faster access.

However, the cache does not have an appropriate eviction policy.

---

# 23. Unbounded Cache

The problematic behavior is:

```text
New Request
    │
    ▼
New Cache Entry
    │
    ▼
Cache Size ↑
    │
    ▼
New Cache Entry
    │
    ▼
Cache Size ↑
```

There is no effective limit.

Eventually:

```text
Cache
  ↓
Millions of Objects
  ↓
Heap Usage ↑
  ↓
GC Pressure ↑
  ↓
Memory Exhaustion
```

---

# 24. Why Garbage Collection Cannot Fix It

Garbage collection only removes objects that are no longer reachable.

If the cache still references the objects:

```text
Cache
  │
  ├── Object A
  ├── Object B
  ├── Object C
  ├── Object D
  └── ...
```

Those objects are still reachable.

Therefore:

```text
GC
 ↓
Objects still referenced
 ↓
Objects cannot be reclaimed
```

The memory remains occupied.

---

# 25. Root Cause

The root cause is identified as:

> A newly introduced in-memory cache continuously retained objects without an effective size or expiration policy, causing heap usage to grow over time and eventually exceed the container's memory limit.

The complete chain is:

```text
New Release
     │
     ▼
Unbounded Cache
     │
     ▼
Objects Retained
     │
     ▼
Heap Usage ↑
     │
     ▼
Post-GC Baseline ↑
     │
     ▼
GC Frequency ↑
     │
     ▼
GC Pause Time ↑
     │
     ▼
Application Latency ↑
     │
     ▼
Memory Limit Reached
     │
     ▼
OOMKilled
     │
     ▼
Pod Restart
```

---

# 26. Immediate Mitigation

The team can reduce impact by:

```text
Rollback problematic release
Reduce cache size
Disable the cache temporarily
Increase memory temporarily if necessary
Scale application replicas
```

The preferred solution is to fix the memory-retention behavior.

---

# 27. Permanent Fix

The cache is redesigned with controls such as:

```text
Maximum cache size
Entry expiration
Eviction policy
Monitoring
Memory-aware configuration
```

Conceptually:

```text
Cache
  │
  ├── Maximum Entries
  ├── Expiration
  └── Eviction
```

This prevents unlimited memory growth.

---

# 28. Validation

After the fix:

```text
Time        Memory
----------------------
09:00       800 MB
10:00       900 MB
11:00       850 MB
12:00       920 MB
13:00       870 MB
14:00       900 MB
```

Memory now fluctuates around a stable baseline.

This is healthier than continuous growth.

---

# 29. Healthy vs Leaking Memory

### Healthy

```text
Memory
  │
  │     /\      /\
  │    /  \    /  \
  │___/    \__/    \____
  │
  └──────────────────── Time
```

Memory increases and decreases around a stable range.

### Potential Leak

```text
Memory
  │
  │             /
  │           /
  │         /
  │       /
  │     /
  │___/
  │
  └──────────────────── Time
```

Memory baseline continuously rises.

---

# 30. Capacity Planning

Memory capacity planning should consider:

```text
Current Memory
Memory Growth Rate
Traffic Growth
Dataset Size
Cache Size
Heap Size
Container Limits
Number of Replicas
```

For example:

```text
Current memory:
1 GB

Growth:
100 MB/hour
```

If the application has a 2 GB limit, the team can estimate when the threshold may be approached.

The goal is to act before the application reaches the limit.

---

# 31. Predictive Monitoring

A memory-growth trend can be used for proactive detection.

```text
Memory Growth
     │
     ▼
Trend Detection
     │
     ▼
Expected Threshold Breach
     │
     ▼
SRE Alert
     │
     ▼
Engineering Investigation
```

Instead of waiting for:

```text
OOMKilled
```

the team can investigate when memory growth becomes abnormal.

---

# 32. Alerting Strategy

Useful alerts include:

```text
Memory utilization sustained above threshold
Rapid memory growth
Increasing post-GC heap baseline
Frequent GC pauses
Repeated OOMKilled events
Increasing container restart count
```

The most useful alert is not necessarily:

```text
Memory > 90%
```

A stronger signal can be:

```text
Memory continuously increasing for several hours
```

because it captures the behavior of the problem.

---

# 33. Kubernetes Dashboard

A useful Kubernetes dashboard could show:

```text
┌────────────────────────────────────────┐
│ Order Service                          │
├────────────────────────────────────────┤
│ Memory                  1.8 GB / 2 GB  │
│ CPU                     58%            │
│ Restarts                2              │
│ OOMKilled               YES            │
│ GC Activity             HIGH           │
│ Memory Trend            INCREASING     │
└────────────────────────────────────────┘
```

---

# 34. Incident Timeline

```text
09:00
Version 4.3.0 deployed
       │
       ▼
09:15
Memory normal
       │
       ▼
10:30
Memory begins increasing
       │
       ▼
12:00
GC frequency increases
       │
       ▼
13:30
Latency begins increasing
       │
       ▼
14:30
Memory reaches 90%
       │
       ▼
15:00
Pod OOMKilled
       │
       ▼
15:05
Container restarts
       │
       ▼
16:00
Memory begins increasing again
       │
       ▼
16:30
SRE identifies repeated pattern
       │
       ▼
17:00
Rollback initiated
       │
       ▼
17:15
Memory stabilizes
       │
       ▼
Later
Cache implementation fixed
       │
       ▼
New version validated
```

---

# 35. Root Cause Analysis

### Immediate Cause

The container exceeded its memory limit.

### Technical Root Cause

An in-memory cache retained objects without an effective eviction or expiration policy.

### Trigger

Deployment of version 4.3.0.

### Contributing Factors

```text
No cache-size limit
Insufficient long-duration testing
No memory-growth alert
No automated leak detection
Insufficient monitoring of post-GC heap baseline
```

### Business Impact

```text
Application slowdowns
Pod restarts
Request failures
Potential transaction interruptions
Poor user experience
```

---

# 36. What Went Well

```text
Memory metrics were available
Kubernetes restart information was available
Version comparison identified the change
GC metrics provided useful evidence
Rollback temporarily restored stability
```

---

# 37. What Went Wrong

```text
Memory growth was not detected early
Cache had insufficient limits
Long-duration testing was inadequate
OOMKilled occurred before investigation completed
Memory trends were not part of deployment gates
```

---

# 38. Preventive Actions

| Action                   | Purpose                            |
| ------------------------ | ---------------------------------- |
| Memory-growth monitoring | Detect leaks early                 |
| Heap monitoring          | Track JVM behavior                 |
| GC monitoring            | Detect GC pressure                 |
| Cache limits             | Prevent unbounded growth           |
| Cache expiration         | Release stale objects              |
| Load testing             | Validate long-running behavior     |
| Soak testing             | Detect gradual leaks               |
| OOM alerts               | Detect severe memory issues        |
| Restart monitoring       | Identify instability               |
| Version comparison       | Detect release-related regressions |

---

# 39. SRE Concepts Demonstrated

This case study demonstrates:

```text
SRE
Observability
Memory Monitoring
JVM Monitoring
Garbage Collection
Heap Analysis
Kubernetes
OOMKilled
Container Restarts
Application Performance
DQL
Capacity Planning
Predictive Monitoring
Incident Response
Root Cause Analysis
Performance Engineering
```

---

# 40. Final Incident Flow

```text
                    NEW RELEASE
                         │
                         ▼
                  UNBOUNDED CACHE
                         │
                         ▼
                 OBJECTS RETAINED
                         │
                         ▼
                    HEAP GROWTH
                         │
                         ▼
                   GC PRESSURE
                         │
                         ▼
                 LATENCY INCREASE
                         │
                         ▼
                 MEMORY SATURATION
                         │
                         ▼
                    OOMKILLED
                         │
                         ▼
                  POD RESTART
                         │
                         ▼
                 TEMPORARY RECOVERY
                         │
                         ▼
                MEMORY GROWS AGAIN
                         │
                         ▼
                  ROOT CAUSE FOUND
                         │
                         ▼
                 CACHE OPTIMIZED
                         │
                         ▼
                MEMORY STABILIZED
```

---

# Final Takeaway

The most important lesson from this case study is:

> **A memory problem should be investigated as a trend, not just as a single utilization value.**

A healthy application can temporarily use high memory. A leaking application shows a persistent increase in its memory baseline.

The investigation moved through:

```text
Memory Trend
   ↓
Heap Usage
   ↓
Garbage Collection
   ↓
Kubernetes Pod
   ↓
OOMKilled
   ↓
Version Comparison
   ↓
Application Change
   ↓
Unbounded Cache
   ↓
Root Cause
   ↓
Rollback
   ↓
Permanent Fix
   ↓
Long-Duration Validation
   ↓
Preventive Monitoring
```

This demonstrates how **SRE combines application metrics, JVM behavior, Kubernetes telemetry, logs, tracing, and historical trends to detect and resolve memory-related production failures.**
