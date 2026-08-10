# Case Study 10: Database Performance Degradation

## Overview

This case study demonstrates how an SRE team investigates increasing application latency caused by database performance degradation.

The application is running normally:

* Pods are healthy
* CPU is moderate
* Memory is normal
* Network connectivity is stable

However, users experience slow responses.

The investigation reveals that database query latency has increased, eventually causing connection-pool saturation and application timeouts.

The investigation covers:

* Database monitoring
* Query latency
* Database CPU
* Connection pools
* Slow queries
* Locks
* Indexes
* Transactions
* Application latency
* Distributed tracing
* Logs
* DQL
* Capacity planning
* Root cause analysis
* Preventive monitoring

The central lesson is:

> **An application can appear healthy while a downstream dependency such as a database is becoming the actual bottleneck.**

---

# 1. Problem Statement

Users report:

> "The application is taking much longer to load."

The initial application dashboard shows:

```text
CPU:             48%
Memory:          55%
Pod Status:      Healthy
Network:         Healthy
```

But:

```text
P95 Latency:
280 ms → 2.4 sec
```

The SRE team begins investigating the request path.

---

# 2. Application Architecture

The application uses a relational database.

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
                     Application
                      /        \
                     /          \
                    ▼            ▼
              Cache            Database
                                  │
                                  ▼
                              DB Storage
```

The application performs database queries for most business operations.

---

# 3. Initial System State

Normal operating conditions:

```text
Application P95:       280 ms
Database P95 Query:     25 ms
DB CPU:                 45%
DB Connections:         50%
Error Rate:              0.1%
```

The system has sufficient capacity.

---

# 4. Incident Symptoms

During the incident:

```text
Metric                  Normal       Incident
------------------------------------------------
App P95                  280 ms        2.4 sec
DB Query P95              25 ms        1.8 sec
DB CPU                    45%          92%
DB Connections             50%          98%
Error Rate                 0.1%         3.5%
```

There is now a strong correlation between database degradation and application performance.

---

# 5. Golden Signals

### Traffic

```text id="2k4vmb"
Traffic:
2,000 RPS → 2,100 RPS
```

Traffic increased only slightly.

### Latency

```text id="7s6rj8"
280 ms → 2.4 sec
```

Significant increase.

### Errors

```text id="o1z3ck"
0.1% → 3.5%
```

Increasing.

### Saturation

Database CPU and connection utilization are extremely high.

```text id="6g2z6c"
DB CPU:
45% → 92%

Connections:
50% → 98%
```

---

# 6. Distributed Trace

A representative trace shows:

```text id="j7b6ps"
API Request
    │
    ▼
Application
    │
    ├── Authentication: 20 ms
    │
    ├── Business Logic: 80 ms
    │
    └── Database Query: 1.8 sec
```

The application code itself is relatively fast.

The majority of the request time is spent waiting for the database.

---

# 7. Database Query Latency

Normal:

```text id="6x8y9n"
Query:
25 ms
```

Incident:

```text id="1c7g4n"
Query:
1.8 sec
```

This immediately shifts the investigation toward the database.

---

# 8. Database CPU

The database CPU is:

```text id="c1r8p4"
92%
```

High CPU utilization indicates that the database is doing significantly more work.

Possible causes include:

```text id="y2v5cx"
Slow queries
Missing indexes
Large table scans
Too many concurrent queries
Expensive joins
Poor query plans
High transaction volume
```

---

# 9. Identify Slow Queries

The database monitoring system identifies a query consuming significant execution time.

Conceptually:

```sql id="k4p3q0"
SELECT *
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC;
```

The query is executed extremely frequently.

The table contains millions of rows.

---

# 10. Query Execution

Without an appropriate index, the database may need to examine many rows.

Conceptually:

```text id="8f2p7q"
Orders Table
────────────────────────────
Row 1
Row 2
Row 3
...
Row 5,000,000
────────────────────────────

Search customer_id
        │
        ▼
Large amount of scanning
```

This increases CPU and query latency.

---

# 11. Index

An index allows the database to find relevant records more efficiently.

Conceptually:

```text id="r6y8m2"
Without Index:

Query
  ↓
Large Table Scan
  ↓
Many Rows Examined
  ↓
High CPU
  ↓
High Latency
```

With an appropriate index:

```text id="p7v5j2"
Query
  ↓
Index Lookup
  ↓
Relevant Rows
  ↓
