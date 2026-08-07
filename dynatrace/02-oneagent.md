# OneAgent

## Introduction

Dynatrace OneAgent is the primary monitoring agent used by the Dynatrace platform.

It is responsible for automatically discovering, monitoring, and collecting telemetry data from applications, services, processes, containers, hosts, cloud environments, and Kubernetes clusters.

Unlike traditional monitoring agents that require extensive manual configuration, OneAgent automatically detects:

* Hosts
* Processes
* Services
* Applications
* Containers
* Databases
* Cloud Resources
* Dependencies

This automatic discovery capability is one of the major reasons organizations adopt Dynatrace.

OneAgent serves as the foundation of Dynatrace observability.

Without OneAgent, Dynatrace cannot automatically build topology maps, service flows, distributed traces, or perform AI-driven root cause analysis.

---

# Learning Objectives

After completing this document, you should understand:

* What OneAgent is
* Why OneAgent is required
* OneAgent architecture
* How OneAgent works
* Automatic discovery
* Monitoring capabilities
* Deployment models
* Installation methods
* OneAgent in Kubernetes
* Security considerations
* Best practices
* Troubleshooting techniques

---

# What is OneAgent?

## Definition

OneAgent is a lightweight monitoring agent installed on monitored systems.

Its primary responsibilities include:

* Data Collection
* Auto Discovery
* Process Monitoring
* Service Monitoring
* Infrastructure Monitoring
* Distributed Tracing
* Log Collection
* Dependency Detection

It continuously sends telemetry data to the Dynatrace platform for analysis.

---

# Why OneAgent is Important

Before OneAgent:

```text
Application
   │
   ▼
Manual Configuration
   │
   ▼
Monitoring Tool
```

Problems:

* Time consuming
* Configuration errors
* Missing dependencies
* Limited visibility

---

With OneAgent:

```text
Application
      │
      ▼
OneAgent
      │
      ▼
Automatic Discovery
      │
      ▼
Dynatrace
```

Benefits:

* Faster deployment
* Better visibility
* Less manual effort
* Automatic topology mapping

---

# OneAgent Architecture

High-level architecture:

```text
Applications
Processes
Containers
Databases
Infrastructure
      │
      ▼
OneAgent
      │
      ▼
ActiveGate (Optional)
      │
      ▼
Dynatrace Platform
      │
      ▼
Davis AI
```

OneAgent acts as the primary telemetry collection layer.

---

# What Data Does OneAgent Collect?

OneAgent collects multiple types of telemetry.

---

## Infrastructure Metrics

Examples:

* CPU Usage
* Memory Usage
* Disk Utilization
* Network Utilization

---

## Process Metrics

Examples:

* Process Availability
* Process CPU Usage
* Process Memory Usage
* Process Restarts

---

## Service Metrics

Examples:

* Request Count
* Response Time
* Error Rate
* Throughput

---

## Distributed Traces

Examples:

```text
User Request
      │
      ▼
Frontend
      │
      ▼
API Gateway
      │
      ▼
Service A
      │
      ▼
Database
```

OneAgent tracks the complete transaction path.

---

## Logs

Examples:

* Application Logs
* System Logs
* Container Logs

---

## Events

Examples:

* Deployments
* Service Restarts
* Configuration Changes
* Infrastructure Events

---

# Automatic Discovery

OneAgent automatically discovers:

```text
Hosts
   │
Processes
   │
Services
   │
Applications
   │
Dependencies
```

No manual service mapping is required.

---

# Host Discovery

OneAgent automatically identifies:

* Host Name
* Operating System
* CPU Information
* Memory Information
* Network Interfaces

Supported operating systems include:

* Linux
* Windows
* Unix Variants

---

# Process Discovery

OneAgent detects running processes automatically.

Examples:

```text
java.exe
python.exe
nginx
apache
node
dotnet
```

Detected processes are monitored immediately.

---

# Service Discovery

OneAgent automatically detects services.

Examples:

* REST APIs
* Web Applications
* Background Services
* Microservices

Service discovery is dynamic.

New services appear automatically.

---

# Dependency Discovery

OneAgent identifies communication between services.

Example:

```text
Frontend
    │
    ▼
API Gateway
    │
    ▼
Order Service
    │
    ▼
Payment Service
```

These dependencies become visible in Smartscape and Service Flow.

---

# Deep Process Monitoring

OneAgent supports code-level visibility.

Examples:

* Method Execution Time
* Database Calls
* External Service Calls
* Exceptions

This enables detailed performance analysis.

---

# Supported Technologies

OneAgent supports many technologies.

Examples:

### Java

* Spring Boot
* Jakarta EE
* Tomcat

---

### .NET

* ASP.NET
* .NET Core

---

### Node.js

* Express
* NestJS

---

### Python

* Django
* Flask
* FastAPI

---

### PHP

* Laravel
* Symfony

---

### Web Servers

* Apache
* NGINX
* IIS

