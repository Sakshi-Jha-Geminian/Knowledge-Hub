# Case Study 11: Monitoring Blind Spot

## Overview

This case study demonstrates an incident where a production application experiences user-facing failures, but the monitoring system does not generate an appropriate alert.

The infrastructure appears healthy:

* CPU is normal
* Memory is normal
* Kubernetes nodes are healthy
* Pods are running
* Network is healthy

However, users are unable to complete an important business operation.

The incident exposes a fundamental observability problem:

> **A monitored system is not necessarily an observable system.**

The investigation focuses on:

* Monitoring gaps
* Observability
* Metrics
* Logs
* Traces
* Synthetic monitoring
* Business metrics
* SLOs
* Alerting
* False negatives
* Dashboard design
* DQL
* Root cause analysis
* Monitoring maturity

---

# 1. Problem Statement

An online banking application is operating in production.

The infrastructure dashboard shows:

```text
CPU:                  42%
Memory:               55%
Pods:                 Healthy
Network:              Healthy
Database CPU:         50%
```

No major infrastructure alerts are firing.

However, customers report:

> "I can log in, but I cannot transfer money."

The SRE team initially cannot see an obvious infrastructure problem.

---

# 2. Application Architecture

The application contains several services.

```text
                         Customers
                             │
                             ▼
                      Load Balancer
                             │
                             ▼
                        API Gateway
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
          Auth Service   Account Service   Transfer Service
                                             │
                                             ▼
                                       Payment Gateway
                                             │
                                             ▼
                                           Bank
```

The authentication and account functions work normally.

The transfer functionality is failing.

---

# 3. Initial Monitoring Status

The main infrastructure dashboard shows:

```text
┌─────────────────────────────────────────┐
│ Production Health                       │
├─────────────────────────────────────────┤
│ CPU                       42%     ✓     │
│ Memory                    55%     ✓     │
│ Pod Availability          100%    ✓     │
│ Network                   Normal  ✓     │
│ Database                  Healthy ✓     │
│ Error Rate                0.2%    ✓     │
└─────────────────────────────────────────┘
```

Everything appears healthy.

But users are experiencing failures.

---

# 4. The Monitoring Paradox

There are two different perspectives.

### Infrastructure Perspective

```text id="e8w6y2"
Everything looks healthy.
```

### User Perspective

```text id="r3k7v1"
Transfer does not work.
```

This means the monitoring system is measuring the wrong signals or insufficient signals.

---

# 5. Why Infrastructure Metrics Missed the Problem

CPU measures:

```text id="m8v3s5"
How much CPU is being used?
```

Memory measures:

```text id="p4n7x2"
How much memory is being used?
```

Pod health measures:

```text id="z6c2q9"
Is the container running?
```

None of these directly answer:

> **Can a customer successfully transfer money?**

This is the central observability gap.

---

# 6. Business Transaction

The important business transaction is:

```text id="a5y7k3"
Login
  ↓
Select Account
  ↓
Enter Amount
  ↓
Submit Transfer
  ↓
Payment Gateway
  ↓
Transfer Completed
```

The team realizes that this workflow needs its own monitoring.

---

# 7. Functional Success Rate

The SRE team checks the Transfer Service.

Application request metrics show:

```text id="x4z8n2"
HTTP Requests:
1,000,000
```

HTTP errors:

```text id="k6m5j1"
5xx:
0.2%
```

This appears healthy.

But a deeper application metric shows:

```text id="w3r9c6"
Transfer Success Rate:
72%
```

This is a major discrepancy.

---

# 8. Why HTTP Success Does Not Mean Business Success

Suppose the API returns:

```text id="u7c4m8"
HTTP 200 OK
```

but the business transaction contains:

```json id="b4h6r9"
{
  "status": "TRANSFER_FAILED"
}
```

From an infrastructure perspective:

```text id="m3n8q5"
HTTP 200 → Success
```

From a business perspective:

```text id="t7v2j9"
Transfer Failed → Failure
```

Therefore, HTTP status codes alone may not provide sufficient business-level observability.

