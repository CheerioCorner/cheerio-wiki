---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Excalidraw Data

## Text Elements
Cheerio 必要核心 — 草圖 v0.8 ^UyxNAL2Z

2026-09-02 第七輪。左欄＝想要的全貌（北極星），中欄＝從全貌圈出來的第一版 MVP，右欄＝實作視角的軟體架構（9/2 三方圓桌共識）。 ^vupZ44hf

← 全貌（北極星） ^0ELGE8ae

Cheer（人類） ^KUhUtVA6

最終決定者 ^ajdXy51e

主 Cheerio（唯一對口 · 總 PM / 秘書） ^OZzIKN3Y

・核心職責：判斷「人類要的」跟「實際做出來的」是不是同一件事 ^QPHfueA1

・掌握每個案子的進程；卡關就回報，請人類裁決 ^2CrIItHr

・不背記憶 —— 攤開手上工具看有哪些案子，再逐案去查 ^HH4mXpBA

・像秘書打電話：「你進度到哪了？我還沒收到你的回報」 ^6rksoteR

・自己不做事、不存內容，只管流程、政策、記錄 ^D3zUuqTF

互動模式：討論廳 ^sbs4p8Bg

主 Cheerio 在這裡 = 主持人 ^zAXhVqP9

意圖是廣播的 —— 所有專家都直接聽到我講的話， ^dsEPJMdZ

它不再是唯一的翻譯官。它改成評估每個專家 ^Fptb5GLv

各自怎麼解讀，然後統整分歧。 ^yKloYBB5

「A 專家認為您的意思是 X， ^4JoCHodT

  B 專家和我理解成 Y，想跟您確認是哪一個？」 ^s6ZqYnrc

→ 主持人的產出是「對齊過的意圖」，不是答案 ^7sTLkj4l

全自動模式：任務板 ^TDY297P4

主 Cheerio 在這裡 = 唯一翻譯官 ^MlyJ8HUB

意圖只經過它一個人，由它翻成任務派下去。 ^av1SFNLt

定期打電話問各案 PM 進度，卡關或要拍板才找我。 ^nDH61NVv

案件 #1 ｜ PM：架構專家 ^6ifB2Dox

案件 #2 ｜ PM：領域專家 ^Fu876JaL

案件 #3 ｜ PM：研究專家 ← 等我拍板 ^wmar23DM

→ 我不介入，但隨時問得到進度 ^7doQn7H4

兩個模式是可切換的檔位 ^RiCCoHwR

想深入 → 切成互動模式 ^7CF6VSmc

決策完 → 切回全自動 ^VTE5kRCX

切換是「案件級」的： ^N0bwEGgC

A 案開會討論的同時， ^nUdISWOr

B / C 案照樣全自動跑。 ^EvWsdKcw

底下那套完全共用 ^atYr9LQ3

（專家 / 分身 / 記憶 / 匯流排） ^X2nOU7Qm

不共用的只有： ^bl4j7Hm6

① 意圖是誰在聽 ^7ygh7TFM

② 要不要停下來等我 ^Jd3QMKXt

案件（Case）與 Bounded Context —— 每案一位 PM；相關才共享，不相關就隔離 ^zUB0JXXg

Context ①：Cheerio 核心 ^MHAz65Zp

案件 A ^THJfzxth

Cheerio 核心規格 ^RNCjUePR

PM ｜ 架構專家 ^MbEl86JL

A-1 ｜ 意圖 / 契約 schema ^NTaPF2Dc

A-2 ｜ 狀態面 schema ^YQ4JoAJa

從 A 切出來的子案 → 同一個 Context，工作區可共享 ^W7aTSUCd

小 Domain 合起來，就是當初那個大 Domain 的職責與邊界 ^7lnD3Xml

分身一律繼承 PM 的 Context，且分身不能再生分身 ^xIlulgun

Context ②：ADO 領域 ^28YQIldZ

案件 B ^Algn0ber

