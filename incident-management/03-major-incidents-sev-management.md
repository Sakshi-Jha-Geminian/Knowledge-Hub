# Major Incidents and Severity Management

## Introduction

Not all incidents are equal.

Some incidents affect a single user or a non-critical feature, while others can impact millions of users, cause significant revenue loss, damage reputation, and attract executive attention.

For this reason, organizations use Severity Management and Major Incident Management (MIM) processes to ensure that critical incidents receive the appropriate level of attention, resources, and communication.

Major Incident Management is one of the most important operational practices in:

* Site Reliability Engineering (SRE)
* IT Operations
* DevOps
* Enterprise Support Organizations
* Financial Services
* Trading Platforms
* E-Commerce Systems

The goal is simple:

```text
Restore critical services as quickly as possible while minimizing business impact.
```

---

# Learning Objectives

After completing this document, you should understand:

* What a Major Incident is
* Severity Levels (SEV-1 to SEV-4)
* Impact vs Urgency
* Major Incident Management (MIM)
* Incident Command Structure
* Incident Commander Responsibilities
* War Rooms
* Bridge Calls
* Executive Communication
* Customer Communication
* ServiceNow Major Incidents
* Dynatrace Integration
* Financial Trading Incident Scenarios
* Best Practices

---

# What is a Major Incident?

## Definition

A Major Incident is an incident that causes significant business impact and requires immediate coordination across multiple teams.

Examples:

```text
Trading Platform Down
Online Banking Outage
Payment Gateway Failure
Cloud Region Failure
Authentication Service Outage
```

Characteristics:

```text
High Business Impact
High User Impact
Executive Visibility
Cross-Team Coordination
Urgent Resolution Required
```

---

# What is Severity Management?

Severity Management is the process of classifying incidents based on:

```text
Business Impact
User Impact
Urgency
Service Criticality
```

Severity determines:

* Response Time
* Escalation Level
* Resources Assigned
* Communication Frequency
* Management Involvement

---

# Impact vs Urgency

Severity is usually calculated using:

```text
Impact
+
Urgency
=
Severity
```

---

## Impact

Measures:

```text
Number of Users Affected
Business Impact
Revenue Impact
Service Availability
```

Examples:

```text
One User Impacted
Thousands of Users Impacted
Entire Customer Base Impacted
```

---

## Urgency

Measures:

```text
How Quickly Action Is Required
```

Examples:

```text
Immediate
Within Hours
Within Days
```

---

# Severity Levels

Most enterprises use four severity levels.

---

# SEV-1 (Critical)

## Definition

The highest severity level.

Represents:

```text
Complete Service Outage
Major Revenue Loss
Severe Customer Impact
```

Examples:

```text
Trading Platform Unavailable
Payment Processing Down
Authentication System Failure
```

Characteristics:

```text
Immediate Response
24x7 Escalation
Executive Involvement
War Room Activated
```

Target Response:

```text
Minutes
```

---

# SEV-2 (High)

## Definition

Major degradation but not a complete outage.

Examples:

```text
High Error Rate
Severe Performance Issues
Critical Feature Failure
```

Characteristics:

```text
Significant User Impact
Potential Revenue Impact
Rapid Response Required
```

Target Response:

```text
Within 30 Minutes
```

---

# SEV-3 (Medium)

## Definition

Moderate impact with available workarounds.

Examples:

```text
Reporting System Failure
Limited Feature Impact
Partial Service Degradation
```

Characteristics:

```text
Business Impact Limited
Workaround Available
```

---

# SEV-4 (Low)

## Definition

Minor issues with minimal impact.

Examples:

```text
Dashboard Display Bug
Non-Critical Error
Minor UI Issue
```

Characteristics:

```text
Minimal User Impact
Low Business Risk
```

---

# Severity Matrix

Example classification model:

