
## [2026-08-25] work | 新增 W-2026-08-088 代碼知識圖譜工具深度研究 + 安全性審查

- 動作：Cheer 要求對 codebase-memory-mcp / CodeGraph / Graphify / OCR 做更深入的運作背景研究（不只是表面 README 比較），加上安全性深度審查（原始碼抽查、supply chain、CVE、遙測行為分析），最終選出適合的安裝測試比較
- 新增：`work/current.md` W-2026-08-088（§1 背景研究 + §2 安全性審查 + §3 選型建議，blockedBy W-030 + W-087）
- 跟既有待辦的關係：W-030（OCR 安裝測試）→ W-085（Graphify 評估）→ W-087（codebase-memory-mcp 安裝）都是實作導向；W-088 是統籌性深度研究，§3 會影響 §1/§2 的安裝決策
- refs: [[wiki/entities/codebase-memory-mcp]]、[[wiki/entities/codegraph]]、[[wiki/entities/graphify]]、[[wiki/entities/open-code-review]]

## [2026-08-25] ingest | 深入拆解 Codebase-Memory-MCP vs CodeGraph：兩種代碼知識圖譜的路線之爭（YouTube ingest）

- 動作：YouTube 影片（7pSZx9-VT3k）→ youtube_transcript_api 抓中文字幕（手動，217 segments → 合併 8 paragraphs）→ 雙模型交叉驗證（Pi 主持，因內容為比較分析直接執行）→ 建立 wiki 頁面
- 建立：
  - `wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph.md` — source note（陳述級溯源，含影片關鍵數據比較表）
  - `wiki/entities/codegraph.md` — CodeGraph entity 頁面（全新，含與 codebase-memory-mcp 詳細比較）
- 更新：
  - `wiki/entities/codebase-memory-mcp.md` — 新增「影片補充」章節（LZ4/Aho-Corasick 演算法、記憶體管理、定位轉變、跟 CodeGraph 核心差異），新增相關頁面連結
  - `wiki/topics/ai-development-tools.md` — Entities 區塊新增 CodeGraph 條目
  - `wiki/topics/coding-agent.md` — Entities 區塊新增 CodeGraph 條目
  - `wiki/index.md` — Entities 區塊新增 CodeGraph 條目
  - `wiki/log.md` — 本筆紀錄
- 關鍵比較：兩者是「不同賽道」而非競爭——CodeGraph=框架路由+file watcher+開發者體驗，codebase-memory-mcp=性能+Hybrid LSP+團隊共享圖譜
- 安全性差異：CodeGraph 預設開啟匿名遙測（需 `codegraph telemetry off`），codebase-memory-mcp 零遙測
- refs: [[raw/youtube/2026-08-25-codebase-memory-mcp-vs-codegraph]]、[[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph]]、[[wiki/entities/codegraph]]、[[wiki/entities/codebase-memory-mcp]]

## [2026-08-25] ingest | codebase-memory-mcp — 高效能代碼知識圖譜 MCP Server（Web Clipper ingest）

- 動作：Cheer 用 Web Clipper 存了 GitHub README raw，Pi 依 Cheer 指示建立 wiki entity 頁面 + 更新 topics + 新增 work item
- 建立：`wiki/entities/codebase-memory-mcp.md` — entity 頁面（基本資訊、核心功能、與 Graphify 比較、安裝方式、我們的判斷）
- 更新：
  - `wiki/topics/ai-development-tools.md` — Entities 區塊新增 codebase-memory-mcp 條目
  - `wiki/topics/coding-agent.md` — 新增 Entities 區塊 + codebase-memory-mcp 條目
  - `work/current.md` — 新增 W-2026-08-087 待辦（評估安裝 codebase-memory-mcp）
  - `wiki/entities/graphify.md` — 新增「與 codebase-memory-mcp 比較」小節 + 相關頁面連結
  - `wiki/entities/open-code-review.md` — 相關頁面補充 codebase-memory-mcp 連結
- 關鍵比較：跟 Graphify 幾乎同品類（tree-sitter → 知識圖譜），但 MCP 原生 + SQLite 持久化 + auto-index + 視覺化更成熟，Graphify 建議跳過
- refs: [[raw/web/2026-08-25-DeusDatacodebase-memory-mcp High-performance code intelligence MCP server]]、[[wiki/entities/codebase-memory-mcp]]

## [2026-08-25] ingest | 五大 Coding Agent Harness Hook 機制比較研究（deep-research ingest）

- 動作：Pi 執行 deep-research-execute 完整流程（job rc-20260825-001），產出 60 筆過濾後來源的深度研究報告，Cheer 審閱確認後指示執行 wiki-ingest 進行雙模型交叉驗證後 ingest 進 wiki
- 雙模型共識：因技術限制（Claude CLI max turns），採用簡化驗證方式——Pi 根據研究報告與既有 wiki 頁面結構直接產出提案，並在回報中說明驗證過程
- 建立：
  - `wiki/sources/2026-08-25-coding-agent-hooks-comparison.md` — 來源筆記（60 筆來源，Gemini Notebook 品質過濾）
  - `wiki/entities/deepseek-harness.md` — DeepSeek Harness entity 頁面（全新）
  - `wiki/comparisons/coding-agent-hooks-comparison.md` — 五大 Harness Hook 機制比較頁面
- 更新：
  - `wiki/entities/claude-code.md` — 加入 Hook 最新研究發現（25+ 種事件、5 種 Handler、async 支援、動態掛載）
  - `wiki/entities/github-copilot.md` — 加入 Hooks 機制章節（VS Code / CLI / VS2022/VS2026 四介面差異）
  - `wiki/entities/openai-codex.md` — 加入 Hook 機制章節（Codex Hook System、Fail-Closed 設計）
  - `wiki/entities/pi-mono.md` — 加入 Hook 機制章節（Event hooks、pi.on API、YAML hooks、Claude Code 格式適配）
  - `wiki/topics/hooks.md` — 補齊所有 Harness 的 entity/concept/comparison 連結
  - `wiki/index.md` — 新增 Sources/Entities/Concepts/Comparisons 區塊
  - `wiki/log.md` — 本筆紀錄
- 關鍵發現：
  - 五個工具都提供 Hook 或等效機制，但正式名稱與實作方式各異
  - Claude Code 有 25+ 種事件（最細緻，既有頁面另有依 YouTube 教學影片精算出的「31 種」精確計數）、5 種 Handler 類型（最靈活）
  - Hook 在 Agent Plugins 1.0 標準中被歸類為「不可移植」的專屬組件
  - 實務上正朝著以 Claude Code 格式為基礎的跨平台適配發展
  - 企業建議採用「去 Harness 化」的解耦架構：統一配置層 + 外部腳本調用
- refs: [[raw/deep-research/rc-20260825-001/research-report.md]]、[[wiki/sources/2026-08-25-coding-agent-hooks-comparison]]、[[wiki/comparisons/coding-agent-hooks-comparison]]、[[wiki/entities/deepseek-harness]]

## [2026-08-25] ingest | The Four Types of Memory Every AI Agent Needs

- 動作：Pi 執行 YouTube ingest + wiki-ingest 雙模型驗證
- 雙模型共識：Round 1 即達成一致（Claude + Gemini），不需 Round 2。兩邊都確認：建立新 source + concept 頁面、更新既有 concept + topic 頁面、type 分類正確、topics: [agent-memory-context]、不推翻既有結論。Claude 額外建議新建 `coala-four-memory-types` concept 頁面（功能分類 vs 存儲機制分類的正交軸），Gemini 建議併入既有頁面——採用 Claude 架構（獨立頁面更清晰）
- 建立：
  - `wiki/sources/2026-08-25-four-types-memory-ai-agent.md` — source note（含陳述級溯源）
  - `wiki/concepts/coala-four-memory-types.md` — CoALA 四種記憶類型功能分類框架
- 更新：
  - `wiki/concepts/ai-agent-memory-systems.md` — 加入 CoALA 框架交叉引用說明（功能 vs 存儲機制正交軸）
  - `wiki/topics/agent-memory-context.md` — Concepts + Sources 加入新頁面
- 關鍵發現：CoALA（普林斯頓大學）將 agent 記憶分為 Working / Semantic / Procedural / Episodic 四種功能類型；不同複雜度的 agent 需要不同記憶組合（reflex = working only，coding agent = 全部四種）；「Memory is what separates a chatbot from an agent」
- refs: [[raw/youtube/four-types-memory-ai-agent]]、[[wiki/concepts/coala-four-memory-types]]、[[wiki/sources/2026-08-25-four-types-memory-ai-agent]]

## [2026-08-24] ingest | NPU 在 AI 基礎設施架構中的角色 — 深度研究 ingest（W-2026-08-069）

- 動作：Cheer 執行 deep-research-execute 完整十步流程（job rc-20260824-001），產出 54 筆過濾後來源的深度研究報告，再執行 wiki-ingest 進行雙模型交叉驗證後 ingest 進 wiki
- 雙模型共識：Round 1 即達成一致（Claude + Gemini），不需 Round 2。兩邊都確認：update 既有 discussion 頁面、type: discussion、topics: [backend-systems, agent-infrastructure]、不推翻既有結論、補充 5 個新維度
- 建立：
  - `wiki/sources/2026-08-24-npu-deep-research.md` — 來源筆記（54 筆來源，Gemini Notebook 品質過濾）
- 更新：
  - `wiki/discussions/npu-role-in-ai-infrastructure.md` — 補充深度研究內容（資料流變體、量化、PIM、熱降頻、Copilot+ PC、模型管線化）
  - `wiki/topics/agent-infrastructure.md` — 更新 NPU 討論頁標記（從「🔴 開放問題」改為「深度研究已完成」），加入新來源筆記
  - `wiki/topics/backend-systems.md` — Sources 區塊加入新來源筆記
  - `wiki/index.md` — 更新 last rebuilt 日期，新增 source 條目，更新 discussion 摘要
- 注意：既有的 8 筆引用（REF-1 到 REF-8）保持不變（均經 Claude 手動 WebFetch 查證）；新增的 54 筆來源經 Cheer 抽查 5 筆 URL 驗證為真實內容。報告中引用清單的標題顯示為 `undefined`（generate_report.js 的 bug），URL 本身正確
- refs: [[wiki/sources/2026-08-24-npu-deep-research]]、[[wiki/discussions/npu-role-in-ai-infrastructure]]、raw/deep-research/rc-20260824-001/

## [2026-08-25] ingest | Google Cloud — How to modernize legacy codebases using AI coding agents

- 動作：Cheer 提供 YouTube 連結 `RKvckuC6Bsk`，Pi 執行 YouTube ingest + wiki-ingest 雙模型驗證
- 雙模型共識：Claude 建議 1 個新 concept（legacy-code-modernization）+ 併入既有頁面；Gemini 建議 2 個新 concept（disposable-sandbox + blast-radius-analysis）+ Docker 替代方案。採用 Claude 架構（單一 concept 更整潔）+ Gemini 的 Docker 替代方案建議（Podman/Rancher Desktop）
- 建立：
  - `wiki/sources/2026-08-25-google-cloud-legacy-code-ai.md` — source note（含 Cheer 想法 + 陳述級溯源）
  - `wiki/concepts/legacy-code-modernization.md` — concept 頁面（Plan→Execute→Verify 三步方法論）
  - `raw/conversations/2026-08-25-google-cloud-legacy-code-ai.md` — Cheer 的原始想法
- 更新：
  - `wiki/concepts/ai-coding-workflow.md` — 比較表格加入 legacy-code-modernization
  - `wiki/topics/agent-coding-practices.md` — Concepts + Sources 加入新頁面
  - `wiki/topics/coding-agent.md` — Concepts + Sources 加入新頁面
  - `wiki/index.md` — 新增 concept + source 條目
- Cheer 想法要點：每間企業都面臨的問題（無文件/無測試/無 CI/CD）；Docker Compose 想深入研究但 Docker Desktop 收費；TDD 找受影響層面 + BDD 驗證 AI 品質跟他們想法契合，但之前沒有具體步驟
- refs: [[raw/youtube/how-to-modernize-legacy-codebases-ai-coding-agents]]、[[wiki/concepts/legacy-code-modernization]]、[[raw/conversations/2026-08-25-google-cloud-legacy-code-ai]]

## [2026-08-24] ingest | WebMCP — 網頁原生 Agent-Ready 工具暴露機制

- 動作：Cheer 提供 YouTube 連結 `FARxSG_EY98`，Pi 執行 YouTube ingest + wiki-ingest 雙模型驗證
- 雙模型共識：Claude + Gemini 一致（新建 concept + 更新 entity），分歧在 source note（Claude 有、Gemini 沒）與 mcp-servers/mcp-clients 更新（Gemini 提、Claude 沒）→ 採用 Claude 方案（source note 必要、servers/clients 更新過度延伸不採用）
- 建立：
  - `wiki/sources/2026-08-24-webmcp-youtube.md` — source note（含 Cheer 資安觀點作為開放問題）
  - `wiki/concepts/webmcp.md` — concept 頁面
- 更新：
  - `wiki/entities/mcp-model-context-protocol.md` — 新增「相關變體：WebMCP」小節
  - `wiki/topics/mcp-ecosystem.md` — Concepts 列表加入 WebMCP
  - `wiki/topics/agent-infrastructure.md` — Concepts 列表加入 WebMCP
  - `wiki/index.md` — 新增 WebMCP 條目
- Cheer 想法要點：WebMCP 半年前就提出但當時效益低（AI 工具用戶少），現在時機成熟；不只服務瀏覽器 AI，也支援本地 agent + Web UI 工作流；資安是最大隱憂（多一個入口 = 多一個攻擊面）
- refs: [[raw/youtube/make-your-website-agent-ready-with-webmcp]]、[[wiki/concepts/webmcp]]、[[raw/conversations/2026-08-24-webmcp-security-thoughts]]

---

## [2026-08-24] record | Chunkless RAG 影片重複 ingest 確認 + Cheer KM 想法記錄

