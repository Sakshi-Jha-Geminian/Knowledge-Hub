# Case Study 02: High Application Error Rate After Deployment

## Overview

This case study demonstrates how an SRE team can investigate a sudden increase in application errors after a software deployment.

The scenario focuses on:

* HTTP 5xx errors
* Deployment correlation
* Application logs
* Distributed traces
* Service dependencies
* Kubernetes workloads
* DQL-based investigation
* Root cause analysis
* Rollback and remediation

The primary lesson is:

> A strong temporal correlation between a deployment and an incident is an important clue, but correlation alone is not proof of root cause.

---

# 1. Problem Statement

An e-commerce application is operating normally when users suddenly begin receiving errors while placing orders.

The SRE monitoring dashboard shows:

```text
Metric                 Normal       Incident
------------------------------------------------
Request Rate           1,500 RPS    1,520 RPS
Error Rate             0.15%        12.5%
P95 Latency            220 ms       1.6 sec
HTTP 5xx               Very Low     Very High
```

The most significant change is the error rate.

```text
Normal
0.15%
  │
  ▼
Incident
12.5%
```

The SRE team must determine:

* When did the errors begin?
* Which service is failing?
* Which requests are affected?
* Was there a recent deployment?
* What changed?
* Is the failure isolated to one service?
* What is the root cause?
* Should the deployment be rolled back?

---

# 2. System Architecture

The application uses several microservices.

```text
                         Users
                           │
                           ▼
                    Load Balancer
                           │
                           ▼
                      API Gateway
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
        User Service   Order Service   Catalog Service
                           │
                           ▼
                      Payment Service
                           │
                           ▼
                       Payment DB
```

The services run on Kubernetes.

```text
Kubernetes Cluster
        │
        ├── API Gateway
        ├── User Service
        ├── Order Service
        ├── Catalog Service
        └── Payment Service
```

Dynatrace monitors the services, requests, traces, logs, Kubernetes resources, and dependencies.

---

# 3. Initial Detection

The SRE dashboard shows a sudden increase in errors.

```text
Error Rate

12% |                    █████
10% |                    █████
 8% |                    █████
 6% |              █████████
 4% |              █████████
 2% |        ███████████████
 0% |███████████████████████
     -------------------------
              Time
```

The increase is abrupt rather than gradual.

This is an important clue.

---

# 4. Establish the Incident Timeline

The team examines the timeline.

```text
09:00 → Normal
09:30 → Normal
10:00 → Deployment begins
10:05 → Deployment completes
10:07 → Error rate increases
10:10 → Latency increases
10:15 → Customer complaints
```

The timing is suspicious.

```text
Deployment
    │
    ▼
~2 minutes
    │
    ▼
Error Rate Increase
```

The deployment becomes the primary investigation hypothesis.

However, the team still needs evidence.

---

# 5. Check the Golden Signals

The SRE team evaluates:

```text
Latency
Traffic
Errors
Saturation
```

### Traffic

```text
Normal
```

There is no significant traffic increase.

### Errors

```text
Significantly Increased
```

### Latency

```text
Increased
```

### Saturation

```text
CPU → Normal
Memory → Normal
```

The pattern is:

```text
Traffic     → Stable
Errors      → ↑↑
Latency     → ↑
Resources   → Normal
```

This suggests an application or dependency problem rather than obvious infrastructure saturation.

---

# 6. Identify the Failing Service

The team compares error rates across services.

```text
Service              Error Rate
--------------------------------
API Gateway             0.2%
User Service            0.1%
Catalog Service         0.3%
Order Service          18.4%
Payment Service         0.4%
```

The Order Service is clearly abnormal.

```text
Order Service
     │
     ▼
18.4% errors
```

The investigation is narrowed to the order-processing path.

---

# 7. Identify the HTTP Status Codes

The team analyzes the returned status codes.

```text
Status Code        Percentage
-----------------------------
200                    87%
400                     0.5%
401                     0.2%
404                     0.1%
500                    11%
502                     1%
503                     0.2%
```

