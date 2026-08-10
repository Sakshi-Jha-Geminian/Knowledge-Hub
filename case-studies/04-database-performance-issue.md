# Case Study 04: Database Performance Issue

## Overview

This case study demonstrates how an SRE team investigates an application slowdown caused by database performance degradation.

The incident begins with increasing application latency and eventually results in request timeouts.

The investigation moves from the application layer to the database layer using:

* Application metrics
* Service flow
* Distributed tracing
* Database metrics
* Query performance
* Connection pools
* Lock contention
* Logs
* DQL
* Dynatrace
* Root cause analysis
* Preventive monitoring

The central lesson is:

> Application performance problems often originate in dependencies. The correct approach is to follow the request path until the slow operation is identified.

---

# 1. Problem Statement

A production application begins experiencing increasing response times.

Users report:

> "Pages are taking too long to load."

The monitoring dashboard shows:

```text
Metric                  Normal       Incident
------------------------------------------------
P95 Latency              250 ms       2.4 sec
P99 Latency              500 ms       6.8 sec
Error Rate               0.2%         3.5%
Request Rate             2,000 RPS    2,050 RPS
```

The request rate has remained relatively stable.

The primary problem is latency.

---

# 2. System Architecture

The application consists of multiple services.

```text id="z9h8g5"
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
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        User Service   Order Database   Payment Service
                           │
                           ▼
                      Database Server
```

The Order Service is heavily dependent on the database.

---

# 3. Initial Symptoms

The application dashboard shows:

```text id="x7u4fz"
Latency       ↑
Errors        ↑
Traffic       →
CPU           →
Memory        →
```

This suggests:

```text id="l3d3q7"
Application Resources
       ↓
     Normal

Application Performance
       ↓
    Degraded
```

The SRE team therefore investigates downstream dependencies.

---

# 4. Golden Signals

The four golden signals are reviewed.

### Latency

```text id="xg4h6q"
P95: 250 ms → 2.4 sec
```

Significant increase.

### Traffic

```text id="yq0d5a"
2,000 RPS → 2,050 RPS
```

Relatively stable.

### Errors

```text id="5f6g2f"
0.2% → 3.5%
```

Increasing.

### Saturation

Application CPU:

```text id="h6s0fa"
45% → 52%
```

Application memory:

```text id="n5is9c"
55% → 60%
```

Neither explains the latency increase.

---

# 5. Service-Level Investigation

The SRE team compares service latency.

```text id="l1e1w2"
Service              P95 Latency
---------------------------------
API Gateway             2.3 sec
User Service            220 ms
Order Service           2.2 sec
Payment Service         250 ms
```

The Order Service is the main outlier.

---

# 6. Service Flow

The service flow is:

```text id="p7q0sp"
API Gateway
      │
      ▼
Order Service
      │
      ├────────► User Service
      │
      ├────────► Order Database
      │
      └────────► Payment Service
```

The Order Database is investigated next.

---

# 7. Distributed Trace

A slow request trace shows:

```text id="7t2xw1"
API Gateway
    │
    ▼
Order Service
    │
    ├── User Service
    │      └── 180 ms
    │
    ├── Order Database
    │      └── 1,900 ms
    │
    └── Payment Service
           └── 200 ms
```

The database call consumes most of the request time.

This is strong evidence that the database is contributing to the application slowdown.

---

# 8. Database Investigation

The database dashboard shows:

```text id="m4a4yh"
Metric                  Normal       Incident
------------------------------------------------
CPU                       55%          82%
Memory                    60%          72%
Connections               45%          92%
Query Latency             80 ms        1.7 sec
Disk I/O                  Normal       High
```

Several signals are abnormal:

```text
CPU ↑
Connections ↑
Query Latency ↑
Disk I/O ↑
```

---

# 9. Database Saturation

Database saturation occurs when demand approaches or exceeds the resources available to process requests.

Examples include:

```text id="r7g7u6"
CPU saturation
Memory pressure
Disk I/O saturation
Connection exhaustion
Lock contention
```

In this case, connection utilization is particularly concerning.

```text id="m9d0by"
Maximum Connections
        │
        ▼
      100
        │
        ▼
Current
92
```

The database is approaching its connection limit.

---

# 10. Connection Pool Investigation

The Order Service uses a database connection pool.

Conceptually:

```text id="1br7sj"
Order Service
      │
      ▼
Connection Pool
      │
 ┌────┼────┐
 ▼    ▼    ▼
 DB   DB   DB
```

The connection pool statistics show:

```text id="6qg3s0"
Pool Size:             100
Active Connections:     95
Idle Connections:        5
Waiting Requests:       32
```

