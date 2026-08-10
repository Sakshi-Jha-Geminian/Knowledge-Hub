# Case Study 12: Incident Response and Postmortem

## Overview

This case study demonstrates how an SRE team handles a major production incident from initial detection through recovery and postmortem.

Unlike the previous case studies, this scenario focuses on the **complete incident-management lifecycle**.

It covers:

* Incident detection
* Alerting
* Incident declaration
* Severity classification
* Triage
* Incident command
* Communication
* Investigation
* Mitigation
* Recovery
* Monitoring
* Root cause analysis
* Five Whys
* Contributing factors
* Postmortem
* Corrective actions
* Preventive actions
* SLOs
* Error budgets
* Incident metrics

The central lesson is:

> **A mature SRE organization does not only fix incidents; it learns from them and improves the system so similar incidents become less likely.**

---

# 1. Problem Statement

A production e-commerce application begins experiencing severe performance degradation during peak traffic.

Customers report:

```text
Checkout is slow.
Orders are failing.
Payments are timing out.
```

The monitoring system detects:

```text
Error Rate ↑
Latency ↑
Database CPU ↑
Checkout Success ↓
```

The incident is classified as high severity.

---

# 2. Application Architecture

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
         Product Service  Cart Service  Checkout Service
                                             │
                              ┌──────────────┼──────────────┐
                              ▼              ▼              ▼
                         Payment        Order DB        Inventory
                         Gateway
```

Checkout depends on multiple downstream systems.

Therefore, a failure in one component can affect the entire transaction.

---

# 3. Initial Monitoring

At 10:00 AM:

```text
Traffic:              4,000 RPS
Error Rate:             0.2%
P95 Latency:            300 ms
Checkout Success:      99.7%
Database CPU:           55%
```

Everything is healthy.

---

# 4. Incident Begins

At 10:17 AM:

```text
Traffic:              4,800 RPS
Error Rate:             4.5%
P95 Latency:           2.8 sec
Checkout Success:      94%
Database CPU:           88%
```

Multiple alerts trigger.

---

# 5. Detection

The primary alert is:

```text
Checkout Success Rate < SLO
```

Additional alerts:

```text
High API latency
Database CPU high
Payment timeout rate high
Connection pool saturation
```

This indicates a potentially major incident.

---

# 6. Incident Declaration

The on-call engineer declares an incident.

Example:

```text
Incident ID:
INC-2026-0810-001

Severity:
SEV-1

Service:
Checkout

Status:
Investigating
```

The incident is formally tracked.

---

# 7. Severity Classification

Severity should be based on customer and business impact.

Example:

### SEV-1

Critical customer impact.

```text
Major functionality unavailable
Large number of customers affected
Significant business impact
```

### SEV-2

Significant but limited impact.

### SEV-3

Moderate or localized impact.

### SEV-4

Minor issue or informational event.

The exact definitions should follow the organization's incident policy.

---

# 8. Incident Roles

The incident commander assigns roles.

```text
Incident Commander
        │
        ├── Technical Lead
        │
        ├── Communications Lead
        │
        ├── Investigation Team
        │
        └── Scribe
```

---

# 9. Incident Commander

The Incident Commander is responsible for coordinating the incident.

Responsibilities include:

```text
Set priorities
Coordinate teams
Make escalation decisions
Maintain incident timeline
Ensure communication
Approve mitigation strategy
Drive recovery
```

The Incident Commander does not necessarily perform every technical investigation personally.

---

# 10. Technical Lead

The Technical Lead coordinates technical investigation.

Initial questions:

```text
What changed?
What is failing?
When did it start?
Which services are affected?
Is the issue internal or external?
Can we mitigate immediately?
```

---

# 11. Communications Lead

The Communications Lead keeps stakeholders informed.

Example update:

```text
10:25 AM

Checkout transactions are currently experiencing
elevated failures and latency.

Engineering is investigating the issue.

