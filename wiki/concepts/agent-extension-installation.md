---
title: Agent Extension 安裝位置與方式
type: concept
created: 2026-07-31
updated: 2026-07-31
sources: 1
tags: [agent, extension, installation, pi, package-management]
collection: concepts
topics: [extension-dev]
canonical: concepts/agent-extension-installation
---

# Agent Extension 安裝位置與方式

> 不同的 Agent 框架有各自安裝 Extension 的方式和儲存位置。記錄這些差異有助於理解 Agent 的運作機制。

---

## Pi 的安裝方式

Pi 支援兩種主要的安裝來源，它們的儲存位置不同：

### 1. 從 GitHub 安裝

```bash
pi install git:github.com/CheerioCorner/pi-plannotator-auto
```

**儲存位置：**
```
~/.pi/agent/git/github.com/CheerioCorner/pi-plannotator-auto/
```

**特點：**
- 直接從 GitHub repo 複製程式碼
- 不經過 NPM registry
- 適合開發中或私有的 extension
- 版本由 Git tag 或 branch 控制

### 2. 從 NPM 安裝

```bash
pi install npm:@cheeriocorner/pi-plannotator-auto
```

**儲存位置：**
```
~/.pi/agent/npm/node_modules/@cheeriocorner/pi-plannotator-auto/
```

**特點：**
- 從 NPM registry 下載
- 經過 npm install 流程
- 適合公開发布的穩定版本
- 版本由 semver 控制
- 依賴由 npm 管理

---

## 完整路徑結構

```
~/.pi/agent/
├── extensions/                   # 全域 loose-file extensions（自動載入）
│   ├── my-extension.ts           # 單檔 extension
│   └── my-complex-extension/     # 子目錄 extension
│       └── index.ts
│
├── git/                          # 從 GitHub 安裝的 extensions
│   └── github.com/
│       └── CheerioCorner/
│           └── pi-plannotator-auto/
│               ├── package.json
│               └── extensions/
│                   └── auto-annotate.ts
│
├── npm/                          # 從 NPM 安裝的 extensions
│   └── node_modules/
│       ├── @cheeriocorner/
│       │   └── pi-plannotator-auto/
│       │       ├── package.json
│       │       └── extensions/
│       │           └── auto-annotate.ts
│       └── @plannotator/
│           └── pi-extension/
│
└── ...（其他目錄）
```

### 專案端 loose-file extensions

```
<project>/.pi/extensions/         # 專案端（信任後才載入）
├── project-extension.ts
└── project-extension/
    └── index.ts
```

---

## 三種載入方式的比較

| 特性 | Loose-file（全域） | Loose-file（專案端） | GitHub 安裝 | NPM 安裝 |
|------|-------------------|---------------------|------------|----------|
| **路徑** | `~/.pi/agent/extensions/` | `.pi/extensions/` | `~/.pi/agent/git/...` | `~/.pi/agent/npm/...` |
| **載入時機** | 啟動時自動 | 專案信任後自動 | 安裝後自動 | 安裝後自動 |
| **版本控制** | 無（手動） | 無（手動） | Git tag/branch | Semver |
| **適用場景** | 個人工具、快速原型 | 專案特定設定 | 開發中/私有 extension | 穩定版/公開发布 |
| **更新方式** | 直接編輯檔案 | 直接編輯檔案 | `pi install` / git pull | `pi install` / npm update |
| **熱重載** | ✅ `/reload` | ✅ `/reload` | ✅ `/reload` | ✅ `/reload` |
| **發布** | 不需要 | 不需要 | Git repo | NPM registry |

---

## 其他 Agent 框架的做法

### Claude Code (Anthropic)

- **安裝方式：** 透過 MCP (Model Context Protocol) servers
- **儲存位置：** `~/.claude/mcp_servers/`
- **特點：** 以 JSON 配置檔為主，不是 npm package

### Cursor

- **安裝方式：** 透過 Extensions marketplace
- **儲存位置：** `~/.cursor/extensions/`
- **特點：** 與 VS Code 相容，使用 VSIX 格式

### Continue

- **安裝方式：** 透過 `~/.continue/config.json`
- **儲存位置：** 依賴本地路徑或 npm
- **特點：** 配置驅動，extension 可以是本地檔案

### Aider

- **安裝方式：** pip install
- **儲存位置：** Python site-packages
- **特點：** 用 Python 生態系，不是 Node.js

---

## 關鍵洞察

1. **路徑反映來源** — Pi 用目錄結構區分安裝來源（`extensions/` loose-file vs `git/` vs `npm/`）
2. **Loose-file 是最簡單的起手方式** — 官方 Quick Start 範例第一句就寫 `Create ~/.pi/agent/extensions/my-extension.ts`，不需要 package.json
3. **不同生態系** — 有些 Agent 用 Node.js (npm)，有些用 Python (pip)
4. **配置 vs 套件** — 有些 Agent 用 JSON 配置，有些用真正的套件管理器
5. **版本控制差異** — loose-file 無版本控制（需手動 git），Git tag vs Semver vs MCP config version

---

## Extension 類型分類

### 工具型（Tool Extension）
- 用 `pi.registerTool()` 註冊新工具給 LLM 呼叫
- 例如：`open_annotate`（pi-plannotator-auto）
- LLM 主動決定是否使用該工具

### 攔截型（Interceptor Extension）
- 用 `pi.on("tool_call", ...)` 訂閱事件，在 tool 執行前攔截或修改
- 例如：`garden-guard`（攔截 Notion 寫入操作做前置檢查）
- Agent 無法跳過——在 runtime 層強制執行
- 可以 block（阻止執行）、mutate（修改參數）、warn（記錄警告）

兩種類型在打包/安裝方式上沒有本質差異，都是 TypeScript 模組、都放在 extensions/ 目錄下自動載入。差別只在內部邏輯（registerTool vs pi.on）。詳見 [[wiki/sources/2026-08-13-garden-guard-incident|花園巡檢事故報告]]。

## 相關頁面

- [[wiki/concepts/npm-publishing-workflow]] — Package Publishing 統一觀點
- [[projects/pi-plannotator-auto/index]] — 我們的第一個 NPM 發佈實例
- [[wiki/sources/2026-08-13-garden-guard-incident]] — garden-guard extension 建立全過程（含工具型 vs 攔截型分類）
