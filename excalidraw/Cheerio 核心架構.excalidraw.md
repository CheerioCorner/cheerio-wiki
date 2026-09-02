---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Excalidraw Data

## Text Elements
Cheerio 必要核心 — 草圖 v0.9 ^UyxNAL2Z

2026-09-03 第八輪。左欄＝想要的全貌（北極星），中欄＝從全貌圈出來的第一版 MVP，右欄＝實作視角的軟體架構流程圖（9/2 三方圓桌共識）。 ^vupZ44hf

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

Cheerio 軟體架構（實作視角）v0.2 ^dg0000q

2026-09-02 三方圓桌（Claude ＋ Gemini ＋ Copilot）2 輪提早收斂、無未解分歧。左欄＝想要的全貌，中欄＝MVP 要跑通什麼，這欄＝實際上是哪幾個 package 在做。 ^dg0001q

Cheerio Harness —— 自建執行底座 ^dg0003q

單一 Node.js process ・ 本地跑 ・ 本質是「被 host 呼叫的 orchestration library」 ^dg0004q

Gateway / Host ^dg0006q

Claude Code ・ Pi ・ CLI（只收話，不判斷） ^dg0007q

人類原話 ^dg0009q

Case（一個案子：從 intent 到 completed） ^dg000bq

① raw_input（原話不改寫） ^dg000dq

② IntentRecord ^dg000gq

【LLM】翻譯，驗收唯一基準 ^dg000hq

③ TaskContract ^dg000kq

【LLM 寫描述】＋【規則查註冊表】 ^dg000lq

packages/orchestration —— 核心迴路（規則引擎在這裡） ^dg000oq

XState 四態狀態機 ^dg000qq

＋ Guard（ActorContext） ^dg000rq

Verifier【LLM】意圖 vs 產出比對 ^dg000tq

verdict / human_override（結構化欄位） ^dg000wq

待辦 ^dg000yq

進行中 ^dg0011q

待驗收 ^dg0014q

完成 ^dg0017q

專家只能推到「待驗收」・只有主 Cheerio 能推「完成」・只有人類能推翻主 Cheerio ^dg0018q

⚠ transitionState() 不 export；所有事件帶 ActorContext，Guard 強制驗證 ^dg0019q

★ 這裡是 Graph（確定性），LLM 只能給建議，推不動狀態 ^dg001aq

packages/experts —— 專家調用層 ^dg001cq

ExpertHarness.invoke(contract) ^dg001eq

統一輸入輸出，不統一執行機制 ^dg001fq

Tier A ^dg001hq

直接 SDK 呼叫 ^dg001iq

Tier B ^dg001kq

spawn 現有 CLI ^dg001lq

Tier C ^dg001nq

child_process ^dg001oq

★ MVP 的零號專家 ＝ Tier C：逾時控制 ＋ IPC ＋ SIGKILL 硬終止 ^dg001pq

Tier 差異只在 invoke() 內部，不在介面上；也只反映在 ^dg001qq

「執行時發出幾種 event_type」，不在 ExpertResult 疊床架屋 ^dg001rq

這裡才是 Loop（自主迭代）—— 核心是 Graph，專家是 Loop ^dg001sq

isolation_level?: none | process | thread（MVP=process） ^dg001uq

TaskContract ^dg001wq

ExpertResult ^dg001yq

packages/core（零外部相依） ^dg0020q

intent-record.ts ／ task-contract.ts ^dg0021q

event-stream.ts ／ transition-rules.ts ^dg0022q

Zod schema 本身就是 runtime validator ^dg0023q

★ 型別從 schema 推導 —— 一份定義兩種用途 ^dg0024q

★ 邊界劃在契約上，不劃在進程 / 服務上 ^dg0025q

packages/events —— SQLite(WAL) ＋ Drizzle ORM ^dg0027q

IntentRecord ／ TaskContract 進表（drizzle-zod 對齊） ^dg0028q

EventStream append-only，trace_id / timestamp 建索引 ^dg0029q

MVP 不分表，但預留 partition_key（YYYY-MM） ^dg002aq

★ EventStream ＝ 可觀測性的單一真相來源 ^dg002bq

★ 一級紀錄的形狀本身就是 decision-ledger ^dg002cq

記憶四層（要用才調，不常駐） ^dg002eq

Episodic：事件流封存 ＋ 每案結案摘要 ^dg002fq

Semantic：MEMORY.md（MVP 不上向量庫） ^dg002gq

Procedural：不新建，是一級紀錄的投影 ^dg002hq

Artifact：檔案系統為真相 ＋ 索引表 ^dg002iq

★ 核心不做相似度檢索，只按 id 精確查 ^dg002jq

schema 驗證 ^dg002lq

