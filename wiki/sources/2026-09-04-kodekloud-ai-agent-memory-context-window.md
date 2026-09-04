---
title: "AI Agent Memory: Why Context Window Expansion Is Not Enough（KodeKloud）"
type: source
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [memory, context-window, agent-memory, rag, memory-management]
topics: [agent-memory-context]
provenance_raw: "raw/youtube/2026-09-04-kodekloud-ai-agent-memory-context-window.md"
provenance_url: "https://www.youtube.com/watch?v=0P-ACuHyu-0"
---

# AI Agent Memory: Why Context Window Expansion Is Not Enough（KodeKloud）

> KodeKloud 入門講解：為何擴充 context window 無法解決 agent 記憶問題——context 與 memory 的本質差異、三種早期權宜之計的失效模式、記憶作為 write+use 迴圈的設計需求。

## 核心論點

### Context ≠ Memory

在 agent 成為主要焦點之前，多數 LLM 應用幾乎完全依賴 context window `[00:00]`——系統提示、當前使用者訊息、近期對話歷史就是模型能記住的全部。模型**不會**自動記得昨天發生的事、10 個 tool call 之前的事、或跨 session 的事 `[00:00]`。應用程式必須把這些資訊放回 prompt，或給模型正確的工具去取回 `[00:28]`。

Context 是**活的**——模型在當前 forward pass 中看到的東西 `[00:55]`。Memory 則不同：儲存在模型外部的資訊，可能被使用也可能不被使用 `[01:00]`。短期與長期記憶住在不同的地方 `[01:00]`。

### RAM vs Disk 類比

類比電腦記憶：運作中的文件存在 RAM 裡，快速、即時可達、但不永久——斷電就消失 `[01:16]`。要讓它更持久，就得寫到更耐用的地方，比如磁碟或 SSD `[01:30]`。Agent memory 運作方式非常相似：context window 像 RAM（活躍、快速、有限），長期記憶像 disk（持久）`[01:30]`。但 agent 需要一個系統來決定**存什麼、取什麼、何時載入** `[01:30]`。

### 三種早期權宜之計及其限制

在 agent 有專用記憶系統之前，業界主要用三種方式繞過 context window 的限制 `[02:00]`：

| 方式 | 作用 | 失效模式 |
|------|------|----------|
| **RAG** | 檢索相關 chunks，不用把整個文件塞進 prompt `[02:00]` | 可能檢索到錯誤資訊；設定門檻比看起來高——需要 chunk、embed、index、正確排序 `[02:33]` |
| **Summaries** | 壓縮長對話，保留重點 `[02:00]` | 有損——依賴 LLM 判斷哪些細節值得保留；一旦摘要丟掉某個後來重要的細節，agent 無法恢復 `[02:53]` |
| **Profiles** | 儲存使用者事實（偏好、寫作風格、進行中的專案）`[02:00]` | 容易過時且自相矛盾——使用者行為會變，上個月是真的東西今天可能不是，但系統仍需視為永久事實 `[02:53]` |

### 擴充 Context Window 不夠

即使 model context 變大，也無法完全解決問題 `[03:25]`。因為記憶不只是存更多 token——更深層的問題是**記憶管理** `[03:25]`。現代 agent 不再只是一個我們提問的 LLM `[03:25]`，業界需要更嚴肅的記憶方案 `[03:25]`。

### 記憶是 Write + Use 迴圈

記憶不是一次性動作，而是一個**迴圈** `[03:58]`：

1. **Write（寫入）**：agent 觀察發生了什麼、決定什麼重要、存到某個持久化的地方 `[04:06]`。光是這一步就很困難——agent 看到的大部分東西不值得永遠記住 `[04:06]`
2. **Use（使用）**：agent 檢索相關資訊、放回 context、如果有所改變就更新 `[04:17]`

對長時間運行的任務，agent 不只需要記住**事實**，還需要記住**經驗**——上次什麼方法有效、下次該避免什麼錯誤 `[04:17]`。這就是為什麼在 long-horizon agent benchmark 中記憶如此重要——模型必須在 many steps 中保持連貫 `[04:17]`。這可能是今日 assistant 與下一代 agent 之間最大的差別 `[04:47]`。

## 與既有知識的關係

### 證實（Confirms）

- **RAM vs Disk 類比**：與 [[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型]] 的 Working Memory 條目（context window 類比 RAM——快速、即時可達、揮發性、容量有限）幾乎逐字對應，屬獨立來源的重複驗證
- **記憶迴圈**：與 [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Sakana AI 的 write-manage-read 迴圈]] 概念一致，為兩階段簡化版
- **Context 擴充不夠**：印證 [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] 的 Context 邊界法則（task 能塞進 context 時記憶不加分只加成本），但本片無量化實驗
- **長任務失去連貫**：印證 [[wiki/concepts/context-rot|Context Rot]] 的觀察（agent 重複犯錯、偏離問題）

### 補充（Supplements）

- **三種早期權宜之計**：既有頁面講了記憶系統本身的設計，但沒有交代「沒有專用記憶系統之前大家怎麼將就」這段動機脈絡。本片把 RAG/Summary/Profile 的**各自失效模式**攤開講清楚，是既有頁面可吸收的增量價值
- **經驗記憶**：「不只需要記住事實，還需要記住經驗」呼應 [[wiki/concepts/coala-four-memory-types|CoALA]] 的 Episodic Memory 定義，但用更直覺的教學語言重述

## 相關頁面

- [[wiki/concepts/context-engineering|Context Engineering]] — 四個對策族（Write/Read/Handoff/Maintain）
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 三支柱記憶架構
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 四階檢索策略效果排名
- [[wiki/concepts/context-rot|Context Rot]] — 長任務脈絡腐化
- [[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型]] — 記憶功能分類
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Sakana AI 來源筆記]] — 記憶系統實驗

## 來源

- [[raw/youtube/2026-09-04-kodekloud-ai-agent-memory-context-window|YouTube 原始逐字稿]]（auto-generated English，00:04:56，16 segments）
