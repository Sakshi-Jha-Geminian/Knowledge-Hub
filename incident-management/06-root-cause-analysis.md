# Root Cause Analysis (RCA)

## Introduction

Restoring a service after an incident is only half the job.

If organizations focus only on restoring services and never understand why failures occurred, the same incidents will continue to happen repeatedly.

This is where Root Cause Analysis (RCA) becomes essential.

Root Cause Analysis is a systematic process used to identify the underlying cause of an incident rather than merely addressing its symptoms.

The goal is not simply to answer:

```text
What failed?
```

The goal is to answer:

```text
Why did it fail?
Why was it allowed to fail?
How can we prevent it from happening again?
```

RCA is a fundamental practice in:

* Site Reliability Engineering (SRE)
* Incident Management
* Problem Management
* DevOps
* Observability
* IT Operations

Organizations with mature RCA practices experience:

* Fewer recurring incidents
* Lower operational costs
* Improved reliability
* Better customer experience
* Faster future troubleshooting

---

# Learning Objectives

After completing this document, you should understand:

* What Root Cause Analysis is
* Why RCA is important
* Root Cause vs Symptoms
* RCA Process
* RCA Techniques
* 5 Whys Analysis
* Fishbone Diagrams
* Fault Tree Analysis
* Timeline Analysis
* Evidence Collection
* Observability-Based RCA
* Dynatrace RCA
* Davis AI Root Cause Detection
* Financial Trading System RCA Examples
* RCA Best Practices

---

# What is Root Cause Analysis?

## Definition

Root Cause Analysis (RCA) is the structured process of identifying the fundamental reason an incident occurred.

The objective is:

```text
Prevent Recurrence
```

instead of:

```text
Treat Symptoms
```

---

# Root Cause vs Symptom

Understanding this distinction is critical.

---

## Symptom

A symptom is what users observe.

Example:

```text
Website Slow
```

---

## Root Cause

The underlying reason.

Example:

```text
Database Connection Pool Exhaustion
```

---

## Example

Symptom:

```text
Order Processing Delayed
```

Immediate Cause:

```text
Application Response Time Increased
```

Root Cause:

```text
Database Query Missing Index
```

Fixing the symptom may restore service temporarily.

Fixing the root cause prevents recurrence.

---

# Why RCA Matters

Without RCA:

```text
Incident
    │
    ▼
Temporary Fix
    │
    ▼
Incident Returns
```

With RCA:

```text
Incident
    │
    ▼
Root Cause Found
    │
    ▼
Permanent Fix
    │
    ▼
Prevention
```

---

# Goals of RCA

RCA aims to:

```text
Identify Failure Causes
Prevent Recurrence
Improve Reliability
Reduce MTTR
Improve Processes
```

---

# When Should RCA Be Performed?

Typically after:

```text
SEV-1 Incidents
SEV-2 Incidents
Major Outages
Security Incidents
Recurring Problems
```

Some organizations perform RCA for every production incident.

---

# RCA Process Overview

A standard RCA workflow:

```text
Incident Occurs
      │
      ▼
Collect Evidence
      │
      ▼
Build Timeline
      │
      ▼
Identify Contributing Factors
      │
      ▼
Determine Root Cause
      │
      ▼
Define Corrective Actions
      │
      ▼
Implement Improvements
```

---

# Step 1: Collect Evidence

The first step is gathering facts.

Sources include:

```text
Metrics
Logs
Traces
Events
Monitoring Alerts
Support Tickets
Screenshots
```

Evidence should be collected before systems change.

---

# Observability and RCA

Modern RCA heavily relies on observability.

Sources:

```text
Metrics
Logs
Traces
Topology
Dependencies
```

These provide visibility into:

```text
What Happened
Where It Happened
Why It Happened
```

---

# Metrics During RCA

Metrics help identify:

```text
Performance Issues
Resource Exhaustion
Capacity Problems
Traffic Spikes
```

Examples:

```text
CPU Usage
Memory Usage
Response Time
Error Rate
```

---

# Logs During RCA

