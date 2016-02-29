<properties 
    pageTitle="ASP.NET 的 Application Insights" 
    description="透過使用 Application Insights 來分析您內部部署或 Microsoft Azure Web 應用程式的效能、可用性與使用模式。" 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>


# 設定 ASP.NET 的 Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Application Insights SDK 會從 Web 應用程式傳送遙測至 Azure 入口網站，您可以在此登入並查看應用程式效能和使用量的圖表。 

![範例效能監視圖表](./media/app-insights-asp-net/10-perf.png)

您也可以調查特殊要求、例外狀況和記錄事件，並將其相互關聯。  您可以使用 API 新增遙測以詳細監視效能和使用量。

#### 開始之前

您需要：

* 若要訂閱 [Microsoft Azure](http://azure.com)。 如果您的小組或組織擁有 Azure 訂用帳戶，擁有者可以將您加入它，使用您 [Microsoft 帳戶](http://live.com)。
* Visual Studio 2013 Update 3 或更新版本。

## <a name="ide"></a> 在 Visual Studio 中將 Application Insights 加入至專案

#### 對於新專案

當您在 Visual Studio 中建立新專案時，請務必選取 Application Insights。 


![Create an ASP.NET project](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ... 對於現有專案

在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [Add Application Insights]。

![Choose Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### 設定選項

如果這是您第一次使用，系統會要求您登入或註冊 Microsoft Azure。 

如果此應用程式是更大的應用程式的一部分，您可能想要使用 **設定** 將它放在與其他元件相同的資源群組。 


####<a name="land"></a> 加入 Application Insights 做了什麼?

命令執行了下列步驟 (您可能會改為 [手動進行](app-insights-start-monitoring-app-health-usage.md) 如果您偏好):

* 建立 Application Insights 資源中的 [Azure 入口網站][portal]。 這是您會看到您的資料的位置。 它會擷取 *檢測金鑰* 可識別資源。
* 將 Application Insights Web SDK NuGet 封裝加入您的專案。 若要在 Visual Studio 中看到它，請以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。
* 將檢測金鑰放在 `ApplicationInsights.config` 中。


## <a name="run"></a> 執行專案

利用 F5 執行應用程式並立即試用：開啟不同的頁面來產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> 開啟 Application Insights

開啟 Application Insights 資源中的 [Azure 入口網站][portal]。

![Right-click your project and open the Azure portal](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### 度量：彙總資料

在 [概觀] 圖表中尋找資料。 剛開始的時候，您只會看見一或兩個資料點。 例如：

![Click through to more data](./media/app-insights-asp-net/12-first-perf.png)

按一下任何圖表以查看詳細度量。 [進一步了解度量。][perf]

* *任何使用者或頁面的資料?* - [新增使用者和頁面的資料](../article/application-insights/app-insights-asp-net-client.md)

### 搜尋：個別事件

開啟 [搜尋] 來調查個別要求和其相關聯的事件。 

![](./media/app-insights-asp-net/21-search.png)

[深入了解搜尋](app-insights-diagnostic-search.md)

* *沒有相關聯的事件嗎?*設定 [伺服器例外狀況](../article/application-insights/app-insights-asp-net-exception-mvc.md) 和 [相依性](../article/application-insights/app-insights-asp-net-dependencies.md)。

### 沒有資料？

* 請確定您查看的是正確的項目。 在登入 [Azure 入口網站](https://portal.azure.com), ，按一下 「 瀏覽 > 」，「 Application Insights 」，然後選取您的應用程式。
* 使用應用程式、開啟不同頁面，以產生一些遙測。
* 開啟 [搜尋][diagnostic] 刀鋒視窗中，來查看個別事件。 有時候，事件通過計量管線所需的時間較長。
* 請稍等片刻，然後按一下 [重新整理]。
* 請參閱 [疑難排解][qna]。


## 發佈您的應用程式

現在部署應用程式並觀看資料累積情形。

以偵錯模式執行時，系統會透過管線迅速傳送遙測資料，因此您應該可以在幾秒內看見資料。 在部署應用程式時，資料累積會較為緩慢。

#### 組建伺服器發生問題？

請參閱 [此疑難排解項目](app-insights-troubleshoot-faq.md#NuGetBuild)。

> [AZURE.NOTE] 如果您的應用程式會產生大量遙測 (而且您正在使用 ASP.NET SDK 版本 2.0.0-beta3 或更新版本)，調適性取樣模組會自動縮減傳送代表分數的事件傳送至入口網站的磁碟區。 不過，與同一個要求相關的事件會選取或取消選取為群組，讓您可以在相關事件之間瀏覽。 
> [深入了解取樣](app-insights-sampling.md)。

## 後續步驟

- [使用者和頁面資料](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [例外狀況](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [相依項目](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## 若要升級至未來的 SDK 版本

若要升級至 [新版的 SDK](app-insights-release-notes-dotnet.md), ，再次開啟 NuGet 封裝管理員，並篩選出已安裝的封裝。 選取 Microsoft.ApplicationInsights.Web 然後選擇 [升級]。

如果您已對 ApplicationInsights.config 進行任何的自訂，請在升級前儲存複本，並在升級後合併您的變更到新版本中。



## <a name="video"></a>影片

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 
