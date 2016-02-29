<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="如何建置使用 Azure AD B2C 呼叫 Web API 的 Web 應用程式。"
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="dastrock"/>

# Azure AD B2C 預覽：從 .NET Web 應用程式呼叫 Web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至 Web 應用程式和 Web API，只要幾個簡短步驟即可完成。  這篇文章將示範如何
若要建立.NET MVC 「 待辦事項清單 」 web 應用程式呼叫.NET web API 使用 OAuth 2.0 持有人權杖。  Web 應用程式和 web api 使用 Azure AD B2C 管理使用者的身分識別
和驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。  而會著重在如何在使用者已通過驗證後呼叫 Web API。
如果您還沒有這麼做，您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md) 來了解 Azure AD B2C 的基本概念。

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。  目錄為所有使用者、應用程式、群組等項目的容器。  如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。  這兩個 web 應用程式和
web API 會由單一 **應用程式識別碼** 在此情況下，因為它們組成一個邏輯應用程式。  若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。  請務必

- 包含 **web 應用程式/web api** 應用程式中
- 輸入 `https://localhost:44316/` 為 **回覆 URL** -它是此程式碼範例的預設 URL。
- 建立 **應用程式密碼** 應用程式並將它複製下來。  稍後您將會用到此資訊。
- 複製 **應用程式識別碼** ，其指派給您的應用程式。  稍後您也會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [**原則**](active-directory-b2c-reference-policies.md)。  此 web 應用程式包含三種 
識別發生位註冊、 登入，並編輯設定檔。  您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。  建立您的三個原則時，請務必：

- 選擇 **顯示名稱** 和您註冊的原則中的其他幾個註冊屬性。
- 選擇 **顯示名稱** 和 **物件識別碼** 應用程式宣告每個原則。  您也可以選擇其他宣告。
- 複製 **名稱** 的每個原則建立之後。  其前置詞應該為 `b2c_1_`。  稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)] 

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。  如果您想要深入了解在 Azure AD B2C，原則的運作方式
您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## 4.下載程式碼

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet)。  當您建置範例，您可以 
[下載為.zip 的基本架構專案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip) 或再製基本架構:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

完成應用程式也是 [成為.zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) 或
`complete` 相同的儲存機制分支。

下載範例程式碼後，請開啟 Visual Studio `.sln` 檔案開始進行。  您會看到方案中有兩個專案：`TaskWebApp` 專案和 `TaskService` 專案。   `TaskWebApp` 是 WPF 
web 應用程式前端使用者進行互動。  `TaskService` 是應用程式的後端 Web API，儲存每個使用者的待辦事項清單。 

## 5.設定工作服務

當 `TaskService` 收到 `TaskWebApp` 的要求時，它會檢查存取權杖是否有效可驗證要求。  若要驗證的存取語彙基元 
您必須提供 `TaskService` 一些您的應用程式相關資訊。  在 `TaskService` 專案中，開啟 `web.config` 根目錄中的檔案 
在專案中的值取代和 `<appSettings>` 區段:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```
  
[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


本文不會介紹保護 `TaskService` 的詳細資料。  如果您想要了解 web API 如何安全地驗證使用 Azure AD B2C 要求，請參閱我們
[Web API 快速入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## 6.設定工作 Web 應用程式

為了讓 `TaskWebApp` 與 Azure AD B2C 通訊，您必須提供一些通用參數。  在 `TaskWebApp` 專案中，開啟
`web.config` 檔案位於專案的根目錄，並中的值取代 `<appSettings>` 一節。  這些值將用於整個 Web 應用程式。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```     

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

另外有兩個 `[PolicyAuthorize]` 裝飾項目，需要在其中提供您的登入原則名稱。   `[PolicyAuthorize]` 屬性用來叫用特定的
當使用者嘗試存取的網頁需要驗證的應用程式中的原則。

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## 7.取得存取權杖和呼叫工作 API

本節將示範如何在 Web 應用程式中使用 Microsoft 的程式庫和架構完成 OAuth 2.0 權杖交換。  如果您是
熟悉 **授權碼** 和 **的存取權杖**, ，它可能是個不錯的主意，瀏覽 [OpenID Connect 通訊協定參照](active-directory-b2c-reference-protocols.md)。

#### 取得授權碼

呼叫的第一個步驟 `TaskService` web API 是以驗證使用者，並接收 **授權碼** 從 Azure AD。
在任何原則成功執行之後 ，包括登入、註冊和編輯設定檔原則，您可以從 Azure AD 收到授權碼。

