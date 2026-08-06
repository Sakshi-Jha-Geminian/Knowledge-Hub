# SLI, SLO, and SLA

## Introduction

Modern software systems are expected to be highly reliable, fast, and available. Organizations invest heavily in infrastructure, monitoring, observability, automation, and Site Reliability Engineering (SRE) to ensure that users receive a consistent and dependable experience.

However, reliability cannot be improved unless it is measured.

Questions such as:

* How reliable is our service?
* How fast is our application?
* Are users experiencing errors?
* Are we meeting customer expectations?

require measurable answers.

This is where SLI, SLO, and SLA become essential.

These concepts provide a structured framework for defining, measuring, and managing service reliability.

They form the foundation of Site Reliability Engineering and are widely used by organizations such as Google, Amazon, Netflix, Microsoft, and financial institutions operating mission-critical systems.

---

# Learning Objectives

After completing this document, you should understand:

* Why reliability must be measured
* What an SLI is
* What an SLO is
* What an SLA is
* Differences between SLI, SLO, and SLA
* How they work together
* Real-world examples
* How Dynatrace supports SLO management
* Best practices and common mistakes

---

# Why Reliability Must Be Measured

Imagine a banking application.

Customers complain that:

* Pages load slowly
* Transactions occasionally fail
* Login requests timeout

The operations team says:

> "The application seems fine."

The development team says:

> "No major issues were reported."

Without measurable reliability indicators, decisions are based on opinions rather than facts.

SRE introduces objective measurements.

```text id="1t2d7a"
Observations
      │
      ▼
Measurements
      │
      ▼
Targets
      │
      ▼
Commitments
```

This process is implemented through:

* SLI
* SLO
* SLA

---

# Overview

The relationship can be visualized as:

```text id="m4z9kq"
User Experience
       │
       ▼
Measure Reliability
       │
       ▼
SLI
       │
       ▼
Target Reliability
       │
       ▼
SLO
       │
       ▼
Business Commitment
       │
       ▼
SLA
```

---

# What is an SLI?

## Definition

SLI stands for:

```text id="j9n6dw"
Service Level Indicator
```

An SLI is a quantitative measurement of service performance or reliability.

It answers:

> How is the service performing right now?

An SLI represents actual measured behavior.

---

# Examples of SLIs

Examples include:

* Availability
* Latency
* Throughput
* Error Rate
* Request Success Rate

---

# Availability SLI

Measures service uptime.

Formula:

```text id="x8p2mh"
Availability %

=
Successful Requests
-------------------
Total Requests
× 100
```

Example:

```text id="tr6a1s"
Successful Requests = 999,500

Total Requests = 1,000,000
```

Availability:

```text id="yb1vgs"
99.95%
```

---

# Latency SLI

Measures response time.

Example:

```text id="g7x4pf"
95% of requests
completed in
less than 200ms
```

Latency is critical for:

* Trading Systems
* Banking Applications
* E-Commerce Platforms
* APIs

---

# Throughput SLI

Measures workload handled by the system.

Example:

```text id="j6h4bn"
10,000 Requests Per Second
```

Used to evaluate scalability.

---

# Error Rate SLI

Measures failed requests.

Formula:

```text id="v3g9ld"
Failed Requests
----------------
Total Requests
```

Example:

```text id="3t5mdz"
Error Rate = 0.05%
```

---

# Quality SLI

Measures user-perceived quality.

Examples:

* Successful Transactions
* Completed Orders
* Successful Payments

Business-focused reliability metrics often fall into this category.

---

# Characteristics of a Good SLI

A good SLI should be:

* Measurable
* User-focused
* Meaningful
* Consistent
* Actionable

Bad Example:

```text id="8g7ytn"
CPU Usage
```

Good Example:

```text id="9v1zms"
Successful User Transactions
```

Users care about outcomes, not infrastructure metrics.

---

# What is an SLO?

## Definition

SLO stands for:

```text id="a5k2dr"
Service Level Objective
```

An SLO is a target value for an SLI.

It answers:

> What level of reliability are we aiming for?

---

# Example

Measured Availability:

```text id="4r8nvm"
99.95%
```

Target:

```text id="2h7xpd"
99.90%
```

The target is the SLO.

---

# SLO Formula

```text id="c6m9pk"
SLI >= SLO
```

Example:

```text id="g2s8lh"
Availability SLI = 99.95%

Target SLO = 99.90%
```

Result:

```text id="m8y3cd"
SLO Achieved
```

---

# Why SLOs Matter

SLOs create clear expectations.

Benefits:

* Measure reliability
* Prioritize engineering work
* Guide operational decisions
* Balance innovation and stability

---

# Real-World SLO Examples

## E-Commerce

```text id="y7m4ps"
99.95% Checkout Availability
```

---

## Banking

```text id="h5q1nd"
99.99% Transaction Success Rate
```

---

## Trading Platform

```text id="n8x2gf"
95% of Orders
Processed Within 100ms
```

---

## API Platform

```text id="d3v9tr"
99.9% API Availability
```

---

# Characteristics of Good SLOs

Good SLOs are:

