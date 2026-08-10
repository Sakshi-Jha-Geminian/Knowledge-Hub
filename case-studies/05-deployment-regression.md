# Case Study 05: Deployment Regression

## Overview

This case study demonstrates how an SRE team identifies and responds to a production performance regression introduced by a software deployment.

Unlike an obvious deployment failure, the application remains available after the release. Pods are healthy, requests succeed, and infrastructure resources appear normal.

However, application performance gradually deteriorates.

The investigation demonstrates:

* Deployment monitoring
* Version comparison
* Canary analysis
* Application performance regression
* Distributed tracing
* Service-level metrics
* Database dependency analysis
* Error budgets
* Change Failure Rate
* Rollback
* DQL investigation
* Root cause analysis
* Preventive deployment practices

The central lesson is:

> A deployment can be technically successful while still being operationally unsuccessful.

---

# 1. Problem Statement

A new version of an application is deployed to production.

The deployment dashboard reports:

```text
Deployment Status: SUCCESS
Pods:              HEALTHY
Readiness:         PASS
Liveness:          PASS
```

Initially, everything appears normal.

However, after approximately 30 minutes, application latency begins increasing.

```text
Metric                  Before       After
------------------------------------------------
P95 Latency              220 ms       850 ms
P99 Latency              450 ms       2.1 sec
Error Rate               0.2%         0.8%
CPU                      45%          58%
Memory                   55%          62%
Request Rate             2,000 RPS    2,020 RPS
```

The application is technically available, but performance has degraded.

---

# 2. System Architecture

The application consists of several microservices.

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
                    Product Service
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          Catalog DB    Cache       Pricing Service
```

The application runs on Kubernetes.

```text
Kubernetes Cluster
       │
       ├── API Gateway
       ├── Product Service
       ├── Pricing Service
       └── Supporting Services
```

Dynatrace monitors:

```text
Applications
Services
Requests
Traces
Logs
Kubernetes
Infrastructure
Databases
Dependencies
```

---

# 3. Deployment

The production environment previously runs:

```text
Version: 5.3.2
```

A new version is released:

```text
Version: 5.4.0
```

The deployment completes successfully.

```text
5.3.2
  │
  ▼
5.4.0
  │
  ▼
Deployment SUCCESS
```

No deployment failure is reported.

---

# 4. Initial Health Check

Immediately after deployment:

```text
Pods:           Healthy
CPU:            Normal
Memory:         Normal
Errors:         Normal
Availability:   Normal
```

Therefore, the deployment appears successful.

But the SRE team continues monitoring the application because successful deployment does not guarantee healthy production behavior.

---

# 5. Performance Regression Appears

After approximately 30 minutes:

```text
P95 Latency
220 ms
  │
  ├── 300 ms
  ├── 420 ms
  ├── 600 ms
  └── 850 ms
```

The increase is gradual.

This differs from an immediate application crash.

```text
Immediate Failure:

Deploy
  ↓
Errors ↑
  ↓
Incident
```

Here:

```text
Deploy
  ↓
Normal
  ↓
Gradual degradation
  ↓
Latency ↑
  ↓
User impact
```

---

# 6. Golden Signals

The team checks the four golden signals.

### Traffic

```text
2,000 RPS → 2,020 RPS
```

Traffic is stable.

### Latency

```text
220 ms → 850 ms
```

Significantly increased.

### Errors

```text
0.2% → 0.8%
```

Slight increase.

### Saturation

```text
CPU:     45% → 58%
Memory:  55% → 62%
```

Resources remain within normal operating ranges.

The pattern is:

```text
Traffic       →
Latency       ↑↑
Errors        ↑
Saturation    →
```

This suggests an application behavior change rather than infrastructure exhaustion.

---

# 7. Compare Application Versions

The SRE team compares version-level performance.

```text
Version       P95 Latency
-------------------------
5.3.2           220 ms
5.4.0           850 ms
```

This is a significant difference.

The team now has a strong hypothesis:

> Version 5.4.0 introduced a performance regression.

---

# 8. Canary Analysis

Suppose the deployment initially sends only a small percentage of traffic to the new version.

```text
             Traffic
                │
       ┌────────┴────────┐
       ▼                 ▼
   Version 5.3.2      Version 5.4.0
      90%                10%
       │                 │
       ▼                 ▼
   220 ms              800 ms
