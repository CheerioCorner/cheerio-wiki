---
title: "透過 API 使用 Agent Skills"
description: "了解如何透過 API 使用 Agent Skills 來擴展 Claude 的能力。"
source_url: "https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide"
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
# 透過 API 使用 Agent Skills

> Source: [透過 API 使用 Agent Skills](https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide)
> Clipped: 2026-08-14

Agent Skills 透過組織化的資料夾（包含指令、腳本和資源）來擴展 Claude 的能力。本指南將向您展示如何透過 Claude API 使用預建和自訂的 Skills。

> [!note] Note
> 如需完整的 API 參考資料，包括請求/回應結構描述和所有參數，請參閱：
> 
> - [Skill 管理 API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/list) - Skills 的 CRUD 操作
> - [Skill 版本 API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/versions/list) - 版本管理

> [!note] Note
> 關於「zero data retention」（零資料保留），即 ZDR 如何適用於此功能，請參閱 [API 與資料保留](https://platform.claude.com/docs/zh-TW/manage-claude/api-and-data-retention) 。

## 快速連結

[開始在 API 中使用 Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart)

了解如何在 10 分鐘內使用 Agent Skills 透過 Claude API 建立文件。

[Skill 編寫最佳實務](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices)

了解如何編寫有效的 Skills，讓 Claude 能夠發現並成功使用。

## 概述

> [!note] Note
> 如需深入了解 Agent Skills 的架構和實際應用，請閱讀工程部落格文章： [Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) 。

Skills 透過 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) 與 Messages API 整合。無論是使用由 Anthropic 管理的預建 Skills，還是您上傳的自訂 Skills，整合方式都完全相同：兩者都需要程式碼執行，並使用相同的 `container` 結構。

### 使用 Skills

無論來源為何，Skills 在 Messages API 中的整合方式都相同。您在 `container` 參數中指定 Skills，包含 `skill_id` 、 `type` 和選用的 `version` ，它們會在程式碼執行環境中運行。

您可以使用來自兩種來源的 Skills：