Logs provide detailed event information.

Examples:

```text
Application Errors
Authentication Failures
Exceptions
Database Errors
```

Logs often reveal the exact failure event.

---

# Traces During RCA

Distributed tracing shows request flow.

Example:

```text
User Request
      │
      ▼
API Gateway
      │
      ▼
Application Service
      │
      ▼
Database
```

Tracing identifies where delays occur.

---

# Timeline Analysis

A timeline reconstructs incident events.

Example:

```text
10:00 Deployment Started
10:05 Deployment Completed
10:10 Error Rate Increased
10:15 Customer Complaints
10:20 Incident Declared
10:45 Rollback Executed
10:50 Service Restored
```

Timelines reveal causal relationships.

---

# Contributing Factors

Most incidents have multiple contributing factors.

Examples:

```text
Software Bug
Configuration Error
Capacity Limitation
Human Error
Monitoring Gap
```

RCA should identify all contributing factors.

---

# What Is a Root Cause?

A root cause is:

```text
The Fundamental Reason
The Incident Occurred
```

Characteristics:

```text
Actionable
Evidence-Based
Preventable
```

---

# The 5 Whys Technique

## What is 5 Whys?

A simple RCA technique developed by Toyota.

Method:

Ask "Why?" repeatedly until the fundamental cause is identified.

---

# Example: 5 Whys

Problem:

```text
Application Unavailable
```

Why?

```text
Database Connection Failed
```

Why?

```text
Connection Pool Exhausted
```

Why?

```text
Too Many Open Connections
```

Why?

```text
Connections Not Released
```

Why?

```text
Application Code Bug
```

Root Cause:

```text
Connection Handling Defect
```

---

# Fishbone Diagram (Ishikawa)

Fishbone diagrams categorize possible causes.

Categories:

```text
People
Process
Technology
Infrastructure
Environment
```

Example:

```text
                 Incident
                     │
      ───────────────┼──────────────
         People      │      Process
                     │
      Infrastructure │ Technology
```

Useful for brainstorming.

---

# Fault Tree Analysis (FTA)

FTA starts with a failure and works backward.

Example:

```text
Trading Failure
      │
      ▼
Database Failure
      │
      ▼
Storage Failure
```

This method helps identify chains of failure.

---

# Change Analysis

Many incidents occur after changes.

Questions:

```text
What Changed?
When Did It Change?
Who Made The Change?
```

Examples:

```text
Deployment
Configuration Update
Infrastructure Change
Database Migration
```

---

# Deployment-Based RCA Example

Timeline:

```text
11:00 Deployment
11:05 Error Rate Increase
11:07 User Complaints
```

Strong correlation suggests deployment-related failure.

---

# Human Factors Analysis

Many incidents involve human actions.

Examples:

```text
Incorrect Configuration
Manual Deletion
Missed Procedure
Documentation Gap
```

Focus on process improvements rather than blame.

---

# Blameless RCA

Modern SRE organizations use blameless RCA.

Focus:

```text
What Failed?
How Do We Improve?
```

Avoid:

```text
Who Made The Mistake?
```

Blameless culture encourages transparency.

---

# Dynatrace Root Cause Analysis

Dynatrace automatically performs root-cause analysis using:

```text
Metrics
Logs
Traces
Topology
Dependencies
```

Benefits:

```text
Faster Investigation
Less Manual Analysis
Reduced MTTR
```

---

# Davis AI Root Cause Detection

Davis AI continuously evaluates:

```text
Infrastructure
Applications
Services
Cloud Resources
Kubernetes
```

Instead of displaying dozens of alerts:

```text
CPU Alert
Memory Alert
Database Alert
Application Alert
```

Davis correlates them into:

```text
Single Root Cause Problem
```

---

# Dynatrace RCA Example

Problem:

```text
User Transactions Slow
```

Davis Analysis:

```text
Service Latency
      │
      ▼
Database Query Delay
      │
      ▼
Missing Database Index
```

Root Cause Identified Automatically.

---

# Kubernetes RCA Example

Problem:

```text
Application Unavailable
```

