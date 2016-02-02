<properties 
    pageTitle="我如何在 Application Insights 中..." 
    description="Application Insights 中的常見問題集。" 
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
    ms.date="10/11/2015" 
    ms.author="awills"/>


# 我如何在 Application Insights 中...？

## ... 時收到電子郵件

### 我的網站當機時寄送電子郵件

設定 [可用性 web 測試](app-insights-monitor-web-app-availability.md)。

### 我的網站多載時寄送電子郵件

設定 [警示](app-insights-alerts.md) 上 **伺服器回應時間**。 介於 1 到 2 秒之間的閾值應該會運作。

![](./media/app-insights-how-do-i/030-server.png)

您的 app 也可能會藉由傳回失敗碼顯示資源耗盡的徵兆。 針對 [失敗的要求]**** 設定警示。

如果您想要設定警示 **伺服器例外狀況**, ，您可能必須為執行 [其他一些設定](app-insights-asp-net-exceptions.md) 才能看到資料。

### 傳送電子郵件的例外狀況

1. [設定例外狀況監視](app-insights-asp-net-exceptions.md)
2. [設定警示](app-insights-alert.md) 例外狀況的計算公制


### 我的 app 發生事件時寄送電子郵件

我們假設您想要在特定事件發生時收到電子郵件。 Application Insights 並不直接提供這項工具，但它可以 [度量超出臨界值時傳送警示](app-insights-alerts.md)。

