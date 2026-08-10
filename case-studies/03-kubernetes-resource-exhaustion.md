# Case Study 03: Kubernetes Resource Exhaustion

## Overview

This case study demonstrates how an SRE team investigates a production application affected by Kubernetes resource exhaustion.

The incident begins with users experiencing intermittent application failures. Monitoring shows increasing latency, pod restarts, and eventually unavailable application instances.

The investigation covers:

* Kubernetes resource requests and limits
* CPU and memory utilization
* Pod restarts
* `OOMKilled`
* Node pressure
* Container behavior
* Application impact
* Dynatrace observability
* Logs and metrics
* Root cause analysis
* Remediation
* Capacity planning

---

# 1. Problem Statement

A production application running on Kubernetes begins experiencing intermittent failures.

Users report:

> "The application is sometimes unavailable and requests are taking longer than usual."

The SRE dashboard shows:

```text
Metric                  Normal       Incident
------------------------------------------------
P95 Latency              250 ms       2.1 sec
Error Rate               0.2%         7.8%
Pod Restarts             0–1/day      35/hour
CPU Utilization          45%          82%
Memory Utilization       55%          94%
Available Pods           8            3
```

The most concerning signals are:

```text
Memory ↑
Pod Restarts ↑
Available Pods ↓
Error Rate ↑
Latency ↑
```

---

# 2. System Architecture

The application runs on a Kubernetes cluster.

```text
                         Users
                           │
                           ▼
                    Load Balancer
                           │
                           ▼
                       Ingress
                           │
                           ▼
                  Application Service
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
          Pod 1          Pod 2          Pod 3
            │              │              │
            └──────────────┼──────────────┘
                           │
                           ▼
                       Database
```

The normal deployment contains:

```text
8 application pods
```

During the incident:

```text
8 pods
   ↓
5 pods
   ↓
3 healthy pods
```

---

# 3. Kubernetes Resource Model

Every container can have resource requests and limits.

Conceptually:

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"
  limits:
    cpu: "1"
    memory: "1Gi"
```

### Request

The request represents the amount of resource Kubernetes uses when scheduling the pod.

### Limit

The limit represents the maximum resource allocation allowed for the container.

For memory, exceeding the limit can result in the container being terminated.

---

# 4. Initial Investigation

The SRE team starts with the application-level symptoms.

```text
Latency ↑
Errors ↑
Availability ↓
```

They then examine the Kubernetes workload.

The workload dashboard shows:

```text
Desired Pods:       8
Available Pods:     3
Unavailable Pods:   5
Restart Count:      35/hour
```

This indicates that the application problem may be caused by pod instability.

---

# 5. Pod Restart Investigation

The team examines restart reasons.

Example:

```text
Pod       Restarts       Reason
-------------------------------------
pod-1        0           Running
pod-2        1           Running
pod-3       12           OOMKilled
pod-4       10           OOMKilled
pod-5        8           OOMKilled
pod-6        4           CrashLoopBackOff
pod-7        0           Running
pod-8        0           Running
```

The pattern is immediately significant.

Several pods are being terminated because of memory exhaustion.

---

# 6. What Does OOMKilled Mean?

`OOMKilled` means a process was terminated because it exceeded the available memory allocation.

OOM stands for:

```text
Out Of Memory
```

Conceptually:

```text
Application
     │
     ▼
Memory Consumption ↑
     │
     ▼
Container Memory Limit
     │
     ▼
Limit Exceeded
     │
     ▼
Container Terminated
     │
     ▼
Pod Restart
```

---

# 7. Memory Trend

The SRE team examines historical memory usage.

```text
10:00 → 55%
10:10 → 60%
10:20 → 68%
10:30 → 74%
10:40 → 82%
10:50 → 91%
11:00 → 97%
11:05 → OOMKilled
```

This is not a random failure.

There is a clear upward trend.

```text
Memory
  ↑
  │                 /
  │              /
  │           /
  │        /
  │     /
  └──────────────────→ Time
```

---

# 8. Application-Level Memory Usage

The next question is:

> Why is the application consuming more memory?

Possible causes include:

```text
Memory leak
Large cache
Increasing traffic
Large response objects
Unbounded queues
Large batch processing
Poor garbage collection behavior
Configuration change
```

The SRE team does not immediately assume a memory leak.

It investigates the evidence.

---

# 9. Traffic Analysis

Request traffic is checked first.

```text
Normal Traffic:
1,000 RPS

Incident Traffic:
1,050 RPS
```

Traffic has increased only slightly.

Therefore:

```text
Traffic Increase
        ↓
Not large enough to explain
the memory growth
```

The investigation continues.

---

# 10. CPU Analysis

CPU utilization is also examined.

```text
Normal:
40–50%

