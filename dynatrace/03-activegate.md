# ActiveGate

## Introduction

Dynatrace environments often contain hundreds or thousands of monitored hosts, applications, containers, cloud resources, and services.

While OneAgent is responsible for collecting telemetry data, organizations also need a secure and scalable way to:

* Route traffic
* Connect isolated networks
* Integrate cloud services
* Execute monitoring extensions
* Reduce direct platform communication

This is where ActiveGate comes in.

ActiveGate acts as a secure communication gateway between monitored environments and the Dynatrace platform.

It helps organizations scale monitoring across complex enterprise environments while improving security, performance, and manageability.

---

# Learning Objectives

After completing this document, you should understand:

* What ActiveGate is
* Why ActiveGate is required
* ActiveGate architecture
* Environment ActiveGate
* Cluster ActiveGate
* Extension execution
* Cloud integrations
* Security considerations
* Enterprise deployment models
* Troubleshooting techniques
* Best practices

---

# What is ActiveGate?

## Definition

ActiveGate is a Dynatrace component that acts as a secure intermediary between monitored systems and the Dynatrace platform.

Its primary responsibilities include:

* Traffic Routing
* Secure Communication
* Extension Execution
* Cloud Monitoring
* Remote Monitoring
* API Access
* Load Distribution

ActiveGate improves scalability and reduces direct communication requirements.

---

# Why Do We Need ActiveGate?

Without ActiveGate:

```text
OneAgent
   │
   ▼
Dynatrace Platform
```

Every monitored host communicates directly with Dynatrace.

This may create challenges:

* Firewall restrictions
* Security concerns
* Network limitations
* Large-scale environments

---

With ActiveGate:

```text
Hosts
  │
  ▼
OneAgent
  │
  ▼
ActiveGate
  │
  ▼
Dynatrace Platform
```

Benefits:

* Centralized communication
* Reduced outbound connections
* Improved security
* Better scalability

---

# ActiveGate Architecture

High-Level View

```text
Applications
Servers
Containers
Cloud Resources
      │
      ▼
OneAgent
      │
      ▼
ActiveGate
      │
      ▼
Dynatrace Platform
```

ActiveGate serves as a communication hub.

---

# Major Functions of ActiveGate

ActiveGate provides several capabilities.

---

## Communication Routing

OneAgent traffic can be routed through ActiveGate.

Benefits:

* Reduced network complexity
* Simplified firewall management
* Better scalability

---

## Extension Execution

ActiveGate executes monitoring extensions.

Examples:

* Database Monitoring
* Network Device Monitoring
* Storage Monitoring
* Custom Integrations

---

## Cloud Monitoring

ActiveGate integrates with cloud providers.

Examples:

* AWS
* Azure
* Google Cloud Platform

---

## Synthetic Monitoring Support

ActiveGate can execute synthetic tests from private locations.

Benefits:

* Internal Application Monitoring
* Private Network Testing

---

## API Communication

ActiveGate facilitates communication with Dynatrace APIs.

---

# Types of ActiveGate

Dynatrace supports multiple ActiveGate types.

---

# Environment ActiveGate

Most commonly deployed type.

Responsibilities:

* OneAgent Communication
* Cloud Monitoring
* Extensions
* Synthetic Monitoring

Used in:

* Dynatrace SaaS
* Dynatrace Managed

---

# Environment ActiveGate Workflow

```text
OneAgent
    │
    ▼
Environment ActiveGate
    │
    ▼
Dynatrace Environment
```

---

# Cluster ActiveGate

Used primarily in Dynatrace Managed environments.

Responsibilities:

* Cluster Communication
* Internal Cluster Services
* Load Distribution

Cluster ActiveGate supports large enterprise deployments.

---

# ActiveGate Capabilities

---

## Cloud Monitoring

ActiveGate collects cloud service metrics.

Examples:

### AWS

* EC2
* RDS
* Lambda
* EKS

---

### Azure

* Virtual Machines
* App Services
* AKS
* Storage Accounts

---

### Google Cloud

* Compute Engine
* Cloud Run
* GKE

---

# Extension Framework

Many technologies cannot run OneAgent.

Examples:

* Network Devices
* Storage Systems
* Hardware Appliances

ActiveGate can collect metrics using extensions.

---

## Extension Examples

```text
Cisco Devices
VMware
Oracle Database
SAP Systems
Custom APIs
```

---

# ActiveGate and Kubernetes

In Kubernetes environments, ActiveGate provides:

* Cluster API Access
* Kubernetes Metadata
* Cloud Integrations
* Extension Execution

It complements OneAgent monitoring.

---

# ActiveGate and Synthetic Monitoring

Private Synthetic Monitoring uses ActiveGate.

Example:

```text
Synthetic Test
      │
      ▼
ActiveGate
      │
      ▼
Internal Application
```