Next update: 10:40 AM.
```

Communication should be factual and avoid speculation.

---

# 12. Scribe

The Scribe maintains the timeline.

Example:

```text
10:17 Alert triggered
10:20 Incident declared
10:25 Investigation started
10:35 Database identified as bottleneck
10:45 Mitigation started
11:00 Error rate declining
11:15 Service recovered
```

The timeline becomes valuable during the postmortem.

---

# 13. Initial Triage

The team checks the Golden Signals.

```text
Traffic       ↑
Latency       ↑
Errors        ↑
Saturation    ↑
```

All four are elevated.

This indicates a serious service degradation.

---

# 14. Scope Identification

The team determines which services are affected.

```text
Product Service       ✓
Cart Service          ✓
Checkout Service      ✗
Payment Gateway       ⚠
Order Database        ⚠
```

The issue is concentrated around checkout.

---

# 15. Change Investigation

The first question is:

> What changed shortly before the incident?

The deployment history shows:

```text
10:05 AM
Checkout Service v4.8 deployed
```

The incident began:

```text
10:17 AM
```

This is a strong correlation.

---

# 16. Deployment Comparison

Previous version:

```text
v4.7
```

Current version:

```text
v4.8
```

The team compares:

```text
Code changes
Database queries
Configuration
Dependencies
Resource usage
Feature flags
```

---

# 17. Distributed Trace

A checkout trace shows:

```text
Checkout Request
       │
       ▼
Checkout Service
       │
       ├── Cart: 50 ms
       │
       ├── Inventory: 80 ms
       │
       └── Order DB: 2.1 sec
```

The database call is consuming most of the request duration.

---

# 18. Database Investigation

Database metrics show:

```text
CPU:              94%
Connections:       98%
Query Latency:    1.9 sec
Lock Wait:        High
```

The database is severely saturated.

---

# 19. Query Investigation

The team identifies a new query introduced in version 4.8.

Conceptually:

```sql
SELECT *
FROM orders
WHERE customer_id = ?
ORDER BY created_at DESC;
```

The query is executed for every checkout.

The table contains millions of records.

---

# 20. Query Plan

The execution plan shows:

```text
Table Scan
     ↓
Large Number of Rows
     ↓
Filter
     ↓
Sort
     ↓
Result
```

This is expensive.

---

# 21. Load Amplification

Traffic increased from:

```text
4,000 RPS
```

to:

```text
4,800 RPS
```

But database workload increased disproportionately.

Why?

Because the new query performs much more work per request.

Conceptually:

```text
Traffic ↑ 20%
       │
       ▼
Query Work ↑ 200%+
       │
       ▼
DB CPU ↑
       │
       ▼
Connection Saturation
```

This is an example of workload amplification.

---

# 22. Root Cause Hypothesis

The team forms a hypothesis:

> Checkout Service v4.8 introduced an inefficient database query that caused excessive database work under production traffic.

The hypothesis must be validated before declaring it the root cause.

---

# 23. Mitigation Options

The team considers:

```text
Rollback deployment
Disable feature flag
Scale database
Add index
Reduce traffic
Enable caching
Increase connection pool
```

The safest immediate action is usually the one that restores service quickly while minimizing additional risk.

---

# 24. Rollback Decision

Because the incident started shortly after deployment:

```text
v4.8 → Incident
v4.7 → Previously Healthy
```

the Incident Commander approves rollback.

```text
Checkout v4.8
       │
       ▼
Rollback
       │
       ▼
Checkout v4.7
```

---

# 25. Rollback

The deployment is reverted.

Database metrics begin improving.

```text
DB CPU:
94% → 78% → 60%
```

Connection utilization:

```text
98% → 80% → 55%
```

---

# 26. Application Recovery

Application metrics recover:

```text
P95 Latency:
2.8 sec → 500 ms → 300 ms

Error Rate:
4.5% → 1.2% → 0.2%

Checkout Success:
94% → 98.8% → 99.8%
```

The service returns to normal.

---

# 27. Recovery Validation

The team does not immediately close the incident.

They validate:

```text
Checkout success rate
Latency
Error rate
Database CPU
Database connections
Pod health
Payment success
User experience
```

The system must remain stable.

---

# 28. Incident Resolution

At 11:15 AM:

```text
Checkout Success:    99.8%
Error Rate:           0.2%
P95 Latency:          300 ms
DB CPU:               55%
```

The Incident Commander declares:

```text
Incident Status:
RESOLVED
```

---

# 29. Customer Impact

The team calculates the impact.

Suppose:

```text
Affected Duration:
58 minutes

Checkout Attempts:
278,000

