# Deep Research 報告 — rc-20260830-002

- 研究主題：Enterprise API Security：OAuth 2.0 / OIDC 最佳實踐、Azure AD / Entra ID 權限管理、API Key 安全管理（rotation/rotation/scoping）、資料外洩防禦（DLP）、企業級 API Gateway 安全配置。以 Microsoft 生態系（Azure DevOps / SharePoint / Outlook）為主要場景。
- Notebook：https://notebooklm.google.com/notebook/5c819ecf-5de3-448c-83b4-f4e9e2ff0224
- 產生時間：2026-08-30T12:53:43.222Z
- 品質過濾：71 → 68 筆（移除 3 筆）
- 來源分類/重新命名：63 / 63 筆成功

## 研究結果

### 1. OAuth 2.0 在 AI Agent 場景下的安全考量？Token scope 設計、refresh token 管理、on-behalf-of flow 的風險？

在 AI Agent 與大語言模型（LLM）API 整合的企業級應用中，OAuth 2.0 與 OIDC 的安全設計已不僅僅是傳統的「使用者登入」，而是延伸至**多代理人協作、即時資料外洩防禦（DLP）以及 API 閘道（API Gateway）防禦**。以下針對您的問題，逐一剖析 Token Scope 設計、Refresh Token 管理與 On-Behalf-Of (OBO) 流程的核心安全考量：

---

### 一、 OAuth 2.0 在 AI Agent 場景下的整體安全考量

在 AI Agent 呼叫 LLM 服務時，API 閘道（如 Azure APIM、Solo agentgateway 或 LiteLLM）扮演著「深層防禦」的關鍵角色：
*   **深層防禦與閘道權杖驗證**：用戶端應用程式呼叫 AI 服務時，閘道必須透過 `validate-jwt` 或 `validate-azure-ad-token` 原則強制進行全域驗證與 API 範圍（Scope/Audience）檢查 [1-3]。不合法的權杖在抵達 LLM 後端前就會被阻擋 [2]。
*   **細粒度流量限制與配額（Rate Limiting & Quotas）**：為了防範 LLM 被濫用或遭遇阻斷服務攻擊，閘道可以從已驗證的 JWT 權杖中提取使用者主體（如 UPN 或 Email 宣告），並將其傳遞給 `llm-token-limit` 或 `azure-openai-token-limit` 原則，針對單一使用者設定每小時或每日的 Token 使用配額 [4, 5]。
*   **Fail-Closed（失敗關閉）機制**：當 AI 閘道整合 Microsoft Purview DLP 來防護 prompt 與 completion 時 [6, 7]，如果閘道無法成功取得權杖或無法與 Purview 連線，**必須採取 Fail-Closed 策略，直接回傳 503 錯誤，拒絕未經 DLP 評估的敏感內容流向 LLM** [8-10]。

---

### 二、 Token Scope 設計：從「過度授權」到「最小權限」

AI Agent 通常需要存取企業資源（如 Outlook、SharePoint、Teams），傳統 SaaS 應用經常因開發便利而要求廣泛的應用程式權限（Application Permissions，如 `Mail.ReadWrite`），這會為企業帶來極高的安全風險 [11, 12]：
1.  **委派權限（Delegated Permissions）優先**：
    *   在有登入使用者的場景下，**必須強制使用「委派權限」**，此時應用程式的實際存取權限是由「使用者本身的權限」與「被授予的 API 權限」取交集決定 [13, 14]。這能有效避免當 AI Agent 憑證遭竊取時，攻擊者可以存取組織內所有信箱或敏感文件的風險 [11, 15]。
2.  **AI Agent 專屬 Scope**：
    *   Microsoft 識別平台與 Graph API 已針對 AI 代理人定義了專屬權限，例如 `AgentInstance.Read.All` 與 `AgentInstance.ReadWrite.All`，用於安全管理組織 Agent 註冊表中的實例 [16, 17]；以及 `AiEnterpriseInteraction.Read`，專門用於在代表使用者時讀取企業與 AI 的互動歷程 [18]。
3.  **DLP 與安全原則整合 Scope**：
    *   若 AI Agent 整合了 Purview DLP API 以過濾內容 [6, 7]，則其 Entra ID 應用程式註冊應限制於專屬的內容處理與範圍計算權限，例如委派權限 `Content.Process.User`、`ProtectionScopes.Compute.User`，或應用程式權限 `Content.Process.All`、`ProtectionScopes.Compute.All` [19-22]。

---

### 三、 Refresh Token 的安全管理

Refresh Token 具備較長的生命週期，用於在 Access Token 到期後靜默換發新 Token [23-25]。在 AI 應用場景下，其安全管理要點如下：
*   **明確宣告 offline_access**：在 Microsoft 識別平台 v2.0 端點中，用戶端必須在初始化授權請求中明確加入 `offline_access` 範圍，才能取得 Refresh Token [26-28]。
*   **單頁應用程式（SPA）的 24 小時壽命限制**：
    *   如果 AI 介面是單頁 web 應用，其註冊的 Redirect URI 類型為 `spa` [29]。在這種情況下，**Microsoft 簽發的 Refresh Token 壽命將被強制限制為 24 小時** [30, 31]。當到期後，應用程式必須準備好重新引導使用者進行互動式驗證，以確保公共用戶端（Public Client）的憑證安全性 [31]。
*   **拋棄舊 Token 的單向輪轉（Token Rotation）**：
    *   當使用 Refresh Token 向 `/token` 端點換取新的 Access Token 時，識別平台通常會簽發一個新的 Refresh Token [31, 32]。**應用程式（或祕密金鑰快取）必須立即捨棄舊的 Refresh Token，並使用最新的 Token 進行覆蓋**，以降低 Token 被截獲重放的攻擊窗口 [31]。
*   **嚴禁在用戶端儲存 Client Secret**：對於需要長期維持存取權的後端 Agent 服務，必須使用機密用戶端（Confidential Client）流程，且祕密金鑰必須儲存在硬體或安全的 Key Vault 中，絕不能洩露給前端用戶端 [33, 34]。

---

### 四、 On-Behalf-Of (OBO) Flow 的風險與設計考量

當 AI Agent 閘道需要「代表」登入的使用者向後端 API（例如呼叫 Microsoft Graph 進行 DLP 檢查）發送請求時，會使用 On-Behalf-Of (OBO) 流程 [35, 36]。

```
[ 用戶端 (Client) ] ──( 攜帶使用者 Token )──> [ AI 閘道 (Gateway) ]
                                                   │
                                            ( OBO 憑證交換 )
                                                   ▼
[ 外部系統 (例如 Purview) ] <──( 攜帶 OBO Token )── [ Microsoft Entra ID ]
```

這個流程雖然能將活動正確歸屬到個人 [35]，但存在以下關鍵風險與相應的防護設計：

1.  **憑證雙重證明（Dual Proof）的要求與洩露風險**：
    *   **風險**：OBO 流程需要兩種憑證證明——「使用者的 Access Token（Asserted Token）」以及「閘道應用程式本身的 Client Secret 或憑證」 [37]。如果閘道的 Client Secret 洩露，攻擊者即可偽造閘道身分進行任意用戶的權杖交換。
    *   **防護**：閘道服務應採用 **Azure 受控識別（Managed Identity）** 或 OIDC 同盟身分憑證（Federated Identity Credentials）來取得自身權杖，徹底消除儲存、維護與手動輪轉靜態祕密金鑰的維運開銷與洩露風險 [38-41]。
