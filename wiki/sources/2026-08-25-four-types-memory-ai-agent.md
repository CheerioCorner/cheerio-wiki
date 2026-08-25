---
title: "The Four Types of Memory Every AI Agent Needs"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [memory-system, ai-agent, coala, context-window, agent-skills]
topics: [agent-memory-context]
provenance_raw: "raw/youtube/four-types-memory-ai-agent.md"
---

# The Four Types of Memory Every AI Agent Needs

> 來源：[YouTube](https://www.youtube.com/watch?v=BacJ6sEhqMo) | Duration: 10:41 | Language: en

## 摘要

本影片介紹 AI Agent 的四種記憶類型，基於普林斯頓大學（Princeton University）的 CoALA 框架（Cognitive Architectures for Language Agents）`[01:47]`。

## 四種記憶類型

### 1. Working Memory（工作記憶）`[02:09]`
- 對應 agent 的 **context window**
- 類比為 **RAM**（隨機存取記憶體）：快速、即時可達、但**揮發性**（volatile）且**容量有限** `[02:30]`
- 包含：當前對話、系統指令、已載入的檔案/資料
- 當 session 結束就消失 `[02:44]`
- 即使最大的 context window（100 萬 tokens）仍有上限，塞太多效能會下降 `[02:50]`
- **每個 agent 都有**，但 chatbot 也只有這個 `[03:13]`

### 2. Semantic Memory（語義記憶）`[03:25]`
- Agent 的**知識庫**：儲存事實、規則、慣例、文件
- 學術文獻常描述為向量資料庫或知識圖譜 `[03:45]`
- 但實務上，很多 production 系統用的是**簡單的 Markdown 檔案** `[03:59]`
- 例：Claude Code 的 `CLAUDE.md` — 包含專案架構、coding conventions、build commands、框架選擇 `[04:07]`
- 每次 session 開始時載入 context window `[04:25]`
- **語義記憶告訴 agent 一般需要知道的事**；沒有它，agent 會反覆犯同樣的錯誤 `[04:31]`

### 3. Procedural Memory（程序記憶）`[04:51]`
- Agent **如何做事**的記憶
- 有個開放標準叫 **agent skills**，使用 `skill.md` 檔案格式 `[05:07]`
- Skill 就是一個資料夾加一個 Markdown 檔案，描述技能用途和步驟 `[05:13]`
- 使用 **progressive disclosure**（漸進式揭露）：agent 不會一次載入所有 skills `[05:29]`
  - 先只看輕量索引（名稱+描述，約 100 tokens/skill）`[05:49]`
  - 任務匹配時才載入完整指令 `[06:01]`
  - 引用的檔案/模板/腳本只在執行需要時才拉入 `[06:12]`
- 與語義記憶不同：**語義記憶的知識一直在 context 中，程序記憶是按需載入** `[06:27]`

### 4. Episodic Memory（情節記憶）`[06:36]`
- Agent 過去互動和決策的**記錄**，以及從中學到的東西 `[06:40]`
- 簡單做法：儲存所有對話 transcript 再搜尋 `[06:48]` — 但通常不太有用
- Production 系統做的是**蒸餾/壓縮**：agent 跨 session 累積筆記，但不存一切 `[07:03]`
- 決定什麼值得記住的标准：**該資訊是否在未來對話中有用** `[07:19]`
- 例：「上次除錯 auth module，問題在 middleware 層」比 45 分鐘除錯 transcript 有用多了 `[07:31]`
- **這是記憶開始看起來像「學習」的地方**，agent 會隨時間變好 `[07:45]`
- 但也**最難做對**：什麼該刪除？資訊何時過時？使用者換工作要保留舊專案記憶嗎？`[07:52]`
- **對 agent 來說，遺忘是工程問題** `[08:17]`

## 記憶複雜度 vs Agent 類型 `[08:32]`

| Agent 類型 | 需要的記憶 |
|-----------|-----------|
| Simple reflex agent（恆溫器、基本路由 bot） | Working memory only |
| Customer support agent（密碼重設） | Working + Procedural |
| Coding agent | 全部四種（Working + Semantic + Procedural + Episodic）|

## 核心論點

「Memory is what separates a chatbot from an agent」`[09:49]` — 記憶是區分聊天機器人與 agent 的關鍵要素。Agent 能回應的不只是當下，而是基於**持續性知識**和**累積經驗**的回應。

## 關鍵引用

- 「Memory is what separates a chatbot from an agent」`[09:49]`
- 「For agents, forgetting is an engineering problem」`[08:17]`
- CoALA = Cognitive Architectures for Language Agents（普林斯頓大學研究團隊）`[01:47]`

## 相關頁面

- [[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型]] — 詳細框架解析
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 三支柱分析 × 五種存儲方式
- [[wiki/concepts/agent-skills|Agent Skills]] — Procedural Memory 的實作標準
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 記憶檢索策略效果階梯
- [[wiki/topics/agent-memory-context|Agent Memory & Context]]
