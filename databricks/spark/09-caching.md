# Spark Caching and Persistence

## Learning Objectives

By the end of this module, you will understand:

- What caching means in Spark
- Why caching is needed
- Lazy evaluation and caching
- `cache()`
- `persist()`
- Cache vs persist
- Spark storage levels
- Memory-only caching
- Memory-and-disk caching
- Disk persistence
- Cache eviction
- `unpersist()`
- When caching helps
- When caching hurts
- Caching DataFrames
- Caching RDDs
- Cache and Spark jobs
- Cache and lineage
- Cache monitoring
- Common mistakes
- Performance optimization
- Real-world examples
- Interview questions

---

# Introduction

Spark is designed to process large datasets efficiently.

However, sometimes the same dataset is used multiple times.

For example:

```text
Read Data
    ↓
Clean Data
    ↓
Filter Data
    ↓
     ├── Analysis 1
     ├── Analysis 2
     └── Analysis 3
