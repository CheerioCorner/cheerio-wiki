---
title: "Types of database caching - Database Caching Strategies Using Redis"
description: "A database cache supplements your primary database by removing unnecessary pressure on it, typically in the form of frequently-accessed read data. The cache itself can live in several areas, including in your database, in the application, or as a standalone layer."
source_url: "https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/types-of-database-caching.html"
source_domain: "amazon.com"
author:
published:
clipped: 2026-08-03
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Types of database caching - Database Caching Strategies Using Redis

> Source: [Types of database caching - Database Caching Strategies Using Redis](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/types-of-database-caching.html)
> Clipped: 2026-08-03

Types of database caching - Database Caching Strategies Using Redis

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

A database cache supplements your primary database by removing unnecessary pressure on it, typically in the form of frequently-accessed read data. The cache itself can live in several areas, including in your database, in the application, or as a standalone layer.

The following are the three most common types of database caches:

## Database-integrated caches

Some databases, such as [Amazon Aurora](https://aws.amazon.com/rds/aurora/), offer an integrated cache that is managed within the database engine and has built-in write-through capabilities. The database updates its cache automatically when the underlying data changes. Nothing in the application tier is required to use this cache.

The downside of integrated caches is their size and capabilities. Integrated caches are typically limited to the available memory that is allocated to the cache by the database instance and can’t be used for other purposes, such as sharing data with other instances.

## Local caches

A local cache stores your frequently-used data within your application. This makes data retrieval faster than with other caching architectures because it removes network traffic that is associated with retrieving data.

A major disadvantage is that among your applications, each node has its own resident cache working in a disconnected manner. The information that is stored in an individual cache node (whether it’s cached database rows, web content, or session data) can’t be shared with other local caches. This creates challenges in a distributed environment where information sharing is critical to support scalable dynamic environments.

Because most applications use multiple application servers, coordinating the values across them becomes a major challenge if each server has its own cache. In addition, when outages occur, the data in the local cache is lost and must be rehydrated, which effectively negates the cache. The majority of these disadvantages are mitigated with remote caches.

## Remote caches

A remote cache (or *side cache*) is a separate instance (or separate instances) dedicated for storing the cached data in-memory. Remote caches are stored on dedicated servers and are typically built on key/value NoSQL stores, such as [Redis](https://redis.io/download) and [Memcached](https://memcached.org/). They provide hundreds of thousands of requests (and up to a million) per second per cache node. Many solutions, such as [Amazon ElastiCache (Redis OSS)](https://aws.amazon.com/elasticache/redis/), also provide the high availability needed for critical workloads.

The average latency of a request to a remote cache is on the sub-millisecond timescale, which, in the order of magnitude, is faster than a request to a disk-based database. At these speeds, local caches are seldom necessary. Remote caches are ideal for distributed environments because they work as a connected cluster that all your disparate systems can utilize. However, when network latency is a concern, you can apply a two-tier caching strategy that uses a local and remote cache together. This paper doesn’t describe this strategy in detail, but it’s typically used only when needed because of the complexity it adds.

With remote caches, the orchestration between caching the data and managing the validity of the data is managed by your applications and/or processes that use it. The cache itself is not directly connected to the database but is used adjacently to it.

The remainder of this paper focuses on using remote caches, and specifically Amazon ElastiCache (Redis OSS), for caching relational database data.