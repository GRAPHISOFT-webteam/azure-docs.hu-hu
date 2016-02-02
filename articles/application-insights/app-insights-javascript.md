<properties
    pageTitle="JavaScript Web App 適用的 Application Insights | Microsoft Azure"
    description="取得頁面檢視和工作階段計數、Web 用戶端資料，並追蹤使用量模式。Detect exceptions and performance issues in JavaScript web pages."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>


# 適用 JavaScript Web 應用程式的 Application Insights

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

了解網頁的效能和使用量。 將 Visual Studio Application Insights 加入至您的頁面，您會了解您擁有多少使用者、他們回來的頻率，以及他們最常使用哪些頁面。 您也可以取得載入時間和任何例外狀況的報告。 加入幾個 [自訂事件和度量](app-insights-api-custom-events-metrics.md), ，而且您可以詳細分析最受歡迎的功能，最常見的錯誤，並調整頁面以滿足使用者。

![選擇 ](./media/app-insights-javascript/16-page-views.png)

如果您已經設定伺服器遙測您 [ASP.NET](app-insights-asp-net.md) 或 [Java](app-insights-java-get-started.md) web 應用程式，您會從用戶端和伺服器角度取得的圖片。 兩個資料流會在 Application Insights 入口網站中整合。

#### 快速示範

