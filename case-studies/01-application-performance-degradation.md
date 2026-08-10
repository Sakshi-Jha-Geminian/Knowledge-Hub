# Case Study 01: Application Performance Degradation

## Overview

This case study demonstrates how an SRE team can investigate and resolve an application performance degradation using observability data and Dynatrace.

The scenario focuses on an application where response times gradually increase, eventually affecting users and downstream services.

The investigation demonstrates the relationship between:

```text
Application
    ↓
Services
    ↓
Requests
    ↓
Dependencies
    ↓
Infrastructure
    ↓
Root Cause
```

---

# 1. Problem Statement

Users begin reporting that an application is becoming slow.

The application was previously responding within approximately:

```text
P95 latency: 200 ms
```

but gradually increased to:

```text
P95 latency: 1.8 seconds
```

At the same time, some requests begin timing out.

The SRE team needs to determine:

* What changed?
* Which service is responsible?
* Is the problem application-level or infrastructure-level?
* Which dependency is contributing to the latency?
* When did the degradation begin?
* What is the root cause?
* How can the problem be prevented?

---

# 2. System Context

Consider a typical e-commerce application:

```text
                    Users
                      │
                      ▼
               Load Balancer
                      │
                      ▼
                Web Frontend
                      │
                      ▼
               API Gateway
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
     User Service  Order Service  Payment Service
          │           │              │
          │           ▼              │
          │       Order DB           │
          │                          ▼
          │                     Payment DB
          │
          ▼
       User DB
```

The application is deployed on Kubernetes.

```text
Kubernetes Cluster
        │
        ├── Frontend
        ├── API Gateway
        ├── User Service
        ├── Order Service
        └── Payment Service
```

Dynatrace monitors the application, services, infrastructure, Kubernetes workloads, logs, traces, and dependencies.

---

# 3. Initial Symptoms

The first indication is a user complaint:

> "The application is very slow."

The monitoring dashboard shows:

```text
Metric              Normal       Current
-------------------------------------------
P95 Latency         200 ms       1.8 sec
P99 Latency         400 ms       4.2 sec
Error Rate          0.2%         1.8%
Request Rate        1,200 RPS    1,250 RPS
```

The important observation is:

```text
Traffic ≈ Normal
Latency ↑
Errors ↑
```

Therefore, the problem does not appear to be caused by a major traffic spike.

---

# 4. Establish the Timeline

The SRE team first determines when the degradation started.

Example:

```text
10:00 → Normal
10:10 → Normal
10:20 → Latency begins increasing
10:30 → Significant latency increase
10:40 → Errors begin increasing
10:50 → User complaints
```

This provides an incident timeline.

```text
10:20
   │
   ▼
Latency degradation begins
   │
   ▼
10:40
   │
   ▼
Errors increase
   │
   ▼
10:50
   │
   ▼
User impact
```

---

# 5. Check the Golden Signals

The SRE team evaluates the four golden signals:

```text
Latency
Traffic
Errors
Saturation
```

### Latency

```text
↑ Significant increase
```

### Traffic

```text
→ Relatively stable
```

### Errors

```text
↑ Increasing
```

### Saturation

```text
CPU → Normal
Memory → Normal
```

This suggests that the problem may not be caused by infrastructure resource exhaustion.

---

# 6. Investigate Services

The next step is to compare service performance.

Example:

```text
Service              P95 Latency
---------------------------------
Frontend              1.9 sec
API Gateway           1.8 sec
User Service          220 ms
Order Service         1.7 sec
Payment Service       250 ms
```

The Order Service immediately stands out.

```text
Order Service
     │
     ▼
P95 = 1.7 sec
```

This narrows the investigation.

---

# 7. Analyze Service Flow

The service flow shows:

```text
API Gateway
     │
     ▼
Order Service
     │
     ├──────────► User Service
     │
     ├──────────► Order Database
     │
     └──────────► Payment Service
```

The SRE team investigates each dependency.

---

# 8. Dependency Latency

The observed dependency behavior is:

```text
Dependency             Latency
--------------------------------
User Service             180 ms
Order Database         1,400 ms
Payment Service          200 ms
```

The database is the clear outlier.

