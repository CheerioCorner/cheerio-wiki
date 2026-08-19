---
title: "Agent Skills API 使用方法"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 2
tags: [skill, anthropic, api, container, versioning, prompt-caching]
topics: [skill-system]
canonical: concepts/agent-skills-api-usage
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-在api中開始使用agent-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-透過api使用agent-skills.md"
---

# Agent Skills API 使用方法

> 透過 Claude Messages API 呼叫與管理 Agent Skills 的技術機制：container 參數、多輪對話、版本管理與快取。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**

## 整合架構

Skills 透過 Messages API 的 `container` 參數指定，在程式碼執行容器中運行：

```yaml
model: claude-opus-5
container:
  skills:
    - type: anthropic       # 或 "custom"
      skill_id: pptx        # 或 "skill_01AbCd..."
      version: latest       # 或特定版本 ID
messages:
  - role: user
    content: "Create a presentation"
tools:
  - type: code_execution_20250825
    name: code_execution
```

### 先決條件

1. Claude API 金鑰
2. Beta Headers：
   - `code-execution-2025-08-25`：啟用程式碼執行（Skills 必需）
   - `skills-2025-10-02`：啟用 Skills API
   - `files-api-2025-04-14`：使用 Files API 時

### 限制

- 每個請求最多 **8 個 Skills**
- 上傳大小 < **30 MB**
- 無網路存取、無執行時套件安裝

## Anthropic vs 自訂 Skills

| 面向 | Anthropic Skills | 自訂 Skills |
|------|-----------------|------------|
| Type 值 | `anthropic` | `custom` |
| Skill ID | 簡短名稱：`pptx`/`xlsx`/`docx`/`pdf` | 自動產生：`skill_01AbCdEf...` |
| 版本格式 | 日期：`20251013` 或 `latest` | Epoch 時間戳記或 `latest` |
| 管理方式 | Anthropic 預建維護 | Skills API CRUD |
| 可用性 | 所有使用者 | 僅限工作區 |

## 關鍵操作

### 多輪對話（Container Reuse）

透過 `container.id` 復用容器，延續上下文：

```yaml
container:
  id: $CONTAINER_ID    # 從前一個回應取得
  skills:
    - type: anthropic
      skill_id: xlsx
```

### 長時間運行操作（pause_turn）

回應可能包含 `pause_turn` stop reason，表示 Skill 操作暫停。在後續請求中原樣提供該回應即可繼續。

### 使用多個 Skills

單一請求可組合多個 Skills 處理複雜工作流：

```yaml
container:
  skills:
    - {type: anthropic, skill_id: xlsx}
    - {type: anthropic, skill_id: pptx}
    - {type: custom, skill_id: skill_01AbCd...}
```

## 自訂 Skills 管理（CRUD API）

### 建立

```bash
ant beta:skills create --file skill.zip --beta skills-2025-10-02
```

需求：頂層 `SKILL.md`、目錄名稱與 `name` frontmatter 相符。

### 列出 / 擷取 / 刪除

```bash
ant beta:skills list [--source custom]
ant beta:skills retrieve --skill-id <id>
ant beta:skills delete --skill-id <id>    # 須先刪所有版本
```

### 版本管理

- 新版本是**完整快照**（非差異更新）
- 生產環境：固定特定版本
- 開發環境：使用 `latest`

```yaml
# 生產：固定版本
version: "1759178010641129"

# 開發：自動最新
version: latest
```

## 提示快取考量

- 相同 Skills 清單（含順序）→ 相同可快取前綴 → **快取命中**
- 變更清單 → 前綴改變 → **快取未命中**
- 固定版本也有助於快取穩定

## 檔案處理流程

Skills 建立的檔案透過 Files API 下載：

1. Skills 在容器中建立檔案
2. 回應包含 `file_id`
3. 使用 `ant beta:files download --file-id <id>` 下載

## 來源

- [[wiki/sources/2026-08-14-agent-skills-api-quickstart|API Quickstart]] — 入門教學
- [[wiki/sources/2026-08-14-agent-skills-api-guide|API 完整指南]] — 完整技術文件

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Skill 系統本體
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]]
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Model Context Protocol