```

This is extremely valuable.

The new version is handling only 10% of traffic but already shows substantially higher latency.

That provides an early warning.

---

# 9. Canary Decision

A canary deployment can use predefined health criteria.

For example:

```text
Latency
Error Rate
Availability
Resource Utilization
Dependency Health
```

Conceptually:

```text
New Version
     │
     ▼
Metrics Healthy?
   ┌─┴─┐
  YES  NO
   │    │
   ▼    ▼
Expand  Stop
         │
         ▼
      Rollback
```

In this case:

```text
Latency → FAIL
```

Therefore, the rollout should be stopped.

---

# 10. Why the Deployment Was Initially Considered Successful

Kubernetes checks conditions such as:

```text
Pod Created
Container Started
Readiness Passed
Liveness Passed
Deployment Completed
```

These checks answer:

> "Is the application running?"

They do not necessarily answer:

> "Is the application performing as well as the previous version?"

This distinction is extremely important.

---

# 11. Endpoint-Level Analysis

The SRE team compares endpoint performance.

```text
Endpoint                    v5.3.2      v5.4.0
------------------------------------------------
GET /products                 180 ms      190 ms
GET /products/{id}            220 ms      240 ms
GET /products/search           300 ms      1.2 sec
GET /categories               150 ms      160 ms
```

Only the search endpoint shows significant degradation.

This narrows the investigation.

---

# 12. Distributed Trace

The team examines slow traces.

Before deployment:

```text
API Gateway
    │
    ▼
Product Service
    │
    ├── Cache       10 ms
    │
    └── Catalog DB  120 ms
```

After deployment:

```text
API Gateway
    │
    ▼
Product Service
    │
    ├── Cache        15 ms
    │
    └── Catalog DB  900 ms
```

The database call is now taking substantially longer.

---

# 13. Query Comparison

The team compares the database queries generated by both versions.

### Version 5.3.2

```sql
SELECT product_id, name, price
FROM products
WHERE category_id = ?;
```

### Version 5.4.0

The new version performs additional filtering and sorting.

Conceptually:

```text
Query
  ↓
Filter
  ↓
Join
  ↓
Sort
  ↓
Additional Processing
  ↓
Result
```

The query is more expensive.

---

# 14. Query Volume

The team also checks how many queries are being generated.

Before:

```text
100 queries/request
```

After:

```text
350 queries/request
```

This indicates another problem.

The new implementation may have introduced excessive database calls.

---

# 15. N+1 Query Pattern

A possible cause is an N+1 query pattern.

Conceptually:

```text
Request
   │
   ▼
Get 100 products
   │
   ▼
For each product
   │
   ├── Query database
   ├── Query database
   ├── Query database
   ├── ...
   └── Query database
```

Instead of:

```text
Request
   │
   ▼
One optimized query
   │
   ▼
100 products
```

The N+1 pattern can significantly increase database load and latency.

---

# 16. Database Impact

The database dashboard shows:

```text
Metric                  v5.3.2      v5.4.0
---------------------------------------------
CPU                       55%         78%
Queries/sec              8,000       25,000
Query Latency              80 ms       650 ms
Connections                40%          75%
```

The database is now processing substantially more queries.

---

# 17. Application Behavior Change

The root behavioral difference is:

```text
Version 5.3.2
     │
     ▼
Efficient data retrieval
     │
     ▼
Low DB load
```

Versus:

```text
Version 5.4.0
     │
     ▼
Additional per-item queries
     │
     ▼
Query Volume ↑
     │
     ▼
Database Load ↑
     │
     ▼
Latency ↑
```

---

# 18. DQL Investigation

The SRE team can use DQL to investigate request behavior.

A conceptual example:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
```

The team can also analyze relevant spans to compare service performance.

The exact dataset and fields depend on the Dynatrace environment and telemetry schema.

---

# 19. Compare Before and After Deployment

The investigation should compare a consistent time window.

For example:

```text
Before:
08:00–09:00

After:
10:00–11:00
```

The team compares:

```text
Request Rate
Latency
Errors
Database Queries
Database Latency
CPU
Memory
```

The goal is to identify what changed.

---

# 20. Root Cause

The root cause is identified as:

