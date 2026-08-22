---
title: "個人 AI 助理架構願景"
type: raw-conversation
source_kind: session-handoff
created: 2026-08-22
---

# 個人 AI 助理架構願景（Cheer 2026-08-22 提出）

## 背景

跟 Claude 討論目前手上工作優先順序時，Cheer 岔開帶出一個更大的架構願景：我們自己要建造的個人 AI 助理，應該長什麼樣子。

## 核心主張

- 定位：「擁有手跟腳」的架構。核心（像 Pi 一樣，本身沒有外部搜尋能力）先做好、持續優化；其他能力全部以 plugin 方式按需求掛載。
- 開發分兩部分：
  1. 核心開發（無外部搜尋能力，先做好核心並持續優化）
  2. Plugin 擴充：
     (a) 搜尋能力 plugin —— 做到極致
     (b) 長期記憶 + 專屬知識庫體系 —— AI 不能只有記憶沒有知識
     (c) 其他外接能力 —— 同樣以 plugin 方式擴充
- 觸發動機：Pi 目前沒有 Deep Research 能力，無法跟 Gemini 交互作用；且 Gemini 最近品質有下降感受，讓 Cheer 覺得「搜尋／研究」這塊需要自己掌握，相對重要。

## 運作機制構想

1. **自我成長與觀測機制**：系統要完整追蹤／觀測每次動作——調用了哪些工具與 skill、hook 何時起作用、每段 input/output 都要記錄。這是 AI 持續進步的關鍵。
2. **單一對口與統籌調度**：這個 AI 就是專屬跟 Cheer 對口的主助理。由它告訴 Cheer 今天有哪些工作、會派哪些 agent 執行、各 agent 負責什麼／搭配什麼 model，整體工作進程與統籌都由它負責。
3. **Stateless 互動 + 記憶檢索**：每次對話本身維持類似 stateless 狀態；被問到時才去調記憶、重新檢視當前狀況。類比人類：「你今天交待事情，我事後回想你指的是哪件、再去問負責人進度」。
4. **多元 Harness 與外部 Agent 調用**：不同 harness 搭配不同 agent model 效果差很多，希望這個自建 agent 能調用外部 AI agent 來派工，調用了什麼、取得的資訊，也要全部記錄。

## 附帶疑問

- 這些都要寫 code 才能做，那是不是應該先把「寫 code 的方式」（有效管理程式碼、有效管理知識庫）建好，再開始做這件事？
- Obsidian Web Clipper 範本的屬性（title/description/source_url/source_domain/author/published/clipped/type/source_kind/immutable/tags）夠不夠用來抓 MCP 官方最新資訊？

## Refs

- [[work/current#W-2026-08-074|W-2026-08-074]]（本次想法整理成的工作項目）
- [[work/current#W-2026-08-017|W-2026-08-017]] 研究 harness 架構
- [[work/current#W-2026-08-025|W-2026-08-025]] 研究 AI Agent 網路查詢能力
- [[work/current#W-2026-08-022|W-2026-08-022]] 研究 MCP Server 架構
- [[work/current#W-2026-08-033|W-2026-08-033]] 研究 Hook 機制
- [[work/current#W-2026-08-NEW-001|W-2026-08-NEW-001]] 研究 Session 管理
- [[work/current#W-2026-08-NEW-002|W-2026-08-NEW-002]] 研究 Context 管理
