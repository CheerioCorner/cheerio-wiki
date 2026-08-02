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

---

## 兩種安裝方式的比較

| 特性 | GitHub 安裝 | NPM 安裝 |
|------|------------|----------|
| **來源** | Git repository | NPM registry |
| **路徑** | `~/.pi/agent/git/...` | `~/.pi/agent/npm/node_modules/...` |
| **版本控制** | Git tag / branch | Semver |
| **依賴管理** | 手動 | npm 自動 |
| **適用場景** | 開發中、私有、測試 | 穩定版、公開发布 |
| **更新方式** | `git pull` | `npm update` |
| **安裝指令** | `pi install git:github.com/...` | `pi install npm:@scope/...` |

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

1. **路徑反映來源** — Pi 用目錄結構區分安裝來源（`git/` vs `npm/`）
2. **不同生態系** — 有些 Agent 用 Node.js (npm)，有些用 Python (pip)
3. **配置 vs 套件** — 有些 Agent 用 JSON 配置，有些用真正的套件管理器
4. **版本控制差異** — Git tag vs Semver vs MCP config version

---

## 相關頁面

- [[wiki/concepts/npm-publishing-workflow]] — Package Publishing 統一觀點
- [[projects/pi-plannotator-auto/index]] — 我們的第一個 NPM 發佈實例
