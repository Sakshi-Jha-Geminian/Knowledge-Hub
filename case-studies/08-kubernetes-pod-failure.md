# Case Study 08: Kubernetes Pod Failure

## Overview

This case study demonstrates how an SRE team investigates a Kubernetes workload that repeatedly crashes and enters `CrashLoopBackOff`.

The application is deployed successfully, but one of its pods repeatedly terminates after startup.

The investigation covers:

* Kubernetes pods
* Deployments
* ReplicaSets
* Container lifecycle
* CrashLoopBackOff
* Readiness probes
* Liveness probes
* Startup probes
* Resource limits
* OOMKilled
* Application logs
* Kubernetes events
* Scheduling
* DQL
* Service availability
* Root cause analysis
* Preventive monitoring

The central lesson is:

> **A Kubernetes deployment being "successful" does not guarantee that the workload will remain healthy after startup.**

---

# 1. Problem Statement

A production application begins returning intermittent errors.

Users report:

> "Sometimes the application works, and sometimes requests fail."

The initial dashboard shows:

```text id="5e6kz2"
Application Status:   DEGRADED
Error Rate:           4.2%
Latency:              1.1 sec
Availability:         96.5%
```

The SRE team investigates the Kubernetes workload.

---

# 2. Application Architecture

The application runs in Kubernetes.

```text id="u2p5b7"
                    Load Balancer
                         │
                         ▼
                    Kubernetes
                         │
                         ▼
                    Service
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
            Pod 1      Pod 2      Pod 3
              │          │          │
              ▼          ▼          ▼
          Container   Container   Container
```

The Deployment is configured for:

```text id="3m2x6f"
Desired Replicas: 3
```

---

# 3. Initial Symptoms

The Kubernetes dashboard shows:

```text id="u0z0m8"
Pod 1     Running
Pod 2     Running
Pod 3     CrashLoopBackOff
```

The application is still partially available because two replicas remain healthy.

However, the third replica continuously fails.

---

# 4. What Is CrashLoopBackOff?

`CrashLoopBackOff` indicates that a container repeatedly starts, exits, and is restarted by Kubernetes, with increasing delays between restart attempts.

Conceptually:

```text id="x5x8x6"
Container Start
      │
      ▼
Application Starts
      │
      ▼
Application Crashes
      │
      ▼
Container Restarts
      │
      ▼
Application Crashes
      │
      ▼
Restart Backoff
      │
      ▼
CrashLoopBackOff
```

It is not itself the root cause.

It is a symptom that the container is repeatedly failing.

---

# 5. Pod Status

Example:

```text id="8n7h3g"
NAME             STATUS             RESTARTS
------------------------------------------------
app-7d8f1        Running                 0
app-7d8f2        Running                 0
app-7d8f3        CrashLoopBackOff        8
```

The restart count is increasing.

---

# 6. First Investigation

The SRE team does not immediately restart the pod manually.

Instead, they investigate:

```text id="w7s2vl"
1. Pod status
2. Container status
3. Restart reason
4. Application logs
5. Kubernetes events
6. Resource usage
7. Probe configuration
8. Recent deployments
```

This prevents the underlying problem from being hidden.

---

# 7. Container Exit Reason

The container status indicates:

```text id="8e2y3w"
Last State:
Terminated

Reason:
Error

Exit Code:
1
```

This indicates that the application process exited with an error.

The next step is to inspect the application logs.

---

# 8. Application Logs

The logs show:

```text id="3b6t5v"
Starting Order Service...

Loading configuration...

Connecting to database...

ERROR: Database connection failed

Application startup failed
```

This is an important clue.

The container itself is not necessarily the problem.

The application is failing during startup because it cannot establish a database connection.

---

# 9. Dependency Investigation

The architecture is:

```text id="1w7w3g"
Order Service
      │
      ▼
Database
```

The team checks database availability.

The database itself is healthy.

```text id="d9t9mw"
Database:
CPU:       45%
Memory:    55%
Connections: Normal
Availability: Healthy
```

Therefore, the team investigates why only one pod cannot connect.

---

# 10. Compare Pods

Pod 1:

```text id="zj4m83"
Application → Database
Connection: SUCCESS
```

Pod 2:

```text id="09p9gk"
Application → Database
Connection: SUCCESS
```

Pod 3:

```text id="kn8m4a"
Application → Database
Connection: FAILURE
```

The problem appears isolated to Pod 3.

---

# 11. Pod Environment

The team compares environment variables.

Expected:

```text id="yq4n48"
DB_HOST=database.internal
DB_PORT=5432
```

Affected pod:

```text id="c3w7j7"
DB_HOST=database-old.internal
DB_PORT=5432
```

The affected pod is using an outdated configuration value.

---

