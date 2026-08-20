---
title: "OpenWiki and OKF: Agent-Readable Code Wikis — Antão Almada"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [openwiki, okf, agents-md, blog, walkthrough]
topics: [okf]
provenance_url: "https://antaoalmada.dev/posts/OpenWiki-and-OKF-Agent-Wikis/"
---

# OpenWiki and OKF: Agent-Readable Code Wikis

> Antão Almada 的詳細 walkthrough：AGENTS.md 應保持精簡、OKF bundle structure、OpenWiki 使用流程。

## AGENTS.md 的 Token 稅

AGENTS.md 被注入每個 session 的 context window，每一行都是固定 token 稅。Bloated AGENTS.md = 每個 session 都多花 token，不管任務是否需要那些知識 `https://antaoalmada.dev/posts/OpenWiki-and-OKF-Agent-Wikis/`。

**正確架構**：AGENTS.md 保持簡短（build commands、testing conventions、coding standards），wiki 處理其餘（module descriptions、cross-cutting concepts、API surfaces、ADRs）。AGENTS.md 指向 wiki，agent 讀 index 後按需導航。

## OKF Bundle 實作

### Concept Documents

每個 file 是一個 concept document，唯一要求：YAML frontmatter 有非空 `type` field。`type` 值不由中央註冊——producer 自訂，consumer 必須容忍未知 types。

### Reserved Files

- `index.md` — 目錄清單，無 frontmatter
- `log.md` — 變更歷史，最新在前

### Cross-links

Standard markdown links between concept documents. OKF 不定義 edge types；broken links 被容忍（代表 not-yet-written knowledge）。

## OpenWiki 實作細節

- **12k+ stars**、MIT license、LangChain AI
- `openwiki/INSTRUCTIONS.md` — user-authored brief for scope and priorities，永不被覆寫
- `openwiki --update` 只重寫變更模組（非全量重建）
- `<!-- OPENWIKI:START -->…<!-- OPENWIKI:END -->` 注入模式：只改寫自己的區塊
- 與 `code-graph` 和 `graph-query` tools 是互補關係，不是取代

## 為什麼 AGENTS.md 單獨不夠

Agent 好的文件生產者，但如果不受控會產出太多。大型 codebase → 大型 wiki → 多到 agent 無法一次載入。文件層必須可導航：agent 需要找到對的兩頁，不用載入其他四十頁。

## 相關頁面

- [[wiki/entities/langchain-openwiki|OpenWiki]]
- [[wiki/concepts/okf-open-knowledge-format|OKF]]
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 「Context 邊界法則」與本文 token 經濟學論證互補