Incident:
70–85%
```

CPU has increased, but not enough to explain the repeated memory termination.

The primary signal remains memory.

---

# 11. Check the Node

The SRE team checks whether the issue is limited to a node.

Example:

```text
Node       CPU       Memory
--------------------------------
Node 1     50%       55%
Node 2     58%       62%
Node 3     65%       91%
Node 4     48%       58%
```

Node 3 is under significantly higher memory pressure.

Several affected pods are scheduled on Node 3.

This raises another hypothesis:

> Is the node itself causing the problem?

---

# 12. Node Pressure vs Container Limit

These are different situations.

### Container memory limit

```text
Container
    │
    ▼
Memory > Container Limit
    │
    ▼
Container may be OOMKilled
```

### Node memory pressure

```text
Node
 │
 ├── Pod A
 ├── Pod B
 ├── Pod C
 └── Pod D
       │
       ▼
Total Memory Demand > Available Node Memory
       │
       ▼
Memory Pressure
```

The team must determine which situation is occurring.

---

# 13. Pod-Level Evidence

The affected pods show:

```text
Memory Limit:
1 GiB

Peak Usage:
1.03 GiB
```

The application is consistently reaching its configured memory limit.

Therefore, container-level memory exhaustion is a strong explanation.

---

# 14. Application Logs

Application logs show:

```text
Cache size increased
Loading customer data
Processing large batch
Memory allocation warning
```

The timing matches the memory increase.

This provides another clue.

---

# 15. Identify the Workload Change

The team checks recent application changes.

A new release was deployed earlier that day.

The release introduced an in-memory caching mechanism.

Before:

```text
Customer data
     ↓
Database
```

After:

```text
Customer data
     ↓
Application Cache
     ↓
Memory
```

The cache has no effective upper bound.

---

# 16. Root Cause

The root cause is identified as:

> A newly introduced in-memory cache grows without an effective size limit, causing application containers to exceed their configured memory limits and become `OOMKilled`.

The failure chain is:

```text
New Cache
   │
   ▼
Cache Growth
   │
   ▼
Memory Consumption ↑
   │
   ▼
Container Memory Limit Exceeded
   │
   ▼
OOMKilled
   │
   ▼
Pod Restart
   │
   ▼
Available Replicas ↓
   │
   ▼
Latency ↑
   │
   ▼
Errors ↑
   │
   ▼
User Impact
```

---

# 17. Why Kubernetes Restarted the Pods

Kubernetes attempts to maintain the desired state.

The deployment wants:

```text
8 replicas
```

When a pod crashes:

```text
Healthy Pods
    ↓
7
```

Kubernetes attempts to restore the desired state.

```text
Desired = 8
Available = 7
      │
      ▼
Create / restart workload
```

However, if the application continues consuming excessive memory, newly restarted pods may fail again.

This can create a cycle:

```text
Start
 ↓
Memory Growth
 ↓
OOMKilled
 ↓
Restart
 ↓
Memory Growth
 ↓
OOMKilled
```

---

# 18. CrashLoopBackOff

Repeated failures may eventually result in:

```text
CrashLoopBackOff
```

This indicates that Kubernetes is repeatedly restarting a container that keeps failing.

Conceptually:

```text
Container Start
      │
      ▼
Application Runs
      │
      ▼
Memory Exhaustion
      │
      ▼
Container Dies
      │
      ▼
Restart
      │
      ▼
Failure Again
      │
      ▼
Backoff
```

---

# 19. Dynatrace Investigation

Dynatrace provides multiple perspectives.

The SRE team can move through:

```text
Kubernetes Cluster
       │
       ▼
Node
       │
       ▼
Namespace
       │
       ▼
Workload
       │
       ▼
Pod
       │
       ▼
Container
       │
       ▼
Application
```

This allows infrastructure and application signals to be correlated.

---

# 20. Infrastructure + Application Correlation

The important relationship is:

```text
Memory Usage
     │
     ▼
Container
     │
     ▼
Pod Restart
     │
     ▼
Service Capacity
     │
     ▼
Request Errors
     │
     ▼
User Impact
```

This is much more useful than looking at memory utilization alone.

---

# 21. DQL Investigation

The SRE team can investigate Kubernetes-related telemetry using DQL.

For example, a conceptual query could inspect records associated with Kubernetes workloads:

```text
fetch dt.entity.cloud_application
| summarize count(), by:{dt.entity.cloud_application}
```

The exact dataset and fields depend on the telemetry available in the Dynatrace environment.

The important principle is:

> Use DQL to correlate Kubernetes entities, resource behavior, and application symptoms rather than relying on a single dashboard metric.

---

# 22. Investigating Logs

Application logs can be filtered for memory-related events.

Conceptually:

```text
fetch logs
| filter loglevel == "ERROR"
| filter contains(content, "memory")
```

The exact field names and available content depend on the log ingestion configuration.

The team looks for evidence such as:

```text
OutOfMemoryError
Heap exhaustion
Cache growth
Allocation failures
Container termination
```

---

# 23. Deployment Correlation

The team compares the memory trend with the deployment timeline.

```text
09:00
Old version
     │
     ▼
