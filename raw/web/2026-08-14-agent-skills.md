---
title: "Agent Skills"
description: "Agent Skills 是可擴展 Claude 功能的模組化能力。每個 Skill 封裝了指令、中繼資料和可選資源（腳本、範本），Claude 會在相關時自動使用。"
source_url: "https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/overview"
source_domain: "claude.com"
author:
published:
clipped: 2026-08-14
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Agent Skills

> Source: [Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/overview)
> Clipped: 2026-08-14

> [!note] Note
> 關於「zero data retention」（零資料保留），即 ZDR 如何適用於此功能，請參閱 [API 與資料保留](https://platform.claude.com/docs/zh-TW/manage-claude/api-and-data-retention) 。

## 為什麼使用 Skills

Skills 是可重複使用、基於檔案系統的資源，為 Claude 提供特定領域的專業知識：工作流程、上下文和最佳實踐，將通用代理轉變為專家。與提示（針對一次性任務的對話層級指令）不同，Skills 會按需載入，因此您不必在不同對話中重複相同的指引。

**主要優勢：**

- **讓 Claude 專業化：** 為特定領域的任務量身打造能力
- **減少重複：** 建立一次，自動使用
- **組合能力：** 結合多個 Skills 以完成複雜的多步驟任務

> [!note] Note
> 如需更多關於 Agent Skills 的架構和實際應用，請參閱工程部落格文章 [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) 。

## 使用 Skills

Anthropic 為常見的文件任務（PowerPoint、Excel、Word、PDF）提供預建的 Agent Skills，您也可以建立自己的自訂 Skills。兩者的運作方式相同：一旦 Skill 在您的環境中可用，Claude 會在與您的請求相關時自動使用它。

**預建的 Agent Skills** 可在 claude.ai、Claude API、 [Claude Platform on AWS](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-platform-on-aws) 和 [Microsoft Foundry](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-in-microsoft-foundry) 上使用。在 Microsoft Foundry 上，Agent Skills 需要 [Hosted on Anthropic 部署](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-in-microsoft-foundry#additional-features-not-supported-when-hosted-on-azure) 。請參閱 [可用的 Skills](#available-skills) 以取得完整清單。

**自訂 Skills** 讓您封裝領域專業知識和組織知識。它們可在 Claude 的各項產品中使用：在 Claude Code 中建立、透過 Claude API 上傳，或在 claude.ai 設定中新增。在 [Claude Platform on AWS](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-platform-on-aws) 和 [Microsoft Foundry](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-in-microsoft-foundry) 上，可透過 Skills API 上傳自訂 Skills。

> [!note] Note
> **開始使用：**
> 
> - 預建的 Agent Skills：請參閱 [快速入門教學](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart) ，開始在 API 中使用 PowerPoint、Excel、Word 和 PDF Skills
> - 自訂 Skills：請參閱 [Agent Skills Cookbook](https://platform.claude.com/cookbook/skills-notebooks-01-skills-introduction) ，了解如何建立您自己的 Skills

## Skills 的運作方式

Skills 使用 Claude 的 VM 環境來提供超越僅靠提示所能達成的能力。Claude 在具有檔案系統存取權的虛擬機器中運作，讓 Skills 能以目錄的形式存在，其中包含指令、可執行程式碼和參考資料，組織方式就像您為新團隊成員建立的入職指南。

這種基於檔案系統的架構實現了 **漸進式揭露（progressive disclosure）：** Claude 會根據需要分階段載入資訊，而不是預先消耗上下文。

Skills 可以包含三種類型的內容，每種在不同時間載入：

Skill 的 YAML frontmatter 提供探索資訊：

```
---
name: pdf-processing
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
---
```

Claude 在啟動時載入此中繼資料並將其包含在系統提示中。 `description` 是 Claude 在判斷是否觸發 Skill 時用來與您的請求進行比對的內容，因此它必須同時說明 Skill 的功能以及何時使用它。這種輕量級的方法意味著您可以安裝許多 Skills 而不會有上下文負擔：在 Skill 被觸發之前，只有其名稱和描述會佔用上下文。

### 第 2 層：指令（觸發時載入）

SKILL.md 的主體包含程序性知識：工作流程、最佳實踐和指引：

```
# PDF Processing

## Quick start

Use pdfplumber to extract text from PDFs:

\`\`\`python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    text = pdf.pages[0].extract_text()
\`\`\`

For advanced form filling, see [FORMS.md](FORMS.md).
```

當您的請求與某個 Skill 的描述相符時，Claude 會使用 bash 從檔案系統讀取 SKILL.md。只有在此時，這些內容才會進入上下文視窗。

### 第 3 層：資源和程式碼（按需載入）

Skills 可以捆綁額外的資料：

```
pdf-processing/
├── SKILL.md (main instructions)
├── FORMS.md (form-filling guide)
├── REFERENCE.md (detailed API reference)
└── scripts/
    └── fill_form.py (utility script)
```

**指令：** 額外的 markdown 檔案（FORMS.md、REFERENCE.md），包含專門的指引和工作流程

**程式碼：** 可執行腳本（fill\_form.py、validate.py），Claude 使用 bash 執行，提供確定性的操作，而無需將其程式碼載入上下文

**資源：** 參考資料，例如資料庫結構描述、API 文件、範本或範例

Claude 只有在被引用時才會存取這些檔案。檔案系統模型意味著每種內容類型都有不同的優勢：指令用於靈活的指引，程式碼用於可靠性，資源用於事實查詢。

| 層級 | 載入時機 | Token 成本 | 內容 |
| --- | --- | --- | --- |
| **第 1 層：中繼資料** | 始終（啟動時） | 每個 Skill 約 100 個 token | YAML frontmatter 中的 `name` 和 `description` |
| **第 2 層：指令** | Skill 被觸發時 | 低於 5k 個 token | SKILL.md 主體，包含指令和指引 |
| **第 3 層以上：資源** | 按需 | 存取前為零 | 捆綁的檔案。參考檔案在讀取時載入上下文。腳本透過 bash 執行，只有其輸出會進入上下文 |

漸進式揭露確保在任何時刻只有相關內容佔用上下文視窗。

### Skills 架構

Skills 在程式碼執行環境中運行，Claude 在其中擁有檔案系統存取權、bash 命令和程式碼執行能力。Skills 以目錄的形式存在於虛擬機器上，Claude 使用與您在電腦上瀏覽檔案時相同的 bash 命令與它們互動。

![Agent Skills 架構（Agent Skills Architecture）- 顯示 Skills 如何與代理的設定和虛擬機器整合](https://platform.claude.com/docs/images/agent-skills-architecture.png)

**Claude 如何存取 Skill 內容：**

當 Skill 被觸發時，Claude 使用 bash 從檔案系統讀取 SKILL.md，將其指令帶入上下文視窗。如果這些指令引用了其他檔案（例如 FORMS.md 或資料庫結構描述），Claude 也會使用額外的 bash 命令讀取這些檔案。當指令提到可執行腳本時，Claude 會透過 bash 執行它們，並且只接收輸出（腳本程式碼本身永遠不會進入上下文）。

**此架構帶來的能力：**

- **按需檔案存取：** Claude 只讀取每個任務所需的檔案。一個 Skill 可以包含數十個參考檔案，但如果您的任務只需要銷售結構描述，那麼 Claude 只會載入那一個檔案。其餘的檔案留在檔案系統上，不消耗任何 token。
- **高效的腳本執行：** 當 Claude 執行 `validate_form.py` 時，腳本的程式碼永遠不會載入上下文視窗。只有其輸出（例如「Validation passed」或特定的錯誤訊息）會消耗 token，這使得腳本比讓 Claude 即時生成等效程式碼要高效得多。
- **捆綁內容沒有實際限制：** 檔案在被存取之前不會消耗上下文，因此 Skills 可以包含完整的 API 文件、大型資料集或大量範例。未使用的捆綁內容不會造成上下文負擔。

以下是 Claude 如何載入和使用先前範例中的自訂 `pdf-processing` Skill（而非預建的 `pdf` Skill）：

1. **啟動：** 系統提示包含： `pdf-processing - Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.`
2. **使用者請求：** 「從這個 PDF 中提取文字並進行摘要」
3. **Claude 呼叫：** `bash: cat pdf-processing/SKILL.md` → 指令載入上下文
4. **Claude 判斷：** 不需要填寫表單，因此不讀取 FORMS.md
5. **Claude 執行：** 使用 SKILL.md 中的指令完成任務

![Skills 載入上下文視窗（context window）- 顯示 skill 中繼資料和內容的漸進式載入](https://platform.claude.com/docs/images/agent-skills-context-window.png)

## Skills 可在哪裡使用

Skills 可在 Claude 的各項代理產品中使用：

> [!note] Note
> Claude Platform on AWS 和 Microsoft Foundry 在以下所有章節中繼承與 Claude API 相同的 Skills 行為。

### Claude API

Claude API 同時支援預建的 Agent Skills 和自訂 Skills。兩者的運作方式完全相同：在 `container` 參數中指定相關的 `skill_id` ，並搭配 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) 。

**先決條件：** 透過 API 使用 Skills 需要 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) （Skills 在其容器中執行）以及一個 beta 標頭：

- `skills-2025-10-02` - 啟用 Skills 功能

當您使用 [Files API](https://platform.claude.com/docs/zh-TW/build-with-claude/files) 將輸入檔案上傳到容器或下載 Skill 產生的檔案時，請新增第二個標頭 `files-api-2025-04-14` 。

透過引用其 `skill_id` （ `pptx` 、 `xlsx` 、 `docx` 或 `pdf` ）來使用預建的 Agent Skills，或透過 Skills API（ `/v1/skills` 端點）建立並上傳您自己的 Skills。自訂 Skills 在整個工作區中共享：所有工作區成員都可以存取它們。

API 上的 Skills 在沙箱容器中執行，沒有網路存取權，也無法在執行時安裝套件。詳情請參閱 [限制與約束](#limitations-and-constraints) 。

如需了解更多，請參閱 [透過 API 使用 Agent Skills](https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide) 。

### Claude Code

[Claude Code](https://code.claude.com/docs/zh-TW/overview) 支援自訂 Skills。預建的文件 Skills（PowerPoint、Excel、Word、PDF）在 Claude Code 中不可用，但開源的 [Claude API skill](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/claude-api-skill) 已隨附其中。請參閱 Claude Code 隨附的 [內建命令和 Skills](https://code.claude.com/docs/zh-TW/commands) 完整清單。

**自訂 Skills：** 將 Skills 建立為包含 SKILL.md 檔案的目錄。Claude 會自動探索並使用它們。

Claude Code 中的自訂 Skills 是基於檔案系統的，不需要透過 API 上傳：將它們放在 `~/.claude/skills/` （個人）或 `.claude/skills/` （專案）中。

如需了解更多，請參閱 [在 Claude Code 中使用 Skills](https://code.claude.com/docs/zh-TW/skills) 。

### claude.ai

[claude.ai](https://claude.ai/) 同時支援預建的 Agent Skills 和自訂 Skills。

**預建的 Agent Skills：** 這些 Skills 在您建立文件時處於啟用狀態。Claude 無需任何設定即可使用它們。

**自訂 Skills：** 透過「Settings > Features」以 zip 檔案形式上傳您自己的 Skills。適用於已 [啟用程式碼執行](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude) 的 Pro、Max、Team 和 Enterprise 方案。自訂 Skills 屬於個別使用者。它們不會在整個組織中共享，也無法由管理員集中管理。

如需了解更多關於在 claude.ai 中使用 Skills 的資訊，請參閱 Claude 說明中心的以下資源：

- [什麼是 Skills？](https://support.claude.com/en/articles/12512176-what-are-skills)
- [在 Claude 中使用 Skills](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [如何建立自訂 Skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [使用 Skills 教 Claude 您的工作方式](https://support.claude.com/en/articles/12580051-teach-claude-your-way-of-working-using-skills)

## Skill 結構

每個 Skill 都需要一個帶有 YAML frontmatter 的 `SKILL.md` 檔案：

```
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
---

# Your Skill Name

## Instructions
[Clear, step-by-step guidance for Claude to follow]

## Examples
[Concrete examples of using this Skill]
```

**必填欄位：** `name` 和 `description`

**欄位要求：**

`name` ：

- 最多 64 個字元
- 只能包含小寫字母、數字和連字號
- 不能包含 XML 標籤
- 不能包含保留字：「anthropic」、「claude」

`description` ：

- 不得為空
- 最多 1024 個字元
- 不能包含 XML 標籤

`description` 必須同時包含 Skill 的功能以及 Claude 應在何時使用它。如需完整的撰寫指引，請參閱 [Skill 撰寫最佳實踐](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices) 。

## 安全性考量

僅使用來自可信來源的 Skills：您自己建立的或從 Anthropic 取得的。Skills 透過指令和程式碼賦予 Claude 新的能力，這也意味著惡意的 Skill 可能會指示 Claude 以不符合該 Skill 所宣稱目的的方式呼叫工具或執行程式碼。

> [!note] Note
> 如果您必須使用來自不可信或未知來源的 Skill，請格外謹慎，並在使用前徹底審核。根據 Claude 在執行 Skill 時擁有的存取權限，惡意的 Skills 可能導致資料外洩、未經授權的系統存取或其他安全風險。

**主要安全性考量：**

- **徹底審核：** 檢查 Skill 中捆綁的所有檔案：SKILL.md、腳本、圖片和其他資源。尋找異常模式，例如非預期的網路呼叫、檔案存取模式，或與 Skill 所宣稱目的不符的操作
- **外部來源具有風險：** 從外部 URL 擷取資料的 Skills 具有特別的風險，因為擷取的內容可能包含惡意指令。即使是值得信賴的 Skills，如果其外部相依性隨時間變化，也可能遭到入侵
- **工具濫用：** 惡意的 Skills 可能以有害的方式呼叫工具（檔案操作、bash 命令、程式碼執行）
- **資料外洩：** 可存取敏感資料的 Skills 可能被設計成將資訊洩漏到外部系統
- **視同安裝軟體：** 將 Skills 整合到可存取敏感資料或關鍵操作的生產系統時，請格外小心

如需組織規模的治理、審查和部署指引，請參閱 [企業版 Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/enterprise) 。

## 可用的 Skills

### 預建的 Agent Skills

以下預建的 Agent Skills 可立即使用：

- **PowerPoint (pptx)：** 建立簡報、編輯投影片、分析簡報內容
- **Excel (xlsx)：** 建立試算表、分析資料、產生含圖表的報告
- **Word (docx)：** 建立文件、編輯內容、格式化文字
- **PDF (pdf)：** 產生格式化的 PDF 文件和報告

這些 Skills 可在 Claude API、 [Claude Platform on AWS](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-platform-on-aws) 、 [Microsoft Foundry](https://platform.claude.com/docs/zh-TW/build-with-claude/claude-in-microsoft-foundry) 和 claude.ai 上使用。請參閱 [快速入門教學](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart) ，開始在 API 中使用它們。

### 開源 Skills

Anthropic 也在 [skills 儲存庫](https://github.com/anthropics/skills) 中發布開源 Skills：

- **[Claude API skill](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/claude-api-skill) ：** 為 Claude 提供最新的 API 參考資料、SDK 文件，以及八種程式語言的最佳實踐。隨附於 Claude Code，也可從 skills 儲存庫安裝。

### 自訂 Skills 範例

如需自訂 Skills 的完整範例，請參閱 [Skills cookbook](https://platform.claude.com/cookbook/skills-notebooks-01-skills-introduction) 。

## 資料保留

Agent Skills 不在 ZDR 安排的涵蓋範圍內。Skill 定義和執行資料會根據 Anthropic 的標準資料保留政策保留。

如需所有功能的 ZDR 適用性，請參閱 [API 與資料保留](https://platform.claude.com/docs/zh-TW/manage-claude/api-and-data-retention) 。

## 限制與約束

Claude Platform on AWS 和 Microsoft Foundry 在以下小節中遵循與 Claude API 相同的限制。

### 跨平台可用性

**自訂 Skills 不會跨平台同步** 。上傳到某個平台的 Skills 不會自動在其他平台上可用：

- 上傳到 claude.ai 的 Skills 必須另外上傳到 API
- 透過 API 上傳的 Skills 在 claude.ai 上不可用
- Claude Code 的 Skills 是基於檔案系統的，與 claude.ai 和 API 皆分開

請在您想使用 Skills 的每個平台上分別管理和上傳。

### 共享範圍

Skills 根據您使用的位置有不同的共享模式：

- **claude.ai：** 僅限個別使用者。每位團隊成員必須分別上傳。
- **Claude API：** 整個工作區。所有工作區成員都可以存取已上傳的 Skills。
- **Claude Code：** 個人（ `~/.claude/skills/` ）或基於專案（`.claude/skills/` ）。也可以透過 Claude Code Plugins 共享。

claude.ai 不支援集中式管理員管理或自訂 Skills 的全組織分發。

### 執行環境約束

您的 Skill 可用的確切執行環境取決於您使用它的產品平台。

- **claude.ai：**
	- **網路存取權各異：** 根據使用者/管理員設定，Skills 可能擁有完整、部分或沒有網路存取權。如需更多詳情，請參閱 [建立和編輯檔案](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude#h_6b7e833898) 支援文章。
- **Claude API：**
	- **無網路存取權：** Skills 無法進行外部 API 呼叫或存取網際網路。
		- **無法在執行時安裝套件：** 只有預先安裝的套件可用。您無法在執行期間安裝新套件。
		- **僅限預先設定的相依性：** 請查看 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) 文件以取得可用套件清單。
- **Claude Code：**
	- **完整網路存取權：** Skills 擁有與使用者電腦上任何其他程式相同的網路存取權。
		- **不建議全域安裝套件：** Skills 應僅在本機安裝套件，以避免干擾使用者的電腦。

請規劃您的 Skills 以在這些約束內運作。

了解如何在 10 分鐘內使用 Agent Skills 透過 Claude API 建立文件。

[透過 API 使用 Agent Skills](https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide)

了解如何使用 Agent Skills 透過 API 擴展 Claude 的能力。

[在 Claude Code 中使用 Skills](https://code.claude.com/docs/zh-TW/skills)

在 Claude Code 中建立和管理自訂 Skills。

[Skill 撰寫最佳實踐](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices)

了解如何撰寫 Claude 能夠成功探索和使用的有效 Skills。

Was this page helpful?