<properties
    pageTitle="如何監視儲存體帳戶 | Microsoft Azure"
    description="了解如何使用 Azure 入口網站來監視 Azure 中的儲存體帳戶。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="adinah"
    editor="cgronlun"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="tamram"/>

# 在 Azure 入口網站中監視儲存體帳戶

## 概觀

您可以監視從儲存體帳戶 [Azure 入口網站](portal.azure.com)。 當您設定透過入口網站來監視儲存體帳戶時，會使用 Azure 儲存體 [儲存體分析](http://msdn.microsoft.com/library/azure/hh343270.aspx) 來追蹤您帳戶的度量和記錄要求資料。

> [AZURE.NOTE] 其他成本也與查看監視資料相關 [Azure 入口網站](portal.azure.com)。 如需詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">儲存體分析及計費</a>。 <br />

> Azure 檔案儲存體目前支援儲存體分析度量，但還不支援記錄。 您可以啟用透過 Azure 檔案儲存體度量 [Azure 入口網站](portal.azure.com)。

> 如需使用儲存體分析和其他工具來識別、 診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、 診斷與疑難排解 Microsoft Azure 儲存體](storage-monitoring-diagnosing-troubleshooting.md)。


## 如何：設定儲存體帳戶的監視

1. 在 [Azure 入口網站](portal.azure.com), ，按一下 [ **儲存體**, ，然後按一下 [儲存體帳戶名稱，以開啟儀表板。

2. 按一下 [ **設定**, ，向下捲動至 **監視** blob、 資料表和佇列服務的設定。

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. 在 **監視**, ，設定監視等級和資料保留原則，針對每個服務:

    -  若要設定監視等級，請選取下列其中一項：

      **最小** -收集度量，例如入口流量/出口流量、 可用性、 延遲和成功百分比，針對 blob、 資料表和佇列服務彙總。

      **Verbose** -除了最小度量收集相同的 Azure 儲存體服務 API 中的每個儲存體作業的度量集合。 詳細資訊度量可供進一步分析在應用程式運作期間發生的問題。

      **關閉** -關閉監視。 現有的監視資料會儲存到保留期間結束。

- 若要設定資料保留原則，在 **保留期限 (以天為單位)**, ，輸入要保留從 1 到 365 天的資料的天數。 如果不想設定保留原則，則請輸入零。 如果沒有保留原則，您可以決定是否刪除監視資料。 我們建議根據要保留帳戶的儲存體分析資料多久來設定保留原則，以便系統可以不花費成本地刪除舊的無用分析資料。

4. 當您完成監視組態時，按一下 [ **儲存**。

您應該會開始看到監視資料的儀表板和 **監視** 頁面之後，大約一小時。

直到您設定監視儲存體帳戶，請收集任何監視資料，以及儀表板上的度量圖表和 **監視** 頁面是空的。

設定監視等級與保留原則之後，您可以選擇其中一個可用的度量來監視在 [Azure 入口網站](portal.azure.com), ，以及在度量圖表上繪製哪些度量。 每個監視等級會顯示一組預設的度量。 您可以使用 **加入度量** 新增或移除度量清單中的度量。

度量存放在儲存體帳戶中的四個資料表：$MetricsTransactionsBlob、$MetricsTransactionsTable、$MetricsTransactionsQueue 及 $MetricsCapacityBlob。 如需詳細資訊，請參閱 [關於儲存體分析度量](http://msdn.microsoft.com/library/azure/hh343258.aspx)。


## 如何：自訂儀表板以進行監視

在儀表板上，您可以從九個可用度量中選擇最多六個度量，繪製在度量圖表上。 針對每個服務 (Blob、資料表和佇列)，可以使用「可用性」、「成功百分比」和「要求總數」等度量。 對於最小或詳細資訊監視，儀表板上的可用度量相同。

1. 在 [Azure 入口網站](portal.azure.com), ，按一下 [ **儲存體**, ，然後按一下以開啟儀表板的儲存體帳戶名稱。

2. 若要變更圖表上繪製的度量，請採取下列其中一個動作：

    - 若要將度量加入圖表中，請在圖表下面資料表中的度量標頭旁邊按一下彩色的核取方塊。

    - 若要隱藏圖表上繪製的度量，請清除度量標頭旁邊彩色的核取方塊。

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. 根據預設，圖表會顯示趨勢，顯示每個度量的目前值 ( **相對** 圖表頂端的選項)。 若要顯示 Y 軸，以看到絕對值，請選取 **絕對**。

4. 若要變更度量圖表顯示的時間範圍，請選取圖表頂端的 [6 hours]、[24 hours] 或 [7 days]。


## 如何：自訂 [監視] 頁面

在 **監視** ] 頁面上，您可以檢視儲存體帳戶的完整度量集合。

- 如果儲存體帳戶已設定最小監視，則會從 Blob、資料表和佇列服務彙總例如入口流量/出口流量、可用性、延遲和成功百分比等度量。

- 如果您的儲存體帳戶已設定詳細資訊監視，則除了服務等級彙總，度量也可以用來更精細地解析個別儲存體作業。

使用下列程序來選擇要檢視的度量圖表及資料表儲存體度量會顯示在 **監視** 頁面。 這些設定不會影響儲存體帳戶中的監視資料收集、彙總和儲存。

## 做法：將計量新增至計量表


1. 在 [Azure 入口網站](portal.azure.com), ，按一下 [ **儲存體**, ，然後按一下以開啟儀表板的儲存體帳戶名稱。

2. 按一下 [ **監視**。

     **監視** 頁面隨即開啟。 依預設，度量表會顯示一部份可用於監視的度量。 下圖顯示所有三個服務都設定詳細資訊監視的儲存體帳戶預設 [監視] 顯示。 使用 **加入度量** 選取您想要監視從所有可用度量的度量。

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] 當您選取度量時，請考慮成本。 重新整理監視顯示時有相關的交易和出口流量成本。 如需詳細資訊，請參閱 [儲存體分析及計費](http://msdn.microsoft.com/library/azure/hh360997.aspx)。

3. 按一下 [ **新增度量**。

    最小監視中可用的彙總度量在清單的頂端。 如果選取核取方塊，度量便會顯示在度量清單中。

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. 移動游標停留在對話方塊的右邊，以顯示捲軸，您可以拖曳捲軸來將其他度量捲入檢視中。

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. 按一下度量旁邊的向下箭頭，展開可以包含該度量的操作清單。 選取您想要檢視的度量資料表中每個操作 [Azure 入口網站](portal.azure.com)。

    在下圖中，已展開 AUTHORIZATION ERROR PERCENTAGE 度量。

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. 選取所有服務的度量後，按一下 [確定] (核取記號) 更新監視組態。 選取的度量隨即新增到度量表。

7. 若要從資料表刪除度量，請按一下度量加以選取，然後按一下 [ **Delete Metric**。

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## 如何：自訂 [監視] 頁面上的度量圖表

1. 在 **監視** 儲存體帳戶，在度量表中的頁面上，選取最多 6 個度量在度量圖表上繪製。 若要選取度量，請按一下左邊的核取方塊。 若要從圖表移除度量，請清除核取方塊。

2. 若要切換圖表的相對值 (只顯示最終值) 和絕對值 (顯示 Y 軸)，請選取 **相對** 或 **絕對** 頂端的圖表。

3.  若要變更時間範圍內的度量圖表顯示中，選取 **6 小時**, ，**24 小時**, ，或 **7 天** 頂端的圖表。



## 如何：設定記錄

針對儲存體帳戶能使用的每個儲存服務 (Blob、資料表和佇列)，您可以儲存「讀取要求」、「寫入要求」及/或「刪除要求」的診斷記錄檔，也可以設定每個服務的資料保留原則。

1. 在 [Azure 入口網站](portal.azure.com), ，按一下 [ **儲存體**, ，然後按一下以開啟儀表板的儲存體帳戶名稱。

2. 按一下 [ **設定**, ，並使用鍵盤上的向下箭頭，向下捲動到 **記錄**。

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. 針對每個服務 (Blob、資料表和佇列)，設定下列各項：

    - 要記錄的要求類型：「讀取要求」、「寫入要求」及「刪除要求」。

    - 記錄資料的保留天數。 如果不想設定保留原則，請輸入零。 如果沒有設定保留原則，您可以決定是否刪除記錄檔。

4. 按一下 [ **儲存**。

診斷記錄檔儲存在儲存體帳戶中的 $logs Blob 容器。 如需存取 $logs 容器的詳細資訊，請參閱 [關於儲存體分析記錄](http://msdn.microsoft.com/library/azure/hh343262.aspx)。

