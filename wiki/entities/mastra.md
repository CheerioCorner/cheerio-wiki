---
title: "Mastra — TypeScript 全包式 Agent 框架與觀察式記憶"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [mastra, typescript, agent-framework, observational-memory, durable-workflow]
topics: [agent-runtime-implementations]
canonical: entities/mastra
---

# Mastra — TypeScript 全包式 Agent 框架與觀察式記憶

> 由原 Gatsby 團隊創立。Platform-agnostic（Node/Cloudflare Workers/自建），以觀察式記憶（OM）為技術亮點。

## 核心架構

- **確定性工作流引擎**：`createWorkflow` API，鏈式語法（`.then()` 串行 / `.parallel()` 併行 / `.branch()` 條件 / `.doWhile()` 循環） `[§6]`
- **Platform-agnostic**：可部署在 Node、Cloudflare Workers 或自建伺服器 `[§6]`

## 觀察式記憶（Observational Memory, OM）

獨特技術亮點，解決長對話 Context 膨脹問題 `[§6]`：

- **Observer（30k 觸發）**：背景非阻塞 Agent，對話 token 累積達 30k 前分批壓縮歷史為 dense notes（5x-40x 壓縮） `[§6]`
- **Reflector（40k 重構）**：合併重複資訊、去除失效事實、主動遺忘過期 context `[§6]`
- **效果**：Context window 維持穩定且 Prompt-cacheable，節省 4x-10x Token 費用 `[§6]`
- **LongMemEval SOTA**：94.87% `[§6]`
- **雙重 Scope**：Thread（單一對話）+ Resource（跨對話用戶） `[§6]`

## 可觀測性

- **Mastra Studio**：本地 `localhost:4111` Web UI，Workflow 視覺化執行圖 + OM 進度 + Spans 樹 `[§6]`
- **Evals 測試框架**：LLM-as-a-judge + 工具調用準確度測試，CI/CD 回歸測試 `[§6]`

## 多 Agent

- 原生支援 Sequential + Handoff + Hierarchical 混合 `[§6]`
- 最獨特：將整套 Workflow 包裝成普通 Tool 交給 Supervisory Agent 呼叫 `[§6]`
- 支援 Workflow suspend / resume 的人類審批 `[§6]`

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]
