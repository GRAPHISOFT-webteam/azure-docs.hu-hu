<properties 
    pageTitle="部署及設定 SaaS 連接器 API 應用程式" 
    description="了解如何設定您從 Azure Marketplace 安裝在 Azure 訂閱中的 SaaS 連接器。" 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="tdykstra"/>


# 在 Azure App Service 中部署及設定 SaaS 連接器 API 應用程式

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## 概觀

本教學課程說明如何安裝、 設定和測試 [軟體-做為服務 (SaaS) 連接器](../app-service-logic-what-are-bizTalk-api-apps.md) 中 [Azure App Service](/documentation/services/app-service/) 呼叫它以程式設計的方式，例如從行動應用程式。 SaaS 連接器是 [API 應用程式](app-service-api-apps-why-best-platform.md) ，可簡化與 SaaS 平台，例如 Office 365、 Salesforce、 Facebook 和 Dropbox 的互動。 如果不使用預先封裝的連接器，您想要建立自訂的.NET API 應用程式，請參閱 [從 ASP.NET API 應用程式連接至 SaaS 平台](app-service-api-dotnet-connect-to-saas.md)。

例如，如果您想要編碼 HTTP 要求，以您的 Dropbox 帳戶讀取和寫入檔案，則直接使用 Dropbox 的驗證程序很複雜。 Dropbox 連接器會處理驗證的複雜性，以便您可以專注於撰寫商業特有程式碼。
> [AZURE.NOTE] 如果您想要在邏輯應用程式中使用 SaaS 連接器，就不需要遵循這裡的指示。如需有關如何使用 SaaS 連接器，在邏輯應用程式內的資訊，請參閱 [建立新的邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md) 和 [在連接器中使用自訂的 OAUTH 應用程式](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps&announcementId=4af1e4c5-d220-4457-97d8-d08e427ae6c1)。

本教學課程使用 DropBox 連接器作為範例，並逐步引導您完成下列步驟：

* Dropbox 連接器安裝 [資源群組](../resource-group-overview.md) Azure 訂閱中。
* 設定 Dropbox 連接器，以便它可以連接至 Dropbox 服務。 (若要完成此步驟，您將需要 Dropbox 帳戶。)
* 設定資源群組，以便只有通過驗證的使用者才能存取資源群組中包括的 API 應用程式。
* 測試以確認使用者驗證和 Dropbox 驗證兩者可以運作。

如需 App Service 中驗證的詳細資訊，請參閱 [API 應用程式和行動應用程式驗證](../app-service/app-service-authentication-overview.md)。

## 安裝 Dropbox 連接器