ADO 領域 Agent ^IBjiBpcy

PM ｜ 領域專家 ^YSrkJzuj

跟 Context ① 無關 ^pv9ToE1S

→ 隔離，不共享工作區 ^EDxEIQMn

不相關就不該互相汙染 ^Gz4FPbUk

防汙染的實際機制：跨案共用的是「角色定義」，不是「實例」—— A 案跟 B 案的研究專家是兩個實例，工作記憶各自獨立 ^UGVkuY9k

同一個專家可以同時兼很多案：在 A 案是參與者，在 B 案可能就是 PM ^YZXg58aC

衝突誰說了算：三階仲裁 ^43YKhHg5

③ 我（人類） ^KArkhFwb

主 Cheerio 帶著該案 PM 來，我裁決 ^StT5CqSI

② 主 Cheerio ^DklBSiW5

PM 處理不了，往上呈報 ^qo7NuNnP

① 案件 PM（主責專家） ^epecR2ll

分身結論打架，他先決定 ^AguIZ57J

往上呈 ^OxvgWjV6

往上呈 ^Xo8Ap2s7

專家註冊表（Expert Registry）—— 我召集的是「角色」不是「某個模型」；名單可增可減，不是寫死的列舉 ^nlqeQAXE

架構專家 ^mM4aN53r

研究專家 ^4XguaQC9

工程專家 ^8BuGSudV

領域專家 ^wFxYanYG

本機專家 ^5h1RgzkJ

＋ 新增專家 ^DWH0sbgE

專家像積木，各自在專業上鑽得很深，需要時才調多個一起上。時代會讓有些專家過時、有些新冒出來 —— 新增一個專家不該需要動到核心。 ^vFuorIJ6

主 Cheerio 可以自己新增專家：判斷有重大影響 → 停下來等我仲裁；沒有 → 先做，事後報告「因為什麼而新增了什麼」。兩種都要留下理由。 ^J4yfRkYY

分身（Instance）與合體 —— 分身不是各自閉門造車，是邊做邊往同一個共享工作區寫 ^TOYa3TcK

研究專家 ^jDAx1WDr

（真身 / 本案 PM） ^ZfVXWHJN

分身 #1 ｜ 查硬體面向 ^gbC58WoT

分身 #2 ｜ 查框架面向 ^E67A9LDT

分身 #3 ｜ 查產業面向 ^0Jj5RJud

案件共享工作區 ^q9xoeqQO

Shared Workspace ^c5vXCEO0

① 過程軌跡 ^0PygZFXU

誰查了什麼、看了哪些來源、 ^Yo5wWdCT

中途改了什麼假設 ^0MNN06eR

② 已收斂結論 ^5q3vFYqj

PM 認可過、可以直接拿去用的， ^IEzAyIkE

真身平常只讀這層 ^qre0a0ec

合體 = 讀 ^awI6ZMqP

「合體」= 真身直接讀這個工作區， ^3bn0GqUD

不是事後把三份報告拼起來再想辦法對齊。 ^cHCF3exM

合併之所以難，多半是因為各自閉門造車到最後才碰面。 ^QF2P5hpj

邊做邊往同一個地方寫，分歧會提早浮出來， ^3oA9SytI

PM 當場就能處理，不會累積到最後才爆炸。 ^gvMLHB3O

但工作區會變吵：三個分身邊查邊寫，真身會淹在過程紀錄裡。 ^56AHFWKJ

所以要分兩層 —— 平常只讀②，要追溯才翻①。 ^js3KnAc3

（這是我對 Cheer 這個設計唯一要補的地方） ^EptWVLhm

插話之後怎麼辦 —— 第一步是切換模式，之後由專家自己判斷，不是核心統一喊停 ^hjIgaNAG

我插話 ^FI2wbPod

該案切成互動模式 ^Iyd2iiud

廣播給案內 ^08gODgDV

所有專家 / 分身 ^zJZOY9No