每一步寫進事件流 ^dg002nq

調出意圖比對 ^dg002pq

專家調記憶 ^dg002rq

Pi 的邊界 —— 排程推不動狀態機 ^dg002tq

Pi 管：巡檢 ／ 排程 ／ 跨 session 執行 ^dg002uq

W-074 管：一個案子從 intent 到 completed 的正式化追蹤 ^dg002vq

Tier B 專家直接包現有 chat-with-* skills，MCP 工具層直接複用 ^dg002wq

W-074 對 MCP 只做一件事：execution_constraint → tool 白名單 ^dg002xq

✗ ^dg002zq

Pi 偽造 COMPLETED → Guard 攔截 ＋ 產生 security 審計事件 ^dg0030q

零號專家 wrapper 驗收 —— 四條測試路徑（vitest） ^dg0032q

A 正常流：合法資料 → 專家執行 → completed（schema 銜接、EventStream 串鏈） ^dg0033q

B 逾時重試流：專家掛起 → SIGKILL → 指數退避 → 重試成功 ^dg0034q

C 毒藥訊息流：不合法 schema → 致命錯誤，直接 Failed 不重試 ^dg0035q

D 越權防護流：Pi 偽造 COMPLETED → Guard 攔截 → security 審計事件 ^dg0036q

三條架構紅線 —— 越過就不是這張圖了 ^dg0038q

① 核心不呼叫工具、不做相似度檢索 —— 工具在 harness 裡；驗收不能因為「沒撈到」而判錯 ^dg0039q

② 狀態轉換只走狀態機 —— LLM 只能產生建議，推狀態一律規則引擎；連 Pi 都推不動 ^dg003aq

③ 自我改進不准改：四態與轉換權限、一級紀錄必填欄位、驗收基準來源 —— 能改自己的驗收標準等於沒有驗收 ^dg003bq

EventStream（同一份資料，不另外埋點） ^dg003dq

自我改進迴路 —— 吃核心的資料，但不在核心裡 ^dg003fq

別人做這件事最貴的一步是標評測集；我們為了治理已經在生產了 ^dg003gq

Trace ^dg003iq

事件流本身 ^dg003jq

一案一條 trace ^dg003kq

Eval ^dg003nq

驗收準確率 ^dg003oq

一次過率 ^dg003pq

Diagnose ^dg003sq

歸因到 prompt / skill ^dg003tq

/ policy / 註冊表 ^dg003uq

Gate ^dg003xq

保留集重放 ^dg003yq

不退步才准上 ^dg003zq

Release ^dg0042q

版本化發布 ^dg0043q

改動寫回事件流 ^dg0044q

★ eval 資料是免費的：每跑完一次閉環就產生一筆（意圖, 交付物, verdict, 理由）； ^dg0045q

　 而人類每推翻主 Cheerio 一次（human_override）就是一筆黃金標籤 —— 這是自建 harness 真正的複利點 ^dg0046q

不准進這個迴路的東西：四態與轉換權限 ／ 一級紀錄必填欄位 ／ 驗收基準的來源（一律是意圖紀錄） ^dg0048q

理由：讓 agent 能改自己的驗收標準，等於沒有驗收 —— reward hacking 最典型的入口 ^dg0049q

MVP 只做 Trace（事件流已內建）；Eval 之後 4 階等六條驗收條件全過再說，但欄位現在就要留得住指標 ^dg004aq

五種 Engineering 落點：Prompt→orchestration 的 3 個 prompt ｜ Context→core 契約帶 rejected_alternatives ＋ experts 壓縮 ｜ ^dg004bq

　Harness→experts 的調用 / 觀測兩層契約 ｜ Loop→專家內部 ｜ Graph→orchestration 的 guarded 狀態機 ^dg004cq

四個未解問題（不擋 MVP，但實作時會撞到） ^dg004eq

1. Tier B 專家（chat-with-* skills）的逾時機制，怎麼跟 W-074 的 max_retries 銜接？第一條垂直切片會碰到 ^dg004fq

2. drizzle-zod 的 codegen 流程（Zod → Drizzle → migration）要實驗確認 ^dg004gq

3. Windows 上 child.kill(SIGKILL) 可能殺不掉子系命令，需 taskkill /F /T /PID 輔助 —— 直接影響測試路徑 B ^dg004hq

4. ExpertResult.traceId 跟 EventStream 在 Drizzle 層怎麼做外鍵 ^dg004iq

來源：2026-09-02 三方圓桌會議紀要（C:/Cheerio/.pi/round-table/20260902-141650/synthesis.md）｜四份 schema：Obsidian/work/designs/schemas/ ^dg004jq

%%
## Drawing
```compressed-json
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
```
%%