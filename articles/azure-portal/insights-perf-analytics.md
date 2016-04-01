<properties 
    pageTitle="監視 Azure Web 應用程式效能" 
    description="圖表載入和回應時間、相依性資訊以及設定效能警示。" 
    services="azure-portal"
    documentationCenter="na"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2015" 
    ms.author="awills"/>

# 監視 Azure Web 應用程式效能

在 [Azure 入口網站](http://portal.azure.com) 可以藉由監視收集的統計資料和詳細資料中的應用程式相依性設定您 [Azure web 應用程式](../app-service-web/app-service-web-overview.md) 或 [虛擬機器](../virtual-machines/virtual-machines-about.md)。

Azure 支援應用程式效能監視 (或， *APM*) 利用 *延伸*。 這些擴充功能已安裝在應用程式中，可收集資料並回報給監視服務。 

Application Insights 和 New Relic 是兩個可用的效能監視延伸模組。 若要使用這些模組，您需要在執行階段安裝代理程式。 藉由 Application Insights，還有可用 SDK 建置程式碼的選項。 SDK 可讓您撰寫程式碼來監視應用程式的詳細使用狀況和效能。

## 啟用擴充功能

1. 按一下 [ **瀏覽** ，然後選取 [web 應用程式或您想要的虛擬機器進行檢測。

2. 加入 Application Insights 或 New Relic 延伸模組。 

    如果您要檢測 Web 應用程式：

![設定、延伸模組、加入、Application Insights](./media/insights-perf-analytics/05-extend.png)

如果您使用的是虛擬機器：

![按一下 [分析] 磚。](./media/insights-perf-analytics/10-vm1.png)

### Application Insights 的選項：請使用 SDK 重建

Application Insights 可以提供更詳細的遙測，方法是將 SDK 安裝至您的 App。 

在 Visual Studio 中，將 Application Insights SDK 加入專案。

![以滑鼠右鍵按一下 Web 專案，然後選擇 [加入 Application Insights]。](./media/insights-perf-analytics/03-add.png)

系統要求您登入時，請使用 Azure 帳戶的認證。

您可以在開發電腦中執行應用程式來測試遙測，或是直接繼續執行並將重新發佈遙測。 

SDK 會提供 API，讓您可以 [撰寫自訂遙測](../app-insights-api-custom-events-metrics.md) 追蹤使用狀況。

## 探索資料

使用您的應用程式一段時間以產生一些遙測。

1. 接著，從您的 Web 應用程式或虛擬機器刀鋒視窗，您將會看到已安裝的擴充功能
2. 按一下代表您的應用程式瀏覽至該提供者的資料列 ︰
![按一下 [重新整理]](./media/insights-perf-analytics/06-overview.png)

您也可以使用 **瀏覽** ，直接移至 Application Insights 元件或您所使用的 New Relic 帳戶。

當您進入 Application insights 刀鋒視窗中，例如，您可以 ︰
- 開啟 [效能]:

![在 Application Insights 的概觀刀鋒視窗中，按一下 [效能] 磚](./media/insights-perf-analytics/07-dependency.png)

- 逐步深入以查看個別要求：

![在方格中，按一下相依性，查看相關的要求。](./media/insights-perf-analytics/08-requests.png)

- 以下範例說明在 SQL 相依性中花費的時間，該相依性中包括 SQL 的呼叫數和相關的統計資料 (例如平均持續時間和標準差)。 

![](./media/insights-perf-analytics/01-example.png) 



## 後續步驟

* [監視服務健康狀態計量](insights-how-to-customize-monitoring.md) 並確認您的服務可用且可回應。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md) 到您的服務中收集詳細的高頻率計量。
* [接收警示通知](insights-receive-alert-notifications.md) 每當發生操作事件或計量超過臨界值。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../app-insights-web-track-usage.md) 以取得有關造訪網頁瀏覽器的用戶端分析。
* [監視可用性和回應性的任何網頁](../app-insights-monitor-web-app-availability.md) 使用 Application Insights，因此您可以找出您的頁面是否關閉。
 