2.  **不當讀取/解析 Token 宣告（Token Claim Decoding）**：
    *   **風險**：開發人員常試圖解密或讀取 Token 內部的 claim 來做為安全邊界。然而，Access Token 是專門簽發給 Resource Server（接收端 API）的，其格式可能並非標準 JWT，甚至可能隨時被識別平台加密或修改，導致解密邏輯崩潰 [42-44]。
    *   **防護**：**閘道與後端必須將 Token 視為不透明（Opaque）的字串** [45]。所有身分與權限的識別，應直接透過受支持且合規的 API（例如呼叫 Graph 的 `/me` 端點）來檢索，而非仰賴自行解析權杖宣告 [35, 45]。
3.  **無 Token 請求的 Fail-Closed 防護**：
    *   **風險**：在混和驗證環境下，若用戶端呼叫閘道時未攜帶使用者 Token，系統可能退而求其次，改用「閘道自身的應用程式憑證」代為呼叫後端服務。這會導致在後端審計日誌中，所有操作都變成閘道的系統服務帳戶（Service Account），失去個人歸屬性，並可能導致資料外洩 [10, 37]。
    *   **防護**：在 OBO 模式中，**若請求中缺失 end-user Token，或 OBO 的 STS（安全權杖服務）交換失敗，閘道必須立即拒絕請求（Fail-Closed）**，絕對不允許降級回閘道服務帳戶的權限來執行操作 [9, 10, 37]。

---

💡 **建議的下一步**：
我們是否可以針對您目前實作 AI Gateway 時所使用的架構（例如是使用 Azure API Management，還是其他第三方網關如 LiteLLM），來為您量身配置一份對應的 XML 政策或部署設定檔？

