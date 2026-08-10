# Case Study 07: Network Latency Between Microservices

## Overview

This case study demonstrates how an SRE team investigates a production performance issue caused by increased network latency between microservices.

The individual services appear healthy:

* CPU is normal
* Memory is normal
* Pods are healthy
* Error rates are initially low

However, end-to-end request latency increases significantly.

The investigation uses:

* Golden Signals
* Distributed tracing
* Service flow
* Network metrics
* Kubernetes monitoring
* DNS investigation
* Load balancer analysis
* TCP connection behavior
* Logs
* DQL
* Root cause analysis
* Capacity planning
* Preventive monitoring

The central lesson is:

> **In distributed systems, a service can be healthy by itself while the communication between services is unhealthy.**

---

# 1. Problem Statement

Users report that the checkout application has become slow.

The initial application dashboard shows:

```text
CPU:             Normal
Memory:          Normal
Pod Status:      Healthy
Database:        Healthy
Error Rate:      Low
```

However:

```text
P95 Latency:
300 ms → 2.1 sec
```

The SRE team must determine where the additional latency is being introduced.

---

# 2. System Architecture

The application uses multiple microservices.

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
                    Checkout Service
                     /          \
                    /            \
                   ▼              ▼
            Inventory Service   Payment Service
                   │              │
                   ▼              ▼
              Inventory DB    Payment DB
```

The Checkout Service depends on both Inventory and Payment.

---

# 3. Normal Request Flow

A checkout request normally follows:

```text
User
 │
 ▼
API Gateway
 │
 ▼
Checkout Service
 │
 ├────► Inventory Service
 │
 └────► Payment Service
 │
 ▼
Response
```

Typical latency:

```text
API Gateway:          20 ms
Checkout Service:     80 ms
Inventory Service:    70 ms
Payment Service:      100 ms
Network overhead:     30 ms
--------------------------------
Total:                ~300 ms
```

---

# 4. Incident Symptoms

During the incident:

```text
Metric                  Normal       Incident
------------------------------------------------
P95 Latency              300 ms        2.1 sec
P99 Latency              600 ms        4.8 sec
Error Rate               0.2%          1.2%
CPU                      45%           48%
Memory                   55%           58%
Traffic                  2,000 RPS      2,050 RPS
```

The application resources remain healthy.

This suggests that the problem may exist outside the application compute resources.

---

# 5. Golden Signals

### Traffic

```text
2,000 RPS → 2,050 RPS
```

Traffic is stable.

### Latency

```text
300 ms → 2.1 sec
```

Major increase.

### Errors

```text
0.2% → 1.2%
```

Increasing.

### Saturation

CPU and memory remain normal.

Therefore:

```text
Traffic       →
Latency       ↑↑
Errors        ↑
Saturation    →
```

---

# 6. Service-Level Analysis

The SRE team compares service latency.

```text
Service                  P95
--------------------------------
API Gateway              2.0 sec
Checkout Service         1.9 sec
Inventory Service        150 ms
Payment Service          160 ms
```

At first glance, the downstream services appear healthy.

This creates an important question:

> Why is Checkout Service slow if Inventory and Payment are individually healthy?

---

# 7. Distributed Tracing

A trace provides the answer.

```text
API Gateway
    │
    ▼
Checkout Service
    │
    ├────► Inventory Service
    │         └── 120 ms
    │
    └────► Payment Service
              └── 150 ms

Network Wait:
~1.5 sec
```

The service execution itself is relatively fast.

The request is spending significant time waiting for communication.

---

# 8. Network Latency

Network latency represents the time required for data to travel between communicating systems.

Conceptually:

```text
Service A
   │
   │ Request
   ▼
Network
   │
   ▼
Service B
   │
   │ Response
   ▼
Network
   │
   ▼
Service A
```

If the network becomes slower:

```text
Request
   ↓
Network Delay ↑
   ↓
Service Processing
   ↓
Network Delay ↑
   ↓
Response
```

End-to-end latency increases even if both services are healthy.

---

# 9. Normal vs Incident Network Latency

Normal:

```text
Checkout → Inventory
~10 ms
```

Incident:

```text
Checkout → Inventory
~700 ms
```

Normal:

```text
Checkout → Payment
~15 ms
```

Incident:

```text
Checkout → Payment
~650 ms
```

The network path is clearly abnormal.

---

# 10. Service Dependency Flow

The service flow can be represented as:

```text
                 Checkout
                 /      \
                /        \
               ▼          ▼
          Inventory     Payment
               │          │
               ▼          ▼
              DB         DB