1. 移至 [Azure 預覽入口網站] 首頁上，按一下 [ **Marketplace**。

    ![Azure 預覽入口網站中的 Marketplace](./media/app-service-api-connnect-your-app-to-saas-connector/marketplace.png)

2. 搜尋 Dropbox，然後按一下 [**Dropbox 連接器**] 圖示。

    ![按一下 ](./media/app-service-api-connnect-your-app-to-saas-connector/searchdb.png)

3. 按一下 [建立]****。

    ![Click Create](./media/app-service-api-connnect-your-app-to-saas-connector/clickcreate.png)

5. 在 [**Dropbox 連接器**] 分頁的 [**App Service 方案**] 下，按一下 [**新建**]，然後在 [**建立新的 App Service 方案**] 方塊中輸入 DropBoxPlan。

    如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

4. 在 [**資源群組**] 下，按一下 [**新建**]，然後在 [**建立新的資源群組**] 方塊中輸入 DropboxRG。

    如需資源群組的詳細資訊，請參閱 [使用資源群組來管理您的 Azure 資源](../resource-group-overview.md)。

7. 選取免費的**定價層**。 (如果您在清單中看不到它，請按一下 [**檢視全部**]。 在按一下 [**F1 免費**] 之後，再按一下 [**選取**] 按鈕。)

    您可以使用已付費的定價層，但本教學課程不需要它。

11. 選擇接近您的**位置**。

9. <a id="gateway"></a>將預設"dropboxconnector 保留為 **名稱** 的連接器，然後按一下 **建立**。

    ![按一下 ](./media/app-service-api-connnect-your-app-to-saas-connector/createdropbox.png)

    Azure App Service 會建立一個資源群組，而且在資源群組中，它會建立一個 Dropbox 連接器 API 應用程式，以及一個*閘道* Web 應用程式。 閘道的功能就是管理如何存取資源群組中的所有 API 應用程式。

    您可以按一下 Azure 預覽入口網站首頁上的 [**通知**，來檢查資源的建立進度。

3. 當 Azure 完成連接器的建立時，請按一下 [**瀏覽 > 資源群組 > DropboxRG**]。

    DropboxRG 的 [**資源群組**] 分頁會顯示資源群組中的連接器和閘道。

    ![資源群組圖](./media/app-service-api-connnect-your-app-to-saas-connector/rgdiagram.png)

## 設定您的 Dropbox 帳戶和 Dropbox 連接器

若要讓 API 能夠存取您的 Dropbox 帳戶，您必須在 Dropbox 開發人員網站上建立 Dropbox 應用程式。 然後，將用戶端識別碼和用戶端密碼值從該 Dropbox 應用程式複製到您的 Dropbox 連接器，並將連接器設為只接受已驗證的要求。

### <a id="createdbapp"></a>建立 Dropbox 應用程式

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### <a id="copysettings"></a>複製到 Azure Dropbox 連接器，反之亦然 Dropbox 應用程式設定

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

### 將 Dropbox 連接器設為需要已驗證的存取

依預設，連接器的 [**存取層級**] 會設為 [**內部**]，這表示只有同一資源群組中的其他 API 應用程式和 Web 應用程式才能呼叫它。 但是，Dropbox 僅允許已驗證的使用者才能存取您的 Dropbox 帳戶，因此您必須將存取層級設定變更為需要使用者驗證。

1. 回到 [**設定**] 分頁，並按一下 [**應用程式設定**]。

2. 在 [**應用程式設定**] 分頁中，將 [**存取層級**] 設為 [**公用 (已驗證)**]，然後按一下 [**儲存**]。

    ![設為公用 (已驗證)](./media/app-service-api-connnect-your-app-to-saas-connector/pubauth.png)

您現在已設定 Dropbox 連接器，因此外撥可以存取您的 Dropbox 帳戶，而且來電必須來自已驗證的使用者。 在下一節，您將指定要使用哪一個驗證提供者來驗證使用者。

## 設定閘道

說明 [早](#gateway), ，閘道是特殊的 web 應用程式管理的資源群組中的所有 API 應用程式的存取。 若要設定閘道來驗證使用者，您可以選擇一個驗證提供者，例如 Azure Active Directory、Google 或 Twitter。 然後，使用者將必須利用選擇的提供者進行驗證，才能順利呼叫 Dropbox 連接器。

- 若要執行此步驟，請移至 [設定閘道](app-service-api-dotnet-add-authentication.md#configure-the-gateway) 區段 [保護 API 應用程式](app-service-api-dotnet-add-authentication.md) 教學課程中，並遵循該處的指示來設定閘道，在 DropboxRG 資源群組中。

## 測試以確認使用者和 Dropbox 驗證

您在 DropboxRG 資源群組中將閘道設為使用驗證提供者之後，就可以測試 Dropbox 連接器。

大部分時間，您將從程式碼呼叫連接器來使用它，而且我們也正在撰寫教學課程，顯示如何執行該動作。 但是，有時候您想要在佈線應用程式的其他部分之前驗證連接器是否正在運作中。 本教學課程顯示如何使用瀏覽器和簡單的 REST 用戶端工具，透過您剛安裝並設定的 Dropbox 連接器來確認您可以與 Dropbox 服務互動。

下列指示顯示如何使用 Chrome 瀏覽器開發人員工具和 Postman REST 用戶端工具，來執行這些步驟。 這只是範例而已，您也可以利用其他瀏覽器和工具來執行相同程序。 「進階 REST 用戶端」是另一個您可以使用的 Chrome 附加元件。

### 以使用者身分登入

在新的瀏覽器視窗中執行下列步驟。 視您正在使用的驗證提供者而定，您可能發現需要使用私人或匿名視窗。

2. 移至所設定之閘道和驗證提供者的登入 URL。 URL 遵循以下模式：

        http://[gatewayurl]/login/[providername]

    您可以取得閘道 URL，從 **閘道** 刀鋒視窗中 [Azure 預覽入口網站]。 (若要前往 [**閘道**] 分頁，請按一下 [**資源群組**] 分頁上顯示之圖表中的閘道。)

    ![閘道 URL](./media/app-service-api-connnect-your-app-to-saas-connector/gatewayurl.png)

    [providername] 值為 "facebook" (若為 Facebook)、"twitter" (若為 Twitter)、"aad" (若為 Azure Active Directory) 等等。

    這裡是 Azure Active Directory 的範例登入 URL：

        https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

3. 在瀏覽器顯示登入頁面時輸入您的認證。

    如果您設定 Azure Active Directory 登入，登入做為其中一個列出的使用者 **使用者** Azure Active Directory] 索引標籤的 [Azure 入口網站]，在您建立之應用程式索引標籤，例如 admin@contoso.onmicrosoft.com。

    登入成功時，您會得到 [登入完成] 頁面。

    ![](./media/app-service-api-connnect-your-app-to-saas-connector/logindone.png)

### 提供使用者的身分識別給 Dropbox

若要取得 Dropbox 授權來使用 Dropbox API，您必須提供使用者的認證給 Dropbox。 Azure 將為您起始該程序，但是若要觸發該程序，您必須移至瀏覽器中的特殊閘道 URL。 若要取得該 URL，請向閘道提出 HTTP Post 要求。

閘道的 HTTP Post 要求必須包括 Azure 在您登入時所提供的驗證權杖。 若要瀏覽器要求，包括權杖是自動的，因為權杖儲存在 Cookie 中，但是若為 HTTP Post 要求，使用 REST 用戶端工具時，您必須從 Cookie 取得權杖，並將它置於 HTTP Post 要求的要求標題中。

1. 在瀏覽器視窗中具有 「 登入完成 」 訊息，請移至瀏覽器的開發人員工具，並尋找 `x zumo 驗證` cookie。 將此視窗保持開啟狀態，以便您可以在下一個步驟複製 Cookie 的值。

    若要在 Chrome 取得 Cookie 值，請執行下列步驟：

    - 按 F12 來開啟開發人員工具。
    - 移至 [**資源**] 索引標籤。
    - 尋找您的閘道網站的 cookie，然後按三 **值** 的 `x zumo 驗證` cookie 來選取它的全部。 (確定您取得 Cookie 的所有值。 如果按兩下，可能只會取得它的第一個部分。)

    ![複製權杖](./media/app-service-api-connnect-your-app-to-saas-connector/copytoken.png)

4. 在新的瀏覽器索引標籤或視窗中，建立並傳送 HTTP Post 要求至閘道，以要求同意的 URL。 包含 `x zumo 驗證` 權杖作為 HTTP 標頭。

    URL 遵循以下模式：

        [gatewayurl]/api/consent/list?api-version=2015-01-14&redirecturl=[a URL you want the browser to go to after you authenticate]

    例如：

        https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/api/consent/list?api-version=2015-01-14&redirecturl=https://portal.azure.com

    若要在 Chrome 以 Postman 傳送要求，請執行下列步驟：

    - 輸入上述的**要求 URL**。
    - 將方法設為 **Post**。
    - 加入名為標頭 `x zumo 驗證`。
    - 標頭中 **值** 欄位貼上您從複製的值 `x zumo 驗證` cookie。
    - 按一下 [傳送]****。

    下圖顯示 Chrome 中的 Postman 工具：

    ![針對同意的 URL 傳送](./media/app-service-api-connnect-your-app-to-saas-connector/sendforconsent.png)

    回應包括一個 URL，用來起始利用 Dropbox 登入使用者的程序 (如果您看到錯誤回應，指出雖然已將 Get 方法下拉式清單設定為 [Post]****，但是不支援該方法，請確定您的閘道 URL 是 HTTPS，而非 HTTP)。

    ![同意的 URL](./media/app-service-api-connnect-your-app-to-saas-connector/getconsenturl.png)

2. 移至您收到的 URL，以回應 HTTP Post 要求。

    對此 URL 的回應，會將瀏覽器重新導向到 Dropbox 網站，在其中使用者會登入並將對使用者帳戶的存取權授與應用程式。

    登入和同意程序完成時，Dropbox 瀏覽器重新導向至您指定的重新導向 URL (例如，Azure 預覽入口網站，如果您遵循範例並使用 https://portal.azure.com)。 如果您是從 Web 應用程式呼叫，則會是 Web 應用程式中下一個要顯示的頁面。 應用程式應該檢查 URL，因為如果登入或同意程序時發生錯誤，可能包括重新導向 URL `錯誤` 查詢字串變數。

3. 將此瀏覽器保持開啟狀態，因為您將在下一節中使用它。

### 呼叫 Dropbox 連接器 API

Azure 現在正為您管理三個驗證權杖：

- 一個來自您已對閘道設定的驗證提供者，例如 Azure Active Directory。
- 一個來自 Dropbox。
- 一個則為 Azure 建立的 ("zumo" 權杖)。

當提出 HTTP 要求以使用 Dropbox 時您必須使用的唯一權杖為 zumo 權杖。 在幕後，Azure 已使該權杖與其他兩個權杖產生關聯，而且連接器在對 Dropbox 進行呼叫時會代表您提供其他兩個權杖。

在下列步驟中，您會向 Dropbox 連接器提出 Get 要求，以查看 Dropbox 帳戶中的檔案。 因為您可以使用瀏覽器視窗提出 Get 要求，而且因為您的瀏覽器視窗已有 zumo 權杖在 Cookie 中，所以您必須做的一切就是移至 URL，並取回 Dropbox 資料。

1. 在已開啟 Azure 預覽入口網站的瀏覽器視窗中，移回 Dropbox 連接器的 [**API 應用程式**] 分頁。

2. 複製 API 應用程式的 URL。

4. 按一下 [**API 定義**] 圖示，以查看可在連接器中使用的 API 方法。

    ![API 應用程式刀鋒視窗](./media/app-service-api-connnect-your-app-to-saas-connector/apiappblade.png)

    ![API 定義](./media/app-service-api-connnect-your-app-to-saas-connector/apidef.png)

2. 在您用來向閘道進行驗證的瀏覽器視窗中，呼叫從資料夾擷取檔案名稱的 GET 方法。 這裡是 URL 模式：

        [connectorurl]/folder/[foldername]

3. 例如，如果您的連接器 URL https://dropboxrg784237ad3e7.azurewebsites.net，而且您想要查看 blog 資料夾中的檔案，URL 會是:

        https://dropboxrg784237ad3e7.azurewebsites.net/folder/blog

    不同瀏覽器會以不同方式回應 API 呼叫；如果您是使用 Chrome，則會看到類似於下列範例的回應：

    ![取得資料夾要求的回應](./media/app-service-api-connnect-your-app-to-saas-connector/dbresponse.png)

## 後續步驟

您已了解如何安裝、設定和測試 SaaS 連接器。 如需詳細資訊，請參閱這些資源：

* [使用連接器](../app-service-logic/app-service-logic-connectors-list.md)
* [驗證 API 應用程式和行動應用程式](../app-service/app-service-authentication-overview.md)


[azure preview portal]: https://portal.azure.com/ 
[azure portal]: https://manage.windowsazure.com/ 