```text
Order Service
      │
      ▼
 Order Database
      │
      ▼
High Query Latency
```

The investigation now moves toward the database.

---

# 9. Database Investigation

The database dashboard shows:

```text
CPU                  55%
Memory               60%
Connections          Normal
Disk I/O             Normal
Query Latency        ↑ High
```

This is an important observation.

The database itself is not necessarily resource-exhausted.

Instead:

```text
Database Resources → Normal
Query Performance  → Poor
```

This suggests a query-level or database workload issue.

---

# 10. Trace Investigation

Distributed tracing is used to identify which operation consumes the request time.

A slow request appears as:

```text
API Gateway
    │
    └── Order Service
            │
            └── Database Query
                    │
                    └── 1.4 sec
```

Normal behavior:

```text
Database Query
     ↓
~100 ms
```

Current behavior:

```text
Database Query
     ↓
~1,400 ms
```

The database query becomes the primary suspect.

---

# 11. Identify the Slow Query

The SRE team identifies a query similar to:

```sql
SELECT *
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC;
```

The query previously completed quickly.

After a recent data growth event, the table has become significantly larger.

The query now performs an expensive scan.

---

# 12. Database Execution Analysis

The database team examines the query execution plan.

The query is performing a large scan instead of efficiently using an appropriate index.

Conceptually:

```text
Before:

Query
  ↓
Index
  ↓
Small Data Set
  ↓
Fast Result
```

Current behavior:

```text
Query
  ↓
Large Table Scan
  ↓
Millions of Rows
  ↓
Slow Result
```

---

# 13. Root Cause

The root cause is identified as:

> A frequently executed order lookup query is performing inefficient scans because the database does not have an appropriate index for the query pattern.

This causes:

```text
Database Query Latency
        ↓
Order Service Latency
        ↓
API Latency
        ↓
User Experience Degradation
```

---

# 14. Why CPU Was Not Extremely High

A common mistake would be:

> "Database CPU is only 55%, so the database cannot be the problem."

This is incorrect.

A database can experience performance degradation because of:

```text
Poor Query Plan
Missing Index
Lock Contention
Inefficient Joins
Disk Latency
Network Latency
Connection Problems
Large Result Sets
```

without necessarily reaching 100% CPU.

---

# 15. Remediation

The database team adds an appropriate index for the query pattern.

Conceptually:

```sql
CREATE INDEX ...
ON orders(customer_id, created_at);
```

The exact index definition must be validated against the database engine and workload.

---

# 16. Post-Fix Results

After deploying the database change:

```text
Metric              Before       After
-----------------------------------------
P95 Latency         1.8 sec      210 ms
P99 Latency         4.2 sec      380 ms
Error Rate          1.8%         0.2%
DB Query Latency    1.4 sec      90 ms
```

The application returns to normal behavior.

---

# 17. Incident Timeline

The complete timeline becomes:

```text
10:00
Normal application behavior
       │
       ▼
10:20
Database query latency increases
       │
       ▼
10:30
Order Service latency increases
       │
       ▼
10:40
Application error rate increases
       │
       ▼
10:50
Users report slow application
       │
       ▼
11:05
SRE identifies Order Service
       │
       ▼
11:15
Distributed trace identifies database query
       │
       ▼
11:30
Slow query identified
       │
       ▼
11:45
Index optimization deployed
       │
       ▼
12:00
Application returns to normal
```

---

# 18. Dynatrace Investigation Flow

A practical investigation could follow:

```text
Problem Detected
       │
       ▼
Application Overview
       │
       ▼
Check Golden Signals
       │
       ▼
Identify Slow Service
       │
       ▼
Service Flow
       │
       ▼
Identify Slow Dependency
       │
       ▼
Distributed Trace
       │
       ▼
Identify Slow Operation
       │
       ▼
Database Investigation
       │
       ▼
Root Cause
```

---

# 19. DQL Investigation

DQL can support the investigation by querying relevant telemetry.

For example, the team may investigate error volume:

```text
fetch logs
| filter loglevel == "ERROR"
| summarize count(), by:service.name
| sort count desc
```

This helps determine which service is generating the largest number of errors.

---

# 20. Latency Analysis

The team can analyze latency by service using an appropriate span dataset and duration field.

