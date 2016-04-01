<properties 
    pageTitle="開發、測試和生產環境適用的不同 Application Insights 資源" 
    description="監視應用程式在不同開發階段的效能和使用量" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/25/2015" 
    ms.author="awills"/>

# 開發、測試和生產環境適用的不同 Application Insights 資源


若要避免混合來自偵錯、 測試和生產版本的應用程式的遙測，建立個別 [Application Insights][start] 資源，以接收來自每個版本的資料。

從您的應用程式收到的資料儲存及處理由 Microsoft Azure 中的 Application Insights *資源*。 每個資源由 *檢測金鑰。* 在應用程式中，該金鑰會提供給 Application Insights SDK，使它可以將所收集的資料傳送到正確的資源。 可以在程式碼或在 ApplicationInsights.config 中提供金鑰。 藉由變更 SDK 中的金鑰，您可以將資料導向不同資源。 


## 建立 Application Insights 資源
  

在 [portal.azure.com](https://portal.azure.com), ，加入 Application Insights 資源 ︰

![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-separate-resources/01-new.png)


* **應用程式類型** 會影響您看到 [概觀] 分頁中可用的屬性等 [計量瀏覽器][metrics]。 如果沒有看到您的應用程式類型，請針對網頁選擇其中一個 Web 類型，針對其他裝置則選擇其中一個手機類型。
* **資源群組** 是讓您輕鬆管理屬性，例如 [存取控制](app-insights-resources-roles-access-control.md)。 您可以對開發、測試和生產環境使用不同的資源群組。
* **訂閱** 是您在 Azure 中的付款帳戶。
* **位置** 是我們保留您資料的地方。 目前無法變更位置。
* **新增至開始面板** 將 Azure 首頁上的快速存取磚以取得您的資源。 

建立資源需要幾秒鐘。 完成時，您會看到警示。

(您可以撰寫 [PowerShell 指令碼](app-insights-powershell-script-create-resource.md) 自動建立資源。)


## 複製檢測金鑰

檢測金鑰會識別您所建立的資源。 

![按一下 [基本功能]，按一下 [檢測金鑰]，CTRL+C](./media/app-insights-separate-resources/02-props.png)

您將需要您的應用程式會將資料傳送至其中的所有資源的檢測金鑰。


## <a name="dynamic-ikey"></a>動態檢測金鑰

通常 SDK 會從 ApplicationInsights.config 取得 iKey。 相反地，在您的程式碼中設定它，讓它更易於變更。

在初始化方法中設定金鑰，例如 ASP.NET 服務中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

在此範例中，不同資源的 ikeys 會放置在不同版本的 Web 組態檔中。 交換 Web 組態檔會交換目標資源。

#### 網頁

IKey 也會在您的應用程式網頁中，在 [指令碼，從 [快速入門] 分頁中取得](app-insights-javascript.md)。 不要按其原義編寫至指令碼，請從伺服器狀態產生。 例如，在 ASP.NET 應用程式中：

*Razor 中的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