---

# 9. Logs

The team searches application logs.

They find:

```text id="n6c8w2"
WARN Payment gateway response delayed

WARN Transfer processing timeout

INFO Request completed with HTTP 200
```

This is another clue.

The application is returning technically successful HTTP responses even though the business operation is unsuccessful.

---

# 10. Distributed Tracing

The team examines traces for transfer requests.

```text id="q2m7v8"
Transfer Request
      │
      ▼
Transfer Service
      │
      ▼
Payment Gateway
      │
      ├── Request sent
      │
      └── Response delayed
```

Trace duration:

```text id="f5k9w1"
Normal:
300 ms

Incident:
4.5 sec
```

The external dependency is introducing significant latency.

---

# 11. External Dependency

The Payment Gateway is outside the organization's infrastructure.

Therefore:

```text id="g8v3m5"
Internal Monitoring
       │
       ▼
Transfer Service ✓
       │
       ▼
External Gateway ✗
```

The internal infrastructure dashboard cannot directly show the external provider's health.

This is an important observability boundary.

---

# 12. Dependency Monitoring Gap

The organization monitors:

```text id="h6p4s2"
Application
Pods
Nodes
Database
Network
```

But does not adequately monitor:

```text id="c5z8r1"
External Payment Gateway
Transfer Success Rate
Payment Gateway Latency
Payment Timeout Rate
Business Transaction Success
```

The monitoring gap explains why no major alert fired.

---

# 13. Synthetic Monitoring

A synthetic test can simulate a real user workflow.

For example:

```text id="j7m4p9"
Open Application
      ↓
Login
      ↓
Select Account
      ↓
Initiate Transfer
      ↓
Submit Test Transaction
      ↓
Verify Result
```

If the transfer workflow fails:

```text id="k8x5c2"
Synthetic Test
      ↓
Failure
      ↓
Alert
```

This detects issues from the user's perspective.

---

# 14. Synthetic vs Infrastructure Monitoring

### Infrastructure Monitoring

Answers:

```text id="v6j2r4"
Are servers healthy?
Are pods running?
Is CPU normal?
Is memory normal?
```

### Synthetic Monitoring

Answers:

```text id="m9c5x7"
Can a user actually complete the workflow?
```

Both are important.

---

# 15. Golden Signals Limitation

The Golden Signals are:

```text id="h3n8w5"
Traffic
Latency
Errors
Saturation
```

They are extremely useful, but they may not capture every business failure.

For example:

```text id="p5k7x2"
HTTP Traffic      ✓
HTTP Latency      ✓
HTTP Errors       ✓
CPU Saturation    ✓
Transfer Success  ✗
```

Therefore, SRE monitoring should combine technical and business-level signals.

---

# 16. SLI for Transfer Success

A useful SLI could be:

```text id="x9m4c7"
Successful Transfers
---------------------
Total Transfer Attempts
```

For example:

```text id="d6k8v2"
Successful:
720,000

Total:
1,000,000
```

Success rate:

```text id="b3r7n5"
72%
```

This clearly indicates a serious problem.

---

# 17. SLO

Suppose the organization defines:

```text id="f8w2m6"
Transfer Success SLO:
99.5%
```

Current performance:

```text id="q4v7c9"
72%
```

The SLO is severely violated.

This provides a much stronger incident signal than infrastructure metrics alone.

---

# 18. Error Budget

If the SLO is:

```text id="n5x8m3"
99.5%
```

the allowed failure rate is:

```text id="p7c4k2"
0.5%
```

But actual failure rate is:

```text id="y6m9v1"
28%
```

The error budget is being consumed extremely quickly.

This should trigger immediate investigation.

---

# 19. DQL Investigation

The team can analyze transfer-related logs.

A conceptual DQL query:

```text id="r4h8k6"
fetch logs
| filter contains(content, "TRANSFER_FAILED")
| summarize failures = count()
```

They can also group failures:

```text id="s7c2m5"
fetch logs
| filter contains(content, "TRANSFER_FAILED")
| summarize failures = count(), by:service.name
| sort failures desc
```

