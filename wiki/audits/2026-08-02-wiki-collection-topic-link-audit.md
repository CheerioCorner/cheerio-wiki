---
title: 2026-08-02 Wiki Collection / Topic / Link Audit
type: audit
created: 2026-08-02
updated: 2026-08-02
status: draft
scope: wiki-only-analysis
immutable: false
tags: [wiki, audit, collections, topics, links, migration, okf]
---

# 2026-08-02 Wiki Collection / Topic / Link Audit

> 本報告只記錄分析與建議，不執行批量遷移、不修改既有 Wiki 頁面、不修改 `PLAN.md`，也不修改 `raw/`。

## 結論摘要

目前 Wiki 是一個已經可用、但 collection 語義尚未與新的 Wiki × OKF 架構對齊的 28 頁 Markdown 圖譜：7 個 source、9 個 entity、7 個 concept、5 個無 frontmatter 的導覽／專案 README。`wiki/topics/` 仍承載 15 個主要內容頁，`wiki/projects/` 仍承載 3 個專案 README；尚未建立 shared `concepts/`、`entities/`、`decisions/`、`discussions/` collections，也尚未建立 Obsidian root `projects/` Project Bundle。

最重要的發現不是「所有 links 都壞了」，而是 link 解析必須分層：

1. `wiki/index.md` 的完整路徑連結在 Obsidian vault root 語義下是合法且可解析的；不能用只從目前 process working directory 解析的簡單 parser 將它們誤判為 broken。
2. 目前真正需要處理的是 25 個唯一的 unresolved basename target（共 42 次出現）、2 個帶反斜線的錯誤 wikilink（`wiki/projects/README.md`），以及 1 個 basename 同時命中 `.md` 與 `.canvas` 的歧義。
3. 其中很多 unresolved target 是正文中的「預留但尚未建立」概念，而不是應立即建立的頁面；依目前決策，不應自動補頁。
4. `wiki/log.md` 的歷史連結需要和正文知識圖譜分開評估。它包含已刪除頁面的歷史引用，不能直接等同為現行 Wiki broken links。

---

## 1. 掃描範圍與方法

### 1.1 掃描範圍

- Repository：`C:/Cheerio/Obsidian`
- 操作前同步：已執行 `git pull --ff-only`，結果為 `Already up to date.`
- Wiki：`wiki/**/*.md`、`wiki/**/*.canvas`
- 交叉參照：`raw/`、`journal/`、`todos/`、`.obsidian/` 設定與 Git tracked files
- 本輪不把 `raw/` 中任何檔案列為可修改項目
- `raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md` 被視為原始交接紀錄，不作為可遷移 source/content 頁面

### 1.2 解析規則

本報告使用下列 link 分類，而不是只用單一檔案系統相對路徑判定：

- **完整路徑**：先依 Obsidian vault root（例如 `topics/...`、`projects/...`、`sources/...`）解析，再檢查 `wiki/` root 與目前頁面資料夾的合理情況。
- **basename**：先檢查目前頁面資料夾，再檢查 Wiki 內唯一 basename；若沒有目標則列為 unresolved；若同時有多個目標則列為 ambiguous。
- **alias**：`[[path|顯示文字]]` 只以 pipe 前的 target 解析。
- **heading/block subpath**：解析 link target 時移除 `#heading` 或 `^block` 部分。
- **Canvas**：`.canvas` 也是可被 basename 命中的 Obsidian 檔案，因此不能只掃 Markdown。

> 注意：`wiki/index.md` 的 `[[topics/...]]`、`[[projects/...]]`、`[[sources/...]]` 是 vault-root 完整路徑，應視為合法，不是 broken link。

---

## 2. 現有目錄與 collection 分布

### 2.1 Wiki 檔案分布

| 現況目錄 | Markdown | Canvas | 語義判斷 |
|---|---:|---:|---|
| `wiki/` root | 2 | 0 | `index.md`、`log.md` 導覽／歷史層 |
| `wiki/topics/` | 15 | 2 | 目前主要知識內容；同一層混合 concept、entity |
| `wiki/sources/` | 7 | 1 | 整理後的 source notes；另有一個同 basename 的 Canvas |
| `wiki/projects/` | 4 | 0 | project README 與 project navigation；尚未是 root Project Bundle |
| **合計** | **28** | **3** | `wiki` 內共 31 個內容檔，加 `.gitkeep` 為 32 個 files |

### 2.2 新架構對照

