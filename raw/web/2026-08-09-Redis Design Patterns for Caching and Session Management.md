---
source: https://medium.com/@artemkhrenov/key-value-store-patterns-redis-design-patterns-for-caching-and-session-management-418d91148701
author: Artem Khrienov
date: 2026-03-05
fetched: 2026-08-09
---

# Key-Value Store Patterns: Redis Design Patterns for Caching and Session Management

How to actually use Redis beyond just `SET` and `GET`, real patterns that scale to millions of users.

## What Redis Actually Is (And Why It Is So Fast)

Redis keeps all data in RAM. A typical database query involves disk I/O, which is measured in milliseconds. A Redis operation is measured in microseconds, often under 100 microseconds. That is roughly 100 to 1000 times faster. On top of that, Redis uses a single-threaded event loop for command processing, which means no lock contention between operations. It also uses non-blocking I/O, so while one client is waiting for a network response, Redis is already processing other commands.

Redis is incredible for reads and writes that fit in RAM. It is not the right tool for complex joins, full-text search across millions of documents, or storing terabytes of data.

## Redis Data Structures: Pick the Right Tool

### Strings: The Workhorse
- Most basic type, can store text, integers, floats, or serialized JSON
- Maximum size of 512MB, binary-safe
- `INCR` command is atomic - perfect for counters

### Hashes: Perfect for Objects
- Store and retrieve individual fields without deserializing the whole object
- More bandwidth efficient than JSON strings
- Use `HGET` instead of loading entire JSON

### Lists: Queues and Activity Feeds
- Doubly linked lists, O(1) for head/tail operations
- Excellent for message queues, activity feeds, time-ordered data
- `LPUSH`/`RPUSH` for adding, `LRANGE` for fetching slices

### Sets: Unique Membership Tracking
- Store unique strings with no duplicates
- `SADD`, `SISMEMBER`, `SMEMBERS`, `SINTER` (intersection), `SUNION`
- Perfect for "which users have seen this notification"

### Sorted Sets: Leaderboards and Rate Limiting
- Like Sets but each member has a numeric score
- Members always sorted by score
- Behind leaderboards, priority queues, rate limiting windows

## Caching Patterns That Actually Work

### Pattern 1: Cache-Aside (Lazy Loading)
- Most common pattern, application manages the cache
- On cache miss: load from database, write to cache
- Cache never talks to database directly
- Weakness: "thundering herd" / "cache stampede" problem
- Solution: mutex lock / request coalescing

### Pattern 2: Write-Through
- Every write goes to both cache and database synchronously
- Cache always up to date
- Ideal when data is read much more frequently than written
- Trade-off: writes are slightly slower

### Pattern 3: Write-Behind (Write-Back)
- Write to Redis immediately, sync to database asynchronously
- Maximizes write speed but adds complexity
- Risk: data loss if Redis crashes before flush
- Use only for data where some loss is acceptable (analytics, events)

## Session Management: The Right Way
- Use Hashes for efficient partial updates
- Idle TTL (30 min) + Absolute TTL (24 hours)
- Cryptographically secure session IDs (crypto.randomBytes)
- Session rotation for privilege escalation
- Track all sessions per user for bulk invalidation

## Rate Limiting with Redis
- Sliding window rate limiter using Sorted Sets
- More accurate than fixed-window approach
- Pipeline for atomic execution
- Standard X-RateLimit headers

## Best Practices

### Key Naming Conventions
Pattern: `[environment:]entity:identifier[:attribute]`
- Hierarchical, descriptive, environment-prefixed
- Avoids collisions, enables prefix scanning

### Always Use Pipelines for Bulk Operations
- Multiple round trips vs single pipeline execution
- `exec()` returns array of [error, result] pairs

### Memory Management
- Monitor with `INFO memory`
- Every key should have TTL unless deliberate reason not to
- Know your eviction policy (noeviction vs allkeys-lru)

### Graceful Degradation
- Circuit breaker pattern for Redis failures
- Application should still work (just slower) if Redis is unavailable
- Treat Redis as performance optimization, not data store

## Common Mistakes to Avoid
1. Not setting TTLs
2. Storing too much in one key
3. Using `KEYS` in production (use `SCAN` instead)
4. Ignoring eviction policies
5. Trusting the cache too much - database is source of truth

## Conclusion
- Use Strings for simple values and counters
- Use Hashes for objects with partial updates
- Use Lists for queues and feeds
- Use Sets for membership tracking
- Use Sorted Sets for leaderboards and time-windowed rate limiting
- Default to Cache-Aside for most things
- Use Write-Through for strong read consistency
- Use Write-Behind only for high-frequency writes where some loss is acceptable