The exact log fields depend on the telemetry implementation.

---

# 20. External Dependency Latency

The team creates a metric:

```text id="w3k9p6"
Payment Gateway P95 Latency
```

Normal:

```text id="t8m5v2"
250 ms
```

Incident:

```text id="c4r7n1"
4.2 sec
```

This correlates strongly with transfer failures.

---

# 21. Timeout Analysis

The Transfer Service has a timeout:

```text id="j5q8w3"
Timeout = 5 seconds
```

The external gateway frequently takes:

```text id="m6v2k9"
4–6 seconds
```

Therefore, some requests exceed the timeout.

```text id="y8r3c5"
Payment Gateway Slow
       ↓
Transfer Request Waits
       ↓
Timeout
       ↓
Transfer Failure
```

---

# 22. Retry Problem

The application retries failed payment requests.

Suppose:

```text id="z4n7x2"
Original Request
    ↓
Timeout
    ↓
Retry
    ↓
Timeout
    ↓
Retry
```

This creates additional external traffic.

The retry mechanism may make the external dependency even more overloaded.

---

# 23. Retry Amplification

Suppose:

```text id="k8m5c4"
100,000 transfers
```

If each request is retried twice:

```text id="w7p2r9"
300,000 gateway requests
```

instead of:

```text id="v6x3n8"
100,000 requests
```

This can worsen dependency degradation.

---

# 24. Circuit Breaker

A circuit breaker can prevent excessive calls to an unhealthy dependency.

Conceptually:

```text id="h5q9m2"
Normal
  ↓
Requests Allowed
  ↓
Failure Rate ↑
  ↓
Circuit Opens
  ↓
Requests Blocked/Fast Failed
```

This protects the application from continuously waiting for a failing dependency.

---

# 25. Graceful Degradation

Depending on business requirements, the application might provide:

```text id="p4r8x7"
"Transfer temporarily unavailable.
Please try again later."
```

instead of:

```text id="n7m3c5"
Request hangs for 30 seconds.
```

Graceful degradation can improve user experience during dependency failures.

---

# 26. Root Cause

The root cause is:

> The external payment gateway experienced significant latency, causing transfer requests to time out. The organization's monitoring focused heavily on infrastructure health and HTTP-level errors, so it failed to detect the business transaction failure quickly.

The complete chain:

```text id="u5r8m2"
External Gateway Degradation
          │
          ▼
Gateway Latency ↑
          │
          ▼
Transfer Timeout
          │
          ▼
Transfer Failure
          │
          ▼
Business Success Rate ↓
          │
          ▼
Customer Impact
```

The monitoring gap was:

```text id="c8m4v7"
No strong business transaction monitoring
```

---

# 27. Immediate Mitigation

The SRE team:

```text id="q7n3x9"
Confirms external gateway degradation
Reduces unnecessary retries
Adjusts traffic if possible
Enables circuit breaker
Communicates with provider
Monitors transfer success rate
```

The goal is to protect customers and prevent retry amplification.

---

# 28. Recovery

The external provider restores normal performance.

Metrics recover:

```text id="x2v7m8"
Gateway P95:
4.2 sec → 250 ms

Transfer Success:
72% → 99.8%

Error Rate:
28% → 0.2%
```

The application infrastructure metrics remain relatively unchanged.

This proves that infrastructure health alone was not sufficient to detect the incident.

---

# 29. Monitoring Improvements

The organization adds:

```text id="m8r4c2"
Business transaction monitoring
External dependency monitoring
Synthetic tests
Transfer success SLI
Gateway latency metrics
Gateway timeout metrics
Retry-rate monitoring
Circuit-breaker metrics
```

---

# 30. New Dashboard

The improved dashboard includes:

```text id="j5v8n3"
┌─────────────────────────────────────────┐
│ Transfer Service Health                 │
├─────────────────────────────────────────┤
│ Requests                  1,000 RPS     │
│ P95 Latency               320 ms        │
│ HTTP Error Rate           0.2%          │
│ Transfer Success          99.8%         │
│ Gateway P95               250 ms        │
│ Gateway Timeout Rate      0.1%          │
│ Retry Rate                0.2%          │
│ Circuit Breaker           Closed        │
└─────────────────────────────────────────┘
```