The majority of failures are HTTP 500 responses.

This suggests that the application itself may be encountering an internal failure.

---

# 8. Analyze Failed Endpoints

The SRE team breaks errors down by endpoint.

```text
Endpoint                     Error Rate
----------------------------------------
GET /orders                     0.2%
GET /orders/{id}                0.4%
POST /orders                   24.8%
POST /orders/{id}/cancel        0.3%
```

The problem is strongly concentrated around:

```text
POST /orders
```

This is useful because it reduces the investigation scope.

---

# 9. Distributed Trace Investigation

The team opens traces for failed `POST /orders` requests.

A typical failed trace looks like:

```text
API Gateway
     │
     ▼
Order Service
     │
     ├── User Service
     │       └── Success
     │
     ├── Catalog Service
     │       └── Success
     │
     └── Payment Service
             │
             ▼
          HTTP 500
```

The Payment Service becomes the next area of investigation.

---

# 10. Payment Service Investigation

The Payment Service dashboard shows:

```text
Metric                  Value
--------------------------------
Request Rate             Normal
CPU                      45%
Memory                   50%
Latency                  Increased
Error Rate               0.4%
```

Interestingly, the Payment Service itself is not generating a large number of errors.

The team investigates the request being sent from Order Service.

---

# 11. Request Comparison

The team compares a successful request with a failed request.

### Before Deployment

```text
POST /payments
{
    "customerId": "...",
    "amount": 100,
    "currency": "USD"
}
```

### After Deployment

```text
POST /payments
{
    "customerId": "...",
    "amount": 100,
    "currency": "USD",
    "paymentMethod": {
        ...
    }
}
```

The new application version is sending an additional field.

The payment API version currently deployed does not expect this payload structure.

---

# 12. Application Logs

The Order Service logs contain messages similar to:

```text
Payment request failed
Invalid request payload
Unexpected field: paymentMethod
```

This is strong evidence.

The team now has:

```text
Deployment
    ↓
Changed Request Payload
    ↓
Payment API Rejects Request
    ↓
Order Service Exception
    ↓
HTTP 500
```

---

# 13. DQL Investigation

The SRE team can use DQL to investigate the error logs.

A conceptual query:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
```

This identifies which services are generating the largest number of error logs.

---

# 14. Investigate Error Messages

The team then filters for payment-related failures.

Conceptually:

```text
fetch logs
| filter loglevel == "ERROR"
| filter contains(content, "payment")
```

The exact field and string function should be validated against the telemetry schema in the environment.

The results show a strong increase in payment-related errors after the deployment.

---

# 15. Compare Error Volume Over Time

The team investigates the error trend.

Conceptually:

```text
fetch logs
| filter loglevel == "ERROR"
| makeTimeseries count()
```

The resulting pattern is:

```text
Before deployment
      ↓
Low errors
      ↓
Deployment
      ↓
Sharp increase
      ↓
Sustained high errors
```

This strengthens the deployment hypothesis.

---

# 16. Deployment Correlation

The deployment timeline is:

```text
10:00
Version 4.7.1 deployed

10:02
Deployment completed

10:04
First payment-related errors

10:07
Order error rate increases

10:10
Customer impact
```

The close timing is significant.

But the team verifies the actual code/configuration change before declaring the deployment the root cause.

---

# 17. Root Cause

The root cause is identified as:

> A new version of the Order Service changed the payment request payload without maintaining compatibility with the currently deployed Payment Service API.

The failure chain is:

```text
Code Change
     │
     ▼
Changed Payment Payload
     │
     ▼
Payment API Rejects Request
     │
     ▼
Order Service Exception
     │
     ▼
HTTP 500
     │
     ▼
Customer Order Failures
```

---

# 18. Why the Error Rate Increased Suddenly

The problem was introduced during deployment.

Therefore:

```text
Before deployment:
Compatible payload
      ↓
Successful payment calls

After deployment:
Incompatible payload
      ↓
