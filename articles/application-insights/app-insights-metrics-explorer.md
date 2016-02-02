<properties 
    pageTitle="在 Application Insights 中探索度量" 
    description="如何解譯計量瀏覽器上的圖表，以及如何自訂計量瀏覽器刀鋒視窗。" 
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
    ms.date="11/23/2015" 
    ms.author="awills"/>


# 在 Application Insights 中探索度量

中的度量 [Application Insights 的 ][start] 測量的值和從您的應用程式傳送的遙測中的事件計數。 它們幫助您偵測效能問題，並觀察使用應用程式方式的趨勢。 標準度量的範圍很廣泛，而您也可以建立自己的自訂度量和事件。

度量和事件計數會顯示在彙總值 (例如總和、平均或計數) 的圖表中。

以下是範例圖表：

![開啟 Azure 入口網站中應用程式的 ](./media/app-insights-metrics-explorer/01-overview.png)

有些圖表經過分段：任何點的圖表高度總計為顯示的度量的總合。 圖例預設會顯示最大數量。

虛線顯示前一週度量的值。



## 時間範圍

您可以在任何刀鋒視窗上變更圖表或格線涵蓋的時間範圍。

![開啟 Azure 入口網站中應用程式的 ](./media/app-insights-metrics-explorer/03-range.png)


如果您預期的部分資料尚未出現，請按一下 [重新整理]。 圖表本身會定期重新整理，但是時間範圍越大，間隔時間會越長。 在發行模式中，資料從分析管線往圖表上顯示可能需要一些時間。

若要放大圖表的一部分，拖曳它，然後按一下 [放大鏡] 符號：

![拖曳過圖表的一部分。](./media/app-insights-metrics-explorer/12-drag.png)



## 資料粒度和點值

將滑鼠移至圖表上可顯示該點度量的值。

![將滑鼠移至圖表上](./media/app-insights-metrics-explorer/02-focus.png)

特定點的度量值會繼著前一個取樣間隔而彙總。

取樣間隔或「資料粒度」會顯示在刀鋒視窗的頂端。

![刀鋒視窗的標題。](./media/app-insights-metrics-explorer/11-grain.png)

您可以在時間範圍刀鋒視窗中調整資料粒度：

![刀鋒視窗的標題。](./media/app-insights-metrics-explorer/grain.png)

可提供的資料粒度取決於您選取的時間範圍。 明確的資料粒度可替代時間範圍內的「自動」資料粒度。

## 計量瀏覽器

按一下 [概觀] 刀鋒視窗上的任何圖表，可查看更詳細的一組相關圖表和格線。 您可以編輯這些圖表和格線，以著重在感興趣的詳細資料。

或者，您也可以按一下概觀刀鋒視窗頂端的 [計量瀏覽器] 按鈕。

例如，逐一點選 Web 應用程式的「失敗的要求」圖表：

![在 ](./media/app-insights-metrics-explorer/14-trix.png)


## 數據代表意義為何？

旁邊的圖例通常預設會顯示圖表在這段期間的彙總值。 如果您將滑鼠停留在圖表上，它會顯示在該點的值。

圖表上的每個資料點是在先前取樣間隔或「資料粒度」中所收到的資料值彙總。 資料粒度會顯示在刀鋒視窗頂端，並隨著圖表的時幅而有所不同。

度量可以用不同方式彙總：

 * **總和**：將取樣間隔或圖表期間收到的所有資料點的值相加。
 * **平均**：將總和除以間隔期間收到的資料點數目。
 * **唯一**：計數會用於使用者及帳戶的計數。 在取樣間隔或圖表期間，圖形顯示在該時間看到的不同使用者的計數。


您可以變更彙總方法：

![選取圖表，然後選取彙總](./media/app-insights-metrics-explorer/05-aggregation.png)

當您建立新的圖表時，會顯示每個度量的預設方法：

![取消選取所有度量以查看預設值](./media/app-insights-metrics-explorer/06-total.png)


## 編輯圖表和格線

若要對刀鋒視窗加入新圖表：

![在 ](./media/app-insights-metrics-explorer/04-add.png)

選取現有或新圖表來編輯其顯示的內容：

![選取一或多個度量](./media/app-insights-metrics-explorer/08-select.png)