Now the dashboard reflects both technical and business health.

---

# 31. Alerting Improvements

Before:

```text id="f7m3k2"
Alert:
CPU > 90%
```

After:

```text id="c5v8n4"
Alert:
Transfer Success Rate < 99.5%
```

Additional alerts:

```text id="z8r4m6"
Gateway P95 latency above threshold
Gateway timeout rate increasing
Retry rate increasing
Circuit breaker opened
Business SLO violation
```

---

# 32. Monitoring Layers

A mature monitoring strategy has multiple layers.

```text id="q2w6j8"
                    User Experience
                          │
                          ▼
                 Business Transactions
                          │
                          ▼
                    Application
                          │
                          ▼
                     Services
                          │
                          ▼
                   Infrastructure
                          │
                          ▼
                      Network
                          │
                          ▼
                     Hardware
```

Monitoring should cover the layers relevant to the service.

---

# 33. Four Observability Pillars

The team uses:

```text id="y7m3p8"
Metrics
Logs
Traces
Events
```

### Metrics

Show trends and numerical behavior.

### Logs

Provide detailed events and error information.

### Traces

Show request flow across services.

### Events

Show changes such as deployments, scaling, and configuration changes.

---

# 34. Observability vs Monitoring

Monitoring asks:

> "Is something wrong?"

Observability helps answer:

> "Why is it wrong?"

For this incident:

```text id="k8p4m2"
Monitoring:
Transfer failures detected

Observability:
Payment gateway latency caused timeout
```

That distinction is important for SRE.

---

# 35. False Negative

This incident is an example of a monitoring false negative.

A false negative occurs when:

```text id="r3v7n8"
Real Problem Exists
        │
        ▼
Monitoring Does Not Detect It
        │
        ▼
No Alert
```

This is particularly dangerous because engineers may believe the system is healthy.

---

# 36. Monitoring Coverage

The organization evaluates monitoring coverage across:

```text id="m4x8c1"
Infrastructure
Applications
Services
Dependencies
Business Transactions
User Experience
```

The goal is to identify blind spots before they cause incidents.

---

# 37. Synthetic Monitoring Strategy

Critical workflows should be tested periodically.

Examples:

```text id="j8p5m2"
Login
Search
Checkout
Payment
Transfer
Order Creation
API Authentication
```

Synthetic monitoring should verify actual functionality rather than merely checking whether a TCP port is open.

---

# 38. Business Metrics

Depending on the application, useful business metrics may include:

```text id="f7c3m9"
Successful Orders
Successful Payments
Successful Transfers
Failed Transactions
Cart Conversion
Authentication Success
Checkout Completion
```

These metrics can be critical SLIs.

---

# 39. Capacity and Reliability

Monitoring blind spots can also affect capacity planning.

Suppose:

```text id="x6m8v4"
Transfer failures increase
```

but the monitoring system interprets them as successful HTTP requests.

Then traffic and workload estimates may become inaccurate.

Business metrics provide another dimension for capacity and reliability analysis.

---

# 40. Incident Timeline

```text id="q4m7z2"
09:00
All systems healthy
       │
       ▼
10:00
Payment gateway latency begins increasing
       │
       ▼
10:10
Transfer success rate begins falling
       │
       ▼
10:20
Users report transfer failures
       │
       ▼
10:25
No infrastructure alert
       │
       ▼
10:35
SRE begins investigation
       │
       ▼
10:45
Business metric identifies failure
       │
       ▼
11:00
Distributed trace identifies gateway latency
       │
       ▼
11:10
Retry amplification identified
       │
       ▼
11:20
Circuit breaker enabled
       │
       ▼
11:40
External provider recovers
       │
       ▼
11:50
Transfer success rate normal
       │
       ▼
Later
Monitoring gaps addressed
```

---

# 41. Root Cause Analysis

