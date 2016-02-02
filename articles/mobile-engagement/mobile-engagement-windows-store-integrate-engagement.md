<properties 
    pageTitle="Windows 通用 app Engagement SDK 整合" 
    description="如何將 Azure Mobile Engagement 與 Windows 通用 app 整合"                  
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
    ms.topic="article" 
    ms.date="07/07/2015" 
    ms.author="piyushjo" />


# Windows 通用 app Engagement SDK 整合

> [AZURE.SELECTOR] 
- [Universal Windows](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 


本程序說明如何以最簡單的方式啟用 Windows 通用 app 內 Engagement 的分析與監視功能。

下列步驟便足以啟用計算使用者、工作階段、活動、當機和技術相關的所有統計資料需要的記錄檔之報告。 需要計算事件、 錯誤及工作等其他統計資料的記錄檔報告必須使用 Engagement API 手動完成 (請參閱 [如何使用進階的 Mobile Engagement 標記 API 在 Windows 通用 app](mobile-engagement-windows-store-use-engagement-api.md) 因為這些統計資料與相依的應用程式。

## 支援的版本

適用於 Windows 通用 app 的 Mobile Engagement SDK 只能整合至目標為以下作業系統的 Windows 執行階段和通用 Windows 平台應用程式：

-   Windows 8
-   Windows 8.1
-   Windows Phone 8.1
-   Windows 10 (桌上型電腦和行動裝置系列)

> [AZURE.NOTE] 如果您的目標 Windows Phone Silverlight，請參閱 [Windows Phone Silverlight 整合程序](mobile-engagement-windows-phone-integrate-engagement.md)。

## 安裝 Mobile Engagement 跨平台 app SDK

### 所有平台

提供適用於 Windows 通用 app 的 Mobile Engagement SDK 時會使用稱為 *MicrosoftAzure.MobileEngagement* 的 Nuget 封裝。 您可以從 Visual Studio Nuget 封裝管理員安裝該封裝。

### Windows 8.x 和 Windows Phone 8.1

NuGet 會自動部署中的 SDK 資源 `資源` 在應用程式專案的根資料夾。

### Windows 10 通用 Windows 平台應用程式

NuGet 目前還不會自動在您的 UWP 應用程式部署 SDK 資源。 您必須手動執行，直到 NuGet 重新引進資源部署：

1.  開啟 [檔案總管]。
2.  瀏覽至下列位置 (**x.x.x** Engagement 您要安裝的版本):
*%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3.  從檔案總管將 [Resources]**** 資料夾拖放到您的專案在 Visual Studio 中的根目錄。
4.  在 Visual Studio 中，選取您的專案並啟動 [方案總管]**** 上方的 [顯示所有檔案]**** 圖示。
5.  部分檔案未包含在專案中。 若要將它們一次匯入，請在 [Resources]**** 資料夾上按一下滑鼠右鍵，[從專案移除]**** 然後再次在 [Resources]**** 資料夾上按一次滑鼠右鍵，[加入至專案]**** 以重新包含整個資料夾。 所有來自 [Resources]**** 資料夾的檔案現在已經包含在您的專案中。

擷取的 Engagement 套件也可以在 *$(Solutiondir)\Packages* 或如您的 *NuGet.config* 檔案中定義的位置找到。

## 新增功能

Engagement SDK 需要一些 Windows SDK 的功能以正常運作。

開啟您 `Package.appxmanifest` 檔案，並確定已宣告下列功能:

-   `網際網路 (用戶端)`

## 初始化 Engagement SDK

### Engagement 組態

Engagement 組態會集中在 `Resources\EngagementConfiguration.xml` 在專案檔案。

編輯此檔案來指定：

-   您的應用程式之間的連接字串標記 `< connectionString >` 和 `< \connectionString >`。

若想要改為在執行階段指定它，您可以在 Engagement 代理程式初始化之前呼叫下列方法：

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    
          /* Connection string for my Windows Store App. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

應用程式的連接字串會顯示在 Azure 傳統入口網站中。

### Engagement 初始化

當您建立新的專案， `App.xaml.cs` 產生檔案。 這個類別繼承自 `應用程式` 且包含許多重要的方法。 它將會用來初始化 Engagement SDK。

修改 `App.xaml.cs`:

-   加入您 `使用` 陳述式:

        using Microsoft.Azure.Engagement;

-   定義一個方法以針對所有呼叫一次共用 Engagement 初始化：

    private void InitEngagement(IActivatedEventArgs e)
    {
      EngagementAgent.Instance.Init(e);
    
      // or
    
      EngagementAgent.Instance.Init(e, engagementConfiguration);
    }

-   呼叫 `InitEngagement` 中 `OnLaunched` 方法:

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
          InitEngagement(e);
        }

-   當使用自訂配置、 其他應用程式或命令列啟動您的應用程式時則 `OnActivated` 方法呼叫。 您也需要在您的應用程式啟動時初始化 Engagement SDK。 若要這樣做，請覆寫 `OnActivated` 方法:

        protected override void OnActivated(IActivatedEventArgs args)
        {
          InitEngagement(args);
        }


> [AZURE.IMPORTANT] 我們強烈地建議您不要在應用程式的其他地方加入 Engagement 初始化。

## 基本報告

### 建議使用的方法: 多載您 `頁面` 類別

為了啟用 Engagement 計算使用者、 工作階段、 活動、 當機和技術的統計資料所需的所有記錄檔報告，您可以讓所有您 `頁面` 子類別繼承自 `EngagementPage` 類別。

以下是如何在您應用程式其中一個頁面使用此方法的範例。 您可以將相同的方法用於您應用程式的所有頁面。

#### C# 來源檔案

修改您的頁面 `。 xaml.cs` 檔案:

-   加入您 `使用` 陳述式:

        using Microsoft.Azure.Engagement;

-   取代 `頁面` 與 `EngagementPage`:

**沒有 Engagement：**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**有 Engagement：**

        using Microsoft.Azure.Engagement;
    
        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [AZURE.IMPORTANT] 如果您的頁面會覆寫 `OnNavigatedTo` 方法，請務必呼叫 `基底。OnNavigatedTo(e)`。 否則，不會報告活動 ( `EngagementPage` 呼叫 `StartActivity` 內其 `OnNavigatedTo` 方法)。

#### XAML 檔案

修改您的頁面 `.xaml` 檔案:

-   新增至命名空間宣告：

        xmlns:engagement="using:Microsoft.Azure.Engagement"

-   取代 `頁面` 與 `engagement: EngagementPage`:

**沒有 Engagement：**

        <Page>
            
            ...
        </Page>

**有 Engagement：**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            
            ...
        </engagement:EngagementPage >

#### 覆寫預設行為

根據預設，頁面的類別名稱會在報告時做為活動名稱 (沒有額外的名稱)。 如果類別使用 "Page" 尾碼，Engagement 也會移除它。

如果想要覆寫名稱的預設行為，您只需將下列新增至您的程式碼：

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

如果想要報告活動的一些額外資訊，您可以將下列新增至您的程式碼：

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

會呼叫這些方法從 `OnNavigatedTo` 頁面的方法。

### 替代方法: 呼叫 `StartActivity()` 手動

如果您無法或不想多載您 `頁面` 類別，您可以改為啟動活動藉由呼叫 `EngagementAgent` 直接的方法。

我們建議您呼叫 `StartActivity` 內您 `OnNavigatedTo` 頁面的方法。

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [AZURE.IMPORTANT]  請確定您正確地結束工作階段。
> 
> Windows 通用 SDK 會自動呼叫 `EndActivity` 方法在應用程式關閉時。 因此，它是 **高** 建議您呼叫 `StartActivity` 方法，每當使用者的活動變更時，以及 **永不** 呼叫 `EndActivity` 方法，這個方法會傳送至 Engagement 伺服器，目前使用者已離開應用程式，這會影響所有應用程式記錄檔。

## 進階報告

(選擇性) 您可能想要報告應用程式特定事件、 錯誤和作業，若要這樣做，請使用方法中所找到的其他 `EngagementAgent` 類別。 Engagement API 允許使用所有 Engagement 的進階功能。

如需詳細資訊，請參閱 [如何使用進階的 Mobile Engagement 標記 API 在 Windows 通用 app](../mobile-engagement-windows-store-use-engagement-api/)。

## 進階組態

### 停用自動當機報告

您可以停用 Engagement 的自動當機報告功能。 然後，發生未處理的例外狀況時，Engagement 將不會執行任何動作。
> [AZURE.WARNING] 如果您打算停用此功能，請注意當您的應用程式中將發生未處理的當機時，Engagement 將不會傳送該當機，****「且」亦不會關閉工作階段和工作。

若要停用自動當機報告，只要依照您宣告的方式自訂您的組態即可：

#### 從 `EngagementConfiguration.xml` 檔案

報告當機設為 `false` 之間 `< reportCrash >` 和 `< / reportCrash >` 標記。

#### 從 `EngagementConfiguration` 在執行階段物件

使用 EngagementConfiguration 物件將報告當機設為 false。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
    
        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### 高載模式

根據預設，Engagement 服務會即時報告記錄檔。 如果應用程式報告記錄檔的頻率很高，最好先緩衝處理記錄檔，再定期一次報告它們 (此稱為「高載模式」)。

若要這樣做，請呼叫方法：

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

該引數是以****「毫秒」為單位的值。 如果您想要重新啟動及時記錄，您隨時可以呼叫該方法，而不需要使用任何參數 (或使用 0 為值)。

高載模式可以稍微延長電池使用時間但對 Engagement 監視器會有影響： 所有工作階段和工作持續時間將調整為高載閾值 (因此，可能將看不到時間比高載閾值短的工作階段和作業)。 建議使用低於 30000 (30 秒) 的閾值。 您必須留意儲存記錄檔僅限於 300 個項目。 如果傳送太長，您可能會遺失某些記錄檔。
> [AZURE.WARNING] 高載閾值無法設定為小於 1 秒的時間間隔。 如果您嘗試這樣做，SDK 會顯示含錯誤訊息的追蹤，並且會自動重設為預設值 (0 秒)。 這樣會觸發 SDK 以即時的方式報告記錄檔。


[here]: http://www.nuget.org/packages/Capptain.WindowsCS 
[nuget website]: http://docs.nuget.org/docs/start-here/overview 

