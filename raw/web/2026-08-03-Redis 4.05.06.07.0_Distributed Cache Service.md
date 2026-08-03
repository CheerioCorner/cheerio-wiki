---
title: "Redis 4.0/5.0/6.0/7.0_Distributed Cache Service"
description: "This section describes DCS Redis 4.0/5.0/6.0/7.0 basic edition instance specifications, including the total memory, available memory, maximum number of connections allowe"
source_url: "https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-0522002.html"
source_domain: "huaweicloud.com"
author:
published: 2025-08-11
clipped: 2026-08-03
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Redis 4.0/5.0/6.0/7.0_Distributed Cache Service

> Source: [Redis 4.0/5.0/6.0/7.0_Distributed Cache Service](https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-0522002.html)
> Clipped: 2026-08-03

[View PDF](https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-productdesc-pdf.pdf)

Copied.

## Redis 4.0/5.0/6.0/7.0

This section describes DCS Redis 4.0/5.0/6.0/7.0 basic edition instance specifications, including the total memory, available memory, maximum number of connections allowed, maximum/assured bandwidth, and reference performance.

The following metrics are related to the instance specifications:

- Used memory: You can check the memory usage of an instance by viewing the **Memory Usage** and **Used Memory** metrics.
- Maximum connections: The maximum number of connections allowed is the maximum number of clients that can be connected to an instance. To check the number of connections to an instance, view the **Connected Clients** metric. This metric is implemented by the **maxclients** parameter (unavailable for read/write splitting instances). To modify this parameter, go to the **Instance Configuration** > **Parameters** page on the console.
- QPS represents queries per second, which is the number of commands processed per second. For details about QPS testing, see the [Performance White Paper](https://support.huaweicloud.com/intl/en-us/usermanual-dcs/dcs_03_0009.html).
- Bandwidth: You can view the **Flow Control Times** metric to check whether the bandwidth has exceeded the limit. You can also check the **Bandwidth Usage** metric. This metric is for reference only, because it may be higher than 100%. For details, see [Why Does Bandwidth Usage Exceed 100%?](https://support.huaweicloud.com/intl/en-us/dcs_faq/dcs-faq-0513001.html)

- DCS Redis 4.0, 5.0, and 6.0 basic edition instances are available in single-node, master/standby, Proxy Cluster, Redis Cluster, and read/write splitting types.
- **DCS Redis 7.0 instances are available in single-node, master/standby, and Redis Cluster types.**
- DCS Redis instances support x86 and Arm architectures. **x86 is recommended. Arm is unavailable in some regions.**
- The specifications available on the console vary by region.
- The **Assured/Maximum Bandwidth** in the instance specifications is the instance's default bandwidth. To adjust a DCS instance's bandwidth, see [Adjusting DCS Instance Bandwidth](https://support.huaweicloud.com/intl/en-us/usermanual-dcs/dcs_03_0002.html).

#### Single-Node Instances

| Total Memory  (GB) | Available Memory  (GB) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- |
| 0.125 | 0.125 | 10,000/10,000 | 40/40 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.tiny.128  Arm: redis.single.au1.tiny.128 |
| 0.25 | 0.25 | 10,000/10,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.tiny.256  Arm: redis.single.au1.tiny.256 |
| 0.5 | 0.5 | 10,000/10,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.tiny.512  Arm: redis.single.au1.tiny.512 |
| 1 | 1 | 10,000/50,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.1  Arm: redis.single.au1.large.1 |
| 2 | 2 | 10,000/50,000 | 128/128 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.2  Arm: redis.single.au1.large.2 |
| 4 | 4 | 10,000/50,000 | 192/192 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.4  Arm: redis.single.au1.large.4 |
| 8 | 8 | 10,000/50,000 | 192/192 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.8  Arm: redis.single.au1.large.8 |
| 16 | 16 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.16  Arm: redis.single.au1.large.16 |
| 24 | 24 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.24  Arm: redis.single.au1.large.24 |
| 32 | 32 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.32  Arm: redis.single.au1.large.32 |
| 48 | 48 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.48  Arm: redis.single.au1.large.48 |
| 64 | 64 | 10,000/50,000 | 384/384 | x86: 100,000  Arm: 80,000 | x86: redis.single.xu1.large.64  Arm: redis.single.au1.large.64 |

#### Master/Standby Instances

Each instance has two default replicas (one master node and one standby node) and they can be set.

Given the same memory size, the differences between x86-based master/standby instances, Arm-based master/standby instances, and master/standby instances with multiple replicas are as follows:

- The available memory, maximum number of connections, and assured/maximum bandwidth are the same.
- Specification code: [Table 2](#dcs-pd-0522002__table12667255595) only lists the specification names of instances with two default replicas. The specification names reflect the number of replicas, for example, redis.ha.xu1.large.**r2**.8 (master/standby | x86 | 2 replicas | 8 GB) and redis.ha.xu1.large.**r3**.8 (master/standby | x86 | 3 replicas | 8 GB).
- IP addresses: Number of occupied IP addresses = Number of master nodes × Number of replicas. For example:
	2 replicas: Number of occupied IP addresses = 1 x 2 = 2
	3 replicas: Number of occupied IP addresses = 1 × 3 = 3

| Total Memory  (GB) | Available Memory  (GB) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- |
| 0.125 | 0.125 | 10,000/10,000 | 40/40 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.tiny.r2.128  Arm: redis.ha.au1.tiny.r2.128 |
| 0.25 | 0.25 | 10,000/10,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.tiny.r2.256  Arm: redis.ha.au1.tiny.r2.256 |
| 0.5 | 0.5 | 10,000/10,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.tiny.r2.512  Arm: redis.ha.au1.tiny.r2.512 |
| 1 | 1 | 10,000/50,000 | 80/80 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.1  Arm: redis.ha.au1.large.r2.1 |
| 2 | 2 | 10,000/50,000 | 128/128 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.2  Arm: redis.ha.au1.large.r2.2 |
| 4 | 4 | 10,000/50,000 | 192/192 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.4  Arm: redis.ha.au1.large.r2.4 |
| 8 | 8 | 10,000/50,000 | 192/192 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.8  Arm: redis.ha.au1.large.r2.8 |
| 16 | 16 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.16  Arm: redis.ha.au1.large.r2.16 |
| 24 | 24 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.24  Arm: redis.ha.au1.large.r2.24 |
| 32 | 32 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.32  Arm: redis.ha.au1.large.r2.32 |
| 48 | 48 | 10,000/50,000 | 256/256 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.48  Arm: redis.ha.au1.large.r2.48 |
| 64 | 64 | 10,000/50,000 | 384/384 | x86: 100,000  Arm: 80,000 | x86: redis.ha.xu1.large.r2.64  Arm: redis.ha.au1.large.r2.64 |

#### Proxy Cluster Instances

- The following table lists only the Proxy Cluster instance specifications with default shards. If you customize shards, see the maximum number of connections, assured/maximum bandwidth, and product specification code (flavor) in the **Instance Specification** table on the **Buy DCS Instance** page of the DCS console.
- The maximum connections of a cluster are for the entire instance, and not for a single shard. Maximum connections per shard = Maximum connections of an instance/Number of shards
- The maximum bandwidth and assured bandwidth of a cluster is for the entire instance, and not for a single shard. The relationship between the instance bandwidth and the bandwidth of a single shard is as follows:
	- Instance bandwidth = Bandwidth of a single shard x Number of shards
		- If an instance has more than 16 shards, single-shard bandwidth is determined by the actual value displayed on the bandwidth adjustment page due to ELB bandwidth limits.
		- The upper limit of the bandwidth of a Proxy Cluster instance is 10,000 Mbit/s. That is, even if the bandwidth of a single shard multiplied by the number of shards is greater than 10,000 Mbit/s, the bandwidth of the instance is still 10,000 Mbit/s.

| Specification  (GB) | Available Memory  (GB) | Shards (Master Nodes) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- | --- |
| 4 | 4 | 3 | 20,000/20,000 | 2,304/2,304 | x86: 300,000  Arm: 240,000 | x86: redis.proxy.xu1.large.4  Arm: redis.proxy.au1.large.4 |
| 8 | 8 | 3 | 30,000/30,000 | 2,304/2,304 | x86: 300,000  Arm: 240,000 | x86: redis.proxy.xu1.large.8  Arm: redis.proxy.au1.large.8 |
| 16 | 16 | 3 | 30,000/30,000 | 2,304/2,304 | x86: 300,000  Arm: 240,000 | x86: redis.proxy.xu1.large.16  Arm: redis.proxy.au1.large.16 |
| 24 | 24 | 3 | 30,000/30,000 | 2,304/2,304 | x86: 300,000  Arm: 240,000 | x86: redis.proxy.xu1.large.24  Arm: redis.proxy.au1.large.24 |
| 32 | 32 | 3 | 30,000/30,000 | 2,304/2,304 | x86: 300,000  Arm: 240,000 | x86: redis.proxy.xu1.large.32  Arm: redis.proxy.au1.large.32 |
| 48 | 48 | 6 | 60,000/60,000 | 4,608/4,608 | x86: 600,000  Arm: 480,000 | x86: redis.proxy.xu1.large.48  Arm: redis.proxy.au1.large.48 |
| 64 | 64 | 8 | 80,000/80,000 | 6,144/6,144 | x86: 800,000  Arm: 640,000 | x86: redis.proxy.xu1.large.64  Arm: redis.proxy.au1.large.64 |
| 96 | 96 | 12 | 120,000/120,000 | 9,216/9,216 | x86: 1,200,000  Arm: 960,000 | x86: redis.proxy.xu1.large.96  Arm: redis.proxy.au1.large.96 |
| 128 | 128 | 16 | 160,000/160,000 | 10,000/10,000 | x86: 1,600,000  Arm: 1,280,000 | x86: redis.proxy.xu1.large.128  Arm: redis.proxy.au1.large.128 |
| 192 | 192 | 24 | 200,000/240,000 | 10,000/10,000 | x86: 2,400,000  Arm: 1,920,000 | x86: redis.proxy.xu1.large.192  Arm: redis.proxy.au1.large.192 |
| 256 | 256 | 32 | 200,000/320,000 | 10,000/10,000 | x86: 3,200,000  Arm: 2,560,000 | x86: redis.proxy.xu1.large.256  Arm: redis.proxy.au1.large.256 |
| 384 | 384 | 48 | 200,000/480,000 | 10,000/10,000 | x86: 4,800,000  Arm: 3,840,000 | x86: redis.proxy.xu1.large.384  Arm: redis.proxy.au1.large.384 |
| 512 | 512 | 64 | 200,000/500,000 | 10,000/10,000 | x86: 6,400,000  Arm: 5,120,000 | x86: redis.proxy.xu1.large.512  Arm: redis.proxy.au1.large.512 |
| 768 | 768 | 96 | 200,000/500,000 | 10,000/10,000 | x86: 9,600,000  Arm: 7,680,000 | x86: redis.proxy.xu1.large.768  Arm: redis.proxy.au1.large.768 |
| 1024 | 1024 | 128 | 200,000/500,000 | 10,000/10,000 | x86: 12,800,000  Arm: 10,240,000 | x86: redis.proxy.xu1.large.1024  Arm: redis.proxy.au1.large.1024 |
| 2048 | 2048 | 128 | 200,000/500,000 | 10,000/10,000 | x86: 12,800,000  Arm: 10,240,000 | x86: redis.proxy.xu1.large.2048  Arm: redis.proxy.au1.large.2048 |
| 4096 | 4096 | 128 | 200,000/500,000 | 10,000/10,000 | x86: 12,800,000  Arm: 10,240,000 | x86: redis.proxy.xu1.large.4096  Arm: redis.proxy.au1.large.4096 |

[Table 4](#dcs-pd-0522002__table11841193594415) lists the number of shards corresponding to different specifications. You can customize the shard size when creating an instance. Currently, the number of replicas cannot be customized. By default, each shard has two replicas.

**Memory per shard=Instance specification/Number of shards**. For example, if a 48 GB instance has 6 shards, the size of each shard is 48 GB/6 = 8 GB.

| Total Memory | Proxies | Shards | Memory per Shard (GB) |
| --- | --- | --- | --- |
| 4 GB | 3 | 3 | 1.33 |
| 8 GB | 3 | 3 | 2.67 |
| 16 GB | 3 | 3 | 5.33 |
| 24 GB | 3 | 3 | 8 |
| 32 GB | 3 | 3 | 10.67 |
| 48 GB | 6 | 6 | 8 |
| 64 GB | 8 | 8 | 8 |
| 96 GB | 12 | 12 | 8 |
| 128 GB | 16 | 16 | 8 |
| 192 GB | 24 | 24 | 8 |
| 256 GB | 32 | 32 | 8 |
| 384 GB | 48 | 48 | 8 |
| 512 GB | 64 | 64 | 8 |
| 768 GB | 96 | 96 | 8 |
| 1024 GB | 128 | 128 | 8 |

#### Redis Cluster Instances

Redis Cluster instances can have 1 to 5 replicas. For example, the instance specifications can be **Redis Cluster | 1 replica** or **Redis Cluster | 5 replicas**. By default, a Redis Cluster instance has two replicas. A Redis Cluster instance with only 1 replica indicates that the replica quantity has been decreased.

Given the same memory size, the differences between x86-based Redis Cluster instances, Arm-based Redis Cluster instances, and Redis Cluster instances with different replicas are as follows:

- The available memory, shard quantity (master node quantity), maximum number of connections, and assured/maximum bandwidth are the same.
- Instance flavor code (specification name): [Table 5](#dcs-pd-0522002__table59612933110) only lists the specification names of x86- and Arm-based instances with 2 replicas. The specification names reflect the number of replicas, for example, redis.cluster.xu1.large.**r2**.24 (Redis Cluster | x86 | 2 replicas | 24 GB) and redis.cluster.xu1.large.**r3**.24 (Redis Cluster | x86 | 3 replicas | 24 GB).
- IP addresses: Number of occupied IP addresses = Number of shards × Number of replicas. For example:
	24 GB | Redis Cluster | 3 replicas: Number of occupied IP addresses = 3 x 3 = 9
- **Available memory per node** = Instance available memory/Master node quantity
	For example, a 24 GB x86-based instance has 24 GB available memory and 3 master nodes. The available memory per node is 24/3 = 8 GB.
- **Maximum connections limit per node** = Maximum connections limit/Master node quantity For example:
	For example, a 24 GB x86-based instance has 3 master nodes and the maximum connections limit is 150,000. The maximum connections limit per node = 150,000/3 = 50,000.

- The following table lists only the Redis Cluster instance specifications with default shards. If you customize shards, see the maximum number of connections, assured/maximum bandwidth, and product specification code (flavor) in the **Instance Specification** table the **Buy DCS Instance** page of the DCS console.
- The maximum connections of a cluster are for the entire instance, and not for a single shard. Maximum connections per shard = Maximum connections of an instance/Number of shards
- The maximum bandwidth and assured bandwidth of a cluster is for the entire instance, and not for a single shard. The relationship between the instance bandwidth and the bandwidth of a single shard is as follows:
	- Instance bandwidth = Bandwidth of a single shard x Number of shards
		- For a cluster instance, if the memory of a single shard is 1 GB, the bandwidth of a single shard is 384 Mbit/s. If the memory of a single shard is greater than 1 GB, the bandwidth of a single shard is 768 Mbit/s.

| Total Memory  (GB) | Available Memory  (GB) | Shards (Master Nodes) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- | --- |
| 4 | 4 | 3 | 30,000  /150,000 | 2304/2304 | x86: 300,000  Arm: 240,000 | x86: redis.cluster.xu1.large.r2.4  Arm: redis.cluster.au1.large.r2.4 |
| 8 | 8 | 3 | 30,000  /150,000 | 2304/2304 | x86: 300,000  Arm: 240,000 | x86: redis.cluster.xu1.large.r2.8  Arm: redis.cluster.au1.large.r2.8 |
| 16 | 16 | 3 | 30,000  /150,000 | 2304/2304 | x86: 300,000  Arm: 240,000 | x86: redis.cluster.xu1.large.r2.16  Arm: redis.cluster.au1.large.r2.16 |
| 24 | 24 | 3 | 30,000  /150,000 | 2304/2304 | x86: 300,000  Arm: 240,000 | x86: redis.cluster.xu1.large.r2.24  Arm: redis.cluster.au1.large.r2.24 |
| 32 | 32 | 3 | 30,000  /150,000 | 2304/2304 | x86: 300,000  Arm: 240,000 | x86: redis.cluster.xu1.large.r2.32  Arm: redis.cluster.au1.large.r2.32 |
| 48 | 48 | 6 | 60,000  /300,000 | 4608/4608 | x86: 600,000  Arm: 480,000 | x86: redis.cluster.xu1.large.r2.48  Arm: redis.cluster.au1.large.r2.48 |
| 64 | 64 | 8 | 80,000  /400,000 | 6144/6144 | x86: 800,000  Arm: 640,000 | x86: redis.cluster.xu1.large.r2.64  Arm: redis.cluster.au1.large.r2.64 |
| 96 | 96 | 12 | 120,000  /600,000 | 9216/9216 | x86: 1,200,000  Arm: 960,000 | x86: redis.cluster.xu1.large.r2.96  Arm: redis.cluster.au1.large.r2.96 |
| 128 | 128 | 16 | 160,000  /800,000 | 12,288/12,288 | x86: 1,600,000  Arm: 1,280,000 | x86: redis.cluster.xu1.large.r2.128  Arm: redis.cluster.au1.large.r2.128 |
| 192 | 192 | 24 | 240,000  /1,200,000 | 18,432/18,432 | x86: 2,400,000  Arm: 1,920,000 | x86: redis.cluster.xu1.large.r2.192  Arm: redis.cluster.au1.large.r2.192 |
| 256 | 256 | 32 | 320,000  /1,600,000 | 24,576/24,576 | x86: 3,200,000  Arm: 2,560,000 | x86: redis.cluster.xu1.large.r2.256  Arm: redis.cluster.au1.large.r2.256 |
| 384 | 384 | 48 | 480,000  /2,400,000 | 36,864/36,864 | x86: 4,800,000  Arm: 3,840,000 | x86: redis.cluster.xu1.large.r2.384  Arm: redis.cluster.au1.large.r2.384 |
| 512 | 512 | 64 | 640,000  /3,200,000 | 49,152/49,152 | x86: 6,400,000  Arm: 5,120,000 | x86: redis.cluster.xu1.large.r2.512  Arm: redis.cluster.au1.large.r2.512 |
| 768 | 768 | 96 | 960,000  /4,800,000 | 73,728/73,728 | x86: 9,600,000  Arm: 7,680,000 | x86: redis.cluster.xu1.large.r2.768  Arm: redis.cluster.au1.large.r2.768 |
| 1024 | 1024 | 128 | 1,280,000  /6,400,000 | 98,304/98,304 | x86: 12,800,000  Arm: 10,240,000 | x86: redis.cluster.xu1.large.r2.1024  Arm: redis.cluster.au1.large.r2.1024 |
| 2048 | 2048 | 128 | 1,280,000  /6,400,000 | 98,304/98,304 | x86: 12,800,000  Arm: 10,240,000 | x86: redis.cluster.xu1.large.r2.2048  Arm: redis.cluster.au1.large.r2.2048 |
|  |  |  |  |  |  |  |

[Table 6](#dcs-pd-0522002__table3552324111) lists the shard specifications for different instance specifications.

You can customize the shard size when creating a Redis Cluster instance. If the shard size is not customized, the default size is used. **Size of a shard = Instance specification/Number of shards**. For example, if a 48 GB instance has 6 shards, the size of each shard is 48 GB/6 = 8 GB.

| Total Memory | Shards |
| --- | --- |
| 4GB/8GB/16GB/24GB/32GB | 3 |
| 48 GB | 6 |
| 64 GB | 8 |
| 96 GB | 12 |
| 128 GB | 16 |
| 192 GB | 24 |
| 256 GB | 32 |
| 384 GB | 48 |
| 512 GB | 64 |
| 768 GB | 96 |
| 1024 GB | 128 |
| 2048GB | 128 |
| 4096GB | 128 |

#### Read/Write Splitting Instances

- [Table 7](#dcs-pd-0522002__table131411110371) describes the specifications of read/write splitting x86-based instances.
- The maximum connections of a read/write splitting DCS Redis instance cannot be modified.
- Bandwidth limit per Redis Server (MB/s) = Total bandwidth limit (MB/s)/Number of replicas (including masters)
- Reference performance (QPS) = Reference performance per node (QPS) × Number of replicas (including masters)
- When using read/write splitting instances, note the following:
	1. Read requests are sent to replicas. There is a delay when data is synchronized from the master to the replicas.
		If your services are sensitive to the delay, do not use read/write splitting instances. Instead, you can use master/standby or cluster instances.
		2. Read/Write splitting is suitable when there are more read requests than write requests. If there are a lot of write requests, the master and replicas may be disconnected, or the data synchronization between them may fail after the disconnection. As a result, the read performance deteriorates.
		If your services are write-heavy, use master/standby or cluster instances.
		3. If a replica is faulty, it takes some time to synchronize all data from the master. During the synchronization, the replica does not provide services, and the read performance of the instance deteriorates.
		It is best practice to use an instance with less than 32 GB of memory. The smaller the memory, the shorter the time required for full data synchronization between the master and replicas, and the smaller the impact of the interruption.

| Specification | Available Memory (GB) | Replicas (Including Masters) | Max. Connections (Default/Limit) | Bandwidth Limit (MB/s) | Bandwidth Limit per Redis Server (MB/s) | Reference Performance (QPS) | Reference Performance per Node (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 1 | 2 | 20,000 | 96 | 48 | 200,000 | 100,000 | redis.ha.xu1.large.p2.1 |
| 1 | 1 | 3 | 30,000 | 144 | 48 | 300,000 | 100,000 | redis.ha.xu1.large.p3.1 |
| 1 | 1 | 4 | 40,000 | 192 | 48 | 400,000 | 100,000 | redis.ha.xu1.large.p4.1 |
| 1 | 1 | 5 | 50,000 | 240 | 48 | 500,000 | 100,000 | redis.ha.xu1.large.p5.1 |
| 1 | 1 | 6 | 60,000 | 288 | 48 | 600,000 | 100,000 | redis.ha.xu1.large.p6.1 |
| 2 | 2 | 2 | 20,000 | 96 | 48 | 200,000 | 100,000 | redis.ha.xu1.large.p2.2 |
| 2 | 2 | 3 | 30,000 | 144 | 48 | 300,000 | 100,000 | redis.ha.xu1.large.p3.2 |
| 2 | 2 | 4 | 40,000 | 192 | 48 | 400,000 | 100,000 | redis.ha.xu1.large.p4.2 |
| 2 | 2 | 5 | 50,000 | 240 | 48 | 500,000 | 100,000 | redis.ha.xu1.large.p5.2 |
| 2 | 2 | 6 | 60,000 | 288 | 48 | 600,000 | 100,000 | redis.ha.xu1.large.p6.2 |
| 4 | 4 | 2 | 20,000 | 96 | 48 | 200,000 | 100,000 | redis.ha.xu1.large.p2.4 |
| 4 | 4 | 3 | 30,000 | 144 | 48 | 300,000 | 100,000 | redis.ha.xu1.large.p3.4 |
| 4 | 4 | 4 | 40,000 | 192 | 48 | 400,000 | 100,000 | redis.ha.xu1.large.p4.4 |
| 4 | 4 | 5 | 50,000 | 240 | 48 | 500,000 | 100,000 | redis.ha.xu1.large.p5.4 |
| 4 | 4 | 6 | 60,000 | 288 | 48 | 600,000 | 100,000 | redis.ha.xu1.large.p6.4 |
| 8 | 8 | 2 | 20,000 | 192 | 96 | 200,000 | 100,000 | redis.ha.xu1.large.p2.8 |
| 8 | 8 | 3 | 30,000 | 288 | 96 | 300,000 | 100,000 | redis.ha.xu1.large.p3.8 |
| 8 | 8 | 4 | 40,000 | 384 | 96 | 400,000 | 100,000 | redis.ha.xu1.large.p4.8 |
| 8 | 8 | 5 | 50,000 | 480 | 96 | 500,000 | 100,000 | redis.ha.xu1.large.p5.8 |
| 8 | 8 | 6 | 60,000 | 576 | 96 | 600,000 | 100,000 | redis.ha.xu1.large.p6.8 |
| 16 | 16 | 2 | 20,000 | 192 | 96 | 200,000 | 100,000 | redis.ha.xu1.large.p2.16 |
| 16 | 16 | 3 | 30,000 | 288 | 96 | 300,000 | 100,000 | redis.ha.xu1.large.p3.16 |
| 16 | 16 | 4 | 40,000 | 384 | 96 | 400,000 | 100,000 | redis.ha.xu1.large.p4.16 |
| 16 | 16 | 5 | 50,000 | 480 | 96 | 500,000 | 100,000 | redis.ha.xu1.large.p5.16 |
| 16 | 16 | 6 | 60,000 | 576 | 96 | 600,000 | 100,000 | redis.ha.xu1.large.p6.16 |
| 32 | 32 | 2 | 20,000 | 192 | 96 | 200,000 | 100,000 | redis.ha.xu1.large.p2.32 |
| 32 | 32 | 3 | 30,000 | 288 | 96 | 300,000 | 100,000 | redis.ha.xu1.large.p3.32 |
| 32 | 32 | 4 | 40,000 | 384 | 96 | 400,000 | 100,000 | redis.ha.xu1.large.p4.32 |
| 32 | 32 | 5 | 50,000 | 480 | 96 | 500,000 | 100,000 | redis.ha.xu1.large.p5.32 |
| 32 | 32 | 6 | 60,000 | 576 | 96 | 600,000 | 100,000 | redis.ha.xu1.large.p6.32 |
| 64 | 64 | 2 | 20,000 | 192 | 96 | 200,000 | 100,000 | redis.ha.xu1.large.p2.64 |
| 64 | 64 | 3 | 30,000 | 288 | 96 | 300,000 | 100,000 | redis.ha.xu1.large.p3.64 |
| 64 | 64 | 4 | 40,000 | 384 | 96 | 400,000 | 100,000 | redis.ha.xu1.large.p4.64 |
| 64 | 64 | 5 | 50,000 | 480 | 96 | 500,000 | 100,000 | redis.ha.xu1.large.p5.64 |
| 64 | 64 | 6 | 60,000 | 576 | 96 | 600,000 | 100,000 | redis.ha.xu1.large.p6.64 |

**Parent topic:** [DCS Instance Specifications](https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-200713003.html)