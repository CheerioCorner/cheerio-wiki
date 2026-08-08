---
title: Plannotator 安裝手冊（Copilot CLI 整合）
source_type: pdf
source_file: "C:/Users/User/Downloads/安裝手冊.pdf"
original_date: 2026-07-23
extracted_date: 2026-08-08
extraction_method: markitdown (Microsoft)
---

# Plannotator 安裝手冊

> 來源：PDF 檔案 `安裝手冊.pdf`，2026-07-23 建立
> 提取工具：markitdown (Microsoft)

---

# Plannotator 安裝手冊（給長榮 IT 同仁自行安裝）

適用對象：已經在用 GitHub Copilot CLI／GitHub Copilot App 的同仁 適用平台：Windows

（PowerShell） 目的：在自己的電腦上裝好 Plannotator，讓 Copilot 在 plan mode 要退出時，自動跳出

瀏覽器讓你先審查、標註、核准或打回計畫，而不是盲目信任 AI 產生的計畫。 隱私預設：本手冊會順

手把「分享到雲端」功能關掉，全程只在 localhost 執行，不會把你的計畫內容送到外部伺服器。

方法一（推薦）：複製一段 Prompt，讓你的 Copilot 自己動手裝

安裝SKILL - plannotator-copilot-setup

npx skills@latest add https://github.com/EVACopilot/skills-itd-aas --skill
plannotator-copilot-setup

到 Copilot CLI／App 的對話輸入框（不是終端機），執行 Skill

/plannotator-copilot-setup

AI 會完成同方法二的 Prompt 內容後，會回報一份「你接下來要手動做的事」清單，照著做完第 3 步就完成安

裝了。

方法二（推薦）：複製一段 Prompt，讓你的 Copilot 自己動手裝

不用自己打指令，把下面整段複製貼到 Copilot CLI／App 的對話輸入框（不是終端機），送出就好：

請幫我在這台 Windows 電腦上安裝 Plannotator（一個給 AI coding agent 用的本機
Plan/Code Review 工具），
並完成以下設定：

1. 用 PowerShell 執行官方安裝腳本安裝執行檔本體：
   irm https://plannotator.ai/install.ps1 | iex
   裝完後幫我確認執行檔版本號有正確裝好。

2. 停用分享功能，避免我的計畫內容被送到 share.plannotator.ai：
   - 設定使用者層級環境變數 PLANNOTATOR_SHARE=disabled
   - 在 ~/.plannotator/config.json 寫入 { "share": "disabled" }（如果檔案已存在就合
併，不要整個覆蓋掉）

3. 完成後，用清單列出接下來還需要我自己手動做的事：
   - 要在這個對話視窗手動輸入哪兩行指令來安裝 GitHub Copilot CLI 專用外掛
（plannotator-copilot），
     或者要去 Settings → Plugins 按哪個按鈕操作
   - 提醒我裝完外掛後要「完整重新啟動」GitHub Copilot App（不是只開新的終端機分頁），

1 / 8

安裝手冊.md

2026-07-23

PATH 跟外掛才會生效
   - 教我怎麼用 Shift+Tab 進入 plan mode，驗證有沒有跳出 Plannotator 的審查頁面

請直接動手執行第 1、2 步（不要只是把指令印出來叫我自己貼），第 3 步等你做完 1、2 之後再列
給我看。

AI 做完第 1、2 步後，會回報一份「你接下來要手動做的事」清單，照著做完第 3 步就完成安裝了。

方法三：自己手動一步步裝（AI 幫不了忙、或想搞懂細節時用）

1. 安裝執行檔本體

打開 PowerShell：

irm https://plannotator.ai/install.ps1 | iex

跑完會看到 plannotator vX.Y.Z installed to C:\Users\<你的帳號

>\AppData\Local\plannotator\plannotator.exe， 並自動把該路徑加進 PATH（需要完整重啟 GitHub

Copilot App 才會生效，開新終端機分頁沒有用）。

2. 關閉分享功能（建議一定要做）

[Environment]::SetEnvironmentVariable("PLANNOTATOR_SHARE", "disabled", "User")

再到 C:\Users\<你的帳號>\.plannotator\config.json 建立（或編輯）成：

{ "share": "disabled" }

這樣「Export → Share」「Copy Share Link」等功能都會消失，確保計畫內容不會被送到

share.plannotator.ai。

3. 安裝 GitHub Copilot CLI 專用外掛

兩種方式擇一：

A. 對話框輸入 slash command（在跟 Copilot 聊天的輸入框，不是 PowerShell）：

/plugin marketplace add backnotprop/plannotator
/plugin install plannotator-copilot@plannotator

