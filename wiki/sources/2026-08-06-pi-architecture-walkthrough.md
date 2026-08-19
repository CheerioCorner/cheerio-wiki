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

1. **Context Initialization**（上下文初始化）`[01:51]`
   - 載入 system prompt（~20 行，極簡）`[02:11]`
   - 追加 `AGENTS.md`（home + cwd）`[02:43]`
   - 追加 skills descriptions（markup 格式）`[03:16]`
   - 追加 tools descriptions `[03:26]`
   - 追加 message history（或 compaction summary）+ current message `[03:32]`

2. **Context Transformation**（上下文轉換）`[04:10]`
   - 檢查是否需要 compaction `[04:17]`
   - 若需要，用 LLM 摘要 history，替換為 summary `[04:29]`

3. **LLM Call + Tool Loop** `[04:48]`
   - 呼叫 LLM（OpenAI / Anthropic / Kimi / Minimax 等）`[04:53]`
   - LLM 可回傳 tool call → 工具執行 → 回傳結果 → LLM 決定是否再呼叫 `[05:07]`
   - 可達數百次 tool call（複雜任務）或僅幾次（搜尋）`[05:26]`
   - LLM 決定不再 tool call 時，回覆最終回應 `[05:37]`

> 關鍵：agentic loop 完全從零撰寫，不依賴 OpenAI Agents SDK 等現成庫 `[06:04]`。

### 二、Sessions & Memory

**儲存位置**：`~/.pi/agent/sessions/<working-directory>/` `[07:23]`

**格式**：JSONL（每行一個 JSON 物件），包含 role、message、ID、parent ID、timestamp `[08:12]`

**Tree Structure**（非 List）：
- 每個 message 有 `id` 與 `parent` 欄位 `[10:13]`
- 透過 `/tree` 指令導覽 `[10:03]`
- 支援 fork（分叉）：從同一 parent 建立不同對話分支 `[10:54]`
- 所有分支在同一 JSONL 檔案中

> 這是 Pi 的一大亮點——相較於線性 list，tree 結構讓 session 導覽與 fork 非常直覺。

### 三、Tools（預設 4 個）`[15:09]`

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
- 註冊新 tools `[18:06]`
- 訂閱 events（tool_call, agent_response, user_message 等）`[18:14]`
- 註冊 commands `[18:50]`
- 新增 keyboard shortcuts `[18:54]`
- 新增 CLI flags `[18:59]`
- 更新 system prompt `[19:02]`
- 渲染 custom messages `[19:07]`

⚠️ Extension 在系統上載入並執行程式碼——不要安裝不可信的第三方套件 `[19:48]`。

### 五、System Prompt

- **預設**：~20 行，極簡（"You are a helpful Pi assistant"）`[20:27]`
- **覆寫方式**：
  - `.pi/system.md`（workspace 級）`[21:56]`
  - `--system-prompt` CLI flag `[22:09]`
- **追加方式**：`.pi/append-system.md`（插在 "you are pi" 之後）`[20:46]`
- **Skills 區塊**：以 markup 格式列出所有 skill（name + description），供 TUI 解析 `[21:11]`

### 六、Compaction（上下文壓縮）

- **觸發時機**：`agent_end`（回合結束）`[28:13]`、`before_prompt`（發送前）`[28:29]`
- **token 計算**：agent 回覆後立即測量 token 數 `[28:57]`；優先用 LLM provider 回傳的 context tokens；若無則累加 `usage.input + usage.output + cache.read + cache.write` `[29:33]`
- **壓縮 prompt**：結構化 checkpoint summary（goal / constraints / progress / decisions / next steps / critical context）`[30:42]`
- **不使用**字元數除以 4 的粗估方式 `[27:19]`

### 七、Skills 機制

1. System prompt 中列出所有 skill（markup 格式：name + description）`[34:35]`
2. 使用者執行 `/skill:<name>` 時，**interactive layer 攔截**，不送入 core `[35:31]`
3. 替換為 `<skill>` markup（含 name、description、location）`[36:03]`
4. LLM 收到後，依 prompt 指示用 `read` 工具讀取 location 檔案 `[37:01]`
5. Skill 內容不會自動注入——LLM 主動讀取

> 與某些 agent（直接替換 skill 內容）不同，Pi 選擇讓 LLM 自行讀取，保留 tool call 的可觀測性。

### 八、Pi Interactive（TUI）

- **完全自建**，不使用 Textual 等框架 `[25:56]`
- **Component-based**：每個元件負責自己的 rendering、inputs、動態更新 `[26:02]`
- 訂閱 agent core events `[26:31]`
- 可替換為其他 UI（CLI / Web）

### 九、CLI Entry Point

1. `client.ts`：接收 `pi` 命令，設定 process title，呼叫 `main()` `[23:39]`
2. `main.ts`：
   - 解析 arguments `[24:14]`
   - 解析 configuration（cwd 等）`[24:17]`
   - 載入 extensions `[24:25]`
   - 建立 agent session（初始化 PyCore）`[24:30]`
   - 依模式執行：interactive / RPC / print-to-STDIO `[24:46]`

## 與現有頁面的關係

本 source note 補充了以下現有頁面未涵蓋的細節：

- **[[wiki/entities/pi-agent-core]]**：session tree 結構、compaction 觸發時機與 token 計算、skills 機制（intercept → markup → read tool）、TUI component-based 設計
- **[[wiki/entities/pi-mono]]**：CLI entry point（client.ts → main.ts）、extensions 的 events 系統、read-only mode（grep + find）
- **[[wiki/sources/2026-02-10-pi-agent-core-design]]**：本影片從「使用者/教學」角度補充了程式碼分析的角度

## 相關頁面
- Entities: [[wiki/entities/pi-agent-core]]、[[wiki/entities/pi-mono]]
- Sources: [[wiki/sources/2026-02-10-pi-agent-core-design]]、[[wiki/sources/2026-08-05-pi-github-readme]]
- Concepts: [[wiki/concepts/minimal-agent-philosophy]]、[[wiki/concepts/late-conversion]]
