<properties
    pageTitle="開始使用適用於 Windows 通用 App 的 Azure Mobile Engagement"
    description="了解如何使用適用於 Windows 通用 App 的 Azure Mobile Engagement 執行分析和傳送推播通知。"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/22/2015"
    ms.author="piyushjo" />


# 開始使用適用於 Windows 通用 App 的 Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)


本主題說明如何使用 Azure Mobile Engagement 了解您的應用程式使用狀況，以及傳送推播通知給 Windows 通用 app 的分佈使用者。
本教學課程將示範使用 Mobile Engagement 的簡單廣播案例。 您將建立一個空白的 Windows 通用 app，以使用 Windows 通知服務 (WNS) 來收集基本的應用程式使用資料及接收推播通知。

本教學課程需要下列各項：

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget 封裝

> [AZURE.IMPORTANT] 完成本教學課程是所有其他 Windows 通用 app 的 Mobile Engagement 教學課程的必要條件。 若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免費試用</a>。

## <a id="setup-azme"></a>設定 Windows 通用 app 的 Mobile Engagement

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## <a id="connecting-app"></a>應用程式連接到 Mobile Engagement 後端

本教學課程將說明「基本整合」，這是收集資料及傳送推播通知所需的最低設定。 完整的整合文件位於 [Mobile Engagement Windows 通用 SDK 整合](../mobile-engagement-windows-store-sdk-overview/)。

我們將會使用 Visual Studio 建立基本應用程式來示範整合。

### 建立一個新的 Windows 通用 app 專案

即使下列步驟與舊版 Visual Studio 中的步驟類似，但這些步驟假設使用的是 Visual Studio 2015。

1. 啟動 Visual Studio，然後在 [主畫面]**** 上選取 [新增專案]****。

2. 在快顯視窗中，選取 [Windows 8]**** -> [通用]**** -> [空白應用程式 (通用 Windows 8.1)]****。 填入 App 的**名稱**和**方案名稱**，然後按一下 [確定]****。

    ![][1]

現在已建立新的 Windows 通用 app 專案，接著我們要將 Azure Mobile Engagement SDK 整合至其中。

### 將您的應用程式連線至 Mobile Engagement 後端

1. 在專案中安裝 [MicrosoftAzure.MobileEngagement] nuget 封裝。 如果您是以 Windows 和 Windows Phone 兩個平台為目標，您就必須對這些專案執行此操作。 對於 Windows 8.x 和 Windows Phone 8.1，相同的 Nuget 封裝會在每個專案中放置正確的平台專屬二進位檔。

2. 開啟 **Package.appxmanifest**，並確定已在該處新增下列功能：

        Internet (Client)

    ![][2]

3. 現在複製您稍早為 Mobile Engagement 應用程式複製的連接字串，並將它貼 `Resources\EngagementConfiguration.xml` 檔案之間 `< connectionString >` 和 `< / connectionString >` 標記:

    ![][3]
    >[AZURE.TIP] 如果您的應用程式將以 Windows 和 Windows Phone 兩個平台為目標，那麼您仍應該建立兩個 Mobile Engagment 應用程式，讓每個支援的平台各使用一個。 這是為了確定您可以建立正確的對象區隔，以及正確地針對各平台傳送目標式通知。

4. 在 `App.xaml.cs` 檔案:

 a. 新增 `使用` 陳述式:

         using Microsoft.Azure.Engagement;

 b. 將專用方法加入至 Engagement 初始化和設定：

        private void InitEngagement(IActivatedEventArgs e)
        {
          EngagementAgent.Instance.Init(e);
    
          //... rest of the code
        }

 c. 在 **OnLaunched** 方法中初始化 SDK：

         protected override void OnLaunched(LaunchActivatedEventArgs e)
         {
           InitEngagement(e);
    
           //... rest of the code
         }

 c. 在 **OnActivated** 方法中插入下列內容，並新增該方法 (如果它尚未存在)：

         protected override void OnActivated(IActivatedEventArgs e)
         {
           InitEngagement(e);
    
           //... rest of the code
         }


## <a id="monitor"></a>若要啟用即時監視

若要開始傳送資料並確定使用者正在使用，您必須至少傳送一個畫面 (活動) 到 Mobile Engagement 後端。