Payment failures
      ↓
Order failures
```

This explains the abrupt transition.

---

# 19. Immediate Mitigation

Because the incident is actively affecting customers, the SRE team chooses the fastest safe mitigation.

The deployment is rolled back.

```text
Version 4.7.1
      │
      ▼
Rollback
      │
      ▼
Version 4.7.0
```

The older version uses the compatible payment payload.

---

# 20. Post-Rollback Results

After rollback:

```text
Metric                 Incident      After Rollback
-----------------------------------------------------
Error Rate              12.5%            0.2%
P95 Latency             1.6 sec          230 ms
Order Failures          High             Normal
Payment Errors          High             Normal
```

This provides additional evidence that the new Order Service version caused the problem.

---

# 21. Permanent Fix

The development team modifies the new version to maintain API compatibility.

Possible approaches include:

```text
Backward-compatible payload
API version negotiation
Contract validation
Feature flag
Coordinated service deployment
```

The corrected version is then tested before deployment.

---

# 22. Contract Testing

A major preventive action is introducing API contract testing.

Conceptually:

```text
Order Service
      │
      ▼
API Contract
      │
      ▼
Payment Service
```

The test verifies that:

```text
Request format
Response format
Required fields
Optional fields
Data types
Error behavior
```

remain compatible.

---

# 23. Deployment Strategy Improvements

Instead of immediately deploying to all instances, the team can use:

```text
Canary Deployment
       │
       ▼
Small Percentage
       │
       ▼
Observe Metrics
       │
       ▼
Healthy?
   ┌───┴───┐
  Yes      No
   │        │
   ▼        ▼
Expand    Rollback
```

This reduces the blast radius of application defects.

---

# 24. Feature Flags

A feature flag can also reduce risk.

```text
New Payment Payload
       │
       ▼
Feature Flag
       │
   ┌───┴───┐
  OFF      ON
   │        │
   ▼        ▼
Old       New
Payload   Payload
```

If errors increase, the new behavior can be disabled quickly.

---

# 25. Monitoring Improvements

The team adds monitoring for:

```text
HTTP 5xx rate
Endpoint error rate
Dependency failures
Payment API errors
Request payload validation errors
Deployment health
```

The goal is to detect the problem earlier.

---

# 26. Deployment-Aware Monitoring

The incident demonstrates why deployment information should be correlated with telemetry.

Useful timeline:

```text
Deployment
    │
    ├── Error Rate
    ├── Latency
    ├── Request Volume
    ├── Dependency Health
    └── User Impact
```

This makes deployment regressions easier to identify.

---

# 27. Predictive / Proactive Opportunity

Although this incident was caused by a deployment, predictive monitoring can still help reduce impact.

For example:

```text
Deployment
     │
     ▼
Early Error Increase
     │
     ▼
Automatic Detection
     │
     ▼
Canary Halt
     │
     ▼
Prevent Full Rollout
```

The goal is not necessarily to predict the exact code defect.

Instead, the system can detect abnormal behavior quickly enough to prevent widespread impact.

---

# 28. Business Impact

The technical failure resulted in:

```text
Order requests
      ↓
Payment failures
      ↓
Order creation failures
      ↓
Customer impact
      ↓
Potential revenue loss
```

For a financial or transactional system, the impact can be even more significant because failed transactions may directly affect customers and business operations.

---

# 29. Incident Response Flow

The complete response:

```text
Error Alert
    │
    ▼
Confirm Incident
    │
    ▼
Check Timeline
    │
    ▼
Identify Affected Service
    │
    ▼
Identify Failed Endpoint
    │
    ▼
Trace Dependency
    │
    ▼
Inspect Logs
    │
    ▼
Check Recent Deployment
    │
    ▼
Validate Hypothesis
    │
    ▼
Rollback
    │
    ▼
Verify Recovery
    │
    ▼
