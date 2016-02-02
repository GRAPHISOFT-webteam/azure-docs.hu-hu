<properties 
    pageTitle="管理 Application Insights 的定價和配額" 
    description="選擇您需要的價格方案" 
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
    ms.date="11/18/2015" 
    ms.author="awills"/>


# 管理 Application Insights 的定價和配額

*Application Insights 目前僅供預覽。*

[定價 ][pricing] 的 [Visual Studio Application Insights ][start] 根據每個應用程式的資料量。 我們提供實質的免費層，讓您在該層次中使用大部分的功能，但會有一些限制。

每項 Application Insights 資源的收費方式採個別服務計價，並計入您 Azure 訂用帳戶的帳單。

[請參閱 ][pricing]。

## 檢視 Application Insights 資源的配額和價格方案

您可以在應用程式資源的 [設定] 中開啟 [配額 + 定價] 刀鋒視窗。

![依序選擇 ](./media/app-insights-pricing/01-pricing.png)

您選擇的定價結構描述會影響：

* [每月配額](#monthly-quota) -遙測，您可以分析每個月的數量。
* [資料速率](#data-rate) -從應用程式可以處理的最大速率。
* [保留](#data-retention) -long 資料是保存在 Application Insights 入口網站，讓您檢視。
* [連續匯出](#continuous-export) -是否可以將資料匯出到其他工具和服務。

這些限制會分別為每個 Application Insights 資源加以設定。

### 免費試用高階

當您第一次建立新的 Application Insights 資源時，會先從免費層開始。

您可以隨時切換成免費試用 30 天的高階版。 如此便可體驗高階層的優點。 30 天之後，如果您未明確選擇另一個層次，它會自動還原到您之前所在的任一層次。 您可以在試用期間隨時選取想要的層次，但在 30 天的期限結束前，您仍可以免費試用。


## 每月配額

* 您的應用程式每月最多可以將指定的遙測資料量上傳至 Application Insights。 請參閱 [定價配置 ][pricing] 實際數字。
* 配額取決於您所選擇的定價層。
* 配額的計算是從每個月第一天的午夜起 (UTC)。
* 資料點圖表會顯示您這個月已使用多少配額。
* 配額是以**資料點來進行測量。不論您的程式碼，或其中一個標準遙測模組是否有明確呼叫，單一資料點都是其中一種追蹤方法的呼叫。 資料點包括：
 * 您在中看到每個資料列 [診斷搜尋](app-insights-diagnostic-search.md)。
 * 每個原始測量 [度量](app-insights-metrics-explorer.md) 效能計數器等。 (您在圖表看到的點通常是多個原始資料點的彙總)。
 * 在每個點 [web 測試 (可用性)](app-insights-monitor-web-app-availability.md) 圖表。
* **工作階段資料不會計入配額。 這包括使用者、工作階段、環境和裝置資料的計數。


### 超額部分

如果您的應用程式傳送的配額超過每月上限，您可以：

* 為額外的資料付費： 請參閱 [定價配置 ][pricing] 如需詳細資訊。 您可以預先選擇此選項。 免費定價層中沒有此選項。
* 升級定價層。
* 不做任何動作。 系統仍會繼續記錄工作階段資料，但診斷搜尋或計量瀏覽器中將不會顯示其他資料。


### 正在傳送多少資料？

[價格] 刀鋒視窗底部的圖表中，會依資料點數量分組，顯示您應用程式的資料點使用量。 (您也可以在 [計量瀏覽器] 中建立此圖表。)

![位於 ](./media/app-insights-pricing/03-allocation.png)

按一下圖表可取得詳細資料，或在圖表上拖曳並按 (+) ，以取得某個時間範圍內的詳細資料。


## 資料速率

除了每月配額，還有資料速率的節流限制。 針對免費 [定價層 ][pricing] 限制為 200 個資料點每秒平均超過 5 分鐘，然後針對它付費層 500/s 平均值超過 1 分鐘。

有三個個別計數的貯體：

* [TrackTrace calls](app-insights-api-custom-events-metrics.md#track-trace) and [captured logs](app-insights-asp-net-trace-logs.md)
* [例外狀況](app-insights-api-custom-events-metrics.md#track-exception), ，限制為 50 點/s。
* 所有其他遙測 (頁面檢視、工作階段、要求、相依性、度量、自訂事件、Web 測試結果)。

如果您的應用程式傳送超過限制的數量達數分鐘，某些資料可能會被捨棄。 您會看到通知，警告這種情況的發生。

### 降低資料速率的秘訣

如果您遇到節流限制，以下是您可以執行的一些事項：

* Use [Sampling](app-insights-sampling.md). 這項技術可減少資料率而不會曲解您的計量，且不會中斷在 [搜尋] 中於相關項目之間瀏覽的能力。
* 關閉您不需要收集模組 [編輯 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)。 例如，您可能會決定效能計數器或相依性資料是不必要的。
* 預先彙總度量。 如果您在應用程式中呼叫 TrackMetric，您可以使用接受批次測量之平均及標準差計算的多載來減少流量。 或者您可以使用 [預先彙總套件](https://www.myget.org/gallery/applicationinsights-sdk-labs)。


### 名稱限制

1.  您的應用程式具有最多 200 個唯一度量名稱和 200 個唯一屬性名稱。 度量包括透過 TrackMetric，以及其他資料類型，例如事件上的度量單位傳送的資料。 [計量和屬性名稱 ][api] 是全域的每個檢測金鑰不只限於資料類型。
2.  [屬性 ][apiproperties] 可以用於篩選和分組方式，只有在具有少於 100 個唯一值，每個屬性時。 唯一值超過 100 之後，屬性仍可用於搜尋與篩選，但無法用於篩選器。
3.  標準屬性，例如要求名稱和網頁 URL 會限制為每週 1000 個唯一值。 超過 1000 個唯一值之後，額外值都會標示為「其他值」。 原始值仍然可以用於全文檢索搜尋和篩選。

## 資料保留

價格層會決定資料保存在入口網站的時間長度，並因此決定您可以設定的時間範圍。


* 未處理的資料點 (即您可以在 [診斷搜尋] 中檢查的執行個體)：7 至 30 天。
* 彙總的資料 (即您在計量瀏覽器中看到的計數、平均和其他統計資料) 在 1 分鐘的資料粒度中保存 30 天，而 1 小時或 1 天 (視類型而定) 則保存至少 13 個月。




## 檢視 Azure 訂用帳戶的帳單

Application Insights 費用會加到您的 Azure 帳單中。 帳單帳務] 區段中的 Azure 入口網站，或是在您的 Azure 的詳細資料請參閱 < [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)。

![選擇側邊功能表中的 ](./media/app-insights-pricing/02-billing.png)

## 限制摘要

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]





[api]: app-insights-api-custom-events-metrics.md 
[apiproperties]: app-insights-api-custom-events-metrics.md#properties 
[start]: app-insights-overview.md 
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/ 

