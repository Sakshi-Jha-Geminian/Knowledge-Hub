# Availability and Reliability

## Introduction

One of the primary goals of Site Reliability Engineering (SRE) is ensuring that systems remain available and reliable for users.

Organizations invest heavily in:

* Infrastructure
* Monitoring
* Observability
* Automation
* Incident Management
* Capacity Planning

to achieve these goals.

Although the terms *availability* and *reliability* are often used interchangeably, they represent different concepts.

Understanding the difference is essential for:

* SRE Teams
* DevOps Engineers
* Platform Engineers
* Cloud Engineers
* Operations Teams

and anyone responsible for operating production systems.

---

# Learning Objectives

After completing this document, you should understand:

* What availability means
* What reliability means
* Differences between availability and reliability
* How availability is measured
* How reliability is measured
* Availability and reliability in SRE
* Real-world examples
* Availability and reliability in financial systems
* Dynatrace monitoring capabilities
* Best practices

---

# Why Availability and Reliability Matter

Users expect services to:

* Work correctly
* Be accessible
* Perform consistently
* Recover quickly from failures

Examples:

* Online Banking
* Trading Platforms
* E-Commerce Sites
* Healthcare Systems
* Cloud Platforms

Failures directly impact:

* Revenue
* Customer Trust
* Business Operations
* Regulatory Compliance

---

# What is Availability?

## Definition

Availability measures whether a service is accessible when users need it.

It answers:

> Can users access the service?

If users can access and use the service, the system is considered available.

---

# Simple Example

Suppose an application is running and accepting requests.

```text id="ab5c0n"
User
 │
 ▼
Application
 │
 ▼
Response
```

The application is available.

---

# Availability Formula

```text id="mwg85m"
Availability

=

Uptime
--------
Total Time
× 100
```

---

# Example Calculation

Monthly Time:

```text id="vxtf1m"
30 Days
```

Total Minutes:

```text id="u9e5lq"
43,200 Minutes
```

Downtime:

```text id="hj4psa"
43 Minutes
```

Availability:

```text id="mnm2cf"
99.9%
```

---

# Availability Targets

Common availability goals:

| Availability | Downtime Per Month |
| ------------ | ------------------ |
| 99%          | ~7 Hours           |
| 99.9%        | ~43 Minutes        |
| 99.99%       | ~4 Minutes         |
| 99.999%      | ~26 Seconds        |

These are often referred to as "nines."

---

# Understanding the Nines

## Two Nines

```text id="f2v6zn"
99%
```

Suitable for less critical systems.

---

## Three Nines

```text id="m1s3yu"
99.9%
```

Common enterprise target.

---

## Four Nines

```text id="jjv9bn"
99.99%
```

Used for highly critical applications.

---

## Five Nines

```text id="m7x4wt"
99.999%
```

Typically required for mission-critical systems.

Examples:

* Financial Trading Platforms
* Telecommunications
* Core Banking Systems

---

# What is Reliability?

## Definition

Reliability measures how consistently a system performs its intended function without failure.

It answers:

> Does the service work correctly over time?

Reliability focuses on successful operation rather than simple accessibility.

---

# Reliability Example

A website may be accessible.

```text id="mq1fht"
Login Page Loads
```

However:

```text id="g6sm0r"
Transactions Fail
```

The service is:

```text id="te3f5q"
Available
```

but not:

```text id="txd5xn"
Reliable
```

---

# Reliability Characteristics

Reliable systems:

* Perform consistently
* Produce correct results
* Recover gracefully
* Handle failures effectively

---

# Reliability Components

Reliability depends on:

```text id="v57clx"
Availability

Performance

Correctness

Recoverability
```

---

# Availability vs Reliability

## Key Difference

Availability asks:

```text id="m4r3jc"
Can users access it?
```

Reliability asks:

```text id="qzv6ua"
Does it work correctly?
```

---

# Comparison

| Availability                  | Reliability                     |
| ----------------------------- | ------------------------------- |
| Accessibility                 | Consistent Correct Operation    |
| Focuses on Uptime             | Focuses on Success              |
| Measured by Uptime            | Measured by Successful Outcomes |
| Can Exist Without Reliability | Usually Requires Availability   |

---

# Example 1

Website Status:

```text id="lxv1ys"
Online
```

Checkout Process:

```text id="s2kjxj"
Fails Frequently
```

Result:

```text id="k6qlf6"
Available

Not Reliable
```

---

# Example 2

System Offline:

```text id="q6u9ji"
10 Minutes
```

Otherwise:

```text id="x0yxr5"
Perfect Operation
```

Result:

```text id="sj2xke"
Highly Reliable

But Availability Reduced
```

---

# Availability in SRE

Availability is commonly measured using:

* SLI
* SLO
* SLA

Example:

```text id="p4r1lq"
Availability SLO

99.95%
```

Availability directly affects customer satisfaction.

---