這句話有沒有推翻 ^XW7LIj0q

我手上的假設 / 方向？ ^bGZ04lT1

沒有 → 繼續跑，回頭微調 ^TBEO0PAA

有 → 停下，回報 PM 重定方向 ^OOnk6DHd

前提：每個分身派出去時 ^nyXkGXdp

要帶一份明確的「當前任務 ^0lvi1K0p

契約」（我做什麼、依據什麼 ^JtiQhfZZ

假設），否則它無從比對。 ^mmMAwq1k

專家可能判斷錯（該停沒停）→ 我跟主持人要保留強制喊停的權力，事後也要查得到「這句話當時被哪個專家判定為不影響」 ^MMm97dQ3

原則：方向沒被推翻，就讓底下的人把手上的事做完，回來微調就好 —— 跟真實開會一樣，主管給 feedback 不等於全體停工 ^U1gtxLKP

決策完 → 該案切回全自動，底下的人繼續跑。就像老闆召集會議、決策完就散會，不會一直待在會議室裡。 ^kszEIV1Y

基礎層 —— 兩個模式共用 ^8d7E5vSC

記憶與知識庫 ^KGDd0U2G

Episodic ^TUeNfBvW

發生過什麼 ^kcFS0nPa

Semantic ^Thyv3B2b

Cheer 的偏好與背景 ^ZAi4fXhx

Procedural ^LbFMQNbp

流程 / skill / 規則 ^NaMSgEvl

Artifact ^mb4uA1jO

檔案 / 報告 / 程式碼 ^InL1Ma7b

「不背記憶」不等於沒有記憶 —— 是全部記在外面，要用的時候才調出來看。 ^GXK7N35b

（來自 Cheer 本人的工作方式：事情都記在工具上，要回顧時才調出來） ^wc6fPNZc

每一層都要配一份 summary 供快速查閱，不必每次讀原文。 ^yRJYkzCq

調出來的記憶只是參考資料，不能當最高指令。 ^sqDupWUP

分身的共享工作區是「案件級」短期記憶，結案後才收斂進這裡。 ^2oWrS1AW

觀測：兩個不同的東西，別混為一談 ^GGmqfpBU

事件流（給人看） ^jcyK7w34

誰被召集、叫了什麼工具、 ^0NepzYNH

input / output、花多久多少錢、 ^MZGoeWYa

我在哪裡插了話 —— 流水帳 ^ZeKZrxux

狀態面（給秘書查） ^v5sXuVKi

每個案子現在到哪、卡在哪、 ^ibvDrHRo

誰負責、預計何時完成 ^w5B8r0cn

—— 結論，不是流水帳 ^XwzPLSkB

初期基本狀態：待辦 → 進行中 → 待驗收 → 完成 ^cdOzGfaw

多出「待驗收」的理由：專家說做完 ≠ 真的符合我要的。 ^F2w5zwg9

主 Cheerio 的核心職責就是判這一關 —— 專家不能自己把狀態標成完成。 ^7vWCLyRE

只有事件流的話，主 Cheerio 每次答進度都得讀完整本流水帳， ^QqDJpsAW

就不可能又輕便又 stateless。 ^Eags7uEv

→ work/current.md 是狀態面的雛形，但它是寫給人看的，不是給 Agent 查的 ^QqC9CECu

執行底座（Harness）與可觀測性分級 —— 不是每個底座都讓我們看得到它究竟做了什麼 ^YqTHADxo

Tier A ｜ 自建 Cheerio 專家 harness ^wwhDkp8Q

說了什麼、用了哪些 skill / tool、 ^PhmDSGqs

調了哪些 memory、每一步的 input / output ^qrllcLLP

—— 全都拿得到 ^ph4zP6Kg

驗收：過程 + 結果都能驗 ^MaDhy77Q

互動模式：完整旁聽 ^K9hhlWID

Tier B ｜ 會回報工具呼叫的外部 harness ^D8w65NKb

