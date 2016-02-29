<properties
   pageTitle="整合應用程式與 Azure Active Directory | Microsoft Azure"
   description="如何在 Azure Active Directory (Azure AD) 中新增、更新或移除應用程式的詳細資料。"
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/24/2015"
   ms.author="mbaldwin" />

# 整合應用程式與 Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

企業開發人員和軟體即服務 (SaaS) 提供者可以開發可與 Azure Active Directory (Azure AD) 整合的商業雲端服務或企業營運應用程式，以提供安全的登入和授權給其服務。 若要整合應用程式或服務與 Azure AD，開發人員必須先使用 Azure 管理入口網站，向 Azure AD 註冊他們的應用程式相關詳細資料。

本文將說明如何在 Azure AD 中新增、更新或移除應用程式。 您將了解可與 Azure AD 整合的不同類型應用程式，以及如何設定您的應用程式存取其他資源，例如 web API 等等。

應用程式屬性的詳細資訊，請參閱 [應用程式與服務主體物件](active-directory-application-objects.md); 若要了解開發的應用程式與 Azure Active Directory，請參閱時，您應該使用的商標指導方針 [整合式應用程式的商標指導方針](active-directory-branding-guidelines.md); 應用程式資訊清單中會說明 [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)。

## 新增應用程式

任何想要使用 Azure AD 功能的應用程式都必須先在目錄中註冊。 此登錄程序牽涉到提供 Azure AD 應用程式的相關詳細資料，例如其所在的 URL、要在使用者驗證之後傳送回應的 URL，以及會識別應用程式的 URI 等。