Lower Work
  ↓
Lower Latency
```

The exact benefit depends on the database engine, query, data distribution, and index design.

---

# 12. Composite Index

The query filters by:

```text id="0n3y9d"
customer_id
```

and sorts by:

```text id="4m5v7k"
created_at
```

A suitable composite index may improve the query.

Conceptually:

```sql id="7b1h2m"
CREATE INDEX idx_orders_customer_created
ON orders(customer_id, created_at);
```

The exact index should be validated using the database's query planner.

---

# 13. Query Plan

The team checks the query execution plan.

The problematic plan shows:

```text id="z8k4c1"
Table Scan
     ↓
Millions of Rows
     ↓
Filter
     ↓
Sort
     ↓
Result
```

An optimized plan might instead look like:

```text id="e8y5r7"
Index Lookup
     ↓
Relevant Rows
     ↓
Ordered Result
```

---

# 14. Connection Pool

The application maintains a database connection pool.

Example:

```text id="9u6m4e"
Maximum Connections = 100
```

During the incident:

```text id="3g5m2d"
Active Connections = 98
```

The pool is nearly exhausted.

---

# 15. Connection Pool Saturation

When all connections are busy:

```text id="1m9x4b"
Application Request
       │
       ▼
Connection Pool
       │
       ├── Connection available → Query
       │
       └── No connection → Wait
```

Waiting requests increase application latency.

Eventually:

```text id="n8k4t2"
Connection Timeout
       ↓
Application Error
```

---

# 16. The Performance Chain

The complete chain is:

```text id="r3f7p8"
Slow Query
    │
    ▼
Database CPU ↑
    │
    ▼
Query Execution Time ↑
    │
    ▼
Connections Remain Busy
    │
    ▼
Connection Pool Saturation
    │
    ▼
Requests Wait
    │
    ▼
Application Latency ↑
    │
    ▼
Timeouts
    │
    ▼
Errors ↑
```

This is the key relationship in the incident.

---

# 17. Transaction Locks

The team also checks database locks.

A transaction may hold a lock:

```text id="7d6x1p"
Transaction A
     │
     ▼
Locks Row
     │
     X
Transaction B
     │
     ▼
Waits
```

If transactions wait for locks:

```text id="6h5q2v"
Lock Wait Time ↑
       ↓
Query Latency ↑
       ↓
Connection Usage ↑
```

Locks can therefore create application-level performance problems.

---

# 18. Deadlocks

A deadlock can occur when two transactions wait for each other.

Conceptually:

```text id="q5k8v4"
Transaction A
   │
   ├── Locks Resource 1
   │
   └── Waits for Resource 2
             ▲
             │
             │
Transaction B
   │
   ├── Locks Resource 2
   │
   └── Waits for Resource 1
```

Neither can proceed until the database resolves the deadlock.

---

# 19. Logs

Application logs show:

```text id="j4z2m7"
WARN Database query timeout
WARN Connection pool wait exceeded threshold
ERROR Unable to acquire database connection
```

Database logs may show:

```text id="n4b6s8"
Slow query detected
High lock wait
Connection saturation
```

These logs support the metrics and traces.

---

# 20. DQL Investigation

The SRE team can query application logs for database-related failures.

For example:

```text id="9x5k1w"
fetch logs
| filter contains(content, "database")
| summarize count(), by:service.name
| sort count desc
```

The team can then correlate database errors with:

```text id="5z7h3c"
Request latency
Connection usage
Database CPU
Query duration
```

Exact field names depend on the telemetry configuration.

---

# 21. Compare Normal and Incident Periods

The team compares metrics.

```text id="m2r6s8"
Metric                  Normal     Incident
------------------------------------------------
DB CPU                    45%        92%
Query P95                 25 ms      1.8 sec
Connections               50%        98%
App P95                   280 ms      2.4 sec
Error Rate                0.1%        3.5%
```

The correlation is strong.

---

# 22. Root Cause

The investigation identifies a newly introduced query pattern that performs inefficient database access against a rapidly growing orders table.

The query lacks an appropriate supporting index.

As traffic increases:

```text id="4d7y8s"
Query Frequency ↑
       │
       ▼
Rows Examined ↑
       │
       ▼
DB CPU ↑
       │
       ▼
Query Latency ↑
       │
       ▼
Connection Pool Saturation
       │
       ▼
Application Latency ↑
       │
       ▼
