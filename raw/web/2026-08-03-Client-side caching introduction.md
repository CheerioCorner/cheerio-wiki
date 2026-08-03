---
title: "Client-side caching introduction"
description: "Server-assisted, client-side caching in Redis"
source_url: "https://redis.io/docs/latest/develop/clients/client-side-caching/"
source_domain: "redis.io"
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
# Client-side caching introduction

> Source: [Client-side caching introduction](https://redis.io/docs/latest/develop/clients/client-side-caching/)
> Clipped: 2026-08-03

*Client-side caching* reduces network traffic between a Redis client and the server, which generally improves performance.

By default, an [application server](https://en.wikipedia.org/wiki/Application_server) (which sits between the user app and the database) contacts the Redis database server through the client library for every read request. The diagram below shows the flow of communication from the user app, through the application server to the database and back again:

[![](https://redis.io/docs/latest/images/csc/CSCNoCache.drawio.svg)](https://redis.io/docs/latest/images/csc/CSCNoCache.drawio.svg)

When you use client-side caching, the client library maintains a local cache of data items as it retrieves them from the database. When the same items are needed again, the client can satisfy the read requests from the cache instead of the database:

[![](https://redis.io/docs/latest/images/csc/CSCWithCache.drawio.svg)](https://redis.io/docs/latest/images/csc/CSCWithCache.drawio.svg)

Accessing the cache is much faster than communicating with the database over the network and it reduces network traffic. Client-side caching reduces the load on the database server, so you may be able to run it using less hardware resources.

As with other forms of [caching](https://en.wikipedia.org/wiki/Cache_\(computing\)), client-side caching works well in the very common use case where a small subset of the data is accessed much more frequently than the rest of the data (according to the [Pareto principle](https://en.wikipedia.org/wiki/Pareto_principle)).

## Updating the cache when the data changes

All caching systems must implement a scheme to update data in the cache when the corresponding data changes in the main database. Redis uses an approach called *tracking*.

When client-side caching is enabled, the Redis server remembers or *tracks* the set of keys that each client connection has previously read. This includes cases where the client reads data directly, as with the [`GET`](https://redis.io/docs/latest/commands/get/) command, and also where the server calculates values from the stored data, as with [`STRLEN`](https://redis.io/docs/latest/commands/strlen/). When any client writes new data to a tracked key, the server sends an invalidation message to all clients that have accessed that key previously. This message warns the clients that their cached copies of the data are no longer valid and the clients will evict the stale data in response. Next time a client reads from the same key, it will access the database directly and refresh its cache with the updated data.

The sequence diagram below shows how two clients might interact as they access and update the same key:

[![](https://redis.io/docs/latest/images/csc/CSCSeqDiagram.drawio.svg)](https://redis.io/docs/latest/images/csc/CSCSeqDiagram.drawio.svg)

## Which client libraries support client-side caching?

The following client libraries support CSC from the stated version onwards:

| Client | Version |
| --- | --- |
| [`redis-py`](https://redis.io/docs/latest/develop/clients/redis-py/connect/#connect-using-client-side-caching) | v5.1.0 |
| [`Jedis`](https://redis.io/docs/latest/develop/clients/jedis/connect/#connect-using-client-side-caching) | v5.2.0 |
| [`node-redis`](https://redis.io/docs/latest/develop/clients/nodejs/connect/#connect-using-client-side-caching) | v5.1.0 |

Note that some other clients support the [`CLIENT TRACKING`](https://redis.io/docs/latest/commands/client-tracking/) command to configure CSC on the server, but this does not mean they support the features required for CSC themselves.

## Which commands can cache data?

All read-only commands (with the `@read` [ACL category](https://redis.io/docs/latest/operate/oss_and_stack/management/security/acl/)) will use cached data, except for the following:

- Any commands for the [probabilistic](https://redis.io/docs/latest/develop/data-types/probabilistic/) and [time series](https://redis.io/docs/latest/develop/data-types/timeseries/) data types. These types are designed to be updated frequently, which means that caching has little or no benefit.
- Non-deterministic commands such as [`HRANDFIELD`](https://redis.io/docs/latest/commands/hrandfield/), [`HSCAN`](https://redis.io/docs/latest/commands/hscan/), and [`ZRANDMEMBER`](https://redis.io/docs/latest/commands/zrandmember/). By design, these commands give different results each time they are called.
- Redis Search commands (with the `FT.*` prefix), such as [`FT.SEARCH`](https://redis.io/docs/latest/commands/ft.search/).

You can use the [`MONITOR`](https://redis.io/docs/latest/commands/monitor/) command to check the server's behavior when you are using client-side caching. Because `MONITOR` only reports activity from the server, you should find the first cacheable access to a key causes a response from the server. However, subsequent accesses are satisfied by the cache, and so `MONITOR` should report no server activity if client-side caching is working correctly.

## What data gets cached for a command?

Broadly speaking, the data from the specific response to a command invocation gets cached after it is used for the first time. Subsets of that data or values calculated from it are retrieved from the server as usual and then cached separately. For example:

- The whole string retrieved by [`GET`](https://redis.io/docs/latest/commands/get/) is added to the cache. Parts of the same string retrieved by [`SUBSTR`](https://redis.io/docs/latest/commands/substr/) are calculated on the server the first time and then cached separately from the original string.
- Using [`GETBIT`](https://redis.io/docs/latest/commands/getbit/) or [`BITFIELD`](https://redis.io/docs/latest/commands/bitfield/) on a string caches the returned values separately from the original string.
- For composite data types accessed by keys ([hash](https://redis.io/docs/latest/develop/data-types/hashes/), [JSON](https://redis.io/docs/latest/develop/data-types/json/), [set](https://redis.io/docs/latest/develop/data-types/sets/), and [sorted set](https://redis.io/docs/latest/develop/data-types/sorted-sets/)), the whole object is cached separately from the individual fields. So the results of `JSON.GET mykey $` and `JSON.GET mykey $.myfield` create separate entries in the cache.
- Ranges from [lists](https://redis.io/docs/latest/develop/data-types/lists/), [streams](https://redis.io/docs/latest/develop/data-types/streams/), and [sorted sets](https://redis.io/docs/latest/develop/data-types/sorted-sets/) are cached separately from the object they form a part of. Likewise, subsets returned by [`SINTER`](https://redis.io/docs/latest/commands/sinter/) and [`SDIFF`](https://redis.io/docs/latest/commands/sdiff/) create separate cache entries.
- For multi-key read commands such as [`MGET`](https://redis.io/docs/latest/commands/mget/), the ordering of the keys is significant. For example `MGET name:1 name:2` is cached separately from `MGET name:2 name:1` because the server returns the values in the order you specify.
- Boolean or numeric values calculated from data types (for example [`SISMEMBER`](https://redis.io/docs/latest/commands/sismember/)) and [`LLEN`](https://redis.io/docs/latest/commands/llen/) are cached separately from the object they refer to.

## Usage recommendations

Like any caching system, client-side caching has some limitations:

- The cache has only a limited amount of memory available. When the limit is reached, the client must *evict* potentially useful items from the cache to make room for new ones.
- Cache misses, tracking, and invalidation messages always add a slight performance penalty.

Below are some guidelines to help you use client-side caching efficiently, within these limitations:

- **Use a separate connection for data that is not cache-friendly**: Caching gives the most benefit for keys that are read frequently and updated infrequently. However, you may also have data, such as counters and scoreboards, that receives frequent updates. In cases like this, the performance overhead of the invalidation messages can be greater than the savings made by caching. Avoid this problem by using a separate connection *without* client-side caching for any data that is not cache-friendly.
- **Estimate how many items you can cache**: The client libraries let you specify the maximum number of items you want to hold in the cache. You can calculate an estimate for this number by dividing the maximum desired size of the cache in memory by the average size of the items you want to store (use the [`MEMORY USAGE`](https://redis.io/docs/latest/commands/memory-usage/) command to get the memory footprint of a key). For example, if you had 10MB (or 10485760 bytes) available for the cache, and the average size of an item was 80 bytes, you could fit approximately 10485760 / 80 = 131072 items in the cache. Monitor memory usage on your server with a realistic test load to adjust your estimate up or down.
	## Reference
	The Redis server implements extra features for client-side caching that are not used by the main Redis clients, but may be useful for custom clients and other advanced applications. See [Client-side caching reference](https://redis.io/docs/latest/develop/reference/client-side-caching/) for a full technical guide to all the options available for client-side caching.