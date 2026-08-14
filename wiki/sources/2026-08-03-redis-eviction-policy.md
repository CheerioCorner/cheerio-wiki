---
title: "Redis Eviction Policy 完整指南"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-eviction-policy.md
    url: https://redis.io/docs/latest/operate/rs/databases/memory-performance/eviction-policy/
provenance_raw: "raw/web/2026-08-03-eviction-policy.md"
provenance_url: https://redis.io/docs/latest/operate/rs/databases/memory-performance/eviction-policy/
tags: [redis, eviction, memory, lru, lfu]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-eviction-policy
---

> 來源：[Redis Eviction Policy](https://redis.io/docs/latest/operate/rs/databases/memory-performance/eviction-policy/)
> 原始剪藏：[[raw/web/2026-08-03-eviction-policy.md|raw clip]]

## 一句話

Eviction Policy 決定 Redis 記憶體滿時如何淘汰舊資料，預設為 `volatile-lru`（只淘汰有 TTL 的 least recently used keys）。

## 重點摘要

### 核心概念

- 當資料庫達到記憶體上限時，根據 policy 淘汰（evict）舊資料
- 叢集資料庫中，每個 shard 獨立追蹤記憶體使用量
- 若資料分佈不均，某些 shard 可能在整體未滿時就開始淘汰

### 10 種 Eviction Policy

| Policy | 範圍 | 淘汰邏輯 |
|--------|------|----------|
| **noeviction** | — | 不淘汰，寫入時回傳錯誤 |
| **allkeys-lru** | 所有 key | 最久未使用（Least Recently Used） |
| **allkeys-lfu** | 所有 key | 最不常使用（Least Frequently Used） |
| **allkeys-lrm** | 所有 key | 最久未修改（Least Recently Modified） |
| **allkeys-random** | 所有 key | 隨機淘汰 |
| **volatile-lru** | 有 TTL 的 key | 最久未使用 |
| **volatile-lfu** | 有 TTL 的 key | 最不常使用 |
| **volatile-lrm** | 有 TTL 的 key | 最久未修改 |
| **volatile-random** | 有 TTL 的 key | 隨機淘汰 |
| **volatile-ttl** | 有 TTL 的 key | TTL 最短的先淘汰 |

### 預設值

- **一般資料庫**：`volatile-lru`
- **Active-Active 資料庫**：`noeviction`

### Active-Active 特殊行為

- 淘汰在 shard 達到 **80%** 記憶體上限時就開始（需預留傳播時間）
- 網路問題時，所有 key 可能被全部淘汰

### 避免資料淘汰

- 確保資料庫夠大
- 使用 **Auto Tiering**：熱資料放 RAM，冷資料放 SSD

## 相關頁面

- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session、Rate Limiting
- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization]] — 記憶體優化策略
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
