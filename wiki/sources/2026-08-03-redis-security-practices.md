---
title: "Redis Security Best Practices"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-03-recommended-security-practices.md
    url: https://redis.io/docs/latest/operate/rs/security/recommended-security-practices/
provenance_raw: "raw/web/2026-08-03-recommended-security-practices.md"
provenance_url: https://redis.io/docs/latest/operate/rs/security/recommended-security-practices/
tags: [redis, security, tls, access-control, deployment]
collection: sources
topics: [backend-systems]
canonical: sources/2026-08-03-redis-security-practices
---

> 來源：[Redis Recommended Security Practices](https://redis.io/docs/latest/operate/rs/security/recommended-security-practices/)
> 原始剪藏：[[raw/web/2026-08-03-recommended-security-practices.md|raw clip]]

## 一句話

Redis 安全三大層面：部署安全（網路隔離、anti-virus 排除）、叢集安全（存取控制、LDAP、HTTPS）、資料庫安全（強密碼、TLS、備份）。

## 重點摘要

### 1. 部署安全（Deployment Security）

| 實踐 | 說明 |
|------|------|
| **可信網路** | 部署在trusted network，不對公網開放 |
| **Anti-virus 排除** | 排除 `/opt/redislabs` 等路徑，避免掃描干擾 |
| **遠端日誌** | syslog 送到遠端伺服器，避免日誌被刪 |
| **奇數節點** | 至少 3 個節點，確保 failover 正常 |
| **循序重啟** | 一次只重啟一個節點，避免 quorum 失敗 |
| **Client-side 加密** | 在應用層加密後再存入 Redis（限制：無法直接搜尋/比較） |

### 2. 叢集安全（Cluster Security）

| 實踐 | 說明 |
|------|------|
| **存取分級** | 控制 cluster / database / both 的存取權限 |
| **LDAP 認證** | 整合組織的 LDAP 做角色驗證 |
| **HTTPS API** | 停用未加密的 REST API endpoint |

### 3. 資料庫安全（Database Security）

| 實踐 | 說明 |
|------|------|
| **強密碼** | 符合組織安全政策的密碼 |
| **停用 default user** | 預設使用者開啟，建議停用 |
| **TLS 設定** | 配置 TLS 協定支援合規需求 |
| **Client certificate** | 只允許授權主機存取 |
| **自訂憑證** | 用組織自己的憑證取代 self-signed |
| **備份策略** | 配置災難恢復備份 |

### Anti-virus 排除路徑

| 路徑 | 說明 |
|------|------|
| `/opt/redislabs` | 主安裝目錄 |
| `/opt/redislabs/bin` | CLI 工具（rladmin, redis-cli） |
| `/opt/redislabs/config` | 系統設定檔 |
| `/opt/redislabs/lib` | 系統函式庫 |
| `/opt/redislabs/sbin` | provisioning 工具 |

## 相關頁面

- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 叢架架構、Sharding、HA
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns]] — 快取模式、Session、Rate Limiting
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning]] — 效能調校最佳實踐
- [[wiki/concepts/redis|Redis]] — Redis 核心概念
- [[wiki/topics/backend-systems|Backend Systems]] — 後端系統導航頁