> Version 5.4.0 introduced an inefficient data-access pattern for the product-search endpoint, resulting in excessive database queries and increased database processing time.

The complete chain is:

```text
New Release
     │
     ▼
Changed Search Implementation
     │
     ▼
Excessive Database Queries
     │
     ▼
Database Load ↑
     │
     ▼
Query Latency ↑
     │
     ▼
Product Search Latency ↑
     │
     ▼
Overall Application Latency ↑
     │
     ▼
User Experience Degradation
```

---

# 21. Immediate Mitigation

Because the canary shows clear degradation, the rollout is stopped.

```text
v5.4.0
   │
   ▼
Canary Failure
   │
   ▼
Stop Rollout
   │
   ▼
Rollback
   │
   ▼
v5.3.2
```

---

# 22. Recovery

After rollback:

```text
P95 Latency:
850 ms → 230 ms

Database Queries:
25,000/s → 8,500/s

Database CPU:
78% → 55%
```

The application returns to normal.

This confirms that the new release was responsible for the regression.

---

# 23. Permanent Fix

The development team redesigns the search implementation.

Instead of:

```text
One request
   ↓
Many database queries
```

the application uses:

```text
One request
   ↓
Optimized query
   ↓
Required data
```

Possible improvements include:

```text
Batch queries
JOIN optimization
Caching
Pagination
Query optimization
Precomputed data
Efficient data-access patterns
```

---

# 24. Performance Testing

The new implementation is tested using realistic production-like data volumes.

Testing should include:

```text
Normal traffic
Peak traffic
Large datasets
Concurrent requests
Slow database conditions
High query volume
```

The goal is to identify performance regressions before production.

---

# 25. Canary Deployment

The organization improves its deployment process.

Instead of:

```text
100%
```

the deployment follows:

```text
5%
 ↓
10%
 ↓
25%
 ↓
50%
 ↓
100%
```

At each stage:

```text
Observe
  ↓
Compare
  ↓
Validate
  ↓
Continue or Rollback
```

---

# 26. Automated Canary Validation

The deployment pipeline can monitor:

```text
P95 Latency
P99 Latency
Error Rate
Availability
Database Query Rate
Dependency Latency
CPU
Memory
```

For example:

```text
New Version
     │
     ▼
P95 latency > baseline threshold?
     │
   ┌─┴─┐
  Yes  No
   │    │
   ▼    ▼
Rollback Continue
```

This turns observability data into a deployment safety mechanism.

---

# 27. Error Budget Impact

Suppose the service has an SLO:

```text
Availability SLO = 99.9%
```

A performance regression may not immediately violate availability.

However, if latency is also part of the service objective, the regression can consume the performance-related error budget.

This demonstrates an important principle:

> Reliability is not only about whether the application is up; it is also about whether it behaves within agreed performance expectations.

---

# 28. Change Failure Rate

This incident is an example of a deployment-related failure.

The organization can track:

```text
Change Failure Rate
```

Conceptually:

```text
Change Failure Rate =
Deployments causing failure
--------------------------------
Total deployments
```

A deployment that requires rollback or causes significant customer impact may count as a failed change according to the organization's measurement definition.

---

# 29. Deployment vs Operational Success

The incident demonstrates two different outcomes.

### Technical Deployment

```text
Pods started
Readiness passed
Deployment completed
```

Result:

```text
SUCCESS
```

### Operational Outcome

```text
Latency ↑
Database Load ↑
User Experience ↓
```

Result:

```text
REGRESSION
```

Therefore:

> Deployment success and production success are not the same thing.

---

# 30. Preventive Monitoring

The team introduces version-aware monitoring.

Dashboards should allow comparisons such as:

```text
Version 5.3.2
       vs
Version 5.4.0
```

Metrics include:

```text
Latency
Error Rate
Throughput
Database Calls
Dependency Latency
Resource Consumption
```

---

# 31. Deployment Dashboard

A useful deployment dashboard could contain:

```text
┌────────────────────────────────────────┐
│ Deployment: v5.4.0                     │
├────────────────────────────────────────┤
│ Traffic                 10%            │
│ P95 Latency             850 ms   ↑     │
│ Error Rate              0.8%     ↑     │
│ DB Queries              25K/s    ↑     │
│ DB CPU                  78%      ↑     │
│ Status                  WARNING        │
└────────────────────────────────────────┘
```

