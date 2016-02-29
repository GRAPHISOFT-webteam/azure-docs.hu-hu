<properties
    pageTitle="調整串流分析工作以增加輸送量 | Microsoft Azure"
    description="了解如何透過設定輸入資料分割、微調查詢定義，及設定工作串流處理單元來調整串流分析工作。"
    keywords="data streaming, streaming data processing, tune analytics"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="12/16/2015"
    ms.author="jeffstok"/>

# 調整 Azure 串流分析工作，以提高串流資料處理的輸送量 #

了解如何微調分析工作並計算 *串流處理單元* 串流分析如何調整資料流分析工作，藉由設定輸入資料分割、 微調分析查詢定義及設定工作串流處理單元。 

## 串流分析工作由哪些部分所組成？ ##
串流分析工作的定義包含輸入、查詢及輸出。 輸入是工作從中讀取串流之處，查詢則是用來轉換輸入串流，而輸出是工作將工作結果傳送到的位置。  

一個工作至少需要一個輸入來源來進行資料串流。 資料流輸入來源可以儲存在 Azure 服務匯流排事件中樞或 Azure Blob 儲存體。 如需詳細資訊，請參閱 [Azure 串流分析簡介](stream-analytics-introduction.md), ，[開始使用 Azure 串流分析](stream-analytics-get-started.md), ，和 [Azure 串流分析開發人員指南](../stream-analytics-developer-guide.md)。

## 設定串流處理單位 ##
串流處理單位 (SU) 代表執行 Azure 串流分析工作的資源和能力。 SU 會根據 CPU、記憶體，以及讀寫率的混合量值，提供一個方式來描述相關的事件處理容量。 每個串流處理單元會對應至約 1 MB/秒的輸送量。 