目前尚未存在：

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/decisions/`
- `wiki/discussions/`
- root `projects/` Project OKF Bundle

目前已存在但角色需重新定義：

- `wiki/topics/`：應保留為導航／taxonomy 層，不再作為主要內容 collection。
- `wiki/projects/`：目前是舊 project documentation collection；未來與 root `projects/<project-id>/` 有遷移／分工風險。
- `wiki/sources/`：可直接保留為 shared `sources` collection 的基礎，但需補明確 provenance 與 source link contract。

### 2.3 目前 index 統計的可信度

`wiki/index.md` 目前宣稱「總來源數 7、總頁面數 17」。這個數字與檔案實際數量及統計口徑不一致：

- 7 個 `wiki/sources/*.md` 是合理的 source Markdown 數。
- 但 Wiki 內共有 28 個 Markdown，另有 3 個 Canvas。
- `17` 不能代表目前全部 Wiki Markdown，也不能代表「主要內容頁」而不加說明。

建議後續採用可明確重算的口徑，例如：`source notes`、`concept/entity pages`、`project README`、`navigation/log` 分開統計；不要在未決定口徑前自動改數字。

---

## 3. Frontmatter 的 type、欄位與格式差異

### 3.1 Wiki Markdown 現況

| 類型 | 數量 | 主要位置 |
|---|---:|---|
| `entity` | 9 | `topics/` 內 8、`projects/pi-todo-journal/README.md` 1 |
| `concept` | 7 | `topics/` |
| `source` | 7 | `sources/` |
| 無 frontmatter | 5 | `index.md`、`log.md`、`projects/README.md`、兩個 project README |
| **合計** | **28** | |

23 個有 frontmatter 的 Wiki Markdown 具備共同欄位：

```yaml
title
type
created
updated
sources
tags
```

### 3.2 欄位差異

| 欄位／格式 | 出現位置 | Audit 判斷 |
|---|---|---|
| `upstream`, `last_checked` | `wiki/sources/2026-07-11-mattpocock-skills.md` | 合理的 source-specific extension，但尚未納入正式 schema 說明 |
| `source_url` | `wiki/topics/extension-dev/okf-open-knowledge-format.md` | 可保留作過渡欄位；新規範較適合統一到 `provenance`／`sources` 結構 |
| `sources: N` | 幾乎所有正式頁 | 現為數字計數，不是可追溯的 wikilink 或 provenance 清單；數字與實際正文引用可能不同 |
| `type: concept/entity/source` | 正式 Wiki 頁 | 與目前五 collections 大致對應，但 collection 與 type 尚未被明確分離 |
| 無 `description`、`status`、`generated`、`verified` | 所有現行 shared Wiki 正式頁 | 與 OKF 範例的 provenance／trust metadata 尚未對齊；不應在本輪自動填入推測值 |
| `verified.by` | 目前未見正式 shared Wiki 頁 | 未來若填寫，應使用 `human:Cheer`；不可寫成 `human:Cheerio` |
| 日期 | 多為 `YYYY-MM-DD` | 現有歷史文件與新決策對日期策略有不一致；本 audit 不改歷史事實 |

### 3.3 Raw 與 Journal 的邊界

- `raw/` 的既有原始 Markdown 使用不同 metadata（例如 `source`、`author`、`published`），不是 shared Wiki frontmatter；raw 永遠只讀。
- session handoff 已使用：

```yaml
type: raw-conversation
immutable: true
```

這符合目前決策，應維持為 raw conversation metadata，不要為了 shared Wiki schema 強行改造。
- `journal/daily/` 有自己的 `date` 欄位與每日紀錄格式，不能直接當成 Wiki collection。

### 3.4 格式瑕疵

- `wiki/projects/README.md` 兩條 link 寫成 `[[pi-plannotator-auto/README\|pi-plannotator-auto]]` 與 `[[pi-web-access-zh-tw/README\|pi-web-access-zh-tw]]`；反斜線使其不是正常 alias pipe，屬於可明確修復的格式問題，但本輪不直接修改。
- `wiki/sources/2026-07-11-mattpocock-skills.canvas` 與同 basename 的 Markdown 造成 basename link 歧義；應透過完整 path 或明確命名處理，不應猜測哪一個是歷史 log 想指向的對象。
- `wiki/topics/ai-agent/pi-agent-core.md` 等正文使用的 `minimal-agent-loop`、`observability-layer`、`steering-followup` 等概念頁尚未建立。這是 unresolved reference，不代表應自動建立頁面。

---

## 4. `topics/` 各頁面的未來 collection 建議

以下是「語義歸類建議」，不是本輪遷移命令。所有需要重新命名、拆頁或合併的項目都應保留原頁，待 Cheer 確認後分批處理。

| 目前頁面 | 現有 type | 建議未來 collection | 建議 type | 理由／注意 |
|---|---|---|---|---|
| `topics/ai-agent/pi-mono.md` | entity | `entities/` | entity | 具體 monorepo／產品實體；保留 topic `ai-agent` 作導航標籤 |
| `topics/ai-agent/pi-agent-core.md` | entity | `entities/` | entity | runtime／package 實體，但內容含尚未建立的概念 references |
| `topics/ai-agent/mario-zechner.md` | entity | `entities/` | entity | 人物實體／作者 |
| `topics/ai-agent/plannotator.md` | entity | `entities/` | entity | 外部工具／產品實體；與自有 `pi-plannotator-auto` 專案分開 |
| `topics/ai-agent/pi-web-access-zh-tw.md` | entity | `entities/` | entity | 具體 extension/package 實體；project 狀態另由 project bundle 表示 |
| `topics/knowledge-mgmt/notionApi.md` | entity | `entities/` | entity | MCP server／工具實體；可考慮日後統一大小寫檔名，但需人工確認 link 影響 |
| `topics/knowledge-mgmt/wiki-knowledge.md` | entity | `entities/` | entity | skill 實體／維運工具；內容同時含 procedure，暫不拆分 |
| `topics/meta-systems/omnigent.md` | entity | `entities/` | entity | 具體開源工具／實作案例 |
| `topics/ai-agent/late-conversion.md` | concept | `concepts/` | concept | 可跨專案重用的型別設計抽象 |
| `topics/ai-agent/minimal-agent-philosophy.md` | concept | `concepts/` | concept | 跨專案的設計哲學／抽象；不是單一產品 |
| `topics/extension-dev/agent-extension-installation.md` | concept | `concepts/` | concept | 安裝模型與比較抽象；涉及多 agent，應保留 topic metadata |
| `topics/extension-dev/npm-publishing-workflow.md` | concept | `concepts/` | concept | 發布流程抽象；實作紀錄留在 source/project |
| `topics/extension-dev/okf-open-knowledge-format.md` | concept | `concepts/` | concept | OKF 是全域／跨專案架構概念，建議從 extension-dev topic 下解耦 |
| `topics/knowledge-mgmt/pi-project-workspace-model.md` | concept | `concepts/` | concept | workspace／project 邊界抽象；與 Project Bundle 架構高度相關 |
| `topics/meta-systems/meta-harness.md` | concept | `concepts/` | concept 或 discussion | 核心內容是 concept，但「我們的研究定位／未來樹苗」部分帶 discussion／research backlog 性質；建議先保留單頁，之後由 Cheer 決定是否拆 `discussions/` |

### 4.1 特別注意：topics 不等於 type

目前 `topics/ai-agent/` 同時有 `entity` 與 `concept`，證明 topic 與 type 本來就是兩個維度。未來移至 collections 後，建議把 topic 留在 frontmatter（例如 `topics: [ai-agent]` 或現有 `tags`），不要把 `topics/` 路徑本身當作知識類型。

---

## 5. Shared Wiki 五 collections 建議

| Collection | 首批來源 | 不應放入 |
|---|---|---|
| `wiki/concepts/` | 7 個現有 `type: concept` 頁 | 單一 project 狀態、未確認決策 |
| `wiki/entities/` | 9 個現有 `type: entity` 頁 | 把 package project README 當成 shared entity 的唯一 source of truth |
| `wiki/sources/` | 現有 7 個 source notes | raw 原檔、歷史 log |
| `wiki/decisions/` | 本輪確認的跨專案／全域架構決策 | 尚未確認的 annotator feedback、project-local decisions |
| `wiki/discussions/` | 未定案的架構討論、候選方案、研究問題 | 已確認的架構選擇、raw 原始交談本身 |

本輪沒有把 handoff 直接轉成 `decisions/`：它是 raw conversation，雖然包含已確認方向，但仍應先維持原始 immutable provenance。正式 decision/discussion 頁面要在整理與確認後另建，且透過 `provenance` 回指 raw conversation。

---

## 6. Broken links、合法 links 與 parser 限制

### 6.1 Link 統計

以 28 個 Wiki Markdown 的正文、index、log 全部掃描，總共抽出 **199 次 wikilink**：

| 分類 | 出現次數 | 唯一 target | 說明 |
|---|---:|---:|---|
| 可解析 | 152 | 43 | 包含 vault-root 完整路徑與唯一 basename |
| unresolved basename | 42 | 25 | 16 個頁面出現；其中很多是未建立的未來概念或已刪歷史頁 |
| ambiguous basename | 1 | 1 | `2026-07-11-mattpocock-skills` 同時命中 `.md` 與 `.canvas` |
| malformed escaped alias | 2 | 2 | 僅出現在 `wiki/projects/README.md` |
| **合計** | **199** | | |

若只看非 `index.md`／非 `log.md` 的正文：94 次可解析、39 次 unresolved、2 次 malformed。這個數字較能代表現行內容圖譜，但仍不能把 unresolved 全部當成必修錯誤。

### 6.2 真正 broken 與合法 Obsidian link 的區分

#### 合法、不要誤報為 broken

- `wiki/index.md` 的 20 次完整 path references（例如 `[[topics/ai-agent/pi-mono|pi-mono]]`）在 vault-root 語義下均可解析。
- `wiki/projects/pi-todo-journal/README.md` 的 `[[topics/extension-dev/...]]` 也屬 vault-root 完整 path。
- 以 basename 指向 Wiki 內唯一頁面的 link，例如 `[[pi-mono]]`、`[[plannotator]]`，是合法 Obsidian link。

#### 確定需要處理，但不等於要建立頁面

- 2 個 escaped alias：`wiki/projects/README.md`。
- `2026-07-11-mattpocock-skills`：需要決定 link 應指向 Markdown source note 還是 Canvas，再改為完整 path。
- 25 個 unresolved target：需逐項標成「歷史保留」「未來概念」「應補 canonical page」「應移除／改為純文字」，而不是自動建頁。

### 6.3 Unresolved target 分組

| 分組 | targets | 建議 |
|---|---|---|
| 早期已刪／改名導覽頁：`guide`、`快速開始`、`overview` | 3 | 僅在歷史 log 中保留事實；不要為了修 log 恢復已刪頁 |
| 尚未建立但被多處使用的 Pi runtime 概念：`minimal-agent-loop`、`observability-layer`、`steering-followup`、`yolo-by-default` | 4 | 需要 Cheer 決定是否建立 canonical concept；先不要自動建立 |
| 其他候選概念／抽象：`pi-coding-agent`、`pi-ai`、`pi-tui`、`pi-web-ui`、`provider-abstraction`、`coding-agent-comparison` | 6 | `pi-mono` 頁面已明確說暫不拆；保留 audit finding，不自動建頁 |
| 外部工具／技能候選：`skill-creator`、`notion-cli`、`harness-forge`、`SuperagenticAI-metaharness`、`OmniAgent`、`GEPA`、`MemEx`、`RLM` | 8 | 可能是 entity、source 或 future research item；需先確認來源與 canonical identity |
| project／暫存概念：`pi-plannotator-auto`、`plannotator-seedling`、`wikilink` | 3 | `pi-plannotator-auto` 應使用明確 project path；其餘不應僅因文字出現就建頁 |
| Canvas／其他：`2026-07-11-mattpocock-skills` | 1 | 明確 path 指向 Markdown 或 Canvas |

> 分組是以唯一 target 的治理建議為主；總數 25，實際出現 42 次。

---

## 7. Orphan pages 與 inbound / outbound link 缺漏

### 7.1 Orphan 結果

若把 `index.md` 與 `log.md` 視為特殊導航／歷史層，並把完整 path、basename 正確解析後，以下 3 個頁面沒有任何 inbound link：

1. `wiki/projects/README.md`
2. `wiki/sources/2026-07-11-mattpocock-skills.md`
3. `wiki/sources/2026-07-31-npm-publishing-setup.md`

補充：三個 project README 都有 index inbound；其他 source／concept／entity 頁至少有正文或 index inbound。這表示 orphan 問題集中在 index 沒列出的 source note 與 project navigation，不是整個圖譜斷裂。

### 7.2 明顯 inbound 缺漏

- `wiki/sources/2026-07-31-npm-publishing-setup.md` 在 log 有歷史引用，但 `wiki/index.md` 沒有列出；應由 Cheer 決定它是否仍是正式 source collection 成員。
- `wiki/sources/2026-07-11-mattpocock-skills.md` 有 log 引用及同 basename Canvas，但 index 沒有列出；同時存在 Canvas／Markdown target ambiguity。
- `wiki/projects/README.md` 沒有被 index 或其他頁面連入；若它保留作 collection navigation，應有明確入口，否則可視為舊導航檔而非 orphan content。
- `wiki/sources/2026-08-01-okf-extension-development.md` 由 index 與 log 引用，但正文沒有 outbound wikilink；它仍有 inbound，不是 orphan，但 provenance／相關 project link 不完整。
- `wiki/sources/2026-07-18-pi-resource-inventory.md` 有正文 inbound，但沒有 outbound wikilink；作為 inventory snapshot 可以接受，但若作為 source note，應至少有明確 subject／related links 的治理規則。
- `wiki/topics/extension-dev/okf-open-knowledge-format.md` 有 inbound，但正文沒有 wikilink；它有 raw URL 欄位，表示 provenance 尚未使用統一 Wiki link contract。

### 7.3 明顯 outbound 缺漏

- 15 個 `topics/` 頁面中，`okf-open-knowledge-format.md` 沒有 outbound wikilink；應至少在未來補上 source／project／decision relation，但必須確認 canonical destinations。
- `agent-extension-installation.md`、`npm-publishing-workflow.md`、`pi-project-workspace-model.md` 的 outbound links 數量很低，且部分指向未建頁概念；應先修 link contract，再補語意關係。
- 多數頁面使用 `## 相關頁面`，但來源關係、實作關係、決策關係未分 section。未來建議區分 `Sources`、`Related`、`Implemented by`、`Used in`、`Builds on`。

### 7.4 不應把 log inbound 當成正文 inbound

`wiki/log.md` 的 38 個可解析連結主要是歷史活動索引，不代表當前內容頁的語意關聯。後續 lint 應至少輸出兩組數字：

- `body/index inbound`
- `log-only inbound`

否則會把「只在歷史紀錄出現」誤判成內容頁已經有良好導航。

---

## 8. 可能重複或應合併的概念

以下是候選，不是自動合併指令：

### 8.1 高信心：同一知識單位的不同層次，不宜直接合併

- `pi-mono`、`pi-agent-core`、`mario-zechner`：互相高度相關，但分別是 monorepo、runtime、作者三種 entity，不應因互鏈多就合併。
- `plannotator` 與 `pi-plannotator-auto`：前者是外部工具 entity，後者是自有 project/package；應保持 entity/project 分離。
- `okf-open-knowledge-format` 與 `2026-08-01-okf-extension-development`：前者是可重用 concept，後者是實作／工作紀錄 source；不應合併。
- `meta-harness` 與 `omnigent`：前者是抽象 concept／研究專題，後者是具體 entity／案例；不應合併。

### 8.2 中信心：需要人工判斷拆分或重寫

- `wiki/topics/knowledge-mgmt/wiki-knowledge.md` 與 skill 文件、AGENTS.md 的規範內容有重疊。建議把 `wiki-knowledge` 保持為 entity／skill reference，將現行操作規範視為 skill／AGENTS source of truth，不要在 Wiki 複製完整規則。
- `pi-web-access-zh-tw` entity 與 `wiki/projects/pi-web-access-zh-tw/README.md` project README 內容有部分重疊。未來應讓 entity 保持 reusable identity／capability，project bundle 保持當前狀態、決策、進度。
- `pi-plannotator-auto` project README、OKF source note、`plannotator` entity 有交集，但分層合理；風險是 project README 的決策／狀態與 package repo docs 分叉。
- `minimal-agent-philosophy`、`meta-harness`、`pi-project-workspace-model` 都包含「系統邊界／功能取捨」敘述；這是主題鄰近，不足以證明應合併。

### 8.3 可能需要 canonical identity review 的 target

`OmniAgent`、`omnigent`、`SuperagenticAI-metaharness`、`harness-forge` 可能是不同專案，也可能有名稱／實作關係。未取得足夠 source evidence 前，不應自動建立 entity 或合併。

---

## 9. 歷史 log 連結與正文連結的區分

### 9.1 Log 特性

`wiki/log.md` 是 append-only 歷史紀錄，包含 49 次 wikilink（其中 38 可解析、5 unresolved、1 ambiguous，另以整體 parser 統計可能因特殊語法而略有差異）。它引用：

- 現行頁面，例如 `index`、`meta-harness`、`pi-mono`
- 已刪除或改名頁面，例如 `guide`、`快速開始`、`overview`
- 當時的 source／project path
- 同 basename 的 Markdown／Canvas

### 9.2 治理建議

- 不要為修復歷史 log 而恢復已刪頁。
- 不要重寫歷史 log 以符合今天的 collection path；append-only 的歷史事實應保留。
- 可在未來 lint 報告中標記 `log-only historical unresolved`，並從現行 broken-link gate 排除，或建立明確的 historical-link allowlist。
- 新的 log entry 才採用當時有效的完整 path與 alias；若要描述遷移，新增 migration decision／log entry，不改舊 entry。

---

## 10. 舊 `wiki/projects/` 遷移至 Obsidian `projects/` 的風險

### 10.1 高風險

1. **雙 source of truth**：project README、package repository、package `docs/`、未來 root Project Bundle 可能同時描述狀態。必須明確：package repo／原始碼是 package source of truth；Obsidian root bundle 是跨 session project knowledge；package `docs/` 是隨 package 發布的 bundle；三者不互相依賴。
2. **歷史 links 斷裂**：`wiki/index.md`、`wiki/log.md`、project README 與其他正文使用 `projects/...`，單純搬目錄會改變 vault-root target。
3. **Project-local decision 外溢**：把所有現有 project decisions 搬到 shared `wiki/decisions/` 會違反「局部決策留在 `projects/<project-id>/decisions/`」的邊界。
4. **名稱與身份不穩定**：`pi-plannotator-auto`、`pi-web-access-zh-tw`、`pi-todo-journal` 在 package repository、Wiki entity、project bundle 的命名可能不同；需先定 project-id 與 canonical GitHub URL。

### 10.2 中風險

- 現有 project README 沒有一致 frontmatter，遷移時若自動套用 shared schema，可能把 project metadata 誤當 entity metadata。
- `wiki/projects/README.md` 的 escaped wikilinks 目前已經有格式問題；搬遷會把問題帶到新入口。
- `pi-todo-journal` README 的 Phase 狀態與 `todos/current.md`、package `PLAN.md` 可能隨時間變動；root bundle 不應複製整份 PLAN／todo。
- 未來跨機器引用若使用固定本地路徑，會破壞 portability；應優先使用 GitHub repository URL。
- 搬遷後若保留 redirect stub，Obsidian basename resolution 可能出現重複／歧義；redirect strategy 尚未定案。

### 10.3 低風險前提

只在以下條件成立後，搬移單一 project README 才可視為低風險：

- project-id、GitHub URL、bundle schema 已確認
- 先建立新 bundle，再驗證 inbound/outbound links
- 明確標示舊 path 的處理方式（redirect、stub、歷史保留或一次性 link update）
- project-local decisions、progress、issues 不進 shared collections
- 不改 package repository 的 source of truth

---

## 11. 第一批低風險遷移／整理建議

這裡的「遷移」是建議順序，不是本輪執行結果。

### L0：只修 parser／audit contract，不搬檔

1. 定義 lint resolver：支援 vault-root path、basename、alias、heading/block、`.canvas`，並分開 `index`、`log`、正文統計。
2. 把 2 個 escaped alias 列為明確格式修復項目。
3. 為 `2026-07-11-mattpocock-skills` 指定完整 target，解除 Markdown／Canvas ambiguity；需 Cheer 確認指向哪一個。
4. 建立 unresolved target inventory，不建立新頁。

### L1：低風險的索引與 metadata 整理（需確認後執行）

1. 更新 index 的統計口徑與 source entries；先不搬 `topics/`。
2. 為現有 source notes 補明確 source／provenance link contract，優先處理 `2026-08-01-okf-extension-development`、`2026-07-31-npm-publishing-setup`。
3. 將 `wiki/projects/README.md` 的兩個 escaped links 改為正常 Obsidian alias。
4. 不碰 `raw/`、歷史 log、Canvas 內容。

### L2：先建立 collection skeleton，再逐頁複製／審核

1. 建立空的 `wiki/concepts/`、`wiki/entities/`、`wiki/decisions/`、`wiki/discussions/` 導航／README（是否建立需 Cheer 確認）。
2. 先處理最少歧義的 concept：`late-conversion`、`minimal-agent-philosophy`、`agent-extension-installation`、`npm-publishing-workflow`。
3. 每頁採「新 canonical page + 舊頁暫留／redirect strategy」而非直接 delete；需要確認後才執行。
4. `topics/` 保留 taxonomy index，逐頁加 collection canonical link。

### L3：Entity／source 分層

1. 遷移 entity pages，明確區分外部 entity 與自有 project。
2. source notes 統一 provenance；`sources` 數字欄位是否改成清單需先定 schema。
3. 對 `wiki-knowledge`、`pi-web-access-zh-tw` 等重疊頁做人工內容切分，不批量合併。

### L4：Project Bundle proof-of-concept

建議只選一個低風險、GitHub identity 清楚的 project 做 proof-of-concept，例如 `pi-plannotator-auto`：

```text
projects/pi-plannotator-auto/
├── index.md
├── decisions/
├── discussions/
├── references.md
└── log.md
```

這個 bundle 只保存跨 session project knowledge、project-local decisions 與 provenance；不複製 package source code、完整 PLAN 或 package `docs/`。

### L5：其餘 project 與 discussion／decision 整理

- `pi-todo-journal`：先等 PLAN 相關確認，不把本輪 audit 直接寫入 PLAN。
- `pi-web-access-zh-tw`：先處理外部貢獻者與 repository identity，再決定 bundle。
- annotator handoff：原始檔保留在 raw；確認後再將摘要分流到 decision 或 discussion。

---

## 12. 不應自動遷移、需要 Cheer 確認的項目

1. 是否正式建立 shared `concepts/`、`entities/`、`decisions/`、`discussions/` 目錄，以及第一版 schema。
2. `topics/` 舊頁採 copy、move、redirect stub 或保留導航頁哪一種策略。
3. `wiki/projects/` 是否全部遷移、保留為 legacy navigation，或與 root `projects/` 並存一段時間。
4. `projects/<project-id>/` 的 project-id 命名與 GitHub canonical URL。
5. `2026-07-11-mattpocock-skills` link 應指向 Markdown source note 還是 Canvas。
6. 25 個 unresolved targets 中，哪些是歷史 references、哪些要建立 canonical concept/entity、哪些只保留為文字。
7. 是否建立 `minimal-agent-loop`、`observability-layer`、`steering-followup`、`yolo-by-default` 等頁面。
8. `sources` 欄位維持數字計數，還是改為可追溯 provenance list；若兩者並存，欄位名稱與相容期如何設計。
9. 是否為所有正式 Wiki 頁補 `status`、`description`、`generated`、`verified`；若補，`verified.by` 採 `human:Cheer`。
10. `meta-harness` 是否拆出 research discussion，或維持 concept 單頁。
11. `pi-plannotator-auto`／`pi-web-access-zh-tw` 的 entity page 與 project bundle 的內容界線。
12. 歷史 `log.md` unresolved links 是否採 allowlist／分類報告，而不修改 append-only 內容。
13. 是否將 `wiki/audits/` 納入正式 audit collection 與 index，或只作暫存報告。

---

## 13. 建議後續 migration phases

### Phase A — Contract confirmation

由 Cheer 確認 collection、type、topic、provenance、verified、project bundle 與 link resolver 規則。此階段不搬檔。

### Phase B — Audit baseline and safe link fixes

固定掃描口徑；只修明確 parser／格式問題（escaped alias、選定 Canvas／Markdown target）；不建立推測頁、不修改 raw、不改歷史 log。

### Phase C — Collection skeleton and canonical page pilot

建立 collections，選 3–4 個低歧義 concept 做 pilot。每次搬移都保留可追溯 provenance 與舊 path 策略，完成後再跑 inbound/outbound audit。

### Phase D — Entity/source normalization

逐一處理 entity 與 source notes；補 GitHub URL／外部 URL provenance；處理重疊內容，避免複製 package source of truth。

### Phase E — Project Bundle pilot

只為一個 project 建 root bundle，驗證跨 session／跨本地環境引用、project-local decisions、references 與 package docs 邊界。

### Phase F — Decision/discussion promotion

把已確認的全域架構選擇提升到 `wiki/decisions/`；把尚未定案的候選方案放 `wiki/discussions/`；raw conversation 只作 immutable provenance。

### Phase G — YouTube pipeline gate

在 collection、linking contract、lint、migration 規則穩定前，不建立 YouTube pipeline。完成 gate 後才另行研究獨立 `youtube-to-wiki` skill/workflow。

---

## 14. 完成報告

### 掃描範圍

- `C:/Cheerio/Obsidian/wiki/` 全部 28 個 Markdown、3 個 Canvas
- `wiki/index.md`、`wiki/log.md`、所有 topics/projects/sources 頁面
- `raw/` metadata 與 raw conversation（唯讀檢查）
- `AGENTS.md`、Wiki skill、Obsidian link-related settings、Git tracked files
- 操作前已完成 `git pull --ff-only`

### 統計數字

- Wiki Markdown：**28**
- Wiki Canvas：**3**
- 現有 `topics/` Markdown：**15**
- 現有 `sources/` Markdown：**7**
- 現有 `projects/` Markdown：**4**（含 projects README）
- Frontmatter：**23/28** Wiki Markdown 有共同標準欄位；**5** 無 frontmatter
- Type：**9 entity、7 concept、7 source**
- Wikilinks：**199 次**
- 可解析 links：**152 次**
- unresolved basename：**42 次／25 個唯一 target**
- ambiguous basename：**1 次**
- malformed escaped alias：**2 次**
- 正文／index 視角下 orphan：**3 頁**（projects README、mattpocock source、npm publishing source）
- 目前 shared collections 尚缺：**4 個**（concepts、entities、decisions、discussions）

### 高風險問題

1. `wiki/projects/` 與新 root `projects/` 的 source-of-truth、path、redirect 策略未定。
2. collection、type、topic 尚未正式分離，批量搬移容易把 project／source／concept 混成同一 schema。
3. `sources` 目前只存數字，provenance 仍不夠可追溯。
4. 25 個 unresolved targets 混合未來概念、歷史頁面、外部候選 entity；批量自動修復會製造假 canonical pages。
5. entity／project／package docs 可能重複，造成雙 source of truth。
6. `verified.by` 尚未落地；若自動填值可能違反 `human:Cheer` 決策。

### 低風險問題

1. `wiki/projects/README.md` 兩個 escaped alias 可明確修復，但仍應在確認後操作。
2. index 的統計口徑可重新定義，無需先搬檔。
3. `2026-07-11-mattpocock-skills` 可透過完整 path 解決 Canvas／Markdown ambiguity，但需先選 target。
4. 可先建立 unresolved inventory 與 link resolver contract，不建立新頁。
5. source note 的缺漏 outbound links 可分批補，但必須先決定 provenance schema。

### 建議下一步

1. 先請 Cheer 確認本報告第 12 節的 collection、legacy project path、unresolved target 與 provenance schema 決策。
2. 確認後只做 Phase B 的兩項明確修復：escaped aliases 與選定的 mattpocock full-path link。
3. 接著建立 collection skeleton，再選 3–4 個 concept 做 pilot；每批操作後重新 audit。
4. 暫不修改 `PLAN.md`、不建立 YouTube pipeline、不修改 raw。

### 尚需 Cheer 決策的事項

請優先回答：

- 是否批准先建立四個 shared collection skeleton？
- `wiki/topics/` 舊頁要採「新頁 + 舊頁保留／redirect」還是直接 move？
- root `projects/` pilot 是否選 `pi-plannotator-auto`？
- `2026-07-11-mattpocock-skills` 要連 Markdown source note 還是 Canvas？
- unresolved concepts 是否先全部列為 audit exceptions，不建立頁面？
- `sources` 是否從數字計數升級為明確 provenance list？

---

## 15. Post-migration pilot verification

本報告後續 migration pilot 已完成，以下是重新掃描結果（仍把 audit report 本身排除在 link gate 之外，避免程式碼範例被誤判）：

- Wiki Markdown（audit report 除外）：**49**
- `wiki/concepts/` canonical pages：**7**
- `wiki/entities/` canonical pages：**8**
- `wiki/sources/` source notes：**7**
- `wiki/topics/`：**16**（含 `topics/README.md` 與 15 個 compatibility stubs）
- 現行 index links：**31 次，全部可解析**
- 正文 links：**133 次可解析、35 次 unresolved、2 次仍受 `index`／`log` basename 特殊語義影響**
- `log.md`：歷史 links 仍有 **3 個已刪除導覽頁**（`guide`、`快速開始`、`overview`）與多個 legacy basename references；不改寫 append-only 歷史
- Project Bundle links：**全部可解析**

### Pilot 後的判斷

1. Collection canonical pages 與 root Project Bundle 已建立，且 package repository／package docs／Obsidian bundle 邊界維持不變。
2. Unresolved body targets 目前仍是已知 audit exceptions，未自動建頁；這符合原先的治理決策。
3. 舊 topic stubs 會造成 basename ambiguity，因此後續若要將 compatibility stubs 移除或改成明確 redirect，需要 Cheer 另行確認。
4. `.obsidian/graph.json` 有一個外部工作區變更（`scale` 數值），不屬於本 migration pilot，應在 commit 前由 Cheer 決定保留或還原。

## 附錄 A：原始工作樹狀態

Audit 開始前 `git status --short` 顯示 `raw/conversations/` 為未追蹤項目；本輪沒有修改或加入該 raw 路徑。Audit report 是本輪先前產生的報告；本次 migration pilot 另新增 canonical collections 與 Project Bundle。