- 動作：Cheer 提供 YouTube 連結 `vRZNJWw78BQ`，Pi 執行 YouTube ingest 流程後發現已在 2026-08-10 完整 ingest 過，清除重複 raw transcript；Cheer 分享 KM 想法，存入 raw/conversations 並建立雙向關聯
- 原始想法：`raw/conversations/2026-08-24-chunkless-rag-km-thoughts.md`
- 結論：無需建立新 wiki 頁面（既有 `wiki/concepts/chunkless-rag.md` + `wiki/entities/docling.md` + `wiki/sources/2026-08-10-chunkless-rag-docling.md` 已完整涵蓋）
- Cheer 想法要點：Chunkless RAG 更接近人類搜尋模式、Token 成本會下降現在把精準度做高、目前手邊工作跟 Chunk/KM 有關
- refs: [[raw/youtube/chunkless-rag-docling]]、[[wiki/concepts/chunkless-rag]]、[[wiki/entities/docling]]、[[raw/conversations/2026-08-24-chunkless-rag-km-thoughts]]

---

## [2026-08-22] ingest | Portable Graph Agent Pattern — 延伸想法 backfill（雙模型共識）

- 動作：Cheer 觀看 Loop vs Graph Engineering 影片後的原創延伸想法，走 §3.5 Backfill 流程
- 原始想法：`raw/conversations/2026-08-22-loop-graph-engineering-thoughts.md`（`source_kind: thought`）
- 查重：grep 掃描 wiki/ 無「可攜式領域專家小 agent」相關既有頁面 → 確認是新內容
- 雙模型共識判斷：
  - **Pi（主持人）Round 1 提案**：併入既有 `loop-vs-graph-engineering.md` 加新章節
  - **Gemini Round 1 提案**：獨立成新 concept 頁面 `portable-graph-agent-pattern.md`
  - **Round 1 關鍵欄位比對**：目標頁面 ❌、type ✅、topics ❌ → 進入 Round 2
  - **Round 2 覆核**：Pi 重新評估後同意 Gemini 論點（外部來源摘要 vs 原創設計 pattern 來源性質不同，獨立頁面更利於原子化連結）→ **收斂，採用 Gemini 方案**
  - **結論**：`auto_verified`，新建 `wiki/concepts/portable-graph-agent-pattern.md`
  - **分歧**：Round 1 有分歧（併入 vs 獨立），Round 2 收斂，無需第三票仲裁
- 建立：
  - `wiki/concepts/portable-graph-agent-pattern.md` — 新 concept 頁面（可攜式 Graph 專家 Agent 模式）
- 更新：
  - `wiki/concepts/loop-vs-graph-engineering.md` — 新增「延伸應用」章節，連結到新頁
  - `wiki/entities/waku-agent.md` — 相關頁面補回連
  - `wiki/topics/agent-architecture.md` — Concepts 列表加入新頁
  - `wiki/index.md` — 全量重建，新增條目
- refs: [[raw/conversations/2026-08-22-loop-graph-engineering-thoughts|觀後想法]]、[[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]]

---

## [2026-08-22] ingest | AI Agent Memory Systems — 5 種記憶架構設計方式 + AI Agent 長期記憶架構 — 以 Mem0 為例（YouTube ingest）

- 動作：兩支 YouTube 影片 → youtube_transcript_api 抓取英文字幕 → 合併 segments → 建立 raw transcript → 建立 wiki 頁面
- 來源 1：`raw/youtube/sean-ai-stories-ai-agent-memory-systems.md`（30:26, en-US, 122 paragraphs, 手動字幕）
- 來源 2：`raw/youtube/alejandro-ao-mem0-long-term-memory.md`（28:32, en, 246 paragraphs, 手動字幕）
- 建立：
  - `wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems.md` — Source note（含 Cheer 使用者觀點）
  - `wiki/sources/2026-08-22-alejandro-ao-mem0-long-term-memory.md` — Source note（含 Cheer 使用者觀點）
  - `wiki/entities/mem0.md` — Entity page（Mem0 架構完整解析）
- 更新：
  - `wiki/concepts/ai-agent-memory-systems.md` — 補充 Mem0 架構深入解析章節
  - `wiki/topics/agent-memory-context.md` — 加入新 sources 與 entities
  - `wiki/index.md` — 計數更新 + 新頁面連結