| 面向 | Anthropic Skills | 自訂 Skills |
| --- | --- | --- |
| **Type 值** | `anthropic` | `custom` |
| **Skill ID** | 簡短名稱： `pptx` 、 `xlsx` 、 `docx` 、 `pdf` | 自動產生： `skill_01AbCdEfGhIjKlMnOpQrStUv` |
| **版本格式** | 基於日期： `20251013` 或 `latest` | Epoch 時間戳記： `1759178010641129` 或 `latest` |
| **管理方式** | 由 Anthropic 預建和維護 | 透過 [Skills API](https://platform.claude.com/docs/zh-TW/api/beta/skills/create) 上傳和管理 |
| **可用性** | 所有使用者皆可使用 | 僅限您的工作區私有使用 |

兩種 Skill 來源都會由 [List Skills 端點](https://platform.claude.com/docs/zh-TW/api/beta/skills/list) 回傳（使用 `source` 參數進行篩選）。整合方式和執行環境完全相同。唯一的差異在於 Skills 的來源以及管理方式。

### 先決條件

要使用 Skills，您需要：

1. 來自 [Claude Console](https://platform.claude.com/settings/keys) 的 **Claude API 金鑰**
2. **Beta 標頭：**
	- `code-execution-2025-08-25` - 啟用程式碼執行（Skills 必需）
		- `skills-2025-10-02` - 啟用 Skills API
		- `files-api-2025-04-14` - 僅當您使用 [Files API](https://platform.claude.com/docs/zh-TW/build-with-claude/files) 上傳輸入檔案或下載 Skill 產生的檔案時才需要
3. 在您的請求中啟用\*\* [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) \*\*

Skills 需要程式碼執行工具，因此請使用其 [模型相容性清單](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool#model-compatibility) 中的模型。

---

## 在 Messages 中使用 Skills

### Container 參數

Skills 是透過 Messages API 中的 `container` 參數來指定。每個請求最多可包含 8 個 Skills。

Anthropic 和自訂 Skills 的結構完全相同。指定必要的 `type` 和 `skill_id` ，並可選擇性地包含 `version` 以固定到特定版本：

```
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: pptx
      version: latest
messages:
  - role: user
    content: Create a presentation about renewable energy
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

當 Skills 建立文件（Excel、PowerPoint、PDF、Word）時，它們會在回應中回傳 `file_id` 屬性。您必須使用 Files API 來下載這些檔案。

**運作方式：**

1. Skills 在程式碼執行期間建立檔案。
2. 回應會在程式碼執行工具結果區塊中包含每個建立檔案的 `file_id` （請參閱 [回應格式](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool#response-format) ）。
3. 使用 Files API 下載實際的檔案內容。
4. 儲存到本機或依需求處理。

若要提供輸入檔案供 Skills 處理，請 [使用 Files API 上傳檔案](https://platform.claude.com/docs/zh-TW/build-with-claude/files#uploading-a-file) ，並在您的請求中透過 [container upload 區塊](https://platform.claude.com/docs/zh-TW/build-with-claude/files#container-upload-blocks) 參照它們。

**範例：建立並下載 Excel 檔案**

```
# 步驟 1：使用 xlsx Skill 建立檔案
# 步驟 2：使用 --transform（GJSON 路徑）從回應中擷取 file_id
FILE_ID=$(ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 \
  --transform 'content.#.content.content.#.file_id|@flatten|0' \
  --raw-output <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
messages:
  - role: user
    content: Create an Excel file with a simple budget spreadsheet
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
)

# 步驟 3：從檔案中繼資料取得檔案名稱
FILENAME=$(ant beta:files retrieve-metadata \
  --file-id "$FILE_ID" \
  --transform filename \
  --raw-output)

# 步驟 4：使用 Files API 下載檔案
ant beta:files download \
  --file-id "$FILE_ID" \
  --output "$FILENAME" > /dev/null

printf 'Downloaded: %s\n' "$FILENAME"
```

**其他 Files API 操作：**

```
# 取得檔案中繼資料
ant beta:files retrieve-metadata \
  --file-id "$FILE_ID" \
  --transform '{filename,size_bytes}' \
  --format yaml

# 列出所有檔案
ant beta:files list \
  --transform '{filename,created_at}' \
  --format yaml

# 刪除檔案
ant beta:files delete --file-id "$FILE_ID" >/dev/null
```

> [!note] Note
> 如需完整詳細資訊，請參閱 [Files API](https://platform.claude.com/docs/zh-TW/build-with-claude/files) 。

### 多輪對話

回應的 `container` 物件包含容器的 `id` 和 `expires_at` 時間戳記（請參閱 [容器重複使用](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool#container-reuse) 以了解生命週期詳細資訊）。透過指定容器 ID，在多個訊息之間重複使用相同的容器：

```
# 第一個請求會建立容器
CONTAINER_ID=$(ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 \
  --transform container.id \
  --raw-output <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - {type: anthropic, skill_id: xlsx, version: latest}
messages:
  - role: user
    content: Create a sample sales dataset and analyze it
tools:
  - {type: code_execution_20250825, name: code_execution}
YAML
)

# 使用相同容器繼續對話
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  id: $CONTAINER_ID  # Reuse container
  skills:
    - {type: anthropic, skill_id: xlsx, version: latest}
messages:
  - role: user
    content: Create a sample sales dataset and analyze it
  - role: assistant
    content: []  # the assistant's text from the first response
  - role: user
    content: What was the total revenue?
tools:
  - {type: code_execution_20250825, name: code_execution}
YAML
```

### 長時間運行的操作

Skills 可能會執行需要多輪的操作。請處理 `pause_turn` 停止原因：

```
RESP=$(mktemp)

# 初始請求：將完整的 JSON 回應擷取至暫存檔案
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 \
  > "$RESP" <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages:
  - role: user
    content: Generate and process a large sample dataset
tools:
  - type: code_execution_20250825
    name: code_execution
YAML

# 若 stop_reason 為 "pause_turn"，則在同一容器中繼續，
# 將先前回應的 content 陣列作為 assistant 回合附加至 messages。
# 重複此步驟，直到 stop_reason 不再是 "pause_turn" 為止。
CONTAINER_ID=$(jq -r '.container.id' "$RESP")

ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 \
  > "$RESP" <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  id: $CONTAINER_ID
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages: [] # replace with conversation history + prior assistant content
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

> [!note] Note
> 回應可能包含 `pause_turn` 停止原因，這表示 API 暫停了長時間運行的 Skill 操作。您可以在後續請求中原樣提供該回應，讓 Claude 繼續其回合；或者如果您想中斷對話並提供額外指引，也可以修改內容。

### 使用多個 Skills

在單一請求中組合多個 Skills 以處理複雜的工作流程：

```
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
    - type: anthropic
      skill_id: pptx
      version: latest
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages:
  - role: user
    content: Analyze sales data and create a presentation
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

---

## 管理自訂 Skills

### 建立 Skill

Skill 套件是一個目錄，其頂層包含一個具有 `name` 和 `description` YAML frontmatter 的 `SKILL.md` 檔案，以及任何支援的腳本或資源。請參閱 [開始在 API 中使用 Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart) 來編寫一個 Skill，並參閱範例後方的 **需求** 清單以了解完整的限制條件。

上傳您的自訂 Skill 以使其在您的工作區中可用。您可以上傳 zip 壓縮檔或個別檔案物件。Python SDK 也提供了一個接受目錄路徑的 `files_from_dir` 輔助函式。

檔案是透過您附加的檔案名稱來識別。個別檔案上傳必須在其路徑中保留共同的頂層目錄（cURL 範例中的 `;filename=` 後綴以及 SDK 範例中的 filename 引數）。zip 壓縮檔必須包含 Skill 目錄作為其單一頂層項目。對於本逐步說明中的 Skill，請使用 `zip -r financial_skill.zip financial_skill/` 建立一個壓縮檔，並將其替換 zip 上傳選項中的 `example_skill.zip` 預留位置。

```
ant beta:skills create \
  --file example_skill.zip \
  --beta skills-2025-10-02

# 逐檔上傳需要含路徑的檔名，而 CLI 目前
# 無法設定此項。請改為上傳 zip 封存檔。
```

**需求：**

- 必須在頂層包含一個 `SKILL.md` 檔案
- 所有檔案必須在其路徑中指定共同的根目錄
- 頂層目錄名稱必須與 `SKILL.md` frontmatter 中的 `name` 相符（不區分大小寫和底線： `Financial_Skill` 與 `financial-skill` 相符）
- `display_title` 為選用：省略時，會從 `SKILL.md` 的 `name` 衍生；明確指定的值在您工作區的自訂 Skills 中必須是唯一的
- 總上傳大小必須小於 30 MB（未壓縮）
- YAML frontmatter 需求：
	- `name` ：最多 64 個字元，僅限小寫字母/數字/連字號，不含 XML 標籤，不含保留字（「anthropic」、「claude」）
		- `description` ：最多 1024 個字元，非空白，不含 XML 標籤

如需完整的請求/回應結構描述，請參閱 [Create Skill API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/create) 。

### 列出 Skills

擷取您工作區可用的所有 Skills，包括 Anthropic 預建 Skills 和您的自訂 Skills。使用 `source` 參數依 Skill 類型篩選：

```
# 列出所有 Skills
ant beta:skills list

# 僅列出自訂 Skills
ant beta:skills list --source custom
```

請參閱 [List Skills API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/list) 以了解分頁和篩選選項。

### 擷取 Skill

取得特定 Skill 的詳細資訊：

```
ant beta:skills retrieve \
  --skill-id skill_01AbCdEfGhIjKlMnOpQrStUv
```

### 刪除 Skill

要刪除 Skill，您必須先刪除其所有版本：

```
# 步驟 1：列出所有版本，然後逐一刪除
ant beta:skills:versions list \
  --skill-id skill_01AbCdEfGhIjKlMnOpQrStUv \
  --transform version \
  --raw-output

# 對列表傳回的每個版本 ID 重複此操作
ant beta:skills:versions delete \
  --skill-id skill_01AbCdEfGhIjKlMnOpQrStUv \
  --version 1759178010641129 >/dev/null

# 步驟 2：刪除該 Skill
ant beta:skills delete \
  --skill-id skill_01AbCdEfGhIjKlMnOpQrStUv >/dev/null
```

嘗試刪除仍有現存版本的 Skill 會回傳 400 錯誤。

### 版本管理

Skills 支援版本管理以安全地管理更新：

**Anthropic Skills：**

- 版本使用日期格式： `20251013`
- 隨著更新發布新版本
- 指定確切版本以確保穩定性

**自訂 Skills：**

- 自動產生的 epoch 時間戳記： `1759178010641129`
- 使用 `"latest"` 以始終取得最新版本
- 更新 Skill 檔案時建立新版本

新版本是完整的快照，而非差異更新：每次都要上傳 Skill 的完整檔案集，並使用建立時所用的相同頂層目錄名稱。您省略的檔案不會被保留。以下範例重新上傳 [建立 Skill](#creating-a-skill) 中的完整 `financial_skill/` 套件。

```
# 建立新版本
VERSION_NUMBER=$(ant beta:skills:versions create \
  --skill-id skill_01AbCdEfGhIjKlMnOpQrStUv \
  --file financial_skill.zip \
  --transform version \
  --raw-output)

# 使用特定版本
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: "$VERSION_NUMBER"
messages:
  - role: user
    content: Use updated Skill
tools:
  - type: code_execution_20250825
    name: code_execution
YAML

# 使用最新版本
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages:
  - role: user
    content: Use latest Skill version
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

請參閱 [Create Skill Version API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/versions/create) 以了解完整詳細資訊。

---

## Skills 的載入方式

當您在容器中指定 Skills 時：

1. **中繼資料探索：** Claude 會在系統提示中看到每個 Skill 的中繼資料（名稱、描述）。
2. **檔案載入：** Skill 檔案會被複製到容器中的 `/skills/{skill-name}/` 。目錄名稱是 Skill 的名稱（Anthropic Skill 為 `pptx` ，自訂 Skill 為 `SKILL.md` 中的 `name` ），而非其 `skill_01...` ID。
3. **自動使用：** 當與您的請求相關時，Claude 會自動載入並使用 Skills。
4. **組合：** 多個 Skills 可組合在一起以處理複雜的工作流程。

Claude 僅在需要時才載入完整的 Skill 指令。

---

## 使用案例

Skills 適用於組織和個人工作。組織使用它們來將品牌格式套用到文件、依據公司範本組織筆記和報告，以及執行公司特定的分析程序。個人則使用它們來建立自訂文件範本、專門的資料管線，以及程式碼產生或部署慣例。

### 範例：財務建模

結合 Excel 和自訂 DCF 分析 Skills：

```
# 建立自訂 DCF 分析 Skill
DCF_SKILL_ID=$(ant beta:skills create \
  --file dcf_skill.zip \
  --transform id \
  --raw-output)

# 搭配 Excel 使用以建立財務模型
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
    - type: custom
      skill_id: $DCF_SKILL_ID
      version: latest
messages:
  - role: user
    content: Build a DCF valuation model for a SaaS company
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

---

## 限制與約束

### 請求限制

- **每個請求的最大 Skills 數量：** 8
- **最大 Skill 上傳大小：** 30 MB（所有檔案合計，未壓縮）
- **YAML frontmatter 需求：**
	- `name` ：最多 64 個字元，僅限小寫字母/數字/連字號，不含 XML 標籤，不含保留字（「anthropic」、「claude」）
		- `description` ：最多 1024 個字元，非空白，不含 XML 標籤

### 環境約束

Skills 在程式碼執行容器中運行，具有以下限制：

- **無網路存取：** 無法進行外部 API 呼叫
- **無執行階段套件安裝：** 僅可使用預先安裝的套件
- **隔離環境：** 除非您指定現有的容器 ID，否則會建立全新的容器

請參閱 [程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool) 以了解可用的套件。

---

## 最佳實務

### 何時使用多個 Skills

當任務涉及多種文件類型或領域時，組合使用 Skills：

**適合的使用案例：**

- 資料分析（Excel）+ 簡報建立（PowerPoint）
- 報告產生（Word）+ 匯出為 PDF
- 自訂領域邏輯 + 文件產生

**避免：**

- 包含未使用的 Skills（會影響效能）

### 版本管理策略

本節中的 SDK 分頁顯示要包含在 Messages 請求中的 `container` 值。cURL 和 CLI 分頁則顯示完整的請求。

**用於正式環境：** 固定特定版本，如此 Skill 更新就不會改變您已部署的行為。版本 ID 來自 [版本管理](#versioning) 中的建立版本回應，或來自 [List Skill Versions API](https://platform.claude.com/docs/zh-TW/api/beta/skills/versions/list) 。ID 始終是字串：在 JSON 或 YAML 中請為 epoch 時間戳記 ID 加上引號。

```
# 固定至特定版本以確保穩定性
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: "1759178010641129"  # quoted: the API requires a string
messages:
  - role: user
    content: Analyze the sales data
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

**用於開發環境：** 使用 `latest` 以在您反覆開發時自動取得最新版本。

```
# 在積極開發時使用 latest
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<YAML
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages:
  - role: user
    content: Analyze the sales data
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

### 提示快取考量

如果您使用 [提示快取](https://platform.claude.com/docs/zh-TW/build-with-claude/prompt-caching) ，變更容器中的 Skills 清單會使快取失效。Skills 會以固定順序呈現到系統提示中，因此相同的清單會產生相同的可快取前綴：

```
# Skills 會以固定且對快取友善的順序渲染至系統提示中
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
messages:
  - role: user
    content: Analyze sales data
tools:
  - type: code_execution_20250825
    name: code_execution
YAML

# 變更 Skills 清單（[xlsx] 與 [xlsx, pptx]）會改變前綴：造成快取未命中，而相同的清單則為快取命中
ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: anthropic
      skill_id: xlsx
      version: latest
    - type: anthropic
      skill_id: pptx
      version: latest
messages:
  - role: user
    content: Create a presentation
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
```

為獲得最佳快取效能，請在各請求之間保持 Skills 清單（包括其順序）一致。固定自訂 Skill 版本也有幫助：使用 `"latest"` 時，如果發布新版本改變了 Skill 的描述，可能會使快取的前綴失效。

妥善處理與 Skill 相關的錯誤：

```
if ! RESULT=$(ant beta:messages create \
  --beta code-execution-2025-08-25,skills-2025-10-02 \
  --transform-error error.message \
  --format-error yaml 2>&1 <<'YAML'
model: claude-opus-5
max_tokens: 4096
container:
  skills:
    - type: custom
      skill_id: skill_01AbCdEfGhIjKlMnOpQrStUv
      version: latest
messages:
  - role: user
    content: Process data
tools:
  - type: code_execution_20250825
    name: code_execution
YAML
); then
  case "$RESULT" in
    *skill*)
      printf 'Skill error: %s\n' "$RESULT"
      # 處理技能特定的錯誤
      ;;
    *)
      printf '%s\n' "$RESULT" >&2
      exit 1
      ;;
  esac
fi
```

---

## 資料保留

Agent Skills 不在 ZDR 協議的涵蓋範圍內。Skill 定義和執行資料會依據 Anthropic 的標準資料保留政策進行保留。

如需了解所有功能的 ZDR 適用性，請參閱 [API 與資料保留](https://platform.claude.com/docs/zh-TW/manage-claude/api-and-data-retention) 。

[API 參考](https://platform.claude.com/docs/zh-TW/api/beta/skills/create)

包含所有端點的完整 API 參考

[Skill 編寫最佳實務](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices)

了解如何編寫有效的 Skills，讓 Claude 能夠發現並成功使用。

[程式碼執行工具](https://platform.claude.com/docs/zh-TW/agents-and-tools/tool-use/code-execution-tool)

在沙箱容器中執行 Python 和 bash 程式碼，以分析資料、產生檔案並反覆改進解決方案。

Was this page helpful?