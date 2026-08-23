---
title: MCP + Agent Skills 整合
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: 1
tags: [mcp, agent-skills, integration, extensibility]
topics: [skill-system, mcp-ecosystem]
canonical: concepts/mcp-agent-skills-integration
provenance_raw:
  - "raw/web/2026-08-22-Build with Agent Skills.md"
---

# MCP + Agent Skills 整合

> Agent Skills 與 MCP 是互補的擴充機制——Skills 提供知識與流程，MCP 提供工具連接。兩者可組合建構更強大的 AI Agent。

## Skills 與 MCP 的差異

| 面向 | Agent Skills | MCP |
|------|-------------|-----|
| 定位 | 知識與流程封裝 | 外部工具連接 |
| 載入方式 | 按需載入到 context | 透過協定連接 Server |
| 互動模式 | 指導 AI 行為 | 提供可呼叫的 tools |
| 擴充對象 | AI 的知識與能力 | AI 的工具與資料來源 |

## 整合方式

Agent Skills 可以：
- 包裝 MCP Server 的使用說明
- 提供 MCP Server 的最佳實踐
- 定義如何組合多個 MCP tools 完成複雜任務
- 作為 MCP 之上的知識抽象層 `Build with Agent Skills`

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/agent-skills|Agent Skills]] — Skill 系統根概念
- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制

## 來源

- [[raw/web/2026-08-22-Build with Agent Skills|Build with Agent Skills]]