```

The important observation is:

```text
Checkout
   │
   ├── Network latency ↑
   │
   ├── Network latency ↑
   │
   ▼
Overall request latency ↑
```

---

# 11. Kubernetes Investigation

The application runs inside Kubernetes.

The SRE team checks:

```text
Pods
Nodes
Services
Ingress
Network policies
Service mesh
DNS
```

All pods are healthy.

Therefore, the investigation moves to the communication layer.

---

# 12. Node Distribution

The team discovers that the affected services are running on different nodes.

```text
Node A
 └── Checkout Service

Node B
 └── Inventory Service

Node C
 └── Payment Service
```

Communication therefore crosses node boundaries.

---

# 13. Cross-Node Communication

Conceptually:

```text
Node A
  │
  │ Network
  ▼
Node B
```

If the underlying network path becomes congested or degraded:

```text
Node A
  │
  ▼
Network Bottleneck
  │
  ▼
Node B
```

latency increases.

---

# 14. Network Saturation

The team checks network utilization.

Example:

```text
Node A Network:
60% → 92%

Node B Network:
55% → 90%

Packet Drops:
0.01% → 2.5%
```

Packet drops are particularly concerning.

---

# 15. Packet Loss

When packets are lost:

```text
Sender
  │
  ▼
Packet
  X
Dropped
```

The sender may retransmit.

Conceptually:

```text
Packet
  ↓
Dropped
  ↓
Retransmission
  ↓
Additional Delay
```

This can significantly increase latency.

---

# 16. TCP Retransmissions

For TCP-based communication, packet loss can trigger retransmissions.

Conceptually:

```text
Client
 │
 ├── Packet 1 ─────► Server
 │
 ├── Packet 2 ──X
 │
 └── Packet 3 ─────► Server
```

The missing packet may need to be retransmitted.

This introduces additional delay.

---

# 17. DNS Investigation

The team also checks DNS resolution.

A microservice may communicate using:

```text
inventory-service
```

which resolves to an IP address.

Conceptually:

```text
Application
    │
    ▼
DNS Lookup
    │
    ▼
Service IP
    │
    ▼
Network Connection
```

If DNS resolution becomes slow, request latency can increase.

---

# 18. DNS Latency

Normal:

```text
DNS Lookup:
2 ms
```

Incident:

```text
DNS Lookup:
400 ms
```

This could create substantial additional latency when applications perform frequent lookups.

However, the team must distinguish:

```text
DNS latency
```

from:

```text
Actual network transport latency
```

They are separate failure modes.

---

# 19. Connection Reuse

The team checks whether HTTP connection reuse is working properly.

Efficient communication:

```text
Connection
   │
   ├── Request 1
   ├── Request 2
   ├── Request 3
   └── Request 4
```

Less efficient behavior:

```text
Request
  ↓
Create Connection
  ↓
TCP Setup
  ↓
TLS Setup
  ↓
Request
  ↓
Close Connection
```

Repeated connection establishment can add latency.

---

# 20. TLS Overhead

If services communicate over HTTPS, TLS negotiation can add overhead.

Conceptually:

```text
Connection
    │
    ▼
TCP Setup
    │
    ▼
TLS Handshake
    │
    ▼
HTTP Request
```

If connections are frequently created and destroyed, this overhead can become significant.

---

# 21. Service Mesh Investigation

Suppose the environment uses a service mesh.

The communication path may be:

```text
Checkout Container
      │
      ▼
Checkout Sidecar
      │
      ▼
Network
      │
      ▼
Inventory Sidecar
      │
      ▼
Inventory Container
```

Now the investigation must include:

```text
Application
Sidecar
Service Mesh
Network
Destination Service
```

---

# 22. Sidecar Metrics

The team checks:

```text
Proxy latency
Proxy errors
Connection count
Request rate
Retries
Circuit breakers
```

Suppose the proxy shows:

```text
Retries:
Normal: 0.1%
Incident: 8%
```

This is significant.

---

# 23. Retries

Retries can create a cascading problem.

Suppose one request normally takes:

```text
100 ms
```

But the first attempt fails or times out.

The client retries:

```text
Attempt 1 → Timeout
Attempt 2 → Success
```

Total latency may become:

```text
Timeout
+
Retry
+
Successful Request
```

This can dramatically increase end-to-end latency.

---

# 24. Retry Amplification

Consider:

```text
1,000 requests
```

If each request generates two attempts:

```text
2,000 network requests
```

If several services retry independently, traffic can multiply.

Conceptually:

```text
Original Request
      │
      ▼
Service A
      │
      ▼
Service B
      │
      ├── Retry
      ├── Retry
      └── Retry