# 12. How Did This Happen?

The team checks the deployment configuration.

The current Deployment configuration contains:

```text id="7r2f5s"
DB_HOST=database.internal
```

However, Pod 3 was created using an older ReplicaSet configuration.

This suggests a rollout inconsistency or stale workload.

---

# 13. Deployment and ReplicaSet

A Kubernetes Deployment manages ReplicaSets.

Conceptually:

```text id="9d8l9p"
Deployment
    │
    ├── ReplicaSet v1
    │      └── Old Pods
    │
    └── ReplicaSet v2
           └── New Pods
```

During a rollout, multiple ReplicaSets may temporarily exist.

The SRE team checks whether the failing pod belongs to the expected ReplicaSet.

---

# 14. ReplicaSet Investigation

The result shows:

```text id="f7e5gy"
Pod 1 → ReplicaSet v2
Pod 2 → ReplicaSet v2
Pod 3 → ReplicaSet v1
```

This is unexpected.

The Deployment should eventually converge on the desired state.

---

# 15. Kubernetes Events

Kubernetes events provide additional information.

Conceptually:

```text id="v6y4c1"
Warning:
Failed to start container

Normal:
Back-off restarting failed container
```

Events help establish the lifecycle sequence.

---

# 16. Readiness Probe

The team reviews the readiness probe.

Example:

```yaml id="1xgq8j"
readinessProbe:
  httpGet:
    path: /health
    port: 8080
```

A readiness probe determines whether a pod should receive traffic.

If the application cannot connect to the database and reports unhealthy status:

```text id="m5v4wl"
Pod
 ↓
Readiness Probe FAIL
 ↓
Removed from Service endpoints
```

This protects other users from being routed to the unhealthy pod.

---

# 17. Liveness Probe

The liveness probe determines whether the container should be restarted when the application is considered unhealthy.

Conceptually:

```text id="h1y7r9"
Application
    │
    ▼
Liveness Probe
    │
    ├── Healthy → Continue
    │
    └── Failed → Restart
```

A poorly configured liveness probe can create unnecessary restart loops.

---

# 18. Startup Probe

For slow-starting applications, a startup probe can be used.

Conceptually:

```text id="4b0z0k"
Container Starts
      │
      ▼
Startup Probe
      │
      ▼
Application Initializes
      │
      ▼
Startup Complete
      │
      ▼
Liveness / Readiness
```

This prevents liveness checks from restarting an application before it has finished starting.

---

# 19. Probe Failure vs Application Crash

These are different situations.

### Application Crash

```text id="ubp0do"
Application
   ↓
Process exits
   ↓
Container stops
   ↓
Restart
```

### Liveness Failure

```text id="h9o6st"
Application Process
       ↓
Still Running
       ↓
Liveness Probe Fails
       ↓
Kubernetes Restarts Container
```

Correct diagnosis requires distinguishing between the two.

---

# 20. Resource Investigation

The SRE team checks resource usage.

```text id="08uhro"
CPU Request:      500m
CPU Limit:        1
Memory Request:   512Mi
Memory Limit:     1Gi
```

Current usage:

```text id="o6o1cy"
CPU:              400m
Memory:           450Mi
```

Resources are normal.

Therefore, the failure is not caused by resource exhaustion.

---

# 21. OOMKilled Check

The team verifies whether the container was killed because of memory pressure.

```text id="p3p8w0"
Termination Reason:
Error

Not:
OOMKilled
```

This eliminates one common Kubernetes failure mode.

---

# 22. Scheduling Investigation

The team checks where the pods are running.

```text id="2c8s7y"
Pod 1 → Node A
Pod 2 → Node A
Pod 3 → Node C
```

Node C is healthy.

```text id="q1b7qy"
CPU:     35%
Memory:  50%
Status:  Ready
```

Therefore, node health is not the primary problem.

---

# 23. Configuration Investigation

The configuration source is examined.

The application uses:

```text id="6t7l2w"
ConfigMap
Secret
Environment Variables
```

The team discovers that the old ReplicaSet references an outdated configuration.

This explains why the affected pod cannot connect to the database.

---

# 24. Root Cause

The root cause is:

> An outdated ReplicaSet created a pod using stale database configuration. The application failed during startup because it attempted to connect to an obsolete database endpoint, causing the container to exit and repeatedly restart.

The complete chain is:

```text id="x3jz4h"
Stale ReplicaSet
       │
       ▼
Old Configuration
       │
       ▼
Invalid Database Endpoint
       │
       ▼
Database Connection Failure
       │
       ▼
Application Startup Failure
       │
       ▼
Container Exit
       │
       ▼
Kubernetes Restart
       │
       ▼
Repeated Failure
       │
       ▼
CrashLoopBackOff
```

---

