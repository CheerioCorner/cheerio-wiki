---
title: "Skill 撰寫最佳實踐"
type: source
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [skill, anthropic, best-practices, authoring]
topics: [skill]
provenance_raw: "raw/web/2026-08-14-skill-撰寫最佳實踐.md"
---

# Skill 撰寫最佳實踐

> Source: [Skill 撰寫最佳實踐](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices)
> Clipped: 2026-08-14

## 重點摘要

撰寫高品質 Agent Skill 的完整方法論，涵蓋結構規範、內容指引、常見模式、評估迭代與反模式。

### 核心原則

1. **簡潔是關鍵**：Context window 是公共資源，只添加 Claude 尚未擁有的上下文
2. **設定適當自由度**：高自由度（啟發式）vs 低自由度（特定腳本），匹配任務脆弱性
3. **跨模型測試**：Haiku/Sonnet/Opus 各有不同需求

### 結構規範

- YAML frontmatter：`name`（≤64 字元）+ `description`（≤1024 字元）
- SKILL.md 主體 < 500 行
- 額外內容拆分為獨立檔案，參考距離 SKILL.md 只一層深

### 命名慣例

推薦**動名詞形式**：`processing-pdfs`、`analyzing-spreadsheets`

### 撰寫有效描述

- 始終使用**第三人稱**
- 同時包含功能**以及**何時使用
- 具體並包含關鍵詞

### 常見模式

- **範本模式**：嚴格 vs 彈性指引
- **範例模式**：輸入/輸出配對
- **條件式工作流程**：決策點引導
- **驗證循環**：執行驗證器 → 修復 → 重複

### 評估與迭代

1. 識別差距（無 Skill 時的失敗）
2. 建立 3 個評估情境
3. 建立基準
4. 撰寫最少指示
5. 迭代改進

**Claude A+B 迭代法**：Claude A 協助設計，Claude B 在真實任務中測試。

### 反模式

- ❌ Windows 風格路徑（用正斜線）
- ❌ 提供太多選項
- ❌ 深層巢狀參考
- ❌ 時效性資訊
- ❌ 不一致術語

## 相關頁面

- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 概念整合頁
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]]
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]]
