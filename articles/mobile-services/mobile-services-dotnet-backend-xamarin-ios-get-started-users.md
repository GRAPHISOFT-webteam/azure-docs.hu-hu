<properties
    pageTitle="開始在 Xamarin.iOS 應用程式的行動服務中使用驗證 | Microsoft Azure"
    description="了解如何使用行動服務透過眾多識別提供者驗證 Xamarin iOS 應用程式使用者，包括 Google、Facebook、Twitter 和 Microsoft。"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/01/2015" 
    ms.author="donnam"/>

# 在您的行動服務應用程式中新增驗證

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。 在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。 由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1. [註冊您的應用程式以驗證與設定行動服務]
2. [對通過驗證的使用者限制資料表權限]
3. [將驗證新增至應用程式]

本教學課程會以行動服務快速入門為基礎。 您還必須先完成本教學課程 [Get started with Mobile Services]。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。 確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。</p>

    <p>This happens because the app attempts to access Mobile Services as an unauthenticated user, but the <em>TodoItem</em> table now requires authentication.</p></li>
</ol>

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

在本節中您將修改應用程式，以先顯示登入畫面再顯示資料。 應用程式在啟動時將不會連接到您的行動服務，且不會顯示任何資料。 在使用者第一次執行重新整理動作後，登入畫面將會出現；在成功登入後，將會顯示 todo 項目清單。

1. 在用戶端專案中，開啟檔案 **QSTodoService.cs** 並將下列宣告新增至 QSTodoService:

        // Mobile Service logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. 新增新方法 **驗證** 至 **QSTodoService** 取代為下列定義 ︰

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE] 如果您使用的身分識別提供者不是 Facebook，變更值傳遞至 **LoginAsync** 上方的下列其中一個 ︰ _MicrosoftAccount_, ，_Twitter_, ，_Google_, ，或 _WindowsAzureActiveDirectory_。

3. 開啟 **QSTodoListViewController.cs**。 修改的方法定義 **ViewDidLoad** 移除呼叫 **refreshasync （)** 結尾附近的 ︰

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;

            todoService.BusyUpdate += (bool busy) => {
                if (busy)
                    activityIndicator.StartAnimating ();
                else
                    activityIndicator.StopAnimating ();
            };

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();

            AddRefreshControl ();
        }


4. 修改方法 **RefreshAsync** 來進行驗證並顯示登入畫面，如果 **使用者** 屬性為 null。 在方法定義最上方的下列程式碼上：

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. 按下 **執行** ] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式。 確認應用程式未顯示資料。

    將項目清單往下拉以執行重新整理動作，這會使登入畫面出現。 在您成功輸入有效認證後，應用程式將會顯示 todo 項目清單，且您可以對資料進行更新。

<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.
 -->

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md