The waiting queue indicates that application threads are waiting for database connections.

---

# 11. Why Connection Exhaustion Causes Latency

The request flow becomes:

```text id="0kwr7r"
Incoming Request
       │
       ▼
Needs DB Connection
       │
       ▼
Pool Has No Free Connection
       │
       ▼
Request Waits
       │
       ▼
Connection Becomes Available
       │
       ▼
Query Executes
       │
       ▼
Response
```

The waiting time contributes directly to application latency.

---

# 12. Identify Long-Running Queries

The team investigates database queries.

Example:

```text id="6hxv99"
Query                           Duration
-----------------------------------------
Order Lookup                    120 ms
Customer Lookup                  90 ms
Order History                  1,800 ms
Payment Validation              110 ms
```

The Order History query is the clear outlier.

---

# 13. Query Analysis

The problematic query resembles:

```sql id="3q0zdr"
SELECT *
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC;
```

The query is executed extremely frequently.

The table has grown significantly.

```text id="3w7f4a"
Earlier:
1 million rows

Current:
25 million rows
```

The query now requires substantially more work.

---

# 14. Execution Plan

The database team examines the execution plan.

The query is performing an expensive scan.

Conceptually:

```text id="l4j83q"
Query
  │
  ▼
Table Scan
  │
  ▼
25 Million Rows
  │
  ▼
Sort
  │
  ▼
Result
```

This is inefficient.

---

# 15. Missing Index

The query filters by:

```text id="5pxr0k"
customer_id
```

and sorts by:

```text id="n6f2f8"
created_at
```

An appropriate composite index can potentially make this access pattern much more efficient.

Conceptually:

```sql id="9ecx4s"
CREATE INDEX ...
ON orders(customer_id, created_at);
```

The exact syntax and index design should be validated against the database engine and workload.

---

# 16. Lock Investigation

The SRE team also checks whether locks are contributing to the latency.

Conceptually:

```text id="l3w0lq"
Transaction A
      │
      ▼
Locks Row
      │
      X
Transaction B
      │
      ▼
Must Wait
```

If many transactions are waiting for locks, query latency can increase even when CPU is not fully saturated.

---

# 17. Lock Metrics

Example:

```text id="p0x4l6"
Metric                    Value
--------------------------------
Active Locks                240
Waiting Transactions         85
Lock Wait Time              High
```

This requires further investigation.

The team must determine whether lock contention is:

```text id="g7q0on"
Primary Cause
     or
Secondary Effect
```

---

# 18. Distinguishing Query Latency from Lock Wait

A query may be slow because:

```text id="h5k0xj"
Query Execution
```

or because it spends time waiting:

```text id="f2p9a0"
Waiting for Lock
```

The distinction matters.

Conceptually:

```text id="3y6c4k"
Total Query Time
       =
Execution Time
+
Wait Time
```

If wait time dominates, optimizing the SQL query alone may not solve the problem.

---

# 19. Logs

Application logs contain messages such as:

```text id="v2l2uy"
Database connection acquisition timeout
Query execution exceeded threshold
Request timed out
```

These logs correlate with the database metrics.

The evidence now spans:

```text id="z4b6k5"
Application
   ↓
Connection Pool
   ↓
Database
   ↓
Slow Query
```

---

# 20. DQL Investigation

The SRE team can use DQL to investigate relevant application logs.

For example:

```text id="t7b9fj"
fetch logs
| filter loglevel == "ERROR"
| filter contains(content, "database")
```

This can help identify database-related application errors.

The exact dataset and fields depend on the telemetry configuration.

---

# 21. Analyze Errors Over Time

A conceptual time-series investigation can be used:

```text id="q3x0d8"
fetch logs
| filter loglevel == "ERROR"
| makeTimeseries count()
```

The SRE team compares the error increase with:

```text id="2m6x6r"
Database latency
Connection usage
Deployment events
Traffic
```

---

# 22. Historical Comparison

The database was previously healthy.

```text id="r1u7j8"
Week 1:
80 ms

Week 2:
85 ms

Week 3:
100 ms

Week 4:
150 ms

Week 5:
300 ms

Week 6:
700 ms

Week 7:
1.7 sec
```

The gradual increase indicates that the problem may have been developing for some time.

---

# 23. Data Growth

Database size has increased:

```text id="n8p2c0"
10 GB
 ↓
20 GB
 ↓
40 GB
 ↓
70 GB
 ↓
100 GB
```

The query performance has degraded alongside data growth.

This indicates a potential capacity and query-design issue rather than a sudden infrastructure failure.

---

# 24. Root Cause

The root cause is identified as:

> A frequently executed order-history query became increasingly expensive as the orders table grew. The query lacked an appropriate index, resulting in long execution times, increased connection occupancy, and eventually connection-pool exhaustion.

The complete chain is:

```text id="g6z6l5"
Data Growth
     │
     ▼
Large Orders Table
     │
     ▼
Inefficient Query
     │
     ▼
Long Query Execution
     │
     ▼
Connections Occupied Longer
     │
     ▼
Connection Pool Saturation
     │
     ▼
Requests Waiting
     │
     ▼
Application Latency ↑
     │
     ▼
Timeouts / Errors
     │
     ▼
User Impact
```

---

# 25. Immediate Mitigation

The team needs to reduce customer impact.

Possible actions:

```text id="7z5zq9"
Increase connection pool carefully
Reduce expensive query traffic
Enable caching where appropriate
Temporarily scale application instances
Optimize query
Add appropriate index
```

The preferred permanent solution is query optimization rather than indefinitely increasing connection limits.

---

# 26. Index Optimization

The database team validates and implements an appropriate index.

Conceptually:

```text id="v5p0j9"
Before:

Query
 ↓
Large Table Scan
 ↓
Sort
 ↓
Slow Result
```

After:

```text id="f3x1kr"
Query
 ↓
Index
 ↓
Relevant Rows
 ↓
Fast Result
```

---

# 27. Query Result After Optimization

Example:

```text id="8p2s4e"
Before:
1,800 ms

After:
80 ms
```

This dramatically reduces database connection occupancy.

---

# 28. Connection Pool Recovery

Before optimization:

```text id="8i9p1g"
Active:      95/100
Waiting:     32
```

After optimization:

```text id="6h1k4f"
Active:      40/100
Waiting:      0
```

The database returns to a healthy operating range.

---

# 29. Application Recovery

After the database optimization:

```text id="q4x7v8"
Metric                  Before       After
------------------------------------------------
P95 Latency              2.4 sec       240 ms
P99 Latency              6.8 sec       450 ms
Error Rate               3.5%          0.2%
DB Query Latency          1.7 sec        80 ms
DB Connections              92%           40%
```

The application returns to normal behavior.

---

# 30. Why Increasing Connection Limits Is Not the Root Fix

Suppose the team changes:

```text id="f8e2b8"
Maximum Connections:
100 → 500
```

The application may temporarily handle more waiting requests.

But the expensive query still exists.

```text id="v1z8j1"
Slow Query
   ↓
Connections held longer
   ↓
More connections required
   ↓
Higher DB load
   ↓
Potential database instability
```

Increasing connections without fixing query performance can make the situation worse.

---

# 31. Capacity Planning

Database capacity planning should consider:

```text id="4m9qf8"
Data Volume
Query Volume
Query Complexity
Connection Demand
CPU
Memory
Disk I/O
Storage Growth
Peak Traffic
```

Historical trends can help estimate when thresholds may be reached.

---

# 32. Predictive Monitoring

The historical trend can be used proactively.

For example:

```text id="7d7j1z"
Query Latency
    │
    ├── 80 ms
    ├── 100 ms
    ├── 150 ms
    ├── 300 ms
    ├── 700 ms
    └── 1.7 sec
```

A predictive alert could identify:

```text id="y0f4cc"
Performance degradation trend
          ↓
Expected future threshold breach
          ↓
Engineering investigation
          ↓
Optimization
```

The issue can potentially be fixed before customers experience severe impact.

---

# 33. Preventive Monitoring

The team introduces monitoring for:

```text id="4q3k19"
Slow query latency
Query execution time
Connection pool utilization
Connection wait time
Lock wait time
Database CPU
Database memory
Disk I/O
Storage growth
```

---

# 34. Alerting Strategy

Alerts should focus on meaningful symptoms.

Examples:

```text id="8u0dqn"
P95 database query latency above threshold
Connection pool utilization sustained above threshold
Large increase in lock wait time
Rapid database storage growth
Unexpected increase in query execution time
```

Alerts should include sufficient context to help responders investigate quickly.

---

# 35. Database Performance Dashboard

A useful dashboard could contain:

```text id="a6n8ki"
┌──────────────────────────────────────┐
│ Database Health                      │
├──────────────────────────────────────┤
│ CPU             ███████░░░            │
│ Memory          ██████░░░░            │
│ Connections     █████░░░░░            │
│ Query Latency   ███░░░░░░░            │
│ Disk I/O        ██████░░░░            │
│ Lock Wait       ██░░░░░░░░            │
└──────────────────────────────────────┘
```

The dashboard should allow operators to move from high-level health to individual queries.