| Impact | Urgency | Severity |
| ------ | ------- | -------- |
| High   | High    | SEV-1    |
| High   | Medium  | SEV-2    |
| Medium | Medium  | SEV-3    |
| Low    | Low     | SEV-4    |

This helps ensure consistency.

---

# Major Incident Management (MIM)

## Definition

Major Incident Management is a specialized process used to manage high-severity incidents.

Goals:

```text
Restore Service Quickly
Coordinate Teams
Communicate Effectively
Reduce Business Impact
```

MIM focuses on speed rather than perfect root-cause analysis.

---

# Major Incident Lifecycle

```text
Incident Detected
        │
        ▼
Severity Assigned
        │
        ▼
Major Incident Declared
        │
        ▼
War Room Activated
        │
        ▼
Investigation
        │
        ▼
Mitigation
        │
        ▼
Resolution
        │
        ▼
Recovery
        │
        ▼
Postmortem
```

---

# Declaring a Major Incident

A Major Incident is declared when:

```text
Critical Business Service Affected
Large User Impact
Revenue Impact
Regulatory Impact
```

Example:

```text
Stock Trading System Unavailable
```

This immediately triggers MIM procedures.

---

# Incident Command Structure

Large incidents require clear ownership.

Example:

```text
Incident Commander
       │
       ├── Technical Lead
       ├── Communications Lead
       ├── Operations Team
       └── SMEs
```

This prevents confusion and duplicated effort.

---

# Incident Commander

## Who is the Incident Commander?

The Incident Commander (IC) is the individual responsible for coordinating the entire incident response.

Important:

```text
The IC manages the response.
The IC does not necessarily fix the issue.
```

---

# Responsibilities of the Incident Commander

Examples:

```text
Coordinate Teams
Set Priorities
Manage Escalations
Track Progress
Provide Updates
Declare Resolution
```

The IC serves as the central decision-maker.

---

# Technical Lead

Responsibilities:

```text
Investigate Root Cause
Coordinate Engineers
Implement Mitigation
Implement Resolution
```

The Technical Lead focuses on technical recovery.

---

# Communications Lead

Responsibilities:

```text
Executive Updates
Customer Communication
Status Updates
Incident Reports
```

Good communication reduces panic and confusion.

---

# Subject Matter Experts (SMEs)

Examples:

```text
Database Experts
Cloud Engineers
Network Engineers
Security Teams
Application Teams
```

SMEs provide specialized knowledge.

---

# War Rooms

## What is a War Room?

A war room is a dedicated collaboration space used during major incidents.

Examples:

```text
Microsoft Teams Meeting
Slack Channel
Zoom Bridge
Conference Call
```

Purpose:

```text
Real-Time Collaboration
```

---

# War Room Rules

Best practices:

```text
One Incident Commander
One Active Discussion
Clear Action Items
Accurate Updates
```

Avoid:

```text
Chaos
Duplicate Work
Unclear Ownership
```

---

# Bridge Calls

Many organizations use bridge calls during SEV-1 incidents.

Participants:

```text
Operations Teams
Engineering Teams
Management
Support Teams
```

Bridge calls provide:

```text
Shared Situational Awareness
```

---

# Executive Communication

Executives often need:

```text
Business Impact
Estimated Resolution Time
Current Status
Risk Assessment
```

Executives generally do not need low-level technical details.

---

# Customer Communication

Customers require:

```text
Issue Description
Current Status
Expected Resolution
Next Update Time
```

Communication should be:

```text
Clear
Accurate
Transparent
```

---

# Communication Timeline Example

SEV-1 Example:

```text
00:00 Incident Detected
00:05 Major Incident Declared
00:10 War Room Opened
00:15 Executive Notification
00:30 Customer Update
01:00 Status Update
02:00 Resolution
03:00 Recovery Complete
```

---

# Escalation Management

Escalation occurs when:

```text
Resolution Delayed
Additional Expertise Needed
Business Impact Increases
```

Example:

