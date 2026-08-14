---
title: "Redis Memory Optimization 策略"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-memory-optimization.md
    url: https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/
provenance_raw: "raw/web/2026-08-03-memory-optimization.md"
provenance_url: https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/
tags: [redis, memory, optimization, hash, encoding]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-memory-optimization
---

> 來源：[Redis Memory Optimization](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/)
> 原始剪藏：[[raw/web/2026-08-03-memory-optimization.md|raw clip]]

## 一句話

Redis 對小型聚合資料型別使用特殊編碼（listpack/intset），可節省 **5-10 倍**記憶體；用 Hash 取代多個獨立 key 可再省 6 倍。

## 重點摘要

### 1. 小型聚合型別的特殊編碼

Hash、List、Set（僅整數）、Sorted Set 在元素少於閾值時，使用緊湊編碼（listpack/intset），記憶體使用量減少 **5-10 倍**。

| 設定 | Redis ≤ 6.2 | Redis ≥ 7.0 |
|------|-------------|-------------|
| hash-max-entries | 512 | 512（listpack） |
| hash-max-value | 64 bytes | 64 bytes |
| zset-max-entries | 128 | 128（listpack） |
| set-max-intset-entries | 512 | 512 |

### 2. 用 Hash 取代多個獨立 Key

**關鍵洞見**：多個小 key 比一個包含多個 field 的 Hash 更浪費記憶體。

```
# ❌ 低效：3 個獨立 key
SET user:1234:name "Alice"
SET user:1234:email "alice@example.com"
SET user:1234:age "30"

# ✅ 高效：1 個 Hash
HSET user:1234 name "Alice" email "alice@example.com" age "30"
```

**實測數據**：10 萬個物件
- 獨立 key：11 MB
- Hash 優化：1.7 MB（**省 6.4 倍**）

### 3. Hash Key 拆分技巧

將 key 拆分為「key + field」，每個 Hash 約 100 個 field：

```
# key: "object:1234" → key: "object:12", field: "34"
HSET object:12 34 somevalue
```

### 4. Bit/Byte 操作

用 Bitmap 儲存大量布林值（如使用者訂閱狀態）：

- 1 億使用者的訂閱狀態 = **12 MB**
- 命令：`SETBIT`/`GETBIT`/`SETRANGE`/`GETRANGE`

### 5. 記憶體配置注意事項

- Redis 不一定會在 key 刪除後立即釋放記憶體給 OS（malloc 行為）
- 需根據 **峰值使用量** 配置記憶體
- 建議設定 `maxmemory` + `maxmemory-policy noeviction`

## 相關頁面

- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy]] — 記憶體滿時的淘汰策略
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session、Rate Limiting
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning]] — 效能調校最佳實踐
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
