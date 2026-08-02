---
title: 2026-07-18 Pi 資源完整盤點
type: source
created: 2026-07-18
updated: 2026-07-18
sources: 1
tags: [pi, inventory, configuration, packages, skills, mcp, wiki]
collection: sources
provenance_session: "Pi resource inventory session on 2026-07-18"
---

> 來源：人類在 Pi 互動模式下要求「檢視我們現在擁有的所有資源」，Agent 掃描專案目錄、Pi 設定目錄、Obsidian 知識庫，彙整而成。

## 概覽

本筆記記錄 2026-07-18 當下 Pi 環境的完整資源清單，包含：
- Pi 全域設定與已安裝套件
- 本地開發的套件（pi-web-access-zh-tw）
- 全域 Skills
- Obsidian 知識庫結構
- MCP 伺服器設定
- 當前工作目錄狀態

---

## 1. Pi 全域設定 (`C:/Users/User/.pi/agent/`)

### 核心設定檔
| 檔案 | 用途 |
|------|------|
| `settings.json` | 模型、主題、thinking level、已安裝套件清單 |
| `AGENTS.md` | 全域專案守則（指向 `C:/Cheerio/Obsidian/` 知識庫） |
| `mcp.json` | MCP 伺服器設定（Notion API） |
| `auth.json` | 認證資訊 |
| `models-store.json` | 模型目錄快取 |
| `mcp-cache.json` | MCP 快取 |
| `bin/fd.exe` | `fd` 搜尋工具 |

### 目前模型設定（來自 `settings.json`）
- **Provider**: `openrouter`
- **Model**: `nvidia/nemotron-3-ultra-550b-a55b:free`
- **Thinking Level**: `high`
- **Theme**: `light/light`
- **Images**: `blockImages: true`
- **Thinking Block**: 顯示（`hideThinkingBlock: false`）

### 已安裝套件（12 個）

| 套件 | 類型 | 來源 | 狀態 |
|------|------|------|------|
| `@pi-agents/loop` | 套件 | npm | ✅ |
| `@plannotator/pi-extension` | 擴充 | npm | ✅ |
| `@juicesharp/rpiv-ask-user-question` | 擴充 | npm | ✅ |
| `pi-subagents` | 套件 | npm | ✅ |
| `pi-claude-code-tui` | 擴充 | npm | ✅ |
| `pi-btw` | 擴充 | npm | ✅ |
| `pi-mcp-adapter` | 擴充 | npm | ✅ |
| `pi-chrome` | 擴充 | npm | ✅ |
| `@juicesharp/rpiv-todo` | 擴充 | npm | ✅ |
| `@quintinshaw/pi-dynamic-workflows` | 套件 | npm | ✅ |
| `@hypabolic/pi-hypa` | 套件 | npm | ✅ |
| **`pi-web-access-zh-tw`** | **本地套件** | `file:../../../Cheerio/pi-web-access-zh-tw` | **✅ 已安裝啟用** |

---

## 2. 本地套件：pi-web-access-zh-tw (`C:/Cheerio/pi-web-access-zh-tw/`)

**你維護的繁體中文本地化版本**，已安裝到 Pi 全域。

### 套件資訊
- **版本**: `0.13.0-zh-tw.1`
- **GitHub**: `CheerioCorner/pi-web-access-zh-tw`（私有 repo）
- **上游**: `nicobailon/pi-web-access`
- **已完成項目**（參考 `PLAN.md`）：
  1. ✅ 翻譯 `curator-page.ts` 全部 UI 文字為繁體中文
  2. ✅ 建立 GitHub repo 並推送
  3. ✅ 建立 `zh-TW-changes.diff` 更新 patch
  4. ✅ 建立 `apply-zh-TW.mjs` 自動化腳本
  5. ✅ 安裝到 Pi（`pi install .` from local dir）
  6. ✅ Extension `index.ts` 已啟用
  7. ✅ Skill `librarian` 自動啟用（從 package.json pi.skills 偵測）
  8. ✅ 補齊 HTML 範本 + JS 範本字串翻譯
  9. ✅ AI 摘要提示詞改為繁體中文（`summary-review.ts`）
  10. ✅ 推送至 GitHub

### 功能模組（Extension + Skill）
| 模組 | 檔案 | 功能 |
|------|------|------|
| **Extension** | `index.ts` | 註冊 web search、fetch、GitHub、PDF、YouTube、本地影片等工具 |
| **Skill: librarian** | `skills/librarian/SKILL.md` | 程式庫研究技能（GitHub permalink、原始碼引用） |
| 搜尋提供者 | `openai-search.ts` `brave.ts` `parallel.ts` `tavily.ts` `exa.ts` `perplexity.ts` `gemini-search.ts` | 7 大搜尋引擎 |
| 內容擷取 | `extract.ts` `gemini-web.ts` `gemini-url-context.ts` `github-api.ts` `github-extract.ts` `youtube-extract.ts` `video-extract.ts` `pdf-extract.ts` `rsc-extract.ts` | 多格式內容擷取 |
| 工具鏈 | `curator-server.ts` `curator-page.ts` `activity.ts` `storage.ts` `fetch-params.ts` `utils.ts` | 策展伺服器、UI、狀態管理 |

---

## 3. 全域 Skills (`C:/Users/User/.agents/skills/`)

