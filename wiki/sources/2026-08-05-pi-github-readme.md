---
title: "Pi GitHub README — Agent Harness 總覽"
type: source
created: 2026-08-05
updated: 2026-08-05
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-02-pipackagescoding-agent at main.md
tags: [pi, coding-agent, monorepo, containerization, supply-chain]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-05-pi-github-readme
provenance_url: https://github.com/earendil-works/pi
---

> 來源：[earendil-works/pi GitHub README](https://github.com/earendil-works/pi)
> Clipped：2026-08-02

## 一句話

Pi 的官方 GitHub README，記載四個子 package 架構、containerization 方案、supply-chain security 策略與 session publishing 機制。

## 重點摘要

### Repo 狀態

- **Org**：`earendil-works`（前身 `badlogic`）
- **Repo**：`pi`（前身 `pi-mono`）
- **License**：MIT
- **官網**：[pi.dev](https://pi.dev/)
- **文件**：[pi.dev/docs/latest](https://pi.dev/docs/latest)

### 四個子 Package

| Package | 角色 |
|---|---|
| `@earendil-works/pi-ai` | 統一多 provider LLM API（OpenAI, Anthropic, Google…） |
| `@earendil-works/pi-agent-core` | Agent runtime：tool calling + state management |
| `@earendil-works/pi-coding-agent` | 互動式 coding agent CLI |
| `@earendil-works/pi-tui` | Terminal UI library（differential rendering） |

> 附帶提及 [pi-chat](https://github.com/earendil-works/pi-chat)（Slack/chat 自動化）。

### Containerization（三種沙箱方案）

Pi 不含內建權限系統，預設以使用者權限運行。強制隔離需外部方案：

1. **Gondolin extension**：`pi` + provider auth 在 host，tools + `!` commands 路由到 Linux micro-VM
2. **Docker**：整個 pi process 在 container 中
3. **OpenShell**：整個 pi process 在 policy-controlled sandbox 中

→ 詳見 `packages/coding-agent/docs/containerization.md`

### Supply-chain Security

Pi 將 npm dependency 變更視為 reviewed code changes：

- 外部依賴釘死確切版本（`save-exact=true`、`min-release-age=2`）
- `package-lock.json` 為 ground truth，pre-commit 阻擋意外更動
- `npm run check` 驗證 pinned deps、TypeScript import、shrinkwrap
- 發布使用 `npm run release:local` 做 smoke test
- CI 用 `npm ci --ignore-scripts`，定期 `npm audit` + `npm audit signatures`
- Shrinkwrap 有 lifecycle script allowlist

### Session Publishing

- 工具：[`badlogic/pi-share-hf`](https://github.com/badlogic/pi-share-hf)
- 目標：將 OSS session 發布到 Hugging Face，用真實任務改善 coding agent
- Dataset：[badlogicgames/pi-mono on Hugging Face](https://huggingface.co/datasets/badlogicgames/pi-mono)

### Standalone Binaries

可從 release source 編譯：

```bash
VERSION="<release-version>"
tar -xzf "pi-${VERSION}-source.tar.gz"
cd "pi-${VERSION}"
./scripts/build-binaries.sh --offline-model-data --platform linux-x64 --out "$PWD/out"
```

### 開發指令

```bash
npm install --ignore-scripts    # 安裝依賴
npm run build                   # 建構全部 package
npm run check                   # Lint + format + type check
./test.sh                       # 測試（無 API key 時跳過 LLM 測試）
```

## 與既有來源的差異

本 README 補充了 [[wiki/sources/2026-02-10-pi-agent-core-design|agent core 設計]] 與 [[wiki/sources/2026-05-02-pi-mono-framework-tw|pi-mono framework]] 未涵蓋的內容：

- **Repo 改名**：`badlogic/pi-mono` → `earendil-works/pi`
- **Package 數量**：5 → 4（pi-web-ui 已不見）
- **Containerization**：三種沙箱隔離方案（Gondolin / Docker / OpenShell）
- **Supply-chain hardening**：完整的依賴管理與安全策略
- **Session publishing**：OSS session → Hugging Face pipeline
- **MIT License**

## 來源

- [[raw/web/2026-08-02-pipackagescoding-agent at main|Raw — GitHub README]]

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — entity 頁面（已更新）
- [[wiki/entities/pi-agent-core|pi-agent-core]] — agent runtime 核心
- [[wiki/sources/2026-02-10-pi-agent-core-design|2026-02-10 Pi Agent Core 設計]] — 五檔解剖
- [[wiki/sources/2026-05-02-pi-mono-framework-tw|2026-05-02 Pi Mono Framework]] — 繁中框架研究
- [[wiki/sources/2026-07-18-pi-resource-inventory|2026-07-18 Pi 資源盤點]] — 本地環境盤點
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
