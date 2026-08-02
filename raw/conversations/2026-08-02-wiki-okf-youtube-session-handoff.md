---
type: raw-conversation
source_kind: session-handoff
title: Wiki × OKF × YouTube 架構討論 — Session Handoff
status: captured
immutable: true
---
好
# Session Handoff

> 這是 2026-08-02 對話中止前的延續摘要。它是原始討論／工作交接紀錄，不是已完成的正式決策文件。

## 使用者目標

在繼續 `pi-todo-journal` 前，先評估目前 Obsidian Wiki 是否應結合 OKF 與 Cole Medin YouTube knowledge-base pipeline。討論期間使用 annotator UI；確認前不修改 Wiki、PLAN 或程式碼。

## 已確認方向

1. 採用 **Project OKF Bundle** 概念。
2. `C:/Cheerio/Obsidian/projects/` 與 `wiki/` 同層。
3. `projects/` 是專案／系統領域的 AI-readable OKF knowledge bundle，不是程式碼、PLAN、Todo 或 release 的第二份 source of truth。
4. 真正 package source of truth 仍在 `C:/Cheerio/pi/packages/<package>/`；package 內的 `docs/` 是隨 package 發布的開發／維運 bundle；Obsidian `projects/` 是跨 session 的 project knowledge bundle。
5. Shared Wiki 長期建議分離 collections：`concepts/`、`entities/`、`sources/`、`decisions/`、`discussions/`，並保留 `topics/` 作為導航／taxonomy 層。
6. `type` 描述文件性質；topic 描述主題；collection 描述內容集合；三者不能互相取代。
7. Annotator 原始 feedback 先保存到 `raw/conversations/`，使用 `type: raw-conversation`、`immutable: true`。
8. 確認後內容再依性質寫入：
   - `wiki/discussions/` + `type: discussion`：尚未定案
   - `wiki/decisions/` + `type: decision`：已確認架構／技術選擇
   - `wiki/concepts/` + `type: concept`：跨專案可重用知識
   - `wiki/sources/` + `type: source`：整理後的外部來源
9. `pi-todo-journal` 保持獨立，不負責 YouTube transcript、知識 canonicalization、Wiki taxonomy 或語意 linking。
10. `pi-todo-journal` 未來可以保存使用者明確提供的 URL、source ID、wiki path、project bundle path，這是引用型 linking，不是語意型 linking。
11. `youtube-to-wiki` 應是獨立 skill + scripts，不是 `pi-todo-journal` extension，也不直接併入 `pi-plannotator-auto`。
12. YouTube pipeline 延後到 Wiki collection、linking contract、lint 與遷移規則穩定後；可視為後續 Phase 5 / 獨立工作。
13. Cole 的 `channel-to-kb` 不原封不動使用：可重用 fetcher／pipeline 思路，但需改成我們的 raw 路徑、metadata、local date、確認流程與 Wiki schema。
14. Linking 採 AI-first + lint 護欄：AI 處理高信心關係，低信心 canonicalization／父子／對比關係列入 audit，交由人確認。
15. 已釐清 `canonical`：中文可稱「正式主頁／統一代表頁」，指多個來源中代表同一穩定知識單位的頁面。
16. 日期策略使用執行環境的 system local date/time；不再以 `Asia/Taipei` 作為現行規則。部分舊文件仍需日後校正。

## 建議的長期 Obsidian 結構

```text
C:/Cheerio/Obsidian/
├── projects/               # Project / system OKF bundles
├── wiki/                   # Shared cross-project knowledge graph
│   ├── concepts/
│   ├── entities/
│   ├── sources/
│   ├── topics/
│   ├── decisions/
│   ├── discussions/
│   ├── index.md
│   └── log.md
├── raw/
│   ├── conversations/
│   ├── youtube/             # future
│   └── assets/
├── todos/
└── journal/
```

## Linking 核心概念

Linking 不是把所有關鍵字變成連結，而是建立可回溯的知識道路：

```text
source → concept / entity → project / topic
concept ↔ source
concept ↔ related concept
```

高信心關係可放在有語意的 section：`Sources`、`Related`、`Builds on`、`Part of`、`Contrasts with`、`Implemented by`、`Used in`。新來源必須先查現有 index，優先更新正式主頁，不直接建立同義重複頁。

## `pi-todo-journal/PLAN.md` 已完成的影響評估

已讀取：

- `C:/Cheerio/pi/packages/pi-todo-journal/PLAN.md`
- `AGENTS.md`
- `docs/index.md`
- `docs/dependencies.md`
- `docs/log.md`

結論：不需要重寫核心 phases。

- Phase 0：不需重開；可補一個 knowledge boundary 決策。
- Phase 1：已完成，不回頭加入 Wiki taxonomy。
- Phase 2：已完成；未來可加入 optional explicit references。
- Phase 3：TaskStore / JournalStore adapters 保持通用，不知道 concepts/entities/link semantics。
- Phase 4：TUI、CI、package、npm 驗證範圍不變。
- Phase 5：保留 Notion、Git、外部 TaskStore；不要把 youtube-to-wiki 塞進 package。

候選補充文字：

```markdown
### 知識邊界

`pi-todo-journal` 負責 session 狀態、明確任務、Journal、checkpoint
與使用者提供的 references。不負責外部知識 ingest、概念統一、Wiki
taxonomy 或知識頁面之間的語意 linking。YouTube-to-Wiki 是獨立的
skill/workflow，不屬於本 package 功能。
```

## 尚未完成／明天繼續

1. 由使用者確認 PLAN 影響評估的四個推薦項目：
   - 不改核心 phases，只補 knowledge boundary 與 optional references
   - 未來允許明確 URL / source ID / wiki path 作為 optional reference
   - Obsidian `projects/` 維護獨立 Project OKF Bundle，package 不依賴它
   - 先完成 Wiki 規範與 audit，再修改 plan 文件，最後建立獨立 youtube-to-wiki
2. 在不直接修改的前提下，提出正式的 Wiki 規範／migration plan。
3. 產生現有 Wiki 的 collection/topic/link audit 報告：broken links、orphan、missing inbound/outbound、可能重複概念、遷移風險。
4. 確認 `projects/` 是否需要從現有 `wiki/projects/` 搬到 Obsidian root；目前是方向決定，尚未搬檔。
5. 確認何時及如何把 annotator handoff 從 `raw/conversations/` 整理成 `wiki/decision` 或 `wiki/discussion`。
6. 日後校正現有 Wiki 與 package 文件中的舊 `Asia/Taipei` 敘述；不要修改歷史 log 的事實，只更新現行規範。
7. YouTube skill 留到 linking contract、lint、migration 規則穩定後再研究／建立。

## 明天恢復提示

重新開始時先讀：

1. `C:/Cheerio/Obsidian/todos/current.md`
2. 本 handoff 檔案
3. 本次 annotator feedback（若 session 工具仍可取得）
4. `C:/Cheerio/pi/packages/pi-todo-journal/PLAN.md`（已讀，可直接進入影響評估確認）

不要直接修改 PLAN、Wiki 或程式碼；先用 annotator UI 確認剩餘決策，再建立正式 plan。
