---
title: Pi Agent GitHub Copilot Provider 設定指南
type: source
created: 2026-08-19
updated: 2026-08-19
tags: [pi-agent, github-copilot, provider, oauth, setup]
topics: [pi-agent-ecosystem]
provenance:
  - kind: external
    url: https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/providers.md
sources:
  - url: https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/providers.md
    title: Pi Agent Official Providers Documentation
    accessed: 2026-08-19
  - url: https://github.com/earendil-works/pi/issues/4599
    title: "GitHub Copilot provider: support dynamic model discovery"
    accessed: 2026-08-19
  - url: https://marketplace.visualstudio.com/items?itemName=brijbyte.piagent-vscode
    title: PiAgent VSCode Extension
    accessed: 2026-08-19
---

# Pi Agent GitHub Copilot Provider 設定指南

> 整理自 Pi Agent 官方文件與 GitHub Issues，供 W-2026-08-028 參考使用。

## 概述

Pi Agent 原生支援 GitHub Copilot 作為 subscription-based provider，透過 OAuth 認證直接使用公司或個人的 Copilot 訂閱，無需額外付費購買 API key。

---

## 設定流程

### Step 1：啟動 Pi 並執行 `/login`

```bash
pi
# 進入互動模式後
/login
```

### Step 2：選擇 GitHub Copilot

在 provider 選單中選擇 **GitHub Copilot**。

### Step 3：選擇 GitHub 域名

- 按 **Enter** 使用 github.com（個人帳號）
- 或輸入公司 **GitHub Enterprise Server** 域名

### Step 4：完成 OAuth 認證

瀏覽器會自動開啟 GitHub OAuth 頁面，完成授權後 credentials 會自動存入：

```
~/.pi/agent/auth.json
```

### Step 5：選擇模型

```bash
/model
# 選擇 github-copilot 開頭的模型
```

---

## 常見問題：模型不出現

### 問題描述

`/model` 或 `pi --list-models` 看不到某些 Copilot 模型。

### 原因

Pi Agent 的模型清單是**建置時從 `models.dev` 靜態編譯**的（來源：[GitHub Issue #4599](https://github.com/earendil-works/pi/issues/4599)），不是即時從 Copilot API 拉取。因此：

- 有些你訂閱有的模型，Pi 可能沒列出來
- 模型可用性取決於你的 Copilot tier、org membership、feature flags

### 解決方案

**方法 1：在 VS Code 中啟用模型**（官方建議）

1. 開啟 VS Code
2. Copilot Chat → model selector
3. 選擇目標模型 → 點擊 "Enable"

然後回到 Pi 重新 `/model` 選擇。

**方法 2：直接用 model ID**

如果你知道模型 ID（例如 `gpt-4o`、`claude-sonnet-4`），可以直接指定：

```bash
pi --provider github-copilot --model gpt-4o
```

**方法 3：等待 Pi 更新**

Issue #4599 提出 runtime fetch 方案（每次啟動時即時拉取 Copilot 模型清單），但目前尚未實作。

---

## 模型可用性（靜態清單）

根據官方文件，GitHub Copilot 訂閱可使用的模型包括：

| 模型 | API 類型 | 備註 |
|---|---|---|
| GPT-4o | openai-completions | OpenAI 最新模型 |
| GPT-4.1 | openai-completions | 長上下文 |
| Claude Sonnet 4 | anthropic-messages | Anthropic 模型 |
| Claude Sonnet 3.5 | anthropic-messages | |
| Gemini 2.5 Pro | google-generative-ai | Google 模型 |
| o3 / o4-mini | openai-completions | reasoning models |

> ⚠️ 實際可用模型以你的 Copilot 訂閱等級和 GitHub 帳號設定為準。

---

## 認證機制

### OAuth 流程

```
Pi Agent → GitHub OAuth → 瀏覽器授權 → Token 存入 auth.json
```

### Token 儲存位置

```
~/.pi/agent/auth.json
```

格式：
```json
{
  "github-copilot": {
    "type": "oauth",
    "refresh": "...",
    "access": "...",
    "expires": 1234567890
  }
}
```

### Token 自動刷新

Pi Agent 會在 token 過期前自動刷新，無需手動處理。

### 登出

```bash
/logout
# 選擇 github-copilot
```

---

## 與其他工具的整合

### Pi CLI

```bash
pi --provider github-copilot --model gpt-4o
```

### PiAgent VSCode Extension

直接在 VS Code Chat 中使用 `@piagent`，共享 `~/.pi/agent/` 配置。

### 自訂 models.json

可以在 `~/.pi/agent/models.json` 中覆寫 Copilot provider 設定：

```json
{
  "providers": {
    "github-copilot": {
      "baseUrl": "https://api.githubcopilot.com",
      "api": "openai-completions"
    }
  }
}
```

---

## 限制與注意事項

1. **模型清單靜態** — 不是即時從 Copilot API 拉取
2. **訂閱等級影響** — 個人 vs Enterprise 有不同的模型可用性
3. **需有 Copilot 訂閱** — 免費帳號無法使用
4. **OAuth token 有效期** — 約 25 分鐘，但 Pi 會自動刷新

---

## 參考資料

- [Pi Agent 官方 Providers 文件](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/providers.md)
- [GitHub Issue #4599 - Dynamic Model Discovery](https://github.com/earendil-works/pi/issues/4599)
- [PiAgent VSCode Extension](https://marketplace.visualstudio.com/items?itemName=brijbyte.piagent-vscode)
- [[wiki/entities/pi-mono|pi-mono]] — Pi Agent 核心架構
- [[wiki/entities/github-copilot|github-copilot]] — GitHub Copilot 生態系
