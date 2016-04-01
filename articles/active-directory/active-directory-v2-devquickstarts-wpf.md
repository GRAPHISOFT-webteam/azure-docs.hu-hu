<properties
    pageTitle="App 模型 v2.0 .NET 原生 App | Microsoft Azure"
    description="如何建置可使用個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 .NET 原生應用程式。"
    services="active-directory"
    documentationCenter=""
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

# 應用程式模型 v2.0 預覽：將登入加入 Windows 傳統型應用程式

有了 v2.0 應用程式模型，您就可以快速地將驗證加入傳統型應用程式，同時支援個人 Microsoft 帳戶以及工作或學校帳戶。  它也可讓您的應用程式安全地進行通訊與後端 web api，以及少數 [Office 365 統一 Api](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)。

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

如 [，在裝置上執行的.NET 原生應用程式](active-directory-v2-flows.md#mobile-and-native-apps), ，Azure AD 提供 Active Directory 驗證程式庫 ADAL。  ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得權杖以呼叫 Web 服務。  為了示範這有多麼簡單，我們將在此建置一個執行下列動作的 .NET WPF 待辦事項清單應用程式：

-   使用者登入時，和取得存取權杖使用 [OAuth 2.0 驗證通訊協定](active-directory-v2-protocols.md#oauth2-authorization-code-flow)。
-   安全地呼叫受 OAuth 2.0 保護的後端待辦事項清單 Web 服務。
-   將使用者登出。

若要建置完整可用的應用程式，您必須：

2. 註冊應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)。  若要跟著做，您可以 [下載為.zip 的應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) 或再製基本架構 ︰

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

本教學課程最後也會提供完整的應用程式。

## 1.註冊應用程式
建立新的應用程式在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ，或請遵循下列 [詳細步驟](active-directory-v2-app-registration.md)。  請確定：

- 複製 **應用程式識別碼** 指派給您的應用程式，您將需要它很快。
- 新增 **行動** 平台應用程式。
- 複製 **重新導向 URI** 從入口網站。 您必須使用 `urn:ietf:wg:oauth:2.0:oob` 的預設值。

## 2.安裝和設定 ADAL
您現在有了已向 Microsoft 註冊的應用程式，可以安裝 ADAL 並撰寫與您身分識別相關的程式碼。  為了讓 ADAL 能與 v2.0 端點通訊，您需要提供一些應用程式註冊的相關資訊。

-   首先，使用 Package Manager Console 將 ADAL 新增到 TodoListClient 專案中。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease
```

-   在 TodoListClient 專案中，開啟 `app.config`。  取代 `<appSettings>` 區段中的元素值，以反映您在應用程式註冊入口網站中輸入的值。  每當使用 ADAL 時，您的程式碼便會參考這些值。
    -    `ida:ClientId` 是 **應用程式識別碼** 您從入口網站複製的應用程式。
    -    `ida:RedirectUri` 是 **重新導向 Uri** 從入口網站。
- 在 TodoList-Service 專案中，開啟專案根目錄中的 `web.config`。  
    - 取代 `ida:Audience` 具有相同的值 **應用程式識別碼** 從入口網站。

## 3.使用 ADAL 取得權杖
ADAL 的基本原則是，每當應用程式需要存取權杖時，您只需呼叫 `authContext.AcquireToken(...)`，ADAL 就會執行其餘工作。  

-   在 `TodoListClient` 專案中，開啟 `MainWindow.xaml.cs` 並找出 `OnInitialized(...)` 方法。  第一步是初始化應用程式的 `AuthenticationContext` -ADAL 的主要類別。  您在這裡將 ADAL 與 Azure AD 通訊所需的座標傳給 ADAL，並告訴它如何快取權杖。

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        authContext = new AuthenticationContext(authority, new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- 當應用程式啟動時，我們希望檢查並查看使用者是否已登入應用程式。  不過，我們不想這個時候叫用登入 UI，而是讓使用者按一下 [登入] 才執行此作業。  另在 `OnInitialized(...)` 方法中：

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
        result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

        // If we got here, a valid token is in the cache.  Proceed to
        // fetch the user's tasks from the TodoListService via the
        // GetTodoList() method.

        SignInButton.Content = "Clear Cache";
        GetTodoList();
}
catch (AdalException ex)
{
        if (ex.ErrorCode == "user_interaction_required")
        {
                // If user interaction is required, the app should take no action,
                // and simply show the user the sign in button.
        }
        else
        {
                // Here, we catch all other AdalExceptions

                string message = ex.Message;
                if (ex.InnerException != null)
                {
                        message += "Inner Exception : " + ex.InnerException.Message;
                }
                MessageBox.Show(message);
        }
}
```

- 如果使用者未登入而按下 [登入] 按鈕，我們希望叫用登入 UI 並讓使用者輸入其認證。  實作登入按鈕處理常式：

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

        // If the user clicked the 'Sign-In' button, force
        // ADAL to prompt the user for credentials by specifying
        // PromptBehavior.Always.  ADAL will get a token for the
        // TodoListService and cache it for you.

        AuthenticationResult result = null;
        try
        {
                result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
                SignInButton.Content = "Clear Cache";
                GetTodoList();
        }
        catch (AdalException ex)
        {
                // If ADAL cannot get a token, it will throw an exception.
                // If the user canceled the login, it will result in the
                // error code 'authentication_canceled'.

                if (ex.ErrorCode == "authentication_canceled")
                {
                        MessageBox.Show("Sign in was canceled by the user");
                }
                else
                {
                        // An unexpected error occurred.
                        string message = ex.Message;
                        if (ex.InnerException != null)
                        {
                                message += "Inner Exception : " + ex.InnerException.Message;
                        }

                        MessageBox.Show(message);
                }

                return;
        }

}
```

- 如果使用者成功登入，ADAL 會為您接收和快取權杖，您可以放心大膽地繼續呼叫 `GetTodoList()` 方法。  若要取得使用者的工作剩下的就是實作 `GetTodoList()` 方法。

```C#
private async void GetTodoList()
{
        AuthenticationResult result = null;
        try
        {
                // Here, we try to get an access token to call the TodoListService
                // without invoking any UI prompt.  PromptBehavior.Never forces
                // ADAL to throw an exception if it cannot get a token silently.

                result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
        }
        catch (AdalException ex)
        {
                // ADAL couldn't get a token silently, so show the user a message
                // and let them click the Sign-In button.

                if (ex.ErrorCode == "user_interaction_required")
                {
                        MessageBox.Show("Please sign in first");
                        SignInButton.Content = "Sign In";
                }
                else
                {
                        // In any other case, an unexpected error occurred.

                        string message = ex.Message;
                        if (ex.InnerException != null)
                        {
                                message += "Inner Exception : " + ex.InnerException.Message;
                        }
                        MessageBox.Show(message);
                }

                return;
        }

        // Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the ADAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                authContext.TokenCache.Clear();
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

恭喜！ 您現在擁有一個運作正常的 .NET WPF 應用程式，可使用 OAuth 2.0 驗證使用者並安全地呼叫 Web API。  執行您的兩個專案，並以個人的 Microsoft 或工作/學校的帳戶登入。  將工作新增到使用者的待辦事項清單。  登出並以其他使用者再次登入，查看其他使用者的待辦事項清單。  關閉並重新執行應用程式。  注意使用者的工作階段是否維持不變，這是因為應用程式會快取本機檔案中的權杖。

ADAL 可使用個人和工作帳戶，輕鬆地將通用的身分識別功能納入您的應用程式。  它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。  您唯一需要知道的就是單一 API 呼叫，`authContext.AcquireTokenAsync(...)`。

（不含您的設定值） 已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ，或您可以從 GitHub 複製它 ︰

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## 後續步驟

您現在可以進入更進階的主題。  您可以嘗試：

- [使用 v2.0 應用程式模型保護 TodoListService Web API >>](active-directory-v2-devquickstarts-dotnet-api.md)

如需其他資源，請參閱：
- [應用程式模型 v2.0 預覽 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow"adal"標記 >>](http://stackoverflow.com/questions/tagged/adal)


