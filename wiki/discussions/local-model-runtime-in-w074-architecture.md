---
title: "Local Model Runtime 在 W-074 與 W-080 架構中的定位"
type: discussion
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [w-074, w-080, local-llm, harness, process-management, runtime]
topics: [agent-infrastructure, ai-agent]
canonical: discussions/local-model-runtime-in-w074-architecture
---

# Local Model Runtime 在 W-074 與 W-080 架構中的定位

> 一句話：llama-server 應定位為 model runtime（基礎設施），不是可實例化的「專家」；不進 MVP，但留縫。

## 問題

把 GGUF + llama.cpp 包進自建 harness（如零號專家 / ado-worklog-expert-agent）時，llama-server 在架構中該怎麼歸位？

## 已達成的判斷

### 1. llama-server 是 runtime，不是 expert

按 W-074 決策 #4，專家是可實例化的角色；但 llama-server 是**基礎設施**。若 `ExpertHarness.invoke()` 每次開關一次 server，每次都要付模型載入成本（第三方觀測 7B 模型切換約 3–10 秒，⚠️ 未在本機驗證）。

**正確形狀**：singleton runtime + reference counting + health check，生命週期綁在 harness process 而不是單次 invoke。

### 2. 放置位置

建議獨立一層（如 `packages/runtime`），**不要放進 core**——core「零外部相依」是四層 package 中最有價值的約束。

### 3. 與已知問題的交集

2026-09-02 架構圓桌列出的四個未解問題之一：

> Windows 上 `SIGKILL` 可能殺不掉子系命令，需 `taskkill /F /T /PID` 輔助

llama-server 讓這題更硬（可能的三層進程樹：harness → llama-server(router) → 各 model 子進程，⚠️ 未實測）。

**具體建議**：ChildProcessRunner 從一開始就照「要能殺整棵進程樹」設計，並使用 `taskkill` 的絕對路徑 + error handler。

### 4. `execution_constraint` 的第一個真實值

`task-contract.md` 的 `execution_constraint` 目前留空（TBD）。地端 GGUF + 自建 harness 落在 `agent-security-levels` 的 **L5/L6**。真做下去，這欄位就有第一個非空值，也是第一次讓決策 #12「Tier 是專家 × harness 綁定的屬性、可升級」有實例可驗。

### 5. 與 W-080 的關係

實測確認：repo 內**沒有任何 ado-worklog-expert-agent 的程式碼或目錄**。2026-09-01 圓桌已經把 local model 定位在「DevOps skill 執行 + 混合式四層路由 + 7B 起步」那條線。**local model 的正確歸屬是那裡，不應該從 W-074 核心再長出第二條路線。**

## 決策：不進 MVP，但現在就留縫

### 不進的理由

MVP 範圍是「一案一專家、全自動、跑通六步閉環」，零號專家刻意選最弱的 Tier C。現在塞 local model 等於同時引入 runtime 生命週期、Windows 進程樹、模型載入延遲三個新變數，違反「先跑通閉環再擴」。

### 留縫的做法（成本近乎零）

1. 專家註冊表加 `model_runtime` 欄位（`external | local`），MVP 全填 `external`
2. ChildProcessRunner 照「殺三層樹」設計

### 做的時機

零號專家四條測試路徑跑通之後，當作第一次「換 runtime」的練習。那時 EventStream 已在跑，換前換後的差異有證據可看。

## 與既有頁面的關係

- [[wiki/concepts/agent-security-levels|Agent 安全等級]] — L5/L6 的真實應用場景
- [[wiki/entities/llama-cpp|llama.cpp]] — runtime 工具知識
- [[wiki/entities/asus-vivobook-k6502zc|ASUS Vivobook K6502ZC]] — 測試硬體
- [[wiki/sources/2026-09-01-local-llm-round-table-synthesis|圓桌會議紀要]] — 7B 起步的原始決策
- [[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 對 W-074]] — 同系列討論

## 來源

- [[wiki/sources/2026-09-05-llamacpp-deployment-cuda-verification|llama.cpp 部署深度研究與 CUDA build 實測]]（§6）
