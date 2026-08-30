---
title: "Graphify vs codebase-memory-mcp 整合決策"
type: decision
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [knowledge-graph, obsidian, graphify, codebase-memory-mcp, vault-integration]
topics: [agent-research, knowledge-mgmt]
canonical: decisions/graphify-vs-codebase-memory-mcp
provenance_url: "https://github.com/safishamsi/graphify"
---

大腦 wiki（`C:/Cheerio/Obsidian`）本身不搭配 Graphify 或 codebase-memory-mcp；Cheerio 手上的程式碼專案若要搭配，預設只裝 codebase-memory-mcp，Graphify 僅在有明確 docs/PDF/schema 知識圖譜化需求時才加裝，且輸出不併入主 vault。

## 決策過程

1. **起點**：影片 [[wiki/sources/2026-08-25-graphify-obsidian-cheat-code|Graphify + Obsidian + Claude Code = CHEAT CODE]] 提出 Graphify `--obsidian` 匯出的四種整合策略（Standalone / Quarantine subfolder / Piecemeal harvest / Redistribution）。
2. **圓桌會議（round-table，Gemini 單方，Codex 因環境問題缺席）**：Gemini 三輪自我辯論後提出「混合式架構」——Graphify 只匯出宏觀（module/file）節點，微觀（class/function）交給 [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]] runtime 查詢。此方案的前提是「Graphify 支援 `.graphify.json` 的 `levels` 排除設定」，但 Gemini **未查證**此設定是否存在。
3. **Claude 直接查證原始碼**：讀 [safishamsi/graphify](https://github.com/safishamsi/graphify)（112,459★）的 `graphify/export.py`，確認 `to_obsidian()` 函式無任何 granularity/level 參數，CLI 也只有 `--obsidian` / `--obsidian-dir`。**Gemini 的「層級過濾」前提不成立**，`docs/node-summaries-rfc.md` 的 file-level summary 提案也明確標示尚未實作。
4. **Codex 工程判斷**（直接呼叫 `codex exec`，非 round-table skill，因該 skill 在此環境讀取本地檔案受阻）：推翻 Gemini 的「顆粒度」分工框架，改用「資料型態」分工——Graphify 官方定位本就是「codebase + **docs + SQL schemas + configs + PDFs**」，codebase-memory-mcp 完全沒有 PDF/docs ingestion 或 Obsidian 匯出功能（查證其 [README](https://github.com/DeusData/codebase-memory-mcp) 確認）。兩者真正的差異點是「程式碼 runtime 查詢」vs「非程式碼素材 → 人類可瀏覽知識圖譜」，不是同一份程式碼的宏觀/微觀切分。

## 結論

### 1. 大腦 wiki 本身不需要搭配任一工具

兩個工具的共同目的是「把還不是可瀏覽知識庫的東西轉成可瀏覽知識庫」。`C:/Cheerio/Obsidian` 已經是手工策展的 Obsidian vault——有 entity/concept/source 分類、provenance 追溯、[[wiki/entities/obsidian|雙模型共識品質把關]]，語意結構比自動 AST/文件匯出更細緻。對 wiki 本身跑這兩個工具只會製造 note explosion 與 graph view 噪音，是倒退。

- codebase-memory-mcp 索引「程式碼」，wiki 裡沒有程式碼可索引，用不上。
- Graphify 的「docs/PDF → 知識圖譜」能力，wiki 已經有更高品質的等價物：`raw/` → ingest → `wiki/` 管線（雙模型交叉驗證，見 [[wiki/entities/obsidian|Obsidian]] 條目 §知識演化協定），不需要額外引入自動化匯出工具。

### 2. Cheerio 手上的程式碼專案（另一件事，不是這個 wiki）

若要搭配，維持既有判斷（見 [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]] 條目）並補上條件：

| 情境 | 建議 |
|------|------|
| 預設（目前規模，核心需求是程式碼理解） | 只裝 codebase-memory-mcp |
| 有明確、高頻的 docs/PDF/SQL schema → 可瀏覽知識圖譜需求 | 加裝 Graphify，但只掃 `docs/`、`schemas/`、PDF，不碰 `src/`；輸出放獨立 `generated/graphify/` sidecar，不併入主 vault、不跟人工筆記混放（Graphify 輸出是生成式、會覆蓋重建） |
| 只想做文件/PDF → Obsidian，不需要程式碼查詢 | 只裝 Graphify |
| 需求尚未成形 | 兩個都不裝 |

**不要對同一批程式碼同時跑兩者**：會產生兩套不同步的索引（SQLite watcher vs 重新生成的 Markdown），孤兒 backlink、node 命名不一致，維護成本大於效益。

## 分歧與未解問題

- Gemini 的「層級過濾」技術前提已證實不成立，其「混合式架構」提案的實作細節不可採用；但其「不要整批合併」的頂層結論與 Claude/Codex 一致，故此點不算真正分歧。
- Codex 執行時 round-table skill 的本地檔案讀取在該環境被拒絕（`CreateProcessAsUserW` 錯誤），因此本次 Codex 意見是獨立呼叫取得，不是標準 round-table 流程產出，格式上與 §3.1 的雙模型共識機制略有出入，但結論收斂度高，予以採用。
- 尚未實測：codebase-memory-mcp 實際安裝與索引效果（見 [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]] 條目「待辦」）。

## 來源

- [[wiki/sources/2026-08-25-graphify-obsidian-cheat-code|Graphify + Obsidian + Claude Code = CHEAT CODE]]

## 相關頁面

- [[wiki/entities/graphify|Graphify]]
- [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]]
- [[wiki/concepts/graphify-obsidian-export|Graphify → Obsidian Export]]
- [[wiki/entities/obsidian|Obsidian]]
