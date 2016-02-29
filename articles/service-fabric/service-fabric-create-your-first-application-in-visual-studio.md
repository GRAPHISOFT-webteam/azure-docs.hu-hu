<properties
   pageTitle="在 Visual Studio 中建立第一個 Service Fabric 應用程式 | Microsoft Azure"
   description="使用 Visual Studio 建立、部署和偵錯 Service Fabric 應用程式"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# 在 Visual Studio 中建立第一個 Service Fabric 應用程式

Service Fabric SDK 包含的 Visual Studio 增益集提供用來建立、部署和偵錯 Service Fabric 應用程式的範本和工具。 本主題逐步解說在 Visual Studio 中建立第一個應用程式的程序。

## 先決條件

開始之前，請確定您有 [設定開發環境](service-fabric-get-started.md)。

## 建立應用程式

Service Fabric 應用程式可以包含一或多個服務，而每個服務在提供應用程式的功能時都有特定角色。 [新增專案] 精靈可讓您隨著第一個服務專案一起建立應用程式專案。 您稍後可以新增更多服務。

1. 以系統管理員身分啟動 Visual Studio。

2. 按一下 [ **檔案 > 新增專案 > 定域機組 > 服務網狀架構應用程式**。

3. 替應用程式命名，然後按一下 [確定]。

    ![Visual Studio 中的新增專案對話方塊][1]

4. 在下一個對話方塊中，系統會要求您選擇要包含在您的應用程式中的第一個服務類型。 基於本教學課程的用途，我們將選擇 [具狀態服務]。 予以命名，然後按一下 [確定]。

    ![Visual Studio 中的新增服務對話方塊][2]

    >[AZURE.NOTE] 如需選項的詳細資訊，請參閱 [選擇架構](service-fabric-choose-framework.md)。

    Visual Studio 會建立應用程式專案和具狀態服務專案，並在 [方案總管] 中加以顯示。

    ![使用具狀態服務建立應用程式後的方案總管][3]

    應用程式專案未直接包含任何程式碼，它反而會參考一組服務專案。 此外，它包含三種其他類型的內容：

    - **發行設定檔**: 用來管理不同的環境的工具喜好設定。

    - **指令碼**: 部署/升級您的應用程式的 PowerShell 指令碼。 此指令碼是由 Visual Studio 在幕後使用，可以直接在命令列叫用。

    - **應用程式定義**: 應用程式資訊清單和相關聯的應用程式參數檔案定義應用程式，並可讓您設定專為特定的環境。

    如需內容的服務專案的概觀，請參閱 [開始使用可靠的服務](service-fabric-reliable-services-quick-start.md)。

## 部署和偵錯應用程式

您現在有一個應用程式，您可以試著執行它。

1. 在 Visual Studio 中按 F5，部署應用程式以供偵錯。

    >[AZURE.NOTE] 這將會需要一些時間與 Visual Studio 的第一次建立本機開發叢集。 本機叢集會只會在一部電腦上執行您將在多部電腦的叢集中建置的相同平台程式碼。 您會在 Visual Studio 輸出視窗中看見叢集建立狀態。

    備妥叢集時，您將會收到來自 SDK 隨附的本機叢集系統匣管理員應用程式的通知。

    ![本機叢集系統匣通知][4]

2. 啟動應用程式後，Visual Studio 會自動顯示 [診斷事件檢視器]，以便查看服務的追蹤輸出。

    ![診斷事件檢視器][5]

    在具狀態服務範本的情況下，訊息只會顯示在 MyStatefulService.cs 的 `RunAsync` 方法中遞增的計數器值。

3. 展開其中一個事件以查看更多詳細資料，包括程式碼執行所在的節點 - 在此情況下是節點 2，雖然這在您的電腦上可能有所不同。

    ![診斷事件檢視器詳細資訊][6]

    本機叢集是由單一電腦上裝載的五個節點所組成，以模擬五個節點的叢集，而各節點位於不同的電腦上。 讓我們取下本機叢集上的其中一個節點來模擬遺失機器，以及在相同的時間運用 Visual Studio 偵錯工具。

    >[AZURE.NOTE] 專案範本所發出的應用程式診斷事件使用包含 `ServiceEventSource` 類別。 如需詳細資訊，請參閱 [如何監視和診斷本機服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

4. 尋找在服務專案 (例如從 StatefulService 衍生的類別。 MyStatefulService) 上的第一行設定中斷點和 `RunAsync` 方法。

    ![具狀態服務 RunAsync 方法的中斷點][7]

5. 以滑鼠右鍵按一下 [本機叢集管理員] 系統匣應用程式並選擇 [管理本機叢集]，以啟動 Service Fabric 總管。

    ![從本機叢集管理員啟動 Service Fabric 總管][systray-launch-sfx]

    Service Fabric 總管提供了叢集的視覺表示法，包括部署至叢集合的應用程式集以及構成叢集的實體節點集合。 若要深入了解 Service Fabric 總管，請參閱 [視覺化叢集](service-fabric-visualizing-your-cluster)。

6. 在左窗格中，展開 **叢集 > 節點** 並尋找您的程式碼執行所在的節點。

7. 按一下 [ **動作 > 停用 (重新啟動)** 模擬機器重新啟動。

    ![在 Service Fabric 總管中停止節點][sfx-stop-node]

    您應該會在 Visual Studio 中短暫看見達到您的中斷點，因為您一個節點上所做的計算完美地容錯移轉至另一個節點。

8. 返回 [診斷事件檢視器] 並觀察訊息。 請注意，計數器已繼續遞增，即使事件實際上來自不同的節點。

    ![容錯移轉之後的診斷事件檢視器][diagnostic-events-viewer-detail-post-failover]

### 清除

  在我們做結論之前，請務必記得本機叢集非常真實。 即使停止偵錯工具並關閉 Visual Studio，您的應用程式將會繼續在背景執行。 視您的應用程式的本質而言，此背景活動可能會佔用您電腦上的大量資源。 您有數個選項可管理此項目：

  1. 若要移除個別的應用程式和所有資料，請使用 **都移除應用程式** Service Fabric 總管中的動作。

  2. 若要關閉叢集，但保留的應用程式資料及追蹤，請按一下 **停止叢集** 在系統匣應用程式。

  3. 若要完全刪除叢集，請按一下 [ **移除叢集** 在系統匣應用程式。 請注意，此選項會導致下次您在 Visual Studio 中按 F5 鍵時發生其他緩慢部署，只能使用於您有時候不打算使用本機叢集時您迫切需要回收資源時。



## 後續步驟

- [請參閱如何透過 WebAPI 在網際網路服務公開](service-fabric-add-a-web-frontend.md)
- [了解如何在 Azure 中建立叢集](service-fabric-cluster-creation-via-portal.md)
- [深入了解如何建立 Reliable Services](service-fabric-reliable-services-quick-start.md)
- [嘗試使用 Reliable Actor 程式設計模型建立服務](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

