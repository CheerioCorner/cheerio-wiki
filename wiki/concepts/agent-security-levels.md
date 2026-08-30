---
title: "Agent 安全等級 — 從 L1 到 L7 的防護層級"
type: concept
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [security, agent, harness, sandbox, docker, openshell]
topics: [agent-infrastructure, ai-agent]
canonical: concepts/agent-security-levels
---

# Agent 安全等級 — 從 L1 到 L7 的防護層級

> **成長階段：** 🌱 種子期

## 一句話定義

**Agent 安全等級**根據 model 和 harness 的部署位置，定義從「最危險」到「最安全」的七個防護層級。核心原則：**危險的地方絕對不會是模型，一個 agent 最危險絕對是 harness** `[83:22]`。

## 為什麼模型不危險

- 模型只做文字接龍，活在 GPU 中，離開 GPU 就什麼都不會做 `[83:22]`
- 最危險的是 harness：佔 agent 90% 的操作、會寫入檔案、操作電腦、回傳資料到伺服器 `[83:22]`
- Prompt Injection 是 harness 的鍋，不是模型的鍋 `[122:07]`

## 七個安全等級

| 等級 | Model 位置 | Harness 位置 | 特徵 | 適用場景 |
|------|-----------|-------------|------|---------|
| **L1** 🔴 | 公有雲 | 本機（無防護） | 最危險，資料直接上傳雲端 `[93:22]` | 不建議用於敏感資料 |
| **L2** 🟡 | 公有雲 | Sandbox（VM/Docker） | harness 炸了只炸容器 `[89:25]` | 一般開發測試 |
| **L3** 🟡 | 可控雲端（NIM/OpenRouter） | 開源 harness | 可審查 harness 原始碼 `[82:03]` | 需要雲端模型但要可控 |
| **L4** 🟢 | 可控雲端 | 開源 harness + Sandbox | 雙重防護 `[93:22]` | 雲端模型 + 安全要求 |
| **L5** 🟢 | 私有雲 | Sandbox | 資料不出公司 `[94:45]` | 企業內部使用 |
| **L6** 🟢🟢 | 私有雲 | OpenShell（無法上網） | harness 斷網 `[94:45]` | 高資安要求 |
| **L7** 🟢🟢🟢 | 私有雲 | OpenShell + 完全斷網 | 最安全，完全離線 `[103:26]` | 金融/軍事等級 |

## 開源 vs 閉源 Harness 的安全差異

- **閉源 harness**（Claude Code、ChatGPT）：你從來沒看過原始碼，做什麼你都不知道 `[82:03]`
- **開源 harness**（Pi Agent、OpenClaw、Hermes）：原始碼可審查，看過覺得安全才用 `[82:03]`
- **微軟的信任優勢**：企業傾向用 VS Code + Copilot，因為微軟成立 30-40 年，比 Anthropic（4-5 年）、OpenAI（10 年）更被信任 `[85:50]`

## Sandbox 技術選項

| 技術 | 說明 | 特色 |
|------|------|------|
| **Docker** | 容器化隔離 | 方便、可 docker start/stop `[76:32]` |
| **OpenShell** | NVIDIA 的沙箱引擎 | 可完全斷網 `[90:40]` |
| **Docker SBX** | Docker 官方 sandbox | 2026 年 8 月推出 `[90:40]` |
| **VM** | 完全虛擬化 | 最強隔離但較重 `[89:25]` |

## 實際案例：去識別化

Josh 用同一個檔案（含身份證、姓名、地址、電話的假個資）測試三個等級：
- **L1（最危險）**：閉源 harness + 雲端模型 → 3 分鐘後資安部電話就來 `[99:43]`
- **中等安全**：Pi Agent + Qwen 27B 地端模型 → 完全不出網路，效果一樣好 `[99:43]`
- **L6/L7（最安全）**：Pi Agent + Qwen 27B 在 OpenShell 沙箱中 → 連 Google/GitHub 都出不去，仍能完成工作 `[103:26]`

## 與其他概念的關係

- 站在 [[wiki/concepts/harness|Harness]] 之上——harness 是安全等級的核心變數
- 為 [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 的安全面提供分類框架
- 與 [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]] 的 CIA 原則互補
