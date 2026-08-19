---
title: "Notion AI 相關種子 — agentskills.io + Axway AI Gateway"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/notion-ingest/2026-08-07-ai-related-seeds.md
provenance_raw: "raw/notion-ingest/2026-08-07-ai-related-seeds.md"
tags: [notion, seeds, agentskills, ai-gateway, knowledge-garden]
collection: sources
topics: [knowledge-mgmt, skill-cases-and-comparisons, agent-infrastructure]
canonical: sources/2026-08-07-ai-related-seeds
---

> 來源：Notion「任何當下 → AI相關」頁面
> 原始剪藏：[[raw/notion-ingest/2026-08-07-ai-related-seeds.md|raw clip]]

## 一句話

從 Notion 知識花園 ingest 的兩顆 AI 相關種子：agentskills.io（Skill 開放標準）和 Axway AI Gateway（企業 AI 治理層）。

## 種子 A：agentskills.io

| 項目 | 內容 |
|------|------|
| 來源 | https://agentskills.io/ |
| 狀態 | 🌱 種子期 |
| Wiki | [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] |

### 核心洞察

- Skill 開放標準讓跨平台成為可能
- Pi 的 skill 系統已經走在前面，但格式標準化是趨勢
- 差異就是成長的空間

### 比對分析

| 概念 | 來源的說法 | 我的做法 |
|------|-----------|---------|
| Skill 是可移植模組 | 指令+腳本+資源的組合 | Pi 的 `~/.agents/skills/` 結構一致 |
| 跨平台是趨勢 | 支援 Copilot/Claude/Gemini | Pi 目前是專用格式 |
| 分發 | `npx skills` Registry | 本地安裝/npm |
| 安全 | 建議企業審查 | 信任本地檔案 |

---

## 種子 B：Axway Amplify AI Gateway

| 項目 | 內容 |
|------|------|
| 來源 | https://www.axway.com/en/products/ai-gateway |
| 狀態 | 🌱 種子期 |
| Wiki | [[wiki/entities/axway-amplify-ai-gateway|axway-amplify-ai-gateway]] |

### 核心洞察

- 企業 AI 治理的核心是「集中化」：統一端點、集中安全、Token 審核
- 個人 Agent 雖不需要那麼重的治理，但 MCP Proxy 和 Semantic Caching 的概念值得關注

### 比對分析

| 概念 | 來源的做法 | 我的做法 |
|------|-----------|---------|
| MCP 是 AI 的 USB-C | 標準化工具整合 | Pi 已支援 MCP |
| AI 需要治理層 | 集中控管安全+成本 | Pi 有 token budget 但無集中治理 |
| 安全模型 | Gateway 層 RBAC + Guardrails | 信任本地 |
| 成本控管 | Token Quota + Budgets（集中式） | Token Budget（session 級） |

## 相關頁面

- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]] — 企業 AI 治理層
- [[wiki/topics/skill|Skill]] — AI agent skill 生態系
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — AI Agent 技術基礎設施
- [[wiki/topics/knowledge-mgmt|Knowledge Management]] — 知識管理導航頁
