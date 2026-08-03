---
title: "Redis 6.0 Enterprise Edition (Discontinued)_DCS Instance Specifications_Service Overview_Distributed Cache Service"
description: "This section describes DCS Redis 6.0 enterprise edition instance specifications, including the total memory, available memory, maximum number of connections allowed, maxi"
source_url: "https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-210209001.html"
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
# Redis 6.0 Enterprise Edition (Discontinued)_DCS Instance Specifications_Service Overview_Distributed Cache Service

> Source: [Redis 6.0 Enterprise Edition (Discontinued)_DCS Instance Specifications_Service Overview_Distributed Cache Service](https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-210209001.html)
> Clipped: 2026-08-03

*On this page*

*Show all*

0/0

Copied.

## Redis 6.0 Enterprise Edition (Discontinued)

This section describes DCS Redis 6.0 enterprise edition instance specifications, including the total memory, available memory, maximum number of connections allowed, maximum/assured bandwidth, and reference performance.

DCS Redis 6.0 enterprise edition is available with performance and storage types. The instance metrics are as follows:

- Used memory: You can check the memory usage of an instance by viewing the **Memory Usage** and **Used Memory** metrics.
- Maximum connections: The maximum number of connections allowed is the maximum number of clients that can be connected to an instance. To check the number of connections to an instance, view the **Connected Clients** metric. After an instance is created, you can change the maximum number of connections of the instance by modifying the **maxclients** parameter on the **Instance Configuration** > **Parameters** page on the console.
- QPS represents queries per second, which is the number of commands processed per second. For details about QPS testing, see the [Performance White Paper](https://support.huaweicloud.com/intl/en-us/usermanual-dcs/dcs_03_0009.html).
- Bandwidth: You can view the **Flow Control Times** metric to check whether the bandwidth has exceeded the limit. You can also check the **Bandwidth Usage** metric. This metric is for reference only, because it may be higher than 100%. For details, see [Why Does Bandwidth Usage Exceed 100%?](https://support.huaweicloud.com/intl/en-us/dcs_faq/dcs-faq-0513001.html)
NOTE:

Enterprise edition DCS is no longer provided. Existing instances can be used.

#### Video Tutorial

This video shows enterprise edition DCS instances.

<video controls="controls" src="https://res-video.hc-cdn.com/cloudbu-site/intl/en-us/support/dcs-video/1729480564440330869.mp4" title="Professional Edition DCS" height="335.0000" width="600.0000"></video>

#### Enterprise (Performance) Edition

Currently, DCS for Redis 6.0 enterprise (performance) edition supports master/standby instances based on x86 CPUs.

| Total Memory  (GB) | Available Memory  (GB) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- |
| 8 | 8 | 10,000/50,000 | 1,536/1,536 | 400,000 | redis.ha.xu1.large.enthp.8 |
| 16 | 16 | 10,000/50,000 | 1,536/1,536 | 400,000 | redis.ha.xu1.large.enthp.16 |
| 32 | 32 | 10,000/50,000 | 1,536/1,536 | 400,000 | redis.ha.xu1.large.enthp.32 |
| 64 | 64 | 10,000/50,000 | 1,536/1,536 | 400,000 | redis.ha.xu1.large.enthp.64 |

#### Enterprise (Storage) Edition

Currently, DCS for Redis 6.0 enterprise (storage) edition supports master/standby instances based on x86 CPUs.

Enterprise (storage) instances use memory and SSDs. They use memory to cache hot data and SSDs to store all data. "Available Memory" in the following table is the disk capacity.

| Total Memory  (GB) | Maximum Storage  (GB) | Max. Connections (Default/Limit)  (Count) | Assured/Maximum Bandwidth  (Mbit/s) | Reference Performance  (QPS) | Specification Code (spec\_code in the API) |
| --- | --- | --- | --- | --- | --- |
| 8 | 64 | 10,000/50,000 | 768/768 | 70,000 | redis.ha.xu1.large.entst.8 |
| 16 | 128 | 10,000/50,000 | 768/768 | 70,000 | redis.ha.xu1.large.entst.16 |
| 32 | 256 | 10,000/50,000 | 768/768 | 70,000 | redis.ha.xu1.large.entst.32 |

**Parent topic:** [DCS Instance Specifications](https://support.huaweicloud.com/intl/en-us/productdesc-dcs/dcs-pd-200713003.html)