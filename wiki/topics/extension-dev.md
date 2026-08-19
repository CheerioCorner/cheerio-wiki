---
type: topic
title: Extension Development
topic: extension-dev
created: 2026-08-02
updated: 2026-08-06
---

# Extension Development

> Agent extension、package、發布與 OKF 的導航頁。

## Entities

- [[wiki/entities/pi-agent-core|pi-agent-core]] — agent runtime（extensions events 系統、register tools/commands/shortcuts） 🛠️
- [[wiki/entities/pi-mono|pi-mono]] — Pi monorepo（CLI entry point、extension loading、read-only mode） 🛠️
- [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler]] — Cron job 與排程系統（@pi-agents/loop） 🛠️
- [[wiki/entities/plannotator|Plannotator]]
- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]] — Plugin 系統（10 slots）、Extension UI System 🛠️
- [[wiki/entities/pi-web-access-zh-tw|pi-web-access-zh-tw]]

## Concepts

- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置與方式]]
- [[wiki/concepts/npm-publishing-workflow|Package Publishing Workflow]]
- [[wiki/concepts/okf-open-knowledge-format|OKF]]

## Sources

- [[wiki/sources/2026-07-11-plannotator-research|Plannotator 研究]] — Pi extension 研究
- [[wiki/sources/2026-07-31-npm-publishing-setup|Package Publishing 首次實作]] — npm publish 流程建立
- [[wiki/sources/2026-08-01-okf-extension-development|OKF Extension 開發實作]] — pi-plannotator-auto OKF 知識包建立
- [[wiki/sources/2026-08-01-okf-knowledge-catalog|OKF Knowledge Catalog]] — Google Cloud 參考實作
- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|Wiki × OKF 架構優化]] — 完整 migration 工作紀錄
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough|Pi Architecture Walkthrough]] — Extensions events 系統解析
- [[wiki/sources/2026-08-10-pi-cron-job-explained|Pi Cron Job 實作原理]] — @pi-agents/loop extension 實作
- [[wiki/sources/2026-07-23-plannotator-copilot-setup|Plannotator Copilot 安裝手冊]] — Windows 上 Copilot CLI 整合安裝
