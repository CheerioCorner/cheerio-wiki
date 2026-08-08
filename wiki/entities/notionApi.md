---
title: notionApi — Notion MCP 伺服器
type: entity
created: 2026-07-18
updated: 2026-08-02
sources: 1
tags: [pi, mcp, notion, api, integration]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/notionApi
---

> **MCP 伺服器**：`notionApi`，在 `~/.pi/agent/mcp.json` 設定，提供 Notion API 存取能力。

## 設定詳情 (`mcp.json`)
```json
{
  "mcpServers": {
    "notionApi": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_TOKEN": "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"
      },
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
  }
}
```

## 關鍵特性
| 設定 | 值 | 說明 |
|------|-----|------|
| `lazy` | `true` | 按需連線，不在啟動時建立連線 |
| `excludeTools` | 9 個寫入工具 | **僅保留讀取類工具**，安全性考量 |
| Token 格式 | `ntn_...` | Notion Internal Integration Token |

## 可用工具（14 個，扣除 9 個寫入類）
| 工具 | 功能 |
|------|------|
| `API-retrieve-a-page` | 讀取頁面 |
| `API-query-a-database` | 查詢資料庫 |
| `API-list-block-children` | 列出區塊子項 |
| `API-search` | 搜尋 |
| `API-list-users` | 列出用戶 |
| ... | 其他 9 個讀取類端點 |

## 使用情境
- 透過 Pi 直接查詢 Notion 資料庫、讀取頁面內容、搜尋工作區
- 結合 `wiki-knowledge` skill 可將 Notion 內容 ingest 到 Obsidian wiki
- 結合 `knowledge-garden` skill 可維護 Notion 知識花園
- 適合「查詢 Notion 專案進度、會議記錄、規格文件」等唯讀場景

## 安全性考量
- **刻意移除所有寫入/修改/刪除工具** → 避免 AI 誤操作生產資料
- Token 存放在本機 `mcp.json`，不應 commit 到 repo（已在 `.gitignore`）

## CheerioPi Integration

- **Integration 名稱：** CheerioPi
- **Integration ID：** `39e5979e-3a8c-81e9-95f2-00279e993de5`
- **頁面共享方式：** 在 Notion 頁面 → `⋯` → Connections → 搜尋 CheerioPi → 加入
- **⚠️ 錯誤處理：** 如果遇到 404 `object_not_found`，表示頁面尚未共享給 CheerioPi

## 完整 Workflow：MCP + ntn CLI

| 操作 | 工具 | 命令範例 |
|------|------|----------|
| 讀取頁面 | MCP `notionApi` | `notionApi_API-retrieve-a-page` |
| 讀取 Markdown | MCP `notionApi` | `notionApi_API-retrieve-page-markdown` |
| 搜尋 | MCP `notionApi` | `notionApi_API-post-search` |
| 建立頁面 | `ntn` CLI | `ntn pages create --parent page:<id> --content '<md>'` |
| 更新頁面 | `ntn` CLI | `ntn pages update <id> --content '<md>'` |
| 設定 icon | `ntn` CLI | `ntn api v1/pages/<id> -X PATCH -d '{"icon":...}'` |

**原則：讀用 MCP，寫用 `ntn` CLI。**

## 資料流

### 兩個系統的分工
| 系統 | 用途 | 主要使用者 |
|------|------|-----------|
| **Obsidian Wiki** | AI 運作、知識維護、raw → wiki 整理 | AI（讀寫） |
| **Notion 花園** | 人類閱讀、報告、知識分享、美化呈現 | 人類（閱讀） |

### 完整循環

```
raw/ ──wiki ingest──► wiki/ ──美化/整理──► Notion 花園
 ▲                     │                      │
 │                     │                      │
 │    ┌────────────────┘                      │
 │    │                                       │
 │    └──── 「這個要深入研究」 ◄───────────────┘
 │          （人類給 URL 或指定頁面）
 │          （AI 去抓內容）
 │
 └─────────────────────────────────────────────┘
```

### 觸發詞
| 人類說 | AI 做 | 資料流 |
|--------|-------|--------|
| 「存進大腦」 | wiki ingest | raw → wiki |
| 「整理到花園」/「美化」 | 整理 + 美化 | wiki → Notion |
| 「花園裡有什麼」 | 查 manifest | wiki manifest → 回答 |
| 「花園裡那篇 [X] 要深入研究」 | 去 Notion 抓內容 → 建 raw | Notion → raw → wiki |

## 相關頁面
- Source: [[wiki/sources/2026-07-18-pi-resource-inventory]]
- Entities: [[wiki/entities/wiki-knowledge]], [[wiki/entities/pi-web-access-zh-tw]], [[wiki/entities/knowledge-garden]]
- Skills: `knowledge-garden`（花園維護）、`notion-cli`（CLI 參考）、`notion-page-content`（頁面內容產生）、`knowledge-garden-trigger`（研究觸發）、`knowledge-garden-visualmap`（視覺地圖）
- Discussions: [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]]