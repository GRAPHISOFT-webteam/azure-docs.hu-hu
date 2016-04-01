<properties 
    pageTitle="如何監視雲端服務 | Microsoft Azure" 
    description="了解如何使用 Azure 傳統入口網站來監視雲端服務。" 
    services="cloud-services" 
    documentationCenter="" 
    authors="rboucher" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="robb"/>


#如何監視雲端服務

[AZURE.INCLUDE [disclaimer](../../includes/disclaimer.md)]

您可以監視您的雲端服務在 Azure 傳統入口網站中的效能度量。 您可以對於各個服務角色設定從最小到詳細的監視層級，並且可以自訂監視顯示。 詳細資訊監視資料儲存在儲存體帳戶中，您可以從入口網站外部存取。 

Azure 傳統入口網站的監視顯示本身的可設定度相當高。 您可以選擇您想要監視上的度量清單中的度量 **監視** ] 頁面上，且您可以選擇要繪製的度量的度量圖表上 **監視** 頁面和儀表板。 

##概念##

依預設，對於使用從角色執行個體 (虛擬機器) 的主機作業系統收集的效能計數器進行的新雲端服務，將提供最小監視。 最小度量僅限於 [CPU 百分比]、[資料輸入]、[資料輸出]、[磁碟讀取輸送量] 和 [磁碟寫入輸送量]。 設定詳細資訊監視之後，即可收到以虛擬機器 (角色執行個體) 內的效能資料為基礎的其他度量。 詳細度量能夠進一步分析應用程式作業期間發生的問題。

依預設，角色執行個體的效能計數器資料會每隔 3 分鐘取樣一次，並且從執行個體傳輸。 您啟用詳細資訊監視時，將每隔 5 分鐘、1 小時和 12 小時對於各個角色執行個體和各個角色的角色執行個體彙總原始效能計數器資料。 經過 10 天後將清除彙總的資料。

您啟用詳細資訊監視後，彙總的監視資料將儲存於您儲存體帳戶中的資料表。 若要啟用角色的詳細資訊監視，您必須設定連結到儲存體帳戶的診斷連線字串。 您可以對於不同的角色使用不同的儲存體帳戶。

請注意，啟用詳細資訊監視將增加資料儲存、資料傳輸和儲存交易相關的儲存成本。 最小監視不需要儲存體帳戶。 即使將監視層級設定為詳細資訊，在最小監視層級顯現的度量資料也不會儲存在您的儲存體帳戶中。


##做法：為雲端服務設定監視功能##

使用下列程序，在 Azure 傳統入口網站中設定詳細資訊或最小監視。 

###開始之前###

- 建立儲存體帳戶儲存監視資料。 您可以對於不同的角色使用不同的儲存體帳戶。 如需詳細資訊，請參閱說明 **儲存體帳戶**, ，或參閱 [如何建立儲存體帳戶](/manage/services/storage/how-to-create-a-storage-account/)。

- 對於雲端服務角色啟用 Azure 診斷。 請參閱 [設定雲端服務的診斷](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore)。

確認「角色」組態中出現診斷連接字串。 您必須先啟用 [Azure 診斷]，並在「角色」組態中加入診斷連接字串，才能開啟詳細資訊監視功能。   

> [AZURE.NOTE] Azure SDK 2.5 專案未自動診斷連接字串中包含的專案範本。 針對這些專案，您必須手動將診斷連接字串加入「角色」組態。

**若要手動將診斷連接字串加入至「角色」組態**

1. 在 Visual Studio 中開啟雲端服務專案
2. 連按兩下 [ **角色** 可開啟設計工具，並選取的角色 **設定** ] 索引標籤
3. 尋找名為的設定 **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**。 
4. 如果此設定不存在，請按一下 **加入設定** ] 按鈕以新增至組態，並變更為新的設定類型 **ConnectionString**
5. 設定連接字串的值上的 [ **...** ] 按鈕。 此動作會開啟對話方塊，讓您選取儲存體帳戶。

    ![Visual Studio 設定](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

###將監視層級變更為詳細資訊或最小###

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，開啟 **設定** 雲端服務部署] 頁面。

