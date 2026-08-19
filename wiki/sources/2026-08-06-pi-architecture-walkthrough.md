---
title: "Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained"
type: source
created: 2026-08-06
updated: 2026-08-06
sources: 1
provenance:
  - kind: raw
    path: raw/youtube/pi-architecture-walkthrough.md
tags: [pi, architecture, agent-core, sessions, tools, extensions, skills, compaction, tui]
collection: sources
topics: [pi-agent-ecosystem, extension-dev, skill-cases-and-comparisons]
canonical: sources/2026-08-06-pi-architecture-walkthrough
provenance_raw: "raw/youtube/pi-architecture-walkthrough.md"
provenance_url: https://www.youtube.com/watch?v=gTeujlv8qK0
---

> 來源：[YouTube — Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained](https://www.youtube.com/watch?v=gTeujlv8qK0)
> 原始字幕：[[raw/youtube/pi-architecture-walkthrough|raw transcript]]

## 一句話
Pi 的架構從 agent core 到 interactive layer 的完整 walkthrough，涵蓋 agentic loop、session tree、tools、extensions、system prompt、compaction 與 skills 機制。

## 重點摘要

### 一、Agent Core（PyCore）— Agentic Loop

Pi 的核心是一個 agentic loop，每次對話都經過三個步驟：

1. **Context Initialization**（上下文初始化）`[1:52]`
   - 載入 system prompt（~20 行，極簡）`[2:12]`
   - 追加 `AGENTS.md`（home + cwd）`[2:43]`
   - 追加 skills descriptions（markup 格式）`[3:16]`
   - 追加 tools descriptions `[3:26]`
   - 追加 message history（或 compaction summary）+ current message `[3:32]`

2. **Context Transformation**（上下文轉換）`[4:11]`
   - 檢查是否需要 compaction `[4:22]`
   - 若需要，用 LLM 摘要 history，替換為 summary `[4:35]`

3. **LLM Call + Tool Loop** `[4:49]`
   - 呼叫 LLM（OpenAI / Anthropic / Kimi / Minimax 等）`[4:54]`
   - LLM 可回傳 tool call → 工具執行 → 回傳結果 → LLM 決定是否再呼叫 `[5:07]`
   - 可達數百次 tool call（複雜任務）或僅幾次（搜尋）`[5:27]`
   - LLM 決定不再 tool call 時，回覆最終回應 `[5:37]`

> 關鍵：agentic loop 完全從零撰寫，不依賴 OpenAI Agents SDK 等現成庫 `[6:01]`。

### 二、Sessions & Memory

**儲存位置**：`~/.pi/agent/sessions/<working-directory>/` `[7:23]`

**格式**：JSONL（每行一個 JSON 物件），包含 role、message、ID、parent ID、timestamp `[8:20]`

**Tree Structure**（非 List）：
- 每個 message 有 `id` 與 `parent` 欄位 `[9:53]`
- 透過 `/tree` 指令導覽 `[10:07]`
- 支援 fork（分叉）：從同一 parent 建立不同對話分支 `[10:50]`
- 所有分支在同一 JSONL 檔案中

> 這是 Pi 的一大亮點——相較於線性 list，tree 結構讓 session 導覽與 fork 非常直覺。

### 三、Tools（預設 4 個）

| 工具 | 功能 |
|---|---|
| `read` | 讀取檔案 |
| `bash` | 執行 shell 命令 |
| `edit` | 編輯檔案 |
| `write` | 寫入檔案 |

**隱藏的 2 個**：`grep` 與 `find`——預設停用，僅在 read-only 模式（`--tools read,grep,find`）啟用，用於 RPC / 程式化呼叫 `[16:08]`。

> 建議額外安裝 `web-search` extension。

### 四、Extensions（TypeScript 套件）

Extension 可以：
- 註冊新 tools `[18:14]`
- 訂閱 events（tool_call, agent_response, user_message 等）`[18:18]`
- 註冊 commands `[19:54]`
- 新增 keyboard shortcuts `[19:54]`
- 新增 CLI flags `[19:02]`
- 更新 system prompt `[19:07]`
- 渲染 custom messages `[19:12]`

⚠️ Extension 在系統上載入並執行程式碼——不要安裝不可信的第三方套件 `[19:48]`。

### 五、System Prompt

- **預設**：~20 行，極簡（"You are a helpful Pi assistant"）`[20:22]`
- **覆寫方式**：
  - `.pi/system.md`（workspace 級）`[22:00]`
  - `--system-prompt` CLI flag `[22:14]`
- **追加方式**：`.pi/append-system.md`（插在 "you are pi" 之後）`[20:56]`
- **Skills 區塊**：以 markup 格式列出所有 skill（name + description），供 TUI 解析 `[21:11]`

### 六、Compaction（上下文壓縮）

- **觸發時機**：`agent_end`（回合結束）與 `before_prompt`（發送前）`[28:17]`
- **token 計算**：優先用 LLM provider 回傳的 context tokens；若無則累加 `usage.input + usage.output + cache.read + cache.write` `[29:00]`
- **壓縮 prompt**：結構化 checkpoint summary（goal / constraints / progress / decisions / next steps / critical context）`[30:54]`
- **不使用**字元數除以 4 的粗估方式 `[27:23]`

### 七、Skills 機制

1. System prompt 中列出所有 skill（markup 格式：name + description）`[34:12]`
2. 使用者執行 `/skill:<name>` 時，**interactive layer 攔截**，不送入 core `[35:31]`
3. 替換為 `<skill>` markup（含 name、description、location）`[36:03]`
4. LLM 收到後，依 prompt 指示用 `read` 工具讀取 location 檔案 `[37:21]`
5. Skill 內容不會自動注入——LLM 主動讀取

> 與某些 agent（直接替換 skill 內容）不同，Pi 選擇讓 LLM 自行讀取，保留 tool call 的可觀測性。

### 八、Pi Interactive（TUI）

- **完全自建**，不使用 Textual 等框架 `[25:45]`
- **Component-based**：每個元件負責自己的 rendering、inputs、動態更新 `[25:51]`
- 訂閱 agent core events `[26:36]`
- 可替換為其他 UI（CLI / Web）

### 九、CLI Entry Point

1. `client.ts`：接收 `pi` 命令，設定 process title，呼叫 `main()` `[23:49]`
2. `main.ts`：
   - 解析 arguments `[24:14]`
   - 解析 configuration（cwd 等）`[24:24]`
   - 載入 extensions `[24:30]`
   - 建立 agent session（初始化 PyCore）`[24:33]`
   - 依模式執行：interactive / RPC / print-to-STDIO `[24:47]`

## 與現有頁面的關係

本 source note 補充了以下現有頁面未涵蓋的細節：

- **[[wiki/entities/pi-agent-core]]**：session tree 結構、compaction 觸發時機與 token 計算、skills 機制（intercept → markup → read tool）、TUI component-based 設計
- **[[wiki/entities/pi-mono]]**：CLI entry point（client.ts → main.ts）、extensions 的 events 系統、read-only mode（grep + find）
- **[[wiki/sources/2026-02-10-pi-agent-core-design]]**：本影片從「使用者/教學」角度補充了程式碼分析的角度

## 相關頁面
- Entities: [[wiki/entities/pi-agent-core]]、[[wiki/entities/pi-mono]]
- Sources: [[wiki/sources/2026-02-10-pi-agent-core-design]]、[[wiki/sources/2026-08-05-pi-github-readme]]
- Concepts: [[wiki/concepts/minimal-agent-philosophy]]、[[wiki/concepts/late-conversion]]
