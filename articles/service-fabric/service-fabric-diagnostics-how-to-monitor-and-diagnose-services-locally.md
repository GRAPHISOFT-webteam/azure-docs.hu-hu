<properties
   pageTitle="Microsoft Azure Service Fabric 如何在本機上監視和診斷服務"
   description="本文章將說明如何監視和診斷您在本機開發電腦上使用 Microsoft Azure Service Fabric 所撰寫的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/04/2015"
   ms.author="kunalds"/>


# 監視和診斷本機開發設定中的服務
監視、偵測、診斷和疑難排解可讓服務繼續順利執行，盡可能減少服務中斷的使用者經驗。 雖然實際上已部署的生產環境中相當重要，然而效率將取決於服務開發期間所採用的類似模型服務，以便在您移動至實際設定時能確保其正常運作。 Service Fabric 可讓服務開發人員輕鬆實作診斷，可以在單一電腦本機開發和實際生產叢集安裝上順暢地工作。

## 為何要使用 ETW 追蹤和記錄
[Windows 事件追蹤](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 是建議的技術，用於追蹤 Service Fabric 中的訊息。 這樣做的原因是：

* ETW 相當快速。 其是以一種追蹤技術建置而成，並對您程式碼執行時間的影響降到最低。
* ETW 會在本機開發環境以及實際叢集設定順暢地進行追蹤。 這表示您不需要重寫追蹤程式碼，當您準備好要將程式碼部署至實際叢集。
* Service Fabric 系統程式碼也會將 ETW 用於內部追蹤。 這可讓您檢視與 Service Fabric 系統追蹤交錯的應用程式追蹤，更容易了解在基礎系統中應用程式程式碼與事件之間的序列和相互關係。
* 內建支援的 Service Fabric Visual Studio 工具可供您檢視 ETW 事件。


## 在 Visual Studio 中檢視 Service Fabric 系統事件

Service Fabric 會發出 ETW 事件，以協助應用程式開發人員了解平台中發生的事情。 忙線您還沒有這麼做，請繼續進行並遵循中的步驟 [Visual Studio 中建立第一個應用程式](./service-fabric-create-your-first-application-in-visual-studio.md) 診斷事件檢視器顯示追蹤訊息以讓應用程式啟動並執行。 

1. 若 [診斷事件] 視窗不會自動顯示，請移至 Visual Studio 中的 [伺服器總管] 索引標籤，以滑鼠右鍵按一下 [Service Fabric 叢集]，並選擇內容功能表中的 [檢視診斷事件]。

  ![開啟 Visual Studio 診斷事件檢視器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

2. 每個事件皆有標準的中繼資料資訊，可告訴您事件所來自的節點、應用程式和服務。 您也可以使用視窗上方的 [篩選事件] 方塊來篩選事件清單，例如您可以篩選 [節點名稱] 或 [服務名稱]。 查看事件詳細資料時，您也可以暫停在視窗頂端使用按鈕，稍後再繼續，而不會遺失任何事件。

  ![Visual Studio 診斷事件檢視器](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## 將您自己自訂的追蹤新增至應用程式程式碼
Service Fabric Visual Studio 專案範本包含範例程式碼。 程式碼示範如何新增自訂的應用程式程式碼 ETW 追蹤，其會與來自 Service Fabric 的系統追蹤一併顯示在 Visual Studio ETW 檢視器中。 這個方法的優點是中繼資料會自動新增至追蹤，且 Visual Studio 診斷檢視器已設定為顯示追蹤。

從建立專案的 **服務範本** （無狀態或可設定狀態） 只要搜尋 `RunAsync` 實作 ︰

1. 使用 `RunAsync` 方法呼叫 `ServiceEventSource.Current.ServiceMessage` 時會顯示來自應用程式程式碼的自訂 ETW 追蹤範例。
2. 在 **ServiceEventSource.cs** 檔案中，您會發現的多載 `ServiceEventSource.ServiceMessage` 方法應用於高頻率事件，因為效能的考量。

從建立專案的 **動作項目範本** （無狀態或可設定狀態） ︰

1. 開啟 **"ProjectName".cs** 檔案，其中 *ProjectName* 是您選擇的 Visual Studio 專案的名稱。  
2. 尋找程式碼 `ActorEventSource.Current.ActorMessage(this, "Doing Work");` 中 *DoWorkAsync* 方法。  這是來自應用程式程式碼撰寫的自訂 ETW 追蹤範例。  
3. 在檔案中 **ActorEventSource.cs**, ，您會發現的多載 `ActorEventSource.ActorMessage` 方法應用於高頻率事件，因為效能的考量。

將自訂 ETW 追蹤新增至服務程式碼之後，您可以再次建置、部署，以及執行應用程式以查看診斷檢視器中的事件。 如果您要偵錯應用程式，按 f5，診斷檢視器會自動開啟。

## 後續步驟
您加入至上述針對本機診斷應用程式的相同追蹤程式碼會使用工具可讓您檢視這些事件，在 Azure 的叢集上執行應用程式時簽出這些文章會討論工具的不同選項，並且描述如何設定這些。
* [從 Service Fabric 叢集在 Azure 中使用 WAD （Windows Azure 診斷） 和 Operational Insights 收集記錄檔](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md)
* [使用 ElasticSearch 做為 Service Fabric 應用程式追蹤存放區](service-fabric-diagnostic-how-to-use-elasticsearch.md)