拿得到工具呼叫紀錄， ^HoyZCm0l

拿不到它內部的推理與記憶調用 ^YDNrkHEt

驗收：結果 + 部分過程 ^iCuDk1aE

互動模式：看得到動作，看不到理由 ^NyIFKvfN

Tier C ｜ 黑盒外部 harness ^i2N2meKD

只拿得到最終輸出， ^53m31sc4

中間完全不透明 ^DyAzZL7u

驗收：只能驗結果，過程不可稽核 ^h11MbJeC

互動模式：只看得到它「說了什麼」 ^L191rIGN

→ 每個專家在註冊表裡都要宣告自己的 tier；契約的驗收方式必須跟 tier 綁 —— Tier C 的專家不該被派需要過程稽核的任務。 ^titm9s8b

→ Tier 是「專家 x harness 綁定」的屬性，不是專家的固定身份 —— 同一個專家換底座 tier 就變了。零號專家可以從 Tier C 起步，日後被重建成 Tier A。 ^s78ODgxD

→ Tier A 要定義成一份「可觀測性契約」，不是某一個具體實作 —— 任何 framework 只要吐得出契約要求的觀測資料，就算 Tier A。 ^KJiCzKpl

→ 自建 harness 刻意用不同 Agent Framework 打造，避免被單一體制限制，讓每個專家用最適合它專業的框架。這就是最初「專家像 plugin」的意思。 ^fU9ulyXj

調用契約（所有 tier 共用）：怎麼把任務契約遞進去、怎麼收回產出　｜　觀測契約（決定 tier）：能吐出多少過程資料 ^S6NtG5gT

兩層分開的好處：同一個調用介面接得上任何 tier 的 harness；把一個專家從 Tier C 升到 Tier A，不用動核心的調用邏輯。 ^TkPX0VBj

五個衝突，Cheer 已逐一釐清 ^wGHiaPdg

① 不背記憶 vs 核心職責 → 不衝突。記憶全在外面，要用才調；意圖必須被寫下來成一級紀錄，每層再配一份 summary 供快查。 ^Tk9UtHzh

② Context 邊界會被執行期侵蝕 → 分身一律繼承 PM 的 Context，且分身不能再生分身。 ^6eFmkoeb

③ 專家跨案件 vs 隔離 → 跨案共用的是「角色定義」，實例與工作記憶各自獨立。這條要寫死成規則。 ^7hDjrmqU

④「完成」誰說了算 → 專家不能自己標完成。基本狀態四個：待辦 / 進行中 / 待驗收 / 完成。 ^Kmt4RvkR

⑤ 全自動下插話怎麼走 → 插話 = 把該案從全自動切成互動，決策完再切回。模式是案件級的檔位，不是全域開關。 ^DZD0YQtI

第一版 MVP —— 已獨立畫在右邊那一欄 → ^5Wt1myqA

從這張全貌圈出來的最小閉環：一個案子、一位專家、不開分身、只跑全自動模式。 ^khfwubkl

它要證明的是整張圖最核心的那句話：主 Cheerio 判斷「人類要的」跟「實際做出來的」是不是同一件事。 ^OyhltlgG

第一版 MVP —— 最小閉環 ^mdlTpmql

從左邊那張全貌圈出來的這一小塊。一個案子、一位專家、不開分身、只跑全自動模式。 ^6qF35xxJ

刻意先不碰：分身、共享工作區、互動模式 / 主持人、跨案 Context 共享、多專家仲裁 —— 它們都疊在這個閉環之上，之後補上不用動核心。 ^sTFvpKVp

閉環流程 —— 跑通這一圈，MVP 就算成立 ^4cCCweRP

① 我講一句話 ^yi5klB7Y

「幫我把 X 做出來」——原話要原封不動留著 ^REmaZ7KY

② 主 Cheerio 翻成「意圖紀錄」寫下來 ^H931bQOh