Timeouts / Errors
```

---

# 23. Immediate Mitigation

The SRE and development teams consider:

```text id="8m3v6j"
Reduce expensive query traffic
Enable caching where appropriate
Scale database resources if necessary
Increase database capacity temporarily
Optimize query
Add appropriate index
```

Scaling alone should not replace fixing an inefficient query.

---

# 24. Query Optimization

The team reviews:

```text id="x5c8z1"
SELECT columns
WHERE conditions
JOIN conditions
ORDER BY
Indexes
Execution plan
```

They avoid unnecessary:

```text id="1z7f3n"
SELECT *
```

when only specific fields are required.

For example:

```sql id="8n3q2w"
SELECT order_id, created_at, status
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC;
```

The exact optimization depends on the application's requirements and database engine.

---

# 25. Index Validation

After creating an appropriate index, the team validates:

```text id="h7k5m3"
Query execution plan
Query latency
CPU utilization
Rows examined
Connection utilization
Application latency
```

They should verify that the index actually improves the workload rather than assuming it will.

---

# 26. Recovery

After optimization:

```text id="q4x8y7"
DB CPU:
92% → 55%

Query P95:
1.8 sec → 35 ms

Connections:
98% → 58%

Application P95:
2.4 sec → 300 ms

Error Rate:
3.5% → 0.2%
```

The system returns to healthy operation.

---

# 27. Capacity Planning

The incident also exposes a capacity-planning issue.

The database must be evaluated for:

```text id="3f7h2j"
CPU
Memory
Storage
IOPS
Connections
Query throughput
Transaction rate
Replication
Network throughput
```

Capacity planning should consider expected traffic growth.

---

# 28. Database Headroom

Suppose:

```text id="1j8y6k"
Safe DB CPU:
< 75%

Current:
55%
```

Headroom:

```text id="h4x3w2"
75% - 55%
= 20 percentage points
```

The team should monitor whether traffic growth will consume this headroom.

---

# 29. Predictive Monitoring

Historical database metrics can reveal trends.

```text id="r7v5m1"
DB CPU
  │
  │              /
  │            /
  │          /
  │        /
  │      /
  └──────────────── Time
```

If the trend indicates future saturation:

```text id="2m6v9p"
Forecast
   ↓
DB CPU > Safe Threshold
   ↓
Capacity Review
   ↓
Optimization / Scaling
```

This allows the organization to act before users are affected.

---

# 30. Database Performance Dashboard

A useful dashboard could contain:

```text id="5j4c8s"
┌─────────────────────────────────────────┐
│ Database Health                         │
├─────────────────────────────────────────┤
│ DB CPU                    55%           │
│ Memory                    62%           │
│ Connections               58%           │
│ Query P95                 35 ms         │
│ Slow Queries              Low           │
│ Lock Wait                 Low           │
│ Error Rate                0.2%          │
│ Capacity Headroom         Healthy       │
└─────────────────────────────────────────┘
```

---

# 31. Alerting Strategy

Useful alerts include:

```text id="0f7k3v"
Database CPU sustained above threshold
Query latency increase
Connection pool saturation
Slow query rate increase
Lock wait increase
Deadlock increase
Database error rate increase
Storage utilization increase
```

Alerts should be based on sustained conditions where appropriate to reduce noise.

---

# 32. Application-Level Protection

Applications should also protect themselves from database failures.

Possible mechanisms include:

```text id="1b7v5x"
Connection timeouts
Query timeouts
Circuit breakers
Retry policies
Bulkheads
Caching
Graceful degradation
```

Retries must be carefully configured.

Aggressive retries can increase database load during an existing database incident.

---

# 33. Caching

Frequently requested data may be cached.

Without caching:

```text id="q8z4c1"
Request
  ↓
Application
  ↓
Database
```

With appropriate caching:

```text id="k5f7d2"
Request
  ↓
Application
  ↓
Cache
  │
  ├── Hit → Response
  │
  └── Miss → Database
```

Caching can reduce database workload.

However, cache invalidation and stale-data requirements must be considered.

---

# 34. Read Replicas

For read-heavy workloads, read replicas may distribute query traffic.

Conceptually:

```text id="j4h6n9"
                 Primary DB
                /          \
               /            \
          Write            Replication
                            /      \
                           ▼        ▼
                       Replica 1  Replica 2
                           ▲        ▲
                           │        │
                         Reads    Reads