1.  在 **MainPage.xaml.cs**, ，新增下列 `使用` 陳述式:

        using Microsoft.Azure.Engagement.Overlay;

2. 將 **MainPage** 的基底類別從 **Page** 取代為 **EngagementPageOverlay**：

        class MainPage : EngagementPageOverlay

3. 在 `MainPage.xaml` 檔案:

    a. 新增至命名空間宣告：

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. 使用 **engagement:EngagementPageOverlay** 來取代 XML 標記名稱中的 **Page**。

> [AZURE.IMPORTANT] 如果您的頁面會覆寫 `OnNavigatedTo` 方法，請務必呼叫 `基底。OnNavigatedTo(e)`。 否則，不會報告活動 ( `EngagementPage` 呼叫 `StartActivity` 內其 `OnNavigatedTo` 方法)。 這點特別重要的 Windows Phone 專案中，預設範本都有 `OnNavigatedTo` 方法。 

## <a id="monitor"></a>應用程式與即時監視連線

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>啟用推播通知與應用程式內傳訊

Mobile Engagement 可讓您透過推播通知和應用程式內傳訊，於活動進行時與使用者互動和觸達。 此模組在 Mobile Engagement 入口網站中稱為觸達 (REACH)。
以下各節將設定您的用程式來接收它們。

### 啟用您的應用程式接收 WNS 推播通知

1. 在 `Package.appxmanifest` 檔案， **應用程式** 索引標籤中，於 **通知**, ，請將 **支援快顯通知:** 至 **是**

    ![][5]

### 初始化 REACH SDK

在 `App.xaml.cs`, ，呼叫 **EngagementReach.Instance.Init(e);** 中 **InitEngagement** 代理程式初始化後的函式:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

您現在可以傳送快顯通知了。 現在我們要驗證您已正確完成這項基本整合。

### 授與 Mobile Engagement 存取權以傳送通知

1. 在您的 web 瀏覽器中開啟 [Windows 市集開發人員中心] 登入並建立一個帳戶，如有必要。
2. 按一下右上角的 [儀表板]****，然後按一下左面板功能表中的 [建立新的應用程式]****。

    ![][9]

2. 建立您的應用程式並保留其名稱。

    ![][10]

3. 建立應用程式之後，從左側功能表瀏覽至 [服務] -> [推播通知]****。

    ![][11]

4. 在 [推播通知] 區段中按一下 [Live 服務網站]**** 連結。

    ![][12]

5. 您將會瀏覽至推播認證區段。 請確定您位於 [應用程式設定]**** 區段中，然後複製您的 [封裝 SID]**** 和 [用戶端密碼]****

    ![][13]

6. 瀏覽到 Mobile Engagement 入口網站的 [設定]****，然後按一下左側的 [原生推送]**** 區段。 然後，按一下 [編輯]**** 按鈕來輸入您的 [封裝安全性識別碼 (SID)]**** 和 [秘密金鑰]****，如下所示：

    ![][6]

8. 最後確定您的 Visual Studio 應用程式與在應用程式市集中建立的此應用程式相關聯。 您需要從 Visual Studio 按一下 [將應用程式與市集建立關聯]**** 來執行這項操作。

    ![][7]

## <a id="send"></a>將通知傳送至您的應用程式

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

如果 App 正在執行，則您將會看到 App 內的通知，否則會看到快顯通知 (如果 App 已關閉)。 
如果您看見的是 App 內的通知而不是快顯通知，而且您正在 Visual Studio 中的偵錯模式下執行 App，則應嘗試執行工具列中的 [週期事件] -> [暫止]****，以確保 App 會實際暫止。 如果您在 Visual Studio 中偵錯應用程式時只按了 [首頁] 按鈕，則它永遠不會暫止，而您將會看見 App 內的通知，它不會顯示為快顯通知。

![][8]




[mobile engagement windows universal sdk documentation]: ../mobile-engagement-windows-store-integrate-engagement/ 
[microsoftazure.mobileengagement]: http://go.microsoft.com/?linkid=9864592 
[windows store dev center]: https://dev.windows.com 
[windows universal apps - overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration 
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png 
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png 
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png 
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png 
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png 
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png 
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png 
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png 
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png 
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png 
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png 
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png 

