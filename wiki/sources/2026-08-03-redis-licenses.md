---
title: "Redis 授權指南 — 企業導入必讀"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-Licenses.md
    url: https://redis.io/legal/licenses/
provenance_raw: "raw/web/2026-08-03-Licenses.md"
provenance_url: https://redis.io/legal/licenses/
tags: [redis, license, rsalv2, sspl, agpl, enterprise, compliance]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-licenses
---

> 來源：[Redis Licenses](https://redis.io/legal/licenses/)
> 原始剪藏：[[raw/web/2026-08-03-Licenses.md|raw clip]]

## 一句話

Redis 8+ 採用三選一授權（RSALv2 / SSPLv1 / AGPLv3），企業需根據使用場景選擇合適的授權。

## 重點摘要

### Redis 8+ 三選一授權

| 授權 | 類型 | 核心限制 | 適合場景 |
|------|------|---------|---------|
| **RSALv2** | Source Available（非 OSI） | 不可作為 managed service 提供 | 內部使用、SaaS 但不轉售 Redis |
| **SSPLv1** | Source Available（非 OSI） | 若提供服務，需公開完整 source | 雲端服務商 |
| **AGPLv3** | OSI 開源 | 修改後分發需公開 source | 開源專案、社群 |

### 版本授權演進

| 版本 | 名稱 | 授權 |
|------|------|------|
| **7.2.x 及更早** | Redis | BSD-3-Clause |
| **7.4.x - 7.8.x** | Redis Community Edition | RSALv2 或 SSPLv1（雙選） |
| **8+** | Redis Open Source | RSALv2 或 SSPLv1 或 AGPLv3（三選） |

### 關鍵差異

| 面向 | BSD（舊） | RSALv2/SSPL（中） | AGPL（新） |
|------|----------|------------------|-----------|
| 商業使用 | ✅ 自由 | ⚠️ 有限制 | ✅ 自由 |
| 作為服務提供 | ✅ | ❌ RSALv2 禁止 | ⚠️ 需公開 source |
| 修改後分發 | ✅ | ⚠️ 需保留授權 | ❌ 需用 AGPL |
| OSI 認證 | ✅ | ❌ | ✅ |

### 企業導入決策樹

```
你要怎麼用 Redis？
    │
    ├─ 內部使用（不對外提供服務）
    │   └─ ✅ 任何授權都可以
    │
    ├─ 作為 SaaS 提供（不轉售 Redis 本身）
    │   └─ ✅ RSALv2 或 AGPLv3
    │
    ├─ 作為 PaaS/Redis 服務提供
    │   └─ ⚠️ SSPLv1 或 AGPLv3（需公開 source）
    │
    └─ 開源專案整合
        └─ ✅ AGPLv3（需同樣用 AGPL）
```

### Redis Insight / VS Code

- 付費用戶：商業授權
- 社群用戶：SSPLv1

### 模組授權

RediSearch、RedisJSON、RedisTimeSeries、RedisBloom 在 Redis 8+ 後統一為三選一授權。

## 相關頁面

- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 叢架架構
- [[wiki/sources/2026-08-03-redis-security-practices|Redis Security Best Practices]] — 安全實踐
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
