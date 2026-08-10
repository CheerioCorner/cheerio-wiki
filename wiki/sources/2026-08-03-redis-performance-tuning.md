---
title: "Redis Performance Tuning 最佳實踐"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-Performance Tuning Best Practices.md
    url: https://redis.io/faq/doc/1mebipyp1e/performance-tuning-best-practices
provenance_raw: "raw/web/2026-08-03-Performance Tuning Best Practices.md"
provenance_url: https://redis.io/faq/doc/1mebipyp1e/performance-tuning-best-practices
tags: [redis, performance, latency, optimization, troubleshooting]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-performance-tuning
---

> 來源：[Redis Performance Tuning Best Practices](https://redis.io/faq/doc/1mebipyp1e/performance-tuning-best-practices)
> 原始剪藏：[[raw/web/2026-08-03-Performance Tuning Best Practices.md|raw clip]]

## 一句話

Redis 效能調校核心：避免 Big/Hot Key、用 `UNLINK` 取代 `DEL`、啟用連線池與 Pipelining、確保無 Swap。

## 重點摘要

### 1. 命令最佳化

| 問題 | 解決方案 |
|------|----------|
| `KEYS` 掃描整個 keyspace | 避免使用，改用 `SCAN` |
| `HGETALL`/`MGET` 大量資料 | 降低 slowlog 閾值監控 |
| `ZRANGE` 全範圍查詢 | 限制 range，避免 `0 -1` |
| `LRANGE` 無界查詢 | 使用有界 range |

**Slowlog 設定**：
```bash
CONFIG SET slowlog-log-slower-than < microseconds >
```

### 2. Big Key / Hot Key

```bash
# 找出大 key
redis-cli --bigkeys

# 找出熱 key（僅非 CRDB）
redis-cli --hotkeys
```

- 大 key 會導致 CPU 100%，分片無法解決
- 限制 `ZRANGE`/`LRANGE` 範圍
- 時序資料改用其他資料結構

### 3. 刪除最佳化

```bash
# ❌ 同步刪除（阻塞）
DEL big_key

# ✅ 異步刪除（背景執行）
UNLINK big_key

# 批量刪除（不影響效能）
redis-cli -p <PORT> --scan --pattern "city:*" -i 0.01 | xargs redis-cli -p <PORT> UNLINK
```

### 4. 連線池（Connection Pooling）

- 每次請求都開新連線會導致延遲和不穩定
- 使用連線池：少數連線服務所有請求
- Redis Software 閒置連線 5 分鐘自動關閉
- 支援連線池的客戶端：`redis-py`、`jedis`

### 5. Pipelining

- 批次打包多個命令，減少 RTT（Round-Trip Time）
- 減少 socket read 系統呼叫

### 6. 擴展性

| 策略 | 做法 |
|------|------|
| Scale-up | 重新分片，利用多核心 |
| Scale-out | 跨節點分散 shards |
| DMC Proxy | `all-master-shards` 策略讓 proxy 也擴展 |

### 7. 資源監控

- **Swap**：Linux 可能在記憶體充足時仍 swap，導致延遲
- **Disk I/O**：AOF 持久化可能導致寫入延遲
- **記憶體淘汰**：大量 key 淘汰會造成延遲尖峰

### 8. 網路延遲

- Master shard 與 endpoint 在同一節點最佳
- Client 與 DB 在同一 VPC 效果更好

## 相關頁面

- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization]] — 記憶體優化策略
- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy]] — 記憶體滿時的淘汰策略
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session、Rate Limiting
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