您可以在圖表上顯示一個以上的度量，不過，可以一起顯示的組合有一些限制。 只要您選取一個度量，便會停用某些其他度量。

如果您編寫 [自訂度量 ][track] 到您的應用程式 (對 TrackMetric 和 TrackEvent 呼叫)，將這裡予以列出。

## 分段資料

選取圖表或格線，將分組切換成開啟，並選取要分組依據的屬性：

![選取 ](./media/app-insights-metrics-explorer/15-segment.png)

如果您編寫 [自訂度量 ][track] 到您的應用程式，它們包含屬性值，您可以在清單中選取的屬性。

圖表是否對分段的資料來說太小？ 調整其高度：


![調整滑桿](./media/app-insights-metrics-explorer/18-height.png)


## 篩選資料

若只要查看選取的一組屬性值的度量：

![按一下 ](./media/app-insights-metrics-explorer/19-filter.png)

如果您不為特定屬性選取任何值，這與將它們全選相同：該屬性上沒有篩選器。

請注意每個屬性值旁的事件計數。 選取一個屬性的值時，會調整計數與其他屬性值。

### 將屬性加入篩選器清單

您要根據自己選擇的類別篩選遙測嗎？ 比方說，劃分成不同的類別，使用者可能和您想要依照這些類別分割您的資料。

[建立自己的屬性](app-insights-api-custom-events-metrics.md#properties)。 設定 [遙測初始設定式](app-insights-api-custom-events-metrics.md#telemetry-initializers) 讓它出現在所有遙測-包括標準遙測模組傳送的不同 SDK。

## 移除 Bot 和 Web 測試流量

使用篩選器**真實或綜合流量**並勾選**真實**。

您也可以依**綜合流量的來源**篩選。

## 編輯圖表類型

特別是，請注意您可以在格線與圖形之間切換：

![選取格線或圖表，然後選擇圖表類型](./media/app-insights-metrics-explorer/16-chart-grid.png)

## 儲存您的度量分頁

建立一些圖表後，請將它們儲存為我的最愛。 如果您使用組織帳戶，可以選擇是否要將它與他小組成員分享。

![選擇 ](./media/app-insights-metrics-explorer/21-favorite-save.png)

若要再次查看分頁，請**前往 [概觀] 分頁**，並開啟 [我的最愛]：

![在 ](./media/app-insights-metrics-explorer/22-favorite-get.png)

如果儲存時選擇「相對」時間範圍，會以最新度量更新刀鋒視窗。 如果選擇「絕對」時間範圍，它會每次都顯示相同資料。

## 重設刀鋒視窗

如果您編輯刀鋒視窗，但之後想要回到原始儲存的集合，只需要按一下 [重設]。

![在 ](./media/app-insights-metrics-explorer/17-reset.png)

## 設定警示

若要在任何度量有不尋常的值時收到電子郵件通知，請加入警示。 您可以選擇將電子郵件傳送給帳戶管理員，或傳送給特定的電子郵件地址。

![在 ](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[深入了解警示 ][alerts]。

## 匯出至 Excel

您可以將 [計量瀏覽器] 中顯示的度量資料匯出到 Excel 檔案。 匯出的資料包括入口網站中所示所有圖表和資料表的資料。


![在 ](./media/app-insights-metrics-explorer/31-export.png)

每個圖表或資料表的資料都會匯出到 Excel 檔案中的個別工作表。

您看到的內容即為匯出的內容。 如果您想要變更所匯出資料的範圍，請變更時間範圍或篩選器。 對於資料表，如果有顯示 [**載入更多**] 命令，您可以先按它再按一下 [匯出]，以匯出更多資料。

* 匯出僅適用於 Internet Explorer 和 Chrome 目前。 我們正努力新增其他 browsers.* 的支援

### 連續匯出

如果您想要連續匯出，使您可以在外部處理的資料，請考慮使用 [連續匯出](app-insights-export-telemetry.md)。

### Power BI

如果您想要有更豐富您的資料檢視，您可以 [匯出至 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)。

## 後續步驟

* [使用 Application Insights 監視使用情況](app-insights-overview-usage.md)
* [使用診斷搜尋](app-insights-diagnostic-search.md)





[alerts]: app-insights-alerts.md 
[start]: app-insights-overview.md 
[track]: app-insights-api-custom-events-metrics.md 

