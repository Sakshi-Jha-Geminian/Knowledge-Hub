# Smartscape

## Introduction

Modern IT environments contain hundreds or thousands of interconnected components.

A single business transaction may travel through:

* User Applications
* Web Servers
* APIs
* Microservices
* Databases
* Containers
* Kubernetes Clusters
* Cloud Services

Understanding these relationships manually becomes difficult as environments grow.

Dynatrace solves this challenge through Smartscape.

Smartscape automatically discovers, maps, and visualizes relationships between infrastructure, processes, services, and applications in real time.

It provides a continuously updated topology of the entire environment without requiring manual configuration.

---

# Learning Objectives

After completing this document, you should understand:

* What Smartscape is
* Why Smartscape is important
* Smartscape architecture
* Entity relationships
* Dependency mapping
* Topology visualization
* Impact analysis
* Root cause navigation
* Smartscape and Davis AI
* Real-world use cases
* Best practices

---

# What is Smartscape?

## Definition

Smartscape is Dynatrace's real-time topology mapping engine.

It automatically discovers and visualizes:

* Hosts
* Processes
* Services
* Applications
* Containers
* Kubernetes Components
* Cloud Resources
* Dependencies

The topology continuously updates as the environment changes.

---

# Why Smartscape Matters

Without Smartscape:

```text
Application Issue
      │
      ▼
Manual Investigation
      │
      ▼
Check Logs
Check Services
Check Databases
Check Infrastructure
```

Investigation can take hours.

---

With Smartscape:

```text
Problem
   │
   ▼
Affected Service
   │
   ▼
Dependency Chain
   │
   ▼
Root Cause
```

Investigation becomes significantly faster.

---

# Smartscape Architecture

Smartscape builds its topology using data collected by OneAgent.

High-Level Flow:

```text
Applications
Services
Processes
Hosts
     │
     ▼
OneAgent
     │
     ▼
Dynatrace
     │
     ▼
Smartscape
```

Smartscape continuously updates relationships.

---

# Smartscape Layers

Smartscape is organized into multiple logical layers.

---

# Layer 1: Applications

Represents user-facing applications.

Examples:

* Web Applications
* Mobile Applications
* Single Page Applications

Examples:

```text
Customer Portal
Online Banking
E-Commerce Website
```

Applications consume services.

---

# Layer 2: Services

Represents application services and APIs.

Examples:

```text
Authentication Service
Order Service
Payment Service
Inventory Service
```

Services communicate with one another.

---

# Layer 3: Processes

Represents running processes.

Examples:

```text
java.exe
python.exe
node.exe
nginx
apache
```

Processes host services.

---

# Layer 4: Infrastructure

Represents physical and virtual resources.

Examples:

```text
Servers
Virtual Machines
Containers
Nodes
Cloud Instances
```

Infrastructure hosts processes.

---

# Smartscape Relationship Model

The hierarchy typically follows:

```text
Application
      │
      ▼
Service
      │
      ▼
Process
      │
      ▼
Host
```

This relationship is discovered automatically.

---

# Automatic Dependency Mapping

One of Smartscape's most powerful capabilities is dependency detection.

Example:

```text
Frontend Service
        │
        ▼
API Gateway
        │
        ▼
Order Service
        │
        ▼
Payment Service
        │
        ▼
Database
```

Dynatrace automatically identifies these connections.

---

# Dynamic Topology

Modern environments constantly change.

Examples:

* Containers Start
* Containers Stop
* Pods Scale Up
* Pods Scale Down
* New Services Deploy

Smartscape automatically updates topology without manual intervention.

---

# Smartscape in Microservices

Microservice environments often contain hundreds of services.

Example:

```text
User Service
Order Service
Inventory Service
Payment Service
Notification Service
```

Understanding dependencies manually becomes difficult.

Smartscape automatically visualizes relationships.

---

# Smartscape in Kubernetes

Smartscape provides visibility into:

* Clusters
* Nodes
* Namespaces
* Pods
* Containers
* Services

Example:

```text
Cluster
   │
   ▼
Node
   │
   ▼
Pod
   │
   ▼
Container
   │
   ▼
Service
```

This helps engineers understand Kubernetes architecture quickly.

---

# Smartscape and Cloud Environments

Smartscape can visualize relationships across:

* AWS
* Azure
* Google Cloud

Examples:

```text
EC2
RDS
Lambda
AKS
EKS
GKE
```

Dependencies become visible across cloud services.

---

