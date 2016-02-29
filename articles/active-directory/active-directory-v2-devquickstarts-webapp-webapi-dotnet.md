<properties
    pageTitle="App 模型 v2.0 .Net Web App|Microsoft Azure"
    description="如何建置使用個人 Microsoft 帳戶以及工作或學校帳戶登入，以呼叫 Web 服務的 .NET MVC Web 應用程式。"
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽：從 .NET Web 應用程式呼叫 Web API

> [AZURE.NOTE]
    此資訊適用於 v2.0 端點公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

有了 v2.0 應用程式模型，您就可以快速地將驗證加入 Web 應用程式和 Web API，同時支援個人 Microsoft 帳戶以及工作或學校帳戶。  我們將在此建置可執行下列作業的 MVC Web 應用程式：

- 借 Microsoft OWIN 中介軟體之力，使用 OpenID Connect 登入使用者。
- 使用 ADAL 取得 Web API 的 OAuth 2.0 存取權杖。
- 建立、讀取和刪除使用者「待辦事項清單」中的項目，其裝載於 Web API 並受 OAuth 2.0 保護。

本教學課程的重點主要是取得及使用存取權杖中所述的完整的 web 應用程式 [這裡](active-directory-v2-flows.md#web-apps)。  做為必要條件，您可能要先了解如何 [新增 web 應用程式的基本登入](active-directory-v2-devquickstarts-dotnet-web.md) 或如何 [適當保護 web API](active-directory-v2-devquickstarts-dotnet-api.md)。

從用戶端呼叫待辦事項清單 Web API 的基本步驟如下：

1. 註冊應用程式
2. 使用 OpenID Connect 將使用者登入 Web 應用程式
3. 使用 ADAL 於使用者登入時取得存取權杖
4. 以存取權杖呼叫待辦事項清單 Web API。

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet)。  

若要跟著做，您可以 [下載為.zip 的應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) 或再製基本架構:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

或者，您可以 [下載已完成的應用程式，以.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) 或再製已完成的應用程式:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## 1.註冊應用程式
建立新的應用程式在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ，或請遵循下列 [詳細步驟](active-directory-v2-app-registration.md)。  請確定：

- 複製 **應用程式識別碼** 指派給您的應用程式，您將需要它很快。
- 建立 **應用程式密鑰** 的 **密碼** 型別，並關閉它的值，供後續的複製
- 新增 **Web** 平台應用程式。
- 輸入正確 **重新導向 URI**。 重新導向 URI 會向 Azure AD 指出驗證回應應導向的位置，本教學課程的預設為 `https://localhost:44326/`。


## 2.使用 OpenID Connect 登入使用者
在這裡，我們將設定 OWIN 中介軟體使用 [OpenID Connect 驗證通訊協定](active-directory-v2-protocols.md#openid-connect-sign-in-flow)。  OWIN 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-   若要開始，請開啟 `TodoList-WebApp` 專案根目錄中的 `web.config` 檔案，並在 `<appSettings>` 區段中輸入應用程式的組態值。
    -    `ida:ClientId` 是 **應用程式識別碼** 指派給您的應用程式中註冊入口網站。
    -  `ida:ClientSecret` 是 **應用程式密鑰** 您在註冊入口網站中建立。
    -    `ida:RedirectUri` 是 **重新導向 Uri** 您輸入在入口網站。
- 開啟 `web.config` 檔案的根目錄中 `TodoList-Service` 專案，並取代 `ida:Audience` 具有相同 **應用程式識別碼** 與以上所述。


-   現在請將 OWIN 中介軟體 NuGet 封裝加入使用封裝管理員主控台的 `TodoList-WebApp` 專案。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

-   開啟檔案 `App_Start\Startup.Auth.cs`，並加入上述程式庫的 `using` 陳述式。
- 在相同檔案中實作 `ConfigureAuth(...)` 方法。  您在 `OpenIDConnectAuthenticationOptions` 中所提供的參數將會做為您的應用程式與 Azure AD 進行通訊的座標使用。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                    Scope = "openid offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
...
```

## 3.使用 ADAL 於使用者登入時取得存取權杖
在 `AuthorizationCodeReceived` 通知，我們想要使用 [一起使用 OpenID Connect 的 OAuth 2.0](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) 贖回 authorization_code 的待辦事項清單服務的存取權杖。  ADAL 可為您簡化這個程序：

- 首先，安裝 ADAL 預覽版本：

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease```
- 並加入另一個 `using` 陳述式來 `App_Start\Startup.Auth.cs` ADAL 的檔案。
- 現在加入新的方法， `OnAuthorizationCodeReceived` 事件處理常式。  此處理常式會使用 ADAL 取得待辦事項清單 API 的存取權杖，並將 ADAL 權杖中的權杖儲存起來以供日後使用：

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
        var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
}
...
```

- 在 Web Apps 中，ADAL 具有可延伸的權杖快取，可以用來儲存權杖。  這個範例會實作使用 http 工作階段存放區來快取權杖的 `NaiveSessionCache`。

<!-- TODO: Token Cache article -->


## 4.呼叫待辦事項清單 Web API
現在可以實際使用您在步驟 3 中取得的 access_token。  開啟 Web 應用程式的 `Controllers\TodoListController.cs` 檔案，此檔案可向待辦事項清單 API 提出所有 CRUD 請求。

- 這裡可再次使用 ADAL，從 ADAL 快取擷取 access_tokens。  首先，將 ADAL 的 `using` 陳述式加入這個檔案。

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- 在 `Index` 動作中，使用 ADAL 的 `AcquireTokenSilentAsync` 方法取得可用來讀取待辦事項清單服務資料的 access_token：

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- 此範例接下來會將產生的權杖加入 HTTP GET 要求當做 `Authorization` 標頭，讓待辦事項清單服務用來驗證要求。
- 如果待辦事項清單服務傳回 `401 Unauthorized` 回應，表示 ADAL 的 access_tokens 已因為某些原因而無效。  在此情況下，您應該卸除所有來自 ADAL 快取的 access_tokens，並向使用者顯示訊息告知可能需要再次登入，而這會重新啟動權杖的取得流程。

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
        foreach (TokenCacheItem tci in todoTokens)
                authContext.TokenCache.DeleteItem(tci);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- 同樣地，如果 ADAL 因為任何原因而無法傳回 access_token，您也應指示使用者再次登入。  這就像擷取任何 `AdalException` 一樣簡單：

```C#
...
catch (AdalException ee)
{
        // If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- `Create` 和 `Delete` 動作中執行完全一致的 `AcquireTokenSilentAsync` 呼叫。  在 Web 應用程式中，只要應用程式有需要，就可以使用這個 ADAL 方法取得 access_tokens。  ADAL 會為您取得、快取和重新整理權杖。

最後，建置並執行您的應用程式！  使用 Microsoft 帳戶或 Azure AD 帳戶登入，並注意上方導覽列中的使用者身分識別的反映狀態。  在使用者的 [待辦事項清單] 中加入和刪除某些項目，以查看執行中 OAuth 2.0 保護的 API 呼叫。  您的 Web 應用程式和 Web API 現在都使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

(不含您的設定值) 已完成的範例供您參考 [這裡提供](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip)。  

## 後續步驟

如需其他資源，請參閱：
- [應用程式模型 v2.0 預覽 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow"adal"標記 >>](http://stackoverflow.com/questions/tagged/adal)