如果您正在建置的 web 應用程式只需要在 Azure AD 中登入使用者，您只需依照下列指示。 如果您的應用程式需要存取 web API，您要建置原生應用程式需要存取 web API，或您想要讓您的應用程式的多租用戶，您必須繼續讀取 [更新應用程式](#updating-an-application) 一節，以繼續設定您的應用程式。

如果您想要提供應用程式給其他組織，您也必須在新增應用程式之後啟用外部存取。

### 在 Azure 管理入口網站登錄應用程式

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，並按一下想要的目錄。

1. 在頂端功能表上，按一下 [應用程式]。 如果您的目錄中尚未新增任何應用程式，此頁面僅會顯示新增應用程式連結。 按一下此連結，或者您可以按一下命令列上的 [新增] 按鈕。

1. 在 [您想做什麼] 頁面上，按一下此連結以新增我的組織正在開發的應用程式。

1. 在 [告知我們您的應用程式] 頁面上，您必須指定應用程式的名稱並指出您向 Azure AD 註冊的應用程式類型。  您可以選擇從 web 應用程式和/或 web API (預設值) 或原生用戶端應用程式 (代表安裝於電話或電腦等裝置上的應用程式)。一旦完成之後，按一下頁面右下角的箭頭圖示。

1. 在 [應用程式屬性] 頁面上，提供 web 應用程式的登入 URL 和應用程式識別碼 URI (或只提供原生用戶端應用程式的重新導向 URI)，然後按一下頁面右下角的核取方塊。

1. 已新增您的應用程式，而且您將會進入應用程式的 [快速啟動] 頁面。 根據您的應用程式為 web 或原生應用程式，您會看到如何將其他功能加入至應用程式中的不同選項。 一旦新增您的應用程式之後，您就可以開始更新您的應用程式，讓使用者登入、在其他應用程式中存取 web API 或設定多租用戶應用程式 (可讓其他組織存取您的應用程式)。

>[AZURE.NOTE] 根據預設，新建立的應用程式註冊被設定為允許使用者從您的目錄來登入您的應用程式。

## 更新應用程式

一旦您的應用程式已向 Azure AD 註冊，它可能需要更新，以提供 web API 的存取權、供其他組織使用等等。 本節描述如何進一步設定您的應用程式。 如需詳細資訊的方式驗證搭配 Azure AD 中運作，請參閱 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。

### 同意架構的概觀

Azure AD 的新同意架構可讓您更輕鬆地開發需要存取由 Azure AD 保護之 web API 的 web 及原生應用程式。 除了您自己的 web API 之外，這些 web API 包括圖形 API、Office 365 和其他 Microsoft 服務。 此架構以使用者或系統管理員為根據，他們可同意讓應用程式在他們的目錄中註冊，並可能包括存取目錄資料。 例如，如果 web 應用程式需要呼叫 Office 365 web API 來讀取使用者的行事曆資訊，該使用者必須同意此應用程式。 取得同意之後，web 應用程式可以代表使用者呼叫 Office 365 web API，並視需要使用行事曆資訊。

同意架構建置在 OAuth 2.0 和各種不同流程上，例如授權碼授與和用戶端認證授與，使用公用或機密的用戶端。 藉由使用 OAuth 2.0，Azure AD 就可以建置許多不同類型的用戶端應用程式，例如在電話、平板電腦、伺服器或 web 應用程式上，並且存取所需的資源。

如需詳細的同意架構的相關資訊，請參閱 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx), ，[Azure AD 的驗證案例](active-directory-authentication-scenarios.md), ，與 Office 365 主題 [驗證和授權使用一般的同意架構](https://msdn.microsoft.com/library/office/dn605895(v=office.15).aspx)。

#### 同意體驗的範例

下列步驟將示範如何將同意體驗用於應用程式開發人員和使用者。

1. 在 Azure 管理入口網站的應用程式組態頁面上，使用權限中的下拉式功能表，將您的應用程式所需的權限設定為其他應用程式的控制項。

    ![其他應用程式的權限](./media/active-directory-integrating-applications/permissions.png)

1. 請考量您的應用程式權限是否已更新、是否正在執行應用程式，以及使用者是否即將第一次使用它。 如果應用程式尚未取得存取或重新整理權杖，應用程式必須移至 Azure AD 的授權端點以取得授權碼，用來取得新的存取和重新整理權杖。

1. 如果使用者尚未通過驗證，系統會要求他們登入 Azure AD。

    ![使用者或系統管理員登入 Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. 使用者登入之後，Azure AD 會判斷是否需要向使用者顯示同意頁面。 此判斷根據使用者 (或其組織的系統管理員) 是否已經同意應用程式。 如果尚未授與同意，Azure AD 會提示使用者取得同意，並顯示其運作所需的必要權限。 [同意] 對話方塊中顯示的權限集，和在 Azure 管理入口網站的權限中選取做為其他應用程式控制項的內容相同。

    ![使用者同意體驗](./media/active-directory-integrating-applications/userconsent.png)

1. 使用者同意後，授權碼會傳回您的應用程式，藉以兌換取得存取權杖和重新整理權杖。 如需此流程的詳細資訊，請參閱 [Web 應用程式到 Web API 區段](active-directory-authentication-scenarios.md#web-application-to-web-api) 一節中 [Azure AD 的驗證案例](active-directory-authentication-scenarios.md)。

### 在其他應用程式中存取 Web API

使用上述的同意架構，您可以設定您的應用程式要求權限，以存取 web API 在您的目錄中所公開的資料。 根據預設，所有應用程式都可以利用已根據預設選取的 Azure AD 「啟用登入並讀取使用者的設定檔」權限，從 Azure Active Directory (圖形 API) 和 Azure 服務管理 API 選擇權限。 如果您的目錄也有 Office 365 訂用帳戶，則 web API 與 SharePoint 和 Exchange Online 的權限也可供選取。 您可以從所需 web API 旁之下拉式功能表中的兩種權限類型選取：

- 應用程式權限：您的應用程式本身需要直接存取 web API (沒有使用者內容)。 這種類型的權限需要系統管理員的同意，而且無法供原生用戶端應用程式使用。

- 委派權限：您的應用程式需要存取 web API 做為已登入的使用者，但其存取權受到選取權限的限制。 這種類型的權限可由使用者授與，除非權限設定為需要系統管理員的同意。

#### 在其他應用程式中新增 Web API 的存取

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，然後按一下想要的目錄。

1. 在頂端功能表上，按一下應用程式，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 在 [快速啟動] 的 [其他應用程式] 區段中展開存取 Web API，然後按一下 [選取權限] 區段下的立即設定連結。 應用程式屬性頁面會隨即出現。

1. 向下捲動至 [其他應用程式] 區段的 [權限]。 第一個資料行可讓您在公開 web API 的目錄中從可用應用程式中選取。  一旦選取之後，您就可以選取 web API 供開的應用程式和委派權限。

1. 一旦選取之後，按一下命令列上的 [儲存] 按鈕。

>[AZURE.NOTE] 您根據您設定其他應用程式的權限的目錄中，按一下 [儲存] 按鈕也會自動設定您的應用程式的權限。  您可以查看 [應用程式屬性] 索引標籤來檢視這些應用程式權限。

### 向其他應用程式公開 Web API

您可以開發 web API，並藉由公開權限範圍給其他應用程式開發人員，使其可供其他組織使用。 正確設定的 web API 即可供使用，就像其他 Microsoft web API 一樣，包括 圖形 API 和 Office 365 API。 您的 web API 所提供設定 [應用程式資訊清單](active-directory-application-manifest.md), ，即代表您的應用程式識別組態的 JSON 檔案。 您可以藉由瀏覽到您在 Azure 管理入口網站中的應用程式，並按一下命令列上的 [應用程式資訊清單] 按鈕，即可公開權限範圍。  如需詳細資訊，請參閱 [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)。

#### 向其他應用程式公開 web API

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，然後按一下想要的目錄。

1. 在頂端功能表上，按一下應用程式，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 按一下命令列中的 [管理資訊清單] 按鈕並選取 [下載資訊清單]。

1. 開啟 JSON 應用程式資訊清單檔並以下列 JSON 程式碼片段取代 "oauth2Permissions" 節點。 此程式碼片段是如何公開稱為使用者模擬的權限範圍，並確定您變更了應用程式的文字及值的範例：

        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in   user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            “origin”: “Application”
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],

    識別碼值必須是您使用 GUID 產生工具或以程式設計方式建立的新產生 GUID。 它代表由 web API 所公開之權限的唯一識別碼。 一旦您的用戶端已正確設定為要求存取您的 web API 並且呼叫 web API，它會顯示 OAuth 2.0 JWT 權杖，其範圍 (scp) 宣告已設為上述值，在此案例中為 user_impersonation。

    >[AZURE.NOTE] 您可以公開其他權限範圍稍後視需要。 請考慮您的 web API 可能會公開多個與各種不同功能相關聯的權限。 現在您可以使用範圍 (scp) 宣告在收到的 OAuth 2.0 JWT 權杖中控制 web API 的存取權。

1. 儲存更新的 JSON 檔案並將其上傳，方法是按一下命令列中的 [管理資訊清單] 按鈕、選取 [上傳資訊清單]、瀏覽至您的已更新資訊清單檔，然後選取它。 上傳之後，您的 web API 現在已設定為可供目錄中的其他應用程式使用。

#### 確認已向目錄中的其他應用程式公開 web API

1. 在頂端功能表上，按一下 [應用程式]、選取您想要設定 web API 存取權所需的應用程式，然後按一下 [設定]。

1. 向下捲動至 [其他應用程式] 區段的 [權限]。 按一下 [選取應用程式] 下拉式功能表，您就可以選取您只想對其公開權限的 web API。 從 [委派權限] 下拉式功能表，選取新的權限。

![顯示待辦事項清單權限](./media/active-directory-integrating-applications/listpermissions.png)

#### 應用程式資訊清單 JSON 檔案的 AppPermissions 結構描述

下表列出應用程式資訊清單 JSON 檔案中 oauth2Permissions 部分的可能值。

|元素|說明|
|---|---|
|adminConsentDescription|將滑鼠停留在系統管理員的 [同意] 對話方塊中，以及已獲得同意的應用程式屬性頁面中等說明描述。|
|adminConsentDisplayName|權限的易記名稱會顯示在應用程式和/或委派權限範圍下拉式清單中、在同意期間向系統管理員顯示，以及顯示於應用程式的屬性頁面中。|
|id|代表權限範圍的唯一內部識別碼。  它在應用程式的所有權限中必須是唯一的，而且它也必須是 GUID。|
|isEnabled|建立或更新 OAuth2 權限時，一律將其設為 true。 如果您想刪除權限，您必須先將此值設定為 false，並上傳資訊清單。 然後您可以在後續的資訊清單上傳中移除權限。|
|來源|保留以供日後使用。 一律設為「應用程式」。|
|類型|可以是下列值之一："User"：可由使用者同意 "Admin"：必須由公司系統管理員同意|
|userConsentDescription|將滑鼠停留在使用者的 [同意] 對話方塊中的說明描述。|
|userConsentDisplayName|權限的易記名稱會在同意期間向使用者顯示，並顯示於 [應用程式存取] 窗格的應用程式屬性頁面中。|
|value|如果使用者已同意此特定權限，這個值會置於 OAuth 2.0 存取權杖的 scp 宣告中。 Web API 可以在模擬使用者時使用此值來限定應用程式具有的存取層級範圍。 它不能包含任何空白字元，而且在應用程式中的所有權限裡必須是唯一的。|

### 存取圖形 API

本節描述如何更新您的應用程式以存取圖形 API (在其他應用程式的權限清單中稱為 "Azure Active Directory")。 根據預設，它可供所有已向 Azure AD 註冊的應用程式使用。 您可以利用圖形 API 從下列權限選取：

|權限名稱|說明|類型|
|---|---|---|
|啟用登入並讀取使用者的設定檔|允許使用者利用其組織帳戶登入應用程式，並讓應用程式讀取已登入使用者的設定檔，例如其電子郵件地址及連絡資訊。|僅限委派權限。 可由使用者同意。|
|存取您組織的目錄|允許應用程式代表已登入的使用者存取您組織的目錄。|僅限委派權限。 可由原生用戶端中的使用者同意，以及僅能由 web 應用程式的系統管理員同意。|
|讀取目錄資料|允許應用程式讀取您組織目錄中的資料，例如使用者、群組及應用程式。|委派和應用程式權限。 必須由系統管理員同意。|
|讀取和寫入目錄資料|允許應用程式在組織的目錄中讀取和寫入資料，例如使用者和群組。|委派和應用程式權限。 必須由系統管理員同意。|

針對 Azure 管理入口網站的現有使用者，將讀取目錄資料以及讀取和寫入目錄資料應用程式權限透過新的權限設定為其他應用程式的控制項，相當於先前的 [管理存取精靈]。  若要查看的權限範圍公開 Office 365 請參閱 [驗證和授權使用一般的同意架構](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks) 主題。

>[AZURE.NOTE] 由於目前的限制，原生用戶端應用程式，才能呼叫 Azure AD Graph API 使用 「 存取貴組織的目錄 」 權限。  這項限制不適用於 web 應用程式。

### 設定多租用戶應用程式

將應用程式新增至 Azure AD 時，您可以只讓組織中的使用者存取您的應用程式。 或者，您可能想讓外部組織的使用者存取您的應用程式。 這兩個應用程式類型稱為單一租用戶和多租用戶應用程式。 您可以修改單一租用戶應用程式的組態，使其成為多組用戶應用程式，將於本章節下方討論。

請務必注意單一租用戶與多租用戶應用程式之間的差異。 單一租用戶應用程式適合在一個組織中使用。 它們通常是由企業開發人員撰寫的企業營運 (LoB) 應用程式。 單一租用戶應用程式只需要由一個目錄中的使用者存取，因此，它只需要佈建在一個目錄中。 多租用戶應用程式適合在許多組織中使用。 它們通常是由獨立軟體廠商 (ISV) 撰寫的軟體即服務 (SaaS) 應用程式。 多租用戶應用程式需要佈建在將會用到它們的每個目錄中，而這需要使用者或系統管理員同意才能註冊。

#### 讓外部使用者授與存取權

如果您正在撰寫您想要提供給組織外的客戶或合作夥伴使用的應用程式，您必須在 Azure 管理入口網站中更新應用程式定義。

>[AZURE.NOTE] 當啟用外部存取，您必須確定您的應用程式的應用程式識別碼 URI 屬於已驗證的網域。 此外，傳回 URL 必須以 https:// 開頭。 如需詳細資訊，請參閱 [應用程式與服務主體物件](active-directory-application-objects.md)。

##### 讓外部使用者存取您的應用程式

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，然後按一下想要的目錄。

1. 在頂端功能表上，按一下應用程式，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 展開 [快速啟動] 的 [設定多租用戶應用程式] 區段，然後按一下 [啟用存取] 區段中的立即設定連結。 應用程式屬性頁面會隨即出現。

1. 按一下 [應用程式為多租用戶] 旁的 [是] 按鈕，然後按一下命令列上的 [儲存] 按鈕。

一旦您進行上述變更，其他組織中的使用者和系統管理員將可以讓您的應用程式存取他們的目錄和其他資料。

### 使用同意架構授與存取權

若要使用同意架構授與存取權，用戶端應用程式必須使用 OAuth 2.0 要求授權。 [程式碼範例](https://github.com/AzureADSamples) 也提供示範如何 web 應用程式、 原生應用程式或伺服器/服務精靈應用程式要求授權碼和呼叫 web Api 的存取權杖。

Web 應用程式可提供使用者的註冊體驗。 如果您已提供註冊體驗，預期使用者會按一下註冊 (或 [登入] 按鈕)，將瀏覽器重新導向至 Azure AD OAuth2.0 授權端點或 OpenID Connect userinfo 端點。 這些端點可讓應用程式藉由檢查 id_token 取得新使用者的相關資訊。

或者，您的 web 應用程式可能還會提供可允許系統管理員「註冊我的公司」的體驗。  這種體驗也會將使用者重新導向至 Azure AD OAuth 2.0 授權端點。 在此情況下，您也可以傳遞 prompt=admin_consent 參數來觸發系統管理員同意體驗，系統管理員會在其中代表其組織授與同意。 成功同意時，回應將包含 admin_consent=true。 兌換存取權杖時，您也將獲得提供組織資訊的 id_token，以及會註冊您的應用程式的系統管理員。

#### 啟用單一頁面應用程式的 OAuth 2.0 隱含授權。

單一頁面應用程式 (SPA) 通常會利用執行於瀏覽器中的 JavaScript-heavy 前端進行結構化，此前端會呼叫應用程式的 web API 後端以執行其商務邏輯。 針對裝載於 Azure AD 中的 SPA，您會使用 OAuth 2.0 隱含授權驗證具備 Azure AD 的使用者，並取得您可以使用的權杖以保護從應用程式的 JavaScript 用戶端到其後端 web API 的呼叫。 使用者授與同意權之後，這個相同的驗證通訊協定可用來取得權杖以保護用戶端和其他為應用程式設定之 web API 資源之間的呼叫。 根據預設，應用程式的 OAuth 2.0 隱含授權會停用。 您也可以設定您的應用程式啟用 OAuth 2.0 隱含授權 `oauth2AllowImplicitFlow`"' 中的值及其 [應用程式資訊清單](active-directory-application-manifest.md), ，即代表您的應用程式識別組態的 JSON 檔案。

##### 啟用 OAuth 2.0 隱含授權

1. 登入 Azure 管理入口網站。
1. 按一下 [ **Active Directory** 圖示，在左窗格中，然後按一下所需的目錄。
1. 在頂端功能表中，按一下 [ **應用程式**, ，然後按一下您想要設定應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。
1. 按一下 [ **管理資訊清單** 按鈕的命令列中，然後選取 **下載資訊清單**。
開啟 JSON 應用程式資訊清單檔案並將 "oauth2AllowImplicitFlow" 值設為 “true”。 根據預設，它是 “false”。

       "oauth2AllowImplicitFlow": true,

1. 儲存更新的 JSON 檔案，並按一下 [上載 **管理資訊清單** 命令列，選取 [中的] 按鈕 **上傳資訊清單**, ，瀏覽至您的更新資訊清單檔案，然後選取它。 上傳之後，您的 web API 現在已設定為使用 OAuth 2.0 隱含授權來驗證使用者。


### 授與存取權的舊版體驗

本節描述 2014 年 3 月 12 日之前的舊版同意體驗。 這種體驗仍受支援且如下所述。 在使用新功能之前，您只能授與下列權限：

- 登入其組織的使用者

- 登入使用者並讀取其組織的目錄資料 (僅限應用程式)

- 登入使用者並讀取與寫入其組織的目錄資料 (僅限應用程式)

您可以遵循的步驟中 [開發多租用戶 Web 應用程式與 Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) 授與 Azure AD 中註冊新的應用程式的存取權。 請務必注意，新的同意架構可允許更強大的應用程式，也可讓使用者 (而不只是系統管理員) 同意這些應用程式。

#### 建置授與存取權給外部使用者的連結 (舊版)

為了讓外部使用者使用其組織帳戶註冊您的應用程式，您必須更新您的應用程式，在 Azure AD 上顯示連結至頁面的按鈕，讓他們可以授與存取權。 討論這個註冊按鈕的商標指引 [整合式應用程式的商標指導方針](active-directory-branding-guidelines.md) 主題。 使用者授與或拒絕存取權之後，Azure AD 授與存取頁面會利用回應將瀏覽器重新導向回您的應用程式。 如需應用程式屬性的詳細資訊，請參閱 [應用程式物件和服務原則](active-directory-application-objects.md)。

授與存取頁面由 Azure AD 建立，您可以在管理入口網站的應用程式 [組態] 頁面中找到連結。 若要移至 [組態] 頁面，請按一下 Azure AD 租用戶頂端功能表中的應用程式連結、按一下您想要設定的應用程式，然後從 [快速啟動] 頁面的頂端功能表按一下 [設定]。

您的應用程式連結看起來像這樣：`http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`。 下表描述部分的連結：

|參數|說明|
|---|---|
|ClientId|必要。 新增應用程式期間取得的用戶端識別碼。|
|RequestedPermissions|選用。 應用程式要求的存取層級，將會向授與應用程式存取權的使用者顯示。 如果未指定，要求的存取層級會預設為僅單一登入。 其他選項為 DirectoryReaders 和 DirectoryWriters。 如需這些存取層級的詳細資訊，請參閱應用程式存取層級。|
|ConsentReturnUrl|選用。 您希望傳回授與存取回應的目標 URL。 此值必須以 URL 編碼，且必須和設定於應用程式定義中的回覆 URL 在相同的網域下。 如果未提供，授與存取回應將重新導向至已設定的回覆 URL。|

指定 ConsentReturnUrl 和回覆 URL 彼此獨立可讓您的應用程式實作獨立的邏輯，以處理和回覆 URL 不同之 URL 上的回應 (通常會處理登入的 SAML 權杖)。 您也可以在 ConsentReturnURL 編碼的 URL 中指定其他參數。這些參數會在重新導向時，以查詢字串參數的形式傳回至應用程式。  此機制可用來維護其他資訊，或將存取權授與的應用程式要求繫結至來自 Azure AD 的回應。

#### 授與存取權使用者經驗和回應 (舊版)

當應用程式重新導向至授與存取連結時，下列映像會顯示使用者會體驗的內容。

如果使用者尚未登入，它們將收到登入的提示：

![登入 AAD](./media/active-directory-integrating-applications/signintoaad.png)

一旦使用者通過驗證，Azure AD 會將使用者重新導向至授與存取頁面：

![授與存取權](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE] 只有外部組織的公司系統管理員可以授與應用程式存取權。 如果使用者不是公司系統管理員，他們將可以選擇傳送郵件給他們的公司系統管理員，以要求授與此應用程式的存取權。

客戶藉由按一下 [授與存取權] 將存取權授與您的應用程式，或者如果它們藉由按一下 [取消] 拒絕存取權之後，Azure AD 會傳送回應至 ConsentReturnUrl 或已設定的回覆 URL。 此回應包含下列參數：

|參數|說明|
|---|---|
|TenantId|Azure AD 中已將存取權授與您應用程式的組織的唯一識別碼。 只有在客戶已授與存取權時，才會指定此參數。|
|同意|如果存取權已授與應用程式，此值會設為「已授與」，如果此要求遭到拒絕，則設為「已拒絕」。|

如果其他參數指定做為 ConsentReturnUrl 編碼 URL 的一部分，它們會被傳回應用程式。 以下是授與存取要求的範例回應，指出已授權應用程式，且其包含授與存取要求中提供的 ContextID：`https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`。

>[AZURE.NOTE] 存取權授與回應將不會包含安全性權杖的使用者資訊。應用程式必須將使用者登入分開。

以下是存取授與要求遭到拒絕的範例回應：`https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### 不中斷地存取圖形 API 的輪流應用程式金鑰 (舊版)

在您的應用程式存留期間，您可能需要在呼叫 Azure AD 取得存取權杖以呼叫圖形 API 時變更您所使用的金鑰。  經常變更的金鑰分成兩類：當您的金鑰遭到入侵時的緊急變換，或是目前的金鑰即將到期的變更。 請遵循下列程序，在您重新整理金鑰時提供不中斷的存取權 (主要針對第二個案例)。

1. 在 Azure 管理入口網站中，按一下目錄租用戶、按一下頂端功能表的 [應用程式]，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 若要查看您的應用程式屬性清單，請按一下頂端功能表中的 [設定]，您就會看到您的金鑰清單。

1. 在金鑰底下，按一下顯示選取持續時間的下拉式清單，挑選 1 或 2 年，然後按一下命令列中的 [儲存]。 這會產生您應用程式的新密碼金鑰。 複製這個新的密碼金鑰。 此時現有和新的金鑰都可由您的應用程式用來從 Azure AD 取得存取權杖。

1. 回到您的應用程式並更新組態以開始使用新的密碼金鑰。 請參閱 [使用 Graph API 查詢 Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) where 的範例，應該會發生這項更新。

1. 您現在應該在整個生產環境啟用此變更 – 在其他所有服務節點上啟用此變更之前，先在一個服務節點上加以驗證。

1. 一旦在整個生產部署完成此更新之後，您就可以自由回到 Azure 管理入口網站並移除舊的金鑰。

#### 啟用存取之後變更應用程式屬性 (舊版)

一旦您讓外部使用者存取您的應用程式，您可能還是會繼續在 Azure 管理入口網站中變更您的應用程式屬性。 不過，在您變更應用程式前授與您的應用程式存取權的客戶，在 Azure 管理入口網站中檢視您的應用程式相關詳細資料時，將不會看到那些變更。 一旦應用程式可供客戶使用之後，您在進行某些變更時必須要非常小心。 例如，如果您更新應用程式識別碼 URI，在此變更之前授與存取權的現有客戶將無法使用其公司或學校帳戶登入您的應用程式。

如果您變更 RequestedPermissions 以要求更高的存取層級，現有客戶使用的應用程式功能目前已使用此提高的存取層級，利用新的 API 呼叫，所以現有客戶可能會從圖形 API 得到拒絕存取的回應。  您的應用程式應該處理這些案例，並要求客戶將存取權授與您要求提高存取層級的應用程式。

## 移除應用程式

本節描述如何從單一租用戶與多租用戶應用程式的目錄移除應用程式。

### 從您的目錄移除單一租用戶應用程式

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，並按一下想要的目錄。

1. 在頂端功能表上，按一下應用程式，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 按一下命令列中的 [刪除] 按鈕。

1. 在確認訊息處按一下 [是]。

### 從您的目錄移除多租用戶應用程式

1. 登入 Azure 管理入口網站。

1. 按一下左側功能表中的 Active Directory 圖示，並按一下想要的目錄。

1. 在頂端功能表上，按一下應用程式，然後按一下您想要設定的應用程式。 單一登入及其他組態資訊都會顯示 [快速啟動] 頁面。

1. 在應用程式是多租用戶] 區段中，按一下 [否]。 這會將轉換您的應用程式是單一租用戶，但應用程式仍會保留在組織已同意它。

1. 按一下命令列中的 [刪除] 按鈕。

1. 在確認訊息處按一下 [是]。

為了讓公司系統管理員移除應用程式對其目錄的存取權 (在授與同意之後)，公司系統管理員必須具有 Azure 訂用帳戶才能透過 Azure 管理入口網站移除存取權。 或者，公司系統管理員可以使用 [Azure AD PowerShell Cmdlet](http://go.microsoft.com/fwlink/?LinkId=294151) 要移除存取權。

## 後續步驟

- 請參閱 [整合的應用程式的商標指導方針](active-directory-branding-guidelines.md)

- 深入了解 [應用程式與服務主體物件](active-directory-application-objects.md)

- 了解 [Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)

- 請瀏覽 [Active Directory 開發人員指南](active-directory-developers-guide.md)