如果您沒有 Azure 訂用帳戶，而且想要嘗試在網頁上的 Application Insights，請造訪 [嘗試 Application Insights](http://aka.ms/ainow)。

## 開啟 Application Insights 資源

Application Insights 資源是您的頁面的效能和使用量相關資料顯示的位置。 (如果您已經建立資源，或許會從您的 Web 伺服器收集資料，略過此步驟)。

登入 [Azure 入口網站](http://portal.azure.com)。

如果您已經設定好應用程式伺服器端的監視，您已經擁有資源：

![選擇 ](./media/app-insights-javascript/01-find.png)

如果您沒有資源，請建立資源：

![選擇 ](./media/app-insights-javascript/01-create.png)


*已經有問題了嗎？*[建立資源的詳細資訊](app-insights-create-new-resource.md)。


## 將 SDK 指令碼加入至您的應用程式或網頁

在快速入門中，取得網頁指令碼：

![在您的應用程式概觀刀鋒視窗中，選擇 [快速入門]，取得程式碼來監視我的網頁。 複製指令碼]。(。 / media/app-insights-javascript/02-monitor-web-page.png)

在您想要追蹤之每一個頁面的 </head> 標記之前插入指令碼。 如果您的網站有主版頁面，您可以那裡放入指令碼。 例如：

* 在 ASP.NET MVC 專案中，您可放在 View\Shared\\_Layout.cshtml
* 在 SharePoint 網站中，在控制台] 中開啟 [站台設定 / 主版頁面](app-insights-sharepoint.md)。

指令碼包含檢測金鑰，會將資料導向您的 Application Insights 資源。

([的指令碼。 深入說明](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

* (如果您使用的是已知網頁架構，請尋找 Application Insights 配接器。 例如，沒有 [AngularJS 模組](http://ngmodules.org/modules/angular-appinsights).)*


## <a name="run"></a>執行您的應用程式

執行您的 Web 應用程式，稍微使用一下來產生遙測，並等候數秒鐘。 您可以執行使用  **F5** 您的開發電腦上的主要或將它發行，並且讓使用者使用。

如果您想要檢查 Web 應用程式傳送至 Application Insights 的遙測，請使用您瀏覽器的偵錯工具 (在許多瀏覽器上為 **F12**)。 資料會傳送至 dc.services.visualstudio.com。

## 探索資料

在應用程式概觀刀鋒視窗中，頂端附近有圖表顯示將頁面載入瀏覽器的平均時間：


![](./media/app-insights-javascript/05-browser-page-load.png)


還沒有資料嗎？ 按一下頁面頂端的 [**重新整理**]。 仍然沒有嗎？ 請參閱 [疑難排解](app-insights-troubleshoot-faq.md)。 *

按一下該圖表，您會取得更詳細的版本：

![](./media/app-insights-javascript/07-client-perf.png)

這是堆疊的圖表的總頁面載入時間細分為 [W3C 所定義的標準時間](http://www.w3.org/TR/navigation-timing/#processing-model)。

![](./media/app-insights-javascript/08-client-split.png)

請注意，*網路連接*時間通常低於您所預期的時間，因為它是從瀏覽器到伺服器之所有要求的平均值。 許多個別要求的連接時間為 0，因為已經有與伺服器的作用中連線。


### 依據頁面的效能

進一步探索詳細資料刀鋒視窗，有一個依據網頁 URL 分割的方格：


![](./media/app-insights-javascript/09-page-perf.png)

如果您想要查看一段時間的頁面效能，請按兩下方格，並變更其圖表類型：

![](./media/app-insights-javascript/10-page-perf-area.png)

## 用戶端使用量概觀

返回 [概觀] 刀鋒視窗上，按一下 **[使用量]**：

![](./media/app-insights-javascript/14-usage.png)

* **使用者：**圖表的一段時間範圍內不同使用者的計數。 (Cookie 是用來識別傳回的使用者。)
* **工作階段：**當使用者未進行任何要求達 30 分鐘，就會計算為工作階段。
* **頁面檢視** 計算對 trackPageView() 呼叫的數目，通常在每個網頁中呼叫一次。


### 逐一點選以獲得詳細資料

逐一點選任何圖表以查看詳細資料。 請注意，您可以變更圖表的時間範圍。

![](./media/app-insights-javascript/appinsights-49usage.png)


按一下圖表以查看您可以顯示的其他度量，或加入新圖表並選取它要顯示的度量。

![](./media/app-insights-javascript/appinsights-63usermetrics.png)
> [AZURE.NOTE] 度量只能以某種組合來顯示。 當您選取度量時，不相容的度量會停用。



## 自訂頁面計數

依預設，每當用戶端瀏覽器載入新頁面時，頁面計數便會發生。 不過您也許會想要計算其他頁面檢視。 例如，由於頁面可能會將內容顯示在索引標籤中，因此您想要在使用者切換索引標籤時計算一次頁面。 抑或是頁面中的 JavaScript 程式碼可能會在未變更瀏覽器 URL 的情況下載入新內容。

請將與以下範例相似的 JavaScript 呼叫插入用戶端程式碼中的適當位置：

    appInsights.trackPageView(myPageName);

頁面名稱可能會含有與 URL 相同的字元，不過 "#" 或 "?" 之後的任何字元都將遭到忽略。


## 檢查個別的頁面檢視事件

頁面檢視遙測資料一般是由 Application Insights 進行分析，而您只會看見以所有使用者為單位平均計算的累積報告。 然而在偵錯時，您也可以查看個別的頁面檢視事件。

在 [Diagnostic Search] 分頁中，將 [篩選器] 設定為 [網頁檢視]。

![](./media/app-insights-javascript/12-search-pages.png)

選取任一事件以查看詳細資料。 在詳細資料頁面中，按一下 "..." 來查看更多詳細資料。
> [AZURE.NOTE] 如果您使用 [搜尋](app-insights-diagnostic-search.md), ，請注意，您必須符合整個單字:"Abou"和"bout"不符 「 關於 」，但是"Abou *"。 此外，您不能將萬用字元當做搜尋詞彙的開頭。 例如，搜尋 "*bou" 將無法與 "About" 相符。

> [深入了解診斷搜尋](app-insights-diagnostic-search.md)

### 頁面檢視屬性

* **網頁檢視期間**：載入網頁並開始執行指令碼所花費的時間。 具體來說，開始載入頁面及執行 trackPageView 之間的間隔。 如果您在指令碼的初始設定之後將 trackPageView 從一般位置移走，它會反映不同的值。

## 自訂使用情況追蹤

想要了解使用者如何使用您的應用程式嗎？ 在您的用戶端和伺服器程式碼中插入呼叫，可以將您自己的遙測傳送至 Application Insights。 例如，您可以了解雖然建立訂單但未完成的使用者數目、最常發生的驗證錯誤，或遊戲中的平均分數。

* [深入了解自訂事件和度量 API](app-insights-api-custom-events-metrics.md)。
* [API 參考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## 伺服器遙測

如果您尚未這樣做，您可以從您的伺服器取得詳細資料，並且與用戶端的資料一同顯示，讓您評估伺服器的效能和診斷任何問題。 只要將 Application Insights SDK 加入至應用程式：

* [將 SDK 加入至 ASP.NET 應用程式](app-insights-asp-net.md)
* [將 SDK 加入至 Java web 應用程式](app-insights-java-get-started.md)

或者，如果您的 Web 應用程式已上線，您仍可新增伺服器遙測，而不必重建或重新部署：

* [監視即時的 ASP.NET 應用程式](app-insights-monitor-performance-live-website-now.md)
* [監視即時的 Java 應用程式](app-insights-java-live.md)

## <a name="video"></a> 影片: 追蹤使用量

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 後續步驟

* [追蹤使用量](app-insights-web-track-usage.md)
* [自訂事件和度量](app-insights-api-custom-events-metrics.md)
* [建置-測量-學習](app-insights-overview-usage.md)







