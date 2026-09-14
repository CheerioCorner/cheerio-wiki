---
title: "OpenCodeReview 一手查證（取代 2026-08-20 Gemini 研究失真部分）"
type: source
created: 2026-09-14
sources: 6
tags: [verification, code-review, alibaba, security, source-correction]
topics: [ai-development-tools, extension-dev]
provenance_url: "https://github.com/alibaba/open-code-review"
---

# OpenCodeReview 一手查證（2026-09-14）

> Cheer 詢問 OCR（OpenCodeReview）的使用評估、資安疑慮、對 AI token 的影響，Claude 直接讀官方 repo 一手文件核對，取代 [[wiki/sources/2026-08-20-opencode-review-deep-research|2026-08-20 Gemini 深度研究]] 裡查無實據的部分。

## 為什麼要重新查證

2026-08-20 那份 Gemini 深度研究的「來源列表」裡有多個 URL 格式本身就不可能是真實連結——例如 `https://ycombinator.com/news/item?id=opencode_review_discussion`（Hacker News 正確網域是 `news.ycombinator.com`，且 id 必為數字，不會是敘述性 slug）、`https://reddit.com/r/LocalLLaMA/comments/opencode_review_ai`（Reddit comment 網址的 id 段必為 base36 編碼，不會是描述性字串）。判定為捏造引用，這次查證未採用其中任何結論，改為直接讀官方 GitHub repo。

## 查證結果

### 1. 專案真實性
`alibaba/open-code-review` 確為真實 repo（org: alibaba，Apache-2.0，Go 語言），2026-09-14 查得 24,535 stars（原研究記載的 19.3k 是較早的快照，非錯誤，只是過時）。最新版本 `v1.12.1`（2026-09-14 發布）。

### 2. 兩種執行模式（原研究未提及，是最關鍵的補充）
- **預設模式**（`ocr review`）：OCR 自己呼叫 LLM，需要 `ocr config provider` 設定 API key（支援 Anthropic／OpenAI／Gemini／DeepSeek／Bedrock／多家中國區廠商，或任何 OpenAI 相容自訂端點，含本機模型），每次審查是獨立的 LLM 費用。
- **Delegation Mode**（`ocr delegate preview` + `ocr delegate rule`）：不需要設定任何 LLM，OCR 只做確定性的檔案篩選與規則解析，實際審查交給你已經在用的 coding agent（Claude Code／Codex／Cursor 等），吃既有訂閱額度，零額外 LLM 成本。
- 來源：<https://open-codereview.ai/docs/delegate>（原始碼：`pages/src/content/docs/en/integrations/delegate.md`）

### 3. 資安：官方自評 + 真實已修復 CVE
- `ASSURANCE_CASE.md` 用 Saltzer & Schroeder 設計原則 + OWASP Top 10/CWE Top 25 逐條自我檢核（廠商自述，非第三方稽核，但精確到檔名/行號）：command injection（僅呼叫 `git`，硬編碼子命令，不經 shell）、API key（只讀環境變數，不落 log）、path traversal（`pathutil.WithinBase()` 驗證）、viewer 防 DNS rebinding（host allowlist）皆列為已緩解。agent 有一個 shell 工具，但只執行使用者自己設定的腳本，不是 LLM 可任意生成指令執行。
- **真實已發布並修復的 CVE**：`GHSA-wwg6-qfxw-xffj`（CVSS 5.3 medium，CWE-22/59/73/200）——`.opencodereview/rule.json` 若指向絕對路徑或 symlink，舊版沒驗證是否仍在 repo 內，攻擊者可讓 `ocr` 讀出機器上任意 512KB 以內的 `.md/.txt/.markdown` 檔並送進 LLM prompt（純讀檔外送，非程式碼執行）。2026-09-10 發布，`v1.11.1` 修復，目前版本已修復。風險前提是審查一個**不完全信任**的 repo（自架 CI checkout 未信任分支，或本機 clone 陌生 repo 執行 `ocr review`）；只審自己信任的程式碼不受影響。
- 來源：<https://github.com/alibaba/open-code-review/blob/main/SECURITY.md>、<https://github.com/alibaba/open-code-review/blob/main/ASSURANCE_CASE.md>、<https://github.com/alibaba/open-code-review/security/advisories/GHSA-wwg6-qfxw-xffj>

### 4. Telemetry / 隱私
OpenTelemetry 整合，**預設關閉**；開啟後只輸出聚合指標（審查時長、token 數、模型名稱、狀態），官方文件明確保證「never attaches prompt content to spans or events」——不含程式碼或 prompt 內容，且目的地是使用者自己配置的 OTLP endpoint，不會回傳給 Alibaba。FAQ 官方原話：「OCR sends your diffs (and optional read-tool snippets) to whatever LLM endpoint you configured. Nothing else leaves your machine.」
來源：<https://open-codereview.ai/docs/telemetry>、<https://open-codereview.ai/docs/faq>

### 5. Token / 成本
官方 benchmark（README + FAQ）：跟同一底層模型下的 Claude Code 通用 Agent 比，token 消耗約為 **1/9**，precision/F1 更高，recall 較低（precision-first 設計取捨）。Benchmark 基於 50 個開源專案、200 個真實 PR、1,505 筆經 80+ 資深工程師交叉驗證的標註，資料集公開於 Hugging Face（<https://huggingface.co/datasets/Alibaba-Aone/aacr-bench>）——這點原研究有提到但沒附可查證的資料集連結。
成本槓桿（FAQ 明列）：`--effort low/medium/high`（1/2/3 輪，成本隨輪數增加）、plan phase 觸發門檻（單檔 ≥50 行或合計 ≥100 行才觸發，調高門檻可省錢）、`MAX_TOOL_REQUEST_TIMES`（預設 100 輪上限）。另有兩個零成本探路指令：`ocr review --preview`（純本機篩檔，不呼叫 LLM）與 `ocr delegate preview`。

## 相關頁面

- [[wiki/entities/open-code-review|OpenCodeReview]] — 已依本次查證更新
- [[wiki/sources/2026-08-20-opencode-review-deep-research|2026-08-20 Gemini 深度研究]] — 架構與社群爆紅原因部分仍可信，來源列表與部分細節已被本頁取代