首先，請使用 Visual Studio Package Manager Console 安裝 OWIN OpenID Connect 中介軟體。 我們將使用 OWIN 來傳送驗證
Azure ad 要求並處理其回應:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

開啟檔案 `App_Start\Startup.Auth.cs`。  這可讓我們將設定 OWIN 驗證管線中，提供您 B2C 目錄的詳細資料
與您所建立的應用程式:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
        {
            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            },
            Scope = "openid offline_access",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
    }
    ...
}
```

#### 使用授權碼取得存取權杖

您的 Web 應用程式現在已設定為使用您的 B2C 目錄驗證使用者，並從 Azure AD 取回授權碼。
下一步是使用授權碼向 Azure AD 兌換存取權杖。 

每當您的.NET web 應用程式需要從 Azure AD 取得存取權杖時，您可以使用 **Active Directory 驗證程式庫 (ADAL)**。  
您不需要此程序，使用 ADAL 但 ADAL 可讓您更輕鬆為您的許多詳細資料，例如傳送 OAuth
2.0 驗證訊息，快取和重新整理權杖。

首先，再一次使用 Package Manager Console，將 ADAL 安裝到 `TaskWebApp` 專案中：

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

現在您需要將授權碼傳遞給 ADAL，讓它為您取得權杖。  OWIN OpenID Connect 中介軟體會提供通知
您可以使用此授權碼-通知將引發每次您的應用程式接收來自 Azure AD 的授權碼。
在 `App_Start\Startup.Auth.cs` 中，使用 ADAL 實作 `OnAuthorizationCodeReceived` 通知處理常式：

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
    // The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
    // The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
    string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    // We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
    string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

    // The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
    // ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
    AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

    // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
    // The token will be stored in the ADAL token cache, for use in our controllers
    AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
``` 

#### 在控制器中取得存取權杖

既然我們已獲得 `TaskService` 後端的存取權杖並儲存在 ADAL 的權杖快取中，我們必須實際使用它。
 `TasksController` 是負責與通訊 `TaskService` API 和傳送 HTTP 要求的 api 來讀取、 建立，
然後刪除工作。  傳送 HTTP 要求前，請先從 ADAL 取得存取權杖：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    AuthenticationResult result = null;
    try
    {
        string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
        ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

        // We don't care which policy is used to access the TaskService, so let's use the most recent policy
        string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;
        
        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        // AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
        AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
        result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

        ...
    }
    catch (AdalException ee)
    {
        // If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
    }
    ...
}
``` 

ADAL 會負責快取權杖、在快取權杖過期時重新整理，並經由擲回例外狀況，讓您知道使用者必須重新登入。  全部
您所要做為呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)` 每當應用程式中需要的語彙基元。

#### 從 Web API 讀取工作

既然您已經有權杖，您可以將它附加至 HTTP GET 要求的 `Authorization` 標頭，以安全地呼叫 `TaskService`：

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    ... 
    
    try 
    {
        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, then drop the current access token from the cache, 
            // and show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
                foreach (TokenCacheItem tci in todoTokens)
                    authContext.TokenCache.DeleteItem(tci);

                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

#### 在 Web API 上建立和刪除工作

您可以遵循完全相同的模式來傳送 POST 和 DELETE 要求給 `TaskService`。  只要呼叫 `AuthenticationContext.AcquireTokenSilentAsync(...)`,，
並將產生的權杖附加至要求中 `Authorization` 標頭。  我們已經為您實作 `Create` 動作。  請嘗試完成 `Delete` 中的動作
`TasksController.cs` 您自己。

## 8.登出使用者

最後的細節處理。  當使用者登出 Web 應用程式時，您應該清除 ADAL 權杖快取，以移除使用者已驗證的工作階段的任何殘餘部分：

```C#
// Controllers/AccountController.cs

public void SignOut()
{
    if (Request.IsAuthenticated)
    {
        // When the user signs out, clear their token cache in the process
        string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
        AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
        authContext.TokenCache.Clear();

        HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
    }
}
```

## 9.執行範例應用程式

最後，建置並執行 `TaskClient` 和 `TaskService`。  註冊或登入應用程式，並為登入的使用者建立工作。  登出後，再以不同使用者重新登入，並為該使用者建立工作。
請注意每位使用者的工作如何儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip),，
或者，您可以從 GitHub 複製它:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## 後續步驟

您現在可以進入更進階的 B2C 主題。  您可以嘗試：

[從 Web 應用程式呼叫 Web API >>]()

[自訂 B2C 應用程式的 UX >>]()

-->
