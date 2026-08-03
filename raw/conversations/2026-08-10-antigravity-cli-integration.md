# 2026-08-10 Antigravity CLI 整合 Session

## 摘要

研究並整合 Antigravity CLI（取代已停用的 Gemini CLI 免費層），建立 gy skill，測試完整流程。

## 對話重點

### 1. 問題發現
- 使用者想用 Gemini CLI 做 multi-agent 整合
- 執行 `gemini` 時遇到錯誤：「This client is no longer supported for Gemini Code Assist for individuals」
- 查詢後發現：**Gemini CLI 免費層已在 2026/6/18 停止服務**，所有免費/Pro/Ultra 用戶迁移到 Antigravity CLI

### 2. 決策過程（Grilling）
- 目的：免費額度 + 能力互補 + 實驗性質
- 角色：Pi 是 orchestrator，負責啟動 Gemini、收集結果
- 可見性：即時看到 + 事後回顧
- 執行環境：獨立 terminal pane
- 觸發方式：自然語言 + slash command
- Log 結構：每次執行有唯一 ID，存在 `.pi/gemini-runs/<id>/`
- 第一個測試：簡單 Q&A，確認流程跑通
- 認證：Google OAuth

### 3. 技術實作
- 移除 Gemini CLI：`npm uninstall -g @google/gemini-cli`
- 安裝 Antigravity CLI v1.0.14：`irm https://antigravity.google/cli/install.ps1 | iex`
- 完成 Google OAuth 認證
- 建立 gy skill（觸發關鍵字：用 Gemini、問 Gemini、agy、gy、/gy）
- 測試完整流程成功（agy -p + JSON log 存取）

### 4. 關鍵發現
- Antigravity CLI 是 Go 寫的，不需要 Node.js
- 二進位檔名是 `agy`，不是 `gemini` 也不是 `antigravity`
- 免費配額是週制（community 報告約 2000 行程式碼後會被限制）
- 可以用 `/usage` 查看目前用量
- 設定檔位置：`~/.gemini/antigravity-cli/settings.json`

## 產生的知識

### 新增 Entity
- `gy` skill（在 `~/.agents/skills/gy/`）

### 更新的 Wiki 頁面
- 無（本次 session 主要是實作，不是知識整理）

### 決策紀錄
- W-2026-08-020 完成：Antigravity CLI 整合完成，gy skill 可用，log 存取正常

## Refs

- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]
- [[wiki/concepts/meta-harness|meta-harness]]
- [[work/current|Current Work]]