### Immediate Cause

Payment transactions were failing because the external gateway became slow.

### Technical Root Cause

External dependency latency caused transfer requests to exceed application timeout limits.

### Monitoring Root Cause

Monitoring focused primarily on infrastructure and HTTP-level health rather than business transaction success.

### Contributing Factors

```text id="g3r6w8"
Missing business SLI
Missing synthetic transaction
Insufficient dependency monitoring
Aggressive retries
No early alert for transfer success degradation
```

### Business Impact

```text id="w8m5p2"
Customers could not complete transfers
Transaction failures increased
Customer experience degraded
Support requests increased
```

---

# 42. What Went Well

```text id="n6v3k8"
Distributed tracing was available
Application logs contained dependency information
The external dependency was eventually identified
Circuit breaker reduced retry pressure
Business metrics allowed accurate impact measurement
```

---

# 43. What Went Wrong

```text id="r4c7m2"
Infrastructure monitoring created a false sense of health
No business transaction SLI existed
No synthetic transfer test existed
External dependency monitoring was insufficient
Retry behavior amplified the problem
```

---

# 44. Preventive Actions

| Action                | Purpose                           |
| --------------------- | --------------------------------- |
| Business SLIs         | Measure actual user success       |
| Synthetic monitoring  | Test critical workflows           |
| Dependency monitoring | Track external services           |
| Distributed tracing   | Identify dependency latency       |
| Retry controls        | Prevent retry amplification       |
| Circuit breakers      | Protect from failing dependencies |
| SLOs                  | Define acceptable reliability     |
| Error budgets         | Quantify reliability risk         |
| Monitoring audits     | Identify blind spots              |

---

# 45. Monitoring Maturity Model

A useful progression is:

```text id="j6w8p3"
Level 1
Infrastructure Monitoring
       ↓
Level 2
Application Monitoring
       ↓
Level 3
Distributed Observability
       ↓
Level 4
Business Transaction Monitoring
       ↓
Level 5
Predictive / Proactive Monitoring
```

A mature SRE organization aims to move beyond infrastructure-only monitoring.

---

# 46. SRE Concepts Demonstrated

This case study demonstrates:

```text id="v5m2r8"
SRE
Observability
Monitoring
Metrics
Logs
Traces
Events
Synthetic Monitoring
Business SLIs
SLOs
Error Budgets
External Dependencies
Circuit Breakers
Retries
Distributed Tracing
DQL
Incident Response
Root Cause Analysis
Monitoring Maturity
```

---

# 47. Final Incident Flow

```text id="c7m4x9"
                EXTERNAL DEPENDENCY
                     DEGRADES
                        │
                        ▼
                 GATEWAY LATENCY ↑
                        │
                        ▼
                 REQUEST TIMEOUTS
                        │
                        ▼
                TRANSFER FAILURES
                        │
                        ▼
               BUSINESS SUCCESS ↓
                        │
                        ▼
                  USER IMPACT
                        │
                        ▼
             MONITORING BLIND SPOT
                        │
                        ▼
              NO IMMEDIATE ALERT
                        │
                        ▼
                 SRE INVESTIGATION
                        │
                        ▼
              BUSINESS SLI IDENTIFIED
                        │
                        ▼
               ROOT CAUSE FOUND
                        │
                        ▼
              MONITORING IMPROVED
```

---

# Final Takeaway

The most important lesson from this case study is:

> **Monitoring should measure what matters to users, not only what is easy to measure technically.**

A system can have:

```text id="a8v4j2"
Healthy CPU
Healthy Memory
Healthy Pods
Healthy Nodes
Healthy Network
```

and still provide a **failed user experience**.

A mature SRE approach therefore connects:

```text id="e7m3k5"
Infrastructure
      ↓
Application
      ↓
Services
      ↓
Dependencies
      ↓
Business Transactions
      ↓
User Experience
```

The ultimate goal of observability is not simply to know:

> "Is the server healthy?"

It is to be able to answer:

> **"Can our users successfully complete the operations that matter to them, and if not, why?"**