可以設定警示 [自訂度量](app-insights-api-custom-events-metrics.md#track-metric), ，雖然無法自訂事件。 撰寫一些程式碼以在事件發生時增加計量：

    telemetry.TrackMetric("Alarm", 10);

或：

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

因為警示有兩個狀態，所以當您考慮讓警示結束時必須傳送較低的值：

    telemetry.TrackMetric("Alarm", 0.5);

建立圖表中的 [計量瀏覽器](app-insights-metric-explorer.md) 以查看您的警示:

![](./media/app-insights-how-do-i/010-alarm.png)

現在設定警示在計量一段短時間高於中間值時引發：


![](./media/app-insights-how-do-i/020-threshold.png)

將平均期間設定為最小值。

您就會在計量高於和低於閾值時收到電子郵件。

考慮事項：

* 警示有兩種狀態 (「警示」和「良好」)。 只有在收到計量時才會評估狀態。
* 只有在狀態變更時才會傳送電子郵件。 這是為什麼您必須同時傳送高值和低值的計量。
* 若要評估警示，會採用收到的值除以先前的期間來獲得平均值。 因為每次收到計量時都會進行計算，所以傳送電子郵件的頻率會比您設定的期間還要高。
* 由於「警示」和「良好」都會傳送電子郵件，所以您可能要以兩個狀態的條件來重新思考單次發生的事件。 例如，不要採用「作業完成」事件，而採用「作業進行中」的條件，您就可以在工作開始和結束時收到電子郵件。

### 自動設定警示

[使用 PowerShell 建立新的警示](app-insights-alerts/#set-alerts-by-using-powershell)

## 使用 PowerShell 管理 Application Insights

* [建立新資源](app-insights-powershell-script-create-resource.md)
* [建立新的警示](app-insights-alerts/#set-alerts-by-using-powershell)

## 應用程式版本和戳記

### 將開發、測試和生產的結果分開

* 針對不同的環境，設定不同的重點
* 針對不同的戳記 (開發、測試、生產)，使用不同的屬性值來標記遙測

[了解更多](app-insights-separate-resources.md)


### 篩選組建編號

當您發佈新的 App 版本時，希望能夠將不同組建的遙測分開。

您可以設定應用程式的版本屬性，以便您可以篩選 [搜尋](app-insights-diagnostic-search.md) 和 [計量瀏覽器](app-insights-metrics-explorer.md) 結果。


![](./media/app-insights-how-do-i/050-filter.png)

設定 [應用程式版本] 屬性有幾種不同的方法。

* 直接設定：

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass)。Assembly.GetName()。版本;`

* 包裝在該行 [遙測初始設定式](app-insights-api-custom-events-metrics.md#telemetry-initializers) 以確保一致的方式設定所有 TelemetryClient 執行個體。

* [ASP.NET]設定中的版本 `BuildInfo.config`。 Web 模組將會從 BuildLabel 節點取得版本。 在您的專案中包含此檔案， 而且記得要在 [方案總管] 中設定 [永遠複製] 屬性。

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>
    ```
* [ASP.NET] 在 MSBuild 中自動產生 BuildInfo.config。 若要這樣做，請先在 .csproj 檔案中加入幾行：

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    這會產生一個稱為 *yourProjectName*.BuildInfo.config 的檔案。 發佈程序會將這個檔案重新命名為 BuildInfo.config。

    當您使用 Visual Studio 建置時，組建標籤會包含預留位置 (AutoGen_...)。 但是當使用 MSBuild 建立時，則會填入正確的版本號碼。

    若要允許 MSBuild，以產生版本號碼，設定像是版本 `1.0。 *` AssemblyReference.cs 中

## 監視後端伺服器與桌面應用程式

[使用 Windows Server SDK 模組](app-insights-windows-desktop.md)。


## 顯現資料

#### 具有來自多個 App 之計量的儀表板

* 在 [計量瀏覽器](app-insights-metrics-explorer.md), ，來自訂圖表，並將它儲存為我的最愛。 將它釘選到 Azure 儀表板。
*

#### 資料來自其他來源和 Application Insights 的儀表板

* [遙測資料匯出至 Power BI](app-insights-export-power-bi.md)。

或

* 使用 SharePoint 做為您的儀表板，在 SharePoint web 組件中顯示資料。 [使用連續匯出 」 和 「 串流分析匯出至 SQL](app-insights-code-sample-export-sql-stream-analytics.md)。 使用 PowerView 來檢查資料庫，並建立適用於 PowerView 的 SharePoint web 組件。


### 複雜的篩選、分割和聯結

* [使用連續匯出 」 和 「 串流分析匯出至 SQL](app-insights-code-sample-export-sql-stream-analytics.md)。 使用 PowerView來檢查資料庫。

<a name="search-specific-users"></a>
### 篩選出匿名或已驗證的使用者

如果您的使用者登入，您可以設定 [驗證使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users)。 (它不會自動重新整理)。

接著，您可以：

* 搜尋特定的使用者識別碼

![](./media/app-insights-how-do-i/110-search.png)

* 篩選匿名或已驗證使用者的計量

![](./media/app-insights-how-do-i/115-metrics.png)

## 修改屬性名稱或值

Create a [filter](app-insights-api-filtering-sampling.md#filtering). 這可讓您先修改或篩選遙測，然後再將它從您的應用程式傳送至 Application Insights。

## 列出特定使用者和其使用方式

如果您只想要 [搜尋特定使用者](#search-specific-users), ，您可以設定 [驗證使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users)。

如果您想要使用者清單以及像是他們查看過哪些頁面或登入頻率等資料，則有兩個選項：

* [組進行驗證的使用者識別碼](app-insights-api-custom-events-metrics.md#authenticated-users), ，[匯出到資料庫](app-insights-code-sample-export-sql-stream-analytics.md) 並使用適當工具來分析您的使用者資料。
* 如果您只有少數的使用者，則可傳送自訂事件或計量、使用感興趣的資料做為計量值或事件名稱，然後設定使用者識別碼做為屬性。 若要分析頁面檢視，可取代標準的 JavaScript trackPageView 呼叫。 若要分析伺服器端遙測，可使用遙測初始設定式，將使用者識別碼新增至所有伺服器遙測。 然後您可以篩選與分割關於使用者識別碼的計量資訊和搜尋。


## 降低從我的 App 到 Application Insights 的流量

* 在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), ，停用您不需要這種效能計數器收集器的模組。
* 使用 [取樣和篩選](app-insights-api-filtering-sampling.md) 在 SDK。
* 如果您使用 [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric), ，計算的彙總的度量值的批次傳送結果之前。 有一個 TrackMetric() 的多載是針對該動作所提供。


深入了解 [定價和配額](app-insights-pricing.md)。

## 停用遙測

若要從伺服器**動態停止和開始**收集及傳輸遙測資料：

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



若要 **停用選取的標準收集器** -例如，效能計數器、 HTTP 要求或相依性-刪除或註解中相關的程式行會 [ApplicationInsights.config](app-insights-api-custom-events-metrics.md)。 例如，如果您想要傳送自己的 TrackRequest 資料，可以這麼做。



## 檢視系統效能計數器

您可以在計量總管中顯示的計量資訊是一組系統效能計數器。 有一個預先定義且標題為**伺服器**的刀鋒視窗會顯示它們其中幾個。

![開啟 Application Insights 資源並按一下伺服器](./media/app-insights-how-do-i/121-servers.png)

### 如果您看不到效能計數器資料

* 您自己的電腦或 VM 上的 **IIS 伺服器**。 [安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)。
* **Azure 網站** - 我們尚未支援效能計數器。 您可以取得數個計量來做為 Azure 網站控制台的標準部分。
* **Unix 伺服器** - [安裝 collectd](app-insights-java-collectd.md)

### 顯示更多效能計數器

* 首先， [加入新的圖表](app-insights-metrics-explorer.md) 和計數器是否在我們提供基本集合中。
* 如果沒有， [將計數器加入至收集效能計數器模組集](app-insights-web-monitor-performance.md#system-performance-counters)。







