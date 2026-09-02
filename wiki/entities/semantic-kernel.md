---
title: "Semantic Kernel — 微軟企業級 Plugin SDK"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [semantic-kernel, microsoft, enterprise, plugin-sdk, .net, python, java]
topics: [agent-runtime-implementations]
canonical: entities/semantic-kernel
---

# Semantic Kernel — 微軟企業級 Plugin SDK

> 將 LLM 作為 Orchestrator，動態調用企業既有的傳統代碼、API 與資料庫（Plugins）。強型別、可預測性、合規安全。

## 核心架構

- **Kernel** 編排 **Plugins**（Native functions / 自訂代碼工具）與 **Functions**（Semantic prompts） `[§4]`
- **ChatCompletionAgent**：單一會話型代理 `[§4]`
- **宣告式定義**：支援從 YAML 宣告式規格書直接實例化 Agent，行為、提示詞、可用工具均在 YAML 中定義 `[§4]`
- **原生支援**：.NET、Python、Java `[§4]`

## 可觀測性

- **`IFunctionInvocationFilter` 攔截器**：Plugin 函數執行前後強制攔截，精確捕獲輸入參數與執行結果 `[§4]`
- 適合對高風險工具進行強審計與日誌記錄 `[§4]`

## 多 Agent 協作

- **AgentGroupChat** + 顯式策略控制 `[§4]`：
  - **SelectionStrategy**：Sequential / RoundRobin / KernelFunctionSelectionStrategy（LLM 判斷下一位發言者） `[§4]`
  - **TerminationStrategy**：KernelFunctionTerminationStrategy（LLM 判斷任務完成） + MaximumIterations 安全底線 `[§4]`
- 狀態重置需呼叫 `ResetAsync()` `[§4]`

## 2026 年演進

**Microsoft Agent Framework (MAF)** 2026/04 發布 1.0，統一 SK 的企業安全基礎與 AutoGen 的多代理編排理念 `[§4]`。原版 AutoGen 進入維護模式，新專案建議採用 MAF `[§4]`。

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]

## 相關頁面

- [[wiki/entities/autogen|AutoGen]] — 微軟的多代理框架（MAF 統一兩者）