Failed Checkouts:
16,000
```

The impact should be quantified using reliable telemetry.

---

# 30. SLO Impact

Suppose checkout SLO is:

```text
99.5% successful transactions
```

During the incident:

```text
94%
```

Therefore, the service experienced a significant SLO violation.

---

# 31. Error Budget

The error budget is:

```text
0.5%
```

Actual failure rate:

```text
6%
```

The incident consumed a large portion of the available error budget.

This may affect future release decisions.

---

# 32. Postmortem

After the incident, the team conducts a blameless postmortem.

The purpose is not:

```text
Who caused the incident?
```

The purpose is:

```text
Why did the system allow this failure?
```

---

# 33. Five Whys

### Why did checkout requests fail?

Because database requests timed out.

### Why did database requests time out?

Because the database was overloaded.

### Why was the database overloaded?

Because a new query generated excessive database work.

### Why did the inefficient query reach production?

Because query performance was not adequately tested at production-scale data volume.

### Why was performance testing insufficient?

Because the deployment process did not include a workload-based database performance gate.

---

# 34. Root Cause

The technical root cause:

> A new checkout query caused excessive database workload under production traffic and data volume.

The systemic root cause:

> The delivery process lacked sufficient production-scale database performance validation.

This distinction is important.

---

# 35. Contributing Factors

The incident was also influenced by:

```text
Large production dataset
High peak traffic
Missing query performance gate
Insufficient load testing
No automatic rollback based on database saturation
Limited database capacity headroom
```

---

# 36. What Went Well

```text
Monitoring detected checkout SLO degradation
Distributed tracing identified database latency
Deployment history allowed quick correlation
Rollback restored service
Incident roles were clearly defined
Stakeholder communication was maintained
```

---

# 37. What Went Wrong

```text
Inefficient query reached production
Performance testing used insufficient data volume
Database saturation alert triggered late
No automated deployment rollback
Database headroom was lower than expected
```

---

# 38. Corrective Actions

Corrective actions fix the immediate problem.

```text
Optimize query
Add appropriate database index
Improve execution plan
Review connection usage
Increase database capacity if necessary
```

---

# 39. Preventive Actions

Preventive actions reduce the chance of recurrence.

```text
Production-scale performance testing
Query performance gates
Database observability
Deployment health checks
Automated rollback
Capacity forecasting
Slow-query alerts
Database SLO monitoring
```

---

# 40. Action Item Format

Every postmortem action should have:

```text
Action
Owner
Priority
Due Date
Status
```

Example:

| Action                       | Owner        | Priority | Status      |
| ---------------------------- | ------------ | -------- | ----------- |
| Optimize checkout query      | Backend Team | High     | Open        |
| Add query performance test   | QA           | High     | Open        |
| Add DB saturation alert      | SRE          | High     | In Progress |
| Review capacity headroom     | Platform     | Medium   | Open        |
| Add deployment rollback gate | DevOps       | High     | Open        |

---

# 41. Monitoring Improvements

New alerts:

```text
Checkout SLO violation
Database CPU saturation
Connection-pool saturation
Query latency
Slow-query rate
Lock wait
Deployment-induced regression
```

---

# 42. Deployment Improvements

The deployment pipeline is enhanced.

```text
Code
 ↓
Unit Tests
 ↓
Integration Tests
 ↓
Performance Tests
 ↓
Database Query Validation
 ↓
Canary Deployment
 ↓
Health Validation
 ↓
Full Deployment
```

---

# 43. Canary Deployment

Instead of deploying to 100% of instances immediately:

```text
Production
    │
    ├── 95% → v4.7
    │
    └── 5%  → v4.8
```

The team monitors:

```text
Error Rate
Latency
Database CPU
Query Performance
Business Success Rate
```

If the new version performs badly:

```text
v4.8
 ↓
Canary Failure
 ↓
Automatic Rollback
```

This reduces blast radius.

---

# 44. Automated Rollback

A deployment can be automatically rolled back when critical conditions are exceeded.

For example:

```text
Checkout Success < 99%
OR
P95 Latency > 1 sec
OR
DB CPU > 90%
```

The exact thresholds should be based on service behavior and SLOs.

---

# 45. Blameless Culture

A good postmortem avoids statements such as:

```text
"Developer X caused the outage."
```

Instead:

```text
"The deployment process allowed an inefficient query
to reach production without sufficient performance validation."
```

The focus is on improving systems and processes.

---

# 46. Incident Metrics

The organization tracks:

### MTTD

Mean Time to Detect.

```text
How long did it take to detect the incident?
```

### MTTA

Mean Time to Acknowledge.

```text
How long until someone responded?
```

### MTTR

Mean Time to Restore/Resolve.

```text
How long until service recovered?
```

### Change Failure Rate

```text
How often do deployments cause failures?
```

These metrics help evaluate operational maturity.

---

# 47. Incident Lifecycle

The complete lifecycle is:

```text
Detection
   ↓
