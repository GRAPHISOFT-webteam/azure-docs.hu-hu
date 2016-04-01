<properties 
    pageTitle="監視服務計量" 
    description="了解如何在 Azure 自訂監視圖表。" 
    authors="stepsic-microsoft-com" 
    manager="ronmart" 
    editor="" 
    services="azure-portal"
documentationCenter=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2015" 
    ms.author="stepsic"/>

# 監視服務計量

所有的 Azure 服務都會追蹤重要計量，可讓您監視服務的健全狀況、效能、可用性和使用情況。 您可以在 Azure 網站中，檢視這些計量，而且您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式設計方式存取完整度量集合。

在某些服務中，您可能需要開啟診斷，才能查看任何計量。 在其他服務中 (例如虛擬機器)，您將會取得基本的計量集合，但需要啟用完整的高頻率計量集合。 請參閱 [啟用監視和診斷](insights-how-to-use-diagnostics.md) 若要深入了。

## 使用監視圖表 

您可以製作任何選擇時間期間上的計量圖表。

1. 在 [Azure 入口網站](https://portal.azure.com/), ，按一下 [ **瀏覽**, ，則資源就會想要監視。

2.  **監視** 區段包含每個 Azure 資源最重要的指標。 例如，web 應用程式具有 **要求和錯誤**, ，其中虛擬機器則具有 **CPU 百分比** 和 **磁碟讀取和寫入**:
    ![監視透鏡](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. 按一下任何一張圖表會顯示 **度量** 刀鋒視窗。 在此刀鋒視窗上，除了一個圖形之外，還會有一個顯示這些計量彙總 (例如選擇時間範圍上的平均值、最小值和最大值) 的資料表。 下面是資源的警示規則。
    ![計量刀鋒伺服器](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. 若要自訂出現的資料行，請按一下 [ **編輯** 圖表上的按鈕或， **編輯圖表** 度量分頁上的命令。

5. 在 [編輯查詢] 刀鋒視窗上，您可以執行三個動作：
    - 變更時間範圍
    - 在長條圖與折線圖之間切換外觀
    - 選擇不同的計量
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. 變更時間範圍非常簡單，只要選取不同的範圍 (例如 **過去一小時**)，然後按一下 **儲存** 刀鋒視窗的底部。 您也可以選擇 **自訂**, ，可讓您選擇過去 2 週的任何一段時間。 例如，您可以完整檢視這兩週，或僅檢視昨天的某 1 小時。 請在文字方塊中輸入不同的小時。
    ![自訂時間範圍](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. 在時間範圍下，您可以選擇任何要顯示在圖表上的度量數。

8. 當您按一下 [儲存] 時，系統便會儲存為該特定資源所做的變更。 例如，如果您有兩部虛擬機器，若變更其中一部虛擬機器的圖表，它將不會影響到另一部虛擬機器的圖表。

## 建立並排圖表

使用入口網站中的強大自訂功能，您可以新增任意數目的圖表。

1. 在 **...** 刀鋒視窗中按一下頂端功能表 **新增磚**:  
    ![新增功能表](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 然後，您可以選取圖表從 **圖庫** 螢幕右邊 ︰
    ![組件庫](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 如果您沒有看到度量需要，您可以隨時新增其中一個預設計量，以及 **編輯** 圖表以顯示您所需要的度量。 

## 監視使用量配額

大部分的計量會顯示某一段時間的趨勢，但是某些特定資料 (如使用量配額) 則會是包含臨界值的時間點資訊。

您也可以在刀鋒視窗上查看具有配額之資源的使用量配額： 

![使用量](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

和計量一樣，您可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以程式設計方式存取完整的使用量配額集合。

## 後續步驟

* [接收警示通知](insights-receive-alert-notifications.md) 每當計量超出臨界值。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md) 到您的服務中收集詳細的高頻率計量。
* [自動調整執行個體計數](insights-how-to-scale.md) 並確認您的服務可用且可回應。
* [監視應用程式效能](insights-perf-analytics.md) 如果您想要了解如何您的程式碼執行定域機組中。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../app-insights-web-track-usage.md) 以取得有關造訪網頁瀏覽器的用戶端分析。
* [監視可用性和回應性的任何網頁](../app-insights-monitor-web-app-availability.md) 使用 Application Insights，因此您可以找出您的頁面是否關閉。
 