Conceptually:

```text
fetch spans
| summarize avg(duration), by:service.name
| sort avg(duration) desc
```

The exact field names and query syntax should be validated against the telemetry available in the Dynatrace environment.

---

# 21. Compare Before and After

One of the most important incident-analysis techniques is comparing:

```text
Before Incident
        vs
During Incident
        vs
After Remediation
```

Example:

```text
             Before     Incident     After
---------------------------------------------
Latency       200 ms      1.8 sec     210 ms
Errors        0.2%        1.8%        0.2%
DB Query      90 ms       1.4 sec      90 ms
```

This provides strong evidence that the database query was responsible for the degradation.

---

# 22. Business Impact

Although the technical root cause was a database query problem, the business impact was:

```text
Slow application
      ↓
Poor customer experience
      ↓
Failed / timed-out requests
      ↓
Potential abandoned transactions
      ↓
Potential revenue impact
```

This demonstrates why SRE investigations should connect technical metrics with business impact.

---

# 23. Preventive Actions

The team implements several preventive measures.

### Monitoring

Monitor:

```text
Database query latency
P95/P99 service latency
Error rate
Slow queries
```

### Alerting

Create appropriate alerts for:

```text
Unexpected latency increases
Abnormal error rates
Database query degradation
```

### Performance Testing

Include realistic database volumes in performance tests.

### Database Review

Review query plans for frequently executed queries.

### Capacity Planning

Track:

```text
Data growth
Query volume
Database size
Storage
Performance trends
```

---

# 24. Predictive Monitoring Opportunity

Historical data can reveal a gradual degradation before it becomes a major incident.

For example:

```text
Week 1 → 80 ms
Week 2 → 95 ms
Week 3 → 120 ms
Week 4 → 160 ms
Week 5 → 220 ms
Week 6 → 400 ms
```

The increasing trend could indicate:

```text
Data Growth
     ↓
Query Cost Increasing
     ↓
Latency Increasing
     ↓
Future Performance Risk
```

This is where predictive monitoring becomes valuable.

---

# 25. Lessons Learned

### Lesson 1

Do not investigate only infrastructure metrics.

Application performance can degrade even when CPU and memory look normal.

### Lesson 2

Distributed tracing helps connect user-facing latency to the exact downstream operation causing the delay.

### Lesson 3

Dependency analysis is essential in microservice environments.

### Lesson 4

A single metric rarely provides enough information to determine root cause.

### Lesson 5

Historical trends can reveal problems before they become incidents.

### Lesson 6

Monitoring should cover both technical and business impact.

---

# 26. SRE Concepts Demonstrated

This case study demonstrates:

```text
Observability
Monitoring
Golden Signals
Distributed Tracing
Service Flow
Dependency Analysis
DQL
Root Cause Analysis
Incident Response
Performance Engineering
Database Optimization
Capacity Planning
Predictive Monitoring
```

---

# 27. Final Architecture

```text
                         USERS
                           │
                           ▼
                    LOAD BALANCER
                           │
                           ▼
                     API GATEWAY
                           │
                           ▼
                    ORDER SERVICE
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
        USER SERVICE   ORDER DB    PAYMENT SERVICE
                           │
                           ▼
                    SLOW QUERY
                           │
                           ▼
                  PERFORMANCE ISSUE
                           │
                           ▼
                    HIGH LATENCY
                           │
                           ▼
                    USER IMPACT
```

After remediation:

```text
Slow Query
    │
    ▼
Query Optimization
    │
    ▼
Database Index
    │
    ▼
Lower Query Latency
    │
    ▼
Lower Service Latency
    │
    ▼
Normal User Experience
```

---

# 28. Final Takeaway

The most important lesson from this case study is:

> **Do not stop at the first abnormal metric. Follow the dependency chain until the evidence identifies the actual root cause.**

The investigation moved through:

```text
User Complaint
      ↓
Application Latency
      ↓
Service Analysis
      ↓
Dependency Analysis
      ↓
Distributed Trace
      ↓
Database Query
      ↓
Root Cause
      ↓
Remediation
      ↓
Validation
```

This is a practical example of how **observability enables SRE teams to move from symptom → evidence → root cause → resolution → prevention**.