**引用來源：**
- [1] [[Microsoft 官方] 技術文件 - APIM 身分驗證與授權機制設計概述](https://learn.microsoft.com/en-us/azure/api-management/authentication-authorization-overview)
- [2] [[Microsoft 官方] 技術文件 - APIM 全域與 API 範圍 JWT 驗證策略配置](https://learn.microsoft.com/en-us/answers/questions/5789623/apim-policy-jwt-general-validation-on-global-scope)
- [3] [[Microsoft 官方] 技術文件 - APIM 保護與驗證 LLM API 存取最佳實踐](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-ai-apis)
- [4] [[Microsoft Q&A] 社群問答 - 使用 APIM 原則控管 Azure OpenAI API 呼叫](https://learn.microsoft.com/en-us/answers/questions/2279864/apim-manages-azure-openai-policies)
- [5] [[Microsoft Q&A] 社群問答 - 使用 APIM 原則控管 Azure OpenAI API 呼叫](https://learn.microsoft.com/en-us/answers/questions/2279864/apim-manages-azure-openai-policies)
- [6] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [7] [[LiteLLM] 產品文件 - LiteLLM 整合 Microsoft Purview 安全護欄機制](https://docs.litellm.ai/docs/proxy/guardrails/microsoft_purview)
- [8] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [9] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [10] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [11] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [12] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [13] [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [14] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [15] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [16] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [17] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [18] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [19] [[Kindo 官方] 產品文件 - 企業級應用程式 Microsoft Entra ID 授權機制](https://docs.kindo.ai/admin/enterprise-app-permissions/)
- [20] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [21] [[LiteLLM] 產品文件 - LiteLLM 整合 Microsoft Purview 安全護欄機制](https://docs.litellm.ai/docs/proxy/guardrails/microsoft_purview)
- [22] [[Microsoft 官方] 技術文件 - 在自訂應用程式中整合 Microsoft Purview 原則 API](https://learn.microsoft.com/en-us/purview/developer/use-the-api)
- [23] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [24] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [25] [[Microsoft 官方] 技術文件 - Microsoft 識別平台中 Scopes 與委派權限的設計](https://learn.microsoft.com/en-us/entra/identity-platform/scopes-oidc)
- [26] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [27] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [28] [[Microsoft 官方] 技術文件 - Microsoft 識別平台中 Scopes 與委派權限的設計](https://learn.microsoft.com/en-us/entra/identity-platform/scopes-oidc)
- [29] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [30] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [31] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [32] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [33] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [34] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [35] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [36] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [37] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [38] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [39] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [40] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [41] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [42] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [43] [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [44] [[Microsoft 官方] 技術文件 - 基於 Microsoft 識別平台的 OIDC 身分驗證整合](https://learn.microsoft.com/en-us/entra/identity-platform/v2-protocols-oidc)
- [45] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)

### 2. Azure AD / Entra ID 的 App Registration 安全最佳實踐？Conditional Access、Managed Identity、Service Principal 權限最小化？

針對 Microsoft Entra ID (Azure AD) 的應用程式註冊 (App Registration) 安全設計，企業應遵循「零信任 (Zero Trust)」與「最小權限 (Least Privilege)」原則。以下綜整來源文件，提供 App 註冊安全最佳實踐、受控識別 (Managed Identity)、服務主體 (Service Principal) 權限最小化，以及條件式存取 (Conditional Access) 的配置指引：

### 一、 App Registration 安全最佳實踐

1.  **無密碼與無密鑰化（Secret-Free / PAT-Free）驗證**
    *   傳統的個人存取權杖 (PAT) 不僅有長達一年的效期風險，且屬於不具身分安全防護的承載權杖 (Bearer Token) [1-3]。一經洩漏即面臨高度權限被濫用的風險 [1-3]。
    *   在 CI/CD 管線（如 Azure DevOps Pipelines）中，應全面淘汰靜態的 Service Principal Secret 或 PAT [4-6]。**推薦使用基於 OpenID Connect (OIDC) 的「工作負載身分識別同盟（Workload Identity Federation）」** [4, 7]。當管線執行時，系統會動態向 Entra ID 換取小時級到期的短期 Token，免除維護、儲存與手動輪轉靜態憑證的開銷 [8-10]。
2.  **憑證驗證（Certificate-based Auth）優先於密鑰**
    *   若因環境限制必須使用機密用戶端流程（Confidential Client Flow），應**優先上傳憑證（Certificate）進行認證**，而非建立 Client Secret [11, 12]。
    *   若必須使用 Client Secret，請注意 **Entra ID 的 Secret 最大壽命限制已被強制設為 2 年**，嚴禁設定不逾期 [13]。且祕密必須儲存在 Azure Key Vault 中 [2]，並利用結合 Managed Identity 與 Microsoft Graph 的自動化輪轉機制（Auto-rotation Policy）進行安全更替 [14, 15]。
3.  **啟用 Graph API 稽核與日誌監控**
    *   Entra ID 預設未開啟完整的 Graph API 活動記錄 [16]。應透過「診斷設定」**啟用 `MicrosoftGraphActivityLogs` 並傳送至 Azure Log Analytics 工作區** [16]。這允許安全團隊利用 KQL（Kusto Query Language）交叉比對權限宣告與實際呼叫，找出過度授權或閒置的應用程式註冊 [17, 18]。

---

### 二、 Managed Identity (受控識別) 最小權限與適用場景

受控識別可完全消弭開發人員管理憑證與金鑰的維運開銷，是 Azure 託管資源的首選認證方案 [10, 19]。

1.  **系統指派受控識別（System-Assigned Managed Identity）**
    *   **特性**：生命週期與特定 Azure 資源綁定，並隨資源刪除而自動清理 [20]。
    *   **場景**：最適合**單一資源部署**的背景服務（例如：單一 Azure Function 或單一 App Service 執行 API 閘道整合） [21, 22]。
2.  **使用者指派受控識別（User-Assigned Managed Identity）**
    *   **特性**：作為獨立 Azure 資源存在，可指派給多個 Azure 資源，生命週期獨立管理 [20]。
    *   **場景**：適合**多資源共享身分**，或者在建立 Azure DevOps Service Connection 與 Entra ID 聯邦信任關係時，作為持久且可重複使用的身分代理 [23, 24]。
3.  **Azure DevOps 中的權限配置**
    *   受控識別與 Service Principal 在 Entra ID 建立後，**不會自動在 Azure DevOps 中生效**，必須由 PCA（專案集合管理員）顯式加入至組織的使用者清單，給予 Basic 存取層級 [25, 26]，並在專案或資產層級（Repos、Feeds）指派最細粒度權限，遵循最小權限原則 [25, 27]。

---

### 三、 Service Principal 最小權限與精細化控管

當應用程式需要呼叫 Microsoft Graph 等企業資源時，安全漏洞常源於「過度授權（Over-permissioned）」[28, 29]。

1.  **委派權限 (Delegated) 優先於應用程式權限 (Application)**
    *   **委派權限**：在有登入使用者時使用，權限是「使用者本身權限」與「App 被授予權限」的交集（取交集限制了實際能力） [30-32]。
    *   **應用程式權限**：App 直接作為後端服務本身執行（無登入使用者），一旦被授予 `Mail.ReadWrite` 或 `Sites.ReadWrite.All` 等權限，即可存取**組織內所有信箱或所有網站 collection**，一旦憑證洩漏，攻擊者將獲得租戶級存取權 [30, 33-35]。
2.  **特定資源特異性同意（Resource-Specific Scoping）防禦**
    當業務流程迫不得已必須使用應用程式權限時，必須搭配特定 workload 進行範圍限制，嚴禁租戶級敞開：
    *   **SharePoint 限制（Sites.Selected）**：
        註冊 App 時不應授予全局的 `Sites.Read.All`，應**僅授予 `Sites.Selected` 應用程式權限** [36, 37]。接著，由 SharePoint 管理員透過 Graph/PowerShell 或 API 存取控制 UI，指派該 App 對**特定 Site Collection** 的唯讀 (Read) 或寫入 (Write) 角色 [36, 38, 39]。
    *   **Exchange Mailbox 限制（Application Access Policies）**：
        當 App 獲取 Graph Mail.Read 等應用程式權限時，Exchange 管理員必須執行 `New-ApplicationAccessPolicy` 指令，建立**應用程式存取原則**，將該 App 限制在特定安全組（Security Group）或指定信箱內 [40-42]，防止其窺探組織內高階主管或法務的機密信件 [43, 44]。
    *   **Teams 限制（Resource-Specific Consent, RSC）**：
        若要呼叫 Teams API，可透過 Teams App Manifest 宣告 RSC scopes [36, 41]。當團隊擁有者（Team Owner）在特定團隊中安裝此 App 時，代表該 App **僅被授權存取該特定團隊/頻道的資料**，無需全局管理員同意（Admin Consent）即可在細粒度範圍安全運作 [36, 41, 45]。
    *   **DLP 安全原則整合限制（Purview Policy API）**：
        當 AI 閘道（如 LiteLLM 代理）整合 Microsoft Purview DLP 來過濾 prompt 時，其註冊身分只需配置特定對象範圍的 `Content.Process.User`、`ProtectionScopes.Compute.User` 委派權限，以維護最低權限架構 [46-48]。

---

### 四、 條件式存取（Conditional Access, CA）

條件式存取不只限制人，也可以**套用至服務主體與應用程式註冊** [3, 49, 50]。
*   **進階上下文原則**：可以根據呼叫來源的地理位置、裝置合規狀態、來源網路（如限定受信任的企業 IP 範圍）以及即時的風險評估（Risk Factors）來實施防禦 [50]。
*   **阻斷非合規註冊**：藉由 CA 策略，安全團隊可以強制規定只有符合租戶合規要求的 App 註冊才能取得 STS 的 Access Token，若不合規一律拒絕發放，從源頭阻斷未經授權的第三方 SaaS 存取 [18]。

---

💡 **建議的下一步**：
為了協助您在組織內落實這些原則，我們是否可以使用 Bicep 或 Terraform，來為您建構一份包含「使用者指派受控識別（User-Assigned MI）」以及「無密碼工作負載同盟識別憑證（Federated Identity）」的 Azure DevOps IaC 部署範本？

**引用來源：**
- [1] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [2] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [3] [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [4] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [5] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [6] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [7] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [8] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [9] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [10] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [11] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [12] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [13] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [14] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [15] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [16] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [17] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [18] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [19] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [20] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [21] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [22] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [23] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [24] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [25] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [26] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [27] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [28] [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [29] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [30] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [31] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [32] [[GitHub] API 參考 - Microsoft Graph API 授權與權限參考文件 Markdown 來源](https://github.com/microsoft/microsoft-graph-docs-1/blob/main/concepts/permissions-reference.md)
- [33] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [34] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [35] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [36] [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [37] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [38] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [39] [[GitHub] API 參考 - Microsoft Graph API 授權與權限參考文件 Markdown 來源](https://github.com/microsoft/microsoft-graph-docs-1/blob/main/concepts/permissions-reference.md)
- [40] [[Reddit] 社群討論 - MS Graph PowerShell 管理員同意權限授權實務](https://www.reddit.com/r/MsGraphPowerShell/comments/1jqb9iq/admin_consent/)
- [41] [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [42] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [43] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [44] [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [45] [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [46] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [47] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [48] [[LiteLLM] 產品文件 - LiteLLM 整合 Microsoft Purview 安全護欄機制](https://docs.litellm.ai/docs/proxy/guardrails/microsoft_purview)
- [49] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [50] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)

### 3. API Key 如何安全儲存與輪替？Environment variable vs secret manager vs vault 的比較？

在企業級 API 與微服務架構中，API Key、連線字串等機密資訊的管理是資安防護的核心。以下結合資料來源，為您詳述 API Key 的安全儲存與輪替最佳實踐，並對環境變數、祕密管理器與 Key Vault 三種管理媒介進行深度比較：

---

### 一、 API Key 的安全儲存與自動輪替機制

#### 1. 安全儲存原則
*   **集中存放於專用保險箱**：API Key 絕不能寫死在程式碼或一般設定檔中，應集中存放於專用的金鑰管理服務（如 **Azure Key Vault**）[1]。
*   **網關安全 Named Value 整合**：在部署 API 閘道（如 Azure APIM）時，可利用 **Named Value（名稱值）** 作為祕密容器安全地存儲與引用 API Key [2]。對於更高強度的資安需求，應將 Named Value 引用指向 **Azure Key Vault** [3]。APIM 原則（Policy）即可在執行階段動態自 Named Value 提取 Key，並自動注入後端 API 請求的 `api-key` 或 `Authorization` 標頭中，避免憑證洩露 [2, 4]。
*   **開啟稽核軌跡 (Audit Trail)**：應針對儲存庫啟用診斷日誌，將所有的 `SecretGet`（讀取）、`SecretSet`（寫入）與 `SecretDelete`（刪除）事件送往 Log Analytics 或 SIEM，以供合規審計與異常偵測 [5]。

#### 2. 自動輪替機制 (Rotation)
*   **密鑰原生自動輪替 (Rotation Policy)**：對於加密金鑰（Cryptographic Keys），可直接在儲存庫中配置原生的 `rotationPolicy`。設定建立後幾天（如 `timeAfterCreate`）自動建立新版本金鑰，或在過期前（如 `timeBeforeExpiry`）自動輪轉並發送 Event Grid 通知 [6-8]。
*   **事件驅動秘密輪替 (Event-Driven Secret Rotation)**：由於 API Key 或連線字串等「秘密（Secrets）」通常無法由 Key Vault 原生對目標 API 進行密碼變更，因此必須採用事件驅動架構 [9, 10]。
    1.  **事件觸發**：當祕密接近過期時（例如到期前 30 天），Key Vault 會拋出 `SecretNearExpiry` 事件 [11]。
    2.  **自動化串接**：利用 **Event Grid** 訂閱該事件，並自動觸發專門的 **Azure Functions** 或自動化工作串流 [10, 12]。
    3.  **自訂輪轉邏輯**：Functions 內部的程式碼透過 Managed Identity 取得權限，呼叫目標服務的 Key 重置 API 生成新憑證，將新值寫回 Key Vault 作為新的祕密版本，並更新 Tag 中的中繼資料 [13, 14]。
*   **雙憑證交替機制 (Dual-Credential)**：在進行輪轉時（例如 Azure Storage 金鑰），採用雙金鑰（Key1 與 Key2）設計，一次只輪轉其中一個，並確保用戶端先平滑切換，之後再將舊版本停用，以達到零中斷（Zero-downtime）輪轉 [14, 15]。
*   **消費者無痛過渡設計**：
    *   **使用無版本 URI (Versionless URI)**：應用程式引用祕密時，應省略 URI 的版本 Hash，使其始終自動解析至 Key Vault 最新有效（Active）的版本，避免因鎖定（pinning）特定版本而在輪替時造成中斷 [16, 17]。
    *   **提供短暫的重疊視窗 (Overlap window)**：產生新祕密後，不應立刻將舊祕密設為 `disabled`。由於應用程式常有快取或連線池（Connection Pool）機制，應保留數分鐘到數小時的重疊期，讓消費者有時間更新 [18, 19]。
    *   **即時事件快取失效**：應用程式可向 Event Grid 訂閱 `SecretNewVersionCreated` 事件 [19]。當偵測到新祕密發布時，應用程式（例如搭配 Dapr 元件）能即時觸發 Webhook，清除內部快取並非同步重構（Reconfigure）連線池，完全無需重新啟動應用程式或容器 [19-21]。

---

### 二、 Environment Variable vs Secret Manager vs Vault 比較

依據系統架構與憑證生命週期管理，三者之定位與安全層級如下：

| 比較維度 | 環境變數 (Environment Variable) | 祕密管理器 (Secret Manager / Named Value) | 金鑰保險箱 (Key Vault / Dedicated Vault) |
| :--- | :--- | :--- | :--- |
| **安全層級** | **最低** <br>（常明文存在配置中） [22, 23] | **中等** <br>（在平台端加密並遮蔽） [2, 24] | **最高** <br>（支援軟刪除、清除保護與 HSM） [1] |
| **無祕密實踐 (Secret-Free)** | **無法實現** <br>（必須實體注入字串） [23, 25] | **部分支援** <br>（視平台原生配置而定） [24, 26] | **完全實現** <br>（結合 **Managed Identity**，程式不需持有任何憑證） [27, 28] |
| **輪替管理 (Rotation)** | **極難維護** <br>（一旦 Key 複製到多個應用程式中，容易淪為永久憑證） [22] | **手動或依賴管線更新** <br>（金鑰到期時仍可能因忘記更新管線變數而中斷） [29-31] | **自動化/事件驅動** <br>（支援原生金鑰輪替政策，或與 Event Grid+Functions 整合自動輪轉） [6, 10, 12] |
| **應用程式更新開銷** | **需重啟** <br>（通常需要 Pod 或應用程式重啟以載入新變數） [32] | **視平台而定** <br>（可能需透過部署管線重新發布以套用新變數） [30] | **動態載入（無痛）** <br>（可藉由 Versionless URI 動態獲取新版，或透過 Webhook 靜默重配置連線池） [16, 17, 21] |
| **稽核與合規 (Audit)** | **無稽核軌跡** <br>（無法追蹤是誰在何時讀取了該環境變數） [23] | **基礎稽核** <br>（僅記錄平台層級的變更或管線執行紀錄） [33] | **細粒度稽核日誌** <br>（詳實記錄 `SecretGet`, `SecretSet` 等每一筆金鑰調用資訊） [5] |

#### 深度解析：
1.  **環境變數 (Environment Variable)**：
    最大的痛點在於**「憑證散落（Secret Sprawl）」與「永久有效風險」** [22, 23]。當 API Key 被分散複製到多個應用程式設定中，不僅容易隨日誌、映像檔不慎外洩，當金鑰發生變更時，還需要逐一找出相依服務並重新啟動，維運開銷極大且合規風險最高 [22, 23, 32]。
2.  **祕密管理器 / 加密變數 (Secret Manager / Named Value)**：
    提供了「集中化遮蔽與管理」的第一步 [2, 24]。例如 APIM 中的 Named Values（秘密類型）或 CI/CD Pipeline 的 Secret Variables [2, 24]。它們解決了明文外洩的風險，但在輪替時，若是靜態的憑證，仍高度依賴手動介入或管線重跑，稍有不慎便會面臨憑證失效引發的故障 [29-31]。
3.  **專用保險箱 (Key Vault)**：
    屬於**零信任（Zero Trust）**架構下的終極首選 [1, 34]。透過與 **Managed Identity (受控識別)** 結合，應用程式運作時「不需要儲存或知道任何 Key Vault 密鑰」[27, 28]。所有的 API Key 的產生、設定過期時間、自動輪替政策、 Event Grid 整合與 Webhook 自行重新載入，均在安全隔離的生命週期內運作，並附帶完整的數據證據鏈（Evidence Trail），完美平衡了安全性、自動化與業務連續性 [5, 6, 12, 35]。

---

💡 **建議的下一步**：
由於許多第三方服務（例如 OpenAI API Key 或外部 SaaS 憑證）不支援金鑰保險箱的原生直接輪替策略，我們是否可以為您建構一個基於 **Azure Functions** 與 **Event Grid** 的自訂 API Key 輪替實作流程或範例程式碼，讓您可以實現對外部 API Key 的無縫自動輪轉？

**引用來源：**
- [1] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [2] [[Microsoft 官方] 技術文件 - APIM 保護與驗證 LLM API 存取最佳實踐](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-ai-apis)
- [3] [[Microsoft 官方] 技術文件 - APIM 保護與驗證 LLM API 存取最佳實踐](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-ai-apis)
- [4] [[Microsoft 官方] 技術文件 - APIM 保護與驗證 LLM API 存取最佳實踐](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-ai-apis)
- [5] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [6] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [7] [[Microsoft 官方] 技術文件 - 在 Azure Key Vault 中設定密碼金鑰自動輪轉](https://docs.azure.cn/en-us/key-vault/keys/how-to-configure-key-rotation)
- [8] [[OneUptime] 部落格 - 基於 Dapr 與 Azure Key Vault 實作微服務密碼輪轉](https://oneuptime.com/blog/post/2026-03-31-dapr-secret-rotation-azure-key-vault/view)
- [9] [[Microsoft Q&A] 社群問答 - Azure Key Vault 自動輪轉儲存體 SAS 權杖可行性分析](https://learn.microsoft.com/en-us/answers/questions/5565288/does-azure-key-vault-support-automatic-rotation-of)
- [10] [[OneUptime] 部落格 - 使用 Azure Functions 實作 Azure Key Vault 秘密自動輪轉](https://oneuptime.com/blog/post/2026-02-16-how-to-configure-azure-key-vault-secret-rotation-using-azure-functions-and-event-grid/view)
- [11] [[OneUptime] 部落格 - 使用 Azure Functions 實作 Azure Key Vault 秘密自動輪轉](https://oneuptime.com/blog/post/2026-02-16-how-to-configure-azure-key-vault-secret-rotation-using-azure-functions-and-event-grid/view)
- [12] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [13] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [14] [[OneUptime] 部落格 - 使用 Azure Functions 實作 Azure Key Vault 秘密自動輪轉](https://oneuptime.com/blog/post/2026-02-16-how-to-configure-azure-key-vault-secret-rotation-using-azure-functions-and-event-grid/view)
- [15] [[Microsoft Q&A] 社群問答 - Azure Key Vault 自動輪轉儲存體 SAS 權杖可行性分析](https://learn.microsoft.com/en-us/answers/questions/5565288/does-azure-key-vault-support-automatic-rotation-of)
- [16] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [17] [[Microsoft 官方] 技術文件 - 在 Azure Key Vault 中設定密碼金鑰自動輪轉](https://docs.azure.cn/en-us/key-vault/keys/how-to-configure-key-rotation)
- [18] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [19] [[OneUptime] 部落格 - 基於 Dapr 與 Azure Key Vault 實作微服務密碼輪轉](https://oneuptime.com/blog/post/2026-03-31-dapr-secret-rotation-azure-key-vault/view)
- [20] [[OneUptime] 部落格 - 基於 Dapr 與 Azure Key Vault 實作微服務密碼輪轉](https://oneuptime.com/blog/post/2026-03-31-dapr-secret-rotation-azure-key-vault/view)
- [21] [[OneUptime] 部落格 - 基於 Dapr 與 Azure Key Vault 實作微服務密碼輪轉](https://oneuptime.com/blog/post/2026-03-31-dapr-secret-rotation-azure-key-vault/view)
- [22] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [23] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [24] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [25] [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [26] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [27] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [28] [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [29] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [30] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [31] [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [32] [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [33] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [34] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [35] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)

### 4. 企業環境中，AI Agent 存取 SharePoint/Outlook/ADO 的權限該如何設計？最小權限原則的實作方式？

在企業環境中，設計 AI Agent 的存取權限時，直接授予「租戶級的全局授權（Tenant-wide Access）」是極高風險的安全隱憂 [1]。為落實零信任（Zero Trust）與最小權限原則（Least Privilege）[2-4]，針對 **SharePoint、Outlook（Exchange Online）以及 Azure DevOps（ADO）** 這三大 Microsoft 生態系核心場景，必須分別透過 **特定資源特異性同意（Scoping）、應用程式存取原則、以及無秘密受控識別** 來設計。

以下為您拆解各場景的精細化權限實作步驟：

---

### 一、 SharePoint Online 最小權限設計：實作 `Sites.Selected` 控制

如果 AI Agent 只需要讀寫特定的 SharePoint 網站（例如：某個特定部門的知識庫），在 Entra ID App 註冊中絕不能授予全局的 `Sites.Read.All` 或 `Sites.ReadWrite.All` [5-7]。

#### 1. 實作步驟：
1. **Entra ID 權限宣告**：在 AI Agent 的 Entra ID 應用程式註冊中，僅宣告並同意 **`Sites.Selected` 應用程式權限（Application Permission）** [8-13]。
2. **指定目標 Site 授權**：此時 AI Agent 還無法存取任何網站 [14]。必須由「租戶全域管理員」或擁有 `Sites.FullControl.All` 的管理員應用程式 [12, 14]，向特定的 Site Collection 發送 Graph API 請求進行顯式授權 [15]：
   * **API 端點**：`POST https://graph.microsoft.com/v1.0/sites/{site-id}/permissions` [15]
   * **Payload**：
     ```json
     {
       "roles": ["read"], 
       "grantedToIdentities": [{
         "application": {
           "id": "{AI-Agent-App-Client-ID}",
           "displayName": "AI-Knowledge-Agent"
         }
       }]
     }
     ```
     *(角色 `{role}` 可根據需求指派 `read`、`write`、`manage` 或 `fullcontrol` [16, 17])*

#### ⚠️ 關鍵安全盲點與防禦：
* **Graph Search API 的繞過風險**：**Graph 搜尋 API（`/search/query`）在 App-only 模式下「不支援」且會直接忽略 `Sites.Selected` 的限制 [18, 19]**。如果 AI Agent 使用搜尋 API，它會檢索到租戶內所有網站的索引 [19]。
* **解決方案**：AI Agent 在讀取或檢索特定 SharePoint 資料時，必須改用直接的 Drive 或 List APIs（例如 `/sites/{site-id}/drives/{drive-id}/root/children`）[20]，這些端點能確實被 `Sites.Selected` 安全地限制保護 [19, 20]。

---

### 二、 Outlook / 行事曆最小權限設計：配置 Exchange 應用程式存取原則

若 AI Agent 需要存取組織內的行事曆或特定服務信箱（如：自動讀取團隊信箱並自動建立工作項目），在過去一旦授予了 Graph `Mail.Read` 或 `Mail.Send` 的應用程式權限，該 App 就能存取租戶內「所有使用者」的信箱 [21, 22]。

#### 1. 實作步驟：
1. **Entra ID 權限宣告**：在 App 註冊中加入對應的 Microsoft Graph 應用程式權限（如 `Mail.Read` 或 `Mail.Send`），並完成管理員同意 [23-25]。
2. **建立郵件啟用安全群組（Mail-enabled Security Group）**：在 Exchange 管理中心或 Microsoft 365 管理中心，建立一個安全群組（例如：`AI-Allowed-Mailboxes`），並僅將 AI Agent 獲准存取的信箱/服務帳戶加入此群組中 [24, 26-28]。
3. **建立應用程式存取原則（Application Access Policy）**：使用 Exchange Online PowerShell 執行限制 [26, 29, 30]：
   ```powershell
   New-ApplicationAccessPolicy -AppId "{AI-Agent-App-Client-ID}" -PolicyScopeGroupId "AI-Allowed-Mailboxes@yourdomain.com" -AccessRight RestrictAccess -Description "限制 AI Agent 僅能存取特定群組內的信箱"
   ```
   * 使用 **`RestrictAccess`** 參數代表「僅允許」存取該群組成員的信箱，而拒絕其他所有信箱的存取 [31-33]。
4. **驗證與測試**：原則設定後，Exchange Online 約需要 **1 小時** 來進行快取同步 [34]。您可以使用以下指令即時測試原則是否生效 [1, 35, 36]：
   ```powershell
   Test-ApplicationAccessPolicy -Identity "user@yourdomain.com" -AppId "{AI-Agent-App-Client-ID}"
   ```

---

### 三、 Azure DevOps (ADO) 最小權限設計：無秘密整合受控識別與具現化授權

AI Agent 如果需要串接 Azure DevOps API（如：查詢 Boards 工作項、觸發 Pipelines），傳統上多依賴個人存取權杖（PAT）[37, 38]。然而 PAT 是與個人帳戶綁定的 Bearer 憑證，壽命長達一年，一旦洩漏將造成嚴重的供應鏈安全風險 [37-39]。

#### 1. 實作步驟：
1. **採用無密鑰身分驗證（PAT-Free）**：
   * **Azure 託管環境**（如運行於 Azure VM、AKS 或 Azure Functions 上的 AI Agent）：啟用並使用 **受控識別（Managed Identity）** [39-42]。
   * **非 Azure/多雲環境**：採用 **工作負載身分識別同盟（Workload Identity Federation）** [39, 43, 44]，透過 OIDC 短期權杖動態向 Entra ID 換取 Access Token [44-46]。
2. **在 Azure DevOps 中「具現化（Materialize）」該身分**：
   * 受控識別或服務主體（Service Principal）在 Entra ID 建立後，並不會自動存在於 Azure DevOps 中 [47, 48]。
   * 專案集合管理員（PCA）必須手動進入 **Organization Settings > Users**，點選 **Add users**，輸入該**服務主體的名稱或 Object ID**（注意：必須使用 **Enterprise Applications** 刀鋒視窗中的 **Object ID**，而非 App 註冊的 Client ID [49, 50]），並指派授權層級（通常選擇標準存取權的 **Basic**）[51, 52]。
3. **專屬權限隔離**：
   * **Azure DevOps 不使用 Entra ID 的「應用程式權限」**，而是使用 ADO 自身的安全和授權體系 [50, 53]。
   * 登入 ADO Portal，進入 **Project Settings > Permissions** [54]，將該服務主體/受控識別加入適當的 Project 安全群組（例如：僅加入特定專案的 Readers，或建立專屬的最小權限 Group [47, 51, 54]），徹底在專案、Repo 與 Pipeline 層級實施權限隔離 [47]。

---

### 四、 企業級 AI Agent 精細化防禦總結

| 整合場景 | 傳統全局風險權限 | 最小權限最佳實踐 (Least Privilege) | 控制技術關鍵 |
| :--- | :--- | :--- | :--- |
| **SharePoint** | `Sites.ReadWrite.All` | **`Sites.Selected`** | 使用 Graph `/permissions` 端點精細授權個別網站，避免呼叫全局 Search API [15, 19]。 |
| **Outlook / Exchange** | `Mail.ReadWrite` | **`RestrictAccess`** | Exchange `New-ApplicationAccessPolicy` + 郵件啟用安全群組隔離 [26, 29, 31]。 |
| **Azure DevOps** | 個人長期 `PAT` | **受控識別 / 工作負載同盟** | 在 ADO Organization 中顯式註冊受控識別之 **Enterprise App Object ID**，並使用 ADO 內部 ACL 控管 [47, 49, 50]。 |

---

💡 **建議下一步**：
我們是否可以針對您目前實施 AI Agent 的部署情境，為您產出一份對應的 **PowerShell 指令碼**，用於自動化配置 SharePoint 網站的 `Sites.Selected` 權限，或是 Exchange Online 的 `ApplicationAccessPolicy` 原則？

**引用來源：**
- [1] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [2] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [3] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [4] [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [5] [[CrushFTP] 部落格 - 運用 Sites.Selected 權限在 SharePoint 中落實低權限原則](https://www.crushftp.com/crush11wiki/Wiki.jsp?page=CrushTaskExample19)
- [6] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [7] [[Darwin Droll] 部落格 - 實踐 Microsoft Graph 中的 Sites.Selected 權限精細限制網站存取](https://www.darwindroll.com/blog/use-sitesselected-application-permission-in-microsoft-graph)
- [8] [[Reddit] 社群討論 - MS Graph PowerShell 管理員同意權限授權實務](https://www.reddit.com/r/MsGraphPowerShell/comments/1jqb9iq/admin_consent/)
- [9] [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [10] [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [11] [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [12] [[Darwin Droll] 部落格 - 實踐 Microsoft Graph 中的 Sites.Selected 權限精細限制網站存取](https://www.darwindroll.com/blog/use-sitesselected-application-permission-in-microsoft-graph)
- [13] [[GitHub] API 參考 - Microsoft Graph API 授權與權限參考文件 Markdown 來源](https://github.com/microsoft/microsoft-graph-docs-1/blob/main/concepts/permissions-reference.md)
- [14] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [15] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [16] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [17] [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [18] [[Microsoft Q&A] 社群問答 - 解決 Graph Search API 忽略 Sites.Selected 限制之安全異常](https://learn.microsoft.com/en-us/answers/questions/5731476/graph-search-api-returns-all-sites-despite-sites-s)
- [19] [[Microsoft Q&A] 社群問答 - 解決 Graph Search API 忽略 Sites.Selected 限制之安全異常](https://learn.microsoft.com/en-us/answers/questions/5731476/graph-search-api-returns-all-sites-despite-sites-s)
- [20] [[Microsoft Q&A] 社群問答 - 解決 Graph Search API 忽略 Sites.Selected 限制之安全異常](https://learn.microsoft.com/en-us/answers/questions/5731476/graph-search-api-returns-all-sites-despite-sites-s)
- [21] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [22] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [23] [[Reddit] 社群討論 - MS Graph PowerShell 管理員同意權限授權實務](https://www.reddit.com/r/MsGraphPowerShell/comments/1jqb9iq/admin_consent/)
- [24] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [25] [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [26] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [27] [[Humly 知識庫] 知識庫文件 - Azure Application Access Policies 系統配置指引](https://support.humly.com/azure-application-access-policies)
- [28] [[GrinMark] 部落格 - 配置 Exchange Online 應用程式存取原則保護信箱安全](https://www.grinmark4u.com/kb/application-access-policy-exchange-online.html)
- [29] [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [30] [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [31] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [32] [[deskbird 說明中心] 說明中心文件 - 使用存取原則限縮 Office 365 行事曆整合權限](https://help.deskbird.com/hc/en-us/articles/18024523296401-Control-access-to-Calendars-using-an-Application-Access-Policy)
- [33] [[deskbird 說明中心] 說明中心文件 - 使用存取原則限縮 Office 365 行事曆整合權限](https://help.deskbird.com/hc/en-us/articles/18024523296401-Control-access-to-Calendars-using-an-Application-Access-Policy)
- [34] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [35] [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [36] [[GrinMark] 部落格 - 配置 Exchange Online 應用程式存取原則保護信箱安全](https://www.grinmark4u.com/kb/application-access-policy-exchange-online.html)
- [37] [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [38] [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [39] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [40] [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [41] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [42] [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [43] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [44] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [45] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [46] [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [47] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [48] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [49] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [50] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [51] [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [52] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [53] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [54] [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)

## 已移除的來源（品質過濾）

- Application Access Policies (legacy) - Microsoft Learn — Exchange Online 的 Application Access Policies 屬舊版（legacy）功能，已被新版的「Role Based Access Control for Exchange Applications」（App RBAC）功能取代，官方文件載明不應再配置此政策，屬於已被後續更新資訊取代的過期資訊。
- Enable Automatic Key Rotation | TrendAI™ - Trend Micro — 該頁面包含「生命週期結束通知」（End of Life Notice），指出 Cloud One - Conformity 已於 2026 年 7 月 31 日正式停止服務（End of Life），屬於針對已停產/下架產品的過期資訊。
- New-ApplicationAccessPolicy (ExchangePowerShell) | Microsoft Learn — 該指令用於配置 Exchange Online 的舊版（legacy）Application Access Policies。官方文件明確載明該功能已被 App RBAC 取代，不應再建立新的政策，屬於過期且已被後續更新取代的資訊。

## 參考來源清單

- [[Cerbos 官方] 部落格 - APIM 結合 Cerbos 落實 API 策略即程式碼架構](https://www.cerbos.dev/blog/policy-as-code-azure-api-management-cerbos)
- [[CrushFTP] 部落格 - 運用 Sites.Selected 權限在 SharePoint 中落實低權限原則](https://www.crushftp.com/crush11wiki/Wiki.jsp?page=CrushTaskExample19)
- [[Darwin Droll] 部落格 - 實踐 Microsoft Graph 中的 Sites.Selected 權限精細限制網站存取](https://www.darwindroll.com/blog/use-sitesselected-application-permission-in-microsoft-graph)
- [[ENow] 部落格 - 限制 Microsoft Graph API 應用程式存取特定信箱之技術實務](https://www.appgovscore.com/blog/how-to-restrict-microsoft-graph-api-access-to-mailboxes)
- [[Firefly AI] 部落格 - 在 Azure DevOps Pipelines 中設定工作階段同盟識別憑證](https://www.firefly.ai/academy/integrating-federated-identity-credentials)
- [[GitHub] API 參考 - Microsoft Graph API 授權與權限參考文件 Markdown 來源](https://github.com/microsoft/microsoft-graph-docs-1/blob/main/concepts/permissions-reference.md)
- [[GitHub] API 參考 - Microsoft Graph processContent API 機密資訊保護參考原始碼](https://github.com/microsoftgraph/microsoft-graph-docs-contrib/blob/main/api-reference/v1.0/api/userdatasecurityandgovernance-processcontent.md)
- [[GrinMark] 部落格 - 配置 Exchange Online 應用程式存取原則保護信箱安全](https://www.grinmark4u.com/kb/application-access-policy-exchange-online.html)
- [[Humly 知識庫] 知識庫文件 - Azure Application Access Policies 系統配置指引](https://support.humly.com/azure-application-access-policies)
- [[Kindo 官方] 產品文件 - 企業級應用程式 Microsoft Entra ID 授權機制](https://docs.kindo.ai/admin/enterprise-app-permissions/)
- [[LiteLLM] 產品文件 - LiteLLM 整合 Microsoft Purview 安全護欄機制](https://docs.litellm.ai/docs/proxy/guardrails/microsoft_purview)
- [[Microsoft Q&A] 社群問答 - Azure Key Vault 自動輪轉儲存體 SAS 權杖可行性分析](https://learn.microsoft.com/en-us/answers/questions/5565288/does-azure-key-vault-support-automatic-rotation-of)
- [[Microsoft Q&A] 社群問答 - 使用 APIM 原則控管 Azure OpenAI API 呼叫](https://learn.microsoft.com/en-us/answers/questions/2279864/apim-manages-azure-openai-policies)
- [[Microsoft Q&A] 社群問答 - 使用 Sites.Selected 權限整合 SharePoint 端點驗證](https://learn.microsoft.com/en-us/answers/questions/5626253/endpoint-access-with-sites-selected-permission-for)
- [[Microsoft Q&A] 社群問答 - 偵錯 Purview DLP 策略與 Entra ID 整合中的行為不一致性](https://learn.microsoft.com/en-us/answers/questions/5883903/purview-dlp-and-entra-id-app-inconsistent-behavior)
- [[Microsoft Q&A] 社群問答 - 匯入 Azure Function App 至 APIM 時 JWT 策略應用之故障排除](https://learn.microsoft.com/en-us/answers/questions/5827298/trying-to-apply-jwt-policy-for-an-api-import-funct)
- [[Microsoft Q&A] 社群問答 - 解決 Graph Search API 忽略 Sites.Selected 限制之安全異常](https://learn.microsoft.com/en-us/answers/questions/5731476/graph-search-api-returns-all-sites-despite-sites-s)
- [[Microsoft 官方] API 參考 - APIM validate-jwt 權杖驗證原則說明](https://learn.microsoft.com/en-us/azure/api-management/validate-jwt-policy)
- [[Microsoft 官方] API 參考 - APIM 結構描述與內容驗證 validate-content 原則](https://learn.microsoft.com/en-us/azure/api-management/validate-content-policy)
- [[Microsoft 官方] API 參考 - APIM 鍵值限流機制 rate-limit-by-key 策略](https://learn.microsoft.com/en-us/azure/api-management/rate-limit-by-key-policy)
- [[Microsoft 官方] API 參考 - APIM 限流機制 rate-limit 策略參考](https://learn.microsoft.com/en-us/azure/api-management/rate-limit-policy)
- [[Microsoft 官方] API 參考 - APIM 驗證 Entra ID (Azure AD) 權杖策略](https://learn.microsoft.com/en-us/azure/api-management/validate-azure-ad-token-policy)
- [[Microsoft 官方] API 參考 - Azure CLI az keyvault key rotation-policy 原則命令詳解](https://learn.microsoft.com/en-us/cli/azure/keyvault/key/rotation-policy?view=azure-cli-latest)
- [[Microsoft 官方] API 參考 - Azure Key Vault 金鑰 IaC 配置與自動輪轉原則定義](https://learn.microsoft.com/en-us/azure/templates/microsoft.keyvault/2026-03-01-preview/vaults/keys)
- [[Microsoft 官方] API 參考 - Microsoft Graph userDataSecurityAndGovernance processContent API 定義說明](https://learn.microsoft.com/en-us/graph/api/userdatasecurityandgovernance-processcontent?view=graph-rest-1.0)
- [[Microsoft 官方] API 參考 - Microsoft Graph 權限與範圍詳細參考對照表](https://learn.microsoft.com/en-us/graph/permissions-reference)
- [[Microsoft 官方] 技術文件 - APIM 保護與驗證 LLM API 存取最佳實踐](https://learn.microsoft.com/en-us/azure/api-management/api-management-authenticate-authorize-ai-apis)
- [[Microsoft 官方] 技術文件 - APIM 全域與 API 範圍 JWT 驗證策略配置](https://learn.microsoft.com/en-us/answers/questions/5789623/apim-policy-jwt-general-validation-on-global-scope)
- [[Microsoft 官方] 技術文件 - APIM 身分驗證與授權機制設計概述](https://learn.microsoft.com/en-us/azure/api-management/authentication-authorization-overview)
- [[Microsoft 官方] 技術文件 - Azure DevOps 整合 Entra ID 服務主體與受控識別](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/support-azure-managed-identities)
- [[Microsoft 官方] 技術文件 - Azure DevOps 整合用身分驗證方法評估](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/authentication-guidance?view=azure-devops)
- [[Microsoft 官方] 技術文件 - Microsoft Entra ID 上的 OAuth 2.0 授權協議實作](https://learn.microsoft.com/en-us/entra/architecture/auth-oauth2)
- [[Microsoft 官方] 技術文件 - Microsoft Graph API 委派與應用程式權限管理](https://learn.microsoft.com/en-nz/answers/questions/5886574/microsoft-graph-api-permission)
- [[Microsoft 官方] 技術文件 - Microsoft Purview DLP 與 Power Automate 整合快速入門](https://learn.microsoft.com/en-us/purview/dlp-powerautomate-int-get-started)
- [[Microsoft 官方] 技術文件 - Microsoft Purview 擴充性框架與第三方安全合作夥伴](https://learn.microsoft.com/en-us/purview/purview-extensibility)
- [[Microsoft 官方] 技術文件 - Microsoft 識別平台中 Scopes 與委派權限的設計](https://learn.microsoft.com/en-us/entra/identity-platform/scopes-oidc)
- [[Microsoft 官方] 技術文件 - Power Platform 資料原則與 Microsoft Purview DLP 綜整比較](https://powerplatformengineer.com/power-platform-data-policies-vs-microsoft-purview-dlp/)
- [[Microsoft 官方] 技術文件 - 使用 APIM Validate-Jwt 原則驗證 az login 預設憑證](https://learn.microsoft.com/en-us/answers/questions/1504820/how-to-validate-an-azlogin-defaultcredential-jwt-t)
- [[Microsoft 官方] 技術文件 - 使用 Entra ID 工作負載身分識別存取 Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/add-devops-entra-service-connection?view=azure-devops)
- [[Microsoft 官方] 技術文件 - 在 Azure DevOps 中使用服務主體與受控識別存取資源](https://learn.microsoft.com/en-us/azure/devops/integrate/get-started/authentication/service-principal-managed-identity?view=azure-devops)
- [[Microsoft 官方] 技術文件 - 在 Azure Key Vault 中設定密碼金鑰自動輪轉](https://docs.azure.cn/en-us/key-vault/keys/how-to-configure-key-rotation)
- [[Microsoft 官方] 技術文件 - 在自訂應用程式中整合 Microsoft Purview 原則 API](https://learn.microsoft.com/en-us/purview/developer/use-the-api)
- [[Microsoft 官方] 技術文件 - 基於 Microsoft 識別平台的 OIDC 身分驗證整合](https://learn.microsoft.com/en-us/entra/identity-platform/v2-protocols-oidc)
- [[Microsoft 官方] 技術文件 - 深入理解 Azure Key Vault 加密金鑰自動輪轉之實作細節](https://learn.microsoft.com/en-us/azure/key-vault/general/autorotation)
- [[Microsoft 官方] 技術文件 - 深入解析 Entra ID OAuth 2.0 授權碼授權流程](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
- [[Microsoft 官方] 技術文件 - 理解 Microsoft Graph 及 SharePoint 的資源特異性同意 (RSC) 機制](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins-modernize/understanding-rsc-for-msgraph-and-sharepoint-online)
- [[Microsoft 官方] 技術文件 - 藉由 Microsoft Purview 審核 Power Platform 連接器活動稽核記錄](https://learn.microsoft.com/en-us/power-platform/admin/activity-logging-auditing/activity-logs-connectors)
- [[Microsoft 官方] 技術文件 - 運用 Azure AD B2C 來保護及限制 APIM API 的存取](https://learn.microsoft.com/en-us/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)
- [[Microsoft 官方] 技術文件 - 運用 Azure AD 限縮自動化整合中的應用程式權限範圍](https://devblogs.microsoft.com/identity/azure-ad-app-permission-scoping/)
- [[Microsoft 官方] 部落格 - Azure DevOps 支援服務主體與受控識別安全性更新](https://devblogs.microsoft.com/devops/introducing-service-principal-and-managed-identity-support-on-azure-devops/)
- [[OneUptime] 部落格 - 使用 Azure Functions 實作 Azure Key Vault 秘密自動輪轉](https://oneuptime.com/blog/post/2026-02-16-how-to-configure-azure-key-vault-secret-rotation-using-azure-functions-and-event-grid/view)
- [[OneUptime] 部落格 - 基於 Dapr 與 Azure Key Vault 實作微服務密碼輪轉](https://oneuptime.com/blog/post/2026-03-31-dapr-secret-rotation-azure-key-vault/view)
- [[Postman] API 參考 - 調用 Microsoft Purview API 列出所有租戶敏感度標籤](https://www.postman.com/api-evangelist/workspace/microsoft-purview/request/35240-b702c0ba-661d-40aa-98ac-e710203af4e1)
- [[Postman] API 參考 - 調用 Microsoft Purview API 取得租戶敏感度標籤](https://www.postman.com/api-evangelist/workspace/microsoft-purview/request/35240-b78ac0fe-5883-40a2-b98f-6eb7369ff0ed)
- [[Practical365] 部落格 - 在 Exchange Online 中設定應用程式存取原則](https://practical365.com/application-access-policies-in-exchange-online/)
- [[Pulumi 官方] 部落格 - 使用 Pulumi TypeScript 部署 APIM 自訂安全原則](https://oneuptime.com/blog/post/2026-02-16-deploy-azure-api-management-custom-policies-pulumi-typescript/view)
- [[Reddit] 社群討論 - MS Graph PowerShell 管理員同意權限授權實務](https://www.reddit.com/r/MsGraphPowerShell/comments/1jqb9iq/admin_consent/)
- [[Reddit] 社群討論 - 將 Purview DLP 與資訊保護延伸至非微軟第三方平台](https://www.reddit.com/r/microsoft365/comments/1vo1olk/expanding_purview_dlp_and_information_protection/)
- [[Reddit] 社群討論 - 針對 SaaS 要求的過度授權進行 Entra ID 權限限縮的有效策略](https://www.reddit.com/r/sysadmin/comments/1rrx7uq/whats_up_with_all_these_saas_wanting_such_broad/)
- [[Safeguard] 部落格 - Azure Key Vault 秘密輪轉最佳實踐與安全設計](https://safeguard.sh/resources/blog/best-practices-for-rotating-secrets-stored-in-azure-key-vault)
- [[Solo Enterprise] 產品文件 - Microsoft Purview DLP 代理閘道整合與安全策略](https://docs.solo.io/agentgateway/2026.7.1/llm/guardrails/purview-dlp/)
- [[Turbot] 技術文件 - 檢驗 Azure Key Vault 自動金鑰輪轉之 CIS v2.0 合規策略](https://hub.guardrails.turbot.com/mods/azure/policies/azure-cisv2-0/r0808Attestation)
- [[deskbird 說明中心] 說明中心文件 - 使用存取原則限縮 Office 365 行事曆整合權限](https://help.deskbird.com/hc/en-us/articles/18024523296401-Control-access-to-Calendars-using-an-Application-Access-Policy)
