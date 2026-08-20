---
title: "OpenWiki（LangChain）— Agent 專用 Repo Wiki CLI"
type: entity
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [langchain, openwiki, okf, cli, agent-documentation, npm]
topics: [okf, agent-runtime-implementations]
provenance_raw: "raw/youtube/2026-08-20-langchain-openwiki-cli-for-code-wikis.md"
---

# OpenWiki（LangChain）— Agent 專用 Repo Wiki CLI

> LangChain 團隊開發的開源 CLI 工具，自動產生和維護 repo wiki，專為 AI agent 消費設計，採用 Google OKF v0.2 格式。

## 基本資訊

| 欄位 | 值 |
|------|-----|
| 開發者 | LangChain |
| 授權 | MIT |
| 安裝 | `npm install openwiki` |
| 命令 | `openwiki init` / `openwiki update` |
| LLM 支援 | 10-15 個 provider |
| 格式標準 | Google OKF v0.2 |

## 文件結構

```
wiki/
├── quickstart.md          # agent 入口
├── index.md               # 目錄索引
├── log.md                 # 變更歷史
├── <topic-1>.md           # 單一主題文件
├── <topic-2>.md
└── <subdirectory>/        # 分類目錄
    ├── index.md
    └── <concept>.md
```

每個檔案都有 OKF YAML frontmatter（type, title, description, resource, tags, timestamp）。

## 工作流程

### Init（首次設定）
1. 設定 wizard：API keys → model → wiki brief `[04:35]`
2. 自動寫入：AGENTS.md / CLAUDE.md + GitHub Actions workflow `[04:47]`
3. Agent 讀取 repo + git history → 產生 wiki `[13:14]`
4. 確定性 pass：OKF 合規檢查 + index.md + log.md `[13:40]`

### Update（日常維護）
1. GitHub Actions daily cron `[12:48]`
2. 檢查 git history 是否有變更 `[14:14]`
3. 有變更 → agent 更新 wiki → 自動開 PR `[14:47]`

## Eval 結果（DeepSWE）

| 指標 | 無 OpenWiki | 有 OpenWiki |
|------|------------|------------|
| 成功任務（/20） | 7-8 | 9-10 |
| Token 消耗 | 高 | 顯著下降 |

## 設計教訓

- **原假設**：只有 agent 會讀 wiki `[10:13]`
- **實際**：人類也想讀 → 加入 diagrams（序列圖、狀態圖、流程圖）`[10:42]`
- **Agent 導向 vs 人類導向**的文件設計差異是重要的實務教訓

## 相關頁面

- [[wiki/sources/2026-08-20-langchain-openwiki-cli|來源筆記]] — 完整逐字稿分析
- [[wiki/concepts/okf-open-knowledge-format|OKF]] — OpenWiki 採用的知識格式標準
- [[wiki/topics/okf|OKF Topic]] — OKF 導航頁