2. 在 **層級**, ，按一下 [ **Verbose** 或 **最少**。 

3. 按一下 [ **儲存**。

啟動詳細資訊監視之後，您應該在一小時內開始查看 Azure 傳統入口網站中的監視資料。

原始效能計數器資料和彙總的監視資料是儲存在角色的部署 ID 所保留的表格中出現的儲存體帳戶內。 

##做法：接收雲端服務計量的警示##

您可以按照雲端服務監視度量接收警示。 在 **管理服務** 頁面 Azure 傳統入口網站，您可以建立您所選擇的度量達到指定的值時觸發警示的規則。 您也可以選擇在警示觸發時傳送電子郵件。 如需詳細資訊，請參閱 [How to ︰ 接收警示通知及管理在 Azure 中的警示規則](http://go.microsoft.com/fwlink/?LinkId=309356)。

##做法：將計量新增至計量表##

1. 在 [Azure 傳統入口網站](http://manage.windowsazure.com/), ，開啟 **監視** 的雲端服務] 頁面。

    依預設，度量表會顯示一部份的可用度量。 該圖顯示雲端服務的預設詳細資訊度量，這僅限於在角色層集彙總資料的記憶體\可用 MB 效能計數器。 使用 **加入度量** 選取其他彙總和角色層級度量來監視在 Azure 傳統入口網站。

    ![詳細資訊顯示](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. 若要將度量新增至度量表：

    a. 按一下 [ **加入度量** 開啟 **選擇度量**, ，如下所示。
    第一個可用的度量將展開，顯示可用的選項。 對於各個度量，頂端選項將顯示所有角色的彙總監視資料。 此外，您可以選擇要顯示資料的個別角色。

    ![Add metrics](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)


    b. To select metrics to display:

    - Click the down arrow by the metric to expand the monitoring options.
    - Select the check box for each monitoring option you want to display.

    You can display up to 50 metrics in the metrics table.

    > [AZURE.TIP] In verbose monitoring, the metrics list can contain dozens of metrics. To display a scrollbar, hover over the right side of the dialog box. To filter the list, click the search icon, and enter text in the search box, as shown below.
 
    ![Add metrics search](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)

3. 完成選取度量後，按一下 [確定] (核取記號)。

    選取的度量隨即新增到度量表，如下所示。

    ![監視度量](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. 若要從度量表刪除度量，請按一下度量加以選取，然後按一下 [ **Delete Metric**。 (您只看到 **刪除度量** 已選取的度量。)

###若要將自訂量度新增至量度資料表###
 **Verbose** 監視層級提供一份預設的度量，您可以監視入口網站上。 除此之外，您可以監視任何自訂計量，或透過入口網站監視應用程式定義的效能計數器。

下列步驟假設您已開啟 **Verbose** 監視層級，且已設定您的應用程式收集和傳輸自訂效能計數器。 

若要顯示在入口網站中顯示自訂效能計數器，您必須更新 WAD 控制容器的組態：
 
1.  在您的診斷儲存體帳戶中開啟 WAD 控制容器 blob。 您可以透過 Visual Studio 或任何其他的儲存體總管執行此動作。

    ![Visual Studio 伺服器總管](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. 瀏覽使用模式的 blob 路徑 **DeploymentId/RoleName/RoleInstance** 尋找角色執行個體的組態。 

    ![Visual Studio 儲存體總管](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. 下載角色執行個體的組態檔，並更新該檔案以包含所有自訂效能計數器。 例如若要監視 *Disk Write Bytes/sec* 的 *C 磁碟機* 底下加入下列 **PerformanceCounters\Subscriptions** 節點

    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. 儲存變更並將組態檔上傳回相同的位置，覆寫 blob 中的現有檔案。
5. 在 Azure 傳統入口網站組態中切換至「詳細資訊」模式。 如果您已經處於「詳細資訊」模式，請切換至最小再切換回詳細資訊模式。
6. 自訂效能計數器便會出現在 **加入度量** 對話方塊。 

##做法：自訂計量圖##

1. 在度量表中，最多可選取 6 個度量在度量圖表上繪製。 若要選取度量，請按一下左邊的核取方塊。 若要移除度量圖表中的度量，請清除度量表中的核取方塊。

    當您選取度量在度量表中，度量將新增到度量表。 在縮小顯示上， **詳細 n** 下拉式清單包含無法完全顯示的度量標頭。

 
2. 若要切換顯示相對值 (各個度量的最終值) 和絕對值 (顯示的 Y 軸)，請選取圖表頂端的 [相對] 或 [絕對]。

    ![相對或絕對](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. 若要變更度量圖表顯示的時間範圍，請選取圖表頂端的 [1 hour]、[24 小時] 或 [7 days]。

    ![監視顯示期間](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

    在儀表板度量圖表上，繪製圖表的方法並不相同。 其中有一組標準的度量可供使用，而且選取度量標頭即可新增或移除度量。

###自訂儀表板上的度量圖表###

1. 開啟雲端服務的儀表板。

2. 在圖表中新增或移除度量：

    - 若要繪製新的度量，請在圖表標頭中選取度量的核取方塊。 在縮小顯示上，按一下 [向下的箭號，由 ***n*？度量** 來繪製的度量，無法顯示在圖表標頭區域。

    - 若要刪除圖表上繪製的度量，請清除標頭旁邊的核取方塊。

3. 切換 **相對** 和 **絕對** 會顯示。

4. 選擇要顯示的 1 小時、24 小時或 7 天資料。

##做法：從 Azure 傳統入口網站外部存取詳細資訊監視資料##

詳細資訊監視資料儲存在您對於各個角色指定的儲存體帳戶內的表格中。 對於各個雲端服務部署，將為角色建立 6 個表格。 個別 (5 分鐘、1 小時和 12 小時) 建立 2 個表格。 其中一個表格儲存角色層級彙總，另一個表格儲存角色執行個體的彙總。 

表格名稱的格式如下：

    WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

其中：

- *deploymentID* 是指派給雲端服務部署的 GUID

- *aggregation_interval* = 5m、 1h 或 12h

- 角色層級彙總 = R

- 角色執行個體彙總 = RI

例如，下表將儲存 1 小時間隔彙總的詳細資訊監視資料：

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
 


