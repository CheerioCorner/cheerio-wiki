---
title: "Plannotator IT 安裝手冊：GitHub Copilot CLI 整合"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [plannotator, copilot, installation, windows, setup-guide]
topics: [extension-dev, coding-agent]
canonical: sources/2026-07-23-plannotator-copilot-setup
provenance_raw: "raw/web/2026-07-23-plannotator-copilot-setup-manual.md"
---

# Plannotator IT 安裝手冊：GitHub Copilot CLI 整合

> 來源：PDF 安裝手冊（2026-07-23），描述 Plannotator 與 GitHub Copilot CLI 在 Windows 上的整合安裝流程。
> 提取工具：markitdown (Microsoft) — 比 pdftotext 保留更多結構與中文。

## 一句話

這份安裝手冊教你如何在 Windows 上把 Plannotator 裝起來，並與 GitHub Copilot CLI 整合，讓 Copilot 的 plan mode 能在瀏覽器中標注審閱。

## 三種安裝方式

### 方法一（推薦）：用 Skill 自動安裝

```bash
# 安裝 Skill
npx skills@latest add https://github.com/EVACopilot/skills-itd-aas --skill plannotator-copilot-setup

# 在 Copilot 對話框執行
/plannotator-copilot-setup
```

AI 會自動完成安裝，最後列出你還需要手動做的事。

### 方法二（推薦）：貼 Prompt 讓 Copilot 裝

把下面整段貼到 Copilot CLI／App 的對話輸入框：

> 請幫我在這台 Windows 電腦上安裝 Plannotator（一個給 AI coding agent 用的本機 Plan/Code Review 工具），並完成以下設定：
> 1. 用 PowerShell 執行官方安裝腳本：`irm https://plannotator.ai/install.ps1 | iex`
> 2. 停用分享功能：設定 `PLANNOTATOR_SHARE=disabled`，並在 `~/.plannotator/config.json` 寫入 `{ "share": "disabled" }`
> 3. 完成後列出我還需要手動做的事

### 方法三：手動一步步裝

#### 步驟 1：安裝執行檔本體

```powershell
irm https://plannotator.ai/install.ps1 | iex
```

安裝路徑：`C:\Users\<帳號>\AppData\Local\plannotator\plannotator.exe`

> ⚠️ 需要完整重啟 GitHub Copilot App 才會生效，開新終端機分頁沒有用。

#### 步驟 2：關閉分享功能（建議一定要做）

```powershell
[Environment]::SetEnvironmentVariable("PLANNOTATOR_SHARE", "disabled", "User")
```

再到 `C:\Users\<帳號>\.plannotator\config.json` 建立或編輯：

```json
{ "share": "disabled" }
```

這樣「Export → Share」「Copy Share Link」等功能都會消失，確保計畫內容不會被送到 share.plannotator.ai。

#### 步驟 3：安裝 GitHub Copilot CLI 專用外掛

**方式 A：slash command（在 Copilot 聊天輸入框）**

```
/plugin marketplace add backnotprop/plannotator
/plugin install plannotator-copilot@plannotator
```

**方式 B：GUI 介面**

GitHub Copilot App → Settings → Plugins → + Install → 搜尋 `plannotator` → 安裝 `plannotator-copilot` 並打開啟用開關。

> 兩種方式效果一樣，B 比較直覺、不用打指令；A 比較快。

#### 步驟 4：完整重啟 GitHub Copilot App

不是重開一個分頁或終端機視窗，是整個 App 關掉、重新打開，PATH 跟剛裝的外掛才會生效。

#### 步驟 5：驗證安裝成功

重啟後，在對話裡按 `Shift+Tab` 進入 plan mode，讓 Copilot 產生一個計畫並準備退出 plan mode（呼叫 `exit_plan_mode`）——這時應該會自動跳出瀏覽器，顯示 Plannotator 的計畫審查頁面。

#### 步驟 6：Obsidian × Plannotator 設定

1. 選擇 Plan Mode 或直接指定 skill 後，任意給予一個 markdown 檔：
   ```
   /plannotator-annotate obsidian-plannotator-plannotator-plannot.md
   ```

2. 進行 Plannotator 設定

3. 將編輯好的檔案，存入 Obsidian

## 常用指令

| 指令 | 用途 |
|------|------|
| `/plannotator-review` | 審查目前未提交的程式碼變更，或貼 PR 網址審查 |
| `/plannotator-annotate <檔案路徑>` | 標註任一 Markdown / HTML / TXT...等檔案 |
| `/plannotator-last` | 標註 Copilot 最後一則回覆訊息 |

## 資安提醒

- **一定要做**：關閉分享功能（方法一、二的第 2 步），避免計畫內容外流到官方雲端分享服務
- 若要更謹慎，可改用「自架分享服務」（`PLANNOTATOR_SHARE_URL` / `PLANNOTATOR_PASTE_URL` 指到內網網址），詳見團隊研究報告 `research/plannotator-github-copilt-app-vs-code-gi.md` 第 5.2 節
- Plannotator 內建的「Ask AI」功能，資料會直接送到你機器上已登入的 AI CLI 供應商（Anthropic／OpenAI 等），不經 Plannotator 伺服器
- 核心 Plan Review／Code Review／Annotate 三大功能全程只在 localhost 執行，官方隱私政策明講不蒐集使用行為 telemetry

## 常見問題

| 問題 | 解答 |
|------|------|
| 裝完之後 plannotator 指令說找不到？ | PATH 是安裝腳本自動加的，但要完整重啟 GitHub Copilot App（不是開新終端機）才會生效 |
| `/plugin install` 要打在哪裡？ | 打在跟 Copilot 聊天的輸入框，不是打在 PowerShell 或 VS Code 的終端機裡 |
| 在 VS Code 裡可以用嗎？ | 可以，但要另外裝 VS Code Marketplace 的 Plannotator 擴充套件（`backnotprop.plannotator-webview`），且要從 VS Code 的「整合終端機」裡啟動 copilot，不是用側邊 Copilot Chat 面板 |
| 想把安裝流程包成 Skill？ | 已經包好了：`skills/plannotator-copilotsetup/SKILL.md`，複製到 `%USERPROFILE%\.agents\skills\` 底下 |

## 參考資料

- 研究背景：`research/plannotator-github-copilt-app-vs-code-gi.md`
- 官方文件：https://plannotator.ai/docs
- 原始碼：https://github.com/backnotprop/plannotator

## 來源

- [[raw/web/2026-07-23-plannotator-copilot-setup-manual|原始 PDF 文字提取（markitdown）]]

## 相關頁面

- [[wiki/entities/plannotator|Plannotator]]
- [[wiki/entities/markitdown|MarkItDown]] — 用來提取這份 PDF 的工具
- [[wiki/topics/extension-dev|Extension Dev]]