# 25. Immediate Mitigation

The team removes the stale workload and ensures that the Deployment uses the correct ReplicaSet/configuration.

Conceptually:

```text id="t4v9w8"
Old ReplicaSet
      │
      ▼
Remove / Replace
      │
      ▼
Current ReplicaSet
      │
      ▼
Correct Configuration
```

The affected pod is recreated.

---

# 26. Recovery

After recreation:

```text id="j6g7x8"
Pod 1     Running
Pod 2     Running
Pod 3     Running
```

Restart count:

```text id="4qk3cu"
Pod 3:
8 → 0
```

Database connectivity:

```text id="3p3s7w"
SUCCESS
```

---

# 27. Application Recovery

Before remediation:

```text id="l7l5i2"
Availability: 96.5%
Error Rate:    4.2%
Latency:       1.1 sec
```

After remediation:

```text id="v9h9n8"
Availability: 99.9%
Error Rate:    0.2%
Latency:       280 ms
```

The application returns to normal.

---

# 28. DQL Investigation

The team can analyze Kubernetes or application logs with DQL.

A conceptual example:

```text id="q3x1cy"
fetch logs
| filter contains(content, "startup failed")
| summarize count(), by:service.name
| sort count desc
```

The team can correlate:

```text id="3e9m1f"
Startup Failures
Pod Restarts
Deployment Events
Database Errors
```

The exact datasets and fields depend on the configured Dynatrace telemetry.

---

# 29. Why Manual Restart Alone Is Not Enough

Suppose the team simply runs:

```text id="j9a7a2"
Restart Pod
```

The pod starts with the same stale configuration.

Therefore:

```text id="a8c0js"
Restart
  ↓
Same Configuration
  ↓
Database Failure
  ↓
Crash
  ↓
Restart
```

The problem remains.

The correct approach is:

```text id="s5l0gn"
Identify Cause
     ↓
Fix Configuration
     ↓
Recreate Workload
     ↓
Validate
```

---

# 30. Deployment Validation

The organization improves deployment validation.

Before deployment:

```text id="n0n4u3"
Validate configuration
Validate secrets
Validate dependencies
Validate health endpoints
```

During deployment:

```text id="s8v8x7"
Monitor:
Pod status
Readiness
Latency
Errors
Restart count
```

After deployment:

```text id="x8c1u4"
Confirm:
All replicas healthy
No unexpected restarts
Dependencies reachable
Application SLO healthy
```

---

# 31. Kubernetes Health Checks

The team establishes appropriate health checks.

### Startup Probe

Used to determine whether the application has successfully started.

### Readiness Probe

Used to determine whether the pod should receive traffic.

### Liveness Probe

Used to determine whether the application should be restarted.

The distinction is important:

```text id="1v1b8h"
Startup
   ↓
Can application start?

Readiness
   ↓
Can application receive traffic?

Liveness
   ↓
Is application still functioning?
```

---

# 32. Monitoring Strategy

The SRE team monitors:

```text id="j0tqwb"
Pod Status
Restart Count
CrashLoopBackOff
OOMKilled
Readiness Failures
Liveness Failures
Startup Failures
Container Exit Codes
Deployment Status
Replica Availability
```

---

# 33. Alerting

Useful alerts include:

```text id="x9k8j1"
Pod enters CrashLoopBackOff
Restart count increases rapidly
Desired replicas != available replicas
Readiness failures increase
Container exits unexpectedly
OOMKilled detected
Deployment rollout stalls
```

---

# 34. Deployment Dashboard

A useful dashboard could contain:

```text id="g8j4v2"
┌────────────────────────────────────────┐
│ Kubernetes Application Health           │
├────────────────────────────────────────┤
│ Desired Pods              3             │
│ Available Pods            3             │
│ Ready Pods                3             │
│ Restarts                  0             │
│ CrashLoopBackOff          0             │
│ OOMKilled                 0             │
│ Readiness Failures        0             │
└────────────────────────────────────────┘
```

---

# 35. Capacity Planning

Kubernetes capacity planning should consider:

```text id="8x2qhk"
CPU Requests
Memory Requests
CPU Limits
Memory Limits
Number of Replicas
Node Capacity
Pod Density
Traffic Growth
Failure Scenarios
```

The system should have enough capacity to maintain service availability when one pod or node fails.

---

# 36. High Availability

Suppose there are three replicas:

```text id="0l2e0v"
Pod 1
Pod 2
Pod 3
```

If one fails:

```text id="p2r2bl"
Pod 1 ✓
Pod 2 ✓
Pod 3 ✗
```

the service may still operate.

This is one reason multiple replicas are important.

---

# 37. Failure Domain

For stronger resilience, replicas should not all run on the same failure domain.

For example:

```text id="u7j4yl"
Node A
 └── Pod 1

Node B
 └── Pod 2

Node C
 └── Pod 3
```

If Node A fails:

```text id="6m5l9e"
Pod 1 ✗
Pod 2 ✓
Pod 3 ✓
```

the service can continue operating.

---

# 38. Incident Timeline

```text id="e7t3az"
09:00
Application healthy
       │
       ▼
09:30
Configuration change introduced
       │
       ▼
09:35
New workload created
       │
       ▼
09:40
One pod starts failing
       │
       ▼
09:45
CrashLoopBackOff detected
       │
       ▼
10:00
Error rate increases
       │
       ▼
10:10
SRE investigates logs
       │
       ▼
10:20
Database connection failure identified
       │
       ▼
10:30
Stale ReplicaSet identified
       │
       ▼
10:40
Correct configuration applied
       │
       ▼
10:50
Pod recreated
       │
       ▼
11:00
All replicas healthy
       │
       ▼
11:15
Application fully recovered
```

---

# 39. Root Cause Analysis

### Immediate Cause

One application pod repeatedly crashed.

### Technical Root Cause

The pod used stale database configuration from an outdated ReplicaSet.

### Trigger

Configuration/deployment inconsistency.

### Contributing Factors

```text id="b0l4vl"
Stale ReplicaSet
Insufficient rollout validation
No automated configuration consistency check
Delayed CrashLoopBackOff alert
```

### Business Impact

```text id="l7k8xq"
Reduced application capacity
Increased error rate
Higher latency
Potential request failures
Reduced availability
```

---

# 40. What Went Well

```text id="0a7r0s"
Kubernetes restart information was available
Application logs showed startup failure
Healthy replicas maintained partial availability
Deployment and ReplicaSet information helped isolate the issue
Correct configuration restored the service
```

---

# 41. What Went Wrong

```text id="j9s4k3"
Stale configuration reached production
ReplicaSet inconsistency was not detected immediately
CrashLoopBackOff alerting was delayed
Deployment validation did not catch the configuration problem
```

---

# 42. Preventive Actions

| Action                   | Purpose                            |
| ------------------------ | ---------------------------------- |
| Configuration validation | Prevent invalid configuration      |
| Deployment health gates  | Stop unhealthy rollouts            |
| CrashLoopBackOff alerts  | Detect repeated failures           |
| Restart monitoring       | Identify unstable workloads        |
| Readiness probes         | Prevent bad pods receiving traffic |
| Liveness probes          | Recover unhealthy applications     |
| Startup probes           | Protect slow-starting applications |
| Replica distribution     | Improve availability               |
| Automated rollback       | Reduce incident duration           |

---

# 43. SRE Concepts Demonstrated

This case study demonstrates:

```text id="8j5xq2"
SRE
Kubernetes
Pods
Deployments
ReplicaSets
CrashLoopBackOff
Container Lifecycle
Readiness Probes
Liveness Probes
Startup Probes
ConfigMaps
Secrets
Resource Limits
OOMKilled
DQL
High Availability
Capacity Planning
Incident Response
Root Cause Analysis
```

---

# 44. Final Incident Flow

```text id="d0x2v8"
                  STALE CONFIGURATION
                         │
                         ▼
                    OLD REPLICASET
                         │
                         ▼
                INVALID DB ENDPOINT
                         │
                         ▼
              DATABASE CONNECTION FAIL
                         │
                         ▼
              APPLICATION STARTUP FAIL
                         │
                         ▼
                 CONTAINER EXITS
                         │
                         ▼
                   K8s RESTART
                         │
                         ▼
               CRASHLOOPBACKOFF
                         │
                         ▼
                AVAILABLE REPLICAS ↓
                         │
                         ▼
                 ERROR RATE ↑
                         │
                         ▼
                  USER IMPACT
                         │
                         ▼
              CONFIGURATION CORRECTED
                         │
                         ▼
                  POD RECREATED
                         │
                         ▼
                     RECOVERY
```

---

# Final Takeaway

The most important lesson from this case study is:

> **CrashLoopBackOff is a symptom, not a root cause. SRE investigation should determine why the container is repeatedly failing instead of simply restarting it.**

The investigation moved through:

```text id="d3r4mw"
Pod Status
   ↓
Restart Count
   ↓
Container Exit Reason
   ↓
Application Logs
   ↓
Dependency Check
   ↓
Configuration Comparison
   ↓
ReplicaSet Investigation
   ↓
Root Cause
   ↓
Configuration Fix
   ↓
Pod Recreation
   ↓
Validation
   ↓
Preventive Monitoring
```

This demonstrates how **Kubernetes orchestration data, application observability, deployment metadata, and SRE practices work together to diagnose containerized production failures.**