---

# 36. Runbook

A database performance runbook could follow:

```text id="e7l7s0"
1. Confirm application impact
        ↓
2. Check database latency
        ↓
3. Check connections
        ↓
4. Check CPU / memory / I/O
        ↓
5. Check lock contention
        ↓
6. Identify slow queries
        ↓
7. Inspect query plans
        ↓
8. Check recent changes
        ↓
9. Apply mitigation
        ↓
10. Validate recovery
```

---

# 37. Incident Timeline

```text id="1c6qpx"
09:00
Database healthy
      │
      ▼
10:00
Query latency begins increasing
      │
      ▼
10:30
Database connections increase
      │
      ▼
11:00
Order Service latency increases
      │
      ▼
11:15
Connection pool starts queueing requests
      │
      ▼
11:30
Application timeouts increase
      │
      ▼
11:40
SRE investigates database dependency
      │
      ▼
12:00
Slow order-history query identified
      │
      ▼
12:20
Execution plan analyzed
      │
      ▼
12:40
Index optimization implemented
      │
      ▼
13:00
Query latency returns to normal
      │
      ▼
13:15
Application fully recovered
```

---

# 38. Root Cause Analysis

### Immediate Cause

Database queries were taking significantly longer than normal.

### Technical Root Cause

An inefficient query performed poorly as the underlying dataset grew.

### Contributing Factors

```text id="0pm2h"
Missing appropriate index
Increasing data volume
High query frequency
Insufficient performance monitoring
Connection pool saturation
```

### Business Impact

```text id="9u7z0g"
Slow application
Request timeouts
Failed requests
Poor user experience
Potential transaction delays
```

---

# 39. What Went Well

```text id="e8b5vr"
Distributed tracing identified database latency
Database metrics were available
Historical trends showed gradual degradation
Logs provided connection timeout evidence
Query optimization restored performance
```

---

# 40. What Went Wrong

```text id="a1g2u6"
Query performance was not reviewed as data grew
No early alert on query latency trend
Connection pool saturation was detected late
Database growth was not adequately correlated with performance
```

---

# 41. Preventive Actions

| Action                       | Purpose                        |
| ---------------------------- | ------------------------------ |
| Query performance monitoring | Detect slow queries            |
| Query-plan review            | Identify inefficient execution |
| Index review                 | Improve lookup performance     |
| Connection monitoring        | Detect pool saturation         |
| Lock monitoring              | Detect contention              |
| Database growth monitoring   | Plan capacity                  |
| Load testing                 | Validate performance at scale  |
| Slow-query alerts            | Detect regressions early       |
| Performance runbook          | Standardize response           |

---

# 42. SRE Concepts Demonstrated

This case study demonstrates:

```text
Observability
SRE
Database Monitoring
Distributed Tracing
Service Dependencies
Query Performance
Connection Pools
Connection Saturation
Lock Contention
Indexes
Query Plans
DQL
Capacity Planning
Performance Engineering
Incident Response
Root Cause Analysis
Predictive Monitoring
```

---

# 43. Final Incident Flow

```text
                    DATA GROWTH
                         │
                         ▼
                 LARGE DATABASE
                         │
                         ▼
                INEFFICIENT QUERY
                         │
                         ▼
                LONG QUERY TIME
                         │
                         ▼
              CONNECTIONS HELD LONGER
                         │
                         ▼
              CONNECTION POOL SATURATION
                         │
                         ▼
                 REQUESTS WAITING
                         │
                         ▼
                   LATENCY ↑
                         │
                         ▼
                   TIMEOUTS ↑
                         │
                         ▼
                  USER IMPACT
                         │
                         ▼
                  QUERY OPTIMIZATION
                         │
                         ▼
                   INDEX ADDED
                         │
                         ▼
                DATABASE RECOVERED
                         │
                         ▼
                APPLICATION RECOVERED
```

---

# Final Takeaway

The most important lesson from this case study is:

> **When an application becomes slow, investigate its dependencies instead of assuming the application itself is the bottleneck. Database query performance, connection pools, locks, and data growth can all translate into application-level latency.**

The investigation moved through:

```text
User Symptoms
      ↓
Application Latency
      ↓
Affected Service
      ↓
Distributed Trace
      ↓
Database Dependency
      ↓
Database Metrics
      ↓
Connection Pool
      ↓
Slow Query
      ↓
Execution Plan
      ↓
Root Cause
      ↓
Query Optimization
      ↓
Validation
      ↓
Preventive Monitoring
```

This case demonstrates how **application observability, database observability, distributed tracing, and SRE practices work together to identify and resolve performance bottlenecks**.
