---
title: pi-web-access-zh-tw — Web 存取擴充的繁體中文本地化版
type: entity
created: 2026-07-18
updated: 2026-07-18
sources: 1
tags: [pi, extension, web-search, localization, zh-tw, package]
---

> **你維護的 `pi-web-access` 繁體中文本地化套件**，已安裝到 Pi 全域（`pi install file:../../../Cheerio/pi-web-access-zh-tw`）。
> 上游：`nicobailon/pi-web-access`，你的 repo：`CheerioCorner/pi-web-access-zh-tw`（私有）。

## 版本與安裝
- **版本**: `0.13.0-zh-tw.1`
- **安裝方式**: 本地目錄安裝 → 登記在 `~/.pi/agent/settings.json` 的 `packages` 陣列
- **啟用狀態**: Extension (`index.ts`) 已載入，Skill `librarian` 自動偵測啟用

## 核心功能（繼承上游 + 本地化）

### Extension (`index.ts`)
註冊以下工具給 Pi：
| 工具 | 功能 |
|------|------|
| `web_search` | 多引擎搜尋（OpenAI、Brave、Parallel、Tavily、Exa、Perplexity、Gemini） |
| `fetch_content` / `fetch_content_multi` | URL 內容擷取（支援 YouTube、GitHub、PDF、本地影片） |
| `get_search_content` | 取得前一次搜尋/擷取的完整內容 |

### Skill: librarian (`skills/librarian/SKILL.md`)
> **程式庫研究技能**——提供有證據、有 GitHub permalink 的權威回答。
> - 適用情境：使用者詢問函式庫內部實作、需要原始碼引用、想知道為何某改變、需權威答案
> - 核心能力：導航大型開源 repo、提供精確行號引用

## 本地化完成項目（截至 2026-07-18）

| 項目 | 狀態 | 檔案 |
|------|------|------|
| Curator UI 介面翻譯 | ✅ | `curator-page.ts` |
| AI 摘要提示詞 → 繁中 | ✅ | `summary-review.ts` |
| HTML 範本翻譯 | ✅ | `curator-page.ts` + `apply-zh-TW.mjs` |
| JS 範本字串翻譯 | ✅ | `apply-zh-TW.mjs` |
| ARIA labels 翻譯 | ✅ | `apply-zh-TW.mjs` |
| README 安裝說明修正 | ✅ | `README.md` |
| 自動化套用腳本 | ✅ | `apply-zh-TW.mjs` |
| GitHub repo 建立與推送 | ✅ | `CheerioCorner/pi-web-access-zh-tw` |

## 架構亮點（來自原始碼掃描）

### 搜尋提供者抽象 (`fetch-params.ts` / 各 provider 檔)
- 統一介面：`SearchProvider` → `search(params) -> Promise<SearchResult[]>`
- 內建 7 大提供者，可擴充

### 內容擷取管線
```
URL → 偵測類型 → 選擇擷取器
  ├─ GitHub → github-extract.ts (API / 克隆)
  ├─ YouTube → youtube-extract.ts (yt-dlp + transcript)
  ├─ 影片 → video-extract.ts (ffmpeg 抽幀 + Gemini 分析)
  ├─ PDF → pdf-extract.ts (unpdf)
  └─ 一般網頁 → extract.ts (readability + linkedom + turndown)
```

### 策展伺服器 (`curator-server.ts` / `curator-page.ts`)
- 本地 HTTP 伺服器提供互動式審查 UI
- 支援 `summary-review` / `auto-summary` / `none` 三種工作流
- WebSocket 即時更新前端狀態

## 相關頁面
- Source: [[2026-07-18-pi-resource-inventory]]
- Entities: [[wiki-knowledge]], [[notionApi]]
- Concepts: [[provider-abstraction]]
- Packages: [[pi-mono]] (pi-web-access 是獨立擴充，非 pi-mono 子 package)