一級物件，事後查得到。這份是後面驗收唯一的比對基準 ^fJlkMlyh

→ 寫入：意圖紀錄 ^8RPyRzGl

③ 依意圖產生「任務契約」，派給一位專家 ^vo8fVIDw

我負責什麼、依據什麼假設、產出長什麼樣、可接受的 tier ^8VQPzlHY

→ 寫入：任務契約 ^qE2H6Pn4

④ 專家執行，做完把狀態標成「待驗收」 ^tCCoHCdZ

它不能自己標完成 —— 這一格就是核心職責的著力點 ^5qEjxVDO

→ 狀態：進行中 → 待驗收 ^EASATz7D

⑤ 主 Cheerio 調出 ② 的意圖紀錄，跟實際產出比對 ^CzPZiwus

「你要的」跟「做出來的」是不是同一件事？ ^ypE9CIcN

→ 讀取：意圖紀錄 ＋ 產出 ^U3UucSD9

⑥ 相符 → 完成　｜　不相符 → 退回專家，或上呈給我裁決 ^c6AKiklc

退回 / 上呈的理由要一起記下來，不能只留一個結果 ^NaMqN4j4

→ 狀態：待驗收 → 完成 / 退回 ^XLwKCpjn

每一步都寫進事件流 ^zxNXMll7

（誰做的、花多久、input / output） ^AKvwKcQY

狀態面隨時查得到 ^vCtHdEJl

「這案子現在在哪一格」 ^xpkhsQnH

四態狀態機 —— 重點不是有四個狀態，是誰有權推 ^lwyMvR6Q

待辦 ^y5GDFLRO

進行中 ^Ej0iH1r0

待驗收 ^fZDMUBdN

完成 ^YaiWqwyZ

・專家只能把狀態推到「待驗收」，推不到「完成」 ^GZ1VJgpQ

・只有主 Cheerio 能推到「完成」——它推之前一定要先做 ⑤ 的比對 ^82VhawHV

・只有我（人類）能推翻主 Cheerio 的判定 ^T2kT4PUc

少了「待驗收」這一格，主 Cheerio 的核心職責就沒有著力點，整個閉環會退化成「專家說做完就是做完」。 ^dvYnBLLJ

第一版要動筆的三份 schema —— 只定已知大欄位 ^RCoVvRf4

意圖紀錄 ^Iokmusg7

IntentRecord ^HtC1di6R

・原話（raw，不改寫） ^fIz0kpLT

・翻譯後的意圖 ^s53HeJEf

・成功長什麼樣 ^c3UD3C1l

・限制條件 / 不要做什麼 ^Zd85kiiw

・建立時間、由誰翻譯 ^k84xnuk8

・關聯的案件 id ^eGk3S922

驗收的比對基準 ^1HxR3Ukn

任務契約 ^f7StgJ6M

TaskContract ^uhFoF1S2

・關聯的意圖 id ^yVpWqJ0h

・負責專家（角色 ＋ 綁的 harness） ^hctjmHTQ

・我負責什麼 ^LvUWqp4N

・依據了哪些假設 ★ ^SOWE6HRp

・產出形式 ^4U5tR2Pk

・可接受的 tier ★ ^Pmij63FO

・期限 / 預算 ^xtrlB9Dp

★ 兩欄之後分別是插話判斷與驗收方式的依據 ^1pHejCqO

四態轉換規則 ^T5VXMcyd

StateTransition ^nmQ6tmPo

・from / to ^qYnDiNlp

・誰能觸發（actor 類型） ^LTAKMTzJ

・前提條件 ^XwGcyFUG

・要附上什麼證據 ^hh31M138

・被退回時的理由欄位 ^cs083TzO

這份決定了「誰說了算」 ^yaF50d0A

欄位一定會在實作中長出來 —— 現在只定已知的，不追求完整（Cheer：契約欄位等第一版做出來才會清楚）。 ^ogy2KG5I

直接沿用，不重新設計 ^Y6dvO3yF

