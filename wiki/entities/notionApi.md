---
title: notionApi — Notion MCP 伺服器
type: entity
created: 2026-07-18
updated: 2026-07-18
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
- 適合「查詢 Notion 專案進度、會議記錄、規格文件」等唯讀場景

## 安全性考量
- **刻意移除所有寫入/修改/刪除工具** → 避免 AI 誤操作生產資料
- Token 存放在本機 `mcp.json`，不應 commit 到 repo（已在 `.gitignore`）

## 相關頁面
- Source: [[sources/2026-07-18-pi-resource-inventory]]
- Entities: [[entities/wiki-knowledge]], [[entities/pi-web-access-zh-tw]]
- Skills: `notion-cli`（CLI 替代方案，可寫入；尚未建立 Wiki entity 頁）