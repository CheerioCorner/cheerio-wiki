---
title: "在 API 中開始使用 Agent Skills — Quickstart"
type: source
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [skill, anthropic, api, quickstart]
topics: [skill]
provenance_raw: "raw/web/2026-08-14-在api中開始使用agent-skills.md"
---

# 在 API 中開始使用 Agent Skills — Quickstart

> Source: [在 API 中開始使用 Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart)
> Clipped: 2026-08-14

## 重點摘要

10 分鐘快速入門教學，展示如何透過 Claude API 使用 Agent Skills 建立 PowerPoint 簡報。

### 三步驟流程

1. **列出可用 Skills**：`ant beta:skills list --source anthropic`
2. **建立簡報**：在 Messages API 中使用 `container` 參數指定 Skills
3. **下載檔案**：使用 Files API 下載生成的 `.pptx` 檔案

### 關鍵參數

```yaml
model: claude-opus-5
max_tokens: 16000
container:
  skills:
    - type: anthropic
      skill_id: pptx
      version: latest
tools:
  - type: code_execution_20260521
    name: code_execution
```

### Beta Header

- `skills-2025-10-02`：啟用 Skills 功能
- `files-api-2025-04-14`：使用 Files API 時需要

### 其他變體

- 建立試算表：`skill_id: xlsx`
- 建立 Word 文件：`skill_id: docx`
- 生成 PDF：`skill_id: pdf`

## 相關頁面

- [[wiki/sources/2026-08-14-agent-skills-api-guide|API 完整指南]] — 深入版
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]]
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]]