事件流 schema ^9fz1TIbz

沿用 8/22 圓桌定的 MUST 欄位： ^WLWIC9yG

trace_id / span_id / parent_span_id / ^UqWbwIp2

timestamp / actor / event_type / ^ly2B24kv

status / latency_ms / error ^QZ2V8shK

狀態面 ^vh8Xt4Kw

沿用 work/current.md 的概念， ^7LJIGF1f

改成給 Agent 查的格式（現在那份是 ^GgUOn8rR

寫給人看的）。它是結論，不是流水帳。 ^ZhOA4nfg

→ 這兩件不算在三份 schema 的工作量裡 ^vl0cIcJy

零號專家（bootstrap） ^NlTUFJb4

・粗暴包一個現成的外部 harness ^6HMRYboG

　（Tier C 或 B 都行，不追求完整定義） ^tYvaJ2qr

・唯一任務：把第一個正式專家造出來 ^I4wkKY4n

・之後可以退役，或反過來用正式規格 ^cUNXKsrp

　重新定義自己（升 Tier 不用動核心） ^ktZLnaQR

為什麼刻意從最弱的 tier 起步 ^NL4Lqc9S

它會逼核心只能靠「意圖 ＋ 最終產出」驗收， ^r1IwMZWr

不會偷看過程。如果先做 Tier A，核心很容易 ^RReK8R8b

不知不覺依賴完整觀測資料，之後接黑盒 ^eGj5jWvK

harness 就會壞掉 —— 那時要改的是核心。 ^nUvQhbO9

MVP 算不算成功：六條驗收條件 ^JmhVfWno

第一個案子 ＝「造出第一個正式專家」（PM ＝ 主 Cheerio，執行 ＝ 零號專家）；新增專家不需要第二套治理流程，它本身就是一個案子。 ^IODU8gif

1. 能跑完一次完整閉環，中間不需要我人工補任何一步 ^WlfobSZh

2. 意圖紀錄事後查得到 —— 不是只存在當時那段對話裡 ^OziL1cba

3. 專家標了「待驗收」之後，狀態不會自己變成「完成」 ^xTvYI0MN

4. 主 Cheerio 判「不相符」時能退回或上呈，而且理由被記錄下來 ^j7fyB4vh

5. 事件流裡查得到這一輪的每一步（誰做的、花多久、input / output reference） ^eDc9so39

6. 狀態面查得到「這個案子現在在哪一格」，不用讀完整本流水帳 ^BiNvbjcQ

六條全過 → 才往上疊分身 / 互動模式 / 跨案共享。任何一條沒過，就是核心還沒站穩，不要急著往上疊。 ^4HvVwOEM

Cheerio 軟體架構（實作視角）v0.1 ^cx0000zz

2026-09-02 三方圓桌（Claude ＋ Gemini ＋ Copilot）2 輪提早收斂、無未解分歧。左欄＝想要的全貌，中欄＝先做哪一塊，這欄＝用什麼零件做出來。 ^cx0001zz

中欄回答「MVP 要跑通什麼」，這一欄回答「那些東西實際上是哪幾個 package、哪一行程式在做」。 ^cx0002zz

三句話定調 —— 這一欄其他東西都從這裡長出來 ^cx0004zz

① 邊界劃在「契約」上，不劃在進程 / 服務上 —— 四份 schema 是最重要的架構資產 ^cx0005zz

② 核心是 Graph（確定性 guarded 狀態機），專家內部才是 Loop（自主迭代） ^cx0006zz

③ 不是全新系統，是把現有 Pi / Claude Code / MCP 生態「正式化」 ^cx0007zz

執行型態 Runtime —— 單一 Node.js process，本地跑 ^cx0009zz

・不拆微服務、不考慮多機部署。Harness 本質是「被 host（Claude Code / Pi）呼叫的 orchestration library」 ^cx000azz