# Impact Analysis

Impact analysis helps determine which components are affected by an issue.

Example:

```text
Database Failure
      │
      ▼
Payment Service
      │
      ▼
Checkout Service
      │
      ▼
Customer Impact
```

Smartscape shows the complete impact chain.

---

# Root Cause Analysis

Smartscape works closely with Davis AI.

Example:

```text
Application Slowdown
        │
        ▼
Service Latency
        │
        ▼
Database Issue
        │
        ▼
Root Cause Identified
```

This reduces troubleshooting time.

---

# Smartscape and Davis AI

Smartscape provides topology information.

Davis AI uses:

* Dependencies
* Relationships
* Service Flows
* Infrastructure Data

to determine:

* Root Causes
* Impacted Services
* Business Impact

Without Smartscape, Davis AI would have significantly less context.

---

# Smartscape vs Service Flow

These features are often confused.

---

## Smartscape

Focus:

```text
Topology
Relationships
Dependencies
Environment Structure
```

Answers:

> What is connected to what?

---

## Service Flow

Focus:

```text
Request Paths
Transactions
Communication Flows
Latency Analysis
```

Answers:

> How did a request travel?

---

# Real-World Example

An online shopping application contains:

```text
Frontend
API Gateway
Product Service
Order Service
Payment Service
Database
```

Payment Service begins failing.

Smartscape immediately shows:

```text
Database
    │
    ▼
Payment Service
    │
    ▼
Checkout Service
    │
    ▼
Customer Portal
```

Teams can quickly understand:

* Root Cause
* Affected Services
* Customer Impact

---

# Benefits of Smartscape

## Automatic Discovery

No manual topology creation.

---

## Real-Time Updates

Topology updates automatically.

---

## Faster Troubleshooting

Dependencies become visible immediately.

---

## Better Root Cause Analysis

Provides context for Davis AI.

---

## Cloud-Native Visibility

Supports Kubernetes and cloud environments.

---

## Reduced MTTR

Engineers identify issues faster.

---

# Common Use Cases

## Incident Investigation

Identify affected services quickly.

---

## Change Impact Analysis

Understand which systems may be affected.

---

## Architecture Visualization

Understand complex environments.

---

## Dependency Analysis

Discover service relationships.

---

## Kubernetes Troubleshooting

Understand cluster dependencies.

---

# Best Practices

### Deploy OneAgent Broadly

Smartscape accuracy depends on visibility.

---

### Validate Service Naming

Ensure services are clearly identified.

---

### Monitor Dependency Changes

Review topology after major deployments.

---

### Use Smartscape During Incidents

Start investigations with topology analysis.

---

### Combine with Service Flow

Use topology and request flow together.

---

# Common Challenges

## Missing Dependencies

Possible causes:

* Incomplete monitoring coverage
* Unsupported communication methods
* Agent issues

---

## Large Topologies

Enterprise environments may contain:

* Thousands of Services
* Thousands of Processes
* Hundreds of Hosts

Filtering becomes important.

---

# Interview Questions

### What is Smartscape?

Dynatrace's real-time topology mapping engine.

---

### What Does Smartscape Show?

Applications, services, processes, hosts, containers, cloud resources, and dependencies.

---

### How Does Smartscape Build Topology?

Using telemetry collected by OneAgent.

---

### Is Smartscape Manually Configured?

No.

Discovery and mapping are automatic.

---

### How Does Smartscape Help Davis AI?

It provides dependency and relationship information used for root cause analysis.

---

### What is the Difference Between Smartscape and Service Flow?

Smartscape shows topology and dependencies.

Service Flow shows request paths and transaction journeys.

---

### Can Smartscape Visualize Kubernetes?

Yes.

It supports clusters, nodes, pods, containers, namespaces, and services.

---

# Key Takeaways

* Smartscape is Dynatrace's topology visualization engine.
* It automatically discovers relationships across the environment.
* It visualizes applications, services, processes, hosts, containers, and cloud resources.
* It continuously updates as environments change.
* It enables dependency mapping and impact analysis.
* It provides critical context for Davis AI.
* Smartscape significantly reduces troubleshooting effort in complex environments.

---

# References

## Official Documentation

https://docs.dynatrace.com/

## Dynatrace Smartscape Documentation

https://docs.dynatrace.com/docs/discover-dynatrace/platform/smartscape

## Dynatrace University

https://university.dynatrace.com/

## Dynatrace Community

https://community.dynatrace.com/