選擇特定工作所需的 SU 數量取決於輸入的磁碟分割設定以及針對工作定義的查詢。  您可以使用 Azure 入口網站，為工作選取串流處理單元的配額上限。 在特定區域中，每個 Azure 訂用帳戶的所有分析工作預設最多有 50 個串流處理單元的配額。  若要增加您的訂閱的串流處理單元，請連絡 [Microsoft 支援服務](http://support.microsoft.com)。

工作可以使用的串流處理單元數目，取決於輸入的資料分割設定以及為工作定義的查詢。 此外請注意，必須使用有效的串流處理單元的值。 有效值從 1、3、6 開始，然後以 6 向上遞增，如下所示。

![Azure 串流分析的串流處理單元調整][img.stream.analytics.streaming.units.scale]

本文將說明如何計算及微調查詢，以增加分析工作的輸送量。

## 計算工作的最大串流處理單元 ##
資料流分析工作可使用的串流處理單元總數，取決於為工作定義之查詢中的步驟數目，和每個步驟的資料分割數目。

### 查詢中的步驟 ###
一個查詢可以有一或多個步驟。 每個步驟都是使用 WITH 關鍵字定義的子查詢。 WITH 關鍵字唯一的查詢例外也會當成步驟執行，例如，下列查詢中的 SELECT 陳述式：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

上述查詢有兩個步驟。

> [AZURE.NOTE] 在本文稍後將說明此範例查詢。

### 分割步驟 ###

要分割步驟必須符合下列條件：

- 必須分割輸入來源。 如需詳細資訊，請參閱 [Azure 串流分析開發人員指南](../stream-analytics-developer-guide.md) 和 [事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)。
- 查詢的 SELECT 陳述式必須讀取分割的輸入來源。
- 步驟內的查詢必須有 **Partition By** 關鍵字

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中，並且會為每個群組產生輸出事件。 如果需要結合彙總，您必須建立第二個非資料分割的步驟以進行彙總。

### 計算工作的最大串流處理單元 ###

所有非資料分割的步驟可以整合，而將串流分析工作的串流處理單元調整為最多 6 個。 若要新增更多串流處理單元，則必須分割步驟。 每個資料分割可以有 6 個串流處理單元。

<table border="1">
<tr><th>工作的查詢</th><th>工作的最大串流處理單元</th></td>

<tr><td>
<ul>
<li>查詢包含一個步驟。</li>
<li>步驟未分割。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>輸入資料流分割時會除以 3。</li>
<li>查詢包含一個步驟。</li>
<li>步驟進行分割。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>查詢包含兩個步驟。</li>
<li>兩個步驟都不會分割。</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>資料流輸入分割時會除以 3。</li>
<li>查詢包含兩個步驟。 輸入步驟會分割，而第二個步驟則否。</li>
<li>SELECT 陳述式會讀取分割的輸入。</li>
</ul>
</td>
<td>24 (18 個用於分割的步驟 + 6 個用於非分割的步驟)</td></tr>
</table>

### 調整的範例 ###
下列查詢會以三個收費亭為準，計算在 3 分鐘的時間範圍內通過收費站的車流量。 此查詢可以調整為最多 6 個串流處理單元。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

若要讓查詢使用更多串流處理單元，必須同時分割資料流輸入和查詢。 假設資料流資料分割設為 3，則下列修改的查詢最多可以調整為 18 個串流處理單元：

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

分割查詢時將會處理輸入事件並將其彙總在個別的資料分割群組中。 也會為每個群組產生輸出事件。 資料分割可能會導致某些非預期的結果時 **Group by** 欄位不是資料劉輸入中的資料分割索引鍵。 例如，上述範例查詢中的 TollBoothId 欄位不是 Input1 資料分割索引鍵。 收費亭 #1 中的資料可能分散在多個資料分割中。

每個 Input1 資料分割都將由串流分析個別處理，並且會建立相同的收費亭在相同輪動時間範圍內之車流量計數的多筆記錄。 如果輸入資料分割索引鍵無法變更，此問題可藉由新增額外的非資料分割步驟來解決，例如：

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

此查詢可以調整為 24 個串流處理單元。

>[AZURE.NOTE] 如果您要聯結兩個資料流，請確定資料流分割方式，執行聯結，而且您在兩個資料流中有相同數目的資料分割的資料行的資料分割索引鍵。


## 設定資料流分析工作資料分割 ##

**若要調整工作的串流處理單元**

1. 登入 [管理入口網站](https://manage.windowsazure.com)。
2. 按一下 [ **資料流分析** 的左窗格中。
3. 按一下您要調整的資料流分析工作。
4. 按一下 [ **延展** 頁面的頂端。

![Azure 串流分析的串流處理單元調整][img.stream.analytics.streaming.units.scale]

在 Azure Preview 入口網站的 [設定] 下可以調整設定：

![Azure Preview 入口網站的串流分析工作組態][img.stream.analytics.preview.portal.settings.scale]

## 監視工作效能 ##

使用管理入口網站可讓您追蹤工作的輸送量 (事件數/秒)：

![Azure 串流分析監視工作][img.stream.analytics.monitor.job]

計算工作負載的預期輸送量 (事件數/秒)。 如果輸送量少於預期，請微調輸入資料分割、微調查詢，並為工作新增更多串流處理單元。

## 大規模 ASA 輸送量 - Raspberry Pi 案例 ##


為了解 ASA 在跨多個串流處理單位處理輸送量的典型案例下會如何調整，以下實驗會將感應器資料 (用戶端) 傳送到事件中樞，ASA 加以處理後，再將警示或統計資料當成輸出傳送到另一個事件中樞。

用戶端正在將綜合感應器資料以 JSON 格式傳送到 ASA 的事件中樞，而資料輸入也是 JSON 格式。  範例資料看起來如下–  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

查詢：「關燈時傳送警示」  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

測量輸送量：此內容中的輸送量是 ASA 在固定時間 (10 分鐘) 內處理的輸入資料量。 若要讓輸入資料的輸送量處理達到最佳，必須同時分割資料流輸入和查詢。 同時在查詢中包含 COUNT() 來測量已處理的輸入事件數目。 為確保 ASA 不會單純只等待輸入事件到來，因而已將足夠的輸入資料 (大約 300MB) 預先載入輸入事件中樞的每個資料分割。  

以下是事件中樞中數目持續增加的串流處理單位與對應的資料分割計數。  

<table border="1">
<tr><th>輸入資料分割</th><th>輸出資料分割</th><th>串流處理單位</th><th>持續的輸送量
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/秒</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/秒</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/秒</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/秒</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/秒</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/秒</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## 取得說明 ##
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## 後續步驟 ##

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](stream-analytics-get-started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
