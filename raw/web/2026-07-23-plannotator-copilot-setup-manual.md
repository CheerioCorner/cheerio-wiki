---
title: Plannotator IT 安裝手冊（Copilot CLI 整合）
source_type: pdf
source_file: "C:/Users/User/Downloads/安裝手冊.pdf"
original_date: 2026-07-23
extracted_date: 2026-08-08
extraction_method: pdftotext + pymupdf (image extraction disabled)
---

# Plannotator IT 安裝手冊

> 來源：PDF 檔案 `安裝手冊.pdf`，2026-07-23 建立
> 內容：Plannotator 與 GitHub Copilot CLI 的整合安裝指南

---

## 概述

Plannotator IT 是一套將 Plannotator 與 GitHub Copilot CLI 整合的解決方案。主要用途：

- 透過 GitHub Copilot CLI 和 GitHub Copilot App 在 Windows PowerShell 中使用 Plannotator
- Plannotator 在 Copilot 的 plan mode 中攔截 AI 請求（localhost），人類可以在瀏覽器中標注後再發送 Prompt 給 Copilot

**Skill 名稱：** plannotator-copilot-setup

**安裝 Skill：**
```
npx skills@latest add https://github.com/EVACopilot/skills-itd-aas --skill plannotator-copilot-setup
```

Copilot CLI 和 App 都會安裝對應的 Skill。

**使用方式：** `/plannotator-copilot-setup`

AI 會引導你完成 Prompt 設定（分 3 個階段），設定完成後 Prompt 就會自動送給 Copilot。

---

## 快速安裝（3 步驟）

### 步驟 1：安裝 Plannotator

在 Windows PowerShell 中執行：

```powershell
irm https://plannotator.ai/install.ps1 | iex
```

安裝完成後會顯示：
```
plannotator vX.Y.Z installed to C:\Users\<用户名>\AppData\Local\plannotator\plannotator.exe
```

**注意：** 安裝後需要確認 PATH 環境變數包含 Plannotator，GitHub Copilot App 才能找到它。

### 步驟 2：停用 Share 功能

**方法 A：環境變數**
```powershell
[Environment]::SetEnvironmentVariable("PLANNOTATOR_SHARE", "disabled", "User")
```

**方法 B：設定檔**
編輯 `C:\Users\<用户名>\.plannotator\config.json`：
```json
{ "share": "disabled" }
```

> Export 功能中的 Share（Copy Share Link 到 share.plannotator.ai）會被停用。

### 步驟 3：安裝 GitHub Copilot CLI 的 plannotator-copilot 插件

**方法 A：透過 slash command（推薦）**

在 PowerShell 中執行 Copilot CLI 命令：
```
/plugin marketplace add backnotprop/plannotator
/plugin install plannotator-copilot@plannotator
```

**方法 B：透過 GUI**

在 GitHub Copilot App 中：Settings → Plugins → + Install → 搜尋 `plannotator` → 安裝 `plannotator-copilot`

> 方法 B 和方法 A 效果相同。

---

## 完整設定流程（12 步，3 個階段）

### 階段一：基礎安裝（步驟 1-4）

**步驟 1：安裝 Plannotator**
（同上述步驟 1）

**步驟 2：停用 Share**
（同上述步驟 2）

**步驟 3：安裝 Copilot CLI 插件**
（同上述步驟 3）

**步驟 4：確認 GitHub Copilot App**
確認 App 的 PATH 設定正確，能找到 Plannotator。

### 階段二：整合測試（步驟 5）

**步驟 5：測試 plan mode**
- 按 `Shift+Tab` 進入 plan mode
- Copilot 會進入 plan mode
- Plannotator 會在瀏覽器中開啟
- 人類可以在瀏覽器中標注、審閱
- 完成後按 `exit_plan_mode` 發送
- Plannotator 會顯示 "Send Feedback" 給 Copilot

### 階段三：進階整合（步驟 6-12）

**步驟 6：與 Obsidian 整合**

在 Obsidian 中使用 Plannotator：

1. **Plan Mode + Skill + Markdown**
   ```
   /plannotator-annotate obsidian-plannotator-plannotator-plannot.md
   ```

2. **Plannotator 標註**
   在 Plannotator UI 中進行標註。

3. **在 Obsidian 中檢視**
   標註結果會同步到 Obsidian。

---

## Slash Commands

| 命令 | 功能 |
|------|------|
| `/plannotator-review` | 審查 PR |
| `/plannotator-annotate <file>` | 標註 Markdown / HTML / TXT 等檔案 |
| `/plannotator-last` | 標註 Copilot 最後回應 |

---

## 進階設定

### 自訂 URL

可設定 `PLANNOTATOR_SHARE_URL` 和 `PLANNOTATOR_PASTE_URL` 環境變數。

詳細參考：`research/plannotator-githubcopilt-app-vs-code-gi.md` 第 5.2 節

### Ask AI 功能

Plannotator 支援 Ask AI，可選擇 AI CLI provider：
- Anthropic
- OpenAI
- 也可使用 GitHub Copilot

支援的 Agent：Claude、Codex、Pi、OpenCode

Ask AI 功能包括：
- Plan Review
- Code Review
- Annotate

Ask AI 透過 localhost 通訊，不會將資料送往外部 telemetry。

---

## FAQ

**Q: 為什麼 plannotator 沒有出現在 PATH 中？**
A: 需要確認 GitHub Copilot App 的 PATH 設定。安裝 Plannotator 後，確認 `C:\Users\<用户名>\AppData\Local\plannotator\` 在 PATH 中。

**Q: /plugin install 失敗怎麼辦？**
A: 確認 Copilot CLI 已正確安裝。嘗試在 PowerShell 或 VS Code 中執行。

**Q: 在 VS Code 中如何使用？**
A: 從 VS Code Marketplace 安裝 Plannotator 擴充套件（backnotprop.plannotator-webview）。VS Code 中的 copilot 和 VS Code 的 GitHub Copilot Chat 有不同的整合方式（第 4 節）。

**Q: Skill 安裝後在哪裡？**
A: 安裝路徑：`skills/plannotator-copilotsetup/SKILL.md`。Copilot skill 會安裝到 GitHub Copilot CLI 和 Copilot App。也支援 Claude Code、Codex、Gemini CLI 等 agent。`plannotator-copilot-setup` 的安裝位置：%USERPROFILE%\.agents\skills\。Copilot CLI 的 Plannotator AI skill 也可用於其他 agent。

---

## 相關資源

- 研究文件：`research/plannotator-githubcopilt-app-vs-code-gi.md`
- 官方文件：https://plannotator.ai/docs
- GitHub：https://github.com/backnotprop/plannotator