Memory stable

10:00
New version deployed
     │
     ▼
10:15
Memory begins increasing
     │
     ▼
10:45
Memory reaches 90%
     │
     ▼
11:00
OOMKilled
```

The correlation is strong.

---

# 24. Immediate Mitigation

The immediate priority is to restore service stability.

Possible actions:

```text
Rollback application
Reduce workload
Temporarily increase replicas
Disable cache feature
Increase memory limit if justified
```

The safest immediate action is usually to disable or roll back the problematic behavior rather than simply increasing resources.

---

# 25. Why Increasing Memory Alone May Be Wrong

Suppose:

```text
Current Limit = 1 GiB
```

The team changes it to:

```text
Limit = 4 GiB
```

The application may run longer before failing.

But if the cache continues growing:

```text
1 GiB
 ↓
2 GiB
 ↓
3 GiB
 ↓
4 GiB
 ↓
OOM
```

The underlying problem remains.

Therefore:

> Increasing capacity is not always the same as fixing the root cause.

---

# 26. Permanent Fix

The development team changes the cache implementation.

Possible controls include:

```text
Maximum cache size
Expiration time
Eviction policy
Maximum object size
Bounded data structures
Memory-aware caching
```

For example:

```text
Cache
 │
 ├── Maximum Entries
 ├── TTL
 └── Eviction Policy
```

This prevents unbounded growth.

---

# 27. Resource Configuration Review

The team also reviews Kubernetes resources.

Example:

```yaml
resources:
  requests:
    memory: "512Mi"
  limits:
    memory: "1Gi"
```

The values should be based on actual application behavior.

The team should not simply increase limits without understanding:

```text
Application requirement
Node capacity
Pod density
Workload variability
Growth projections
```

---

# 28. Horizontal Scaling

If memory usage is related to workload rather than a leak, horizontal scaling may help.

For example:

```text
8 Pods
   │
   ▼
Traffic increases
   │
   ▼
Memory per pod increases
   │
   ▼
Horizontal scaling
   │
   ▼
12 Pods
```

Each pod receives less workload.

However, scaling does not solve a true unbounded memory leak or cache-growth problem by itself.

---

# 29. Vertical Scaling

Vertical scaling increases resources available to each pod.

```text
Before:
1 GiB memory

After:
2 GiB memory
```

This can be useful when the application legitimately requires more memory.

But it should be based on evidence.

---

# 30. HPA Consideration

A Horizontal Pod Autoscaler can scale workloads based on metrics such as CPU utilization and, depending on configuration, other supported metrics.

Conceptually:

```text
Resource Usage ↑
       │
       ▼
HPA Decision
       │
       ▼
Replicas ↑
       │
       ▼
Load Distributed
```

However, CPU-based scaling may not detect every memory-related problem.

For memory-sensitive workloads, appropriate metrics and scaling strategies should be evaluated.

---

# 31. Capacity Planning

The incident reveals that resource planning must account for:

```text
Current Usage
+
Expected Growth
+
Peak Workload
+
Safety Margin
```

Historical memory data can reveal:

```text
Week 1 → 45%
Week 2 → 50%
Week 3 → 58%
Week 4 → 65%
Week 5 → 75%
Week 6 → 85%
```

This trend indicates increasing resource demand.

---

# 32. Predictive Risk

If memory consumption continues increasing:

```text
Current
85%
 │
 ▼
90%
 │
 ▼
95%
 │
 ▼
100%
 │
 ▼
OOMKilled
```

Predictive monitoring can potentially identify the risk before the resource limit is reached.

The proactive response could be:

```text
High Growth Trend
       │
       ▼
Predicted Resource Exhaustion
       │
       ▼
Investigation
       │
       ▼
Optimization / Scaling
```

---

# 33. Pod Availability

The SRE team should also monitor:

```text
Desired Replicas
Available Replicas
Ready Replicas
Unavailable Replicas
Restart Count
```

Example:

```text
Desired:       8
Available:     3
Ready:         3
Unavailable:   5
```

This directly indicates reduced service capacity.

---

# 34. Application Impact

The infrastructure problem eventually becomes an application problem.

```text
Memory Exhaustion
       ↓
Pod Termination
       ↓
Fewer Available Replicas
       ↓
Reduced Capacity
       ↓
Request Queuing
       ↓
Latency Increase
       ↓
Timeouts
       ↓
5xx Errors
       ↓
