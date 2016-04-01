<properties 
    pageTitle="如何在 Azure API 管理中建立 API" 
    description="了解如何在 Azure API 管理中建立和設定 API。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="sdanie"/>

# 如何在 Azure API 管理中建立 API

API 管理中的 API 代表可供用戶端應用程式叫用的一組作業。 新的 API 是在發行者入口網站中建立，然後新增所需的作業。 加入操作之後，API 就可加入至產品，接著就可發佈。 API 發佈之後，就可供開發人員訂閱和使用。

本指南示範過程中的第一個步驟：如何在 API 管理中建立和設定新 API。 如需有關加入操作和發行產品的詳細資訊，請參閱 [如何將作業加入至 API][] 和 [如何建立和發行產品][]。

## <a name="create-new-api"> </a>建立新的 API

API 是在發行者入口網站中建立和設定。 若要存取發行者入口網站，按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **Api** 從 **API 管理** 左邊的功能表，然後按一下 **將 API 加入**。

![Create API][api-management-create-api]

使用 **加入新的 API** 視窗來設定新的 API。

![Add new API][api-management-add-new-api]

以下欄位可用來設定新 API。

-   **Web API 名稱** 提供 API 的唯一和描述性名稱。 它會顯示在開發人員和發行者入口網站中。
-   **Web 服務 URL** 參考實作 API 的 HTTP 服務。 API 管理則將要求轉送至此位址。
-   **Web API URL 尾碼** 附加至 API 管理服務的基底 URL。 基礎 URL 是 API 管理服務主控的所有 API 所共有。 API 管理依尾碼來區分 API，因此，特定發行者的每一個 API 必須有唯一的尾碼。
-   **Web API URL 配置** 決定哪些通訊協定可以用來存取 API。 預設會指定 HTTPs。
-   若要選擇性地將這個新的 API 加入產品中，按一下 [ **產品 （選擇性）** 下拉式清單並選擇產品。 可以重複此步驟多次來將 API 加入多個產品。

一旦設定所需的值，按一下 **儲存**。 建立新 API 之後，API 的摘要頁面隨即會顯示在發行者入口網站中。

![API summary][api-management-api-summary]

## <a name="configure-api-settings"> </a>設定 API 設定

您可以使用 **設定** ] 索引標籤來驗證和編輯 API 的組態。 **Web API 名稱**, ，**Web 服務 URL**, ，和 **Web API URL 尾碼** 最初設定時，API 會建立，而且可以修改這裡。 **描述** 提供選擇性描述，和 **Web API URL 配置** 決定哪些通訊協定可以用來存取 API。

![API settings][api-management-api-settings]

若要設定閘道驗證實作 API 的後端服務，請選取 **安全性** ] 索引標籤。  **認證** 下拉式清單可用來設定 **HTTP 基本** 或 **用戶端憑證** 驗證。 若要使用 HTTP Basic 驗證，只需要輸入所需的認證。 如需使用用戶端憑證驗證的詳細資訊，請參閱 [如何保護後端服務使用用戶端憑證驗證，在 Azure API 管理][]。

 **安全性** ] 索引標籤也可用來設定 **使用者授權** 使用 OAuth 2.0。 如需詳細資訊，請參閱 [如何授權在 Azure API 管理中使用 OAuth 2.0 的開發人員帳戶][]。

![Basic authentication settings][api-management-api-settings-credentials]

按一下 [ **儲存** 儲存您對 API 設定進行任何變更。

## <a name="next-steps"> </a>後續步驟

建立 API 並完成設定之後，接下來是將操作加入至 API、將 API 加入至產品，以及發佈它供開發人員使用。 如需詳細資訊，請參閱下列文章。

-   [如何將作業加入至 API][]
-   [如何建立和發行產品][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md

