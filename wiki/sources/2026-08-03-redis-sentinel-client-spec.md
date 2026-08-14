---
title: "Redis Sentinel Client Spec — 客戶端整合規範"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-sentinel-client-spec.md
    url: https://redis.io/docs/latest/develop/reference/sentinel-clients/
provenance_raw: "raw/web/2026-08-03-sentinel-client-spec.md"
provenance_url: https://redis.io/docs/latest/develop/reference/sentinel-clients/
tags: [redis, sentinel, failover, service-discovery, client]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-sentinel-client-spec
---

> 來源：[Redis Sentinel Client Spec](https://redis.io/docs/latest/develop/reference/sentinel-clients/)
> 原始剪藏：[[raw/web/2026-08-03-sentinel-client-spec.md|raw clip]]

## 一句話

Redis Sentinel 客戶端需實作服務發現（自動找到 master）和自動 failover 處理，確保 master 地址變更時客戶端能自動重新連接。

## 重點摘要

### Sentinel 是什麼

- Redis 的**監控方案**，處理自動 failover 和服務發現
- 每個 master 用名稱標識（如 "stats"、"cache"）
- 名稱代表一組實例（master + 多個 replicas）

### 客戶端需要 Sentinel 的原因

| 場景 | 說明 |
|------|------|
| **自動 failover** | Master 宕機時，replica 自動提升 |
| **手動 failover** | 升級 Redis 實例時 |
| **地址變更** | Master IP/Port 可能改變 |

### 服務發現流程

```
1. 連接第一個 Sentinel（短超時，數百 ms）
         ↓
2. 呼叫 SENTINEL get-master-addr-by-name <name>
         ↓
3. 取得 ip:port，連接 Redis master
         ↓
4. 呼叫 ROLE 命令確認是 master
         ↓
5. 確認成功，開始使用
```

### 關鍵命令

```bash
# 取得 master 地址
SENTINEL get-master-addr-by-name <master-name>

# 確認角色
ROLE
# 或
INFO replication
```

### 重新連接策略

- 每次重連都需重新解析 master 地址
- 將上次成功的 Sentinel 放在列表最前面（減少延遲）
- 如果 ROLE 確認失敗，等待數百 ms 後從 Step 1 重試

### Sentinel 數據

| 數據 | 說明 |
|------|------|
| `SENTINEL masters` | 所有被監控的 master 列表 |
| `SENTINEL get-master-addr-by-name` | 特定 master 的地址 |
| `SENTINEL replicas` | 特定 master 的 replicas |
| `SENTINEL sentinels` | 其他 Sentinel 實例 |

## 相關頁面

- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 叢架架構、HA
- [[wiki/sources/2026-08-03-redis-security-practices|Redis Security Best Practices]] — 安全實踐
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
