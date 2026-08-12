# Spark Broadcast Joins

## Learning Objectives

By the end of this module, you will understand:

- What a Join Is
- Why Joins Are Expensive in Distributed Systems
- Common Join Types
- Shuffle Joins
- Broadcast Joins
- Broadcast Variables vs Broadcast Joins
- `broadcast()`
- How Broadcast Join Works
- When to Use Broadcast Joins
- When NOT to Use Them
- Broadcast Size Considerations
- Join Strategies
- Sort-Merge Join
- Broadcast Hash Join
- Nested Loop Joins
- AQE and Broadcast Joins
- Data Skew and Joins
- Join Hints
- Performance Optimization
- Real-World Examples
- Common Mistakes
- Interview Questions

---

# Introduction

Joins are one of the most important operations in data engineering.

A join combines records from two datasets based on a related key.

For example:

```text
Customers
    +
Orders
    ↓
Customer Order Information