---

### Databases

* Oracle
* MySQL
* PostgreSQL
* SQL Server

---

# OneAgent Deployment Models

OneAgent can be deployed in multiple ways.

---

## Host-Based Installation

Most common deployment method.

```text
Host
 │
 ▼
OneAgent Installed
```

Benefits:

* Full visibility
* Easy management

---

## Container Deployment

Used in containerized environments.

Examples:

* Docker
* Podman

---

## Kubernetes Deployment

Most common cloud-native deployment.

OneAgent is usually deployed as:

```text
DaemonSet
```

Benefits:

* Automatic node coverage
* Cluster-wide visibility

---

# OneAgent in Kubernetes

Monitors:

* Nodes
* Pods
* Containers
* Namespaces
* Services
* Workloads

Provides:

* Infrastructure Metrics
* Kubernetes Events
* Distributed Tracing

---

# OneAgent and Smartscape

OneAgent continuously feeds topology information into Smartscape.

Example:

```text
Host
  │
  ▼
Process
  │
  ▼
Service
```

This relationship is automatically maintained.

---

# OneAgent and Service Flow

OneAgent captures request paths.

Example:

```text
User
  │
  ▼
Frontend
  │
  ▼
Backend
  │
  ▼
Database
```

This enables end-to-end transaction analysis.

---

# OneAgent and Davis AI

OneAgent provides the telemetry required by Davis AI.

Davis uses collected data to perform:

* Problem Detection
* Event Correlation
* Root Cause Analysis
* Predictive Analysis

Without OneAgent, Davis AI loses critical visibility.

---

# Security Considerations

Organizations should consider:

* Agent Permissions
* Network Access
* Firewall Rules
* Data Privacy Requirements
* Compliance Policies

Dynatrace provides secure communication using encrypted channels.

---

# Resource Consumption

OneAgent is designed to be lightweight.

Resource usage depends on:

* Number of Services
* Application Complexity
* Monitoring Scope
* Log Collection Volume

Typically, overhead is minimal compared to the visibility gained.

---

# Best Practices

### Install OneAgent Broadly

Monitor all critical systems.

---

### Keep OneAgent Updated

Use supported versions.

---

### Validate Auto Discovery

Verify services and dependencies are detected correctly.

---

### Monitor Agent Health

Ensure OneAgent remains operational.

---

### Review Monitoring Scope

Avoid unnecessary monitoring overhead.

---

# Common Issues

## Service Not Detected

Possible causes:

* Unsupported technology
* Process restart required
* Configuration issue

---

## Missing Traces

Possible causes:

* Network issues
* Instrumentation limitations
* Service communication changes

---

## Agent Connectivity Problems

Possible causes:

* Firewall restrictions
* DNS issues
* ActiveGate connectivity issues

---

# Troubleshooting Workflow

```text
Agent Installed
      │
      ▼
Agent Running?
      │
      ▼
Data Collected?
      │
      ▼
Services Detected?
      │
      ▼
Dependencies Visible?
      │
      ▼
Dynatrace UI Validation
```

---

# Real-World Example

A Spring Boot application is deployed.

OneAgent automatically discovers:

```text
Host
   │
   ▼
Java Process
   │
   ▼
Spring Boot Service
   │
   ▼
Database Dependencies
```

No manual service registration is required.

Dynatrace immediately begins collecting:

* Metrics
* Logs
* Traces
* Events

---

# Interview Questions

### What is OneAgent?

Dynatrace's primary monitoring agent responsible for automatic discovery and telemetry collection.

---

### What Does OneAgent Collect?

Metrics, logs, traces, events, topology information, and dependency data.

---

### Does OneAgent Require Manual Service Configuration?

No.

OneAgent automatically discovers services and dependencies.

---

### How Does OneAgent Support Distributed Tracing?

It tracks requests across multiple services and systems.

---

### Can OneAgent Monitor Kubernetes?

Yes.

It provides cluster, node, pod, container, and service visibility.

---

### Why is OneAgent Important?

Because it provides the telemetry foundation required for monitoring, observability, Smartscape, Service Flow, and Davis AI.

---

# Key Takeaways

* OneAgent is the foundation of Dynatrace monitoring.
* It automatically discovers hosts, processes, services, and dependencies.
* It collects metrics, logs, traces, and events.
* It supports cloud, Kubernetes, containerized, and traditional environments.
* OneAgent powers Smartscape, Service Flow, and Davis AI.
* Automatic discovery significantly reduces manual configuration effort.
* Proper deployment of OneAgent is critical for successful Dynatrace adoption.

---

# References

## Official Documentation

https://docs.dynatrace.com/docs/ingest-from/dynatrace-oneagent

## OneAgent Installation Guide

https://docs.dynatrace.com/docs/ingest-from/dynatrace-oneagent/installation-and-operation

## Dynatrace University

https://university.dynatrace.com/

## Dynatrace Community

https://community.dynatrace.com/