Benefits:

* Internal Network Visibility
* Private Endpoint Testing

---

# ActiveGate and Security

ActiveGate improves security by reducing direct platform access.

Benefits:

* Controlled Communication Paths
* Reduced Attack Surface
* Simplified Firewall Rules
* Better Network Segmentation

---

# ActiveGate Deployment Models

---

## Single ActiveGate

```text
Environment
     │
     ▼
ActiveGate
```

Suitable for:

* Small environments
* Development environments

---

## Multiple ActiveGates

```text
Environment
     │
 ┌───┴───┐
 ▼       ▼
AG1     AG2
```

Suitable for:

* Production environments
* High availability requirements

---

## Distributed ActiveGate Architecture

```text
Region A
   │
 AG1

Region B
   │
 AG2

Region C
   │
 AG3
```

Used in:

* Global enterprises
* Multi-region deployments

---

# ActiveGate Communication Flow

Example:

```text
Application
     │
     ▼
OneAgent
     │
     ▼
ActiveGate
     │
     ▼
Dynatrace Platform
     │
     ▼
Davis AI
```

---

# ActiveGate and Davis AI

ActiveGate itself does not perform AI analysis.

However, it provides telemetry and integration data that Davis AI uses for:

* Root Cause Analysis
* Dependency Analysis
* Problem Detection
* Capacity Forecasting

---

# Benefits of ActiveGate

### Improved Security

Controlled communication channels.

---

### Better Scalability

Supports large environments.

---

### Extension Support

Monitor technologies without OneAgent.

---

### Cloud Visibility

Integrates with major cloud providers.

---

### Reduced Network Complexity

Centralized communication.

---

# Common Use Cases

---

## Enterprise Firewall Restrictions

Hosts cannot access the internet directly.

Solution:

```text
OneAgent
   │
   ▼
ActiveGate
   │
   ▼
Dynatrace SaaS
```

---

## Cloud Monitoring

Collect metrics from AWS and Azure.

---

## Private Synthetic Monitoring

Monitor internal applications.

---

## Database Monitoring

Use extensions through ActiveGate.

---

# Troubleshooting ActiveGate

Common issues include:

---

## ActiveGate Offline

Possible causes:

* Service stopped
* Network failure
* Resource exhaustion

---

## OneAgent Connectivity Issues

Possible causes:

* DNS resolution
* Firewall rules
* Routing issues

---

## Extension Failures

Possible causes:

* Configuration errors
* Authentication issues
* API limitations

---

# ActiveGate Validation Checklist

```text
ActiveGate Installed
       │
       ▼
ActiveGate Running
       │
       ▼
Connected to Dynatrace
       │
       ▼
OneAgent Traffic Received
       │
       ▼
Extensions Working
       │
       ▼
Cloud Integrations Operational
```

---

# Real-World Example

An organization monitors:

* 2,000 Servers
* 500 Applications
* AWS Environment
* Kubernetes Clusters

Instead of:

```text
2000 Direct Connections
```

they use:

```text
Servers
   │
   ▼
ActiveGate Cluster
   │
   ▼
Dynatrace
```

Benefits:

* Simplified networking
* Better scalability
* Easier management

---

# Interview Questions

### What is ActiveGate?

A Dynatrace component that acts as a secure communication gateway between monitored systems and the Dynatrace platform.

---

### Why is ActiveGate Needed?

To improve scalability, security, cloud integrations, and extension execution.

---

### What is the Difference Between OneAgent and ActiveGate?

OneAgent collects telemetry.

ActiveGate routes traffic and provides additional platform services.

---

### What is Environment ActiveGate?

The most commonly deployed ActiveGate used for communication, cloud monitoring, synthetic monitoring, and extensions.

---

### What is Cluster ActiveGate?

An ActiveGate used in Dynatrace Managed environments to support cluster communication and scaling.

---

### Can ActiveGate Monitor Cloud Services?

Yes.

It integrates with AWS, Azure, and Google Cloud.

---

### Can ActiveGate Replace OneAgent?

No.

OneAgent and ActiveGate serve different purposes.

---

# Key Takeaways

* ActiveGate is a communication and integration layer within Dynatrace.
* It improves scalability and security.
* It supports cloud monitoring, extensions, synthetic monitoring, and API communication.
* Environment ActiveGate is the most common deployment type.
* ActiveGate complements OneAgent rather than replacing it.
* Enterprise environments frequently rely on ActiveGate for large-scale monitoring architectures.

---

# References

## Official Documentation

https://docs.dynatrace.com/docs/ingest-from/dynatrace-activegate

## ActiveGate Installation Guide

https://docs.dynatrace.com/docs/ingest-from/dynatrace-activegate/installation

## Dynatrace University

https://university.dynatrace.com/

## Dynatrace Community

https://community.dynatrace.com/
