---
title: "在 API 中開始使用 Agent Skills"
description: "了解如何在 10 分鐘內使用 Agent Skills 透過 Claude API 建立文件。"
source_url: "https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart"
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
# 在 API 中開始使用 Agent Skills

> Source: [在 API 中開始使用 Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart)
> Clipped: 2026-08-14

本教學將向您展示如何使用 Agent Skills 建立 PowerPoint 簡報。您將學習如何啟用 Skills、發出請求，以及存取生成的檔案。

## 先決條件

- 一個 [Claude API 金鑰](https://platform.claude.com/settings/keys) 或已登入的 [ant CLI](https://platform.claude.com/docs/zh-TW/cli-sdks-libraries/cli/authentication)
- 適用於您程式語言的 [用戶端 SDK](https://platform.claude.com/docs/zh-TW/cli-sdks-libraries/overview) ，或 `curl` 和 `jq`
- 對發出 API 請求有基本的熟悉度

## Agent Skills 概觀

預建的 Agent Skills 以專業知識擴展 Claude 的能力，用於建立文件、分析資料和處理檔案等任務。Anthropic 在 API 中提供以下預建的 Agent Skills：

- **PowerPoint (pptx)：** 建立和編輯簡報
- **Excel (xlsx)：** 建立和分析試算表
- **Word (docx)：** 建立和編輯文件
- **PDF (pdf)：** 生成 PDF 文件

> [!note] Note
> 若要建立自訂 Skills，請參閱 [Agent Skills Cookbook](https://platform.claude.com/cookbook/skills-notebooks-01-skills-introduction) ，其中包含建立具有特定領域專業知識的 Skills 的範例。

## 步驟 1：列出可用的 Skills

首先，查看有哪些可用的 Skills。使用 Skills API 列出所有由 Anthropic 管理的 Skills。每個語言分頁都是一個連續腳本的節錄，所有 import 和用戶端設定都在頂部：

```
# 列出由 Anthropic 管理的 Skills
ant beta:skills list --source anthropic
```

您會看到以下 Skills： `pptx` 、 `xlsx` 、 `docx` 和 `pdf` 。

此 API 會回傳每個 Skill 的中繼資料：其名稱和描述。Claude 在啟動時載入這些中繼資料，以判斷哪些 Skills 可用。這是「progressive disclosure」（漸進式揭露）的第一層，Claude 在此階段發現 Skills，但尚未載入其完整指示。

## 步驟 2：建立簡報

使用 PowerPoint Skill 建立一份關於再生能源的簡報。在 Messages API 中使用 `container` 參數指定 Skills：

```
# 使用 PowerPoint Skill 建立訊息
response=$(ant beta:messages create --format json \
  --beta skills-2025-10-02 <<'YAML'
model: claude-opus-5
max_tokens: 16000
container:
  skills:
    - type: anthropic
      skill_id: pptx
      version: latest
messages:
  - role: user
    content: Create a presentation about renewable energy with 5 slides
tools:
  - type: code_execution_20260521
    name: code_execution
YAML
)

jq -r '"stop_reason=\(.stop_reason), blocks=\(.content | length)"' <<<"$response"
```

此請求包含以下部分：

- **`model` ：** 一個 [支援程式碼執行工具的模型](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool#model-compatibility)
- **`container.skills` ：** 指定 Claude 可以使用哪些 Skills
- **`type: "anthropic"` ：** 表示這是由 Anthropic 管理的 Skill
- **`skill_id: "pptx"` ：** PowerPoint Skill 的識別碼
- **`version: "latest"` ：** Skill 版本設定為最近發布的版本
- **`tools` ：** 啟用程式碼執行（Skills 所必需）
- **Beta 標頭：** `skills-2025-10-02`

> [!note] Note
> 本頁的範例使用 `code_execution_20260521` 工具版本，該版本已正式推出，只需要 `skills-2025-10-02` beta 標頭。步驟 3 的程式碼會解析目前工具版本回傳的結果類型。Skills 也適用於較舊的 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) 版本，例如 `code_execution_20250825` ：任何目前的程式碼執行工具版本都能滿足 Skills 的需求。如果您使用不同的版本，請保持其工具 `type` 和任何 beta 標頭與程式碼執行工具頁面一致，並始終包含 `skills-2025-10-02` 。

當您發出此請求時，Claude 會自動將您的任務與相關的 Skill 配對。因為您要求建立簡報，Claude 判斷 PowerPoint Skill 是相關的，並載入其完整指示：這是漸進式揭露的第二層。然後 Claude 執行該 Skill 的程式碼來建立您的簡報。

簡報是在程式碼執行容器中建立並儲存為檔案的。步驟 2 的 `response` 包含一個帶有檔案 ID 的檔案參照。擷取檔案 ID 並使用 Files API 下載檔案。此範例會將其儲存到您系統的暫存目錄：

```
# 擷取檔案 ID。程式碼執行工具會透過其 Bash 子工具
# 執行 Skill 的程式碼，產生的檔案會以 bash_code_execution_output
# 項目的形式出現在 bash_code_execution_tool_result 區塊中。
file_id=$(jq -r '
  last(
    .content[]
    | select(.type == "bash_code_execution_tool_result")
    | .content
    | select(.type == "bash_code_execution_result")
    | .content[].file_id
  ) // empty
' <<<"$response")

if [[ -n "$file_id" ]]; then
  # 下載並儲存檔案
  output_path="${TMPDIR:-/tmp}/renewable_energy.pptx"
  ant beta:files download --file-id "$file_id" --output "$output_path"
  echo "Presentation saved to $output_path"
fi
```

> [!note] Note
> 有關處理生成檔案的完整詳細資訊，請參閱程式碼執行工具文件中的 [擷取生成的檔案](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool#retrieve-generated-files) 。

試試這些變化：

### 建立試算表

```
ant beta:messages create --format json \
  --beta skills-2025-10-02 <<'YAML' | jq -r '"stop_reason=\(.stop_reason)"'
model: claude-opus-5
max_tokens: 16000
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
messages:
  - role: user
    content: Create a quarterly sales tracking spreadsheet with sample data
tools:
  - type: code_execution_20260521
    name: code_execution
YAML
```

### 建立 Word 文件

```
ant beta:messages create --format json \
  --beta skills-2025-10-02 <<'YAML' | jq -r '"stop_reason=\(.stop_reason)"'
model: claude-opus-5
max_tokens: 16000
container:
  skills:
    - type: anthropic
      skill_id: docx
      version: latest
messages:
  - role: user
    content: Write a 2-page report on the benefits of renewable energy
tools:
  - type: code_execution_20260521
    name: code_execution
YAML
```

### 生成 PDF

```
ant beta:messages create --format json \
  --beta skills-2025-10-02 <<'YAML' | jq -r '"stop_reason=\(.stop_reason)"'
model: claude-opus-5
max_tokens: 16000
container:
  skills:
    - type: anthropic
      skill_id: pdf
      version: latest
messages:
  - role: user
    content: Generate a PDF invoice template
tools:
  - type: code_execution_20260521
    name: code_execution
YAML
```

了解如何撰寫 Claude 能夠成功發現和使用的有效 Skills。

了解如何使用 Agent Skills 透過 API 擴展 Claude 的能力。

[建立自訂 Skills](https://platform.claude.com/docs/zh-TW/api/skills/create-skill)

上傳您自己的 Skills 以執行專門任務。

了解 Claude Code 中的 Skills。

[Agent Skills Cookbook](https://platform.claude.com/cookbook/skills-notebooks-01-skills-introduction)

探索範例 Skills 和實作模式。

Was this page helpful?