---
title: "smolagents — Hugging Face 的 Code-first Agent 框架"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [smolagents, huggingface, code-first, ast-execution, python]
topics: [agent-runtime-implementations]
canonical: entities/smolagents
---

# smolagents — Hugging Face 的 Code-first Agent 框架

> Hugging Face 推出的 Python Agent 框架，核心哲學：LLM 直接輸出 Python 代碼（AST 解析執行），而非 JSON 工具呼叫。

## 核心架構

- **CodeAgent**：LLM 輸出 Python 程式碼區塊，將 Multi-step loops、If-else 條件式和多工具調用壓縮在單次 LLM 往返中完成 `[§5]`
- **AST 執行**：本地 `LocalPythonExecutor` 僅 best-effort 限制，**不是安全邊界** `[§5]`
- **安全沙箱**：生產環境強制要求 Docker / E2B / Modal / Blaxel 硬體隔離 `[§5]`

## 記憶

預設 memory 僅存活於單次運行的 step logs。社群提供與 Hindsight 記憶庫整合，賦予 Retain（事實儲存）+ Recall（語意檢索）+ Reflect（反思）三層記憶 `[§5]`。

## 可觀測性

- 整合 Arize Phoenix / AX（OTel 衍生）追蹤 `[§5]`
- 支援 `agent.replay()` 本機重放特定對話步驟 `[§5]`
- `agent.memory.steps` 訪問 AST 執行歷史 `[§5]`

## 多 Agent

Orchestrator-Worker 模式。主 CodeAgent 作為中樞調度多個 Specialist 子代理。子 Agent 內部 step-by-step 日誌被物理隔絕（Private History），Orchestrator 僅接收最終 JSON `[§5]`。

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]
