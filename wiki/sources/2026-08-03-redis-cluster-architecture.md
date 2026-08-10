---
title: "Redis Enterprise Cluster Architecture"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-Cluster Architecture.md
    url: https://redis.io/technology/redis-enterprise-cluster-architecture/
provenance_raw: "raw/web/2026-08-03-Cluster Architecture.md"
provenance_url: https://redis.io/technology/redis-enterprise-cluster-architecture/
tags: [redis, cluster, sharding, high-availability, enterprise]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-cluster-architecture
---

> 來源：[Redis Enterprise Cluster Architecture](https://redis.io/technology/redis-enterprise-cluster-architecture/)
> 原始剪藏：[[raw/web/2026-08-03-Cluster Architecture.md|raw clip]]

## 一句話

Redis Enterprise 叢架支援水平擴展（Sharding）、高可用性（HA 99.999%）、多租戶隔離，採用 Shared-nothing 對稱架構。

## 重點摘要

### 叢架基本概念

| 概念 | 說明 |
|------|------|
| **Sharding** | 將大資料庫分割成小片段，分散到多個節點 |
| **Cluster** | 一組雲端實例/VM/裸機伺服器，共享記憶體/儲存池 |
| **多租戶** | 多個資料庫可在同一叢集運行，完全隔離 |

### 四種資料庫形態

| 形態 | 說明 |
|------|------|
| **Simple** | 單一 primary shard |
| **HA** | primary + replica 一對 |
| **Clustered** | 多個 primary shards，各管不同 hash-slots |
| **HA Clustered** | 多對 primary/replica shards |

### 三種存取方式

1. **Database Endpoint** — 連接唯一 URL + port，Redis 透明處理擴展
2. **Sentinel API** — 用 sentinel 協定連接正確節點
3. **OSS Cluster API** — 直接連接每個 shard

### 高可用性（HA）

- 99.999% uptime（five-nines）
- Replica shard 自動在不同節點建立
- 節點故障時，replica 自動提升為 primary
- 支援跨 region 災難恢復

### Shared-nothing 對稱架構

```
┌─────────────────────────────────────────┐
│              Redis Cluster              │
├─────────────────────────────────────────┤
│  Node 1        Node 2        Node 3    │
│  ┌─────┐      ┌─────┐      ┌─────┐   │
│  │Proxy│      │Proxy│      │Proxy│   │
│  │Shard│      │Shard│      │Shard│   │
│  │Mgr  │      │Mgr  │      │Mgr  │   │
│  └─────┘      └─────┘      └─────┘   │
└─────────────────────────────────────────┘
```

| 組件 | 職責 |
|------|------|
| **Redis Shard** | 開源 Redis 實例（primary 或 replica） |
| **Zero-latency Proxy** | C 語言撰寫，multi-threaded、lock-free、stateless；處理連線管理、SSL、pipelining |
| **Cluster Manager** | 分散式進程，管理整個叢集生命週期：provisioning、scaling、re-sharding、re-balancing |
| **Secure REST API** | 所有管理操作透過安全 API，適合微服務架構 |

### 架構優勢

| 面向 | 說明 |
|------|------|
| **效能** | Data-path 與 Control-path 分離，shard 專注處理使用者請求 |
| **可用性** | 擴展/再分片/再平衡時，應用持續存取資料 |
| **安全性** | 設定命令只能透過 CLI/UI/API 執行，RBAC 控制 |
| **可管理性** | 單一命令即可 provisioning、更新 |
| **可擴展性** | 水平擴展，跨多節點/伺服器/叢集 |

### 資源優化建議

- 使用 multi-tenancy 最大化基礎設施利用率
- 智慧儲存分層：熱資料放 RAM，冷資料放 Flash/SSD
- 測試/開發/生產環境分離

## 相關頁面

- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session、Rate Limiting
- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy]] — 記憶體滿時的淘汰策略
- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization]] — 記憶體優化策略
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning]] — 效能調校最佳實踐
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