# Reliability in SRE

Reliability is evaluated through:

* Error Rates
* Success Rates
* Latency
* Incident Frequency
* Recovery Performance

SRE teams focus heavily on reliability improvements.

---

# Reliability Metrics

Examples include:

## Request Success Rate

```text id="d3gh0k"
Successful Requests
-------------------
Total Requests
```

---

## Error Rate

```text id="g2vq4r"
Failed Requests
---------------
Total Requests
```

---

## Mean Time Between Failures (MTBF)

Measures average time between incidents.

---

## Mean Time To Recovery (MTTR)

Measures recovery speed.

---

# Availability and Reliability in Banking

Online Banking Requirements:

Availability:

```text id="e9dzrq"
99.99%
```

Reliability Requirements:

```text id="c5kkft"
Transactions Process Correctly
```

Users need both.

A banking application that is available but processes transactions incorrectly is unacceptable.

---

# Availability and Reliability in Trading Systems

Trading systems have strict requirements.

Availability:

```text id="khq8kl"
24x7 Market Access
```

Reliability:

```text id="rm9cl3"
Correct Order Execution
```

Even a small reliability issue can result in significant financial losses.

---

# Relationship to Golden Signals

Golden Signals help measure availability and reliability.

Examples:

## Latency

Measures responsiveness.

---

## Traffic

Measures demand.

---

## Errors

Measures reliability problems.

---

## Saturation

Measures capacity risks.

---

# Reliability Engineering

Reliability Engineering focuses on:

* Preventing failures
* Reducing incidents
* Improving recovery
* Enhancing user experience

This is the core mission of SRE.

---

# Availability and Error Budgets

Error Budgets are based on availability objectives.

Example:

```text id="yzok67"
Availability SLO

99.9%
```

Error Budget:

```text id="u5ikcc"
0.1%
```

Availability targets directly influence operational decisions.

---

# Dynatrace and Availability Monitoring

Dynatrace provides:

* Uptime Monitoring
* Availability Monitoring
* Synthetic Monitoring
* Real User Monitoring
* Alerting

These capabilities help teams maintain availability targets.

---

# Dynatrace and Reliability Monitoring

Dynatrace monitors:

* Failure Rates
* Exceptions
* Service Errors
* Response Times
* Dependencies

This supports reliability engineering efforts.

---

# Availability and Predictive Monitoring

Predictive monitoring helps identify future availability risks.

Example:

```text id="r0x5lq"
CPU Growth

Memory Growth

Traffic Growth
```

Davis AI can predict:

```text id="v0hpx0"
Potential Service Outage
```

before users are impacted.

---

# Best Practices

1. Measure both availability and reliability.
2. Focus on user experience.
3. Track meaningful SLIs.
4. Monitor Golden Signals.
5. Implement predictive monitoring.
6. Improve recovery processes.
7. Continuously reduce failure rates.

---

# Common Mistakes

## Focusing Only on Uptime

Availability alone does not guarantee reliability.

---

## Ignoring User Outcomes

Users care about successful transactions.

---

## Measuring Infrastructure Instead of Services

Business outcomes are more important.

---

## Reactive Monitoring Only

Predictive monitoring improves reliability.

---

# Interview Questions

### What is Availability?

The percentage of time a service is accessible and operational.

---

### What is Reliability?

The ability of a system to perform correctly and consistently over time.

---

### Can a System Be Available but Not Reliable?

Yes.

A service may be accessible while failing to perform correctly.

---

### Can a System Be Reliable but Temporarily Unavailable?

Yes.

A service may operate perfectly when available but experience occasional downtime.

---

### How Is Availability Calculated?

```text id="z4nd67"
Uptime
-------
Total Time
× 100
```

---

### Why Are Availability and Reliability Important?

They directly impact customer experience and business outcomes.

---

### How Does Dynatrace Help?

Dynatrace provides monitoring, observability, AI analysis, and predictive capabilities that help teams improve both availability and reliability.

---

# Key Takeaways

* Availability and reliability are related but different concepts.
* Availability measures accessibility.
* Reliability measures consistent correct operation.
* A service can be available but unreliable.
* SRE focuses on improving both.
* Golden Signals help measure service health.
* Error Budgets are derived from availability objectives.
* Dynatrace provides powerful capabilities for monitoring and improving availability and reliability.

---

# References

## Official Sources

Google SRE Book

https://sre.google/sre-book/

Google SRE Workbook

https://sre.google/workbook/

Google Cloud SRE Documentation

https://cloud.google.com/architecture/devops/devops-sre

Dynatrace Documentation

https://docs.dynatrace.com/

Dynatrace Availability Monitoring

https://docs.dynatrace.com/docs/observe/digital-experience/synthetic-monitoring

## Further Reading

OpenTelemetry Documentation

https://opentelemetry.io/docs/

Cloud Native Computing Foundation

https://www.cncf.io/
