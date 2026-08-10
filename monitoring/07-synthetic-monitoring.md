# Synthetic Monitoring

## Introduction

Organizations cannot wait for users to report problems.

By the time customers begin experiencing issues, the business may already be losing revenue, reputation, and customer trust.

Traditional monitoring tells us:

```text
Something is wrong.
```

Real User Monitoring (RUM) tells us:

```text
Real users are affected.
```

Synthetic Monitoring helps answer:

```text
Can we detect issues before users are impacted?
```

Synthetic Monitoring proactively simulates user interactions with applications, APIs, and services to verify availability, performance, and functionality.

Instead of waiting for real users, automated tests continuously perform actions such as:

* Opening a Website
* Logging In
* Searching Products
* Completing Transactions
* Calling APIs

This allows organizations to identify problems before customers encounter them.

---

# Learning Objectives

After completing this document, you should understand:

* What Synthetic Monitoring is
* Why Synthetic Monitoring is important
* How Synthetic Monitoring works
* Synthetic Monitoring architecture
* Availability Monitoring
* HTTP Monitoring
* Browser Monitoring
* API Monitoring
* Transaction Monitoring
* Scripted Monitoring
* Synthetic Locations
* Public vs Private Monitoring
* Dynatrace Synthetic Monitoring
* RUM vs Synthetic Monitoring
* Best Practices

---

# What is Synthetic Monitoring?

## Definition

Synthetic Monitoring is the process of simulating user interactions using automated scripts and test transactions to evaluate system availability, functionality, and performance.

Unlike Real User Monitoring, synthetic monitoring does not require actual users.

Example:

```text
Synthetic User
      │
      ▼
Login Page
      │
      ▼
Product Search
      │
      ▼
Checkout Process
```

The system continuously validates whether these actions work correctly.

---

# Why Synthetic Monitoring Matters

Imagine an online banking application.

At 2:00 AM:

```text
No Active Users
```

A database issue occurs.

Without synthetic monitoring:

```text
Problem Discovered
8:00 AM
When Users Log In
```

With synthetic monitoring:

```text
Problem Detected
2:01 AM
```

Operations teams can resolve the issue before customers arrive.

---

# How Synthetic Monitoring Works

A synthetic monitor executes predefined tests at regular intervals.

Example:

```text
Synthetic Test
      │
      ▼
Open Website
      │
      ▼
Measure Response Time
      │
      ▼
Validate Result
      │
      ▼
Generate Alert
```

This process runs continuously.

---

# Synthetic Monitoring Architecture

Typical architecture:

```text
Synthetic Engine
        │
        ▼
Browser Monitor
HTTP Monitor
API Monitor
        │
        ▼
Application
        │
        ▼
Monitoring Platform
        │
        ▼
Dashboards
Alerts
Reports
```

---

# Types of Synthetic Monitoring

Several types of synthetic monitoring exist.

---

# Availability Monitoring

## What is Availability Monitoring?

Availability Monitoring verifies whether an application or service can be reached.

Example:

```text
Website Available?
YES / NO
```

Checks may include:

* DNS Resolution
* TCP Connectivity
* HTTP Response
* SSL Validation

---

## Availability Metrics

Examples:

```text
Availability %
Downtime Duration
Uptime Duration
```

Example:

```text
Availability = 99.99%
```

---

# HTTP Monitoring

HTTP monitors verify website and API availability.

Example:

```text
HTTP GET
https://example.com
```

Validation includes:

```text
Response Time
HTTP Status Code
Content Validation
```

Expected Result:

```text
HTTP 200
```

---

# API Monitoring

Modern applications rely heavily on APIs.

Examples:

```text
REST APIs
GraphQL APIs
Microservices APIs
```

Synthetic API monitoring validates:

```text
Availability
Response Time
Correct Responses
Authentication
```

Example:

```text
GET /customers
POST /orders
```

---

# Browser Monitoring

Browser monitors simulate actual user interactions.

Example:

```text
Open Website
      │
      ▼
Login
      │
      ▼
Search Product
      │
      ▼
Checkout
```

These tests execute within real browsers.

Examples:

```text
Chrome
Edge
Firefox
```

---

# Transaction Monitoring

Transaction monitoring validates business-critical workflows.

Examples:

```text
User Login
Fund Transfer
Product Purchase
Ticket Booking
```

Purpose:

```text
Verify End-to-End Functionality
```

---

# Multi-Step Monitoring

Some workflows involve multiple actions.

Example:

```text
Login
  │
  ▼
Search Product
  │
  ▼
Add to Cart
  │
  ▼
Checkout
```

Each step can be validated independently.

---

# Scripted Monitoring

Complex applications often require custom scripts.

Examples:

```text
Custom Authentication
Dynamic Forms
Conditional Logic
```

Scripted monitors provide flexibility for advanced scenarios.

---

# Synthetic Monitoring Metrics

Common metrics include:

```text
Availability
Response Time
Error Rate
Success Rate
DNS Resolution Time
SSL Handshake Time
```

---

# Response Time Monitoring

Measures:

```text
Request Sent
      │
      ▼
Response Received
```

Example:

```text
Response Time = 350 ms
```

---

# Error Monitoring

Synthetic tests detect:

```text
HTTP Errors
Application Errors
Timeouts
Connection Failures
```

Example:

```text
HTTP 500
```

---

# Geographic Monitoring

Users access applications from different regions.

Synthetic tests can execute from multiple locations.

Example:

```text
United States
Europe
India
Singapore
Australia
```

Benefits:

```text
Regional Visibility
```

---

# Synthetic Locations

A synthetic location is the source from which a test runs.

---

## Public Locations

Managed by the monitoring provider.

Example:

```text
Global Testing Locations
```

Benefits:

* Easy Deployment
* Global Coverage

---

## Private Locations

Installed inside an organization's environment.

Example:

```text
Corporate Network
Private Data Center
Internal Applications
```

Benefits:

* Internal Visibility
* Security Compliance

---

# DNS Monitoring

Synthetic tests validate:

```text
DNS Availability
DNS Resolution Time
DNS Errors
```

Example:

```text
example.com
      │
      ▼
IP Address
```

---

# SSL Monitoring

Synthetic monitoring can verify:

```text
SSL Certificate Validity
Certificate Expiration
TLS Connectivity
```

Benefits:

```text
Prevent Certificate Expiration Outages
```

---

# API Performance Monitoring

API monitoring often evaluates:

```text
Latency
Availability
Payload Validation
Authentication Success
```

This is especially important in microservices architectures.

---

# Cloud Application Monitoring

Synthetic monitoring supports cloud applications hosted on:

```text
AWS
Azure
Google Cloud
```

Benefits:

```text
Validate Global Accessibility
```

---

# Synthetic Monitoring and Observability

Synthetic monitoring complements:

```text
Metrics
Logs
Traces
RUM
```

Example:

```text
Synthetic Test Fails
         │
         ▼
Trace Identifies Slow Service
         │
         ▼
Logs Reveal Database Error
```

Together they provide complete visibility.

---

# Synthetic Monitoring vs Real User Monitoring

## Synthetic Monitoring

Uses:

```text
Simulated Users
```

Benefits:

```text
Proactive Detection
```

Can run:

```text
24x7
```

---

## Real User Monitoring

Uses:

```text
Actual Users
```

Benefits:

```text
Actual User Experience
```

Requires:

```text
Real Traffic
```

---

# Combining RUM and Synthetic Monitoring

Best practice:

```text
Synthetic Monitoring
+
Real User Monitoring
=
Complete Digital Experience Monitoring
```

Synthetic monitoring identifies issues early.

RUM confirms actual user impact.

---

# Dynatrace Synthetic Monitoring

Dynatrace provides enterprise-grade synthetic monitoring.

Capabilities include:

