---
title: "圓桌會議紀要：Local LLM DevOps Skill 執行可行性"
type: source
created: 2026-09-01
updated: 2026-09-01
sources: 3
tags: [round-table, local-llm, devops, hybrid-architecture, copilot]
topics: [agent-infrastructure, ai-agent]
canonical: sources/2026-09-01-local-llm-round-table-synthesis
provenance:
  - kind: raw
    path: .pi/round-table/20260901-221918/synthesis.md
---

# 圓桌會議紀要：Local LLM DevOps Skill 執行可行性

> **日期：** 2026-09-01
> **參與者：** Claude (Claude Code)、Gemini (agy)、Codex (OpenAI Codex)
> **輪數：** 2 輪
> **主持人：** Pi

## 共識（三方一致）

### 研究報告修正
- ❌ 14B/32B 模型不適合直接作為起點 → 降級為 **7B 起步**
- ❌ Aider 分數不適用於評估 DevOps skill 編排能力
- ❌ 報告 benchmark 數字可能不是 UHD 770 實測

### 架構決策：混合式四層架構
```
第一層：確定性路由（Regex / 關鍵字 / Embedding）→ 高信心命令直接走
第二層：LLM 意圖解析（只輸出嚴格 JSON：intent + arguments + confidence）
第三層：Policy / Schema 驗證（skill 存在、參數完整、操作類型、確認需求）
第四層：執行（既有 .mjs 腳本，LLM 不直接執行 shell）
```

### 模型選擇
- 🌟 第一階段：Qwen2.5-Coder-7B-Instruct Q4_K_M（~4.7GB）
- 🔬 第二階段：Qwen2.5-Coder-14B-Instruct Q4_K_M（品質對照組）
- ❌ 不建議：32B CPU-only

### Copilot 整合
- BYOK 可以進入 PoC，但不應未經測試就作為核心編排器
- 建議只暴露少量工具（如 `dispatch_skill`），不暴露任意 shell tool

### 為何要 Local？
- 資料隱私：Azure DevOps 專案狀態不希望外送
- 離線能力：Air-gapped 或網路不穩定環境
- 最務實策略：敏感 → Local；複雜 → Cloud；無法判斷 → 安全遮罩後 Cloud fallback

## 關鍵風險
1. 7B 模型的 tool calling 穩定性
2. Mini PC 散熱與熱節流（i5-14500T 35W）
3. 系統常駐佔用 8-12GB
4. Ollama 對 Intel iGPU 支援不成熟
5. 企業設備政策風險

## PoC 路線圖
1. Phase 1：Ollama 準確率驗證（1-2 天）
2. Phase 2：混合式路由器搭建（2-3 天）
3. Phase 3：端到端整合（3-5 天）
4. Phase 4：效能優化（視結果決定）
