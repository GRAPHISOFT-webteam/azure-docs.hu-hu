<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="您在 Azure AD B2C 預覽中可建置的應用程式類型。"
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>


# Azure AD B2C 預覽：應用程式類型

此文件簡要描述您可以建置的應用程式類型，不涉及您慣用的語言或平台。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 基本概念

應用程式註冊處理序會收集與指派一些值給您的應用程式：

- 可唯一識別應用程式的**應用程式 ID**
- 可用來將回應導回至應用程式的**重新導向 URI**
- 其他幾個狀況特定的值。

註冊完成後，應用程式即會向 Azure AD v2.0 端點傳送要求，以與 Azure AD 通訊：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

每個傳送至 Azure AD B2C 的要求都指定一個**原則**。 原則控制 Azure AD 的行為，還可讓您使用這些端點來建立一組可靈活自訂的使用者體驗。 一些常見的原則包括註冊原則、登入原則和設定檔編輯原則。

應用程式與 v2.0 端點的所有互動都會遵循類似高階的模式：

1. 
2. 使用者完成根據原則定義來完成原則。
4. 應用程式會從 v2.0 端點接收某種安全性權杖。
5. 應用程式使用此安全性權杖存取受保護的資訊或資源。
6. 資源伺服器會驗證安全性權杖，以確保可以授與存取權。
7. 應用程式會定期重新整理安全性權杖。


每個步驟根據您建置的應用程式類型而略有不同，而我們公開的原始碼程式庫會為您顧及細節。

## Web Apps

在 Azure AD B2C 的 OpenID Connect 實作中，Web 應用程式會向 Azure AD 發出驗證要求，以起始這些使用者體驗。

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```





![Web 應用程式泳道映像](./media/active-directory-b2c-apps/webapp.png)

使用接收自 Azure AD 的公開簽署金鑰來驗證 id_token，就足以確保使用者的身分識別，以及設定可在後續頁面要求中用來識別使用者的工作階段 Cookie。



除了簡易登入，Web 伺服器應用程式可能也需要存取一些後端 Web 服務。



## Web API

您也可以使用 Azure AD B2C 來保護 Web 服務，例如應用程式的 RESTful Web API。 Web API 可以使用 OAuth 2.0 保護其資料，並使用權杖來驗證傳入的 HTTP 要求。 Web API 的呼叫端會在 HTTP 要求的授權標頭中附加權杖：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

然後 Web API 會使用這個權杖來驗證 API 呼叫端的身分識別，並從編碼在權杖中的宣告擷取呼叫端的相關資訊。
> [AZURE.NOTE]
    Azure AD B2C 預覽目前僅支援以各自已知的用戶端存取的 Web API。 例如，完整的應用程式可能包括 iOS 應用程式、Android 應用程式和 Web API 後端。 完全支援這種架構。 目前不支援協力廠商用戶端 (例如另一個 iOS 應用程式) 存取相同的 Web API。 實際上，完整應用程式的每個元件必須全部共用單一應用程式識別碼。

Web API 接收的權杖可以來自所有類型的應用程式，包括 Web 應用程式、桌面和行動應用程式、單一頁面應用程式、伺服器端精靈，甚至是其他的 Web API。 範例：Web 應用程式呼叫 Web API 的完整流程。

![Web 應用程式 Web API 泳道映像](./media/active-directory-b2c-apps/webapi.png)





## 行動和原生應用程式

安裝在裝置中的應用程式 (如行動和桌面應用程式) 通常需要代替使用者存取後端服務或 Web API。

authorization_code 代表應用程式有權限代替目前登入的使用者呼叫後端服務。 然後應用程式就可以在背景中以 authoriztion_code 來兌換 id_token 和 refresh_token。 應用程式可以在 HTTP 要求中使用 id_token 向 Web API 驗證，也可以在舊的 id_token 過期時，利用 refresh_token 取得新的 id_token。
> [AZURE.NOTE]
    Azure AD B2C 預覽目前支援取得的 id_token，僅限於用來存取應用程式本身的後端 Web 服務。 例如，完整的應用程式可能包括 iOS 應用程式、Android 應用程式和 Web API 後端。 完全支援這種架構。 目前不支援 iOS 應用程式使用 OAuth 2.0 access_token 存取協力廠商 Web API。 實際上，完整應用程式的每個元件必須全部共用單一應用程式識別碼。

![原生應用程式泳道映像](./media/active-directory-b2c-apps/native.png)

## 目前的預覽版本限制

Azure AD B2C 預覽目前不支援這些類型的應用程式，但計劃於正式運作時提供支援。

### 單一頁面應用程式 (Javascript)

許多新式的應用程式都有單一頁面應用程式前端，以 javascript 編碼為主，而且通常使用 AngularJS、Ember.js、Durandal 等 SPA 架構。正式運作的 Azure AD 服務支援這些使用 OAuth 2.0 隱含流程的應用程式，但 Azure AD B2C 尚不提供此流程。 近期內可望提供。

### 精靈/伺服器端應用程式

包含長時執行處理序或不需要使用者操作的應用程式，也需他法存取受保護的資源，例如 Web API。 這些應用程式可以透過 OAuth 2.0 用戶端認證流程，利用應用程式身分識別 (而非使用者委派身分識別) 驗證及取得權杖。

Azure AD B2C 目前不支援此流程，也就是說，只有在進行互動式使用者流程之後，應用程式才能取得權杖。 近期內即會加入用戶端認證流程。

### Web API 鏈結 (代理者)

許多架構中都有一個 Web API 需要呼叫另一個下游 Web API，而兩者都受 Azure AD B2C 保護。 此情況常見於有 Web API 後端的原生用戶端，而後端會再呼叫 Azure AD 圖形 API 等 Microsoft 線上服務。

使用 OAuth 2.0 Jwt 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 預覽目前未實作代理者流程。