```text
HTTP Monitors
Browser Monitors
API Monitoring
Private Locations
Public Locations
Global Testing
```

Benefits:

* Proactive Detection
* Global Visibility
* Business Transaction Monitoring

---

# Dynatrace Browser Clickpath Monitoring

A clickpath simulates a user journey.

Example:

```text
Homepage
   │
   ▼
Login
   │
   ▼
Search
   │
   ▼
Checkout
```

Dynatrace validates each step.

---

# Dynatrace Synthetic Locations

Dynatrace supports:

```text
Public Synthetic Locations
Private Synthetic Locations
```

This allows testing from both external and internal environments.

---

# Common Synthetic Monitoring Use Cases

---

## Website Availability

Verify website accessibility.

---

## API Health Checks

Validate API functionality.

---

## Login Monitoring

Ensure authentication systems function correctly.

---

## Payment Monitoring

Validate payment workflows.

---

## Mobile Backend Monitoring

Monitor mobile service APIs.

---

## SLA Verification

Validate contractual service levels.

---

# Real-World Example

An airline booking platform uses synthetic monitoring.

Synthetic test:

```text
Search Flight
      │
      ▼
Select Flight
      │
      ▼
Book Ticket
```

Failure detected:

```text
Payment API Timeout
```

Alert generated:

```text
Operations Team Notified
```

Problem resolved before users experience widespread failures.

---

# Benefits of Synthetic Monitoring

## Proactive Issue Detection

Identify issues before customers do.

---

## 24x7 Monitoring

Works continuously.

---

## SLA Validation

Verify service commitments.

---

## Geographic Visibility

Monitor from multiple locations.

---

## Faster Incident Response

Detect failures quickly.

---

## Business Transaction Validation

Ensure critical workflows function correctly.

---

# Best Practices

### Monitor Business-Critical Transactions

Focus on revenue-generating workflows.

---

### Use Multiple Synthetic Locations

Validate global user experiences.

---

### Combine Synthetic Monitoring with RUM

Gain complete visibility.

---

### Monitor APIs Continuously

APIs often drive modern applications.

---

### Validate Content, Not Just Availability

Ensure pages function correctly.

---

### Review and Update Scripts Regularly

Keep monitors aligned with application changes.

---

# Interview Questions

### What is Synthetic Monitoring?

Automated monitoring that simulates user interactions to verify availability, performance, and functionality.

---

### How Does Synthetic Monitoring Differ from RUM?

Synthetic monitoring uses simulated users, while RUM uses actual users.

---

### What is a Browser Monitor?

A monitor that executes actions inside a real browser.

---

### What is an HTTP Monitor?

A monitor that validates HTTP requests and responses.

---

### What is a Synthetic Location?

A location from which synthetic tests execute.

---

### What is the Difference Between Public and Private Synthetic Locations?

Public locations are managed externally; private locations run within an organization's environment.

---

### How Does Dynatrace Support Synthetic Monitoring?

Through browser monitoring, HTTP monitoring, API monitoring, clickpath testing, and global synthetic locations.

---

# Key Takeaways

* Synthetic Monitoring proactively tests application functionality.
* It helps detect problems before users are affected.
* Common monitor types include HTTP, API, browser, and transaction monitoring.
* Public and private synthetic locations provide testing flexibility.
* Synthetic monitoring complements RUM, logs, metrics, and traces.
* Dynatrace provides advanced synthetic monitoring capabilities for modern applications.
* Synthetic Monitoring is a critical component of Digital Experience Monitoring (DEM).

---

# References

## Dynatrace Synthetic Monitoring Documentation

https://docs.dynatrace.com/docs/observe/digital-experience/synthetic-monitoring

## Google SRE Book

https://sre.google/sre-book/

## OpenTelemetry Documentation

https://opentelemetry.io/docs/

## W3C Web Performance Standards

https://www.w3.org/webperf/

## Microsoft Well-Architected Framework

https://learn.microsoft.com/azure/well-architected/