Triage
   ↓
Declaration
   ↓
Severity Classification
   ↓
Incident Command
   ↓
Investigation
   ↓
Mitigation
   ↓
Recovery
   ↓
Validation
   ↓
Resolution
   ↓
Postmortem
   ↓
Corrective Actions
   ↓
Preventive Actions
   ↓
Verification
```

---

# 48. SRE Learning Loop

A mature organization continuously improves.

```text
Incident
   ↓
Postmortem
   ↓
Learning
   ↓
Action Items
   ↓
Engineering Changes
   ↓
Better Monitoring
   ↓
Better Deployment
   ↓
Reduced Risk
   ↓
Future Incidents Less Likely
```

---

# 49. DQL and Incident Investigation

DQL can support investigation by correlating:

```text
Requests
Errors
Latency
Logs
Services
Hosts
Kubernetes workloads
Deployment events
```

A conceptual example:

```text
fetch logs
| filter contains(content, "timeout")
| summarize count(), by:service.name
| sort count desc
```

The exact DQL syntax and available fields depend on the telemetry schema in the Dynatrace environment.

---

# 50. Incident Dashboard

A dedicated incident dashboard could contain:

```text
┌─────────────────────────────────────────┐
│ INCIDENT: CHECKOUT DEGRADATION          │
├─────────────────────────────────────────┤
│ Severity                 SEV-1           │
│ Status                   RESOLVED        │
│ Duration                 58 min          │
│ Checkout Success         99.8%           │
│ Error Rate               0.2%            │
│ P95 Latency              300 ms          │
│ DB CPU                   55%             │
│ DB Connections           55%             │
│ Deployment               Rolled Back     │
└─────────────────────────────────────────┘
```

---

# 51. Postmortem Structure

A standard postmortem can contain:

```text
1. Incident Summary
2. Impact
3. Timeline
4. Detection
5. Response
6. Root Cause
7. Contributing Factors
8. What Went Well
9. What Went Wrong
10. Mitigation
11. Corrective Actions
12. Preventive Actions
13. Lessons Learned
14. Action Items
```

---

# 52. Final Incident Flow

```text
                         INCIDENT
                            │
                            ▼
                       DETECTION
                            │
                            ▼
                         TRIAGE
                            │
                            ▼
                    SEVERITY: SEV-1
                            │
                            ▼
                    INCIDENT COMMAND
                            │
                            ▼
                      INVESTIGATION
                            │
                            ▼
                  DATABASE SATURATION
                            │
                            ▼
                  BAD QUERY IDENTIFIED
                            │
                            ▼
                       MITIGATION
                            │
                            ▼
                        ROLLBACK
                            │
                            ▼
                        RECOVERY
                            │
                            ▼
                      VALIDATION
                            │
                            ▼
                       RESOLUTION
                            │
                            ▼
                       POSTMORTEM
                            │
                            ▼
                    ROOT CAUSE ANALYSIS
                            │
                            ▼
                    CORRECTIVE ACTIONS
                            │
                            ▼
                     PREVENTIVE ACTIONS
                            │
                            ▼
                    SYSTEM IMPROVEMENT
```

---

# 53. Key SRE Principles Demonstrated

This case study demonstrates:

```text
Incident Management
Incident Command
Severity Classification
Triage
Observability
Monitoring
Golden Signals
SLOs
Error Budgets
Distributed Tracing
DQL
Database Monitoring
Capacity Planning
Canary Deployment
Rollback
Root Cause Analysis
Five Whys
Blameless Postmortems
MTTD
MTTA
MTTR
Change Failure Rate
Continuous Improvement
```

---

# Final Takeaway

The most important lesson is:

> **Incident response is not complete when the service comes back online. It is complete when the organization has learned from the incident and reduced the probability or impact of recurrence.**

A strong SRE organization follows this cycle:

```text
Detect
  ↓
Respond
  ↓
Mitigate
  ↓
Recover
  ↓
Understand
  ↓
Learn
  ↓
Improve
  ↓
Prevent
```

The ultimate objective is not simply:

> **"Fix the outage."**

It is:

> **"Build a system that is easier to detect, diagnose, recover, and improve when something goes wrong."**