Permanent Fix
```

---

# 30. Root Cause vs Trigger

It is important to distinguish the trigger from the deeper technical cause.

### Trigger

```text
Deployment of Order Service v4.7.1
```

### Technical Root Cause

```text
Incompatible payment request payload
```

### Contributing Factors

```text
Insufficient contract testing
No canary deployment
Incomplete dependency compatibility validation
```

### Business Impact

```text
Failed customer orders
```

A good RCA separates these concepts.

---

# 31. What Went Well

Possible positives:

```text
Deployment timestamp was available
Dynatrace detected the error increase
Distributed tracing identified the failing dependency
Logs contained useful error information
Rollback restored service quickly
```

---

# 32. What Went Wrong

Potential weaknesses:

```text
No contract test caught the incompatibility
Deployment reached production too quickly
No canary protection
Dependency compatibility was not validated
```

---

# 33. Preventive Action Items

| Action                      | Purpose                       |
| --------------------------- | ----------------------------- |
| API contract testing        | Prevent incompatible changes  |
| Canary deployments          | Reduce blast radius           |
| Automated rollback          | Reduce recovery time          |
| Deployment monitoring       | Detect regressions            |
| Dependency testing          | Validate service interactions |
| Better error classification | Improve investigation         |
| Runbooks                    | Standardize incident response |

---

# 34. SRE Metrics

This incident can be evaluated using:

```text
MTTD
Mean Time to Detect

MTTR
Mean Time to Restore

Error Budget
Amount of acceptable unreliability

Change Failure Rate
Percentage of deployments causing failures
```

A successful improvement should reduce:

```text
MTTD ↓
MTTR ↓
Change Failure Rate ↓
```

---

# 35. Lessons Learned

### Lesson 1

A sudden change immediately after deployment should trigger deployment-focused investigation.

### Lesson 2

Correlation is a clue, not proof. The suspected change must be validated.

### Lesson 3

Distributed tracing is extremely useful for identifying where a request fails across microservices.

### Lesson 4

Logs provide the detailed context needed to understand application failures.

### Lesson 5

Canary deployments can significantly reduce the blast radius of defective releases.

### Lesson 6

API contract testing is critical when multiple independently deployed services communicate with each other.

### Lesson 7

Fast rollback is an important SRE capability.

---

# 36. Concepts Demonstrated

This case study covers:

```text
Observability
SRE
Incident Response
HTTP Status Codes
Microservices
Distributed Tracing
Logs
DQL
Service Dependencies
Deployment Monitoring
Canary Deployment
Rollback
API Contracts
Change Failure Rate
MTTD
MTTR
Error Budgets
Root Cause Analysis
```

---

# 37. Final Incident Flow

```text
                    DEPLOYMENT
                         │
                         ▼
                ORDER SERVICE
                         │
                         ▼
              CHANGED API PAYLOAD
                         │
                         ▼
                PAYMENT SERVICE
                         │
                         ▼
               REQUEST REJECTED
                         │
                         ▼
                ORDER EXCEPTION
                         │
                         ▼
                     HTTP 500
                         │
                         ▼
                 ERROR RATE ↑
                         │
                         ▼
                  USER IMPACT
                         │
                         ▼
                    ROLLBACK
                         │
                         ▼
                   SERVICE RESTORED
                         │
                         ▼
                 PERMANENT FIX
```

---

# Final Takeaway

The most important lesson from this case study is:

> **When an application's error rate suddenly increases, correlate the failure with deployments, identify the affected service and endpoint, trace the failing dependency, inspect logs for the actual error, and validate the suspected root cause before taking corrective action.**

The investigation moved from:

```text
Symptom
  ↓
Error Rate Increase
  ↓
Affected Service
  ↓
Affected Endpoint
  ↓
Distributed Trace
  ↓
Dependency
  ↓
Application Logs
  ↓
Deployment Change
  ↓
Root Cause
  ↓
Rollback
  ↓
Permanent Fix
```

This is a practical example of how **observability, SRE practices, and deployment discipline work together to detect, investigate, and recover from production incidents**.