```

This can increase read capacity, but replication lag and workload characteristics must be considered.

---

# 35. Incident Timeline

```text id="x2b6v8"
09:00
Application healthy
       │
       ▼
10:00
New application feature deployed
       │
       ▼
10:30
Database query volume increases
       │
       ▼
11:00
DB CPU reaches 75%
       │
       ▼
11:20
Query latency increases
       │
       ▼
11:30
Connection pool reaches 90%
       │
       ▼
11:40
Application latency increases
       │
       ▼
11:45
Timeouts begin
       │
       ▼
12:00
SRE incident declared
       │
       ▼
12:15
Slow query identified
       │
       ▼
12:30
Execution plan analyzed
       │
       ▼
12:45
Index/query optimization implemented
       │
       ▼
13:00
Database performance recovers
       │
       ▼
13:15
Application returns to normal
```

---

# 36. Root Cause Analysis

### Immediate Cause

Database query latency increased significantly.

### Technical Root Cause

An inefficient query pattern lacked an appropriate supporting index and performed excessive database work.

### Contributing Factors

```text id="k7f2r4"
Growing dataset
Increasing traffic
High query frequency
Connection-pool saturation
Insufficient query performance testing
```

### Business Impact

```text id="6v3m8x"
Slow application responses
Request timeouts
Increased errors
Poor user experience
Potential transaction failures
```

---

# 37. What Went Well

```text id="2y6q9s"
Distributed tracing identified database wait time
Database metrics showed CPU saturation
Connection-pool metrics exposed resource pressure
Slow-query analysis identified the problematic operation
Query optimization restored performance
```

---

# 38. What Went Wrong

```text id="h5z7m2"
Query performance was not adequately tested
Database growth was underestimated
Connection saturation was detected late
Capacity planning did not account for query growth
```

---

# 39. Preventive Actions

| Action                     | Purpose                        |
| -------------------------- | ------------------------------ |
| Query performance testing  | Detect inefficient queries     |
| Execution-plan analysis    | Understand database work       |
| Index review               | Improve query efficiency       |
| Slow-query monitoring      | Detect expensive operations    |
| Connection-pool monitoring | Detect saturation              |
| Lock monitoring            | Detect contention              |
| Capacity forecasting       | Predict future database limits |
| Database dashboards        | Improve visibility             |
| Query timeout policies     | Prevent indefinite waits       |

---

# 40. SRE Concepts Demonstrated

This case study demonstrates:

```text id="7j3m1z"
SRE
Database Monitoring
Query Performance
Database CPU
Connection Pools
Indexes
Execution Plans
Transactions
Locks
Deadlocks
Distributed Tracing
DQL
Capacity Planning
Predictive Monitoring
Caching
Read Replicas
Incident Response
Root Cause Analysis
```

---

# 41. Final Incident Flow

```text id="p8n4y2"
                 APPLICATION CHANGE
                        │
                        ▼
                 INEFFICIENT QUERY
                        │
                        ▼
                 DATABASE WORK ↑
                        │
                        ▼
                    DB CPU ↑
                        │
                        ▼
                 QUERY LATENCY ↑
                        │
                        ▼
             CONNECTIONS REMAIN BUSY
                        │
                        ▼
              CONNECTION POOL SATURATES
                        │
                        ▼
                 REQUESTS WAIT
                        │
                        ▼
                APPLICATION LATENCY ↑
                        │
                        ▼
                   TIMEOUTS
                        │
                        ▼
                    ERRORS ↑
                        │
                        ▼
                   USER IMPACT
                        │
                        ▼
                QUERY OPTIMIZATION
                        │
                        ▼
                     RECOVERY
```

---

# Final Takeaway

The most important lesson from this case study is:

> **When an application becomes slow, investigate its dependencies rather than looking only at application CPU and memory.**

The investigation moved through:

```text
User Symptoms
   ↓
Application Latency
   ↓
Distributed Trace
   ↓
Database Latency
   ↓
Database CPU
   ↓
Slow Query
   ↓
Execution Plan
   ↓
Connection Pool
   ↓
Root Cause
   ↓
Query Optimization
   ↓
Validation
   ↓
Preventive Monitoring
```

A strong SRE investigation connects the entire chain:

```text
Application
     ↓
Service
     ↓
Database Query
     ↓
Database Resource
     ↓
Infrastructure
```

This is what allows observability to move beyond simply detecting **"the application is slow"** and identify **why it is slow**.