| Skill | 位置 | 用途 |
|-------|------|------|
| `wiki-knowledge` | `wiki-knowledge/SKILL.md` | **Obsidian 知識庫操作**（ingest / query / lint） |
| `notion-cli` | `notion-cli/SKILL.md` | Notion CLI (`ntn`) 操作 |
| `skill-creator` | `skill-creator/SKILL.md` | 建立/修改/評測 Skills |
| `find-skills` | `find-skills/SKILL.md` | 發現/安裝 Skills |

> 注：`wiki-knowledge` 是目前維護本知識庫的核心 skill，封裝了 ingest / query / lint 三大標準流程。

---

## 4. Obsidian 知識庫 (`C:/Cheerio/Obsidian/`)

### 三層架構
```
Obsidian/
├── raw/           # 唯讀原始資料（4 個檔案）
├── wiki/          # LLM 維護的結構化筆記（11 頁面）
└── AGENTS.md      # 工作守則
```

### raw/ 來源資料（4 筆）
| 檔案 | 日期 | 內容 |
|------|------|------|
| `2026-02-10-pi-agent-core-design.md` | 2026-02-10 | 知乎王鵬針對 `packages/agent/src/` 五支檔應用碼級解剖 |
| `2026-05-02-pi-mono-framework-tw.md` | 2026-05-02 | ai-chain.tw 繁中長文，從整個 monorepo 與代理人生態看 Pi |
| `2026-07-11-pi-mono-intro.html` | 2026-07-11 | Pi 官方簡介頁面 |
| `2026-07-13-omnigent-meta-harness-databricks.md` | 2026-07-13 | Databricks 官方部落格，宣布開源 Omnigent meta-harness |

### wiki/ 結構化筆記（11 頁）

**Sources（4 頁）**
- `2026-02-10-pi-agent-core-design.md`
- `2026-05-02-pi-mono-framework-tw.md`
- `2026-07-11-plannotator-research.md`
- `2026-07-13-omnigent-meta-harness-databricks.md`

**Entities（5 頁）**
- `pi-mono.md` — badlogic/pi-mono monorepo + 5 子 package
- `pi-agent-core.md` — agent runtime, 5 檔 / 1,500 行
- `mario-zechner.md` — 作者
- `plannotator.md` — AI coding agent 視覺化審閱工具
- `omnigent.md` — Databricks 開源 meta-harness

**Concepts（3 頁）**
- `late-conversion.md` — TS Declaration Merging 實作的「最晚轉換」型別策略
- `minimal-agent-philosophy.md` — 「減法大於加法」哲學
- `meta-harness.md` — 對 harness 的再抽象/最佳化層

**其他**
- `index.md` — 內容索引
- `log.md` — 時間日誌
- `overview.md` — 總覽
- `plannotator.md` — 研究筆記
- `2026-07-11-plannotator-research.md` — 研究筆記
- `canvas/` — 兩個 Canvas 檔

### Git 同步
- **Remote**: `https://github.com/CheerioCorner/cheerio-wiki`（私有 repo）
- **Branch**: `master`
- **SOP**: 操作前 `git pull`，操作後 `git add -A && git commit && git push`

---

## 5. MCP 伺服器設定

### notionApi (`mcp.json`)
```json
{
  "command": "npx",
  "args": ["-y", "@notionhq/notion-mcp-server"],
  "env": { "NOTION_TOKEN": "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" },
  "lazy": true,
  "excludeTools": [
    "API-patch-block-children",
    "API-update-a-block",
    "API-delete-a-block",
    "API-patch-page",
    "API-post-page",
    "API-create-a-comment",
    "API-update-a-data-source",
    "API-create-a-data-source",
    "API-move-page",
    "API-update-page-markdown"
  ]
}
```
- **工具數**: 14 個（已排除 9 個寫入類工具）
- **狀態**: lazy connect（首次使用時連線）

---

## 6. 當前工作目錄 (`C:/Cheerio/pi/`)

| 檔案 | 說明 |
|------|------|
| `PLAN.md` | pi-web-access-zh-tw 專案完成總結 |
| `search_result.json` `search_all.json` | 搜尋快取 |
| `notion-reorg/` | Notion 重組相關規劃文件 |
| `.pi-loop.json.lock` | loop 排程鎖檔 |

---

## 7. 關鍵實體關聯圖譜

```
Pi 全域設定 (settings.json)
    │
    ├── 已安裝套件 (12 個)
    │       └── pi-web-access-zh-tw (本地套件) ──→ Extension + Skill:librarian
    │
    ├── MCP: notionApi ──→ Notion workspace
    │
    └── AGENTS.md ──→ Obsidian 知識庫 (C:/Cheerio/Obsidian/)
                            │
                            ├── raw/ (4 筆來源)
                            ├── wiki/ (11 頁面)
                            │       └── 由 wiki-knowledge skill 維護
                            └── GitHub: CheerioCorner/cheerio-wiki
```

---

## 待補充/後續行動

1. **將本盤點存入 wiki** → 建立 entity 頁面給 `pi-web-access-zh-tw`、`wiki-knowledge` skill、`notionApi` MCP
2. **更新 `wiki/index.md`** 加入新實體與來源
3. **同步推送至 GitHub**
4. 考慮是否為 `pi-subagents`、`pi-chrome`、`pi-mcp-adapter` 等常用套件建立 entity 頁
5. 定期 `lint wiki` 檢查健康度