B. 用 GUI 介面：GitHub Copilot App → 左下角 Settings → Plugins → 右上角 + Install → 搜尋 plannotator

→ 安裝 plannotator-copilot 並打開啟用開關。

2 / 8

安裝手冊.md

2026-07-23

（兩種方式效果一樣，B 比較直覺、不用打指令；A 比較快。）

4. 完整重啟 GitHub Copilot App

3 / 8

安裝手冊.md

2026-07-23

不是重開一個分頁或終端機視窗，是整個 App 關掉、重新打開，PATH 跟剛裝的外掛才會生效。

5. 驗證安裝成功

重啟後，在對話裡按 Shift+Tab 進入 plan mode，讓 Copilot 產生一個計畫並準備退出 plan mode （呼叫

exit_plan_mode）——這時應該會自動跳出瀏覽器，顯示 Plannotator 的計畫審查頁面。 你可以在頁面上畫

線、留言、標記核准或打回，按 Send Feedback 後結果會直接回饋給 Copilot。

6. Obsidian × Plannotator 設定（已安裝 Obsidian 前提）

1. 選擇 Plan Mode 或直接指定 skill 後，任意給予一個 markdown 檔

/plannotator-annotate obsidian-plannotator-plannotator-plannot.md

2. 進行 Plannotator 設定

4 / 8

安裝手冊.md

2026-07-23

5 / 8

安裝手冊.md

2026-07-23

3. 將編輯好的檔案，存入Obsidian

6 / 8

安裝手冊.md

2026-07-23

常用指令（裝完之後）

指令

用途

/plannotator-review

審查目前未提交的程式碼變更，或貼 PR 網址審查

/plannotator-annotate <檔案路徑> 標註任一 Markdown / HTML / TXT...等檔案

/plannotator-last

標註 Copilot 最後一則回覆訊息

資安提醒

一定要做：關閉分享功能（方法一、二的第 2 步），避免計畫內容外流到官方雲端分享服務。

若要更謹慎，可以進一步改用「自架分享服務」（PLANNOTATOR_SHARE_URL /

PLANNOTATOR_PASTE_URL 指到內網網址），詳見團隊研究報告 research/plannotator-github-

copilt-app-vs-code-gi.md 第 5.2 節。

Plannotator 內建的「Ask AI」／自動程式碼審查功能，資料會直接送到你機器上已登入的 AI CLI 供應商

（Anthropic／OpenAI 等），不經 Plannotator 伺服器。若公司政策只核准 GitHub Copilot，不要額外安

裝並登入 claude／codex／pi／opencode 等個人帳號，這樣 Ask AI 側欄就不會出現其他供應商選項。

核心 Plan Review／Code Review／Annotate 三大功能全程只在 localhost 執行，官方隱私政策明講不

蒐集使用行為 telemetry。

常見問題

7 / 8

安裝手冊.md

2026-07-23

Q: 裝完之後 plannotator 指令說找不到？ A: 正常——PATH 是安裝腳本自動加的，但要完整重啟 GitHub

Copilot App（不是開新終端機）才會生效。

Q: /plugin install 這種指令要打在哪裡？ A: 打在跟 Copilot 聊天的輸入框，跟你平常打字問問題的地方一

樣，不是打在 PowerShell 或 VS Code 的終端機裡。

Q: 在 VS Code 裡可以用嗎？ A: 可以，但要另外裝 VS Code Marketplace 的 Plannotator 擴充套件

（backnotprop.plannotator-webview）， 而且要從 VS Code 的「整合終端機」裡打字啟動 copilot，而

不是用 VS Code 側邊原生的 GitHub Copilot Chat 面板—— 兩者是不同的整合機制，細節見團隊研究報告第 4

節。

Q: 想把這份安裝流程包成可重複使用的「Skill」？ A: 已經包好了，見 skills/plannotator-copilot-

setup/SKILL.md（命名特別標明 copilot，因為這份 skill 只針對 GitHub Copilot CLI／Copilot App，不是

給 Claude Code、Codex、Gemini CLI 等其他 agent 用的通用版）。 把整個 plannotator-copilot-setup 資

料夾複製到自己電腦的 %USERPROFILE%\.agents\skills\ 底下， 重啟 Copilot CLI 後，之後只要說「幫我裝

Plannotator」，AI 就會照著這份 skill 自動執行方法一的步驟。

參考資料

更完整的研究背景（架構原理、企業內部部署選項、已知限制）：research/plannotator-github-

copilt-app-vs-code-gi.md

官方文件：https://plannotator.ai/docs

原始碼：https://github.com/backnotprop/plannotator

8 / 8

