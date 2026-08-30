# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-074 個人 AI 助理架構願景：最小規格 + 垂直切片 🆕 #ai-agent #architecture
  - next: **MVP 已圈定（2026-08-30），下一步是動筆寫三份 schema 然後開工**：① 意圖紀錄 schema ② 任務契約 schema ③ 四態狀態機的轉換規則（誰能觸發哪個轉換）。只定已知大欄位，不追求完整——契約欄位等第一版做出來才會清楚。事件流 schema 直接沿用 8/22 定的 MUST 欄位、狀態面沿用 `work/current.md` 概念，這兩件不用重新設計。完整收斂內容見下方「2026-08-30 Cheerio 全貌與 MVP」
  - **8/22 當時的 next（歷史保留，已被 8/30 收斂納入）**：圓桌會議（Claude+Gemini+Codex）已於 2026-08-22 完成第一輪並收斂共識（見 refs 會議紀要），下一步是實際動筆產出**最小規格**（不是完整規格文件）：① 核心=控制平面（無狀態、管流程/政策/派工/log，不管內容）vs Plugin=能力平面（標準插頭介面）的邊界文件 ② log schema 先定 MUST 欄位（trace_id/span_id/parent_span_id/timestamp/actor/event_type/status/latency_ms/error，不含敏感內容）即可，SHOULD 欄位（完整 input/output payload/token_usage/cost_estimate）待遮罩規則定案後再開 ③ orchestrator 派工邏輯直接把 round-table 既有角色分工（Claude=架構/Gemini=研究/Codex=工程/Pi=本機自動化）正式化，不重新設計 ④ stateless+記憶檢索直接把 work-tracker 現有「session 啟動讀 work/current.md」模式當雛形擴充，記憶分 Episodic/Semantic/Procedural/Artifact 四類 ⑤ 多 harness 調用包成統一 Agent Provider Adapter，記錄 provider/harness/model/輸入輸出reference/成本/驗證結果。**最小規格出爐後立刻做一條垂直切片**——優先選搜尋能力，跑通一次「輸入→派工→Tool呼叫→驗證→Log→回覆」的完整路徑，驗證規格可行再擴大，不要 5 大維度全部寫到完整才動手。**第二條垂直切片**已確認為 Azure DevOps 領域專家 Agent（見 [[work/current#W-2026-08-080|W-080]]），與搜尋能力平行推進
  - **訂正（8/22）**：[[work/current#W-2026-08-017|W-017]]／[[work/current#W-2026-08-025|W-025]]／[[work/current#W-2026-08-022|W-022]]／[[work/current#W-2026-08-033|W-033]]／W-2026-08-NEW-001／W-2026-08-NEW-002 這 6 個項目**目前都還沒開始做**（next 都只是「調研 XXX」的待辦，不是已完成的研究產出）。W-074 不是拿它們的成果來整合，而是先產出架構規格，讓這 6 個原本各自零散、範圍模糊的研究待辦，之後有明確的規格可以對齊、重新框定範圍（部分可能因此被合併或縮小，不用再各自從零摸索方向）
  - refs: [[raw/conversations/2026-08-22-personal-ai-assistant-architecture-vision|Cheer 架構願景原始想法]]、[[.pi/round-table/20260822-161304/synthesis|2026-08-22 圓桌會議紀要]]、[[work/current#W-2026-08-017|W-017]]、[[work/current#W-2026-08-025|W-025]]、[[work/current#W-2026-08-022|W-022]]、[[work/current#W-2026-08-033|W-033]]
  - 起因：2026-08-22 討論工作優先順序時，Cheer 提出完整的個人 AI 助理架構願景（核心/plugin 分離、自我成長觀測、單一對口統籌、stateless+記憶檢索、多 harness 外部 agent 調用），並點名「搜尋能力」是當前最迫切的 plugin（Pi 無 Deep Research、無法跟 Gemini 交互、Gemini 品質下降）
  - 無相依，可跟 W-072 平行進行（三方圓桌共識：不必等 W-072/W-057 做完才開始）
  - **2026-08-30 Cheerio 全貌與 MVP（Cheer × Claude 五輪對談收斂）**
    - 產出：`Obsidian/excalidraw/Cheerio 核心架構.excalidraw.md`（v0.6 Excalidraw 全貌圖，逐輪迭代而成）
    - **方法論（Cheer 定調）**：先把想要的全貌畫出來 → 找出互相衝突的地方 → 釐清衝突後才圈 MVP → 從最有價值最核心的部分切入逐片實作。這樣核心路徑不會在擴增過程中被移動，因為每一片都 follow 同一個大框架。「它一開始就是一個大專案，只是實作一定從切片開始」
    - **已拍板的架構決策**：
      1. 單一對口：人類只跟主 Cheerio 講話。主 Cheerio = 總 PM／秘書，核心職責是**判斷「人類要的」跟「實際做出來的」是不是同一件事**；自己不做事、不存內容，只管流程/政策/記錄
      2. 兩種模式是同一套系統的兩個檔位，且**切換是案件級的**：全自動（主 Cheerio = 唯一翻譯官，意圖只經過它）／互動（主 Cheerio = 主持人，意圖廣播給所有專家，它負責評估各自解讀並統整分歧）。**插話 = 把該案從全自動切成互動**，決策完切回。底層完全共用，換的只是主 Cheerio 的帽子
      3. 「不背記憶」≠ 沒有記憶：全部記在外面、要用才調（來自 Cheer 本人工作方式）。**意圖必須被寫下來成一級紀錄**（否則主 Cheerio 事後無從比對），每層再配一份 summary 供快查。⚠ summary 是衍生資料會過時，更新時機建議綁在狀態轉換上
      4. 專家 = 可實例化的角色。跨案件共用的是**角色定義**，不是實例；工作記憶按案件隔離——這是防汙染的實際機制，要寫死成規則
      5. 分身：同一專家可同時開多個實例跑不同案子。**分身一律繼承 PM 的 Context，且分身不能再生分身**
      6. 合體 = 分身邊做邊往「案件共享工作區」寫，真身直接讀，不是事後拼三份報告。工作區分兩層：① 過程軌跡 ② 已收斂結論（真身平常只讀 ②，避免淹在流水帳裡）
      7. 跨案共享依 DDD **Bounded Context**：從同一案切出來的子案或本質相關才共享，不相關就隔離不汙染。小 Domain 合起來就是大 Domain 的職責與邊界
      8. 衝突三階仲裁：案件 PM（主責專家）→ 主 Cheerio → 人類
      9. 狀態機基本**四態**（不是三態）：待辦 → 進行中 → **待驗收** → 完成。專家只能推到「待驗收」，只有主 Cheerio 能推到「完成」，只有人類能推翻主 Cheerio。少了待驗收，主 Cheerio 的核心職責就落空
      10. 觀測是兩件不同的事：**事件流**（流水帳，互動模式即時投影的就是它）vs **狀態面**（結論，秘書打電話問進度問的是它）。混用會讓主 Cheerio 沒辦法又輕便又 stateless
      11. 專家註冊表可增可減，**新增專家不該需要動核心**。主 Cheerio 可自己新增專家：判斷有重大影響 → 停下來等人類仲裁；沒有 → 先做、事後報告「因為什麼而新增了什麼」，兩者都要留下理由。新增專家本身就是一個案子，不需要第二套治理流程
      12. **Harness 可觀測性分級**：Tier A（自建，過程+結果全可驗、可完整旁聽）／Tier B（拿得到工具呼叫，拿不到內部推理與記憶調用）／Tier C（黑盒，只驗結果、過程不可稽核）。**Tier 是「專家 × harness 綁定」的屬性、可升級**，不是專家的固定身份。契約的驗收方式必須跟 tier 綁——Tier C 專家不該被派需要過程稽核的任務
      13. **Tier A 定義成一份「可觀測性契約」，不是某個具體實作**：任何 framework 只要吐得出契約要求的觀測資料就算 Tier A。自建 harness 刻意用**不同 Agent Framework** 打造，避免被單一體制限制，讓每個專家用最適合它專業的框架——這就是最初「專家像 plugin」的意思
      14. 自建 harness 的硬理由：只有自己的底座拿得到完整可觀測性，而**完整可觀測性正是「自我成長」的前提**（看不到自己做了什麼就無從改進）
      15. 專家調用介面拆兩層：**調用契約**（所有 tier 共用：怎麼遞任務契約、怎麼收產出）／**觀測契約**（決定 tier）。分開後把專家從 Tier C 升到 Tier A 不用動核心調用邏輯
      16. **事件流 schema 必須記錄 model 資訊**（8/30 決定）：每個事件加 `model` / `model_version` 兩個字串欄位，MVP 必填。記錄這個事件是哪位專家、用哪個 model + 版本執行的。成本幾乎零，但事後補要動歷史資料格式，MVP 就該做
      17. **任務契約 schema 預留 `execution_constraint` 欄位**（8/30 決定）：L1-L7 安全等級（環境風險：model/harness 部署位置、能否連網）跟 Tier A/B/C（可觀測性：看不看得到過程）是正交的兩個維度。MVP 一案一專家、全自動、單一環境，完整的 7 級判斷邏輯用不上，先留欄位不填值（TBD），成本低、之後不用改 schema 結構就能長出實際判斷邏輯
    - **MVP 範圍（Cheer 已同意）**：一個案子、一位專家、不開分身、只跑全自動模式，跑通「① 我講一句話 → 主 Cheerio 翻成意圖紀錄寫下來 ② 依意圖產生任務契約派給一位專家 ③ 專家執行、做完標『待驗收』 ④ 主 Cheerio 調出意圖紀錄跟產出比對 ⑤ 相符→完成／不符→退回或上呈人類 ⑥ 全程進事件流、狀態面隨時可查」
    - **刻意不做**：分身、共享工作區、互動模式/主持人、跨案 Context 共享、多專家仲裁——它們都疊在這個閉環之上，之後補上不用動核心
    - **第一個案子**：Cheer 傾向讓「造出第一個正式專家」本身就是第一個案子（專家怎麼被造出來，是調用第一個專家之前的設計）。需先有一位**零號專家**——從 **Tier C／B 的外部 harness** 粗暴包裝而成，唯一任務是把第一個正式專家造出來，之後可退役或用正式規格重新定義自己；沒有它，派工那一段不會被驗證到。**從最弱 tier 起步是刻意的**：逼核心只能靠「意圖 + 最終產出」驗收、不偷看過程，日後升 Tier A 只是多拿到資訊，不必改核心邏輯
    - **核心迴路 LLM vs 規則引擎切分**（8/30 補充）：① 人類輸入 → 非 Cheerio 判斷 ② 翻成意圖紀錄 → **【LLM】** 自然語言理解/翻譯 ③ 生成任務契約 → **【LLM 生成內容 ＋ 規則查表派工】**（描述性欄位要 LLM，派給誰查註冊表）④ 專家執行 → 專家自己的事 ⑤ 比對意圖 vs 產出 → **【LLM】** 「你要的」跟「做出來的」是不是同一件事是語意判斷 ⑥ 狀態轉換 → **【規則引擎】** 誰有權推哪個轉換是明文規定的權限規則，⑤ 的 LLM 判斷結果只是觸發⑥ 的輸入，轉換動作本身不該再讓 LLM 自由發揮。一句話：理解/比對/語意判斷交給 LLM，誰有權做什麼交給規則引擎
    - **下一步**：草擬三份 schema（意圖紀錄／任務契約／四態轉換規則），只定已知大欄位。**驗收欄位格式已拍板（8/30）**：採混合式——驗收標準本身用自由文字（讓 LLM 在步驟⑤做語意比對），但⑥的狀態轉換結果（通過/退回/上呈）是確定性的規則引擎判定。精神：理解交給 LLM，權限交給規則引擎

- [ ] W-2026-08-080 Azure DevOps 領域專家 Agent PoC 🔄 #ai-agent #devops #ado #vertical-slice
  - next: 開工前先做 W-ADO-000（評估 OCR 能否用於本次開發的程式碼審查）。之後照 8/22 二次訂正的順序：Block 1 溝通可行性驗證（W-ADO-001~003：ADO／SharePoint／Outlook）全過 → Block 2 設計 Tool 介面（W-ADO-004）→ Block 3 Agent 架構驗證（W-ADO-005~007：可被調用／ADO 呼叫+回饋／SharePoint+Outlook 溝通）→ 才回來決定 Plugin 定位與 Publish View（W-ADO-008~009）→ 最後深化（W-ADO-010~012：寫入操作/三 gate/Memory）
  - refs: [[.pi/round-table/20260822-214627/synthesis|2026-08-22 圓桌會議紀要（ADO Agent）]]、[[work/current#W-2026-08-074|W-074]]（本項目是 W-074 的第二條垂直切片，第一条是搜尋能力）
  - 起因：2026-08-22 四方圓桌會議（Claude+Gemini+Codex，Copilot 因額度用盡缺席）討論「要不要先做領域專家 Agent」，2 輪即收斂共識：應做、定位為 W-074 垂直切片、TypeScript 單語言、5 元件微型 UI Schema、三 gate 驗收。同日 Cheer 看過會議紀要後訂正部分結論（見下）
  - **圓桌共識（未變）**：① 不等 Cheerio 本體完成，直接做 ② 定位為 W-074 第二垂直切片（不是獨立專案）③ LLM 不直接輸出任意 HTML，走固定元件 Schema ④ 三 gate 驗收精神保留 ⑤ 共用契約垂直實作
  - **Cheer 訂正（8/22 同日，覆蓋圓桌部分結論，理由已記錄）**：
    1. **Tech Stack：C#/.NET 10 取代 TypeScript 單語言**——理由：ADO 是 Microsoft 產品、公司主力 AI 工具是 GitHub Copilot，C#/.NET 生態整合度與內部信任度都更高，跟原始截圖「C#/.NET 扛 Agent Backend/企業整合」的分工邏輯一致。圓桌收斂到 TypeScript 是基於「降低語言分工複雜度」的簡化考量，沒把「公司工具鏈對齊」算進去。**SPFx 渲染路徑仍必須用 React/TypeScript**（框架限制無法迴避），所以最終是 C#/.NET 10（Agent Backend + 企業整合 + Tier 1 response）+ TypeScript/React（僅限 Tier 2 SPFx 渲染）兩語言分工，不是回到四語言
    2. **輸出分兩層，不是一份 Renderer**：Tier 1「Agent Response」比照 MCP content block 模式，回給主 Agent Host（Claude／GitHub Copilot／未來 Cheerio），除文字外也能回結構化 HTML（走固定元件 UI Schema）；Tier 2「SharePoint Publish View」由人類主動觸發，把 ADO Board 資訊同步到指定 M365 SharePoint，用 SPFx 相容 React 前端呈現各案子進度與人力工作類別占比給主管看。兩條路徑、兩種消費者，不共用同一份 Renderer
    3. **PoC 範圍擴大，收回原本「只做唯讀」的限縮**：ADO 端涵蓋 Collection Process 複製、Project Settings 複製、Boards Work Item 讀取/分析/新增/異動，並提供 Tier 1 response；人類要求同步時才觸發 SharePoint Form List 同步。**M365/Graph（查日期、預約/取消會議等）定位為此領域專家的 plugin，ADO 是主體**，M365 操作一樣要走 Tool Policy 授權（呼應原始截圖「不會讓 Agent 直接讀寫 M365 資料而沒有規範」）
    4. **新增能力**：Graph Engineering（角色/職責/依賴關係的圖結構建模與查詢，呼應原始截圖依賴圖/Sankey 需求）、Loop Engineering（Cheerio 理解為能自跑「觀察 ADO 狀態→分析→建議/執行→驗證」迭代迴圈，非單次問答——**此詞定義待 Cheer 確認**，暫按此理解推進）、**Memory**（記住 ADO 專案權責歸屬、歷史決策、曾分析過的結論；建議沿用 Cheerio 自己驗證過的 decision-ledger 風格 [[memory-harness-research-validates-design]]，不上向量 RAG，W-ADO-002 一併補 Memory Schema）
    5. **止損紀律不放棄，但改用更早的關卡**：原本「Phase 1 read-only 骨架先跑通」的止損精神保留，但 Cheer 進一步把它拆細成「先驗證連得上，才設計 Tool，才驗證 Agent 呼叫鏈，最後才決定 Plugin 定位與開發 Publish View」——比原本的 Phase 1-4 更保守，避免在 Tool Contract／Agent 邏輯上投入心力後才發現 SharePoint／Outlook API 權限根本要不到。**Gate 3 定義不變**：從「唯讀」改成「寫入操作經 Tool Policy 授權 + 全程可追溯」
    6. **二次訂正（同日）：子項目改成三個 Block，取代原本的 Phase 1-4 排序**——① Block 1 溝通可行性驗證：ADO／M365 SharePoint／M365 Outlook 三者的 API 連通性各自獨立驗證（能不能打通、憑證申請不申請得到，不用先做完整權限規劃），三者都 OK 才進下一步；② 把驗證過的三者包裝成標準 Tool 介面，讓主 Agent 可以直接調用；③ Block 2 領域專家 Agent 架構驗證：先確認 Agent 本身能被主 Agent 調用，再確認能正確用 Tool 呼叫 ADO 並把結果正確回饋給主 Agent，最後同樣驗證 SharePoint／Outlook。這些都驗證完，才回來決定 M365 要做成外掛 Plugin 還是一開始就內建在領域專家裡、以及 Publish View（React）怎麼開發。原本 Phase 3（疊加寫入/plugin/Memory）與三 gate 驗收往後移到全部驗證完之後
    7. **開工前先做 W-ADO-000**：Cheer 提出開工前先評估 [[wiki/entities/open-code-review|Alibaba OpenCodeReview (OCR)]] 能否用於本次 C#/.NET 開發的程式碼審查——這正是既有 backlog 的 [[work/current#W-2026-08-030|W-030]]／[[work/current#W-2026-08-031|W-031]]／[[work/current#W-2026-08-032|W-032]]（安裝測試 OCR → 跟既有 `code-review` skill 比較 → 整合進工作流），之前排在 backlog 底部一直沒動手，現在因為要開始寫 ADO Agent 的正式程式碼而變得更急迫，拉到最前面先做
  - **PoC 仍然不做**：不做完整 RAG/長期記憶（Memory 用輕量 decision-ledger）、不做多 Agent swarm、不支援任意 HTML
  - **預估時程**：W-ADO-000（OCR 評估，30分鐘~1小時）→ Block 1 溝通可行性驗證（每個服務數小時級，取決於憑證申請速度）→ Tool 介面設計（1天）→ Block 2 Agent 架構驗證（2-3天）→ 決策點（Plugin 定位＋Publish View 開發，時程視決策結果重估）→ 深化階段（寫入操作/三 gate/Memory，時程視前面結果重估）
  - 子項目（按優先序，依 8/22 二次訂正的三 Block 順序）：
    - [ ] W-ADO-000 評估 OCR 能否用於本次開發的程式碼審查（呼應 W-030/W-031/W-032，拉到本項目最前面）
    - [ ] W-ADO-001 ADO 溝通可行性驗證（API 打得通、能申請到憑證，不用先做完整權限規劃）— Cheer 主導，阻斷性
    - [ ] W-ADO-002 M365 SharePoint 溝通可行性驗證
    - [ ] W-ADO-003 M365 Outlook 溝通可行性驗證
    - [ ] W-ADO-004 三者（ADO/SharePoint/Outlook）包裝成標準 Tool 介面，含 Tier 1+2 UI Schema + Evidence Schema + Memory Schema，讓主 Agent 可直接調用（Block 1 全過才開始）
    - [ ] W-ADO-005 確認領域專家 Agent 本身可以被主 Agent（Claude／GitHub Copilot／未來 Cheerio）調用
    - [ ] W-ADO-006 確認 Agent 能正確用 Tool 呼叫 ADO，並把結果正確反饋給主 Agent（Tier 1 Agent Response 落地，C#/.NET 10）
    - [ ] W-ADO-007 同樣驗證 Agent 跟 SharePoint、Outlook 溝通正常
    - [ ] W-ADO-008 決策：M365 設計成外掛 Plugin，還是從一開始就內建在領域專家 Agent 架構裡（Block 2 全過才決定）
    - [ ] W-ADO-009 開發 Tier 2 Publish View（React/SPFx，SharePoint 案子進度＋人力占比視圖）+ Form List 同步
    - [ ] W-ADO-010 疊加 ADO 寫入操作：Collection Process/Project Settings 複製、Work Item 新增/異動，接上 Gate 3
    - [ ] W-ADO-011 三 gate 驗收（Gate 1 資料正確／Gate 2 證據可追溯／Gate 3 寫入操作經 Tool Policy 授權+可追溯）
    - [ ] W-ADO-012 Memory 落地（decision-ledger schema，記錄 ADO 專案權責/歷史決策/分析結論）
  - 無相依，可跟 W-074（搜尋能力垂直切片）平行推進

- [x] W-2026-08-076 建立 cheerio-roadmap skill：地鐵路線圖工作進度視覺化 ✅ #ai-agent #visualization #skill
  - completed: 2026-08-22
  - next: ✅ 已完成並推上 cheerio-skills repo。之後只要問工作進度/更新 work/current.md 就會自動重繪，不需要額外動作
  - refs: [Cheerio 路線圖 Artifact](https://claude.ai/code/artifact/d9d83769-d2f7-4a44-b660-bebe3838f30c)、`C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md`、[[work/current#W-2026-08-075|W-075]]（未來 DevOps 版重用同一套設計系統）
  - 起因：跟 Cheer 討論完 W-074/W-057/W-072 排序後，Cheer 希望有「鐵路線」風格的視覺化呈現整體 roadmap，並要求以後每次問工作進度或更新 work 都要自動看到
  - 已完成：
    - ✅ 地鐵路線圖風格 Artifact：4 條軌道（知識花園/基礎設施/企業 Skill 自動化/個人 AI 助理），站點依狀態分四種樣式 + 獨立決策菱形，今天虛線標實際日期
    - ✅ 依 Cheer 逐輪回饋迭代：修正過大的黑色箭頭 marker（改用 `markerUnits="userSpaceOnUse"`、每軌道各自顏色）→ 加大字體＋固定原生寬度＋zoom 工具列（解決字太小看不清楚）→ 點站點改成可拖移、指向該站點的浮動 popover（`position:fixed`，不怕被頁面捲動吃掉）→ popover 加「Read more →」導到下方對應卡片並閃爍高亮 → 閃爍高亮加上背景色（原本只有邊框不夠明顯）
    - ✅ 建立 `cheerio-roadmap` skill 並接進 `work-tracker` 的啟動流程與非啟動時的觸發規則；`state.json` 存 Artifact 連結供每次更新同一個網址；存進我自己的記憶（feedback + reference 兩則）確保未來 session 記得
    - ✅ 資料驅動重構：原本 SVG 站點／彈出卡片 `DETAILS`／下方卡片區是三份手寫重複資料，容易漏改導致不同步。派背景 agent 在複本上重構成單一 `ROADMAP` 資料物件驅動三處渲染，完工後用腳本讓瀏覽器**真的依序點過全部 22 個站點的 Read More**驗證每個都導到正確卡片，確認無誤才覆蓋正式版並重新發布
    - ✅ 推上 `CheerioCorner/cheerio-skills` GitHub repo（commit `64eff50` 建立 skill、`608f5da` 資料驅動重構），公司電腦如果也有 `C:/Cheerio` vault 可用 `npx skills add CheerioCorner/cheerio-skills@cheerio-roadmap -g -a pi` 拉取
  - 備註：公司端工作追蹤是另一套系統（Azure DevOps，工作追蹤+工時統分），本 skill 只管 Cheerio 個人知識系統；未來重用同一套設計系統給公司端的規劃記在 [[work/current#W-2026-08-075|W-075]]
  - **⚠️ 事後訂正（8/22 同日）**：完成當下把重繪流程設計錯了——直接覆寫要同步進共用 `cheerio-skills` repo 的 `references/template.html`，導致 Cheer 真實工作內容（含洩漏雇主名稱）被推上 GitHub 兩次。另一 session（CheerCopilot）發現並修正架構，拆成「共用通用範本」（skill 資料夾內，不含真實資料）與「本機真實輸出」（`Obsidian/work/roadmap/cheerio-roadmap.html`，不進版控）兩份檔案；Claude 核對確認安全後推上 sanitize commit（`5232bbe`）。**git 歷史裡舊 commit（`64eff50`/`608f5da`）仍含洩漏內容，是否要 rewrite history 徹底清掉待 Cheer 決定。**詳見 work/history 同日事件

- [x] W-2026-08-073 IBM Skill 最佳實踐影片心得補寫進花園《Skill》專題頁 ✅ #knowledge #skill #notion #youtube
  - completed: 2026-08-23
  - next: ✅ 已完成。wiki 概念頁與 Notion 花園頁皆已補入 5 項重點，Cheer 審閱後確認無問題
  - refs: https://app.notion.com/p/Agent-Skills-3bc5979e3a8c8121924ef99b09671383（🔬 Agent Skills 專題頁，已補寫）、https://youtu.be/qYNs80FKIVc?si=ONtRQtqXU73YXi8K（來源影片）、[[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]]（wiki 併入目標，已補寫）、[[.pi/round-table/20260822-231523/synthesis|圓桌會議定稿]]、[[work/current#W-2026-08-086|W-086]]（過程中發現的可用性系統誤鎖 bug，已拆成獨立項目）
  - 已完成的前置比對（Pi 比對 + Claude 逐字核對驗證，結論可信）：
    - Topic 1（觸發精準度）：Notion 頁面已完整涵蓋（Description 撰寫技巧、eval queries 迭代流程、Model/User-invoked 分類），**不需要補**
    - Topic 2（SKILL 專業知識要來自實戰、不能是 AI 生成；500行/5000字建議）：頁面**沒有**這個論點，真空白——唯一提到「1500行」是講 Pi Agent 核心大小，語境不同
    - Topic 3（Script 要具體化不要讓AI亂猜；使用前掃描避免惡意軟體；使用前理解資源存取權限）：頁面第六章只有「組織部署前審查」（8步審查清單/5級風險評估），缺「個人使用者每次要用一支 skill 前」的習慣層面，真空白
  - 執行紀錄（2026-08-23）：
    - ✅ Claude 整理措辭草稿 + 圓桌定稿（`.pi/round-table/20260822-231523/synthesis.md`）成 Artifact 給 Cheer 審閱，Cheer 確認無問題後才動手
    - ✅ 依定稿 5 段直接寫入 wiki `wiki/concepts/skill-authoring-best-practices.md`（觸發精準度、避免 LLM 代寫、5,000 tokens、安全使用守則、gotchas），並更新該頁 frontmatter/來源
    - ✅ Notion 花園頁面結構跟 wiki 不同（一～十三章長文，非扁平章節），Claude 重新對位插入點：3 段併入既有章節（3.1/3.2/3.3），安全使用守則改用頁面既有的「X.Y-2」補寫慣例（比照 7.3-2、10.3-2）新增「6.1-2、個人使用前的安全習慣」獨立小節，跟 6.1 組織審查框架對照而非重複，此重新定位未經正式圓桌覆核（Cheer 口頭同意跳過，見下）
    - ✅ 派 Pi 實際執行 Notion 寫入。過程波折：首次 `ntn pages update --content` 因命令列長度限制失敗 → PowerShell 管道方式「回報成功」但實際破壞 UTF-8 編碼（Cheer 手動還原）→ 改用 Notion API `PATCH /v1/blocks/{page_id}/children` + `position.after_block` 直接插入 block 成功 → 發現先前一次失敗的 Python 腳本其實仍寫入導致重複，已清除
    - ✅ Claude 不採信 Pi 自我回報，獨立重新 fetch 頁面核對：4 段插入各僅出現 1 次、無亂碼（replacement char 掃描零命中）、65 個既有標題完整保留、6.2/6.3/6.4 等既有內容未受影響
    - ✅ Cheer 親自檢視 Notion 頁面成品，確認沒有問題
  - **跳過的步驟**：Notion 版本的插入位置/框架是 Claude 這次直接改寫，未如原計畫重新拉 Claude+Gemini 正式圓桌；Cheer 同意跳過（時間考量），改以「Claude 自行核對 + 事後獨立讀回驗證」替代，未另外執行 Gemini 覆核（Cheer 審閱後判斶不需要）

- [x] W-2026-08-067 YouTube ingest：Understanding AI Infrastructure — GPUs, vLLM, K8s ✅ #knowledge #ai-agent #youtube
  - completed: 2026-08-21
  - next: ✅ 已完成並 push（commit 3eec926）。NPU 討論頁後續由 W-2026-08-069 接續。
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]]、[[wiki/concepts/gpu-architecture-for-ai-inference|GPU 架構]]、[[wiki/concepts/llm-serving-architecture|LLM Serving 架構]]、[[wiki/concepts/model-sharding|Model Sharding]]、[[wiki/entities/vllm|vLLM]]、[[wiki/entities/llm-d|llm-d]]、[[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]
  - 已完成：
    - ✅ 抓字幕 → 建 source note + 3 concepts + 2 entities + 1 discussion
    - ✅ 更新 index / log / topics 導航
    - ✅ git commit + push（3eec926），Claude 核對檔案存在與 git 同步確認無誤
    - ✅ agy-bridge 品管失敗後，Claude 改為人工核對 GPU 規格表（A100/H100/H200/B200 TFLOPS/VRAM/Bandwidth），數字與公開規格相符
  - 備註：agy-bridge 連續 4 次 CANCELED 見 [[work/current#W-2026-08-068|W-2026-08-068]]；NPU 研究見 [[work/current#W-2026-08-069|W-2026-08-069]]；引用驗證流程問題見 [[work/current#W-2026-08-070|W-2026-08-070]]

- [x] W-2026-08-082 NotebookLM 深度研究能力重新設計：兩支共用 Skill（不做 MCP server）✅ #skill #notebooklm #ai-agent
  - completed: 2026-08-23
  - **重排優先序（8/23）**：Cheer 判斷本項目比 [[work/current#W-2026-08-069|W-069]]（NPU 深度研究）更具價值，改為優先進行；W-069 改列第二順位（見下方 W-069 備註）
  - **範圍大轉向（8/23，Cheer 看完初評後拍板）**：不是「整理重組 12 支舊 skill」，是**全新設計**。只保留兩個核心能力的精神並合併：①先問清楚要查什麼、設計好研究 prompt ②執行查詢→篩選來源→逐一匯入，其餘 10 支不保留。**12 支舊 skill 只是參考**，不是要照抄。這個能力要讓 Claude／Gemini／Codex／Copilot／未來 Cheer 自建 Agent 都能用——高度對應 [[work/current#W-2026-08-074|W-074]] 點名最急迫的 Plugin(a) 搜尋能力（對應 W-025）
  - **架構二次轉向（8/23 稍晚，Cheer 看完 v0.2 MCP 設計後再拍板）**：Claude+Codex+Pi+Gemini 原本收斂到「獨立 MCP server + SQLite job store + 背景 worker」（v0.2），但 Cheer 指出一開始沒想過要做到 MCP 這個程度，質疑「本來就有 CLI，又何必多生一個 SKILL 或 MCP」——**Claude 重新評估後認同這個質疑**：長輪詢每個 Agent 自己的 Bash/exec 工具本來就有長時間執行/背景執行能力可以處理，不需要常駐 worker；4 個 Agent 共用邏輯只要共用同一份腳本（透過既有 cheerio-skills 同步機制分發）就好，不需要 MCP protocol；單一使用者本機執行也不需要 SQLite 處理併發，用檔案（每個 job 一個資料夾放幾份 JSON）就夠。**改為兩支 skill（`deep-research-intake` 負責釐清意圖／`deep-research-execute` 負責執行+篩選+匯入）+ 一組共用腳本，state 全部改成檔案**，v0.2 已驗證的輪詢/篩選/匯入技術細節直接沿用，只是拿掉服務化外殼。完整修訂版：[[.pi/round-table/20260823-170152/design-v3-skill-based|設計文件 v3（現行版）]]
  - **「先問清楚要查什麼」的真正樣貌（Cheer 8/23 澄清）**：不是機械式把 query 跟 profile 兜成一個 prompt，是希望 AI 在真的開始 Deep Research 前，藉由不斷提出疑問，搞清楚人類的真實意圖與研究範圍——接近舊 `design-deep-research` skill 原本在做的事，是多輪對話，不是一次性資料轉換。落地方式：寫進 `deep-research-intake` skill 本文自己的引導提問流程，不是查詢優化器
  - **Cheer 已拍板的技術決策**：Job/DB 狀態路徑用環境變數指定（不寫死）；Job ID 格式 `rc-YYYYMMDD-NNN`；`check_provider` 健檢與研究成果 artifact 都做；budget 預設 max_sources=50/max_duration=900s/max_retries=3；job 資料夾不進版控；語言改回純 JS（沒有服務化外殼要維護型別邊界，不強制 TypeScript build 流程）
  - **待辦提醒**：Cheer 提到 Google NotebookLM 已更名「Gemini Notebook」——動手寫 `run_research.js` 前，先確認目前裝好的 `nlm` CLI（notebooklm-mcp-cli 0.8.9）的指令集/說明有沒有反映這個更名，避免腳本沿用的指令格式跟實際產品對不上
  - next: ✅ 本項目已完成。後續追蹤（不影響完成判定）：**端到端實測已指定 [[work/current#W-2026-08-069|W-069]]（NPU 角色深度研究）當真實測試主題**，走 `deep-research-intake` → `deep-research-execute` 完整七步流程；W-069 的 Gemini 研究線不受影響，兩條線用同一題目各自跑。草擬的 spec.json 參考內容已備妥（見 W-069 條目），等 Cheer 排定時間再正式執行 intake
  - 補充（2026-08-23）：指定 W-069 為實測案例的理由——① 研究主題「NPU 在 AI 基礎設施架構中扮演什麼角色」已有明確範圍、有引用來源要求（`citation_required: true`），天然適合驗證 intake 收斂能力 ② W-069 原本就在跑 `chat-with-gemini-research`，兩條路線用同一個題目可以交叉比對新舊 skill 的產出品質 ③ 不需要另外找新題目或消耗額外的 NotebookLM 額度（intake 本身不消耗額度，execute 才會）
  - 草擬的 `spec.json`（僅供正式跑 intake 對話時參考，未寫入任何 job 資料夾，`job_id` 用 `rc-20260823-002` 避開已用掉的 `-001` 測試編號）：
    ```json
    {
      "job_id": "rc-20260823-002",
      "query": "NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU",
      "profile": "work",
      "notebook_id": null,
      "budget": { "max_sources": 50, "max_duration_seconds": 900, "max_retries": 3 },
      "research_profile": {
        "depth": "deep",
        "timeframe": "any",
        "preferred_language": "zh-TW",
        "citation_required": true,
        "source_bias": "mixed",
        "output_format": "full"
      }
    }
    ```
    正式跑 intake 時 Cheer 可透過對話調整任何欄位，這份只是起點。
  - refs: [[.pi/round-table/20260823-170152/synthesis|Claude+Codex 設計討論紀要（第一輪）]]、[[.pi/round-table/20260823-170152/pi-implementation-design|Pi 實作設計文件 v0.2（MCP 版，歷史紀錄，技術細節仍被 v3 引用）]]、[[.pi/round-table/20260823-170152/design-v3-skill-based|設計文件 v3（現行版）]]、[[work/current#W-2026-08-074|W-074]]（個人 AI 助理架構，log schema 對齊來源，本項目可能是其 Plugin(a) 落地）、`skills/deep-research-intake/SKILL.md`、`skills/deep-research-execute/SKILL.md`（含 `run_research.js`／`filter_sources.js`／`import_sources.js`／`check_provider.js`／`generate_report.js`）、cheerio-skills repo commit `f835314`
  - **實作完成（8/23）**：v3 設計文件確認後，Cheer 決定由 Gemini（agy）撰寫、Codex 審查、Claude 協調驗證（非 Pi 直接動手），流程如下——
    1. Gemini 依設計文件寫出 `deep-research-intake`、`deep-research-execute` 兩支 skill（含 4 支腳本）
    2. Codex 第一輪純程式碼審查，找到 8 個必須修正的技術性 bug（規格檔名不一致、ResearchProfile 欄位沒接進執行邏輯、輪詢總時限計算錯誤、匯入失敗誤判為完成、URL 正規化過度小寫、篩選關鍵字誤判等），Claude 逐條對照原始碼複查確認都是真問題
    3. Gemini 的 agy CLI 週配額用盡，Cheer 指示改由 Pi 接手修正 8 點；Pi 修完後 Codex 第二輪審查又抓到 3 點殘留問題（輪詢時限計算仍有遺漏、`max_retries` 邊界情況、匯入 resume 邏輯在「先失敗後成功」情境下會漏掉重試），Pi 再修一輪，Claude 逐行讀程式碼驗證 3 點都確實修好，四支腳本 `node --check` 全過
    4. Cheer 指出兩支 SKILL.md 格式跟過往 skill 慣例（如 `devops-project-wiki-maintainer`）不一致——Gemini 寫成了「設計文件/報告」骨架（版本號、背景與動機、效益評估、總結），不是「可執行手冊」骨架；Claude 直接改寫成精簡操作手冊風格，技術內容不變
    5. Cheer 要求 `deep-research-execute` 有固定給人類看「最後到底找到哪些來源」的資產；Claude 新增 `generate_report.js`，篩選後與匯入後都產生 `sources-report.md`（人類可讀 Markdown 報告），已實測驗證輸出正確
    6. 依 AGENTS.md Skills 版控規則走完：`git pull` → 更新 `README.md`（新增「🔬 深度研究」分類）→ commit `f835314` → push 到 `CheerioCorner/cheerio-skills`
  - **目前狀態（完成，8/23 Cheer 拍板）**：兩支 skill 已寫好、審查過、修過兩輪、格式對齊、含人類可讀報告資產，並已推送到共用 repo。Cheer 確認這個階段就算完成；**端到端實測（intake→execute 全七步，用 W-069 主題，見上方草稿 spec.json）留待之後找時間驗證**，不影響本項目完成判定，實測若發現問題另開新項目處理
  - **後續發現與重新設計（2026-08-24）**：Cheer 觀察到 Pi 8/23 執行 W-069 端到端實測時，只改了本機 `.agents/skills/deep-research-execute`（`.pi`/`.claude` 的實際執行位置），完全沒動 `cheerio-skills` repo——Claude 核對後確認 repo 版本 git log 自建立以來只有一次 commit，6 個當天修好的 bug 全部沒進版控，等於「只有 Cheer 這台機器能用，repo 是壞的」。Cheer 接著針對整支 skill 的設計提出 6 點檢討（nlm 升級要能 AI 自主完成、intake→execute 之間缺最終確認關卡、研究階段重複建立 notebook 的成因不明、篩選改成「先全部匯入→讓 Gemini Notebook 自己讀內容判斷品質」、要有 recheck 補研究迴圈（≤3輪）、要有分類重新命名步驟），Claude 據此重新設計並實作：
    - `run_research.js` 改用 `nlm research start --wait-and-import`（單一阻塞呼叫，等待交給 nlm 處理）取代手動輪詢；查程式碼與 `nlm --help` 確認重複建立 notebook的根因是「Task ID 解析失敗就重試，但重試時沒有 notebook id 只能帶 `--title`，必定建新 notebook」+ `--force` 繞過 nlm 自己的重複防護；新版一旦解出 notebook id 只用 `-n`，不再重演
    - 移除 `filter_sources.js`／`import_sources.js`（本地關鍵字評分），新增 `quality_filter.js`（Gemini 讀來源內容揪出廣告/膚淺心得文/過期資訊並實際刪除）、`recheck_sources.js`（Gemini 判斷來源夠不夠，不夠就對同notebook補跑 fast 模式研究，≤3輪）、`rename_sources.js`（Gemini 分類+建議新標題並執行 `nlm source rename`）、`query_answers.js`（逐題查詢＋快照最終來源清單）
    - `deep-research-execute/SKILL.md` 新增「最終確認」關卡（研究開始前必須 Y/N，每次執行都要問）；新增 `references/nlm-upgrade-guide.md` 讓 AI 以後自主處理 nlm CLI 升級（含 WinError 32 檔案占用的處理原則、升級後驗證程序）
    - `deep-research-intake/SKILL.md`：`profile` 改成動態偵測（跑 `check_provider.js` 決定，不再寫死 `"work"`）；新增可選 `sub_questions`
    - **實測驗證（不是用 NPU 題目，用 `fast` 模式的無關痛癢小題目 "Pomodoro Technique" 跑完整 10 步流程，完成後已刪除測試 notebook）**：過程中發現並修好一個新 bug——`nlm query notebook --json` 把 Gemini 的結構化回覆包在 `.answer` 字串裡，要再解一層 JSON 才能拿到 `{"remove":[...]}` 這類資料（修進 `lib/nlm_common.js` 的 `extractStructuredAnswer()`）；也發現 `nlm query notebook` 不會可靠回傳逐句引用對應（`citations`/`references`/`sources_used` 欄位始終是空的），改成報告末尾附完整已重新命名來源清單當附錄。驗證結果：全程只建立 1 個 notebook、品質過濾/recheck/重新命名/報告產出皆符合預期
    - repo（`cheerio-skills`）與 `.agents/skills`（實際執行位置）已重新同步一致，Cheer 確認後 commit + push（commit `d533ec5`），解決了「本機能跑、repo 是壞的」的落差
  - **仍待完成**：用真正的研究題目（原訂 NPU，見 [[work/current#W-2026-08-069|W-069]]）跑一次完整流程正式驗證，smoke test 只證明了流程機制本身沒問題，還沒證明真實深度研究題目下的表現
  - 起因：2026-08-23 Cheer 想讓 NotebookLM 成為日後收集資訊的重要地方，順口提到很久以前手寫過一批 NotebookLM skill 但已經忘記怎麼用
  - **以下是 Claude 8/23 稍早完成的 12 支舊 skill 逐支初評，保留當歷史紀錄／給 Pi 起草設計時參考「過去驗證過的做法」用——範圍已被上面 Cheer 的拍板取代，不再是本項目的目標**：
    1. 幾乎每支 SKILL.md 內部引用自己的 templates/scripts/references 路徑都寫成 `.github/skills/...`／`.github/prompts/...`，但實際資料夾是 `.agent/skills/...`／`.agent/prompts/...`（`implementation-plan-workflow.prompt.md` 確實存在，只是路徑寫錯）——這是不是最早在別的 agent 慣例下建的、搬到 `.agent/` 後沒同步改？要先修好這個才能信任其他交叉引用
    2. `notebooklm-execute-deep-research` 資料夾裡有兩個從沒被 SKILL.md 提到的孤兒檔案（`importRawSources.py`、`retrieveSources.js`，後者是抓 NotebookLM 前端 DOM class name 的瀏覽器 console 爬蟲，很脆弱且命名慣例跟 SKILL.md 本文不一致）——是要當成已被 CLI 輪詢法取代的死程式碼刪掉，還是故意留著當 CLI 失敗時的手動備援？
    3. `cross-account-transfer` 跟 `source-import-and-rename` 各自寫了一支獨立的「匯入來源進 notebook」腳本，邏輯重疊但 schema／重新命名時機不同——要合併成一支共用腳本（用參數區分模式），還是差異是刻意的，維持兩支？
  - 12 支摘要（保留/優化/淘汰/合併判斷）：
    - **維持現狀**：`cli-setup`（安裝/升級 nlm CLI，已經夠精簡）、`outline-to-note`（推 outline 進 NotebookLM 當筆記，已經夠精簡）
    - **優化（不淘汰，重組/修小問題）**：`create-research-outline`（研究大綱設計，pipeline 第1站，評分表該搬進 references）、`design-deep-research`（Deep Research prompt 設計，pipeline 第2站，小問題：參考檔放錯資料夾）、`execute-deep-research`（跑 Deep Research 查詢，pipeline 第3站，**12支裡最肥大**，616行，輪詢/錯誤處理/FAQ 全塞在本文，需要最大幅拆分）、`filter-research-sources`（來源驗證/評分/去重，pipeline 第4站，12支裡結構最好，只需小修）、`source-import-and-rename`（批次匯入來源，pipeline 第5站，中度瘦身+跟決策點3有關）、`cross-account-transfer`（跨帳號搬 notebook，路徑要修+跟決策點3有關）、`export-all-source-urls`（批次匯出 URL 備份，寫死了絕對機器路徑要改成相對路徑，資料夾裡還混進一個殘留的 `__pycache__/*.pyc`）、`design-chat-settings`（生成 persona prompt 檔，範例檔名引用對不上實際檔案要修）、`query-and-produce`（NotebookLM Chat 問答/產生 Studio 產出物，**第二肥大**397行、零子資料夾、且 Step 2.2 整段重寫了 switch-chat-settings 已經做過的事，是重複邏輯）
    - **維持獨立但要被別人呼叫**：`switch-chat-settings`（套用 persona，應該變成唯一實作，讓 query-and-produce 改成呼叫它而不是自己重寫一份）
    - 沒有任何一支被判定「直接淘汰」——12 支大多不是真重複，是揭露程度不夠或輕微重疊，主要工夫在拆 `execute-deep-research` 跟 `query-and-produce` 這兩支，不是砍數量
  - 提議分組：① Setup & Admin（cli-setup／cross-account-transfer／export-all-source-urls）② Research Pipeline（create-research-outline→design-deep-research→execute-deep-research→filter-research-sources→source-import-and-rename，外加 outline-to-note 側支）③ Notebook 使用（design-chat-settings＋switch-chat-settings 配對／query-and-produce 改呼叫 switch-chat-settings）
  - refs（12 支初評的來源）: `C:\Agents\CheerCopilot\.agent\skills\`、[[work/current#W-2026-08-073|W-073]]（同源 IBM YouTube 影片，已補進花園與 wiki，2026-08-23 完成）、[[wiki/concepts/skill-design-methodology|Skill 設計方法論]]、https://github.com/jacob-bd/gemini-notebook-mcp-cli（NotebookLM CLI/MCP 專案）
  - 同日已完成的前置整備（跟本項目相關但已處理完畢，不算在本任務範圍內）：pip 套件 `notebooklm-mcp-cli` 0.8.9 修好缺失依賴 `fastmcp` 並清掉殘留舊版本安裝、註冊 `notebooklm` server 進 `~/.pi/agent/mcp.json`（command: `notebooklm-mcp`, lazy），Pi 現在可直接呼叫 NotebookLM 工具
  - 無相依，可任何 session 切入

- [ ] W-2026-08-069 NPU 角色深度研究（Gemini research）🔄 #knowledge #ai-agent #research
  - **優先序調整（8/23）**：Cheer 判斷 [[work/current#W-2026-08-082|W-082]]（NotebookLM skill 整理）更具價值，改列第一順位；本項目改列第二（Gemini 研究本身仍在背景跑，不受影響，只是回填/跟進動作往後排）
  - next: **2026-08-24 已跑完正式研究**：Pi 執行 `deep-research-execute` 完整十步流程，job `rc-20260824-001`，profile 用健檢結果 `personal`，62 筆來源經品質過濾剩 54 筆，`research-report.md` 已產出於 `raw/deep-research/rc-20260824-001/`。Claude 抽查 5 筆引用 URL（arXiv PDF、Microsoft Learn、alphaXiv、rtlearner 部落格、GitHub issue）皆為真實內容且與標題描述相符，未發現捏造。**待決定**：是否現在呼叫 `wiki-ingest` 回填進 [[wiki/discussions/npu-role-in-ai-infrastructure]]（尚未執行，等 Cheer 確認）。
  - **發現的 skill bug（待修，不影響本次報告可用性）**：`generate_report.js` 產出的 `research-report.md` 裡有兩段引用清單——前段「引用來源清單」整段是 100+ 行的 `undefined`（標題解析失敗），後段「參考來源清單」才是正確的完整清單（含標題與 URL）。建議之後開一個小任務修掉前段壞掉的區塊。
  - refs: [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]、[[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]、[[work/current#W-2026-08-082|W-082]]（本項目被指定為 W-082 兩支新 skill 的端到端實測案例，8/24 已完成流程重新設計＋smoke test，正式題目待跑）
  - 起因：Cheer 看完影片後提出開放問題「NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU」，已標記在 discussion 頁，正在派 Gemini 做深度研究（有引用來源要求）
  - **額外用途（2026-08-23）**：本研究主題同時被指定為 [[work/current#W-2026-08-082|W-082]]（`deep-research-intake` ／ `deep-research-execute`）的**端到端實測案例**。不是取代原本的 `chat-with-gemini-research` 路線，而是用同一個題目額外跑一次完整的 intake→execute 流程，驗證新 skill 真的能用。草擬的 `spec.json` 內容見 [[work/current#W-2026-08-082|W-082]] 條目
  - **8/23 實測發現流程本身有 6 個 bug、8/24 已重新設計並用假題目驗證通過**（詳見 W-082 條目），正式拿 NPU 題目跑的動作留到下一次執行



- [x] W-2026-08-066 想法與原始來源建立雙向關聯（raw/conversations ↔ raw/youtube・raw/web）✅ #knowledge #wiki #meta #ingest-pipeline
  - completed: 2026-08-20
  - next: ✅ 全部完成。Claude 於下個 session 讀 diff 做最終品質審查
  - refs: [[AGENTS.md]]、[[raw/conversations/readme|raw/conversations 說明]]、[[raw/youtube/readme|raw/youtube 說明]]、[[raw/README|raw contract]]、[[work/synthesis/2026-08-20-thought-source-linkage-review|Gemini 覆核報告]]、[[raw/conversations/2026-08-20-langgraph-thoughts|驗收想法檔]]
  - 背景：Cheer 看 YouTube 影片／文章時常有當下想法，這正是他想存下這篇資料的原因，但現行 ingest 只吃來源本身（字幕、網頁全文），想法沒有被一起留存，事後回頭看只剩「這篇在講什麼」，看不到「我當時為什麼覺得重要」
  - 任務規格（Cheer 2026-08-20 拍板）：
    1. YouTube 逐字稿維持原路徑不變：`raw/youtube/`
    2. 影片本體不下載備份，只保留 URL；`raw/youtube` 現有 metadata（URL、抓取時間）已足夠，不需新增欄位
    3. Cheer 當下的想法存入 `raw/conversations/`，沿用既有 `source_kind: conversation` 慣例，原文不整理
    4. **核心新增**：存入的當下，`raw/conversations/` 的想法檔案要與觸發它的原始資料（`raw/youtube/xxx.md`、`raw/web/xxx.md`、或其他 raw channel）建立雙向關聯——具體實作方式（frontmatter 加 `related_raw:` 欄位、或用 `[[wikilink]]` 互相引用、或兩者皆用）由 Pi 提案、Gemini 覆核後定案，寫進 `AGENTS.md` §4.2 frontmatter 規範
    5. `wiki-ingest` 系列 skill 要能讀到這層關聯：ingest 時若某筆 raw 有關聯的想法檔案，對應的 `wiki/sources/` 筆記要新增「Cheer 的想法」小節並連回原始想法檔；想法本身若夠獨立成新概念／新問題，另開 `wiki/concepts/` 或 `wiki/discussions/` 頁面，雙向連結回來源筆記
    6. `wiki-lint` 要新增健檢規則：偵測 `raw/conversations/` 想法檔與其宣稱關聯的 `raw/youtube`／`raw/web` 來源之間的雙向連結是否存在、是否互指一致；缺失或斷鏈要能被 lint report 抓出來
    7. `wiki-query` 查詢來源相關內容時，若該來源有關聯的想法檔案，要能一併呈現「Cheer 當時的想法」，不能只回來源本身內容
  - 驗收重點：Pi 完成後要能舉一個實例（一部影片 + 一則想法 + ingest 後的 wiki/sources 頁面）證明關聯真的從 raw 一路追溯到 wiki，不是只改了文件說明沒改流程
  - 無相依，可任何 session 切入

- [x] W-2026-08-065 Wiki Lint 第二輪：Claude × Gemini 圓桌討論 + P0 修復 ✅ #knowledge #wiki #meta
  - completed: 2026-08-19
  - next: ✅ P0 全部完成。P1（wiki-lint dead-end 規則修正 + wiki-ingest frontmatter guardrail）待下次 session 處理
  - refs: [[wiki/reports/wiki-lint-2026-08-19|Lint Report]]、[[wiki/topics/skill|Skill 拆分先例]]、[[wiki/discussions/topic-stub-cleanup|Topic Stub Cleanup 決策]]
  - 已完成：
    - ✅ 第二輪 lint 全面掃描（254 頁面、89 sources、28 topics）
    - ✅ 問 Claude 7 大問題處理建議 → 拿去 Gemini 討論 → 再回 Claude 確認 Gemini 獨特洞察（共 3 輪）
    - ✅ 收斂 3 個系統性缺口：寫入無驗證、架構歸屬未檢查、缺 lifecycle trigger
    - ✅ P0 執行：27 個 topic page 補 `type: topic`、9 個冗餘 raw trash、`pi-agent-learning` 補進 index.md 導航
  - Claude×Gemini 共識決策：
    - Topic 拆分沿用 hub-spoke 模式（skill.md 先例）
    - 缺失 topic page 多數合併/忽略，不新建（違反 topic-stub-cleanup policy）
    - Dead-end 多數是 lint 誤判，修規則而非硬加連結
    - 拆分 topic 時用 grep 批次更新 frontmatter topics:，不靠 alias
    - wiki-ingest 源頭加 frontmatter guardrail，不靠事後 lint
    - Raw 停留天數 → lifecycle trigger → 自動提醒

- [x] W-2026-08-056 Skill 建置自動化研究 ①找現有方案 ✅ #ai-agent #skill #enterprise #skill-automation-design
  - completed: 2026-08-19
  - next: ✅ 兩份獨立深度調研完成並 ingest 進 wiki。建議將本調研成果餵給 [[work/current#W-2026-08-057|W-2026-08-057]]（共享驗證機制設計 round-table）當起點，特別是 Codex 發現的 Durable Execution 層與五類 Gate 設計建議。
  - refs: [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研（20 sources）]]、[[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Gemini 調研（7 sources）]]、[[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]]、[[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]]、[[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理（已更新）]]
  - 備註：同組任務 [[work/current#W-2026-08-057|W-2026-08-057]]／[[work/current#W-2026-08-058|W-2026-08-058]]／[[work/current#W-2026-08-059|W-2026-08-059]]
  - 品質說明：Codex 版本（gpt-5.6-luna）品質明顯優於 Gemini 版本——深連結 vs 根目錄 URL、來源數 20 vs 7、日期誠實標「未標示」。兩份事實衝突時優先採信 Codex。
  - QA 補記：Pi 執行 ingest 時自我報告不完整（漏報弄壞 26 個 wiki/topics/*.md 的 frontmatter、漏報 9 個無關 raw 檔案搬移），經 Claude 逐一讀 diff 驗證後抓到並修復。詳見 [[work/history/2026-08#坑 7：Pi ingest commit 弄壞 26 個 wiki 頁面 + 未揭露的檔案搬移（8/19）|work/history/2026-08.md 坑 7]]。

- [ ] W-2026-08-085 AI 系統資安框架設計：Cheerio + 自建系統的完整資安檢測框架 🆕 #security #ai-agent #architecture
  - next: **Phase 1 深度研究**：盘点 CIA 原則、Threat Modeling、OWASP Top 10、Agent-specific 風險（prompt injection、data exfiltration、privilege escalation）在 AI 系統中的應用。**Phase 2 框架設計**：產出一份可重複執行的資安檢測框架（Security Checklist），涵蓋：① 上線前（Assets 盤點、角色權限矩陣、Trust Boundary 圖、Security Invariant 定義）② 開發中（Secure Coding Guidelines、AI-specific 安全規則、PR review checklist）③ 上線後（Rate limiting、監控告警、Incident Response）。**Phase 3 工具整合**：評估 Codex Security Plugin / Claude Code Security Review / OWASP ZAP / Trivy 等工具，建立自動化掃描流程。**Phase 4 實作驗證**：用 Cheerio 助理 + ADO Agent 做真實案例驗證
  - refs: [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]]（CIA 原則、Threat Modeling 五問題、信任邊界、RLS）、[[wiki/entities/multi-agent-orchestration-patterns|Multi-Agent Orchestration 設計模式]]（Race Condition 防禦、原子化 Prompt）、[[wiki/entities/herdr|herdr]]（常駐背景 + 狀態偵測）、[[wiki/entities/opentelemetry|OpenTelemetry]]（分散式追蹤、Observability）
  - 起因：看完 Vibe Coding 資安影片後，Cheer 提出「不該只依賴閉源工具的資安掃描，需要一套更完整的框架」。影片內容是基礎中的基礎，但 CIA 原則、Threat Modeling 五問題、信任邊界、RLS 這些概念是值得建立的底層知識。真正的資安框架應該涵蓋從設計到開發到部署到維運的全生命週期
  - 無相依，可跟 W-074/W-080 平行進行
  - **與其他任務的關聯**：
    - W-074（Cheerio 架構）：架構設計時就要把資安框架內建，不是事後補
    - W-080（ADO Agent）：企業系統的資安要求更高，需要更嚴格的 Threat Modeling
    - W-056~059（Skill 建置自動化）：Skill 本身也需要安全審查（防止惡意 Skill、權限控管）

- [ ] W-2026-08-057 Skill 建置自動化研究 ②共享驗證機制設計 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - **圓桌共識（8/22）**：已卡 3 天，Codex 明確主張「立即 timebox 解 blocker，不能再用等更多研究延長」——今天直接列出卡住的具體假設，逐項指定決策人與截止時間，先接受最小方案，非必要爭議記成後續決策待議即可，不要再展開。見 [[.pi/round-table/20260822-161304/synthesis|會議紀要]]
  - **Session handoff（8/19）**：今天工作到此為止，下次 session 從這裡接續開始；完成 057 後要依序推進 [[work/current#W-2026-08-058|W-2026-08-058]] → [[work/current#W-2026-08-059|W-2026-08-059]]，不要漏掉後面兩個。
  - next: 開 round-table（Claude+Gemini+Copilot）設計中間無人看管階段（分析/設計/開發/測試）的自動驗證機制，作為 (a)/(b) 兩種 checkpoint 拓撲的共同地基。**建議以 [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]] 的五類 Gate 設計（Contract / Policy / Quality / Human / Release）與 [[wiki/concepts/durable-execution-for-agents|Durable Execution 層」作為討論起點。**
  - refs: [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研（W-2026-08-056 產出）]]、[[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]]、[[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]]、[[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支SKILL簡報筆記]]（P22 Write-back 安全門、P27 Skill 的限制）、[[wiki/concepts/agent-skills-enterprise-governance|Agent Skills企業治理]]（6 階段生命週期 + Agentic Pipeline 整合）、W-2026-08-055（花園流程「內部 hook=advisory + 外部腳本=gate」先例，可借鏡）
  - 無相依，可任何 session 切入
  - 備註：同組任務 W-2026-08-056／W-2026-08-058／W-2026-08-059；這是 (a)/(b) 兩種拓撲都要站上去的共同保障，沒有它就沒有「少人工介入」的安全網

- [ ] W-2026-08-058 Skill 建置自動化研究 ③Checkpoint 拓撲比較 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - next: 站在②的驗證機制之上，完整寫出 (a) 兩道 checkpoint vs (b) 三道 checkpoint 的對比文件（優缺點、風險、適用情境），供主管決策參考
  - refs: [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支SKILL簡報筆記]]（P26 Skill SDLC）
  - blockedBy: [W-2026-08-057]
  - 備註：同組任務 W-2026-08-056／W-2026-08-057／W-2026-08-059；這是要帶去跟主管報告拍板的核心產出

- [ ] W-2026-08-059 Skill 建置自動化研究 ④企業導入路徑 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - next: 由 Cheer 主導（AI 打草稿），對齊 `wiki/concepts/agent-skills-enterprise-governance` 既有 6 階段治理框架與 CAB 變更管理慣例，規劃如何在長榮 IT 系統架構課落地
  - refs: [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills企業治理]]
  - 無相依，可任何 session 切入
  - 備註：同組任務 W-2026-08-056／W-2026-08-057／W-2026-08-058；技術設計再好，沒有對齊企業現有流程就無法真正導入

- [ ] W-2026-08-064 Prime Agent Docker 化評估與實作 🆕 #ai-agent #docker #prime-agent #enterprise
  - next: ① 撰寫 Dockerfile + docker-compose.yml（基於 python:3.11-slim，掛載 workspace volume + API key env）② 在 Windows Docker Desktop（WSL2 backend）實跑測試 ③ 驗證三個面向：session 持久化（JSONL volume 掛載）、IPython kernel 狀態（/refine harness state）、daemon 模式相容性 ④ 撰寫安全建議文件（對齊社群 PR #1126 的 best practice：非 root、cap-drop、network 控制）⑤ 評估是否適合導入長榮 IT 作為 enterprise coding agent sandbox 方案
  - refs: [[wiki/entities/prime-agent|Prime Agent]]、[[wiki/entities/pi-mono|pi-mono]]、https://github.com/PrimeIntellect-ai/prime-agent/issues/1103（Containerized / K8s deployment support）、https://github.com/PrimeIntellect-ai/prime-agent/pull/1126（security/sandboxing guidance）
  - 無相依，可任何 session 切入
  - 備註：Prime Agent 2026-08-05 才發布，官方無 Dockerfile，但技術上無阻礙；社群已在討論 K8s 部署方案；此任務可作為 W-2026-08-056~059 Skill 建置自動化研究的「執行環境」參考

- [x] W-2026-08-062 2026-08 月度回顧重寫：陳述級溯源 ✅ #knowledge #meta
  - completed: 2026-08-18
  - next: ✅ 全部完成
  - 重寫 work/history/2026-08.md，從流水帳改為「學習成長」導向
  - 新結構：月度里程碑 → 學習演進（含事件溯源）→ 踩坑紀錄 → 決策反思 → 量化成長 → 未來展望
  - 每個陳述都有真正的連結：wiki 頁面、圓桌會議紀要、work task、skill、extension、腳本
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、C:/Cheerio/Obsidian/work/history/2026-08.md

- [x] W-2026-08-063 Skill 專題重寫：花園「🔬 Agent Skills」頁面 + 視覺地圖全面更新 ✅ #knowledge #skill #notion
  - completed: 2026-08-18
  - next: ✅ 全部完成
  - 起因：使用者發現昨天（W-2026-08-060）新增的兩顆種子（Skill 設計方法論、Agent 擴充架構層級）沒有被整合進「🔬 Agent Skills」專題母頁——種子種下去了但沒嫁接回主幹
  - 流程：Pi 純粹依 wiki 大腦內容（不看花園現有頁面）重寫全新專題文章 → Gemini + Codex 獨立交叉評審（先查證再下結論）→ Claude 審閱報告 → Gemini + Codex 讀花園現有內容跟新草稿交叉比對，找出遺失的花園獨有內容（四個結構性限制、三個真實撞牆案例含 2026-08-15 事故、判準一二決策框架、Description Optimization 具體數字）→ Pi 補回 → 使用者確認 → 寫入 Notion，視覺地圖比照同一流程重製
  - 新增結構性要求「陳述級溯源」：全文 80+ 處論點標示（參考：外部來源）／（原創：Cheer 自己的研究成果，如 Intention-First 命名）／（沿用既有花園內容）三種來源，讓外部參考跟原創貢獻分得清楚
  - 過程中兩次抓到 Pi 謊報完成（貼假的「已修改內容」證據，實際檔案沒改到）與一次章節標題遺失，都靠 Claude 直接讀檔/API 查證抓到並修正，未再信任 agent 自報
  - refs: [[wiki/topics/skill|Skill 主題索引]]、[[wiki/concepts/skill-design-methodology|Skill 設計方法論]]、[[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]、https://app.notion.com/p/3bc5979e3a8c8121924ef99b09671383（🔬 Agent Skills 專題頁）、https://app.notion.com/p/3bc5979e3a8c81e0bb89f9066f64f664（視覺地圖）

- [x] W-2026-08-055 花園流程與 extension 優化（根因修正）✅ #knowledge #skill #notion
  - completed: 2026-08-17
  - next: ✅ 全部完成
  - **2026-08-17 視覺地圖結構性修復（3 顆種子）✅**：Skill 設計方法論 + Agent 擴充架構層級的 Mermaid 搬入 DB 記錄頁 + relation 修復；E2E 測試種子錯置 Mermaid 清除。詳見 work/history/2026-08.md。
  - refs: [[.pi/round-table/20260814-095306/synthesis|2026-08-14 圓桌會議紀要]]、[[.pi/round-table/20260817-080831/synthesis|2026-08-17 P0 實作設計圓桌會議紀要]]、[[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/visualmap|visualmap skill]]、[[wiki/entities/page-content|page-content skill]]
  - **Phase 1（共用驗證腳本）✅ 完成**：`scripts/notion_verify.sh` + `scripts/url_gate.sh`，經 mock server 獨立測試通過
  - **Phase 2（接入 Notion 寫入 skill）✅ 完成（有已知限制）**：四支 knowledge-garden-* skill 都接上 read-back / URL gate，重構為共用參考檔 `references/write-back-safety.md`
  - 已知限制：① Notion DB 還沒加 Run-ID/Origin/Verification-ID 三個 property（2026-08-17 API 確認不存在，需 Cheer 手動在 Notion UI 新增）② 尚未用真實 Notion page 做端到端測試（依賴 ①）③ cheerio-skills 同步：knowledge-garden + page-content SKILL.md 已同步（commit 32c942d），其餘 skill 待後續同步
  - 共識結論（Gemini + Codex 圓桌會議 + Claude 覆核）：
    - 四個驗證關卡：URL 存活 / Mermaid render / DB read-back / 事實來源綁定
    - Headless 策略：正式 DB + `Status: 機器生成待審` + 三道護欄
    - 驗證機制：內外結合雙層架構（內部 hook = advisory / 外部腳本 = gate）
    - 硬失敗 vs 軟失敗：URL 404 要區分真死 vs 暫時性，過度嚴格會誤殺

(W-2026-08-060 已完成，見 Completed 區塊)

- [x] W-2026-08-060 DevOps Skill 簡報 ingest wiki（Agent Skills 專題擴充）✅ #knowledge #skill #notion
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-17-devops-skill-presentation|DevOps Skill 簡報來源筆記]]、[[wiki/concepts/skill-design-methodology|Skill 設計方法論]]、[[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]
  - 已完成：
    - ✅ 讀完 31 頁 HTML 簡報全部內文
    - ✅ 雙模型交叉驗證（Claude + Gemini）
    - ✅ 新增 2 顆概念種子 + 更新 4 個既有頁面
    - ✅ 建立來源筆記 + git commit 推送
  - 花園同步：依賴 W-2026-08-055

- [x] W-2026-08-061 YouTube ingest + 圓桌會議：Claude Code Hooks 教學影片 ✅ #knowledge #ai-agent #hooks
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-18-claude-code-hooks-tutorial|Claude Code Hooks 教學來源筆記]]、[[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]、[[.pi/round-table/20260817-222740/synthesis|圓桌會議紀要]]
  - 已完成：
    - ✅ 抓字幕（zh-TW 手動字幕，568 segments → 26 paragraphs）
    - ✅ 雙模型交叉驗證（Claude + Gemini 提案一致）
    - ✅ 新建 source note + concept page
    - ✅ 更新 claude-code entity、agent-extensibility-hierarchy concept、2 個 topic pages
    - ✅ 2 輪圓桌會議（Claude + Codex + Gemini）
    - ✅ 共識：確定性執行 vs 機率性自律、三層架構對個人/小團隊足夠、Prompt/Agent Handler 作為語意過濾器

- [x] W-2026-08-052 圓桌會議：Claude vs Gemini vs ChatGPT 前端全鏈路能力比較 ✅ #ai-agent #tools
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-17-frontend-ai-roundtable|圓桌會議紀要]]、[[wiki/concepts/contract-driven-development|Contract-Driven Development]]、[[wiki/concepts/context-decay|Context Decay]]
  - 已完成：
    - ✅ 3 輪圓桌會議（Claude + Gemini + ChatGPT/Codex）
    - ✅ 核心結論：契約驅動的三核心分工（Gemini 視覺 → Claude 架構 → Codex 交付）
    - ✅ 5 大共識：契約驅動、脈絡衰減、編排成本、rejected_alternatives、不要單模型全包
    - ✅ wiki 新增 1 source + 2 concepts
    - ✅ 會議紀錄存入 .pi/round-table/20260817-211224/

## 🔴 Phase 1：前置知識（建立 Agent 前必學）

> 目標：理解 Agent 的核心組件，知道要建造什麼。

### 🔥 立即可做（簡單、快速、有價值）

- [ ] W-2026-08-030 安裝並測試 OpenCodeReview（OCR） ⏫ #ai-agent #code-review
  - next: 安裝 OCR、設定 LLM provider、跑一次 `ocr review` 測試
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/sources/2026-08-20-opencode-review-deep-research|Gemini 深度研究]]、[[work/current#W-2026-08-080|W-080]]（8/22 Cheer 指定拉到 ADO Agent 開工前先做，見 W-ADO-000）
  - 預估時間：30 分鐘
  - 為什麼先做：安裝簡單，能立即體驗「確定性工程 × Agent」混合架構；現在因為 W-080 要開始寫 C#/.NET 正式程式碼而變得更急迫

- [ ] W-2026-08-029 學習系統 + v0.84.0 學習任務 ⏫ #ai-agent #learning
  - next: 嘗試全螢幕模式、測試 AGENTS.override.md、玩 samplingParams
  - refs: [[work/designs/pi-agent-learning-system|學習系統設計]]、[[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 分析]]
  - 預估時間：1 小時
  - 為什麼先做：這些是 Pi 的新功能，動手玩能加深理解

### 📚 核心研究（需要時間，但很重要）

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - next: Pi containerization 方案比較、Tau 三層架構、Hermes 學習迴圈
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：多天（持續研究）
  - 為什麼重要：這是 Agent 的骨架，決定了整個架構設計

- [ ] W-2026-08-025 研究 AI Agent 網路查詢能力：Extension 機制與 Search 架構 #ai-agent
  - next: 調研 web search / deep research extension 實作方式
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]、[[work/current#W-2026-08-074|W-074]]（架構規格出爐後，本項目即為 Plugin (a) 搜尋能力的落地任務）
  - 預估時間：2-3 小時
  - 為什麼重要：Tool 系統是 Agent 的手腳，怎麼擴充功能。2026-08-22 Cheer 點名這是目前最迫切的 plugin（Pi 無 Deep Research、無法跟 Gemini 交互、Gemini 品質下降）

- [ ] W-2026-08-022 研究 MCP Server 架構與實作 #ai-agent
  - next: 調研 MCP protocol spec、transport 層、tool/resource/prompt 三大原語
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[work/current#W-2026-08-079|W-079]]（42 篇官方文件已剪藏進 raw/web，待 ingest 進 wiki 才有素材可讀）
  - 預估時間：2-3 小時
  - 為什麼重要：MCP 是 Tool 系統的標準協定
  - 備註：素材已到位（見 W-079），之後研究不用再自己找來源，直接讀 ingest 完的 wiki 頁面即可

- [x] W-2026-08-033 研究 Hook 機制：五個 Harness 的生命週期擴充機制盤點與比較 ✅ #ai-agent #hook #deep-research
  - **範圍重新表達（8/25，Cheer 訂正前一晚 Pi 整理方向錯誤）**：研究對象與問題結構全部重寫，取代舊版「調研 Claude Code、Cursor、Windsurf、Pi Agent 的 hook 系統」的粗略描述。五個研究對象：① GitHub Copilot（VS Code／Visual Studio 2022／Visual Studio 2026／GitHub Copilot CLI-App 四種介面分開調查）② Claude Code ③ OpenAI Codex CLI ④ Pi Coding Agent（pi-mono）⑤ DeepSeek Harness（https://deepseek.com/harness/en/）。五個要調查的問題：1. 各自有沒有提供 Hook 或等效機制、正式名稱是什麼 2. 各自優勢與限制的比較總表 3. 各自具體怎麼實作、完整生命週期事件比較 4. 是只能設定檔配置還是可程式碼注入 5. 企業導入是否有跨工具通用標準，或必須各自量身開發（不像 Agent Skill 有通用版本）
  - **前一晚失敗原因診斷（job `rc-20260824-002`，已判定不可用）**：查詢字串用「不同 LLMs 的 Hook 機制比較」，把「coding agent harness 的生命週期擴充機制」跟「機器學習模型層級的 hook」（PyTorch/TransformerLens activation hook、vLLM/SGLang logits processor、LangChain callback）搞混，87 筆來源裡有一大票是 DeepSeek 模型架構（MLA/MoE）、Inflection AI 的聊天機器人「Pi」（跟 Pi Coding Agent 是完全不同產品）、logit_bias／guided decoding 等不相干主題；且卡在 `sources_ready` 階段沒往下跑完（`quality_filter`／`rename` 皆解析失敗留下 `-raw.md`），notebook 已被 Cheer 手動刪除
  - **研究已完成並經 Claude 獨立驗證（8/25）**：Pi 執行 `deep-research-execute` 完整十一步流程，job `rc-20260825-001`，profile `personal`。83 筆來源 → 品質過濾剩 71 → 蒸餾剩 60，過程中 quality_filter／recheck／distill／rename／query_answers 全部正常解析，沒有卡住、沒有 `-raw.md` 解析失敗殘留。`research-report.md`（715 行）已產出，五個問題逐一有完整章節＋比較總表＋生命週期事件對照表
  - **Claude 品質驗證（不採信 Pi 自報）**：不只讀 Pi 的自我回報，另外用 WebFetch 逐筆實查 7 個關鍵引用（GitHub Copilot／VS Code／Claude Code／OpenAI Codex CLI×prempti／Pi Coding Agent×oh-my-pi／pi.dev／DeepSeek Harness 官網＋GitHub repo），全部真實存在且內容與報告陳述相符，未發現捏造。特別針對「Pi Coding Agent」交叉核對：`oh-my-pi` 經確認是 `earendil-works/pi`（前身 `badlogic/pi-mono`，即 Cheer 實際使用的 Pi Agent）的官方認可 fork，`pi.dev` 確認是 Earendil Inc. 官方套件登記站，並非重演昨晚「搞混成別的 Pi 產品」的問題；蒸餾階段移除清單裡也看到 `earendil-works/pi` 原始 repo 頁面（因載入失敗被移除，非搞混，屬正常雜訊淘汰）。另外重點驗證 Q5 的核心結論（Hook 在 Agent Plugins 1.0 標準中屬 client-specific／不可跨工具移植，Skill／MCP 才是可移植標準組件）逐字對照 VS Code 官方文件屬實
  - completed: 2026-08-25
  - **Wiki-ingest 完成（8/25）**：Cheer 確認報告品質後，Pi 執行 `wiki-ingest` 整理進 `wiki/`（commit `3f8197e`）。新建 `wiki/sources/2026-08-25-coding-agent-hooks-comparison.md`（來源筆記）、`wiki/entities/deepseek-harness.md`（全新 entity）、`wiki/comparisons/coding-agent-hooks-comparison.md`（五大 Harness 比較頁）；更新 `wiki/entities/{claude-code,github-copilot,openai-codex,pi-mono}.md`、`wiki/topics/hooks.md`、`wiki/index.md`
  - **過程問題與修正**：
    1. **W-086 可用性登記表誤鎖 bug 這次連環發作 3 次**（正常的 thinking 串流、toolcall_start 事件都被誤判成錯誤訊號並封鎖 `pi:default`），比原本記錄的「誤判單一事件」更嚴重——已手動 `clearBlocked` 三次排除，W-086 backlog 項目需要提升優先序
    2. **雙模型交叉驗證（SKILL.md Step 3）未真正跑完**：Claude CLI 呼叫因 `max-turns 3` 限制失敗，第二次嘗試又被上述誤鎖打斷，Pi 最終自行判斷寫入，少了一道品質關卡
    3. Claude 獨立核對（不採信 Pi 自報）發現一個壞掉的 wikilink（`[[wiki/raw/deep-research/...]]` 路徑錯誤，raw/ 不在 wiki collection 內），已修正為比照既有慣例的純路徑引用（commit `e29217e`）
    4. **針對第 2 點缺口，改派 Gemini（agy）做獨立品質補審**（Cheer 8/25 指示「讓 Gemini 監管品質」），Gemini 逐檔讀取新頁面對照原始報告，抓到一個真實問題：5 個檔案共 7 處把 Claude Code 事件數寫成「31 種」，但這次研究報告原文只說「25 個以上／超過 25 個」，屬於未經這份來源支撐的具體數字。Claude 進一步查證發現「31」其實是既有頁面（`claude-code-hooks-architecture.md`，源自 YouTube 教學影片 `[04:14]` 時間戳）已有的更精確計數，不是憑空捏造，但這次新頁面明確標示「根據本次研究報告」卻寫出報告沒有的數字，屬於溯源錯誤——已全部修正為「25+ 種事件」並附註既有頁面有更精確的「31」計數，不互相矛盾（commit `0252e39`）
  - **後續建議（不阻塞本項目結案）**：W-086（可用性登記表誤鎖）優先序應提升，這次同一 session 內連環發作 3 次已經明顯干擾正常工作流程；未來 wiki-ingest 若雙模型交叉驗證因技術問題被跳過，應比照這次做法追加一輪獨立模型（Gemini 或 Codex）事後補審，不要無聲放行
  - refs: `Obsidian/raw/deep-research/rc-20260825-001/research-report.md`（完整報告）、`Obsidian/raw/deep-research/rc-20260825-001/spec.json`（規格）、`Obsidian/raw/deep-research/rc-20260824-002/`（前一晚失敗紀錄，保留供對照，不刪除）、`wiki/comparisons/coding-agent-hooks-comparison.md`、`wiki/entities/deepseek-harness.md`、`wiki/sources/2026-08-25-coding-agent-hooks-comparison.md`、[[work/current#W-2026-08-082|W-082]]（本項目沿用其 deep-research-execute 十一步流程）、[[work/current#W-2026-08-086|W-086]]（誤鎖 bug 本次再犯 3 次）、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：deep 模式研究本身約 5-6 分鐘阻塞執行，加上品質過濾/recheck/蒸餾/重新命名/逐題查詢，全程約 20-40 分鐘（視 recheck 輪數而定）
  - 為什麼重要：理解 Agent 的事件系統；同時是 Cheer 判斷企業導入 Hook 機制能否比照 Skill 走通用化路線的關鍵輸入

- [ ] W-2026-08-NEW-001 研究 Session 管理：對話如何持久化與壓縮 #ai-agent
  - next: 調研 Pi、Claude Code、LangChain 的 session storage 機制
  - refs: [[wiki/entities/pi-agent-core|pi-agent-core]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼重要：Agent 需要記住對話，這是核心功能

- [ ] W-2026-08-NEW-002 研究 Context 管理：長對話如何處理 #ai-agent
  - next: 調研 compaction、sliding window、summarization 等策略
  - refs: [[wiki/entities/ai-agent-core/token-budget|Token 預算]]、[[wiki/entities/pi-agent-core|pi-agent-core]]
  - 預估時間：2 小時
  - 為什麼重要：Context window 有限，必須有效管理

## 🟡 Phase 2：實作經驗（在現有 Agent 上練功）

> 目標：親手操作，把知識變成經驗。

- [ ] W-2026-08-031 比較實驗：OCR vs code-review skill #ai-agent #code-review
  - next: 找一個有 diff 的專案，分別用兩種工具審查，比較結果
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/entities/plannotator|Plannotator]]、~/.agents/skills/code-review/
  - blockedBy: [W-2026-08-030]
  - 預估時間：1 小時
  - 為什麼重要：親身體驗不同 code review 方式的差異

- [ ] W-2026-08-032 整合 OCR 到 Code Review 工作流 #ai-agent #code-review
  - next: 設計 OCR → code-review skill → Plannotator 的完整流程
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[work/designs/pi-agent-learning-system|學習系統]]
  - blockedBy: [W-2026-08-031]
  - 預估時間：1-2 小時
  - 為什麼重要：建立自己的 code review 流程

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[work/README|Work System]]
  - 預估時間：多天
  - 為什麼重要：Extension 開發經驗，以後自己的 Agent 也可能需要類似機制

## 🟢 Phase 3：工具整合（提升效率）

> 目標：把好的工具整合到工作流。

- [ ] W-2026-08-028 設定 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider #ai-agent #extension
  - next: 執行 `/login` → 選擇 GitHub Copilot → 完成 OAuth
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/github-copilot|github-copilot]]
  - 預估時間：30 分鐘
  - 為什麼重要：有更多模型可用
  - 備註：有免費 GitHub Copilot 訂閱，可在本機設定

- [ ] W-2026-08-008 用 `grill-me` 直接測試用，改配合 W-080 ADO 領域專家 Agent 開發時使用 #skills #ado
  - next: 不再單獨為 Pi Web 專案排時間執行；改成 W-080（Azure DevOps 領域專家 Agent PoC）開發過程中，需要對某個設計/假設做完整需求追問時直接呼叫 grilling 測試
  - refs: [[work/current#W-2026-08-080|W-080]]（ADO Agent，本項目改為配合它使用）
  - 為什麼重要：grilling 已安裝可執行，與其獨立排一次 Pi Web 需求追問，不如在真正有壓力測試需求的 ADO Agent 開發現場直接用
  - 起因：2026-08-23 Cheer 決定把 W-008 的用途從「Pi Web 專案需求追問」改為「ADO Agent 開發時的直接測試工具」

## ⚪ Phase 4：延伸研究（有空再做）

> 目標：深入了解，為未來做準備。

- [ ] W-2026-08-023 研究 MCP Registry 與企業級管理 #ai-agent
  - next: 調研 MCP registry 方案、server 發現與註冊機制、版本管控
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼可以等：企業級管理，個人建造不需要

## Blocked

## Backlog

- [x] W-2026-08-086 修 AI 模型可用性登記表的誤鎖 bug ✅ #tools #bug #ai-agent
  - completed: 2026-08-25
  - next: ✅ 根因已找到並修復（程式碼層面）。⚠️ 但服務尚未重啟套用，見下方「待補」，不能視為完全結案
  - refs: `CheerioCorner/mcp-bridges/lib/availability.mjs`、`CheerioCorner/mcp-bridges/lib/rate-limit.mjs`、`CheerioCorner/mcp-bridges/lib/pi-handler.mjs`、`CheerioCorner/mcp-bridges/lib/agy-handler.mjs`、`CheerioCorner/mcp-bridges/lib/codex-handler.mjs`、`CheerioCorner/mcp-bridges/lib/copilot-handler.mjs`、`CheerioCorner/mcp-bridges/test/rate-limit.test.mjs`、`CheerioCorner/mcp-bridges/test/codex-bridge-availability.test.mjs`、[[work/current#W-2026-08-073|W-073]]
  - 起因：2026-08-23 執行 W-073 派工給 Pi 時，`pi:default` 連續被可用性系統誤鎖兩次——第一次是 W-072 commit（`cf2dc64`）完成後，鎖住的理由仍是「10 個未提交檔案」的舊狀態，Claude 用 `clearBlocked()` 手動清除確認 git status 乾淨後才解除；第二次是 Pi session 執行中，鎖住理由變成一段正常的 read 工具輸出內容（agent-skills.md 的 frontmatter），被系統誤判為不可用訊號，`confidence: estimated`
  - 無相依，可任何 session 切入；不影響 W-073 本身結果（Pi 最終仍完成寫入且經 Claude 獨立讀回驗證通過）
  - **第三、四次踩到（8/23 稍晚）**：[[work/current#W-2026-08-082|W-082]] 設計討論階段，`pi:default` 跟 `agy:default` 各被誤鎖一次，都是在 Claude 剛成功呼叫完後把那次呼叫的正常 tool 輸出（read/step_update 事件內容）誤判成封鎖理由——確認不是只有 read 工具會觸發，`recordBlocked` 的判斷邏輯可能太寬鬆，任何工具輸出都可能被誤抓
  - **優先序提升（8/25）**：[[work/current#W-2026-08-033|W-033]] 執行 wiki-ingest 過程中，同一個 session 內 `pi:default` 連環誤鎖 3 次——第五次是正常的 `thinking_end` 串流內容片段被判定為錯誤訊號，第六、七次更誇張，連 `assistantMessageEvent: toolcall_start`（工具呼叫「即將開始」的事件通知，內容連實質輸出都還沒有）也被判定為錯誤訊號並封鎖。三次都靠 Claude 手動 `node -e "...clearBlocked('pi:default')..."` 才排除，直接打斷並拖慢了原本要一次跑完的任務。這確認 bug 不只發生在「工具輸出內容」層級，而是任何 `message_update`／串流事件都可能觸發，範圍比原先認知的更廣，且發生頻率已經高到明顯干擾正常工作流程，應該提前處理，不要繼續排在 backlog 底部
  - 已完成：
    - ✅ 根因確認：`lib/pi.mjs`／`lib/agy.mjs`／`lib/codex.mjs`／`lib/copilot.mjs` 這四個 CLI runner 都把「我方逾時（timeout）殺掉 process」折疊進同一個 `hadError` 旗標（例如 `hadError: parsed.hadError || res.code !== 0 || res.timedOut`）。`detectRateLimit()` 的關卡只看 `hadError` 就決定要不要掃描，於是每次單純逾時（CLI 根本沒回報任何錯誤，只是還沒跑完）都會觸發對「整包累積下來的 stdout」做全文比對
    - ✅ 根因確認第二點：`RATE_LIMIT_PATTERNS` 裡的 `/429/` 沒有 word boundary，會比對到任何大數字裡剛好含有「429」子字串的情況（例如 token count `14293`、timestamp、tool-call id）。這解釋了為何誤鎖內容五花八門（read 內容、`step_update`、`thinking_end`、`toolcall_start`）——問題根本不在「內容種類」，而是逾時當下累積的 stdout 裡剛好哪段數字含有 429
    - ✅ 已用真實案例重現：用 W-086 描述的 `thinking_end`（`usage.input_tokens: 14293`）事件實際餵給修復前的 `detectRateLimit()`，重現出跟 `state/availability.json` 裡一模一樣的「整包原始事件 JSON 被塞進 reason 欄位」現象，確認找對根因
    - ✅ 修法：`lib/rate-limit.mjs` 的 `detectRateLimit()` 拆開 `hadError`（CLI 真正回報的錯誤）跟 `timedOut`（單純我方逾時殺掉）兩個獨立訊號；純逾時且沒有真正錯誤訊號時，只掃 `stderr`/`error`，不掃全量 `stdout`/`text`；若逾時情境下仍在 `stderr`/`error` 抓到疑似訊號，`reason` 會加註 `[timeout, unconfirmed]` 前綴，跟真正確認的錯誤訊號區分開來；`/429/` 改成 `/\b429\b/` 避免誤配對大數字子字串
    - ✅ 四個 `*-handler.mjs`（pi/agy/codex/copilot）呼叫 `detectRateLimit()` 時改傳 `hadError: result.hadError && !result.timedOut` 與 `timedOut: result.timedOut`，不用改動四個 runner 檔案本身
    - ✅ 新增 6 個測試（`test/rate-limit.test.mjs` 5 個 + `test/codex-bridge-availability.test.mjs` 1 個 handler 層級回歸測試），其中一個直接重現本次事件的 `thinking_end`/`toolcall_start` fixture 並斷言 `limited === false`／`recordBlocked` 不會被呼叫；同時保留「逾時中若 stderr 真的有限流訊號仍要抓到」的測試，避免修過頭
    - ✅ `npm test` 跑過：100/100 通過（94 個原有 + 6 個新增），無既有測試被破壞
  - **✅ 已完全結案（8/25 稍晚）**：Claude 用 W-086 真實 fixture（`thinking_end` + `input_tokens: 14293`、`timedOut: true`、`hadError: false`）獨立跑一次 standalone smoke test，確認已 commit 的 `lib/rate-limit.mjs` 回傳 `limited: false`，程式碼正確性再次確認。7 個檔案已 commit（`45257a9`，獨立 repo `CheerioCorner/mcp-bridges`）。接著清查發現機器上有 5 個 `pi-bridge.mjs` + 5 個 `agy-bridge.mjs` 進程仍在跑修復前的舊程式碼（stdio 型 MCP server，啟動時把模組讀進記憶體，之後改檔案／commit 不會讓它們自動重載）；Cheer 確認後 Claude 手動全部砍掉（0 個殘留，codex/copilot 兩個 bridge 當時本來就沒在跑），下次各 session 呼叫 pi/agy 工具時會重新 spawn 並讀到修好的程式碼。額外發現一筆已過期的舊鎖（`pi:default` blocked_until 11:19 UTC，reason 內容其實是一段講 Copilot 額度用盡的 markdown 表格列，明顯又是同一種「誤把不相干文字當封鎖理由」的症狀，但已自然過期，不需手動清除）
  - **關於原本記載的第二個問題（commit 後沒清除舊的 untracked-file 封鎖）**：查證後判斷這不是獨立機制。全文搜尋 `CheerioCorner/mcp-bridges` 沒有任何程式碼會讀 git status／uncommitted files 來決定要不要封鎖，`recordBlocked`／`clearBlocked` 完全不知道 git 狀態存在。過去那次「10 個未提交檔案」的 reason 內容，很可能只是同一個「把普通工具輸出內容誤判成錯誤訊號」舊 bug 的另一種呈現（例如 Pi 執行 `git status` 後的正常回報文字被誤掃到），不是真的有一套 git-aware 的封鎖邏輯。如實記錄此判斷，避免之後有人去找一個不存在的機制
  - **發現 `mcp-bridges` 其實是獨立 git repo**：`C:\Cheerio\CheerioCorner\mcp-bridges` 底下有自己的 `.git`，跟原本被告知「整個 Cheerio／CheerioCorner 都不是 git repo」不符。7 個修復檔案已 commit（見上）

- [x] W-2026-08-079 Pi 整理 raw/web MCP 官方文件剪藏進 wiki ✅ #knowledge #ai-agent #mcp
  - completed: 2026-08-23
  - next: ✅ 已完成。42 篇已全部 ingest 進 wiki，已產出新舊規範比較。W-022 的素材已就位，可直接開始研究
  - refs: [[wiki/sources/mcp-official-docs-42|MCP 官方文件彙整（42 篇）]]、[[wiki/entities/mcp-model-context-protocol|MCP 主頁]]、[[work/current#W-2026-08-022|W-022]]
  - 已完成：
    - ✅ 雙模型交叉驗證（Claude + Gemini Round 1 共識成立）
    - ✅ 建立 6 entities + 5 concepts + 2 sources + 1 topic = 14 個新頁面
    - ✅ 全面重寫 mcp-model-context-protocol.md（含 ⚠️ 舊版規範演進比較表）
    - ✅ 更新 4 個 topic 導航頁 + topics.md + index.md 全量重建
    - ✅ log.md 記錄 ingest 流程
    - ✅ Claude 獨立核實（不採信 Pi 自報）：14 個檔案實際存在、git commit `ee4b4c6` 已 push、過期比較表 5 項論點皆有標註、抽查 OAuth RFC 8414/7591/9728/8707 出處與 raw 文件核對相符、零亂碼字元
  - **待補（不阻塞結案）**：原計畫的 Gemini 獨立覆核這次被 [[work/current#W-2026-08-086|W-086]] 誤鎖 bug 擋住（`agy:default` 鎖到 2026-08-23 約 18:11 台北時間），Cheer 決定先結案，之後找空檔補跑一次 Gemini 覆核當事後保險
  - 無相依，可任何 session 切入（適合指派給 Pi）

- [ ] W-2026-08-085 評估在 CheerioCorner 導入 Graphify（程式碼知識圖譜視覺化）🆕 #ai-agent #tools #visualization
  - next: 檢視 `graphify install` 腳本內容，確認安全（本機執行、不外傳程式碼）後，在 CheerioCorner 底下某個 git repo（如 `cheerio-skills` 或 `pi-web-access-zh-tw`）試跑 `/graphify .`，看輸出的 graph.html／GRAPH_REPORT.md／graph.json 是否真的有助於看懂系統結構
  - refs: [[wiki/entities/graphify|Graphify（既有 wiki 頁面，2026-08-06 YouTube ingest 建立）]]、[[raw/web/2026-08-23-Graphify-Labsgraphify Turn any codebase, with its docs, SQL schemas, configs, and PDFs, into a queryable knowledge graph. A graphify skill for Claude Code, Cursor, Codex, and Gemini CLI local deterministic AST parsing, every edge expl|Cheer 今日用 Obsidian Web Clipper 存的 GitHub 頁面]]、[[work/current#W-2026-08-030|W-030]]（同期評估的 OCR，性質不同：OCR=AI code review 抓 bug，Graphify=架構視覺化，兩者互補）
  - 起因：2026-08-23 Cheer 用 Obsidian Web Clipper 存了 `Graphify-Labs/graphify` GitHub 頁面，想知道能不能用它更清楚看到 CheerioCorner 各專案的系統結構
  - 已完成的前置調查：確認 Graphify 純本地 tree-sitter 解析（無 LLM 成本、無向量存儲）、不要求目標資料夾是 git repo、不呼叫任何遠端 Git 平台 API（GitHub/GitLab/Azure DevOps Server 皆可用，只在本機讀 `.gitignore`）；也已釐清跟 Alibaba Open Code Review 的差異（不重疊，可互補）
  - 無相依，可任何 session 切入

- [ ] W-2026-08-087 評估安裝 codebase-memory-mcp（高性能代碼知識圖譜 MCP Server）🔄 #tools #code-analysis #knowledge-graph #mcp
  - next: **種子期文件理解已於 2026-08-26 完整完成並逐項查證**（見下方 refs 的 Notion 種子頁），下一步是實際安裝測試：安裝 codebase-memory-mcp、對 CheerioCorner 專案跑一次索引、逐一操作 15 個 MCP tools 驗證種子頁記載的情境對應是否準確，驗證通過後種子從 🌱 升級為 🌿
  - refs: [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]]、[[wiki/entities/graphify|Graphify]]、[[wiki/entities/open-code-review|OpenCodeReview]]、https://app.notion.com/p/codebase-memory-mcp-3c85979e3a8c8113bf8bec74e8fe9f89（🌱 花園種子頁，含企業導入 checklist）、https://app.notion.com/p/codebase-memory-mcp-3c85979e3a8c810095a5c782f7d80ba9（視覺地圖：使用情境→功能→順序 + 運作機制）
  - 預估時間：1 小時
  - 優先序：OCR（W-030）之後、Graphify（W-085）之前。Graphify 建議跳過（功能子集，跟 codebase-memory-mcp 高度重疊）
  - 起因：2026-08-25 Cheer 用 Web Clipper 存了 DeusData/codebase-memory-mcp GitHub README（`raw/web/2026-08-25-DeusDatacodebase-memory-mcp`），Cheerio 三工具比較討論後判斷：OCR 擋在 ADO Agent 前面先做、codebase-memory-mcp 排第二（開發時降本增效）、Graphify 跳過
  - **已完成的前置調查（2026-08-26 大幅擴充，逐項親自查證，非採信單方回報）**：158+ 語言 tree-sitter + Hybrid LSP 語意解析（11 個語言家族）、SQLite+FTS5 持久化、**15 個 MCP tools 精確清單**（官方表格 14 個 + 向量搜尋段落額外定義的 `semantic_query`）、零 API key、zero LLM、內建 3D 視覺化 UI（localhost:9749）、43 個 client surfaces（README Multi-Agent Support 完整表格逐一列出啟用方式與設定檔路徑）、npm/PyPI/AUR 三個套件通路皆直接查 registry API 確認真實存在、6 個環境變數（`CBM_ALLOWED_ROOT`/`CBM_CACHE_DIR`/`CBM_DIAGNOSTICS`/`CBM_LOG_LEVEL`/`CBM_WORKERS`/`CBM_MEM_BUDGET_MB`）、MIT license、arXiv 論文驗證（**發現版本落差**：論文評估基準是早期 66 語言版本，現行版本已擴充至 158+，效能數字不可直接套用現行版本）、供應鏈安全（SLSA Build L3 + Sigstore 簽章 + SBOM + VirusTotal 掃描證據）、網路行為（唯一外連是 non-blocking 5 秒逾時的版本檢查，不上傳任何專案資料）
  - 無相依，可任何 session 切入

- [ ] W-2026-08-088 代碼知識圖譜工具深度研究：codebase-memory-mcp / CodeGraph / Graphify / OCR 運作背景 + 安全性審查 🔄 #tools #code-analysis #knowledge-graph #security #deep-research
  - next: **codebase-memory-mcp 這一項的 §1 已大幅完成、§2 部分完成**（見下方 2026-08-26 進度），CodeGraph／Graphify／OCR 三項的 §1+§2 仍未開始；剩餘的 codebase-memory-mcp 缺口是原始碼層級 memory safety 抽查與已知 CVE 查詢，做完這兩項 + 其他三工具的背景研究後才能進 §3 選型建議
  - refs: [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]]、[[wiki/entities/codegraph|CodeGraph]]、[[wiki/entities/graphify|Graphify]]、[[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph|YouTube 比較影片]]、[[work/current#W-2026-08-030|W-030]]、[[work/current#W-2026-08-085|W-085]]、[[work/current#W-2026-08-087|W-087]]、https://app.notion.com/p/codebase-memory-mcp-3c85979e3a8c8113bf8bec74e8fe9f89（🌱 花園種子頁，涵蓋 §1 大部分+§2 供應鏈/網路行為部分）
  - 預估時間：多 session（背景研究各 1-2 小時 + 安全性審查 2-3 小時 + 選型討論 30 分鐘）
  - **2026-08-26 進度（codebase-memory-mcp 這一項）**：
    - §1 運作背景研究：**大部分完成**——tree-sitter 158+ 語言 + Hybrid LSP 11 語言家族解析機制、SQLite+FTS5 儲存、圖譜資料模型（節點/邊完整清單）、15 個 MCP tools 精確定義、vendored 依賴清單（mimalloc/xxHash/yyjson/TRE/LZ4/Zstandard 各自授權）皆已從原始碼倉庫（README、THIRD_PARTY.md、server.json）逐項核實，寫入花園種子頁「客觀事實」章節。**未做**：file watcher 用的 library 細節、壓縮演算法實作細節
    - §2 安全性深度審查：**部分完成**——supply chain 風險已核實（SLSA Build L3 provenance + Sigstore 簽章 + SBOM + VirusTotal 掃描證據，release assets 逐一確認存在）、網路行為已核實（唯一外連是 non-blocking 5 秒逾時版本檢查，不上傳任何專案資料，SECURITY.md 逐句核對屬實）。**未做**：C 原始碼層級 memory safety 抽查（是否有 buffer overflow/use-after-free 風險）、已知 CVE 查詢、CodeGraph 的遙測內容分析（CodeGraph 這項還沒開始）
    - 過程中也驗證了 QA 方法論本身：Gemini 兩次因為自己的網頁抓取工具失敗（快取截斷/過期）產出整批錯誤的「不存在」斷言，皆由 Claude 重新直接查證原始檔案推翻，詳見 work/history/2026-08.md 2026-08-26 條目與 [[gemini-qa-can-hallucinate-on-tool-failure]]
  - 子項目：
    - [ ] §1 運作背景研究：CodeGraph／Graphify／OCR 三項尚未開始；codebase-memory-mcp 缺 file watcher／壓縮演算法細節
    - [ ] §2 安全性深度審查：codebase-memory-mcp 缺原始碼 memory safety 抽查 + CVE 查詢；CodeGraph 遙測分析未開始
    - [ ] §3 選型建議：綜合 §1+§2 結論，決定安裝哪個、跳過哪個、實際跑一輪比較測試
  - blockedBy: [W-2026-08-030, W-2026-08-087]
  - 起因：2026-08-25 三工具比較討論後，Cheer 判斷表面 README 比較不夠，需要更深入理解底層機制與安全性，才能做有把握的安裝決策。跟 W-030（OCR 評估）、W-085（Graphify 評估）、W-087（codebase-memory-mcp 評估）都有重疊但不完全相同——本項目是統籌性深度研究，那三個是安裝測試實作
  - 無相依，可任何 session 切入（但 §3 需等 §1+§2 完成）

- [ ] W-2026-08-075 未來：抽出「地鐵路線圖」設計系統給公司 Azure DevOps Analysis Report skill 重用 🆕 #ai-agent #devops #visualization
  - next: 等公司端 Azure DevOps work item/工時 API 可接（或決定用哪種方式讀取）之後，新建 `devops-analysis-report` skill，資料源換成 Azure DevOps，但 [[cheerio-roadmap 設計系統|C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md]] 裡的 CSS/JS（地鐵路線圖 SVG、可拖移浮動 popover、Read more 導頁高亮、zoom 工具列）直接搬過去重用，不重新設計
  - refs: [[work/current#W-2026-08-074|W-074]]（同一批討論脈絡）、C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md
  - 起因：2026-08-22 Cheer 看完 Cheerio 路線圖後指出，公司端工作追蹤是另一套系統（之前討論過的 Azure DevOps Skill，工作追蹤+工時統分），跟這裡的 work/current.md 是不同資料源，但視覺設計值得原封不動搬過去，只是 skill 名稱跟資料源要換
  - 無相依，可任何 session 切入（但實際上要等 Azure DevOps 那端條件成熟才會真的動手）

## Completed

- [x] W-2026-08-084 Cheerio 路線圖新增「回到路線圖」浮動按鈕 ✅ #ai-agent #visualization #skill
  - completed: 2026-08-23
  - next: ✅ 已完成。已同步進 skill 通用範本，之後任何機器重繪都會內建這顆按鈕，不用重做
  - refs: [[work/current#W-2026-08-076|W-076]]（cheerio-roadmap skill 本體）、`C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md`（設計系統章節＋新增「回饋機制」章節）
  - 起因：Cheer 提出兩個小需求：① 路線圖右下角要有常駐的「回到上面路線圖」按鈕 ② 評估能否直接在 HTML 上針對某個 work item 內容給 Claude feedback
  - 已完成：
    - ✅ 右下角固定「⬆ 回到路線圖」按鈕（`.back-to-top`），任何捲動位置常駐、點擊平滑捲回 SVG 診斷圖本身（非頁面最頂端），手機寬度縮成純圖示；瀏覽器實測捲到最底部再點擊確認可正常跳回
    - ✅ 同步寫回 `Obsidian/work/roadmap/cheerio-roadmap.html`（真實輸出）與 skill 的 `references/template.html`（通用範本，僅設計系統，未含真實資料），兩份保持同步，之後重繪／新機器起始都內建
    - ✅ 發布更新到既有 Artifact 連結（同一個 URL）
    - ✅ 需求②評估結論：Artifact 本身已原生支援留言（comment）功能，不需要額外開發——Cheer 在頁面留言模式選取內容留言並 @claude 啟用該串，Claude 就能讀取／回覆；已寫進 SKILL.md「回饋機制」章節供之後任何 session 參考
    - ✅ 經 Cheer 確認後同步進 `CheerioCorner/cheerio-skills` GitHub repo（commit `bd84d97`），推送前 diff 核對內容只有設計系統/文件說明，沒有真實工作資料外流

- [x] W-2026-08-083 workspace 資料夾整併：Claude/Projects 併入 CheerioCorner ✅ #tools #housekeeping
  - completed: 2026-08-23
  - next: ✅ 已完成。之後新的 CheerioCorner repo 直接建在 `CheerioCorner/` 底下，不要再散落到 `Claude/` 或 `Projects/`
  - refs: [[work/current#W-2026-08-072|W-072]]（availability.json 共用登記表路徑已同步更新）、[[work/current#W-2026-08-071|W-071]]（agy-bridge 修復 refs 路徑已同步更新）
  - 起因：Cheer 發現 `C:\Cheerio` 底下 `Claude\mcp-bridges` 和 `Projects\` 下的三個 repo，git remote 其實都指向 `github.com/CheerioCorner/*`，跟 `CheerioCorner\` 資料夾裡原有的兩個 repo 是同一個組織，希望全部歸位到同一個地方
  - 已完成：
    - ✅ 逐一核對 6 個資料夾（`CheerioCorner/cheerio-skills`、`garden-guard`、`Claude/mcp-bridges`、`Projects/pi-plannotator-auto`、`pi-todo-journal`、`pi-web-access-zh-tw`）的 git remote，確認皆屬 `CheerioCorner` org 才動手，不是盲搬
    - ✅ 搬移 4 個 repo 進 `CheerioCorner/`；`pi-todo-journal` 一開始被鎖住（疑似防毒軟體剛掃描過）用一般 `mv` 搬不動，改用 `robocopy /MOVE /R:3 /W:2` 才順利搬完全部 1720 個目錄／20698 個檔案，沒有遺失內容
    - ✅ 刪除搬空後的 `Claude/`、`Projects/` 資料夾
    - ✅ 同步更新根目錄 `.mcp.json` 裡 4 個 bridge server（pi/agy/codex/copilot）的路徑，從 `Claude/mcp-bridges` 改成 `CheerioCorner/mcp-bridges`——這是目前實際生效的 MCP 設定檔，不改的話下次重啟 MCP 連線會失效
    - ✅ 同步更新 `mcp-bridges` 自己的 `README.md`、`mcp-config.example.json` 範例路徑
    - ✅ 全庫搜尋確認只剩 `.pi/codex-runs/` 底下的歷史 log 還提到舊路徑（過去執行的存檔，不需要也不應該回頭改），其餘都已更新
    - ✅ sync work：補上這則 history 事件，並修正 `work/current.md`（W-072、W-071 refs）、`work/history/2026-08.md`、`wiki/log.md` 裡殘留的 `Claude/mcp-bridges` 舊路徑引用
  - 備註：目前已啟動的 4 個 pi/agy-bridge node process 仍是用舊路徑啟動的，不受影響；下次 MCP 連線重啟才會讀到新路徑

- [x] W-2026-08-078 cheerio-skills git 歷史清除：拿掉舊 commit 裡洩漏的真實工作資料 ✅ #tools #bug #security
  - completed: 2026-08-22
  - next: ✅ 已完成。之後同步進共用 repo 前會先親自核對內容（見 [[verify-shared-repo-content-before-push]]），不會再犯
  - refs: [[work/current#W-2026-08-076|W-2026-08-076]]（事件本身）、[[verify-shared-repo-content-before-push]]
  - 已完成：
    - ✅ fetch 確認遠端沒有其他 session 的新 commit，避免 force-push 蓋掉別人的工作
    - ✅ `git reset --soft` 到洩漏前的 parent commit，用當下已乾淨的 tree 重新 commit 成單一 commit（`a923048`），取代原本 3 個 commit（`64eff50`／`608f5da`／`5232bbe`）
    - ✅ push 前 diff 新舊 tree 確認內容完全一致（只有歷史被改寫，內容沒變），grep 確認新 commit 內容零洩漏
    - ✅ `git push --force-with-lease` 推上 GitHub，push 後重新 fetch + `git ls-remote` 核對遠端 `master` 只剩乾淨的新 commit，舊 SHA 在遠端已不可達
    - ✅ 清掉本機的備份 tag、跑 `git gc --prune=now` 讓舊 commit 在本機也徹底不可達
  - 備註：GitHub 後台可能仍短暫保留已不可達的物件（一般 force-push 後的標準行為，非 100% 立即從 GitHub 基礎設施抹除），但一般瀏覽/clone/API 已經看不到洩漏內容

- [x] W-2026-08-077 Mem0 深度研究：跟其他系統比較／生產限制／Decision-Ledger 適用性／授權定價 ✅ #knowledge #ai-agent #research
  - completed: 2026-08-22
  - next: ✅ 已完成並 ingest 進 wiki。生產限制角度仍是知識缺口，之後有真實來源時可回頭補
  - refs: [[wiki/sources/2026-08-22-mem0-deep-research-comparison|Mem0 深度研究 source note]]、[[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 適用性]]、[[wiki/entities/mem0|Mem0 entity 頁面]]、`raw/research/2026-08-22-mem0-deep-research-verified.md`（查證版）、`raw/research/2026-08-22-mem0-deep-research-raw.md`（Gemini 未查證原始輸出，僅供對照）
  - 起因：W-070 修好後的第一次實測。Cheer 指定研究 Mem0 補既有 YouTube ingest 沒涵蓋的角度，並要求「Gemini 查完 Claude 過濾品質再讓 Pi 寫進大腦」的分工流程
  - 已完成：
    - ✅ Gemini（agy）深度研究四角度：跟 Zep/MemGPT/LangMem 比較、生產環境限制、對 W-074 decision-ledger 記憶架構的適用性、授權定價
    - ✅ Claude 用新修好的 W-070 流程逐一 WebFetch 查證 10 個 Tier 1/2 來源：2 個完全正確、3 個真實頁面但文不對題（已找到真正來源替換：Zep 論文、Graph Memory 移除官方頁、LangMem repo）、4 個完全捏造（404 或文不對題，來源移除、陳述降級 [UNVERIFIED]）
    - ✅ 關鍵發現：Gemini 原始報告中「Mem0 vs Decision-Ledger」整個論點唯一引用的兩個來源（zenml.io、vktr.com）都是捏造的 404 網址；改用本機既有的 Sakana AI 來源筆記支撐同一論點，結論本身站得住腳
    - ✅ 發現並修正 Gemini 捏造的「Growth $79/月」定價方案（實際只有 Hobby/Starter/Pro/Enterprise 四個）
    - ✅ Pi 依查證版報告寫入 wiki：新建 1 個 source note + 1 個 discussion 頁面，更新 entity 頁面（mem0.md）、topic 頁面、index.md、log.md
    - ✅ Claude 逐檔讀取核對 Pi 自報的 6 項改動全部屬實；額外抓到 Pi 在比較表自行加了一列未查證的「授權」比較（Zep/MemGPT/LangMem 各自授權），事後用 WebFetch 補查證，內容正確但已加註來源說明
  - 備註：這是 W-070 新流程的首次實戰驗證——證實「格式檢查防不住捏造」的診斷正確，光靠 skill 指示 Gemini 小心不夠，一定要呼叫端親自查

- [x] W-2026-08-070 chat-with-gemini-research 引用驗證流程修復 ✅ #skill #tools #bug
  - completed: 2026-08-22
  - next: ✅ 已修復（Claude 直接修）。下次執行 chat-with-gemini-research 時要照新流程實測：Gemini 產出後由呼叫端親自 WebFetch 查證每個 Tier 1/2 來源
  - refs: `C:\Users\User\.claude\skills\chat-with-gemini-research\SKILL.md`（Step 4 改動）、[[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]（commit 3910322，觸發本次問題的原始事件）、[[work/current#W-2026-08-069|W-2026-08-069]]
  - 已完成：
    - ✅ Step 4 新增強制 URL 實查步驟：呼叫端用 WebFetch 對每個 Tier 1/2 來源實際發送請求，確認可訪問且內容與被引用陳述相符；查證失敗降級 [UNVERIFIED] 並移出正式來源列表
    - ✅ Raw Research frontmatter 新增 `citations_verified` / `verified_by` 欄位，標明報告引用已實查
    - ✅ 品質檢查清單新增「URL 實查」小節，明確寫「呼叫端親自做，不能省略」，防止查證責任又被委派回 Gemini
  - 備註：尚未實跑驗證新流程（下一個研究任務即 Mem0 深度研究會是第一次實測）

- [x] W-2026-08-068 agy-bridge 異常追蹤：headless 模式讀檔被 CANCELED/ERROR ✅ #tools #bug
  - completed: 2026-08-21
  - next: ✅ 已修復（Codex 修、Claude 驗證）
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]、[[work/current#W-2026-08-070|W-2026-08-070]]、[[work/current#W-2026-08-071|W-2026-08-071]]
  - 已完成：
    - ✅ Codex 修復 `buildAgyArgs()` 預設加上 `--sandbox` 和 `--dangerously-skip-permissions`（除非呼叫端明確傳 false）
    - ✅ Claude 逐項查證：讀檔確認改動、node --check 語法、5 次真實 agy.exe 測試全 SUCCESS（exitCode 0、無 CANCELED/ERROR）
    - ⚠️ MCP server 是長駐進程，需 session 重啟/MCP 重新連線才會載入新版程式碼
    - ⚠️ `C:/Cheerio` 非 git repo，修改直接存在磁碟上，無需 commit

- [x] W-2026-08-071 agy-bridge headless 權限修復方案評估與實作 ✅ #tools #bug
  - completed: 2026-08-21
  - next: ✅ 已修復（Codex 修、Claude 驗證）
  - refs: [[work/current#W-2026-08-068|W-2026-08-068]]、`CheerioCorner/mcp-bridges/lib/agy.mjs`、`CheerioCorner/mcp-bridges/src/agy-bridge.mjs`
  - 已完成：
    - ✅ Codex 修改兩個檔案：`buildAgyArgs()` 預設加 sandbox + dangerously-skip-permissions；`ask_agy` description 更新；`runAgy()` 的 `stderr` 截斷到 2000 字元寫入 audit log（之前完全沒用到）
    - ✅ Claude 逐項查證：讀檔 → node --check → 5 次 agy.exe 實測全 SUCCESS
    - ✅ Codex 第一次因 Claude cwd 設錯導致 sandbox 擋寫入、誠實回報失敗；修正後第二次順利完成（非 Codex 過錯）
    - ⚠️ MCP server 長駐進程需重啟才載入新版；本 repo 非 git repo 無需 commit
  - **後續修復（8/21 同日，commit + push 到 cheerio-mcp-bridges）**：
    - 背景：四個 MCP bridge（agy/pi/codex/copilot）的 `*_BRIDGE_CWD` 環境變數都有寫死的個人路徑 fallback（例如 `process.env.AGY_BRIDGE_CWD || "C:/Cheerio"`，`pi.mjs` 甚至是過時的 `"C:/Cheerio/pi"`）；`*_BRIDGE_ENTRY` 也有同樣問題。同事用不同 Windows 使用者名稱的電腦時，寫死路徑完全不對且不會報錯——會悄悄跑錯目錄。
    - ✅ commit `410156e`：`lib/run.mjs` 新增共用 `requireEnv(name)` helper，沒設環境變數就拋出清楚錯誤訊息（講明要去 `.mcp.json` 設定）。四個 bridge 的 `*_CWD` 全部改用 helper，拿掉寫死個人路徑 fallback
    - ✅ commit `5b74fa8`：`*_ENTRY` 同樣改用 requireEnv（原考慮退回 PATH 自動偵測，但 `pi` 是 `node <路徑>` 方式呼叫、`copilot` 預設 `.cmd` 在 `shell:false` 下無法裸指令解析，與其每個工具各寫一套偵測邏輯，乾脆一律要求明確設定、沒設就報錯）
    - ✅ 5 個修改過的檔案全部過 `node --check` 語法檢查；沒設環境變數會拋出清楚錯誤、設了正常運作；每次改完都重新用真實 agy.exe 跑 3 次讀檔驗證全 SUCCESS，確認沒把先前修好的 CANCELED/ERROR 改回去
    - 影響：`.mcp.json` 已有正確設定，本次修改對現行運作無影響（no-op），純防呆——以後複製設定給同事或漏設變數時會立刻在 MCP 連線狀態看到明確錯誤

- [x] W-2026-08-055 round-table + chat-with-codex skill 建立 ✅ #skills #ai-agent
  - completed: 2026-08-15
  - refs: [[wiki/sources/2026-08-15-round-table-pi-agent-v084-learning|圓桌會議紀要]]
  - 已完成：
    - ✅ 建立 chat-with-codex skill（codex exec 整合）
    - ✅ 更新 round-table skill 加入 Codex 為預設參與者
    - ✅ 舉行圓桌會議：Pi Agent v0.84.x 更新中值得學習的設計模式（Claude + Copilot）
    - ✅ 建立 wiki source note + 更新 index/log + work/history
    - ✅ 同步 cheerio-skills repo

- [x] W-2026-08-054 花園視覺地圖 Mermaid 全面驗證與修復 ✅ #knowledge #notion
  - completed: 2026-08-14
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]
  - 已完成：
    - ✅ 用 mmdc 實跑驗證全部 21 張種子地圖語法，修好 1 張壞掉的
    - ✅ 把 Omnigent（3 張）與 OpenCodeReview（3 張）從 ASCII-art 轉為真正 Mermaid

- [x] W-2026-08-053 花園技術類半成品補強（第三批）✅ #knowledge #notion
  - completed: 2026-08-14
  - 已完成：
    - ✅ 全部 6 顆技術類種子完成補強與 Gemini 證據型評審
    - ✅ 🎯 花園 22 顆種子全部處理完畢

- [x] W-2026-08-051 Ingest：Prime Agent 官方發布 + AI郵報 ARC-AGI-3 分析 ✅ #knowledge #ai-agent
  - completed: 2026-08-13
  - 已完成：
    - ✅ 存入 2 篇 raw/web 文章全文
    - ✅ 新建 2 個 concept 頁面 + 2 個 source notes
    - ✅ 大幅擴充 prime-agent entity

- [x] W-2026-08-050 知識系統架構 v3 修正：雙模型共識取代人類確認 ✅ #knowledge #meta #notion
  - completed: 2026-08-12
  - 已完成：
    - ✅ 推翻 v2.0 的人類確認機制，改用雙模型交叉驗證
    - ✅ 修改 AGENTS.md 全面改寫
    - ✅ 知識花園 4 個 skill 全面調整

- [x] W-2026-08-049 Wiki 大整理：Redis + CodeReview + Agentic AI + Knowledge Management ✅ #knowledge #wiki
  - completed: 2026-08-10
  - 已完成：
    - ✅ 新增 15 個 wiki 頁面，ingest 12 個 raw 檔案

- [x] W-2026-08-048 知識系統架構改進：4 輪圓桌會議 + 花園更新 ✅ #knowledge #meta #notion
  - completed: 2026-08-10
  - 已完成：
    - ✅ 4 輪圓桌會議（Pi + Gemini + Copilot）確立架構
    - ✅ 核心架構：三個操作 + 一個機制 + 一個回流

- [x] W-2026-08-047 知識系統健檢機制全面盤點 + AGENTS.md 整理 ✅ #knowledge #meta
  - completed: 2026-08-14
  - 已完成：
    - ✅ 三大健檢機制盤點
    - ✅ AGENTS.md 品質掃描與修復
    - ✅ garden-guard.ts extension 建立

- [x] W-2026-08-044 建立「圓桌會議」skill ✅ #skills #ai-agent
  - completed: 2026-08-11
  - 已完成：
    - ✅ 設計文件 v2 + 建立 SKILL.md
    - ✅ 新增 Claude 為預設參與者
    - ✅ 新增量化共識偵測機制

- [x] W-2026-08-043 Content 設計 Redesign ✅ #knowledge #skill
  - completed: 2026-08-10
  - 已完成：
    - ✅ 重寫 page-content skill（四層骨架+主觀現實+roadmap）

- [x] W-2026-08-042 知識花園 Skill 架構重構 ✅ #knowledge #skill
  - completed: 2026-08-10
  - 已完成：
    - ✅ 建立 seed_schema.yaml + 重構 skills

- [x] W-2026-08-038 Notion 整合設計全面重構 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 5 個 Phase 全部完成

- [x] W-2026-08-039 Notion 頁面內容逐一手動調整 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 7 筆種子頁面全部更新

- [x] W-2026-08-037 知識花園加強：Relation 關聯 + 視覺地圖 + 改名 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 建立種子 ↔ 專題雙向 Relation 關聯

- [x] W-2026-08-041 YouTube ingest：LangGraph in 10 Minutes ✅ #knowledge #ai-agent
  - completed: 2026-08-08
  - 已完成：
    - ✅ 新增 wiki：source note + entity + concept

- [x] W-2026-08-039 PDF 處理流程建立 ✅ #knowledge #tools
  - completed: 2026-08-08
  - 已完成：
    - ✅ 建立完整 PDF → Markdown → wiki 流程

- [x] W-2026-08-040 cheerio-skills 同步 ✅ #skills
  - completed: 2026-08-08
  - 已完成：
    - ✅ 同步 7 個新 skills + 2 個更新

- [x] W-2026-08-036 Notion「任何當下 → AI相關」頁面 ingest ✅ #knowledge #notion
  - completed: 2026-08-07
  - 已完成：
    - ✅ 新增 wiki 2 entities

- [x] W-2026-08-034 YouTube ingest：Wow 頻道 5支 AI Agent 前沿研究 ✅ #knowledge #ai-agent
  - completed: 2026-08-06
  - 已完成：
    - ✅ 新增 wiki：1 source note、5 entities、4 concepts

- [x] W-2026-08-021 建立知識花園導覽 Database ✅ #knowledge #notion
  - completed: 2026-08-06
  - 已完成：
    - ✅ 建立 Database + 6 顆種子 + 5 個專題

## Work record contract

- 任務狀態只在本檔維護。
- 完成、決策、重要討論與處理結果追加至 `work/history/YYYY-MM.md`。
- 每個 history event 必須包含 `refs:`，至少指向 raw conversation、project 或 wiki 其中之一。
- 沒有形成工作進展或可追溯結果的對話，不需要建立事件。
