---
title: pi-project-workspace-model — Pi 專案/工作區模型
type: concept
created: 2026-07-18
updated: 2026-07-18
sources: 1
tags: [pi, coding-agent, project-model, workspace]
---

> Pi 的「專案」概念**不強制綁定單一實體路徑**。專案 = **設定作用域**（`.pi/` 目錄存在的位置），而非工作目錄本身。這使得「一個實體路徑對應多個專案人格」成為可能。

## 核心定義

| 層面 | 行為 |
|------|------|
| **專案根目錄** | 啟動 `pi` 時的 `cwd`（當前工作目錄） |
| **專案配置** | `.pi/settings.json`、`.pi/extensions/`、`.pi/skills/`、`.pi/prompts/`、`.pi/themes/` |
| **Session 儲存** | `~/.pi/agent/sessions/` 以 `cwd` 為鍵分組 |
| **專案信任** | `~/.pi/agent/trust.json` 以**絕對路徑**為鍵記錄信任決策 |
| **Context 檔案** | 從 `cwd` 向上走到根目錄，收集所有 `AGENTS.md` / `CLAUDE.md` |

> **關鍵洞察**：Pi 沒有內建「multi-root workspace」（多根工作區）概念。不像 VS Code 可同時開啟多個資料夾作為一個 workspace。

## 專案 ≠ 實體路徑的三種體現

### 1. 同一路徑，多套設定（專案人格切換）
```bash
~/projects/my-app/           # 唯一實體路徑
  ├─ .pi/                    # 專案 A 設定（前端人格）
  │   ├─ settings.json       # theme=dark, model=claude-sonnet
  │   └─ skills/frontend/
  ├─ .pi.backend/            # 專案 B 設定（後端人格，手動切換）
  │   ├─ settings.json       # theme=light, model=gpt-4o
  │   └─ skills/backend/
  └─ src/
```
**切換方式**：
- 重命名目錄：`mv .pi .pi.frontend && mv .pi.backend .pi`
- 或用 `--no-extensions -e ./my-ext.ts` 臨時載入特定 extension
- 或寫啟動腳本 `pi-frontend.sh` / `pi-backend.sh` 設定不同 `PI_CODING_AGENT_DIR`

### 2. Session 與專案解耦
- `pi --session <id>` 可載入**任意** session 檔案，不管當下 `cwd` 是什麼
- `/fork`、`/clone` 可從任意 session 分支出新 session
- **實務**：專案 A 的目錄下，載入專案 B 的 session 繼續對話

### 3. 向上查找機制 = 半個 Multi-root 體驗
```
~/workspace/
  ├─ monorepo/
  │   ├─ packages/
  │   │   ├─ frontend/     ← 這裡啟動 pi
  │   │   └─ backend/
  │   ├─ .pi/skills/       ← monorepo 共用 skills
  │   └─ AGENTS.md         ← monorepo 共用指令
  └─ other-project/
```
在 `frontend/` 啟動 pi 會自動撿到上層的 skills、prompts、AGENTS.md。**這就是零成本的「專案群共用配置」**。

## 實際工作流建議

| 需求 | 實作方式 |
|------|----------|
| **單機多專案快速切換** | `zoxide` / `z` + `tmux` / `zellij` 分頁，每分頁一個 pi session |
| **Monorepo 共用配置** | 把 `.pi/skills/`、`prompts/`、`AGENTS.md` 放在 monorepo 根目錄 |
| **跨專案共用知識** | 用 **Obsidian wiki**（`wiki-knowledge` skill）或共用 pi package（`pi install npm:@org/shared-skills`） |
| **真正的 Multi-root** | 寫 Extension 掛載多路徑，或用 `pi-sdk` 自己寫啟動器 |

## 設計哲學對應

| Pi 哲學條款 | 對專案模型的影響 |
|------------|------------------|
| **「適應你的工作流，不要 fork pi 內核」** | 專案模型故意極簡，留白給 Extension 實作任意 workspace 抽象 |
| **「無 MCP、無 sub-agents、無 plan mode」** | 專案不內建跨專案編排能力，若需跨專案協作靠 `bash` 自呼叫或 Extension |
| **「YOLO by default」** | 專案信任決策一次性，之後不再干擾；信任即載入 `.pi/` 所有資源 |

## 相關頁面

- Entities:[[pi-mono]] — 含 `pi-coding-agent` 子套件說明
- Sources:[[2026-05-02-pi-mono-framework-tw]] — 來源文獻
- Concepts:[[minimal-agent-philosophy]]、[[late-conversion]]
- Comparisons:[[coding-agent-comparison]]（規劃中）

## 待補充 / 開放問題

- [ ] 實測 `PI_CODING_AGENT_DIR` 環境變數對專案解析的影響
- [ ] 研究 Extension API 中 `pi.config.projectRoot` 是否可動態覆寫
- [ ] 社區是否有既有 Extension 實作「Virtual Workspace」或「Project Group」
- [ ] 對比 VS Code `multi-root workspace`、Cursor `workspace`、Claude Code `project` 的語義差異