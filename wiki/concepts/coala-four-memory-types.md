---
title: "CoALA 四種記憶類型 — AI Agent 的功能分類框架"
type: concept
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [memory-system, ai-agent, coala, taxonomy]
topics: [agent-memory-context]
---

# CoALA 四種記憶類型

> AI Agent 記憶架構的**功能分類**框架：從「記憶做什麼」的角度切分四種類型。與 [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] 的**存儲機制**分類（五種存儲 × 四種檢索 × 六種維護）互補，不衝突。

## 框架來源

CoALA（Cognitive Architectures for Language Agents）由普林斯頓大學（Princeton University）研究團隊提出 `[01:47]`。

## 四種記憶類型

### 1. Working Memory（工作記憶）
- **本質**：Agent 的 context window，當下能看到的一切 `[02:14]`
- **類比**：RAM（隨機存取記憶體）— 快速、即時可達、揮發性、容量有限 `[02:30]`
- **內容**：當前對話、系統指令、已載入的檔案/資料
- **限制**：session 結束就消失；即使最大 100 萬 tokens 仍有上限 `[02:44]`
- **地位**：每個 agent 都有，但 chatbot 也只有這個 `[03:13]`

### 2. Semantic Memory（語義記憶）
- **本質**：Agent 的知識庫 — 事實、規則、慣例、文件 `[03:25]`
- **實作**：學術上是向量資料庫/知識圖譜；實務上常是**簡單的 Markdown 檔案** `[03:59]`
- **例證**：Claude Code 的 `CLAUDE.md` — 專案架構、coding conventions、build commands `[04:07]`
- **載入方式**：每次 session 開始時載入 context window（**常駐**）`[04:25]`
- **關鍵**：沒有它，agent 會反覆犯同樣的錯誤 `[04:31]`

### 3. Procedural Memory（程序記憶）
- **本質**：Agent **如何做事**的記憶 `[04:51]`
- **實作**：Agent Skills 標準，使用 `skill.md` 格式 `[05:07]`
- **載入方式**：**Progressive Disclosure**（漸進式揭露）`[05:29]`
  - 先看輕量索引（~100 tokens/skill）`[05:49]`
  - 任務匹配時才載入完整指令 `[06:01]`
  - 引用資源在執行時才拉入 `[06:12]`
- **與語義記憶差異**：語義記憶常駐 context，程序記憶按需載入 `[06:27]`

### 4. Episodic Memory（情節記憶）
- **本質**：過去互動和決策的**蒸餾經驗** `[06:36]`
- **不是**：儲存所有對話 transcript（簡單做法，通常沒用）`[06:48]`
- **而是**：跨 session 累積的**壓縮筆記**，只記「未來有用的」`[07:19]`
- **例證**：「上次除錯 auth module，問題在 middleware 層」比 45 分鐘 transcript 有用 `[07:31]`
- **意義**：這是記憶看起來像**「學習」**的地方，agent 會隨時間變好 `[07:45]`
- **難點**：什麼該刪除？資訊何時過時？遺忘是工程問題 `[07:52]`

## Agent 複雜度 vs 記憶組合 `[08:32]`

| Agent 類型 | Working | Semantic | Procedural | Episodic |
|-----------|---------|----------|------------|----------|
| Simple reflex（恆溫器、路由 bot） | ✅ | ❌ | ❌ | ❌ |
| Narrow customer support（密碼重設） | ✅ | ❌ | ✅ | ❌ |
| Coding agent | ✅ | ✅ | ✅ | ✅ |

**不是每個 agent 都需要全部四種**，記憶組合取決於任務複雜度。

## 核心論點

「Memory is what separates a chatbot from an agent」`[09:49]`

Chatbot 只回應當下；Agent 回應基於**持續性知識**和**累積經驗**。好的記憶架構還會記住**錯誤**，避免重複犯錯 `[10:04]`。

## 與其他框架的關係

| 分類軸 | 框架 | 切分方式 |
|--------|------|----------|
| **功能** | CoALA（本頁） | Working / Semantic / Procedural / Episodic |
| **存儲機制** | [[wiki/concepts/ai-agent-memory-systems\|AI Agent Memory Systems]] | 純文本 / SQLite / 向量 / 圖譜 / 混合 |

兩者描述同一領域的**正交軸**，可以交叉組合使用。例如：
- Semantic memory 可用純文本（CLAUDE.md）或向量資料庫實作
- Procedural memory 可用 Progressive Disclosure（skill.md）實作
- Episodic memory 可用混合存儲（mem0）實作

## 相關頁面

- [[wiki/sources/2026-08-25-four-types-memory-ai-agent|來源筆記 — YouTube 影片]]
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 存儲機制分類（互補）
- [[wiki/concepts/agent-skills|Agent Skills]] — Procedural Memory 的實作標準
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 檢索策略效果階梯
- [[wiki/concepts/consumer-ai-memory-personalization|Consumer AI Memory]] — 消費級產品記憶設計（不同領域）
- [[wiki/topics/agent-memory-context|Agent Memory & Context]]