```

This can worsen network congestion.

---

# 25. Network Problem Chain

The investigation now reveals:

```text
Network Packet Loss
        │
        ▼
Connection Problems
        │
        ▼
Service Retries
        │
        ▼
Network Traffic ↑
        │
        ▼
Network Congestion ↑
        │
        ▼
Packet Loss ↑
        │
        ▼
Latency ↑↑
```

This is a feedback loop.

---

# 26. Logs

Application logs show:

```text
WARN: Upstream request timeout
WARN: Retrying request
WARN: Connection reset
ERROR: Upstream service unavailable
```

These logs correlate with the network metrics.

---

# 27. DQL Investigation

The team can investigate timeout-related logs using DQL.

Conceptually:

```text
fetch logs
| filter contains(content, "timeout")
| summarize count(), by:service.name
| sort count desc
```

The team can then compare the results with:

```text
Network latency
Packet loss
Retries
Request latency
```

Exact field names depend on the telemetry configuration.

---

# 28. Compare Healthy and Affected Nodes

The team compares network performance.

```text
Node       Packet Loss      Latency
-------------------------------------
Node A        2.5%           700 ms
Node B        0.1%            15 ms
Node C        0.1%            20 ms
```

Node A is clearly abnormal.

---

# 29. Identify the Network Path

The investigation finds:

```text
Checkout
   │
   ▼
Node A
   │
   ▼
Network Interface
   │
   ▼
Network Path
   │
   ▼
Node B
   │
   ▼
Inventory
```

The abnormal behavior occurs along the path involving Node A.

---

# 30. Root Cause

The root cause is identified as:

> A network interface/path associated with one Kubernetes node experienced packet loss and congestion, increasing communication latency between microservices. Increased retries amplified network traffic and further degraded request performance.

The complete chain is:

```text
Network Congestion
       │
       ▼
Packet Loss
       │
       ▼
TCP Retransmissions
       │
       ▼
Network Latency ↑
       │
       ▼
Service Timeouts
       │
       ▼
Retries ↑
       │
       ▼
Network Traffic ↑
       │
       ▼
Further Congestion
       │
       ▼
Application Latency ↑
       │
       ▼
User Impact
```

---

# 31. Immediate Mitigation

The SRE team can:

```text
Move workloads away from affected node
Drain problematic node
Reduce excessive retries
Scale affected services
Redirect traffic
Investigate network infrastructure
```

The immediate objective is to remove the affected workload from the unhealthy network path.

---

# 32. Node Drain

Conceptually:

```text
Affected Node
     │
     ▼
Stop Scheduling New Pods
     │
     ▼
Move Workloads
     │
     ▼
Healthy Nodes
```

After workloads are moved:

```text
Node A
  ↓
Removed from critical workload path
```

---

# 33. Recovery

After moving the workloads:

```text
Network Latency:
700 ms → 15 ms

Packet Loss:
2.5% → 0.1%

Retries:
8% → 0.2%

P95 Application Latency:
2.1 sec → 320 ms
```

The application returns to normal.

---

# 34. Why Application CPU Was Normal

This incident demonstrates why CPU alone is not enough.

The application was not computationally overloaded.

Instead:

```text
Application CPU
      ↓
    Normal

Network Path
      ↓
   Degraded
```

The application spent significant time waiting for network communication.

Therefore:

> Low CPU does not necessarily mean the application is healthy.

---

# 35. Preventive Monitoring

The team introduces monitoring for:

```text
Network latency
Packet loss
Network throughput
TCP retransmissions
Connection errors
DNS latency
Service-to-service latency
Retry rates
Timeouts
Node network utilization
```

---

# 36. Alerting Strategy

Useful alerts include:

```text
Sustained increase in service-to-service latency
Packet loss above threshold
TCP retransmission increase
Unexpected retry increase
Network interface saturation
DNS resolution latency increase
Upstream timeout increase
```

Alerts should correlate network symptoms with affected services.

---

# 37. Service Dependency Dashboard

A useful dashboard could look like:

```text
┌────────────────────────────────────────┐
│ Service Dependency Health              │
├────────────────────────────────────────┤
│ Checkout → Inventory      700 ms  ↑    │
│ Checkout → Payment         20 ms  ✓    │
│ Inventory → Database       30 ms  ✓    │
│ Payment → Database         25 ms  ✓    │
│                                        │
│ Packet Loss               2.5%   ↑     │
│ Retries                    8%    ↑     │
└────────────────────────────────────────┘
```

This makes the unhealthy dependency immediately visible.

---

# 38. Capacity Planning

Network capacity planning should consider:

```text
Current Traffic
Peak Traffic
Expected Growth
Service-to-Service Traffic
Cross-Node Traffic
Cross-Zone Traffic
Retries
Packet Size
Network Bandwidth
```

Capacity planning should also account for unexpected retry amplification.

---

# 39. Predictive Monitoring

Historical network metrics can identify trends.

For example:

```text
Network Utilization
     │
     ├── 60%
     ├── 68%
     ├── 75%
     ├── 82%
     ├── 88%
     └── 94%