This makes regression visible during rollout.

---

# 32. Incident Timeline

```text
09:00
Version 5.3.2 healthy
       │
       ▼
10:00
Version 5.4.0 deployment begins
       │
       ▼
10:05
Canary receives 10% traffic
       │
       ▼
10:15
Latency begins increasing
       │
       ▼
10:20
Database query volume increases
       │
       ▼
10:30
P95 latency reaches 850 ms
       │
       ▼
10:35
Canary marked unhealthy
       │
       ▼
10:40
Rollout stopped
       │
       ▼
10:45
Rollback to v5.3.2
       │
       ▼
11:00
Latency returns to baseline
       │
       ▼
11:30
Root cause identified
       │
       ▼
Later
Optimized version tested
       │
       ▼
Future Deployment
Canary rollout succeeds
```

---

# 33. Root Cause Analysis

### Immediate Cause

Application latency increased significantly after deployment.

### Technical Root Cause

The new product-search implementation generated excessive database queries.

### Trigger

Deployment of version 5.4.0.

### Contributing Factors

```text
Insufficient production-like performance testing
No automated query-volume regression check
Canary monitoring was not fully automated
No strict latency deployment gate
```

### Business Impact

```text
Slower product searches
Poor customer experience
Potential abandoned transactions
Increased database workload
Potential SLO impact
```

---

# 34. What Went Well

```text
Canary deployment limited exposure
Version-level metrics were available
Distributed traces identified database activity
Rollback restored performance
Historical comparison identified the regression
```

---

# 35. What Went Wrong

```text
Performance regression was not detected before deployment
Query volume increased significantly
Performance tests did not represent production data
Deployment initially relied too heavily on infrastructure health
```

---

# 36. Preventive Actions

| Action                      | Purpose                      |
| --------------------------- | ---------------------------- |
| Canary deployment           | Limit blast radius           |
| Automated performance gates | Stop bad releases            |
| Version comparison          | Detect regressions           |
| Query-volume monitoring     | Detect excessive DB calls    |
| Load testing                | Validate realistic workloads |
| Contract/performance tests  | Prevent regressions          |
| Automatic rollback          | Reduce recovery time         |
| SLO monitoring              | Measure user impact          |

---

# 37. SRE Concepts Demonstrated

This case study demonstrates:

```text
SRE
Observability
Deployment Monitoring
Canary Deployment
Performance Regression
Distributed Tracing
Database Monitoring
DQL
Version Comparison
Error Budgets
SLO
Change Failure Rate
Rollback
Performance Testing
Root Cause Analysis
Incident Response
```

---

# 38. Final Incident Flow

```text
                  NEW DEPLOYMENT
                        │
                        ▼
                   CANARY
                        │
                        ▼
              NEW SEARCH BEHAVIOR
                        │
                        ▼
              DATABASE QUERIES ↑
                        │
                        ▼
               DATABASE LOAD ↑
                        │
                        ▼
              QUERY LATENCY ↑
                        │
                        ▼
             APPLICATION LATENCY ↑
                        │
                        ▼
               USER EXPERIENCE ↓
                        │
                        ▼
              CANARY HEALTH CHECK
                        │
                        ▼
                   ROLLBACK
                        │
                        ▼
              SERVICE RECOVERED
                        │
                        ▼
                ROOT CAUSE FIX
                        │
                        ▼
             PERFORMANCE TESTING
                        │
                        ▼
             SAFE REDEPLOYMENT
```

---

# Final Takeaway

The most important lesson from this case study is:

> **A deployment should not be considered successful simply because the pods are running. Production health must be evaluated using application-level behavior, latency, errors, dependencies, and user-facing SLOs.**

The investigation moved through:

```text
Deployment
   ↓
Canary
   ↓
Version Comparison
   ↓
Latency Regression
   ↓
Affected Endpoint
   ↓
Distributed Trace
   ↓
Database Query Increase
   ↓
Root Cause
   ↓
Rollback
   ↓
Permanent Fix
   ↓
Performance Testing
   ↓
Safer Deployment
```

This case demonstrates how **observability can become an active part of the software delivery lifecycle rather than being used only after an incident occurs**.
