---
title: "Redis Cluster Modes: Enabled and Disabled - Database Caching Strategies Using Redis"
description: "Cluster Mode for Amazon ElastiCache (Redis OSS) is a feature that enables you to scale your Redis cluster without any impact on the cluster performance."
source_url: "https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/redis-cluster-modes-enabled-and-disabled.html"
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
# Redis Cluster Modes: Enabled and Disabled - Database Caching Strategies Using Redis

> Source: [Redis Cluster Modes: Enabled and Disabled - Database Caching Strategies Using Redis](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/redis-cluster-modes-enabled-and-disabled.html)
> Clipped: 2026-08-03

Redis Cluster Modes: Enabled and Disabled - Database Caching Strategies Using Redis

[Reader Endpoint](#reader-endpoint)

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

This whitepaper is for historical reference only. Some content might be outdated and some links might not be available.

Cluster Mode for Amazon ElastiCache (Redis OSS) is a feature that enables you to scale your Redis cluster without any impact on the cluster performance. While you initiate a scale-out operation by adding a specified number of new shards to a cluster, you also initiate scale-up or scale-down operation by selecting a new desired node type, Amazon ElastiCache (Redis OSS) a new cluster synchronizing the new nodes with the previous ones. Amazon ElastiCache (Redis OSS) supports three cluster configurations (Redis (single node), Redis (cluster mode disabled), and Redis (cluster mode enabled)), depending on your reliability, availability, and scaling requirements.

With Cluster Mode enabled, your Redis cluster can now scale horizontally (in or out) in addition to scaling vertically (up and down). In a Redis cluster with cluster mode disabled, you can have up to five read replicas in your replication group. Adding or removing replicas incurs no downtime to your application. In a Redis cluster with cluster mode enabled, clusters can have up to ninety shards by default (which can be increased if requested) and up to five read replicas in each node group.

Amazon ElastiCache (Redis OSS) with Cluster Mode enabled will help you to architect a cluster with unpredictable network and storage requirements, or with a write-heavy workload. This horizontal scalability is achieved by preparing a plan that results in an even distribution of the key spaces, which distributes the hash slots to the available shards within the cluster, and thus by spreading the workload over a greater number of nodes. By default, the hash slots get evenly distributed between shards, but customers can also configure a custom hash slot. It is recommended to resize your cluster during off-peak hours.

## Reader Endpoint

Amazon ElastiCache (Redis OSS) cluster with multiple nodes and with cluster mode disabled provides the reader endpoint to direct all your read traffic to a single cluster level endpoint. For more information on reader endpoints, see [Finding Replication Group Endpoints.](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Endpoints.html)

This reader endpoint splits your incoming read connection requests evenly between all read replicas. This reduces the need for the clients to direct traffic to an individual replica, and simplifies configuration to look up and access cached data. Reader endpoint also helps your Amazon ElastiCache (Redis OSS) cluster to load balance all read traffic, as well as to achieve High Availability by placing read replicas in different AWS Availability Zones (AZ).

Reader endpoints works with ElastiCache (Redis OSS) clusters with cluster-mode disabled. For more information, see [Finding Replication Group Endpoints](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Endpoints.html).