Investigation:

```text
Pod Restarts
      │
      ▼
Memory Exhaustion
      │
      ▼
Incorrect Resource Limits
```

Root Cause:

```text
Improper Kubernetes Configuration
```

---

# Cloud Infrastructure RCA Example

Problem:

```text
Application Outage
```

Investigation:

```text
Storage Latency
      │
      ▼
Cloud Provider Issue
```

Root Cause:

```text
Regional Storage Service Failure
```

---

# Financial Trading Platform RCA Example

## Scenario

Users report delayed trade execution.

---

### Symptoms

```text
Order Placement Delays
```

---

### Investigation

Metrics:

```text
Database Latency Increased
```

Logs:

```text
Query Timeout Errors
```

Traces:

```text
Slow Database Transactions
```

---

### Timeline

```text
09:00 Traffic Surge
09:05 Database Saturation
09:08 Trade Delays
09:15 Incident Declared
```

---

### Root Cause

```text
Capacity Planning Failure
```

Database capacity could not handle peak load.

---

### Corrective Actions

```text
Add Capacity Forecasting
Enable Auto Scaling
Improve Monitoring
```

---

# Corrective Actions vs Preventive Actions

## Corrective Actions

Fix existing weaknesses.

Examples:

```text
Patch Software
Add Index
Fix Configuration
```

---

## Preventive Actions

Reduce future risk.

Examples:

```text
Monitoring Improvements
Automation
Capacity Planning
Training
```

---

# RCA Report Structure

A standard RCA document contains:

```text
Incident Summary
Timeline
Impact
Evidence
Root Cause
Contributing Factors
Corrective Actions
Preventive Actions
```

---

# Common RCA Mistakes

### Stopping Too Early

Finding symptoms instead of causes.

---

### Lack of Evidence

Making assumptions.

---

### Focusing on Individuals

Creating blame culture.

---

### Ignoring Process Failures

Technical causes are often only part of the story.

---

### No Follow-Up

Corrective actions never implemented.

---

# RCA Metrics

Organizations often track:

```text
Recurring Incidents
Corrective Action Completion
MTTR
Incident Frequency
```

These metrics help measure effectiveness.

---

# Best Practices

### Use Evidence

Base conclusions on data.

### Build Timelines

Understand event relationships.

### Use Multiple RCA Techniques

Validate findings.

### Remain Blameless

Encourage transparency.

### Focus on Prevention

Not just resolution.

### Track Action Items

Ensure improvements are implemented.

---

# Interview Questions

### What is Root Cause Analysis?

A structured process used to identify the underlying cause of an incident.

### What is the Difference Between a Symptom and a Root Cause?

A symptom is the observed problem; the root cause is the underlying reason it occurred.

### What is the 5 Whys Technique?

An RCA method that repeatedly asks "Why?" until the fundamental cause is identified.

### Why Are Distributed Traces Useful During RCA?

They reveal where requests fail or slow down across multiple services.

### How Does Dynatrace Help with RCA?

Through topology awareness, dependency mapping, distributed tracing, and Davis AI root-cause analysis.

### Why Are Blameless RCAs Important?

They encourage learning and process improvement rather than assigning blame.

---

# Key Takeaways

* RCA focuses on preventing incidents from recurring.
* Symptoms and root causes are not the same.
* Metrics, logs, traces, and timelines are critical during investigations.
* Techniques such as 5 Whys, Fishbone Diagrams, and Fault Tree Analysis help identify causes.
* Dynatrace and Davis AI accelerate root-cause identification.
* Blameless culture improves learning and reliability.
* Corrective and preventive actions are equally important.
* Effective RCA improves system stability and operational maturity.

---

# References

## Google SRE Book

https://sre.google/sre-book/

## Google SRE Workbook

https://sre.google/workbook/

## Dynatrace Davis AI Documentation

https://docs.dynatrace.com/docs/discover-dynatrace/platform/davis-ai

## ITIL Problem Management

https://www.axelos.com

## OpenTelemetry Documentation

https://opentelemetry.io/docs/
