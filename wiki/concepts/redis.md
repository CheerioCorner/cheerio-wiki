---
title: Redis
type: concept
created: 2026-08-10
updated: 2026-08-10
tags: [redis, caching, backend, in-memory-database]
topics: [backend-systems]
canonical: concepts/redis
---

# Redis

> In-memory key-value 資料庫，μs 級延遲，適合快取、Session、即時排行、Rate Limiting。

## 為什麼 Redis 快

| 因素 | 說明 |
|------|------|
| 資料在 RAM | 操作是 μs 級（比 DB ms 級快 100-1000 倍） |
| 單執行緒 event loop | 無鎖競爭，操作原子性保證 |
| 非阻塞 I/O | 高併發處理能力 |

**限制**：不適合 complex joins、全文搜尋、TB 級資料。

---

## 五種核心數據結構

| 結構 | 用途 | 關鍵操作 | 範例 |
|------|------|---------|------|
| **Strings** | 簡單值、計數器、JSON | `SET`/`GET`/`INCR` | Session Token、頁面瀏覽計數 |
| **Hashes** | 物件、Session | `HSET`/`HGET`/`HGETALL` | 使用者資料、產品資訊 |
| **Lists** | 佇列、Activity Feed | `LPUSH`/`RPUSH`/`LRANGE`/`BLPOP` | 訊息佇列、最新動態 |
| **Sets** | 唯一性追蹤 | `SADD`/`SISMEMBER`/`SINTER` | 已讀通知、標籤系統 |
| **Sorted Sets** | 排行榜、Rate Limiting | `ZADD`/`ZREVRANGE`/`ZREVRANK` | 遊戲排行、限流計數器 |

**關鍵洞見**：Hashes 比 JSON 字串更省頻寬——20 個欄位只取 1 個時用 `HGET` 而非 `GET` + parse。

---

## 記憶體優化

### 特殊編碼（5-10 倍節省）

小型 Hash/List/Sorted Set 使用 listpack 緊湊編碼：

```redis
hash-max-listpack-entries 512
hash-max-listpack-value 64
zset-max-listpack-entries 128
set-max-intset-entries 512
```

### Hash Key 拆分技巧

將 key 拆分為「key + field」，每個 Hash 約 100 個 field：

```redis
# key: "object:1234" → key: "object:12", field: "34"
HSET object:12 34 somevalue
```

**實測**：10 萬個物件，獨立 key 11 MB → Hash 優化 1.7 MB（省 6.4 倍）

### Bitmap 節省空間

1 億使用者訂閱狀態 = **12 MB**

```redis
SETBIT user:subscriptions:102393 1 1  # 訂閱
SETBIT user:subscriptions:102393 1 0  # 取消訂閱
```

---

## 四種快取模式

| 模式 | 寫入 | 讀取 | 適用場景 | 風險 |
|------|------|------|---------|------|
| **Cache-Aside** | 先寫 DB，失效 cache | 先讀 cache，miss 時讀 DB | 最常用 | Cache Stampede |
| **Write-Through** | 同步寫 cache + DB | 讀 cache | 不能容忍 stale data | 寫入較慢 |
| **Write-Behind** | 先寫 cache，背景寫 DB | 讀 cache | 高頻寫入 | Redis crash 丟資料 |
| **Read-Through** | 由 cache 代理讀 DB | 讀 cache | 簡化應用邏輯 | — |

---

## Eviction Policy（記憶體滿時）

| Policy | 範圍 | 淘汰邏輯 |
|--------|------|----------|
| `noeviction` | — | 不淘汰，寫入回傳錯誤 |
| `allkeys-lru` | 所有 key | 最久未使用 |
| `allkeys-lfu` | 所有 key | 最不常使用 |
| `volatile-lru` | 有 TTL 的 key | 最久未使用（**預設**） |
| `volatile-ttl` | 有 TTL 的 key | TTL 最短先淘汰 |

---

## 效能調校要點

### ❌ 避免
- `KEYS` 掃描整個 keyspace
- `HGETALL`/`ZRANGE 0 -1` 全範圍查詢
- 同步 `DEL` 刪除大 key

### ✅ 建議
- 用 `SCAN` 取代 `KEYS`
- 限制 `ZRANGE`/`LRANGE` 範圍
- 用 `UNLINK` 異步刪除大 key
- 啟用連線池（Connection Pooling）
- 使用 Pipelining 減少 RTT
- 設定 `maxmemory` + `maxmemory-policy`

```bash
# 找大 key
redis-cli --bigkeys

# 找熱 key
redis-cli --hotkeys
```

---

## 相關頁面

### Sources
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns for Caching and Session Management]] — 快取模式、Session、Rate Limiting 🛠️
- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 叢架架構、Sharding、HA 99.999% 🛠️
- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy 完整指南]] — 10 種淘汰策略 🛠️
- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization 策略]] — 記憶體優化技巧 🛠️
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning 最佳實踐]] — 效能調校要點 🛠️
- [[wiki/sources/2026-08-03-redis-security-practices|Redis Security Best Practices]] — 部署安全、叢集安全、資料庫安全 🛠️
- [[wiki/sources/2026-08-03-redis-acl|Redis ACL 完整指南]] — 存取控制列表、權限管理 🛠️
- [[wiki/sources/2026-08-03-redis-sentinel-client-spec|Redis Sentinel Client Spec]] — 客戶端整合規範、服務發現 🛠️

### Topics
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁

### Concepts
- [[wiki/concepts/defect-metrics|Defect Metrics]] — 快取命中率可作為品質指標
