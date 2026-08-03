---
title: "Redis Design Patterns for Caching and Session Management"
type: source
created: 2026-08-09
updated: 2026-08-09
sources: 1
tags: [redis, caching, backend, session-management, rate-limiting]
topics: [backend-systems]
provenance:
  - kind: raw
    path: raw/web/2026-08-09-Redis Design Patterns for Caching and Session Management.md
    url: https://medium.com/@artemkhrenov/key-value-store-patterns-redis-design-patterns-for-caching-and-session-management-418d91148701
---

# Redis Design Patterns for Caching and Session Management

> 來源：Artem Khrienov (2026-03-05) — Medium 26 分鐘長文，涵蓋 Redis 數據結構、緩存模式、Session 管理、Rate Limiting 與最佳實踐。

---

## Redis 為什麼快

- 資料全在 RAM，操作是 μs 級（比 DB ms 級快 100-1000 倍）
- 單執行緒 event loop，無鎖競爭
- 非阻塞 I/O

**限制**：不適合 complex joins、全文搜尋、TB 級資料

---

## 五種核心數據結構

| 結構 | 用途 | 關鍵操作 |
|------|------|---------|
| **Strings** | 簡單值、計數器、JSON 序列化 | `SET`/`GET`/`INCR`（原子計數） |
| **Hashes** | 物件、Session（部分欄位更新） | `HSET`/`HGET`/`HGETALL` |
| **Lists** | 佇列、Activity Feed | `LPUSH`/`RPUSH`/`LRANGE`/`BLPOP` |
| **Sets** | 唯一性追蹤（已讀通知、標籤） | `SADD`/`SISMEMBER`/`SINTER` |
| **Sorted Sets** | 排行榜、Rate Limiting | `ZADD`/`ZREVRANGE`/`ZREVRANK` |

**關鍵洞見**：Hashes 比 JSON 字串更省頻寬——20 個欄位只取 1 個時用 `HGET` 而非 `GET` + parse。

---

## 四種緩存模式

### 1. Cache-Aside（Lazy Loading）⭐ 最常用
- 應用程式管理快取，cache miss 時從 DB 載入
- **弱點**：Cache Stampede（熱 key 過期時大量請求同時打 DB）
- **解法**：Mutex lock / request coalescing

### 2. Write-Through
- 每次寫入同步更新 cache + DB
- 適合：讀取頻率 >> 寫入頻率，且不能容忍 stale data
- **權衡**：寫入較慢

### 3. Write-Behind（Write-Back）
- 先寫 cache，背景非同步同步 DB
- 適合：高頻寫入（analytics、events）
- **風險**：Redis crash 時可能丟資料

### 4. Write-Around
- 寫入直接進 DB，繞過 cache
- 適合：寫入後很少立即讀取的資料

---

## Session Management 實作要點

- 用 **Hashes** 儲存（支援部分欄位更新）
- **雙重 TTL**：Idle TTL（30 分鐘無活動）+ Absolute TTL（24 小時硬限制）
- Session ID 用 `crypto.randomBytes(32)`（256-bit 熵）
- 權限提升時做 **Session Rotation**（防 Session Fixation）
- 追蹤每個 user 的所有 sessions，支援「登出所有裝置」

---

## Rate Limiting

- 用 Sorted Sets 實作 **滑動視窗**（比固定視窗更精準）
- Pipeline 保證原子性
- 標準 `X-RateLimit-*` 回應標頭

---

## 最佳實踐

### Key 命名
`[environment:]entity:identifier[:attribute]`
- 分層、具描述性、環境前綴
- 範例：`user:12345:profile`、`session:abc123`

### Pipeline 批次操作
- 5 個單獨 round trip → 1 個 pipeline round trip
- `exec()` 回傳 `[error, result]` 陣列

### Memory 管理
- 每個 key 都要有 TTL（除非有明確理由）
- 監控 `INFO memory`，注意 eviction policy
- 預設 `noeviction` 會回傳 error，cache 場景用 `allkeys-lru`

### Graceful Degradation
- Circuit breaker 模式：Redis 掛了 → fallback 到 DB
- Redis 是效能優化，不是資料庫

---

## 常見錯誤

1. ❌ 不設 TTL → memory 膨脹
2. ❌ 單一 key 存 50MB JSON → blocking event loop
3. ❌ 生產環境用 `KEYS *` → 用 `SCAN`
4. ❌ 忽略 eviction policy
5. ❌ 過度信任 cache → DB 才是 source of truth

---

## 相關頁面

- [[wiki/concepts/defect-metrics|defect-metrics]] — 軟體品質指標（同屬 backend engineering）