```

The trend suggests the network may approach saturation.

A proactive approach is:

```text
Trend Detection
      ↓
Capacity Threshold Forecast
      ↓
Network Expansion / Optimization
      ↓
Prevent Incident
```

---

# 40. Incident Timeline

```text
09:00
Network healthy
       │
       ▼
10:00
Network utilization begins increasing
       │
       ▼
10:30
Packet loss detected
       │
       ▼
10:40
Service-to-service latency increases
       │
       ▼
10:50
Retries increase
       │
       ▼
11:00
Checkout latency increases
       │
       ▼
11:15
User complaints begin
       │
       ▼
11:20
SRE begins investigation
       │
       ▼
11:35
Affected node identified
       │
       ▼
11:45
Workloads moved
       │
       ▼
12:00
Network latency normal
       │
       ▼
12:15
Application latency recovered
       │
       ▼
Later
Network infrastructure root cause addressed
```

---

# 41. Root Cause Analysis

### Immediate Cause

Service-to-service network latency increased.

### Technical Root Cause

Packet loss and congestion affected a network path involving a Kubernetes node.

### Contributing Factors

```text
Network saturation
Packet loss
TCP retransmissions
High retry rate
Insufficient network-level alerting
```

### Business Impact

```text
Slow checkout
Request timeouts
Failed requests
Poor user experience
Potential transaction failures
```

---

# 42. What Went Well

```text
Distributed tracing exposed network wait time
Service-level metrics identified affected dependency
Node-level monitoring helped isolate the problem
Logs showed timeouts and retries
Workload migration restored service quickly
```

---

# 43. What Went Wrong

```text
Network degradation was detected late
Retry amplification increased network load
Service-to-service latency was not prominently monitored
Network capacity thresholds were insufficient
```

---

# 44. Preventive Actions

| Action                        | Purpose                         |
| ----------------------------- | ------------------------------- |
| Network latency monitoring    | Detect communication delays     |
| Packet-loss monitoring        | Detect network degradation      |
| Retry monitoring              | Detect retry amplification      |
| Service dependency dashboards | Visualize communication health  |
| Network capacity planning     | Prevent saturation              |
| Node-level monitoring         | Isolate unhealthy nodes         |
| Retry policies                | Prevent excessive amplification |
| Timeout tuning                | Reduce cascading failures       |
| Distributed tracing           | Identify network wait time      |

---

# 45. SRE Concepts Demonstrated

This case study demonstrates:

```text
SRE
Observability
Distributed Systems
Network Monitoring
Microservices
Distributed Tracing
Kubernetes
TCP
Packet Loss
Network Saturation
Retries
Timeouts
DNS
Service Mesh
DQL
Capacity Planning
Predictive Monitoring
Incident Response
Root Cause Analysis
```

---

# 46. Final Incident Flow

```text
                    NETWORK SATURATION
                           │
                           ▼
                       PACKET LOSS
                           │
                           ▼
                  TCP RETRANSMISSIONS
                           │
                           ▼
                  NETWORK LATENCY ↑
                           │
                           ▼
                   SERVICE TIMEOUTS
                           │
                           ▼
                       RETRIES ↑
                           │
                           ▼
                  NETWORK TRAFFIC ↑
                           │
                           ▼
                  FURTHER CONGESTION
                           │
                           ▼
                 APPLICATION LATENCY ↑
                           │
                           ▼
                      USER IMPACT
                           │
                           ▼
                    NODE ISOLATION
                           │
                           ▼
                       RECOVERY
```

---

# Final Takeaway

The most important lesson from this case study is:

> **In a distributed system, application performance depends not only on the health of individual services but also on the health of the communication paths between them.**

The investigation moved through:

```text
User Symptoms
   ↓
Application Latency
   ↓
Service Comparison
   ↓
Distributed Trace
   ↓
Network Wait Time
   ↓
Packet Loss
   ↓
Retries
   ↓
Affected Node
   ↓
Root Cause
   ↓
Node Isolation
   ↓
Recovery
   ↓
Preventive Monitoring
```

This demonstrates how **network observability, distributed tracing, Kubernetes monitoring, and SRE practices work together to identify failures that may not be visible from CPU or memory metrics alone.**