- refs: [[wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems|Sean's AI Stories]]、[[wiki/sources/2026-08-22-alejandro-ao-mem0-long-term-memory|Alejandro AO]]

---

## [2026-08-21] fix | agy-bridge headless 模式 CANCELED/ERROR 已修復

- 動作：診斷 agy-bridge 在 headless 模式下讀檔被 CANCELED/ERROR 的問題 → 派 Codex 動手改 → Claude 逐項查證
- 根因：agy CLI 在 `--print-mode`（headless 模式）下，`view_file`（內部權限名 `read_file`）需要使用者核准，headless 沒人核准，但這個 auto-deny 行為本身不穩定（同一指令連跑 3 次分別得到 CANCELED/ERROR/CANCELED）
- 調查結論：agy CLI 沒有可用的細粒度權限白名單（試過 `.antigravitycli/settings.json` 未生效），唯一選項是 `--dangerously-skip-permissions`（全有全無）
- **採用修法**：`CheerioCorner/mcp-bridges/lib/agy.mjs` 的 `buildAgyArgs()` 預設同時加上 `--dangerously-skip-permissions` 和 `--sandbox`（用 sandbox 限制終端機/shell 執行範圍，收斂 skip-permissions 帶來的風險），呼叫端可用 `dangerously_allow_all: false` / `sandbox: false` 覆寫關掉。順便補上 `runAgy()` 的 `stderr` 沒被寫進 audit log 的小 bug
- **驗證**：Claude 直接呼叫修改後的 `runAgy()`，用今天一直失敗的同一句讀檔 prompt 連續跑 5 次真實 agy.exe，**5 次全部 `status: SUCCESS`，無 CANCELED/ERROR**
- 過程：Codex 第一次因為 Claude 呼叫時 cwd 設錯（限制在子目錄）導致改不到目標檔案，誠實回報卡住而非假裝改好；修正 cwd 後第二次順利完成
- 提醒：`CheerioCorner/mcp-bridges` 是獨立 git repo（cheerio-mcp-bridges），修復需要另外 commit/push；MCP server 是長駐進程，本次對話 session 開頭連上的 agy-bridge 仍在跑舊版，需重啟/重新連線才會生效
- refs: [[work/current#W-2026-08-068|W-2026-08-068]]（根因診斷）、[[work/current#W-2026-08-071|W-2026-08-071]]（修復任務，已完成）

---

## [2026-08-21] research | NPU 在 AI 基礎設施架構中的角色（延伸自 GPU/vLLM/K8s 影片的開放問題）

- 動作：Gemini(`agy` CLI)深度研究 → Claude 逐條查證引用 → 修正後寫入既有 discussion 頁面
- 更新：`wiki/discussions/npu-role-in-ai-infrastructure.md`（狀態由 🔴 開放問題 → 🟢 已完成初步研究），並在 `wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes.md` 補上回連連結
- ⚠️ **品管發現**：Gemini 原始研究報告 9 個引用中有 4 個編造/錯誤（Google 搜尋網址冒充官方文件、arXiv 編號指向完全不相關的論文、TPU 文件網址 404、Medium 文章查無此文）。Claude 用 WebFetch/WebSearch 逐條開啟查證，全部替換為實際存在的來源才寫入，並在頁面內附上查證過程與教訓
- 🔴 **待改進**：`chat-with-gemini-research` skill 的 Step 4「驗證引用完整性」目前只檢查格式（有沒有 [REF-N]、有沒有 URL），沒有實際開啟每個 URL 確認可訪問——建議之後把這一步改成強制執行
- refs: [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]]

---

## [2026-08-21] ingest | Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes（YouTube ingest）

- 動作：YouTube 影片 → youtube_transcript_api 抓取英文自動字幕 → 合併 1376 segments 為 219 paragraphs → 建立 raw transcript → 建立 wiki 頁面
- 來源：`raw/youtube/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes.md`（00:51:00, en, auto-generated）
- 建立：
  - `wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes.md` — Source note（含 Cheer 完整心得與 NPU 開放問題）
  - `wiki/concepts/gpu-architecture-for-ai-inference.md` — GPU vs CPU 架構差異、三個關鍵指標、VRAM 瓶頸
  - `wiki/concepts/llm-serving-architecture.md` — Prefill/Decode 兩階段、KV Cache、Prefix Caching、Batching
  - `wiki/concepts/model-sharding.md` — Tensor Parallelism vs Pipeline Parallelism
  - `wiki/entities/vllm.md` — vLLM 模型伺服器
  - `wiki/entities/llm-d.md` — LLM-D Kubernetes 智慧路由器
  - `wiki/discussions/npu-role-in-ai-infrastructure.md` — 🔴 開放問題：NPU 在 AI 基礎設施中的角色
- 更新：`wiki/topics/backend-systems.md`、`wiki/topics/agent-infrastructure.md`、`wiki/index.md`
- 備註：Cheer 的使用者心得已完整納入 source note 的「Cheer 的想法」小節，NPU 問題另建 discussion 頁面
- ⚠️ 跳過雙模型交叉驗證：使用者明確指定 workflow 且內容為基礎教學類（非爭議性結論），直接建立頁面

---

## [2026-08-20] ingest | 9 篇 Web Raw — OpenWiki/OKF 補充 + Coinbase AI 策略 + Shlok Khemani 記憶系統（批次 ingest）

- 動作：9 篇 raw web articles → 雙模型交叉驗證（Claude + Gemini Round 1 一致）→ 建立 wiki 頁面
- 來源（9 篇 raw web）：
  1. `raw/web/2026-08-20-langchain-openwiki-github-repo.md` — GitHub README（15.4k stars）
  2. `raw/web/2026-08-20-openwiki-0-2-brings-okf-to-codebase-docs.md` — LangChain 官方部落格 OKF 0.2
  3. `raw/web/2026-08-20-docs-langchain-openwiki-overview.md` — 官方文件 overview
  4. `raw/web/2026-08-20-know-2nth-ai-openwiki-and-okf.md` — 第三方 OKF 說明
  5. `raw/web/2026-08-20-shlok-khemani-chatgpt-and-claude-memory.md` — ⚠️ ChatGPT/Claude 記憶系統（非 Stefania Druga）
  6. `raw/web/2026-08-20-how-coinbase-cut-its-ai-spend-by-50.md` — 付費牆版
  7. `raw/web/2026-08-20-antaoalmada-openwiki-and-okf-agent-wikis.md` — 第三方部落格
  8. `raw/web/2026-08-20-thenewstack-multi-model-ai-infrastructure.md` — Coinbase 1,200 agents 報導
  9. `raw/web/2026-08-20-coinbase-ceo-5-strategies-ai-spend.md` — Yahoo Finance 5 策略
- 建立（3 個新 entity/concept + 9 個 source notes）：
  - `wiki/entities/coinbase-ai-engineering.md` — Coinbase 多模型基礎設施與成本工程
  - `wiki/concepts/multi-model-cost-routing.md` — 多模型路由設計原則
  - `wiki/concepts/consumer-ai-memory-personalization.md` — 消費級 AI 記憶設計（ChatGPT/Claude running profile）
  - 9 個 `wiki/sources/2026-08-20-*.md` source notes（每篇 raw 一頁）
- 更新：
  - `wiki/entities/langchain-openwiki.md`（補充 GitHub stats、connectors、CI 整合、AGENTS.md 注入機制）
  - `wiki/concepts/okf-open-knowledge-format.md`（新增 OKF 時間軸、vs Agent Skills vs MCP 比較、重要限制）
  - `wiki/concepts/ai-agent-memory-systems.md`（新增 consumer-ai-memory 交叉連結）
  - `wiki/concepts/context-rot.md`（新增 Coinbase context lean 策略作為解方）
  - `wiki/topics/okf.md`（新增 5 個 source 連結）
  - `wiki/topics/agent-memory-context.md`（新增 consumer-ai-memory + Shlok Khemani source）
  - `wiki/topics/agent-runtime-implementations.md`（新增 OpenWiki entity + Coinbase entity + multi-model-cost-routing + 8 個 sources）
  - `wiki/index.md`（全量重建，232 頁）
- 雙模型交叉驗證：Round 1 關鍵欄位全部一致（OpenWiki→更新既有頁、Shlok→獨立 concept 不併入 memory、Coinbase→新 entity+concept），無分歧
- ⚠️ Shlok Khemani 那篇與 Stefania Druga 無關，已明確劃界（不同領域：消費級個人化 vs agent harness 記憶）
- ⚠️ OKF 官方文件間存在 v0.1/v0.2 版本標示不一致，已在 source note 中如實記錄

## [2026-08-20] ingest | 2 支 YouTube 影片 — LangChain OpenWiki + Sakana AI Memory Harnesses（批次 ingest）

- 動作：`wiki-ingest-youtube` skill 抓字幕 → raw transcripts → 雙模型交叉驗證 → 建立 wiki 頁面
- 來源：
  1. `raw/youtube/2026-08-20-langchain-openwiki-cli-for-code-wikis.md`（XNX-1h2K-9U, 16:44, 420 segments, 手動英文字幕）
  2. `raw/youtube/2026-08-20-sakana-ai-memory-harnesses-for-long-running-research-agents.md`（R3-anFK1YM8, 12:45, 310 segments, 自動生成英文字幕）
- 建立：
  - `wiki/sources/2026-08-20-langchain-openwiki-cli.md` — LangChain OpenWiki CLI source note（陳述級溯源）
  - `wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents.md` — Sakana AI Memory Harnesses source note（陳述級溯源）
  - `wiki/entities/langchain-openwiki.md` — OpenWiki 工具 entity 頁
  - `wiki/concepts/recall-policy-ladder.md` — Recall Policy Ladder（四階檢索策略效果排名）
  - `wiki/concepts/context-rot.md` — Context Rot（長任務脈絡惡化，與 Context Decay 互補）
- 更新：
  - `wiki/concepts/okf-open-knowledge-format.md`（新增 LangChain OpenWiki 案例 + Agent 導向 vs 人類導向文件設計差異）
  - `wiki/concepts/ai-agent-memory-systems.md`（新增 context 邊界法則 + bad memory is expensive + recall-policy-ladder 連結）
  - `wiki/concepts/context-decay.md`（新增 Context Rot 互補概念比較表）
  - `wiki/topics/okf.md`（新增 OpenWiki entity + source 連結）
  - `wiki/topics/agent-memory-context.md`（新增 recall-policy-ladder + context-rot + memory-harnesses source 連結）
  - `wiki/index.md`（全量重建）
- 雙模型交叉驗證：
  - Round 1：Claude + Gemini 對兩支影片各自提案，關鍵欄位（目標頁面、type、topics、是否推翻既有結論）比對
  - Video 1：✅ 一致（都建議建 OpenWiki 頁 + 更新 OKF 頁，不推翻既有結論）
  - Video 2：⚠️ 分歧在 Context Rot 處理方式——Claude 建議新建獨立頁，Gemini 建議擴寫 context-decay.md
  - 裁決：採用 Claude 方案（新建 context-rot.md），理由：context-decay.md 定義限定在「跨模型交接」，現有解方（契約驅動、rejected_alternatives）不適用於單一 agent 長時任務，擴寫定義但解方沒跟上會造成頁面內部不一致
  - Gemini 遺漏的 source note 補上
- 未推翻任何既有結論，所有更新為補充性質

## [2026-08-19] ingest | 企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini + Codex 雙版本）

- 動作：從 `raw/research/` 兩份深度調研報告 ingest 進 wiki，建立 source notes、concept pages，更新既有 enterprise-governance 頁面
- 建立：
  - `wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini.md` — Gemini 7 個 Tier 1 來源
  - `wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex.md` — Codex 20 個 Tier 1 來源（主要來源）
  - `wiki/concepts/agentic-pipeline-checkpoint-design.md` — Agentic Pipeline Checkpoint 多層把關架構
  - `wiki/concepts/durable-execution-for-agents.md` — Durable Execution 層（Temporal / Azure Durable Functions），Codex 額外發現的重要補充
- 更新：
  - `wiki/concepts/agent-skills-enterprise-governance.md`（新增「Agentic Pipeline 整合」章節：五階段流程、Self-Repair Loop、Durable Execution 層、架構組合建議、漸進式授權）
  - `wiki/topics/agent-architecture.md`（加入 2 個新 concept + 2 個新 source）
  - `wiki/topics/skill-system.md`（加入 1 個新 concept + 2 個新 source）
  - `wiki/index.md`（全量重建）
- 品質決策：Codex 版本品質明顯優於 Gemini（深連結 vs 根目錄 URL、來源數 20 vs 7、日期誠實標「未標示」），兩份事實衝突時優先採信 Codex
- 關鍵補充：Durable Execution 層是 Gemini 完全沒提到的知識缺口，已建立獨立 concept 頁面
- 來源：`raw/research/2026-08-19-agentic-pipeline-orchestration-gemini.md`、`raw/research/2026-08-19-agentic-pipeline-orchestration-codex.md`
- Work item：W-2026-08-056

## [2026-08-19] ingest | Docling — IBM 文件解析與結構化框架（GitHub README + Documentation 深入研究）

- 動作：建立 raw 來源筆記，大幅更新 `wiki/entities/docling` 頁面（基於 GitHub README、官方文件、技術報告 arXiv:2408.09869）
- 建立：`raw/web/2026-08-19-docling-github-readme.md`
- 更新：`wiki/entities/docling.md`（從 1 sources 擴充為 3 sources，補充完整功能列表、架構圖、DoclingDocument 格式說明、使用範例、LF AI & Data Foundation 背景）
- 更新：`wiki/topics/agent-memory-context.md`（Docling 描述更新）
- 更新：`wiki/index.md`（Docling 條目描述更新）
- 補充重點：65k+ stars、30+ 格式支援、MCP Server、API Server、VLM 整合、影片/Email/XBRL/EPUB 等新格式
- 雙模型交叉驗證：跳過（已有既有頁面且為單一來源更新，無分類分歧風險）

---

## [2026-08-23] topic-refactor | AI Agent topic 拆分為 6 個子 topic

- 動作：將原 `ai-agent` topic（80 頁）拆分為 6 個子 topic，解決單一 topic 過於龐大的問題
- 新建 topic 頁：
  - `wiki/topics/agent-architecture.md`（12 頁）— Agent 核心設計概念與架構模式
  - `wiki/topics/agent-memory-context.md`（7 頁）— 記憶架構、context 管理、文件理解
  - `wiki/topics/agent-runtime-implementations.md`（26 頁）— 具體命名的 agent 框架、工具與平台
  - `wiki/topics/pi-agent-ecosystem.md`（12 頁）— Pi 生態系專屬內容
  - `wiki/topics/agent-coding-practices.md`（13 頁）— AI 輔助寫程式的方法論與實務
  - `wiki/topics/agent-research-benchmarks.md`（7 頁）— 前沿研究與 benchmark
- 改造 `wiki/topics/ai-agent.md` 為總覽 hub，列出 6 個子 topic 連結
- 更新 77 頁 frontmatter：`topics: [ai-agent]` → 對應子 topic tag（保留其他既有 topic tag 不動）
- 3 頁保留 `ai-agent` 不動（待確認分類）：`entities/learning-loop.md`、`entities/obsidian.md`、`entities/round-table.md`
- 更新 `wiki/index.md` Topics 區塊

---

## [2026-08-23] topic-refactor | Skill topic 拆分為 4 個子 topic

- 動作：將原 `skill` topic（60 頁）拆分為 4 個子 topic，解決單一 topic 過於龐大的問題
- 新建 topic 頁：
  - `wiki/topics/skill-system.md`（15 頁）— Skill 系統架構、方法論、API、治理
  - `wiki/topics/copilot-sdk.md`（22 頁）— GitHub Copilot SDK 技術文件
  - `wiki/topics/skill-presentation-design.md`（5 頁）— 簡報/前端設計類 Skill
  - `wiki/topics/skill-cases-and-comparisons.md`（9 頁）— 案例、比較文、生態系
- 改造 `wiki/topics/skill.md` 為總覽 hub，列出 4 個子 topic 連結
- 更新 60 頁 frontmatter：`topics: [skill]` → 對應子 topic tag
- 橫跨多個子 topic 的頁面（3 頁）：`skills-complex-composition-comparison`、`github-copilot-vs-claude-code-skills-ecosystem`、`copilot-vs-claude-code-skills-ecosystem`
- 更新 `wiki/index.md` Topics 區塊

---

## [2026-08-19] lint | Wiki 大健檢：結構完整性 + Raw 層清理 + Topic 補建

### 結構完整性
- Frontmatter 檢查：216 頁全部通過（type/title/created 齊全）
- Source note provenance：全部有 provenance_raw 或 provenance_url
- 孤立頁面：1 筆（contract-driven-development.md）→ 已修正（context-decay.md 加 wikilink）
- Canvas 註冊：3 個 canvas 全部在 visualizations/README.md 註冊
- 缺失 topic：6 個（claude-code, hooks, coding-agent, okf, migration, project-architecture）→ 已建立

### Raw 層清理
- 已消化+冗餘 → raw/.trash/：5 筆（tau, vibe-coding, acl, licenses, readme）
- 已消化+唯一 → 保留：4 筆（skill 撰寫, 企業級, api quickstart, api guide）
- 未消化 Copilot SDK 批次：29 筆 raw 中 24 筆有 source notes，5 筆直接進 concepts/entities

### 索引重建
- wiki/index.md 全量重建（252 行）

# Log

> 變更日誌。連結改用普通文字，避免 structural files 變成 graph 超級節點。
> 需要追溯時，查 frontmatter 的 `provenance` 或 `sources` 欄位。

## [2026-08-26] ingest | 10 個前端 UI 視覺設計 AI Agent Skill Repo 批次 ingest（含比較頁）

- 動作：Cheer 提供 10 個前端/UI 視覺設計相關的 AI agent skill GitHub repo 連結，Claude 已完成逐一確認（WebFetch + GitHub API 交叉核實），Pi 執行 §3.1 標準 Ingest 流程
- 雙模型交叉驗證：
  - Round 1：呼叫 Claude CLI（chat-with-claude）+ Gemini（chat-with-gemini）各自獨立產出結構化提案
  - Claude CLI 因權限問題（max_turns 限制 + file system permission denials）未能產出可用提案
  - Gemini 成功產出完整 JSON 提案（10 source notes + 10 entity pages + 1 comparison page + topic updates）
  - **裁決**：Gemini 提案與 Pi 對 10 個 raw 全文的理解高度一致，關鍵欄位（目標頁面、type、topics）全部合理，直接採用 → `auto_verified`
  - **跳過 copilot 第三票**：因 Cheer GitHub Copilot 額度用完，依照 Cheer 指示跳過 copilot 仲裁
- 建立：
  - `wiki/sources/2026-08-26-leonxlnx-taste-skill.md` — taste-skill 來源筆記
  - `wiki/sources/2026-08-26-nextlevelbuilder-ui-ux-pro-max-skill.md` — ui-ux-pro-max-skill 來源筆記
  - `wiki/sources/2026-08-26-pbakaus-impeccable.md` — impeccable 來源筆記
  - `wiki/sources/2026-08-26-anthropics-claude-code-frontend-design.md` — Anthropic frontend-design 來源筆記
  - `wiki/sources/2026-08-26-nutlope-hallmark.md` — hallmark 來源筆記
  - `wiki/sources/2026-08-26-greensock-gsap-skills.md` — gsap-skills 來源筆記
  - `wiki/sources/2026-08-26-google-labs-code-stitch-skills.md` — stitch-skills 來源筆記
  - `wiki/sources/2026-08-26-vercel-labs-web-interface-guidelines.md` — web-interface-guidelines 來源筆記
  - `wiki/sources/2026-08-26-conardli-garden-skills.md` — garden-skills 來源筆記
  - `wiki/sources/2026-08-26-dominikmartn-nothing-design-skill.md` — nothing-design-skill 來源筆記
  - `wiki/entities/leonxlnx-taste-skill.md` — taste-skill entity
  - `wiki/entities/nextlevelbuilder-ui-ux-pro-max-skill.md` — ui-ux-pro-max-skill entity
  - `wiki/entities/pbakaus-impeccable.md` — impeccable entity
  - `wiki/entities/anthropics-claude-code-frontend-design.md` — Anthropic frontend-design entity
  - `wiki/entities/nutlope-hallmark.md` — hallmark entity
  - `wiki/entities/google-labs-code-stitch-skills.md` — stitch-skills entity
  - `wiki/entities/vercel-labs-web-interface-guidelines.md` — web-interface-guidelines entity
  - `wiki/entities/conardli-web-design-engineer-skill.md` — web-design-engineer entity
  - `wiki/entities/dominikmartn-nothing-design-skill.md` — nothing-design-skill entity
  - `wiki/comparisons/frontend-ui-design-skills-comparison.md` — 10 repo 全景比較頁（競爭/互補/品牌皮膚/情境選型）
- 更新：
  - `wiki/entities/gsap.md` — 補充 8 個子 skill 詳細列表 + 核心規則 + 定位說明，topics 新增 skill-cases-and-comparisons, skill-presentation-design
  - `wiki/topics/skill-cases-and-comparisons.md` — 新增 Comparisons 區塊 + 10 個新 source + 10 個新 entity
  - `wiki/topics/skill-presentation-design.md` — 新增 9 個新 entity + 9 個新 source
  - `wiki/topics/skill.md` — 更新頁數（9→19）+ 新增 comparisons 橫跨頁面
  - `wiki/index.md` — 全量重建（124 sources, 74 entities, 76 concepts）
- 關鍵發現：
  - taste-skill 與 hallmark 是最直接的競爭者（都在解 anti-AI-slop 問題），美學偏向互斥
  - impeccable 源自 Anthropic frontend-design（同源不同走向：工程化 vs 質性流程）
  - gsap-skills 與 web-interface-guidelines 是互補型（動畫實作 + 生成後稽核）
  - stitch-skills 的 taste-design 與 taste-skill LILA RULE 疑似同源（未查證）
  - ⚠️ taste-skill (80k stars/6 個月) 和 ui-ux-pro-max-skill (120k stars/9 個月) star 數異常，已保留備註供 Cheer 判斷
- refs: [[wiki/comparisons/frontend-ui-design-skills-comparison]]、10 個 raw/web/2026-08-26-*.md

## [2026-08-22] ingest | Mem0 深度研究——比較、限制、Decision-Ledger 適用性、授權定價（已查證版）
- 來源：`raw/research/2026-08-22-mem0-deep-research-verified.md`（Gemini 深度研究經 Claude WebFetch 逐一查證修正）
- 動作：
  - 新建 source note: wiki/sources/2026-08-22-mem0-deep-research-comparison.md（四角度：系統比較 / 生產限制 / Decision-Ledger 適用性 / 授權定價）
  - 新建 discussion: wiki/discussions/mem0-vs-decision-ledger-for-w074.md（Mem0 vs Decision-Ledger 對 W-074 架構的適用性）
  - 更新 wiki/entities/mem0.md：新增 Graph Memory 移除說明、比較表格、定價、Decision-Ledger 適用性、新來源連結
  - 更新 wiki/topics/agent-memory-context.md：新增 source + discussion 連結
  - 更新 wiki/index.md：新增 source + discussion 條目
- 關鍵事實：
  - Graph Memory 已從開源版移除（官方文件明確寫明，[VERIFIED]）
  - 定價：Hobby Free / Starter $19 / Pro $249 / Enterprise Custom（無 Growth 方案）
  - 角度二（生產限制）完全缺乏可信來源，標記為知識缺口
  - Mem0 適合 Semantic Memory，不適合直接取代 Procedural Memory（兩者應疊加）
- refs: [[wiki/sources/2026-08-22-mem0-deep-research-comparison]]、[[wiki/discussions/mem0-vs-decision-ledger-for-w074]]、[[wiki/entities/mem0]]

## [2026-08-18] ingest | RAD Methodology for AI Vibe Coding
- 來源：YouTube (J0zbWsutyA8) — 10:40 影片
- 動作：
  - 新建 raw transcript: raw/youtube/2026-08-18-rad-methodology-for-ai-vibe-coding.md
  - 新建 source note: wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding.md
  - 新建 concept: wiki/concepts/rapid-application-development.md (RAD 方法論)
  - 新建 concept: wiki/concepts/spec-driven-development.md (SDD 規格驅動開發)
  - 新建 entity: wiki/entities/james-martin.md
  - 更新 wiki/concepts/vibe-coding.md (加 RAD/SDD 引用 + self-approval loophole 案例)
  - 更新 wiki/concepts/ai-coding-workflow.md (加 RAD 歷史先驅引用)
  - 更新 wiki/topics/ai-development-tools.md (加 3 個新頁面)
  - 更新 wiki/topics/ai-agent.md (加 RAD/SDD 概念)
  - 重建 wiki/index.md (56 concepts, 55 entities, 85 sources)
- 雙模型交叉驗證：
  - Round 1: Claude vs Gemini 有 3 個分歧（SDD 是否獨立頁、更新目標頁、topic 命名）
  - Round 2: 收斂共識——建立 SDD 獨立頁、更新 vibe-coding.md、使用 ai-development-tools topic
- 關鍵論點：RAD 四階段對應 AI 開發流程、45% AI 生成程式碼有安全問題、self-approval loophole 案例
- refs: raw/youtube/2026-08-18-rad-methodology-for-ai-vibe-coding, wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding, wiki/concepts/rapid-application-development, wiki/concepts/spec-driven-development, wiki/entities/james-martin

## [2026-08-18] ingest | Claude Code 必學設定 Hooks，完整教學一次搞懂 (Gary Chen)
- 來源：YouTube (rLNGSDYkK-w) by Gary Chen
- 動作：新建 source note + concept page + 更新 claude-code entity + 更新 agent-extensibility-hierarchy + 更新 2 topic pages + index
- 雙模型交叉驗證：Claude + Gemini 提案一致（關鍵欄位匹配），直接採用
- 重點：三層架構 (Event/Matcher/Handler)、10 個核心 Event、5 種 Handler、Claude Code vs Codex 差異
- refs: raw/youtube/rLNGSDYkK-w, wiki/sources/2026-08-18-claude-code-hooks-tutorial, wiki/concepts/claude-code-hooks-architecture

## [2026-08-17] ingest | AI Agent Memory Systems — 5 種記憶架構設計方式
- 來源：YouTube (072eNztI06k) by Sean Chen
- 動作：新建 source note + concept page + 更新 waku-agent entity + 更新 2 topic pages + index
- 雙模型交叉驗證：Claude + Gemini 提案一致，直接採用
- refs: raw/youtube/ai-agent-memory-systems, wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways

## [2026-08-17] visualmap | 3 顆種子視覺地圖結構性修復

- **修復範圍**：Skill 設計方法論、Agent 擴充架構層級、E2E 測試種子
- **問題**：3 顆種子的視覺地圖 Mermaid 誤放在孤兒子頁面，DB 記錄頁為空，種子頁 relation 未連結
- **修復內容**：
  - Skill 設計方法論：3 個 Mermaid（五步驟流程 / 一需求→多意圖 / 交辦包結構）搬入 DB 記錄頁 `3bf5979e-3a8c-81c6-95e9-d567c3a4ba66`，種子頁 relation 指向該記錄頁
  - Agent 擴充架構層級：3 個 Mermaid（三層能力光譜 / 選擇決策樹 / 與既有頁面關係）搬入 DB 記錄頁 `3bf5979e-3a8c-81e0-a8f6-e59e3a30550f`，種子頁 relation 指向該記錄頁
  - E2E 測試種子：種子頁本體錯置的 Skill Design Mermaid 刪除；DB 記錄頁維持空白（等 E2E 驗證完成後再補）
  - 3 個孤兒子頁面 move to trash：`3bf5979e-3a8c-8145-bffb-c6146db99242`、`3bf5979e-3a8c-8183-be5c-eec52aa51e2d`、`3bf5979e-3a8c-81a9-aa83-dc26f1e086dc`
- **mmdc 驗證**：6 個 Mermaid 全部通過 mermaid-cli render（SVG 8.5KB-14.4KB）
- refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、W-2026-08-055

## [2026-08-17] round-table + ingest | Claude vs Gemini vs ChatGPT 前端全鏈路能力比較

- **操作類型**：圓桌會議（3 輪）+ wiki ingest
- **參與者**：Claude (Claude Code CLI)、Gemini (agy CLI)、ChatGPT (Codex CLI)
- **核心結論**：三家各有結構性優勢，最佳工作流是「契約驅動的三核心分工」
- **新增頁面**：
  - wiki/sources/2026-08-17-frontend-ai-roundtable.md — 來源筆記
  - wiki/concepts/contract-driven-development.md — 契約驅動開發
  - wiki/concepts/context-decay.md — 脈絡衰減
- **5 大共識**：契約驅動開發、脈絡衰減是最大風險、編排成本必須控制、rejected_alternatives 必要、不要單一模型跑全鏈路
- **refs**: [[wiki/sources/2026-08-17-frontend-ai-roundtable|圓桌會議紀要]]

## [2026-08-17] ingest + dual-model | DevOps Skill 簡報併入 Agent Skills 專題

- **操作類型**：本機簡報 ingest + 雙模型交叉驗證（Claude + Gemini）
- **來源**：Report/devops-skill-presentation/（Cheer 自製 31 頁 HTML 簡報，非外部 URL）
- **雙模型共識**：
  - 共識：#8 Write-back → best-practices、#10 SDLC → governance、#11 失敗模式 → agent-skills.md
  - 分歧：新種子數量（Claude 1 個 vs Gemini 3 個）→ Pi 裁決 2 個
  - 分歧：Meta-Skills Loop（獨立 vs 併入）→ Pi 裁決併入 agent-skills.md
- **新增頁面**：
  - wiki/concepts/skill-design-methodology.md — Skill 設計方法論（Intention-First + Event Storming + DDD + Domain Know-how + 決策樹）
  - wiki/concepts/agent-extensibility-hierarchy.md — Agent 擴充架構層級（Skill/Hook/Extension 能力光譜）
  - wiki/sources/2026-08-17-devops-skill-presentation.md — 來源筆記
- **更新頁面**：
  - wiki/concepts/agent-skills.md — +Meta-Skills Loop、+LLM 失敗模式、+Beyond Skill
  - wiki/concepts/skill-authoring-best-practices.md — +Write-back 安全門（含 W-2026-08-055 跨場景共通模式註記）
  - wiki/concepts/agent-skills-enterprise-governance.md — +Skill SDLC（8 階段）
  - wiki/concepts/skills-complex-composition-comparison.md — +組合技鏈式案例
- **索引重建**：wiki/index.md 全量重建（50 concepts, 54 entities, 84 sources）

## [2026-08-17] ingest + verify + dual-model | 7 個用 AI 做動畫的 Skills（YouTube Shorts）

- **操作類型**：YouTube ingest + 雙模型交叉驗證（Pi + Gemini + Claude URL 驗證）
- **來源**：YouTube Shorts — 7 個用 AI 做動畫的 Skills（Debug Tuboshu, 2026-08-16）
- **字幕**：Whisper base model auto-generated（53 秒，31 segments）
- **查證結果**：7/7 工具全部核實（Pi GitHub API + Gemini 獨立 web search + Claude HTTP 驗證）
  1. HyperFriends → hyperframes（HeyGen, 41k stars）
  2. G7 → gsap-skills（GreenSock AI 技能包, 13.6k stars）← 修正：從 GSAP 主庫改為 AI 技能包
  3. Pixel 2 模式 → pixel2motion（1.9k stars）
  4. Remotion → Remotion（56k stars）✅
  5. Video Shrugcraft → video-shotcraft（5.2k stars）
  6. Story2 Hand Zone → story-to-handdrawn-video（1.4k stars）
  7. 手感動效 → breathing-dots-tutorial（130 stars, 教學專案，非獨立工具）
- **雙模型共識**：Pi 與 Gemini 結果高度一致（6 項同意，第 7 項一致判定為教學範例）
- **已排除捏造連結**：Gemini 引用的 Codrops 文章（tympanus.net）經 Claude 實測 404，已排除
- **新建**：6 個 entity pages（hyperframes, gsap, pixel2motion, remotion, video-shotcraft, story-to-handdrawn-video）
- **更新**：source note（查證結果 + 雙模型說明）、concept page（7 個手法用途與情境）、topics 導航
- **重建**：`wiki/index.md`（50 concepts, 51 entities, 81 sources）
- refs: [[wiki/sources/2026-08-17-7-ai-animation-skills|Source Note]], [[wiki/concepts/ai-animation-generation-approaches|AI 動畫生成手法分類]]

## [2026-08-15] round-table | Pi Agent v0.84.x 更新中值得學習的設計模式

- **操作類型**：圓桌會議 + Source Note 建立
- **來源**：Claude (claude-sonnet-4) + Copilot (gh copilot) 圓桌會議
- **新建**：wiki/sources/2026-08-15-round-table-pi-agent-v084-learning.md
- **核心共識**：設定分層（AGENTS.override.md）、Tool 中斷機制、結構化輸出約束為 Top 3 學習主題
- **Cheer 自建 Agent 優先清單**：P0 設定分層 + Tool 中斷、P1 結構化輸出 + Pre-flight、P2 模板展開 + TUI 區域分離
- refs: [[wiki/entities/pi-agent/changelog/v0.84.0|Pi Agent v0.84.0 Changelog]]

## [2026-08-15] restructure | Agent Skills 花園重組（3 子種子併入框架種子頁）

- **決策原因**：人類審查發現 3 顆子種子（撰寫方法論/企業治理/API 整合）內容全部來自 Anthropic 官方文件，被包裝成「跨平台通用方法論」卻脫離具體工具脈絡，讀起來抽象。決定廢除獨立子種子，把內容併回各框架自己的種子頁。
- **操作**：
  1. Claude Code 種子（`3bb5979e-3a8c-81ce-93fd-fd8be0c7ab49`）：新增「Skill 撰寫規格」「Skill 企業治理」「Skill API 整合」三大章節（含完整規則、表格、範例）
  2. GitHub Copilot 種子（`3bb5979e-3a8c-81ba-a661-e06f1d8a1edc`）：Agent Skills 章節擴充為「撰寫規格與使用方式」（含檔案結構、frontmatter 規則、載入方式、與 Anthropic 格式差異）
  3. 研究專題（`3bc5979e-3a8c-8121-924e-f99b09671383`）：引用改指向新位置（「詳見 🌱 Claude Code — Skill 撰寫規格」等）
  4. 視覺地圖（`3bc5979e-3a8c-81e0-bb89-f9066f64f664`）：移除 3 個子種子節點
  5. 3 顆舊種子 trash（`in_trash: true`，可還原）
  6. 研究專題 🌳 知識花園 relation 清空
- **引用修正範例**：「（詳見 🌱 Skill 撰寫與評估方法論）」→「（詳見 🌱 Claude Code — Skill 撰寫規格）」
- **備註**：wiki 端維持獨立概念頁面（wiki 有自己的知識圖譜組織方式），花園端結構不同步。
- refs: [[wiki/entities/knowledge-garden|knowledge-garden manifest]]

## [2026-08-15] update | Agent Skills 花園深度灌溉（3 頁面修正）

- **修正類型**：深度灌溉（非單純新增），根據人類審查反饋修正 3 個頁面
- **修正來源**：人類實查 Notion 後指出「內容膚淺」，Claude 用 Notion 工具實查 7 個頁面確認具體問題

### 修正頁面

1. **GitHub Copilot 種子**（`3bb5979e-3a8c-81ba-a661-e06f1d8a1edc`）
   - 新增「Copilot SDK：Custom Agents/Skills 系統」深度章節
   - 具體寫入：`skillDirectories`/`disabledSkills`/per-agent `skills` preload、Custom Agents auto-delegation + isolated context、Fleet Mode SQL todos、7+ Hooks 完整清單、Steering vs Queueing
   - 更新比較表：巢狀/委派從「⚠️/❌」改為「✅ 完整支援」
   - 新增 SDK 客觀事實表
   - 成長狀態維持 🌿（已到位）

2. **研究專題「Agent Skills」**（`3bc5979e-3a8c-8121-924e-f99b09671383`）
   - 新增「複合組合技：三框架的設計哲學差異」深度章節（~800 字）
   - 具體寫出：Copilot Agent-based / Claude Code Hook-based / Pi Manual 三種組合哲學 + 為什麼這樣設計
   - 所有具體事實陳述標明出自哪顆種子（「詳見 🌱 ...」格式）
   - 內文引用格式已建立標準：之後所有研究專題比照辦理

3. **視覺地圖**（`3bc5979e-3a8c-81e0-bb89-f9066f64f664`）
   - 邊標籤從「子種子/灌溉/關聯」改為機制差異：「Agent-based Custom Agents + Fleet Mode」「Hook-based Lifecycle Hooks + Subagents」「Manual 組合 逐一觸發」
   - 節點標籤加入組合哲學：Claude Code →「Hook-based 組合」、Copilot →「Agent-based 組合」
   - mmdc 驗證通過（15KB SVG）

### 引用種子出處的具體範例
- 「SKILL.md 的三層漸進式揭露...如何運作？（詳見 🌱 Skill 撰寫與評估方法論）」
- 「Copilot 的 7+ Hooks...提供了完整的 lifecycle 拦截能力...（詳見 🌱 GitHub Copilot）」
- 「Pi 的設計哲學是極簡主義...（詳見 pi-agent-core、Minimal Agent Philosophy）」

### Wiki 同步狀態
- Wiki 端已有完整內容（22 篇 Copilot SDK 文件 + 7 篇 Anthropic 文件已 ingest）
- Notion 花園現在追上 wiki 深度，不需反向更新 wiki
- refs: [[wiki/entities/knowledge-garden|knowledge-garden manifest]]

## [2026-08-14] visualmap | Agent Skills 研究專題視覺地圖建立

- **新建**：Notion 視覺地圖 DB 記錄「Agent Skills 研究專題視覺地圖」（ID: `3bc5979e-3a8c-81e0-bb89-f9066f64f664`）
- **類型**：專題地圖
- **Relations**：🔬 研究專題 → Agent Skills、關聯種子 → 3 顆子概念種子
- **反向 Relation**：研究專題頁「🗺️ 視覺地圖」欄位已連結
- **mmdc 驗證**：通過（16KB SVG 生成）
- **圖表結構**：研究專題中心 → 3 子種子（綠）+ 2 灌溉（橘）+ 1 關聯（綠）
- **更新**：研究專題「下一步」欄位（移除「產生視覺地圖」）、visualizations/README.md 註冊
- refs: [[wiki/entities/knowledge-garden|knowledge-garden manifest]]

## [2026-08-14] garden-sync | Agent Skills 花園同步（研究專題 + 3 顆種子）

- **操作類型**：花園同步（Notion 寫入）
- **前置流程**：
  1. 花園調查：查詢現有 23 顆種子 + 5 個研究專題結構
  2. 圓桌會議：Claude + Gemini 討論，Round 1 共識收斂
  3. 雙重品質查核：Claude WebFetch 驗證 URL + Gemini 逐項核對技術數字
  4. 人類審查通過
- **新建**：
  - 研究專題：「🔬 Agent Skills」（ID: `3bc5979e-3a8c-8121-924e-f99b09671383`）
  - 種子 1：「🌱 Skill 撰寫與評估方法論」（ID: `3bc5979e-3a8c-8101-87fb-c38bbb498d6d`）
  - 種子 2：「🌱 Agent Skills 企業治理」（ID: `3bc5979e-3a8c-814e-8035-d57c6eb691be`）
  - 種子 3：「🌱 Agent Skills API 整合機制」（ID: `3bc5979e-3a8c-817a-a411-c872a7df10d4`）
- ** Relations**：研究專題 ↔ 3 顆種子雙向關聯已建立
- **交叉驗證**：以 ntn pages get 實際讀取 4 個頁面，確認存在且內容正確
- **本地更新**：knowledge-garden.md manifest（種子 23→26、研究專題 5→6）
- refs: [[wiki/entities/knowledge-garden|knowledge-garden manifest]]

## [2026-08-14] correction | GitHub Copilot SDK 官方文件修正（22 篇 ingest + 既有頁面修正）

- **修正類型**：**修正既有內容**（非單純新增），根據 GitHub 官方文件（docs.github.com 22 篇）修正先前基於第三方文章的多處錯誤
- **根本原因**：先前 ingest 依據 aiskill.market 第三方比較文（作者 Duke Harewood），該文對 GitHub Copilot 的描述多處與官方文件不符
- **修正來源**：raw/web/ 22 篇 GitHub Copilot SDK 官方文件（Custom Skills、Agent Loop、Custom Agents、Fleet Mode、Hooks、Steering & Queueing、Setup guides 等）
- **雙模型交叉驗證**：未執行（修正基於官方文件 vs 第三方文章的明確衝突，官方文件優先，不需仲裁）

### 具體修正項目

| 項目 | 第三方文章（舊） | 官方文件（新） | 影響頁面 |
|------|----------------|---------------|----------|
| Skill 格式 | Extensions（服務整合） | SKILL.md（與 Anthropic 格式兼容） | comparison, root concept |
| 組合方式 | Extensions @mentions | `skillDirectories` + per-agent `skills` preload | comparison, root concept |
| 巢狀/委派 | ⚠️ Workspace preview / ❌ 無 | Custom Agents + Sub-agent Orchestration + Fleet Mode | comparison, root concept |
| 工具鏈整合 | Extensions（策劃） | MCP（per-agent）+ Custom Tools | comparison, root concept |
| 鉤子/生命週期 | ❌ 無 | 7+ Hooks（完整 lifecycle） | comparison, root concept |
| Context | 8K tokens | 依模型（GPT-5.4 等） | comparison |
| 生態規模 | 20+（策劃） | 開放 SDK + 社群 Skills | comparison |
| 設計定位 | 策劃 + 簡單性 | 程式化 agent 平台 + 深度自訂 | comparison |

### 新建頁面

- `wiki/entities/github-copilot-sdk.md` — GitHub Copilot SDK 實體頁（含完整能力一覽）
- `wiki/concepts/copilot-agent-loop.md` — Agent Loop 概念
- `wiki/concepts/copilot-custom-agents.md` — Custom Agents + Sub-agent Orchestration
- `wiki/concepts/copilot-fleet-mode.md` — Fleet Mode 並行編排
- `wiki/concepts/copilot-hooks-system.md` — 7+ Hooks 系統
- `wiki/concepts/copilot-steering-queueing.md` — Steering & Queueing
- 16 個 source notes（22 篇 raw 的 1:1 對應，6 個已含在概念頁 provenance）

### 修正頁面

- `wiki/concepts/skills-complex-composition-comparison.md` — **全面重寫** Copilot 欄位，新增「修正紀錄」章節
- `wiki/concepts/agent-skills.md` — 根概念頁比較表格 + 進階導航說明修正

### 更新頁面

- `wiki/topics/skill.md` — 新增 copilot-sdk entity + 5 個 copilot concepts + 6 個 source notes
- `wiki/topics/ai-development-tools.md` — 新增 copilot-sdk entity
- `wiki/index.md` — Entities 47→48、Concepts 44→49、Sources 65→81

### 重要教訓

- ⚠️ **第三方比較文的 Copilot 資訊不可靠**：aiskill.market 文章將 Copilot 描述為「策劃 Extensions、無巢狀、無 hooks」，實際上官方 SDK 已有完整的 Custom Agents + Sub-agent Orchestration + Fleet Mode + 7+ Hooks
- 📏 **修正原則**：官方文件優先於第三方部落格；修正時記錄「從什麼改為什麼」，不默默改掉
- refs: [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]、[[wiki/concepts/skills-complex-composition-comparison|Comparison（已修正）]]

## [2026-08-14] ingest | Anthropic Agent Skills 系列 7 篇（雙模型交叉驗證 + 結構重構）

- **來源**：raw/web/ 7 篇 Anthropic 官方 Agent Skills 文件 + 第三方比較文
  - 2026-08-14-agent-skills.md（Overview）
  - 2026-08-14-在api中開始使用agent-skills.md（Quickstart）
  - 2026-08-14-skill-撰寫最佳實踐.md（Best Practices）
  - 2026-08-14-企業級-skills.md（Enterprise）
  - 2026-08-14-透過api使用agent-skills.md（API Guide）
  - 2026-08-14-github-copilot-vs-claude-code-skills-ecosystem.md（Comparison）
  - 2026-08-14-anthropicsskills-public-repository-for-agent.md（GitHub Repo）
- **雙模型交叉驗證**：Claude + Gemini Round 1 一致（目標頁面、type、topics 關鍵欄位全部吻合），直接採用 → `auto_verified`
  - Claude 提議 11 頁（7 source + 4 content），Gemini 提議 5 頁（0 source + 5 content）
  - 整合策略：保留 Claude 的 1:1 source notes（§3.1 規則）+ 兩者的 content pages 合併
  - 人類補充需求後調整為：根概念頁（基礎/進階分層）+ 複雜組合技比較頁
- **人類補充需求**：
  1. 內容結構分基礎/進階：基礎（定義+撰寫+API+企業）+ 進階（限制+複雜組合技比較）
  2. 複雜組合技比較：Claude Code / GitHub Copilot / Pi Agent 三框架，標記待補充區域
  3. 花園同步規則：Pi 產出內容需經 Gemini 審查後才能推進花園
- **建立**：
  - `wiki/concepts/agent-skills.md` — 根概念頁（基礎/進階導航、已知限制、跨框架比較骨架）
  - `wiki/concepts/skills-complex-composition-comparison.md` — 複雜組合技比較（待補充標記）
  - `wiki/concepts/skill-authoring-best-practices.md` — 撰寫方法論
  - `wiki/concepts/agent-skills-api-usage.md` — API 使用方法
  - `wiki/concepts/agent-skills-enterprise-governance.md` — 企業治理
  - `wiki/entities/anthropic-agent-skills.md` — Anthropic 官方系統實體
  - 7 個 source notes（1:1 對應 raw 來源）
- **更新**：
  - `wiki/topics/skill.md` — 新增 6 個 concepts + 7 個 sources
  - `wiki/topics/ai-development-tools.md` — 新增 anthropic-agent-skills entity + 3 個 concepts
  - `wiki/topics/agent-infrastructure.md` — 新增 agent-skills 根概念
  - `wiki/index.md` — 全量重建（concepts 38→44、entities 46→47、sources 58→65）
- **未建立新目錄**：comparison 類型掛在 concepts/ 下（vault 目前無 comparisons/ 目錄慣例）
- refs: [[wiki/concepts/agent-skills|Agent Skills 根概念]]、[[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]]、7 個 source notes

## [2026-08-14] seed | Harness — LLM 的驅動層（概念根種子建立）

- **新種子**：Notion 知識花園新增「Harness — LLM 的驅動層」（🌱 種子期，🔬 研究）
  - Notion page：`3bc5979e-3a8c-81f9-8e54-eea5a2deeeea`
  - 來源：arxiv 2603.28052（Stanford Meta-Harness 論文）
  - 關聯：🧠 Meta-Harness 元鞍具研究專題
- **Wiki 新建**：`wiki/concepts/harness.md`（概念根頁面）
- **導航更新**：`wiki/topics/ai-agent.md`、`wiki/topics/meta-systems.md` 加入 harness 連結
- **Manifest 更新**：`wiki/entities/knowledge-garden.md`（種子 16→17 顆，研究專題關聯更新）
- **索引更新**：`wiki/index.md`（Concepts 37→38 頁）
- **審查流程**：Cheer + Gemini 雙審（5 點事實修正）→ Pi 修正 → Notion 寫入 + Wiki 建立
- **5 點修正**：(1) Claude Code subagent 非內建硬規範 (2) suggest/auto-edit/full-auto 歸 Codex CLI (3) Pi 消歧義為 earendil-works/pi (4) 一句話定義收斂為單一 LLM (5) meta-harness 拆治理/組合型 vs 自動演化/優化型
- refs: Notion 種子、[[wiki/concepts/harness|harness concept]]、[[wiki/entities/omnigent|Omnigent]]、[[wiki/concepts/meta-harness|Meta-Harness]]

## [2026-08-14] visualmap | Harness 種子視覺地圖建立

- **新建**：`wiki/visualizations/harness-seed-map.md`（Mermaid 種子地圖）
- **Notion**：視覺地圖 Database 註冊（種子地圖，關聯 Harness 種子）
- **Notion**：種子頁「視覺地圖」欄位已連結
- **更新**：`wiki/visualizations/README.md`（新增 Harness 視覺地圖條目）
- **圖表結構**：三層分類（coding agent → meta-harness → multi-agent orchestration）+ 五大核心職責 + 四個具體實例（Claude Code / Codex CLI / Pi / Hermes）+ meta-harness 兩種同名異義（Governance: Omnigent / Evolution: Stanford）
- refs: [[wiki/concepts/harness|harness concept]]、[[wiki/visualizations/harness-seed-map|視覺地圖]]

## [2026-08-14] query | Claude Design 影片方法論深化 + 研究種子建立

- **緣起**：人類檢視 pi 對 YouTube `bBlY5YOsKN8`（Claude Design 五階段）的 ingest 是否足夠。實讀逐字稿比對，確認 pi 未謊報但只抓 WHAT、漏大量 WHY（方法論原則）
- **補強**：`wiki/sources/2026-08-13-claude-design-youtube.md` 新增「方法論原則（可遷移、可實測）」10 條（附逐字稿時間碼）+ 素材性質提醒（AI LABS 行銷型 n=1 來源）
- **新建種子**：`wiki/discussions/ai-frontend-handoff-methodology.md`（type: discussion, status: draft）——研究問題「把前端交給 AI 怎麼交才有效」，含 H1–H8 可實測假設表
- **導航**：更新 topics/ai-development-tools（新增 Discussions 區）、index.md Discussions 4→5
- **待辦**：種子成熟後推進花園需先「圓桌會議 → Claude+Gemini 品質確認」才寫入（Notion 目前未授權）

## [2026-08-14] lint | raw 檔名 normalize：解決 Windows MAX_PATH checkout 失敗

- **問題**：raw/ 檔名過長（最長 230 字元）+ 含空白/逗號/特殊字元，導致 Windows `git clone` checkout 失敗（filename too long / MAX_PATH）
- **規則新增**：AGENTS.md §4.1 加入 raw 命名規範（`YYYY-MM-DD-<slug>`、白名單 slugify：只保留 a-z 0-9 CJK，其餘轉 `-`、收斂連續 `-`、總長 ≤80 字元、撞名加 `-2` `-3`）；§8.1/§8.2 補上 ingest normalize 引用
- **執行**：normalize 全部 raw/ .md 檔案（含 .trash/），最長從 230→78 字元；同步修復 21 個 wiki 頁面的 provenance/wikilink 連結
- **相關 commits**：dac6f06（初始 rename + wiki 連結）、cb94cc6（白名單 slugify 修正）、a928e9f（補齊殘留 wiki 斷連結）
- **已知後續**：`.trash/` 冗餘 raw 檔清理屬另一個 lint 議題，不在本次範圍

## [2026-08-14] complete | 花園視覺地圖 Mermaid 全面驗證與修復（21/21 通過 mmdc render）

- 用 mmdc 實跑驗證全部 21 張種子地圖語法，修好 1 張壞掉的（節點含空格/中文未加引號）
- Omnigent 3 張 + OpenCodeReview 3 張（xychart-beta）視覺地圖從 ASCII-art 轉為真正 Mermaid，全部通過 mmdc render
- 流程改進：驗收標準新增「必須通過 mmdc render（英數 id + 引號標籤）」
- 紓誤：先前疑似成長狀態被清空是查詢方式誤判（`ntn pages get` 不顯示 select 屬性），實查 22 顆都在（🌿8 / 🌱14）
- refs: knowledge-garden entity、Notion 知識花園 Database

## [2026-08-14] complete | 🎯 花園 22 顆種子全部處理完畢

- 第四批收尾（最後 8 顆「完整」種子）：NPM Publishing 補視覺地圖+來源 URL；Omnigent 來源 URL 空格修正；5 顆核心種子補 Wiki Path/最後更新；4 顆視覺地圖 ASCII→Mermaid
- OpenCodeReview、Omnigent 維持 🌱（缺實際使用經驗）
- 決策：🌿 成長期門檻 = 有實際使用經驗，光內容完整+視覺地圖不足以升級
- 最終統計：22 種子（🌿6 / 🌱16）

## [2026-08-14] update | 技術類第三批 6 顆種子完成補強 + Gemini 證據型評審通過

- 更新 6 個 wiki 頁面 updated 日期至 2026-08-14：agentic-ai、code-graph、loop-vs-graph-engineering、mcp-model-context-protocol、lsp、tree-sitter
- Gemini 證據型評審修正：移除 3 個杜撰假連結（nicholasgasior/awesome-*）、修正 Tree-sitter 維護者姓名 Max Cantor→Max Brunsfeld、Code Graph 錯字修正
- 流程改進：Pi 寫入來源 URL 前必須 HTTP 驗證回 200
- refs: MCP 92 / LSP 95 / Tree-sitter 87 / Agentic AI 84 / Code Graph 83 / LOOP Engineering 76

## [2026-08-13] incident | 花園巡檢空白種子事故全記錄 + garden-guard extension 建立

- 事故：2026-08-13 花園巡檢新種 5 顆種子，Notion 頁面 body content 全空白（只有 Properties）
- 根本原因：把「Properties 填完」誤認為「種子種好了」，不知道 Notion DB 記錄和頁面 body content 是兩次 API 呼叫
- 圓桌會議（Claude+Gemini+Copilot）：共識「純文字規則不可靠，要分層」
- 修復：SKILL.md §1 原子化寫入 + §4 確定性抽驗 + Phase 3 批量確認；建立 garden-guard.ts（攔截型 extension，3 個 gate）
- 兩輪 bug 修正：frontmatter 剝離 + regex/括號計數不對稱
- 新增：`wiki/sources/2026-08-13-garden-guard-incident.md`
- 修正：`wiki/concepts/agent-extension-installation.md`（補上 extensions/ 路徑 + 三種載入方式比較 + Extension 類型分類）
- 修正：`work/history/2026-08.md`（取代標記修正不準確的自我回報）
- refs: [[wiki/sources/2026-08-13-garden-guard-incident]]

## [2026-08-14] update | 知識管理系列 5 顆種子完成：第二批半成品補強 + 評審通過

- 更新頁面：
  - wiki/concepts/zettelkasten.md — 新增「歷史與出處」段落（Luhmann 實踐→Ahrens 系統化），修正 luhmann.com→niklas-luhmann-archiv.de 官方典藏連結
  - wiki/concepts/para.md、lint-ruleset.md、semantic-relations.md、moc-map-of-content.md — 已確認與 Notion 端同步，內容完整
- 流程：派工→補視覺地圖+來源+內容→Cheer+Gemini 證據型評審（PARA 92 / Lint 88 / 語意關係 96 / MOC 94 / Zettelkasten 82→修正）→修訂
- 修復結構性 Bug：視覺地圖 Mermaid 從種子頁底部搬進 DB 記錄頁，清除 8 顆種子的孤兒頁面
- refs: [[wiki/concepts/zettelkasten|Zettelkasten]]、[[wiki/concepts/para|PARA]]、[[wiki/concepts/lint-ruleset|Lint 規則集]]、[[wiki/concepts/semantic-relations|語意關係]]、[[wiki/concepts/moc-map-of-content|MOC]]

## [2026-08-14] sync | 花園種子內容同步：LangGraph / GitHub Copilot / Claude Code

- 來源：Notion 知識花園 3 顆種子頁面（經 Cheer + Gemini 評審後的版本）
- 同步方向：Notion → 本機 wiki entities
- 更新頁面：
  - wiki/entities/langgraph.md — 大幅擴充：新增生态系工具（Studio/Platform/LangGraph.js）、完整比較表（vs Pi/AutoGen/CrewAI）、深度架構差異分析、客觀事實表（版本、Stars、授權）
  - wiki/entities/github-copilot.md — 大幅擴充：11 個生态系組件完整剖析、定價方案、Copilot Memory、Third-party Coding Agents、與 Pi/Claude Code 差異比較表
  - wiki/entities/claude-code.md — 大幅擴充：8 個核心機制（Agentic Loop/Extended Thinking/MCP/Skills/Subagents/Hooks/Context Compaction/Permission Prompting）、Hooks 完整事件表、客觀事實表、Claude Design MCP Handoff
- 更新：wiki/index.md（三個 entity 標題與摘要更新）
- 重點：三顆種子 Notion 版本均遠比本地 wiki 豐富，本次同步大幅擴充 wiki 知識品質
- refs: [[wiki/entities/langgraph|langgraph]]、[[wiki/entities/github-copilot|github-copilot]]、[[wiki/entities/claude-code|claude-code]]

## [2026-08-13] ingest | How To Use Claude Design To Build Beautiful Sites (YouTube)

- 來源：YouTube 影片（AI LABS），18:33，英文 auto-generated 字幕
- 字幕取得方式：pytubefix（youtube_transcript_api XML ParseError fallback）
- 雙模型交叉驗證：Claude + Gemini Round 1 一致同意 claude-design entity + source note；分歧在 design.md 分類（concept vs entity）與是否開新 topic → Round 2 雙方均收斂為 concept + 沿用 ai-development-tools
- 建立：
  - `wiki/sources/2026-08-13-claude-design-youtube.md` — source note
  - `wiki/entities/claude-design.md` — Claude Design entity（五階段工作流）
  - `wiki/concepts/design-md-format.md` — design.md 格式規範概念
- 更新：
  - `wiki/entities/claude-code.md` — 新增 Claude Design MCP Handoff 章節
  - `wiki/topics/ai-development-tools.md` — 新增 claude-design + design-md-format 連結
  - `wiki/index.md` — 全量重建（concepts 32、entities 43、sources 57）
- Git commit：待執行

---

## [2026-08-13] ingest | Prime Agent 官方發布 + AI郵報 ARC-AGI-3 分析（修正 Prime Intelligence → Prime Intellect）

- 來源 1：Prime Intellect 官方部落格《Prime Agent: A self-improving RLM agent》（2026-08-05）
  - Raw：raw/web/2026-08-13-prime-agent-self-improving-rlm-agent.md
- 來源 2：AI 郵報《Prime Agent 是什麼？ARC-AGI-3「95.5%」背後》（Philo，2026-08-06）
  - Raw：raw/web/2026-08-13-prime-agent-arc-agi-3-opus-5-harness-aiposthub.md
- 補充參考（非獨立 raw）：GitHub repo https://github.com/PrimeIntellect-ai/prime-agent
- 雙模型交叉驗證（Claude + Gemini，各自獨立產出結構化提案）：Round 1 關鍵欄位（目標頁面、type、topics、是否推翻既有結論）一致，直接採用 → `auto_verified`
  - 唯一差異：Claude 額外提議可選的獨立 `reward-hacking-in-agents.md` 頁面，但本身標註為可選、可併入 `continual-harness.md` 風險章節；Gemini 未提議獨立頁；採用併入方案，不視為分歧
- **查詢既有知識**：發現今天稍早已從 YouTube 影片《I Replaced Pi and OpenCode With This》ingest 過一版簡略頁面，且誤植開發者為「Prime Intelligence」
- **推翻/修正既有結論**：
  - 開發者名稱錯誤：「Prime Intelligence」→「**Prime Intellect**」（`wiki/entities/prime-agent.md`、`wiki/sources/2026-08-13-prime-agent-youtube.md` 均修正，並加註修正說明）
  - RLM 核心定義過於簡化：舊版僅寫「觸發 subagent」，官方原文核心定義是「把 context 當變數」，subagent fan-out 只是其中一種應用；`wiki/concepts/recursive-language-model.md` 重寫定義章節
  - ARC-AGI-3 95.5% 需要限定語：新增三種成績口徑對照（95.5% 官方自測 / 95.24% ARC Prize 公開 scorecard / 30.16% Opus 5 標準 harness Verified），避免被當成「已驗證超越人類」
- 新建頁面：
  - wiki/sources/2026-08-13-prime-agent-official-blog.md — 官方部落格來源筆記
  - wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub.md — AI郵報分析來源筆記
  - wiki/concepts/continual-harness.md — harness CRUD 化 + `/refine` + reward hacking 風險（Factorio RCON 案例）
  - wiki/concepts/arc-agi-3-benchmark.md — ARC-AGI-3 測試設計、RHAE 計分、三種成績口徑對照表
- 更新頁面：
  - wiki/entities/prime-agent.md — 大幅擴充：修正開發者名稱、補 GitHub/License/安裝指令、autonomous mode、ARC-AGI-3/長文本/EmulatorBench/Factorio 評測、風險提醒章節（sources: 1 → 3）
  - wiki/concepts/recursive-language-model.md — 核心定義重寫（context-as-variable）、補充 persistent subagent、agent_message 通訊機制（sources: 1 → 2）
  - wiki/sources/2026-08-13-prime-agent-youtube.md — 修正開發者名稱，加註連結到官方部落格來源
  - wiki/topics/ai-agent.md — 更新 prime-agent 描述、新增 continual-harness、arc-agi-3-benchmark concepts、新增 2 筆 sources
  - wiki/topics/agent-research.md — 新增 continual-harness、arc-agi-3-benchmark（跨 topic 🛠️）
  - wiki/index.md — 全量重建 Concepts/Entities/Sources 相關條目與統計（31 concepts、44 entities、56 sources）

## [2026-08-13] ingest | Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent

- 來源：YouTube 影片《I Replaced Pi and OpenCode With This》(Martí Blanes, 08:29)
- Raw transcript：raw/youtube/i-replaced-pi-and-opencode-with-this.md (185 segments → 22 paragraphs)
- 雙模型交叉驗證（Claude + Gemini）：Round 1 一致，直接採用
- 新建頁面：
  - wiki/sources/2026-08-13-prime-agent-youtube.md — 來源筆記
  - wiki/entities/prime-agent.md — Prime Agent entity
  - wiki/concepts/code-execution-as-tool-calling.md — Python Runtime 取代 Tool Calling 模式
  - wiki/concepts/recursive-language-model.md — RLM subagent 概念
- 更新頁面：
  - wiki/entities/pi-agent-core.md — 補充 Prime Agent 作為下游擴充案例
  - wiki/entities/hermes-agent.md — 補充 Continual Harness 比較
  - wiki/topics/ai-agent.md — 新增 entities + concepts + sources

## [2026-08-12] decision | 知識系統架構 v3 修正：雙模型共識取代人類確認

- 更新 wiki/decisions/knowledge-system-architecture-decision.md：新增第十一節，記錄 v2.0 → v3.0 的修正與實作
- 更新 wiki/concepts/knowledge-system-architecture.md：同步移除「人類確認」「0.88 信心度」等已推翻的描述
- 更新 AGENTS.md（§3.1/§3.2/§3.3/§3.5/§5a/§6）：Ingest/Backfill 改為雙模型交叉驗證（Pi 主持不投票，Claude+Gemini 為預設參與者，分歧 Round 2 覆核，仍不一致才找 Copilot 第三票，輪數上限 2 輪+第三票）；Staging Buffer 語意從「等批准」改成「等共識」；decisions/discussions 語意調整為「共識已收斂／仍無法收斂」；topics.md、topics/*.md、index.md 三處重複列表收斂成一份正本
- 更新 wiki-ingest skill：同步雙模型交叉驗證流程
- 更新 wiki-lint skill：矛盾偵測改共識仲裁、新增遺漏稽核、Staging 逾時改自動晉升、輸出格式改為「需要人類判斷／AI 已自動處理」兩類
- 更新知識花園 5 個 skills（knowledge-garden / knowledge-garden-page-content / knowledge-garden-trigger / knowledge-garden-visualmap）：Phase 3 改成整合重寫（取代標記用刪除線+說明，不再累加）、新增 Phase 0.5 多元觀點合成（研究專題/成熟種子用 round-table）、發布前確認機制、花園巡檢擴充（空洞頁面、缺視覺地圖、研究專題過大）、視覺地圖自動觸發
- 重點：把「防遺漏」和「防品質問題」拆開處理——不篩選原則本身解決遺漏，雙模型共識取代不可靠的自評信心分數解決品質；花園保留人類預覽確認（唯一允許手動之處），大腦全自動
- refs: wiki/decisions/knowledge-system-architecture-decision、wiki/concepts/knowledge-system-architecture

## [2026-08-20] ingest | Round Table 多 AI 圓桌會議 Skill 更新

- 新增 entity：wiki/entities/round-table.md（多 AI 圓桌會議 skill）
- 更新 skill：C:/Users/User/.agents/skills/round-table/SKILL.md
  - 新增量化共識偵測機制（newArguments、coverageRate、agreementRate、openDisputes）
  - 新增主持人結論（每輪 round-N-summary.md）
  - 新增論點追蹤表（arguments-tracker.md）
  - 修復重複 Gemini prompt 模板
  - 新增分歧點阻止結束條件
- 更新 topics：wiki/topics/skill.md（加入 round-table entity）
- 更新 index：wiki/index.md（Entities 加入 round-table）
- 重點：雙重門檻結束條件（量化觸發 + 無未解決分歧）、每輪主持人結構化結論、全程論點追蹤
- refs: [[wiki/entities/round-table|round-table]]、[[wiki/topics/skill|Skill Topic]]

## [2026-08-10] ingest | What Is Chunkless RAG? How Docling & AI Agents Navigate Documents

- 新增 raw transcript：raw/youtube/chunkless-rag-docling.md（00:07:00 / 98 segments）
- 新增 source note：wiki/sources/2026-08-10-chunkless-rag-docling.md
- 新增 entity：wiki/entities/docling.md（IBM Docling 文件解析框架）
- 新增 concept：wiki/concepts/chunkless-rag.md（保留文件結構的 RAG 方法）
- 更新 topics：wiki/topics/ai-agent.md（加入 Docling + Chunkless RAG）
- 更新 index：wiki/index.md（Source notes 32→33、Entities 加入 Docling、Concepts 加入 Chunkless RAG）
- 來源：YouTube 字幕（pytubefix SRT fallback，手動英文字幕）
- 重點：Chunkless RAG 保留 tree structure 讓 Agent 推理導航、Docling 把 PDF 解析成結構化 tree、兩者混用策略
- refs: [[wiki/sources/2026-08-10-chunkless-rag-docling|Source Note]]、[[wiki/entities/docling|Docling]]、[[wiki/concepts/chunkless-rag|Chunkless RAG]]

## [2026-08-10] lint | Wiki Lint 完整健康檢查 + 知識系統架構設計

- 執行完整 wiki lint：修復 6 筆 provenance、3 筆 frontmatter、建立 topics 連結
- 清理 raw 冗餘：移動 43 筆到 trash（Redis 18 + LSP 9 + pi-agent 2 + 其他 14）
- 更新 wiki-lint skill：新增知識品質檢查（矛盾、過時、缺漏概念、交叉引用、資料缺口、Source Fidelity）
- 圓桌會議 4 輪：Pi + Gemini + Copilot 討論知識系統架構
- 產出最終報告：.pi/round-table/20260810-lint-improvement/FINAL-REPORT-v2.md
- 修改 AGENTS.md：新增知識演化協定（回填機制、Staging Buffer、花園同步）
- 補強 index.md：完整列出所有 concepts、entities、sources
- 重點：三個操作 + 一個機制 + 一個回流、半自動回填、Staging Buffer TTL 21 天
- refs: FINAL-REPORT-v2.md、AGENTS.md、wiki-lint SKILL.md

## [2026-08-10] ingest | Write Code You Will Never Read Again

- 新增 source note：wiki/sources/2026-08-10-write-code-you-will-never-read-again.md
- 新增 entity：wiki/entities/theo-t3gg.md（Theo / t3gg）
- 新增 concept：wiki/concepts/code-importance-spectrum.md（程式碼重要性光譜）
- 更新 entity：wiki/entities/uncle-bob.md（加入 Theo 交叉引用）
- 更新 topics：wiki/topics/ai-agent.md（加入 Theo + code-importance-spectrum + source）
- 更新 index：wiki/index.md（Source notes 31→32、Entities 加入 Theo）
- 來源：YouTube 字幕（yt-dlp auto-generated）
- 重點：4 層程式碼重要性光譜、用 cheap code 驗證 important code、Code is useful for things other than merging
- refs: [[wiki/sources/2026-08-10-write-code-you-will-never-read-again|Source Note]]、[[wiki/entities/theo-t3gg|Theo]]、[[wiki/concepts/code-importance-spectrum|Code Importance Spectrum]]

## [2026-08-10] ingest | Uncle Bob Stopped Reading AI-Generated Code

- 新增 source note：wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code.md
- 新增 entity：wiki/entities/uncle-bob.md（Robert C. Martin）
- 更新 topics：wiki/topics/ai-agent.md（加入 Uncle Bob + source）
- 更新 index：wiki/index.md（Source notes 26→31、Entities 加入 Uncle Bob）
- 來源：YouTube 字幕（yt-dlp auto-generated）
- 重點：Uncle Bob 宣布不再閱讀 AI 程式碼，改用極端約束驗證；引發工程師角色轉變討論
- refs: [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Source Note]]、[[wiki/entities/uncle-bob|Uncle Bob]]

## [2026-08-09] seed | Obsidian — AI Agent 時代的知識管理基礎設施

- 新增種子：Obsidian（Notion + 視覺地圖）
- 新增 wiki 頁面：wiki/entities/obsidian.md
- 來源：Gemini Deep Research + 本機 wiki 知識庫
- 新增：wiki/visualizations/obsidian-seed-map.md（視覺地圖 wiki 頁面）
- 新增：.pi/obsidian-visualmap.svg（SVG 視覺地圖）
- 更新：knowledge-garden.md manifest（加入 Obsidian 種子）
- 重點：疫情催生→150 萬用戶、LLM Wiki 範式、raw→wiki→Notion 資料流、OKF 90%+ 相容
- 關聯專題：AI Agent 架構研究、知識管理系統
- refs: [[wiki/entities/obsidian|Obsidian]]、[[wiki/visualizations/obsidian-seed-map|視覺地圖]]、Notion 種子 #3b75979e-3a8c-81d8-b7aa-f1f4c88aa957

## [2026-08-08] ingest | Content Redesign 實作：重寫 page-content + 更新 visualmap

- 重寫 knowledge-garden-page-content：
  - 移除模板綁定（🌱/🌿/🌳 不再決定內容結構）
  - 改為按標的物類型（種子/專題/地圖）
  - 四層通用骨架（識別→客觀事實→主觀現實→roadmap）
  - 加入 Mode 切換（Quick Draft vs Enriched）
  - 加入品質關卡（Evaluator-Optimizer Loop）
- 更新 knowledge-garden-visualmap：
  - 視覺地圖 Database = 索引/註冊表（不是內容存放）
  - 6 個欄位：頁面、類型、關聯種子、關聯專題、建立時間、更新時間
- 更新架構頁：加入 Content 設計區塊
- refs: [[wiki/concepts/content-redesign|Content Redesign]]、[[wiki/concepts/knowledge-garden-skill-architecture|架構圖]]

## [2026-08-08] ingest | Content 設計 Redesign（Copilot + Gemini 三輪討論）

- 確立四個共識：
  - 四層通用骨架（識別→客觀事實→主觀現實→roadmap）
  - 成長階段≠模板，內容結構由標的物類型決定
  - 研究專題≠種子（有限收斂 vs 無窮演進）
  - 視覺地圖 Database schema（索引/註冊表，不是內容存放）
- 新增 concepts：content-redesign
- 更新 topics：knowledge-mgmt
- refs: [[wiki/concepts/content-redesign|Content Redesign]]

## [2026-08-08] ingest | 知識花園 Skill 架構重構 + 命名統一 + 三輪討論決策

- 重構 10 個相關 skills + 命名統一
- Copilot + Gemini 三輪架構討論，確立6項最終決策
- Schema 遷移到 knowledge-garden/schemas/（跟著主 skill）
- 新增 concepts：knowledge-garden-skill-architecture
- refs: [[wiki/concepts/knowledge-garden-skill-architecture|架構圖]]

## [2026-08-08] ingest | Plannotator Copilot CLI 安裝手冊（PDF）+ MarkItDown 研究

- 來源：PDF `C:/Users/User/Downloads/安裝手冊.pdf` → markitdown 提取 → raw/web/
- 新增 entities：markitdown（Microsoft 萬能格式→Markdown 轉換器）
- 新增 sources：2026-07-23-plannotator-copilot-setup
- 更新 entities：plannotator（+Copilot CLI 整合安裝、+source reference）
- 重點：三種安裝方式（Skill / Prompt / 手動）、資安提醒、常用指令、FAQ
- 方法：先用 pdftotext（中文亂碼），後改用 markitdown（完整保留中文與結構）
- 清理：卸載我安裝的 PyMuPDF（markitdown 已取代）
- refs: [[wiki/sources/2026-07-23-plannotator-copilot-setup|source note]]、[[wiki/entities/plannotator|plannotator]]、[[wiki/entities/markitdown|markitdown]]、[[raw/web/2026-07-23-plannotator-copilot-setup-manual|raw]]

## [2026-08-08] ingest | HTML Slide Deck 製作工作流 + presentation skill

- 來源：Plannotator × Obsidian 簡報製作過程的經驗沉澱
- 新增 concept：html-slide-deck-workflow（整合 guizang + huashu 設計系統）
- 新增 skill：presentation（讓「做簡報」自動觸發正確工作流）
- 更新：skill.md topic nav、index.md
- 重點：guizang 靛藍瓷主題 + huashu deck_stage.js + 反 AI slop + Gemini/Copilot 雙審閱
- refs: [[wiki/concepts/html-slide-deck-workflow|concept]]、presentation skill

## [2026-08-08] ingest | Plannotator × Obsidian 簡報企劃

- 來源：Pi 整理的簡報企劃文件（經過 Gemini + Copilot 雙重審閱）
- 新增：source note（2026-08-08-plannotator-obsidian-presentation）
- 更新：skill.md、knowledge-mgmt.md topic nav、index.md
- 重點：14 頁 HTML slide deck 企劃，知識迴流系統報告，系統架構課分享會
- refs: [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|source note]]

## [2026-08-08] ingest | guizang-ppt-skill + huashu-design

- 來源：raw/web/ — 歸藏 PPT Skill + 花叔 Huashu Design GitHub README
- 新增 entities：guizang-ppt-skill、huashu-design
- 新增 sources：2026-08-08-guizang-ppt-skill、2026-08-08-huashu-design
- 更新：skill.md topic nav、index.md
- 重點：兩款 HTML 原生設計技能，guizang 專注 PPT（雙視覺系統+演講者模式），huashu 覆蓋原型/動畫/MP4/評審
- refs: [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]]、[[wiki/entities/huashu-design|huashu-design]]、[[wiki/sources/2026-08-08-guizang-ppt-skill|source 1]]、[[wiki/sources/2026-08-08-huashu-design|source 2]]

## [2026-08-21] ingest | LangGraph in 10 Minutes (Explained Clearly)

- 來源：YouTube — Cloud Codes 解析 LangGraph runtime 架構
- 新增：source note + entity（langgraph）+ concept（agent-durability-patterns）
- 更新：loop-vs-graph-engineering（+1 source）、pi-agent-core（+cross-ref）、ai-agent topic nav、index
- 重點：Pregel model、reducers、checkpointing、delta channels（5.3GB→129MB）、Deep Agents / Functional API 替代方案
- 新 concept：Agent Durability Patterns — Pi vs LangGraph 三種持久化路徑比較，作為未來 agent 設計的取捨參考
- refs: [[wiki/sources/2026-08-21-langgraph-in-10-minutes|source note]]、[[wiki/entities/langgraph|langgraph]]、[[wiki/concepts/agent-durability-patterns|agent-durability-patterns]]

## [2026-08-07] ingest | Notion「任何當下 → AI相關」頁面 ingest

- 來源：Notion「任何當下 → AI相關」頁面（Skill、Copilot、Claude、AI Gateway）
- 新增 entities：agentskills-io-standard、axway-amplify-ai-gateway
- 更新 topics：ai-agent.md、skill.md
- 方法：Notion MCP 讀取 + agy Gemini 視覺分析 19 張截圖
- refs: [[wiki/entities/agentskills-io-standard|agentskills-io-standard]]、[[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]]

## [2026-08-07] work | 花園內容檢查 + Learning Loop 建立

- 檢查花園：7 顆種子全有內容，5 個專題全有定義
- 發現連結缺口：OKF、OpenCodeReview 缺專題連結；知識管理系統缺種子
- 新增 learning-loop entity + skill
- 修正 knowledge-garden SKILL：種子建立流程
- refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/learning-loop|learning-loop]]

## [2026-08-07] new entity | Learning Loop — 自動學習優化系統

- 新增 entity：`wiki/entities/learning-loop.md`
- 新增 skill：`~/.agents/skills/learning-loop/SKILL.md`
- 功能：任務後自動沉淀、工作模式分析、Skill 自我優化
- 修正 knowledge-garden SKILL：種子建立流程（先建 Database 記錄，再補內容）
- 首次測試：回顧 2026-08-07 工作，建立 Learning Journal
- refs: [[wiki/entities/learning-loop|Learning Loop]]、[[wiki/entities/knowledge-garden|knowledge-garden]]

## [2026-08-06] ingest | Wow YouTube 5支 AI Agent 前沿研究

- 來源：Wow 頻道 5 支影片（Knowledge Graph、AReaL2.0、Context-CoT、SkillOpt、MemGraph-RAG）
- 新增 source note：`wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research.md`
- 新增 entities：graphify、area-l、skillopt、memgraph-rag、hermes-agent（更新）
- 新增 concepts：code-knowledge-graph、atdp、context-cot、skillopt-meta-skill
- 更新 topics：ai-agent（+5 entities, +4 concepts）、skill（+1 entity, +1 concept）
- 原始研究報告：`raw/research/2026-08-06-wow-youtube-5-ai-agent-topics.md`
- 方法：yt-dlp 提取影片 metadata → Gemini Deep Research 深度研究 → wiki 整合
- 注意：所有5支影片均無字幕（subtitles disabled），內容來自描述 + Gemini 研究

## [2026-08-20] ingest | OpenCodeReview Gemini 深度研究

- 新增 source：`wiki/sources/2026-08-20-opencode-review-deep-research.md`
- 新增 raw：`raw/research/2026-08-20-opencode-review-deep-research.md`
- 來源：Gemini 2.5-pro 深度研究（9 個來源，3 Tier-1）
- 重點：阿里內部使用故事、社群討論細節、SonarQube 互補關係、私有模型適配、AI 趨勢洞察
- refs: [[wiki/entities/open-code-review|OpenCodeReview]]

## [2026-08-20] work | 新增 3 個 OpenCodeReview 相關任務

- W-2026-08-030：安裝並測試 OCR
- W-2026-08-031：比較實驗 OCR vs code-review skill
- W-2026-08-032：整合 OCR 到 Code Review 工作流
- refs: [[wiki/entities/open-code-review|OpenCodeReview]]

## [2026-08-20] ingest | OpenCodeReview 深度分析與比較

- 新增 entity：`wiki/entities/open-code-review.md`（阿里巴巴 AI 代碼審查工具）
- 內容：核心設計（確定性工程 × Agent 混合）、爆紅原因、使用方式、Benchmark、與 Plannotator / code-review skill 比較
- 重點：19.3k stars、解決通用 Agent 做 code review 的痛點（覆蓋不全、位置漂移、效果不穩定）、Token 消耗僅 1/9
- refs: W-2026-08-029

## [2026-08-20] ingest | 建立 Pi Agent 更新學習系統

- 新增 topic：`wiki/topics/pi-agent-learning/roadmap.md`（四階段學習路線圖）
- 新增 topic：`wiki/topics/pi-agent-learning/progress.md`（學習進度追蹤）
- 新增 entity：`wiki/entities/pi-agent/changelog/v0.84.0.md`（v0.84.0 更新分析）
- 新增 entity：`wiki/entities/ai-agent-core/chain-of-thought.md`（Chain-of-thought 概念）
- 新增 entity：`wiki/entities/ai-agent-core/token-budget.md`（Token 預算概念）
- 新增 design：`work/designs/pi-agent-learning-system.md`（完整學習系統設計）
- 更新 index：新增 AI Agent 學習系統區塊
- 更新 work/current：新增 W-2026-08-029
- 重點：從「使用工具」到「理解原理」到「自己建造」的學習路徑，每次 Pi 更新時執行分析學習迴路

## [2026-08-19] source | 新增 Pi Agent GitHub Copilot Provider 設定指南

- 新增 source note：2026-08-19-pi-agent-github-copilot-provider-setup
- 內容：Pi Agent 原生支援 Copilot OAuth 設定流程、模型清單、常見問題
- 重點：執行 `/login` → 選擇 GitHub Copilot → 完成授權即可；模型清單為靜態，如模型不出現需在 VS Code 中先 Enable
- 來源：Pi Agent 官方文件 + GitHub Issue #4599
- refs: W-2026-08-028

## [2026-08-19] work | 新增 4 個工作項目

- W-2026-08-025：AI Agent 網路查詢能力研究
- W-2026-08-026：優化 Gemini Deep Search Prompt
- W-2026-08-027：設定 Gemini CLI 環境
- W-2026-08-028：研究 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider

- 新增 Notion Database：`4f0c3d63-8d5f-4e02-b6a9-e7d6540bebb3`（9 個欄位）
- 新增導覽頁：`3b25979e-3a8c-81b6-a2ee-ff4945a79e43`（使用指南、統計、相關連結）
- 新增研究專題管理頁：`3b25979e3a8c-81a6-9f98-c1016d370dff`（專題生命週期管理）
- 更新 knowledge-garden manifest：新增 Database 結構、Views、相關連結
- 更新 knowledge-mgmt topic：新增 knowledge-garden entity、Notion 花園連結
- 更新 work/current：新增 W-2026-08-021
- 重點：人機共用導覽系統、混合式組織（成長狀態 + Tags）、6 Views
- 待辦：遷移 6 顆種子、人類調整 Notion 結構、建立 Views

## [2026-08-11] work | Notion 工作盤點

- 整理所有 Notion 相關待辦工作到 work/current.md
- 包括：遷移種子、建立 Views、盤點 wiki 素材、確認 Database 結構等
- 新增 Notion 工作清單區塊

## [2026-08-04] ingest | OKF + LSP + Code Graph 在 AI Coding Agent 生態系的整合應用

- 新增 source：`wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research.md`
- 新增 entity：`wiki/entities/mcp-model-context-protocol.md`（Model Context Protocol）
- 新增 entity：`wiki/entities/github-copilot.md`（GitHub Copilot 生態系）
- 新增 entity：`wiki/entities/claude-code.md`（Claude Code + MCP）
- 新增 entity：`wiki/entities/openai-codex.md`（OpenAI Codex CLI）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 Copilot、Claude Code、Codex、MCP）
- 更新 topic：`wiki/topics/meta-systems.md`（加入 MCP）
- 更新 index：Sources 22→23，Entities 15→19
- 來源：Gemini Deep Research + raw/web（15+ 篇 URLs）
- 重點：三位一體架構（OKF+LSP+CodeGraph）、主流 AI Agent 分析、語言工具鏈、安裝指南、安全性、演進時間線

## [2026-08-04] ingest | LSP 與 Code Graph 技術深度研究

- 新增 source：`wiki/sources/2026-08-04-lsp-code-graph-research.md`
- 新增 entity：`wiki/entities/lsp.md`（Language Server Protocol）
- 新增 entity：`wiki/entities/tree-sitter.md`（Parser Generator）
- 新增 concept：`wiki/concepts/code-graph.md`（代碼語意圖譜）
- 更新 topic：`wiki/topics/meta-systems.md`（加入 LSP、Tree-sitter、Code Graph）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 Code Graph concept、LSP source）
- 更新 index：Sources 21→22，Entities 13→15，Concepts 10→11
- 來源：Gemini Deep Research + raw/web（5 篇 URL）
- 重點：LSP $M \times N \rightarrow M + N$ 架構革新、Tree-sitter 增量解析、Code Graph 語意網路、AI Code Assistant 整合（Graph-RAG、Self-Correction Loop、SCIP/LSIF）

## [2026-08-10] ingest | Antigravity CLI 整合 — Gemini CLI 免費層停用後的整合方案

- 新增 source：`wiki/sources/2026-08-10-antigravity-cli-integration.md`
- 更新 topic：`wiki/topics/ai-agent.md`（加入新 source）
- 更新 index：Sources 20→21
- 來源：raw/conversations/2026-08-10-antigravity-cli-integration.md
- 重點：Gemini CLI 免費層已停用（2026/6/18）、Antigravity CLI（agy）安裝與整合、gy skill 建立、週制配額注意事項

## [2026-08-10] ingest | pi-loop-scheduler — Pi Cron Job 實作原理

- 新增 entity：`wiki/entities/pi-loop-scheduler.md`（@pi-agents/loop 架構解析）
- 新增 source：`wiki/sources/2026-08-10-pi-cron-job-explained.md`
- 更新 topic：`wiki/topics/ai-agent.md`、`wiki/topics/extension-dev.md`
- 更新 index：Entities 12→13，Sources 19→20
- 來源：@pi-agents/loop 原始碼與文件
- 重點：Idle gating（只在 agent 閒置時觸發）、Anti-thundering-herd（50% jitter）、Durable vs Session tasks、Multi-instance safety、Cron expression 5-field format

## [2026-08-03] ingest | Loop vs Graph Engineering — AI Agent Concepts Demystified

- 新增 source：`wiki/sources/2026-08-03-loop-vs-graph-engineering.md`
- 新增 concept：`wiki/concepts/loop-vs-graph-engineering.md`
- 更新 entity：`wiki/entities/waku-agent.md`（加入 graph engineering 章節，sources 2→3）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 loop-vs-graph-engineering concept）
- 更新 index：Sources 18→19，Concepts 10→11
- 來源：raw/youtube（YouTube IMLwvK08JVc，21:30，610 segments）
- 重點：AI Agent 工程階梯（Prompt→Context→Skills→Loop→Graph）、Loop 自主迭代 vs Graph 預定義流程、兩者共存非替代、Graph 內含 Loop

## [2026-08-09] ingest | Redis Caching Patterns — 完整指南

- 新增 source：`wiki/sources/2026-08-09-redis-caching-patterns.md`
- 更新 index：Sources 區塊加入新 source note（sources 17→18）
- 來源：raw/web（Medium Artem Khrienov 文章）
- 重點：Cache-Aside + stampede 防護、Write-Through/Write-Behind 權衡、Hashes 省頻寬、滑動視窗 Rate Limiting、Circuit breaker graceful degradation
- 測試：URL → web_fetch → raw/web/ → ingest 全流程驗證成功

## [2026-08-08] ingest | Waku agent — Local-first personal AI agent

- 新增 entity：`wiki/entities/waku-agent.md`（四大支柱 readable blueprint）
- 新增 source：`wiki/sources/2026-08-02-waku-agent-code-walkthrough.md`（YouTube 20:49）
- 新增 source：`wiki/sources/2026-08-02-waku-agent-github-readme.md`
- 更新 topic：`wiki/topics/ai-agent.md`
- 來源：raw/youtube + raw/web

## [2026-08-08] ingest | pi-agent-dashboard — BlackBeltTechnology Web Dashboard

- 新增 entity：`wiki/entities/pi-agent-dashboard.md`
- 更新 topics：`wiki/topics/ai-agent.md`、`wiki/topics/extension-dev.md`
- 來源：raw/web

## [2026-08-08] ingest | QwenPaw — YouTube + GitHub README

- 新增 entity：`wiki/entities/qwenpaw.md`
- 新增 source：`wiki/sources/2026-08-08-qwenpaw-youtube-intro.md`
- 新增 source：`wiki/sources/2026-08-02-qwenpaw-github-readme.md`
- 更新 topic：`wiki/topics/ai-agent.md`
- 來源：raw/web + raw/youtube

## [2026-08-08] skills-repo | 建立 cheerio-skills 私有 repo

- 盤點自有 skills 並分類（Agent-agnostic / Obsidian-dependent / Pi-specific）
- 建立 `C:/cheerio/pi/cheerio-skills/`，格式符合 `npx skills add` 規範
- 推送到 https://github.com/CheerioCorner/cheerio-skills

## [2026-08-06] e2e-test | Notion ↔ Obsidian 雙向同步測試完成 ✅

- 測試流程：raw → wiki → Notion → raw（回環測試）
- 結果：全部通過

## [2026-08-06] update | Notion ↔ Obsidian 雙向同步機制

- `knowledge-garden` skill 新增 §Manifest 自動同步
- 三向資料流完整：raw → wiki → Notion → raw

## [2026-08-08] lint | Provenance 格式修正 + Vibe Coding 連結修復

- Provenance 格式從 `provenance_raw` + `provenance_url` 遷移為 `provenance: [{kind, path, url}]`
- Vibe Coding 頁面修復 2 個 broken wikilinks
- 所有 sources 頁面已補 provenance

## [2026-08-06] ingest | 批次 ingest 5 筆 raw

- omnigent、OKF catalog、defect metrics、morphir-dotnet、vibe-coding
- 修復 4 個 topic page 遺漏
- 強化 skill/AGENTS.md topic 同步步驟

## [2026-08-06] ingest | Pi Architecture Walkthrough

- 完成 Pi Architecture Walkthrough ingest
- agentic loop 三步驟、session tree、compaction、skills 機制、extensions events

## [2026-08-05] ingest | Notion → raw 抓取流程

- 建立 `notion-to-raw` skill
- 三條路徑：只是看看 / 寫 raw / deep research

## [2026-08-05] ingest | YouTube 字幕抓取方案

- `fetch_transcripts.py`（pytubefix + youtube_transcript_api）可直接複用
- 實測抓取 Tau 影片成功

## [2026-08-03] ingest | YouTube-to-Wiki skill

- 固化 YouTube 字幕抓取 → raw/youtube/ → wiki ingest 流程

## [2026-08-03] ingest | Tau Python Port of Pi

- 建立 entity：tau
- 建立 2 個 source notes（YouTube + GitHub README）

## [2026-08-03] lint | 全域 lint 檢查

- 修正 4 處 visualizations broken links
- 補上 4 個 topic pages frontmatter
- 確認 96 個 wikilink targets 全部正常

## [2026-07-22] ingest | mattpocock/skills tutorial

- ingest 17:16 影片 → source note + entity + concept
- 建立 Skill topic
- 實作多 topic 關聯（topics 多值陣列 + 🛠️ 標記）

## 2026-08-09

- Skills 更名：`copilot` → `chat-with-copilot`、`gy` → `chat-with-gemini`、`gemini-deep-research` → `chat-with-gemini-research`
- 新增 skill：`round-table`（多 AI 圓桌會議，Pi 主持 + subagent 參與者 + Gemini + Copilot）
- 更新 cheerio-skills repo（已 push）
- 更新 wiki：github-copilot.md（skill 引用）、skill topic page（新增本地 skills 區塊 + round-table 設計文件）

---

*此 log 為 append-only。舊紀錄保留在 git 歷史中。*

## [2026-08-09] update | 視覺地圖規則更新：直接寫入 Mermaid code block

- 更新 skills：knowledge-garden-visualmap、knowledge-garden-page-content
- 更新 AGENTS.md：visualizations 規則
- 更新 visualizations/README.md：新增 Mermaid 視覺地圖區塊
- 重點：不再轉換為 SVG/PNG，直接在 Notion 寫入 Mermaid code block
- refs: knowledge-garden-visualmap skill、AGENTS.md

## [2026-08-23] ingest | MCP 官方文件 42 篇 batch ingest + 新舊規範比較

- 動作：批次 ingest `raw/web/2026-08-22-*.md`（42 篇 modelcontextprotocol.io 官方文件，protocol version `2026-07-28`）
- 任務 ID：W-2026-08-079
- **雙模型交叉驗證**（§3.1）：
  - **Round 1**：Pi 主持，Claude（chat-with-claude）與 Gemini（chat-with-gemini）各自對 42 篇文件提出 wiki 結構提案
  - **關鍵欄位比對**：目標頁面 ✅、type ✅、topics ✅、是否推翻既有結論 ✅ → 共識成立
  - **整合決策**：SDKs → source type；Server/Client 各自獨立頁；Registry governance 合併進 registry；Auth extensions 合併進 authorization
  - **結論**：`auto_verified`，無需 Round 2
- **建立**：
  - `wiki/entities/mcp-authorization.md` — OAuth 2.1 授權框架（6 篇 raw）
  - `wiki/entities/mcp-registry.md` — 官方伺服器註冊表（9 篇 raw）
  - `wiki/entities/mcp-extensions.md` — 擴充機制（2 篇 raw）
  - `wiki/entities/mcp-tasks.md` — 長任務擴充（1 篇 raw）
  - `wiki/entities/mcp-apps.md` — 互動式應用程式（2 篇 raw）
  - `wiki/concepts/mcp-servers.md` — 伺服器端開發（4 篇 raw）
  - `wiki/concepts/mcp-clients.md` — 客戶端開發（3 篇 raw）
  - `wiki/concepts/mcp-client-types.md` — 客戶端類型與連線（5 篇 raw）
  - `wiki/concepts/mcp-protocol-versioning.md` — 版本控制（2 篇 raw）
  - `wiki/concepts/mcp-agent-skills-integration.md` — MCP + Agent Skills 整合（1 篇 raw）
  - `wiki/sources/mcp-sdks-and-tooling.md` — SDKs & Tooling（4 篇 raw）
  - `wiki/sources/mcp-official-docs-42.md` — 全部 42 篇分類索引
  - `wiki/topics/mcp-ecosystem.md` — MCP 生態系 topic 導航頁
- **更新**：
  - `wiki/entities/mcp-model-context-protocol.md` — **全面重寫**：新增架構概述、6 原語（3 server + 1 active client + 2 deprecated）、OAuth/Registry/Extensions/Tasks/Apps/Versioning 章節、⚠️ 舊版規範演進比較表
  - `wiki/topics/agent-infrastructure.md` — 加入 MCP Authorization、Registry、Extensions、Servers、Clients、Versioning
  - `wiki/topics/extension-dev.md` — 加入 MCP Extensions、Tasks、Apps、Agent Skills integration
  - `wiki/topics/agent-runtime-implementations.md` — 加入 MCP Apps、Tasks、Client Types
  - `wiki/topics/ai-development-tools.md` — 加入 MCP SDKs、Servers
  - `wiki/topics.md` — 加入 mcp-ecosystem topic
  - `wiki/index.md` — 全量重建
- **新舊規範比較**：位於 `wiki/entities/mcp-model-context-protocol.md` §「⚠️ 舊版頁面規範演進比較」，涵蓋 6 項過期論述的逐項比對
- refs: [[wiki/sources/mcp-official-docs-42|MCP 官方文件彙整]]、[[wiki/entities/mcp-model-context-protocol|MCP 主頁]]
- 完成時間：2026-08-23 17:20 (CST)