* Realistic
* Measurable
* User-focused
* Aligned with business goals

Bad Example:

```text id="u7n5bc"
100% Availability
```

This is generally unrealistic.

Better Example:

```text id="v4m1jh"
99.95% Availability
```

---

# What is an SLA?

## Definition

SLA stands for:

```text id="b8q4wy"
Service Level Agreement
```

An SLA is a formal business commitment between a service provider and a customer.

It answers:

> What have we promised customers?

Unlike SLI and SLO, an SLA is contractual.

---

# Example SLA

A cloud provider may promise:

```text id="s2n8lm"
99.9% Monthly Availability
```

If the provider fails to meet this target, customers may receive:

* Service Credits
* Refunds
* Financial Compensation

---

# SLA Characteristics

An SLA typically includes:

* Availability Commitments
* Response Time Targets
* Support Expectations
* Financial Penalties

---

# Example

```text id="f1m9xb"
SLA = 99.5%
```

Actual Performance:

```text id="w7n3ks"
99.2%
```

Result:

```text id="r5x8td"
SLA Violation
```

Potential business consequences follow.

---

# Relationship Between SLI, SLO, and SLA

The relationship is simple:

```text id="j4c9pl"
SLI
 │
 ▼
SLO
 │
 ▼
SLA
```

---

# Practical Example

Measured Reliability:

```text id="x1m7rs"
Availability SLI = 99.95%
```

Internal Target:

```text id="k8n4zt"
Availability SLO = 99.90%
```

Customer Commitment:

```text id="c3p7md"
Availability SLA = 99.50%
```

---

# Understanding the Relationship

```text id="u6v9hp"
SLI > SLO > SLA
```

Why?

Because organizations want a safety margin.

If:

```text id="w1t8ng"
SLO = SLA
```

then even a small issue may cause contractual violations.

A buffer protects the business.

---

# Banking Example

Consider an online banking platform.

Measured Availability:

```text id="y5g2kx"
99.98%
```

Target:

```text id="t9m1wr"
99.95%
```

Customer Commitment:

```text id="h7x4lb"
99.90%
```

Result:

* SLI achieved
* SLO achieved
* SLA satisfied

---

# Trading System Example

A financial trading platform requires extremely low latency.

Measured:

```text id="m6p3qy"
95% Orders < 80ms
```

Target:

```text id="r2n7kc"
95% Orders < 100ms
```

Customer Commitment:

```text id="d5t8mf"
95% Orders < 150ms
```

The platform is performing better than required.

---

# How Dynatrace Supports SLI and SLO

Dynatrace provides native SLO management capabilities.

SRE teams can define:

* Availability SLOs
* Latency SLOs
* Error Rate SLOs
* Custom Business SLOs

Dynatrace continuously evaluates performance against defined objectives.

---

# Dynatrace SLO Architecture

```text id="q8p2xh"
Applications
      │
      ▼
Metrics
Logs
Traces
      │
      ▼
Dynatrace
      │
      ▼
SLO Evaluation
      │
      ▼
Dashboards
Alerts
Reports
```

---

# Common Mistakes

## Measuring Infrastructure Instead of User Experience

Bad:

```text id="z4x7km"
CPU Utilization
```

Better:

```text id="a9n5ld"
Successful User Transactions
```

---

## Unrealistic SLOs

Example:

```text id="k2m8yv"
100% Availability
```

Usually impractical.

---

## Too Many SLOs

Excessive objectives create confusion.

Focus on critical user journeys.

---

## Poorly Defined SLIs

An SLO is only useful if the underlying SLI is meaningful.

---

# Best Practices

1. Measure user experience.
2. Use meaningful SLIs.
3. Define realistic SLOs.
4. Maintain a buffer between SLO and SLA.
5. Review objectives regularly.
6. Align reliability goals with business goals.
7. Monitor SLO compliance continuously.

---

# Interview Questions

### What is an SLI?

A Service Level Indicator is a measurable indicator of service reliability or performance.

---

### What is an SLO?

A Service Level Objective is a target value for an SLI.

---

### What is an SLA?

A Service Level Agreement is a contractual commitment made to customers.

---

### How are SLI, SLO, and SLA related?

SLI measures actual performance.

SLO defines reliability targets.

SLA defines business commitments.

---

### Why Should SLO Be Higher Than SLA?

To provide a safety margin and reduce the risk of contractual violations.

---

### Give an Example of an Availability SLI.

```text id="z3v8fr"
99.95% Successful Requests
```

---

### Why Are SLOs Important in SRE?

They provide measurable reliability targets that guide engineering decisions.

---

# Key Takeaways

* Reliability must be measured.
* SLI represents actual measured performance.
* SLO represents internal reliability targets.
* SLA represents external customer commitments.
* SLI, SLO, and SLA form the foundation of reliability management.
* SRE teams rely heavily on these concepts.
* Dynatrace provides built-in support for SLO monitoring and reporting.
* These concepts directly lead to Error Budgets, one of the most important SRE practices.

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

## Further Reading

OpenTelemetry Documentation

https://opentelemetry.io/docs/

Cloud Native Computing Foundation

https://www.cncf.io/
