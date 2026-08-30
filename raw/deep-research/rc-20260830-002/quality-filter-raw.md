{
  "answer": "{\n  \"remove\": [\n    {\n      \"title\": \"Application Access Policies (legacy) - Microsoft Learn\",\n      \"reason\": \"Exchange Online 的 Application Access Policies 屬舊版（legacy）功能，已被新版的「Role Based Access Control for Exchange Applications」（App RBAC）功能取代，官方文件載明不應再配置此政策，屬於已被後續更新資訊取代的過期資訊。\"\n    },\n    {\n      \"title\": \"Enable Automatic Key Rotation | TrendAI™ - Trend Micro\",\n      \"reason\": \"該頁面包含「生命週期結束通知」（End of Life Notice），指出 Cloud One - Conformity 已於 2026 年 7 月 31 日正式停止服務（End of Life），屬於針對已停產/下架產品的過期資訊。\"\n    },\n    {\n      \"title\": \"New-ApplicationAccessPolicy (ExchangePowerShell) | Microsoft Learn\",\n      \"reason\": \"該指令用於配置 Exchange Online 的舊版（legacy）Application Access Policies。官方文件明確載明該功能已被 App RBAC 取代，不應再建立新的政策，屬於過期且已被後續更新取代的資訊。\"\n    }\n  ]\n}",
  "question": "請逐一檢視這個 Notebook 目前的每一筆來源，找出符合以下任一條件、應該被移除的來源：\n1. 廣告／宣傳／行銷頁面（產品功能介紹首頁、展示頁、銷售或定價頁、無具體實作細節的推廣頁）\n2. 膚淺心得文（一味稱讚或一味批判、沒有具體論據或技術細節支撐；或簡短無結論的社群問答）\n3. 過期資訊（內容明顯針對已停產/已下架的產品或版本，或已被後續更新的資訊取代）\n\n即使是官方網域，只要頁面本身缺乏實質技術深度（例如官網行銷頁），一樣要列入移除，不能因為網域名稱就自動視為高品質。\n\n請只回傳一個 JSON 物件，不要有其他文字說明，格式如下：\n{\"remove\": [{\"title\": \"來源標題（要跟清單裡的標題完全一致）\", \"reason\": \"移除理由\"}]}\n如果沒有任何來源需要移除，回傳 {\"remove\": []}。",
  "conversation_id": "c4717132-90bb-422f-a9f7-93db8ab4f276",
  "sources_used": [],
  "citations": {},
  "references": []
}
