<properties
    pageTitle="開始使用 Visual Studio Application Insights | Microsoft Azure"
    description="使用 Visual Studio Application Insights 分析內部部署或 Microsoft Azure Web 應用程式的使用情況、可用性和效能。"
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# 開始使用 Visual Studio Application Insights

*Application Insights 目前僅供預覽。*

偵測問題、解決問題並持續改善您的應用程式。 快速診斷即時應用程式中的任何問題。 了解您的使用者與其有無任何關係？

組態作業非常簡單，幾分鐘之內就能看到結果。

我們目前支援 iOS、Android 和 Windows 應用程式；J2EE 和 ASP.NET Web 應用程式以及 WCF 服務。 Web 應用程式可以在 Azure 或您的內部部署伺服器上執行。 我們的 JavaScript SDK 可在任何網頁上執行。

## 開始使用

依照任意順序開始使用出現在下列圖表之任意進入點的組合。 請遵循適合您的路徑操作。

Application Insights 的運作方式是將 SDK 加入至您的應用程式，可將傳送遙測， [Azure 入口網站](http://portal.azure.com)。 目前有不同的 SDK 適用於各個支援的平台、語言和 IDE 組合。

您將需要在帳戶 [Microsoft Azure](http://azure.com)。 您可能已透過組織獲得群組帳戶的存取權；或者，您可以使用隨用隨附帳戶。 Application Insights 有免費層，因此在您的應用程式受歡迎之前，您不需要支付任何費用。 檢閱 [定價頁面](https://azure.microsoft.com/pricing/details/application-insights/)。

您想要 | 怎麼做 | 得到什麼結果
---|---|---
 <a href="app-insights-asp-net.md">![ASP.NET](./media/app-insights-get-started/appinsights-gs-i-01-perf.png)</a> | <a href="app-insights-asp-net.md">將 Application Insights SDK 加入 Web 專案</a> <br/> ![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-asp-net.md">![效能與使用情形監視](./media/app-insights-get-started/appinsights-gs-r-01-perf.png)</a>
<a href="app-insights-monitor-performance-live-website-now.md">![ASP.NET 網站已上線](./media/app-insights-get-started/appinsights-gs-i-04-red2.png)</a><br/><a href="app-insights-monitor-performance-live-website-now.md">![相依性與效能監視](./media/app-insights-get-started/appinsights-gs-i-03-red.png)</a>|<a href="app-insights-monitor-performance-live-website-now.md">您的 IIS 伺服器上安裝狀態監視器</a> <br/> ![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-performance-live-website-now.md">![ASP.NET 相依性監視](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="insights-perf-analytics.md">![Azure Web 應用程式或 VM](./media/app-insights-get-started/appinsights-gs-i-10-azure.png)</a>|<a href="insights-perf-analytics.md">在您的 Azure web 應用程式或 VM 中啟用 Insights</a> <br/> ![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="insights-perf-analytics.md">![相依性與效能監視](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="app-insights-java-get-started.md">![Java](./media/app-insights-get-started/appinsights-gs-i-11-java.png)</a>|<a href="app-insights-java-get-started.md">將 SDK 加入至您的 Java 專案</a><br/>![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-java-get-started.md">![效能與使用情形監視](./media/app-insights-get-started/appinsights-gs-r-10-java.png)</a>
<a href="app-insights-web-track-usage.md">![JavaScript](./media/app-insights-get-started/appinsights-gs-i-02-usage.png)</a>|<a href="app-insights-web-track-usage.md">Application Insights 指令碼插入您的網頁</a><br/>![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-web-track-usage.md">![頁面檢視與瀏覽器效能](./media/app-insights-get-started/appinsights-gs-r-02-usage.png)</a>
<a href="app-insights-monitor-web-app-availability.md">![Availability](./media/app-insights-get-started/appinsights-gs-i-05-avail.png)</a>|<a href="app-insights-monitor-web-app-availability.md">建立 web 測試</a><br/>![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-web-app-availability.md">![Availability](./media/app-insights-get-started/appinsights-gs-r-05-avail.png)</a>
<a href="app-insights-platforms.md">![iOS、Android 和 Windows 裝置](./media/app-insights-get-started/appinsights-gs-i-07-device.png)</a>|<a href="http://hockeyapp.net">使用 HockeyApp</a><br/>![取得](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="http://hockeyapp.net">![當機與使用狀況資料](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>

## 支援與意見反應


* 問題：
 * [疑難排解][qna]
 * [MSDN 論壇](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* 錯誤：
 * [連線](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* 建議：
 * [使用者心聲 (英文)](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)
* 程式碼範例
 * [程式碼範例](app-insights-code-samples.md)



## <a name="video"></a>影片


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]



<!--Link references-->

[qna]: app-insights-troubleshoot-faq.md