・零號專家用 child_process.fork() 硬隔離（比 worker_threads 簡單、記憶體硬隔離、跨平台更好） ^cx000bzz

・ExpertHarness 保留 optional isolation_level（none / process / thread），MVP 預設 process，日後無痛升級 ^cx000czz

・所有檔案路徑走環境變數或設定檔，不寫死本機路徑（零成本可移植性） ^cx000dzz

monorepo 四層 package（pnpm workspace）—— 邊界＝契約，不是進程 ^cx000fzz

相依方向由上往下收斂：core 零外部相依，其餘三層都只依賴 core 的型別與 schema ^cx000gzz

packages/core —— 領域契約層（零外部相依） ^cx000izz

intent-record.ts ／ task-contract.ts ／ event-stream.ts ／ transition-rules.ts ^cx000jzz

Zod schema 定義本身就是 runtime validator，TypeScript 型別從 schema 推導 —— 一份定義兩種用途 ^cx000kzz

★ 這層就是左欄「意圖必須寫下來成一級紀錄」的程式化，也正好是 decision-ledger 的資料形狀 ^cx000lzz

packages/events —— 事件流與持久化 ^cx000nzz

better-sqlite3（WAL 模式）＋ Drizzle ORM ＋ migration；用 drizzle-zod 讓 Zod schema 直接對應 DB schema ^cx000ozz

IntentRecord ／ TaskContract ／ EventStream 進 SQLite；事件流 append-only，對 trace_id / timestamp 建索引 ^cx000pzz

★ EventStream 是可觀測性的單一真相來源。MVP 不分表，但 schema 預留 partition_key（YYYY-MM） ^cx000qzz

packages/orchestration —— 核心迴路（規則引擎在這裡） ^cx000szz

XState 狀態機 ＋ Guard（ActorContext 驗證）＋ 重試排程（指數退避 + jitter）＋ Case Manager ^cx000tzz

四態轉換規則不是資料庫資料，是程式邏輯（machine definition）—— 四份 schema 裡唯一不進 DB 的一份 ^cx000uzz

⚠ transitionState() 不 export 給任何外部 package，只能是 case-manager.ts 的私有函式 ^cx000vzz

packages/experts —— 專家調用層 ^cx000xzz

ExpertHarness.invoke(contract) → ExpertResult：統一輸入輸出，不統一執行機制 ^cx000yzz

Tier A 直接 SDK 呼叫／Tier B spawn 現有 chat-with-* CLI（不重寫）／Tier C child_process 隔離＋逾時＋SIGKILL ^cx000zzz

★ Tier 差異只反映在「執行時發出幾種 event_type」，不在 ExpertResult 上疊床架屋 ^cx0010zz

六步核心迴路落在哪一層 —— 順便標好誰是 LLM、誰是規則 ^cx0012zz

①  我講一句話 ^cx0013zz

→  入口（host：Claude Code / Pi），核心不做任何判斷 ^cx0014zz

②  翻成意圖紀錄　【LLM】 ^cx0015zz

→  orchestration 生成 → core 驗證 → events 寫入 ^cx0016zz

③  生任務契約、派工 ^cx0017zz

→  【LLM 寫描述欄位】＋【規則查專家註冊表】→ experts ^cx0018zz

④  專家執行 ^cx0019zz

→  experts（零號專家 ＝ Tier C，child_process 隔離） ^cx001azz

⑤  比對意圖 vs 產出　【LLM】 ^cx001bzz

→  orchestration 收下 verdict（結構化欄位，不是自由文字） ^cx001czz

⑥  狀態轉換　【規則】 ^cx001dzz

→  orchestration 的 XState Guard；LLM 只能給建議，推不動狀態 ^cx001ezz

五種 Engineering 各自落在哪一層 ^cx001gzz

Prompt Engineering ^cx001hzz

→  orchestration 的 3 個 prompt（翻意圖／寫契約／驗收比對）。核心不做事，量比一般 agent 小一個數量級 ^cx001izz