```text
Support Team
      │
      ▼
Engineering Team
      │
      ▼
Cloud Vendor
      │
      ▼
Executive Leadership
```

---

# ServiceNow Major Incidents

Many enterprises use ServiceNow for MIM.

Workflow:

```text
Incident Created
       │
       ▼
SEV-1 Assigned
       │
       ▼
Major Incident Triggered
       │
       ▼
Assignment Groups Notified
       │
       ▼
Resolution
```

Benefits:

* Centralized Tracking
* Automated Notifications
* SLA Monitoring

---

# Dynatrace and Major Incidents

Dynatrace assists major incident response through:

```text
Problem Detection
Root Cause Analysis
Topology Mapping
Dependency Analysis
Davis AI
```

Benefits:

```text
Faster Detection
Reduced Alert Noise
Faster Investigation
```

---

# Dynatrace Problem Workflow

Example:

```text
Database Failure
       │
       ▼
Application Errors
       │
       ▼
Slow User Experience
```

Traditional Monitoring:

```text
Multiple Alerts
```

Dynatrace:

```text
Single Problem Record
```

This helps responders focus on the root cause.

---

# Financial Trading System Example

## Scenario

An online brokerage platform experiences:

```text
Order Placement Failures
```

Impact:

```text
Thousands of Traders Unable To Trade
```

Severity:

```text
SEV-1
```

---

# Response Process

```text
Dynatrace Alert
       │
       ▼
Major Incident Declared
       │
       ▼
War Room Opened
       │
       ▼
Investigation
       │
       ▼
Database Latency Found
       │
       ▼
Failover Executed
       │
       ▼
Service Restored
```

---

# Common Challenges During Major Incidents

Examples:

```text
Poor Communication
Unclear Ownership
Slow Escalation
Insufficient Documentation
Alert Storms
```

These challenges increase resolution times.

---

# Metrics for Major Incident Management

Organizations track:

```text
MTTD
MTTA
MTTR
Major Incident Count
Service Availability
```

These metrics help improve response processes.

---

# Best Practices

### Define Severity Levels Clearly

Avoid inconsistent classifications.

### Establish Incident Command Structure

Ensure clear ownership.

### Practice Through Simulations

Run incident exercises regularly.

### Automate Detection

Reduce reliance on user reports.

### Communicate Frequently

Keep stakeholders informed.

### Focus on Service Restoration First

Restore service before perfect diagnosis.

### Conduct Postmortems

Capture lessons learned.

---

# Interview Questions

### What is a Major Incident?

An incident with significant business impact requiring immediate coordinated response.

### What is the Difference Between SEV-1 and SEV-2?

SEV-1 usually represents a critical outage; SEV-2 represents severe degradation.

### What Does an Incident Commander Do?

Coordinates incident response, communication, and decision-making.

### Why Are War Rooms Used?

To centralize collaboration during major incidents.

### What Information Should Executives Receive?

Business impact, risk, status, and estimated resolution time.

### How Does Dynatrace Help During Major Incidents?

Through AI-driven problem detection, topology awareness, dependency mapping, and root-cause analysis.

### Why Is Severity Management Important?

It ensures incidents receive the appropriate level of response and attention.

---

# Key Takeaways

* Major incidents require specialized management processes.
* Severity classification drives response and escalation.
* Incident Command structures improve coordination.
* War rooms provide centralized collaboration.
* Communication is as important as technical troubleshooting.
* Dynatrace accelerates detection and investigation.
* ServiceNow helps manage major incident workflows.
* Well-practiced MIM processes significantly reduce business impact.

---

# References

## Google SRE Book

https://sre.google/sre-book/

## Google SRE Workbook

https://sre.google/workbook/

## ITIL Major Incident Management

https://www.axelos.com

## Dynatrace Davis AI Documentation

https://docs.dynatrace.com/docs/discover-dynatrace/platform/davis-ai

## ServiceNow Major Incident Management

https://www.servicenow.com/products/itsm.html