User Impact
```

This is a key SRE concept:

> Infrastructure symptoms should always be connected to service-level impact.

---

# 35. Incident Timeline

```text
09:00
Application healthy
       │
       ▼
10:00
New version deployed
       │
       ▼
10:15
Memory usage begins increasing
       │
       ▼
10:30
Memory reaches 75%
       │
       ▼
10:45
Memory reaches 90%
       │
       ▼
11:00
Pods begin getting OOMKilled
       │
       ▼
11:05
Available replicas decrease
       │
       ▼
11:10
Latency increases
       │
       ▼
11:15
Error rate increases
       │
       ▼
11:20
SRE begins investigation
       │
       ▼
11:35
Unbounded cache identified
       │
       ▼
11:45
Cache disabled / rollback
       │
       ▼
12:00
Memory stabilizes
       │
       ▼
12:15
Application fully recovered
```

---

# 36. Before vs After

| Metric         | During Incident | After Fix |
| -------------- | --------------: | --------: |
| Memory         |          94–98% |    50–60% |
| Pod Restarts   |         35/hour |   0–1/day |
| Available Pods |             3/8 |       8/8 |
| P95 Latency    |         2.1 sec |    240 ms |
| Error Rate     |            7.8% |      0.2% |

The improvement confirms that the remediation addressed the problem.

---

# 37. Root Cause Analysis

### Immediate Cause

Containers exceeded their memory limits.

### Technical Root Cause

An unbounded in-memory cache caused continuous memory growth.

### Trigger

A new application version introduced the cache behavior.

### Contributing Factors

```text
No cache size limit
Insufficient load testing
Insufficient memory-growth monitoring
No deployment guardrail
Resource limits not reviewed
```

### Business Impact

```text
Reduced application availability
Slow requests
Failed requests
Poor user experience
Potential transaction failures
```

---

# 38. What Went Well

```text
Dynatrace detected memory growth
Pod restart information was available
Deployment history helped narrow the timeline
Distributed application telemetry showed user impact
Rollback restored stability
```

---

# 39. What Went Wrong

```text
Unbounded cache was deployed
Load testing did not reproduce memory growth
No early warning for abnormal memory trend
Resource configuration was not reviewed
Deployment was not sufficiently guarded
```

---

# 40. Preventive Actions

| Action              | Purpose                  |
| ------------------- | ------------------------ |
| Bound cache size    | Prevent unlimited growth |
| Configure TTL       | Remove stale data        |
| Memory monitoring   | Detect abnormal growth   |
| Canary deployment   | Limit blast radius       |
| Load testing        | Detect resource problems |
| Resource review     | Match limits to workload |
| HPA evaluation      | Handle workload growth   |
| Memory trend alerts | Detect future risk       |
| Runbook             | Standardize response     |

---

# 41. SRE Concepts Demonstrated

This case study demonstrates:

```text
Kubernetes
Containers
Resource Requests
Resource Limits
CPU
Memory
OOMKilled
CrashLoopBackOff
Pod Restarts
Node Pressure
Horizontal Scaling
Vertical Scaling
HPA
Observability
Dynatrace
DQL
Incident Response
Root Cause Analysis
Capacity Planning
Predictive Monitoring
```

---

# 42. Final Incident Flow

```text
                     NEW RELEASE
                          │
                          ▼
                   UNBOUNDED CACHE
                          │
                          ▼
                  MEMORY CONSUMPTION ↑
                          │
                          ▼
                CONTAINER MEMORY LIMIT
                          │
                          ▼
                      OOMKilled
                          │
                          ▼
                    POD RESTART
                          │
                          ▼
                 AVAILABLE PODS ↓
                          │
                          ▼
                   SERVICE CAPACITY ↓
                          │
                          ▼
                    LATENCY ↑
                          │
                          ▼
                    ERRORS ↑
                          │
                          ▼
                    USER IMPACT
                          │
                          ▼
                 ROLLBACK / FIX
                          │
                          ▼
                  MEMORY STABILIZES
```

---

# Final Takeaway

The most important lesson from this case study is:

> **When Kubernetes workloads become unstable, do not look only at pod restarts. Trace the problem from resource consumption → container behavior → pod availability → service performance → user impact.**

The investigation moved through:

```text
User Symptoms
      ↓
Application Metrics
      ↓
Pod Availability
      ↓
Pod Restarts
      ↓
OOMKilled
      ↓
Memory Trend
      ↓
Application Logs
      ↓
Deployment Change
      ↓
Unbounded Cache
      ↓
Root Cause
      ↓
Rollback / Remediation
      ↓
Validation
      ↓
Capacity Planning
```

This case demonstrates how **Kubernetes observability and SRE practices connect infrastructure resource behavior to real application reliability problems**.