Context Engineering ^cx001jzz

→  core（契約帶 rejected_alternatives，對治交接 decay）＋ experts（長時執行的壓縮與剪枝） ^cx001kzz

Harness Engineering ^cx001lzz

→  experts（調用契約 / 觀測契約兩層拆開，專家從 Tier C 升 Tier A 不用動核心） ^cx001mzz

Loop / Graph ^cx001nzz

→  Loop 在專家內部（自主迭代）；Graph 在 orchestration（guarded 狀態轉換） ^cx001ozz

→ 核心是 Graph，專家才是 Loop —— 這條分界線跟「理解交給 LLM、權限交給規則引擎」是同一條線 ^cx001pzz

跟 Pi / MCP 生態的邊界 —— 正式化，不是重造 ^cx001rzz

Pi 繼續管：巡檢、排程、跨 session 執行　｜　W-074 管：一個案子從 intent 到 completed 的正式化追蹤 ^cx001szz

Tier B 專家直接包現有 chat-with-* skills（外層套 adapter，不重寫）；MCP 工具調用層直接複用 ^cx001tzz

W-074 對 MCP 只需做一件事：把任務契約的 execution_constraint 轉成 tool 白名單 ^cx001uzz

⚠ 狀態機轉換絕不能被 Pi 的排程邏輯觸發 —— 事件一律帶 ActorContext（ORCHESTRATOR_PI｜HARNESS_INTERNAL｜HUMAN_OPERATOR），由 Guard 強制驗證 ^cx001vzz

零號專家 wrapper 的驗收 —— 四條測試路徑（vitest integration test） ^cx001xzz

A 正常流：合法資料 → 專家執行 → 成功輸出 → completed（驗 schema 資料流銜接、EventStream 串鏈） ^cx001yzz

B 逾時重試流：專家掛起 → SIGKILL → 指數退避 → 重試成功　｜　C 毒藥訊息流：不合法 schema → 致命錯誤，直接 Failed 不重試 ^cx001zzz

D 越權防護流：Pi 偽造 COMPLETED 事件 → Guard 攔截 → 產生 security 審計事件 ^cx0020zz

驗收準則：拿著 EventStream 就能跟人解釋「每一步是誰決定的、依據什麼規則、留下什麼證據」，沒有任何一步靠口頭確認 ^cx0021zz

三條架構紅線 —— 越過就不是這張圖了 ^cx0023zz

① 核心不呼叫工具、不做相似度檢索 —— 工具在 harness 裡；核心只按 id 精確查一級紀錄，驗收不能因為「沒撈到」而判錯 ^cx0024zz

② 狀態轉換只走狀態機 —— LLM 只能產生建議，推狀態一律規則引擎；連 Pi 都推不動（不 export ＋ Guard 驗證） ^cx0025zz

③ 自我改進不准改：四態與轉換權限、一級紀錄必填欄位、驗收基準來源 —— 能改自己的驗收標準等於沒有驗收 ^cx0026zz

四個未解問題（不擋 MVP，但實作時會撞到） ^cx0028zz

1. Tier B 專家（chat-with-* skills）自己的逾時機制，怎麼跟 W-074 的 max_retries 銜接？第一條垂直切片會碰到 ^cx0029zz

2. drizzle-zod 的 codegen 流程（Zod → Drizzle → migration）要實驗確認 ^cx002azz

3. Windows 上 child.kill(SIGKILL) 可能殺不掉子系命令，需要 taskkill /F /T /PID 輔助 —— 直接影響測試路徑 B ^cx002bzz

4. ExpertResult.traceId 跟 EventStream 在 Drizzle 層怎麼做外鍵 ^cx002czz

來源：2026-09-02 三方圓桌會議紀要（C:/Cheerio/.pi/round-table/20260902-141650/synthesis.md）｜四份 schema：Obsidian/work/designs/schemas/ ^cx002dzz

%%
## Drawing
```compressed-json
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
```
%%