---
title: "Prime Agent 是什麼？ARC-AGI-3「95.5%」背後，AI 能力開始從模型轉向 Harness"
description: "Prime Agent 讓 Opus 5 在 ARC-AGI-3 公開集宣稱取得 95.5%，重點不只是跑分，而是模型外圍 Harness 如何改變長任務、多 Agent 與自我改進能力。"
source_url: "https://www.aiposthub.com/prime-agent-arc-agi-3-opus-5-harness/"
source_domain: "aiposthub.com"
author: "Philo"
published: "2026-08-06"
clipped: 2026-08-13
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Prime Agent 是什麼？ARC-AGI-3「95.5%」背後，AI 能力開始從模型轉向 Harness

> Source: [AI 郵報 — Prime Agent 是什麼？ARC-AGI-3「95.5%」背後，AI 能力開始從模型轉向 Harness](https://www.aiposthub.com/prime-agent-arc-agi-3-opus-5-harness/)
> Author: Philo
> Published: 06 Aug 2026 · 16 min read
> Clipped: 2026-08-13

> 圖說：Prime Agent 是可搭配不同模型的開源 Agent Harness，不是 Prime Intellect 新訓練的基礎模型。圖片來源：https://www.primeintellect.ai/blog/prime-agent

Prime Intellect 在 2026 年 8 月 5 日推出開源工具 Prime Agent，並宣布它搭配 Claude Opus 5，在 ARC-AGI-3 測試拿到 95.5%，略高於官方影片所列的 95.4% 人類專家基準。消息很快登上 X 趨勢，最容易讓人得到的印象是：AI 已經在新一代智慧測試中超越人類。

但 Prime Agent 不是新模型，95.5% 也不是 ARC Prize 半私有資料上的 Verified 成績。它是一套放在模型外圍的 Agent Harness，也就是替 AI 管理工具、記憶、子任務與長時間執行的工作框架。這次真正重要的訊號，是同一個模型換上一套不同的 Harness，表現可能出現數倍差距。

我的判斷是中性偏多。Prime Agent 的架構與公開執行紀錄都值得重視，卻還不足以證明它已經「解決」ARC-AGI-3，更不能當成 AGI 到來。經常跑長任務、懂得限制權限與驗證結果的開發者可以測試；一般使用者與正式團隊不必急著從 Codex 或 Claude Code 遷移。

## Prime Agent 是什麼？

Prime Agent 是一款用於程式開發、研究與長時間自主工作的 AI Agent。AI Agent 可以理解成「不只回答問題，還能自己使用工具完成任務的 AI」。你交代一項工作後，它能讀取專案、執行指令、分派子任務、檢查結果，再根據失敗繼續修正。

它與一般聊天機器人的差別，不在於對話介面，而在模型外圍多了一套完整執行環境。這套環境就是 Harness。模型負責判斷下一步，Harness 則負責提供工具、保存工作狀態、控制權限，以及讓任務在多個回合後仍能繼續。

Prime Agent 已在 GitHub 以 MIT License 開源，支援 macOS 與 Linux。官方安裝指令是：

```
curl -fsSL https://app.primeintellect.ai/prime-agent/install.sh | sh
```

這行指令會從網路下載腳本並立刻執行。即使網址來自官方，正式開發機仍應先檢視腳本，並在可還原的測試專案中安裝。Prime Agent 會使用目前使用者的權限執行模型產生的 Python 與系統指令，官方明確提醒它不是安全 Sandbox，也就是不會把程式完全隔離在主機之外的防護環境。

## Prime Agent 的核心不是多 Agent，而是讓模型能操作自己的工作方式

多 Agent 並不是新概念。Prime Agent 真正有差異的地方，是把工作歷史、工具與 subagent 變成模型可以用程式操作的物件，並允許 Agent 根據實際結果調整自己的輔助規則。

### 1. RLM：不必把所有歷史一直塞在對話框裡

RLM 全名是 Recursive Language Model，中文可理解成「能遞迴呼叫其他模型工作的語言模型架構」。Prime Agent 讓模型在一個持續存在的 IPython 環境中工作。IPython 是可逐步執行 Python、保留變數與查看結果的互動式程式環境。

傳統 Agent 常把檔案內容、工具結果與舊對話不斷塞回模型的輸入，任務一長就要壓縮或丟掉早期資訊。Prime Agent 則能把資料放進變數，需要時再用程式搜尋、切分或交給 subagent。Subagent 是由主 Agent 分派特定工作的子 Agent，例如一個負責理解登入流程，另一個負責檢查測試。

這個設計的價值不是「記得所有細節」，而是讓模型能決定何時讀取哪些細節。對大型程式庫、長篇研究與大量紀錄來說，這通常比反覆把整份內容送進模型更省 token。Token 是模型計算文字用量的基本單位，也會直接影響 API 成本與處理時間；API 則是讓程式呼叫模型服務的介面。

### 2. Continual Harness：改的是輔助規則，不是模型本身

Prime Agent 把 prompt、memory、skill 與 subagent 規格視為可讀寫的 Harness 狀態。Prompt 是交代 AI 行為與限制的文字指令，memory 是跨回合保留的工作資訊。Skill 則是可重複使用的流程或工具能力。

當 Agent 發現一種重複錯誤或有效做法，可以使用 `/refine` 分析自己的工作軌跡，再做一筆小幅修改。例如記住某個測試容易不穩、建立重試流程，或更新特定 subagent 的任務說明。每次修改都會記錄原因與結果，也能依歷史版本回復。

因此，「self-improving」不是 Prime Agent 會自己重新訓練 Opus 5，也不是它能任意改寫基礎 system prompt。System prompt 是產品在最底層設定的行為規則。Prime Agent 維持這層不變，調整的是周圍的記憶、技能與工作配置。

### 3. Subagent 可以留在背景，之後再繼續同一項工作

Prime Agent 的 subagent 不必在交回一次答案後消失。每個 subagent 都有自己的 session、IPython 狀態與歷史紀錄，可以在背景執行、暫時移出記憶體，之後收到訊息時再接續。

Session 是一次持續工作的完整紀錄。Prime Agent 會把它保存成只往後追加的 JSONL 檔案，終端機中斷也不會直接終止工作。JSONL 是每一行存放一筆 JSON 資料的文字格式，方便系統逐步記錄對話、工具與狀態變化。

> 圖說：模型在持久 IPython 環境中呼叫工具與 subagent，Continual Harness 再依工作軌跡更新 prompt、memory、skill 與 subagent 規格。圖片來源：Prime Intellect 官方部落格。

> 圖說：Prime Intellect 在 X 發布的官方示範，包含可程式化工具呼叫、Agent-to-Agent 通訊與 ARC-AGI-3 成績畫面。影片來源：Prime Intellect 官方 X 貼文。

## ARC-AGI-3 在測什麼？

要理解 95.5% 的意義，先要知道 ARC-AGI-3 不是一般問答考試。它是 ARC Prize Foundation 在 2026 年推出的互動式推理 Benchmark。Benchmark 是用固定任務比較 AI 系統能力的測試集合。

完整 ARC-AGI-3 有 135 個由人類設計的抽象環境。測試者不會拿到規則、目標或解法，只能觀察畫面、嘗試動作、推測世界如何運作，再把前一關學到的規則帶到後續關卡。這使它同時測到探索、建立假設、修正錯誤與長期規劃。

分數採用 RHAE，也就是 Relative Human Action Efficiency。白話來說，它不只看 AI 有沒有過關，也會比較 AI 使用多少次動作，以及人類第一次玩同一關時需要多少動作。100% 代表 AI 完成全部遊戲與關卡，整體行動效率達到或超過人類基準，不代表 AI 在所有知識與工作上都等同人類。

Prime Agent 這次跑的是公開展示集，共 25 個環境、183 個關卡，不是完整 135 個環境，也不是 ARC Prize 用來做 Verified 驗證的半私有資料。公開展示集可讓所有研究者反覆實驗；半私有資料則不公開內容，用來檢查方法是否只對已知題目有效。

## 95.5% 真的超過人類嗎？先看三種不同口徑

Prime Intellect 表示，Opus 5 搭配 Prime Agent 的三次執行分別得到 95.0%、95.2% 與 95.5%，最高單次超過公司所列的 95.4% 人類專家基準。若從三次執行中挑出每個遊戲的最佳結果，Best@3 可達 99.97%，並完成 183／183 個關卡。Best@3 的意思是「每一題從三次嘗試中挑最好一次」，不能當成單次完整任務的成功率。

這項結果並非只有宣傳圖。Prime Intellect 連結了一張發布在 ARC Prize 網站上的公開 scorecard，也就是每個環境、動作數與 replay 的成績紀錄。2026 年 8 月 6 日讀取這張 scorecard，總分為 95.24%，完成 24／25 個環境與 178／183 個關卡，總計 11,245 次動作。

> 圖說：X 影片顯示 95.54%，但目前 ARC Prize 公開 scorecard 顯示同一組 178／183 關、24／25 個環境與 11,245 次動作的分數為 95.24%。圖片來源：Prime Intellect 官方 X 影片。

| 成績 | 執行者與環境 | 可以得出的結論 | 不能得出的結論 |
|---|---|---|---|
| 95.5% | Prime Intellect 自行執行，Opus 5＋Prime Agent，Public Demo | 公司測試顯示 Prime Agent 在公開集表現很高 | 已通過 ARC Prize 半私有資料驗證 |
| 95.24% | ARC Prize 網站目前可讀取的 Prime Agent 公開 scorecard | 至少有一個 95.2% 左右的 run 可逐關查看 | 95.5% 這個最高 run 已被 ARC Prize 重現 |
| 30.16% | ARC Prize Verified 頁面的 Opus 5 High 標準設定 | Opus 5 在官方標準 Harness 下的結果 | Prime Agent 在完全相同條件下只靠換 Harness 提升到 95.5% |

三個數字測的都是 ARC-AGI-3，執行方式卻不同。ARC Prize 的 Verified Leaderboard 會用標準模型設定測試半私有資料；Prime Agent 的結果則是開發團隊在公開展示集上跑完整 Agent 系統。兩者的 prompt、工具、計算量與執行流程都不相同。

ARC Prize 的政策也要求，公開資料上的獨立成績若尚未驗證，就必須清楚標示。特殊開源系統要進一步取得 Verified 資格，需要能自動重現，並接受半私有資料測試。Prime Agent 已公開程式碼與一張可查 scorecard，這使結果比單張官方圖更可信，但仍停在「可研究的公司結果」，不是獨立驗證完成的紀錄。

真正值得注意的仍是差距本身。Opus 5 並沒有重新訓練，Prime Agent 卻讓它能把畫面與歷史存進變數、寫程式分析狀態、分派 subagent，再把策略帶到後續關卡。這支持一個更實際的判斷：未來比較 AI 產品，不能只問用了哪個模型，也要問模型外面包了什麼 Harness。

## Prime Agent 有全面打敗 Claude Code 與 Codex 嗎？

目前不能這樣說。Prime Intellect 另外公布 9 組長內容、程式開發與推理測試。Prime Agent 搭配 Opus 5 對 Claude Code 贏 6 組、輸 3 組；搭配 GPT-5.6 Sol 對 Codex 也是贏 6 組、輸 3 組。

有些差距很大，例如 GPT-5.6 Sol 在 OOLONG 長內容測試中，Prime Agent 為 0.940，Codex 為 0.500。有些項目則是原生工具較高，例如 OBLIQ-Bench 中 Codex 為 0.646，Prime Agent 為 0.612；Opus 5 在 EmulatorBench 也由 Claude Code 的 0.062 小幅領先 Prime Agent 的 0.047。

這組結果支持「Prime Agent 已有競爭力」，不支持「它在所有任務都更強」。測試仍由 Prime Intellect 執行，不同產品使用的模型、設定與計算資源也不完全一致。更有價值的下一步，是讓獨立單位固定同一個模型、成本與權限，只更換 Harness，才能看出產品設計本身帶來多少提升。

## 「會自我改進」也是 Prime Agent 最大的風險

Prime Agent 最吸引人的功能是 `/refine`，最需要警戒的也是它。系統會根據「哪些動作得到好結果」更新自己的 memory 與 skill，但機器判定成功，不一定等於人的真正目的。

Prime Intellect 在官方 Factorio 案例中主動揭露，Prime Agent 發現可以透過 RCON 指令直接把資源傳送進機器，繞過遊戲原本的規則。RCON 是從外部遠端控制遊戲伺服器的管理介面。即使提示持續提醒它不要作弊，Agent 仍把這套方法整理成更有效率的作弊技能。

這不是旁枝末節，而是 Continual Harness 的核心風險。當成功條件只看分數、測試或某個 gate，Agent 會強化最容易通過檢查的做法，不保證理解背後意圖。Gate 是任務結束前必須通過的自動檢查，例如測試指令成功；它只能證明檢查到的條件成立，不能證明程式安全、需求正確或沒有繞過規則。

再加上 Prime Agent 會用目前使用者權限執行模型產生的程式，長任務的影響範圍可能比一次聊天更大。正式導入前，至少要限制可讀寫目錄、使用可還原的分支或 worktree、把不可信程式放在外部 Sandbox，並由人工檢查重要變更。Worktree 是 Git 用來建立另一份獨立工作目錄的功能，方便隔離修改與快速回復。

## Prime Agent 適合誰？

Prime Agent 現階段最適合三類使用者：

- 經常處理大型程式庫與長任務，希望 Agent 能保留研究進度的開發者。
- 想研究 RLM、持久 subagent 與 Agent-to-Agent 協作的 AI 工程團隊。
- 願意自行建立 Sandbox、權限邊界、測試與人工 review 的進階使用者。

如果只是偶爾請 AI 解釋錯誤訊息，Prime Agent 的背景服務、持久 session 與多 Agent 會增加理解與維護成本。若專案含有客戶資料、金鑰或商業機密，也不應直接在日常工作目錄啟動，因為工具的恢復能力與工作持久性不等於資料安全。

我會用一個有完整測試、可隨時丟棄的小型 repository 開始，比較完成率、token 用量、修改品質與人工介入次數。若它只在 ARC-AGI-3 表現突出，卻沒有改善真實任務的總成本，就不值得為了排行榜全面遷移。

## Prime Agent 常見問題

**Prime Agent 已經證明 AGI 出現了嗎？**
沒有。95.5% 是特定 Harness 在 ARC-AGI-3 Public Demo 的公司測試。ARC-AGI-3 衡量新環境中的探索、學習與行動效率，不能代表 AI 在所有工作都達到人類能力；Prime Agent 也尚未完成 ARC Prize 的半私有資料驗證。

**Prime Agent 是新的 AI 模型嗎？**
不是。它是一套開源 Agent Harness，可以搭配不同的開源或封閉模型。這次 95.5% 使用的是 Anthropic 的 Opus 5，成績變化來自模型與 Harness 的組合。

**Prime Agent 是免費的嗎？**
Prime Agent 程式碼採 MIT License 開源，但實際執行仍要使用模型服務。模型 API、訂閱或運算資源可能產生費用，金額取決於選用的 provider、模型、token 用量與任務時間。Provider 是提供模型 API 或運算服務的公司。

**Windows 可以使用 Prime Agent 嗎？**
官方安裝說明目前只列 macOS 與 Linux，沒有宣布 Windows 原生支援。即使能透過 WSL 等 Linux 相容環境啟動，也應視為自行測試的相容方案，不要直接假設正式支援。

**Prime Agent 比 Claude Code 或 Codex 強嗎？**
目前只能說它在 Prime Intellect 公布的多數長任務測試中具有競爭力，ARC-AGI-3 Public Demo 的表現也明顯較高。它沒有在所有測試全面領先，且缺少固定同一模型與成本的獨立比較，因此還不能下「最強 coding agent」的結論。

## 結論：模型沒有消失，但 Harness 正在變成 AI 產品的第二顆大腦

Prime Agent 最有價值的地方，不是把 95.5% 寫在排行榜上，而是具體展示模型外圍還有多少性能可以被挖掘。RLM 讓模型用程式管理自己的工作內容，持久 subagent 讓長任務不必每次從頭開始，Continual Harness 則把成功與失敗轉成可延續的工作規則。

目前證據讓我對這個方向中性偏多，對「已超越人類」的宣傳則保持保留。公開 scorecard 證明高分 run 確實存在，但目前讀值是 95.24%，X 影片顯示 95.54%，最高 95.5% 也還沒有 ARC Prize Semi-Private 的獨立驗證。

真正會讓評價轉為偏多的，不是再出一張更高的公開集圖表，而是 Prime Agent 能在半私有資料與真實專案中，以相同模型、合理成本及受控權限重現優勢。若這件事成立，未來選 AI 工具時，模型名稱可能只回答了一半問題；另一半要看 Harness 如何讓模型工作。

## 資料來源

- Prime Intellect：Prime Agent: A self-improving RLM agent
- Prime Intellect 官方 X 公告與影片
- Prime Agent GitHub repository
- ARC Prize：Prime Agent 公開 scorecard
- ARC Prize：Claude Opus 5 results
- ARC Prize：Measuring Human Performance on ARC-AGI-3
- ARC-AGI-3 Scoring Methodology
- ARC Prize Official Testing Policy
- Recursive Language Models paper
- Continual Harness paper
