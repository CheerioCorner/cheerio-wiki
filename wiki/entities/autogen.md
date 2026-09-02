---
title: "AutoGen — 微軟多代理框架（v0.4 / AG2 / MAF）"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [autogen, microsoft, multi-agent, event-driven, actor-model, ag2, maf]
topics: [agent-runtime-implementations]
canonical: entities/autogen
---

# AutoGen — 微軟多代理框架（v0.4 / AG2 / MAF）

> 定位於「對話導向（Conversation-first）」，強調多個 Agent 透過非結構化對話與動態互動共同解決複雜任務。2026 年演進為 Microsoft Agent Framework (MAF)。

## 核心架構

### v0.4 三層架構
1. **AgentChat**：高階 API 層（`AssistantAgent`、`UserProxyAgent`、Team） `[§4]`
2. **Core (`autogen-core`)**：非同步事件驅動 Actor Model，每個 Agent 擁有獨立 Mailbox `[§4]`
3. **Extensions (`autogen-ext`)**：LLM 客戶端與自訂工具對接 `[§4]`

### 2026 年演進
- 原版 AutoGen 進入維護模式 `[§4]`
- **AG2**：開源社群分支，繼續獨立維護 `[§4]`
- **Microsoft Agent Framework (MAF)**：2026/04 發布 1.0，統一 SK 的企業安全基礎與 AutoGen 的多代理編排 `[§4]`

## 可觀測性

- 實時運行事件（Events）：模型呼叫、工具呼叫、狀態變遷 `[§4]`
- `run_stream` / `team.run_stream`：增量 delta、工具執行事件、Actor 切換信號，pipe 導出至日誌或前端 UI `[§4]`
- v0.4 後引入 OTel 支援 `[§4]`

## 多 Agent 協作

- **GroupChat / RoundRobinGroupChat**：同一 Thread 內自由討論 `[§4]`
- **Swarm 模式**：去中心化、LLM 驅動的動態分派/交接 `[§4]`
- **GraphFlow 模式**：DAG 有向無環圖，確定性分支流轉 `[§4]`
- **Debate 模式**：多 Agent 針對同一主題辯論，引入 Judge 代理評審 `[§4]`
- **UserProxyAgent**：人類包裝為特殊 Agent，`human_input_mode`（ALWAYS/TERMINATE/NEVER）控制 `[§4]`

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]

## 相關頁面

- [[wiki/entities/semantic-kernel|Semantic Kernel]] — 微軟的企業級 Plugin SDK
