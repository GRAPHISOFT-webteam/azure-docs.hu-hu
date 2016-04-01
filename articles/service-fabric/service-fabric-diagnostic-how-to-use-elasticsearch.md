<properties
   pageTitle="使用 ElasticSearch 做為 Service Fabric 應用程式追蹤存放區 | Microsoft Azure"
   description="描述 Service Fabric 應用程式如何使用 ElasticSearch 和 Kibana 來儲存、檢索和搜尋應用程式追蹤 (記錄檔)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# 使用 ElasticSearch 做為 Service Fabric 應用程式追蹤存放區
## 簡介
本文將說明如何 [Service Fabric](http://azure.microsoft.com/documentation/services/service-fabric/) 應用程式可以使用 **ElasticSearch** 和 **Kibana** 應用程式追蹤儲存體、 索引和搜尋。 [ElasticSearch](https://www.elastic.co/guide/index.html) 是開放原始碼、 分散式和可擴充即時搜尋和分析引擎，非常適合這項工作，而且可以安裝在 Microsoft Azure 中執行的 Windows 或 Linux 的虛擬機器上。 ElasticSearch 可以非常有效率的方式處理 *結構化* 產生使用技術，例如追蹤 **事件追蹤的 Windows (ETW)**。

Service Fabric 執行階段使用 ETW 來提供診斷資訊 (追蹤)，建議 Service Fabric 應用程式也使用此方法來提供診斷資訊。 這可讓執行階段提供和應用程式提供的追蹤之間相互關聯，使疑難排解更輕鬆。 在 Visual Studio 中的 Service Fabric 專案範本包含記錄 API (根據.NET **EventSource** 類別)，預設會發出 ETW 追蹤。 如需 Service Fabric 應用程式追蹤使用 ETW 的一般概觀，請參閱 [這篇文章](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

需要在 Service Fabric 叢集節點上即時 (當應用程式正在執行時) 擷取追蹤並傳送至 ElasticSearch 端點，ElasticSearch 中才會顯示追蹤。 追蹤擷取有兩個主要選項：

+ **在程序追蹤擷取**  
應用程式或服務處理序更精確地說，負責傳送診斷資料追蹤存放區 (ElasticSearch)。

+ **跨處理序追蹤擷取**  
獨立的代理程式會擷取來自服務的處理程序的追蹤，並傳送至追蹤存放區。

在本文的其餘部分，我們將說明如何在 Azure 上設定 ElasticSearch、討論這兩種擷取選項的優缺點，並說明如何設定 Fabric 服務將資料傳送至 ElasticSearch。


## 在 Azure 上設定 ElasticSearch
若要設定 ElasticSearch 服務在 Azure 上最直接的方法是透過 [**Azure ARM 範本**](../resource-group-overview.md)。 完整 [ElasticSearch 的快速入門 ARM 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) 也可以從 Azure 快速入門範本儲存機制。 此範本對縮放單位 (節點的群組) 使用個別的儲存體帳戶，而且可以佈建個別用戶端和伺服器節點，各有不同組態和連接不同的資料磁碟數。

在這篇文章中，我們會使用呼叫另一個範本 **ES MultiNode** 從 [Microsoft 模式和作法 ELK 分支](https://github.com/mspnp/semantic-logging/tree/elk/)。 此範本比較容易使用，依預設會建立由 HTTP 基本驗證所保護的 ElasticSearch 叢集。 繼續之前請下載 [Microsoft p&p"elk 「 儲存機制](https://github.com/mspnp/semantic-logging/tree/elk/) 從 GitHub 到您的電腦 （無論是藉由複製儲存機制或下載的 ZIP 檔案）。 ES-MultiNode 範本位於具有相同名稱的資料夾中。
>[AZURE.NOTE] ES MultiNode 範本和相關聯的指令碼目前支援 ElasticSearch 1.7 版本。 日後將加入 ElasticSearch 2.0 的支援。

### 準備電腦以執行 ElasticSearch 安裝指令碼
若要使用 ES-MultiNode 範本，最簡單的方式是透過提供的 PowerShell 指令碼，稱為 `CreateElasticSearchCluster`。 若要使用這個指令碼，您需要安裝 Azure PowerShell 模組和名為 openssl 的工具。 需要後者，才能建立可用來從遠端管理 ElasticSearch 叢集的 SSH 金鑰。

注意：`CreateElasticSearchCluster` 指令碼主要是為了從 Windows 電腦輕鬆使用 ES-MultiNode 範本。 可以在非 Windows 電腦上使用此範本，但這已超出本文的範圍。

1. 如果您還沒有安裝的話，安裝 [**Azure PowerSell 模組**](http://go.microsoft.com/fwlink/p/?linkid=320376)。 出現提示時，請按一下 [執行]，再按一下 [安裝]。
>[AZURE.NOTE] Azure PowerShell 正在進行大量變更 Azure PowerShell 1.0 版。 CreateElasticSearchCluster 目前設計為用於 Azure PowerShell 0.9.8，不支援 Azure PowerShell 1.0 Preview。 日後將提供 Azure PowerShell 1.0 相容指令碼。

2.  **Openssl** 工具隨附的分佈於 [**Git for Windows**](http://www.git-scm.com/downloads)。 如果您有不這麼做，請將安裝 [Git for Windows](http://www.git-scm.com/downloads) 現在 （預設安裝選項是 [確定]）。

3. 假設 Git 已安裝，但未包含在系統路徑中，請開啟 Microsoft Azure PowerShell 視窗並執行下列命令：

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    取代 `<Git installation folder>` 與 Git 位置，在您的電腦; 預設值是 *"C:\Program Files\Git"*。 請注意第一個路徑開頭的分號字元。

4. 確認您已登入 Azure (透過 [*Add-azureaccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx) 指令程式)，而且您已選取應該用來建立 ElasticSearch 叢集的訂用帳戶 ([*Select-azuresubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx))。

5. 如果您尚未將目前目錄切換至 ES-MultiNode 資料夾，請切換。

### 執行 CreateElasticSearchCluster 指令碼
執行指令碼之前，開啟 `azuredeploy-parameters.json` 檔案，確認或提供指令碼參數的值。 提供下列參數：

|參數名稱           |說明|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |此名稱用來為 ElasticSearch 叢集建立公開可見的 DNS 名稱 (將 Azure 區域網域附加至提供的名稱)。 例如，如果此參數值為 "myBigCluster"，而選擇的 Azure 區域是美國西部，則為叢集產生的 DNS 名稱會是 myBigCluster.westus.cloudapp.azure.com。 <br /><br />這個名稱也會成為根 ElasticSearch 叢集，例如資料節點名稱與相關聯的許多成品的名稱。|
|storageAccountPrefix    |將為 ElasticSearch 叢集建立的儲存體帳戶的首碼。 <br /><br /> 範本的目前版本會使用一個共用的儲存體帳戶，但可能在未來變更。|
|adminUsername           |用於管理 ElasticSearch 叢集的系統管理員帳戶的名稱 (對應的 SSH 金鑰會自動產生)|
|dataNodeCount           |ElasticSearch 叢集中的節點數目。 目前版本的指令碼不會區分資料與查詢節點。所有節點會同時扮演這兩種角色。|
|dataDiskSize            |將配置給每個資料節點的資料磁碟大小 (以 GB 為單位)。 每個節點將會收到 4 個資料磁碟，專供 ElasticSearch 服務使用。|
|region                  |應該放置 ElasticSearch 叢集的 Azure 區域的名稱。|
|esClusterName           |ElasticSearch 叢集的內部名稱。 <br /><br />需要這個值會變更預設值，除非您打算在相同的虛擬網路，ES MultiNode 範本目前不支援執行一個以上的 ElasticSearch 叢集。|
|esUserName esPassword  |將設定為可存取 ES 叢集的使用者的認證 (受限於 HTTP 基本驗證)。|

您現在可以開始執行指令碼。 發出下列命令 ︰
```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
```
其中 `<es-group-name>` 會包含所有的叢集資源的 Azure 資源群組的名稱。

>[AZURE.NOTE] 如果您從測試 azure Create cmdlet 取得 NullReferenceException，忘記登入 Azure (`Add-AzureAccount`)。

如果執行指令碼發生錯誤，而且您判斷錯誤起因於錯誤的範本參數值，請更正參數檔案，然後以不同資源群組名稱再次執行指令碼。 您也可以將 `-RemoveExistingResourceGroup` 參數加入至指令碼引動過程，以重複使用相同的資源群組名稱，並讓指令碼清除舊的資源群組。

### 執行 CreateElasticSearchCluster 指令碼的結果
執行 `CreateElasticSearchCluster` 指令碼之後會建立下列主要構件。 為了清楚說明，我們假設您已使用 'myBigCluster' 作為 `dnsNameForLoadBalancerIP` 參數的值，而且您建立叢集的區域為美國西部。

|構件|名稱、位置及備註|
|----------------------------------|----------------------------------|
|用於遠端系統管理的 SSH 金鑰 |myBigCluster.key 檔 (在執行 CreateElasticSearchCluster 的目錄中)。 <br /><br />這是在叢集中的資料節點可用來連接到 [系統管理] 節點和 （透過 [系統管理] 節點中） 的索引鍵。|
|管理節點                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />這是遠端 ElasticSearch 叢集管理，唯一的可讓外部 SSH 連線的專用的 VM。 它與所有 ElasticSearch 叢集節點一樣在相同的虛擬網路上執行，但不會執行 ElasticSearch 服務。|
|資料節點                        |myBigCluster1... myBigCluster*N* <br /><br />執行 ElasticSearch 和 Kibana 服務的資料節點。 您可以透過 SSH 連接至每個節點，但只能透過管理節點。|
|ElasticSearch 叢集             |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />以上是 ElasticSearch 叢集 （請注意 /es 尾碼） 的主要端點。 它是由基本 HTTP 驗證保護 (由 ES-MultiNode 範本的 esUserName/esPassword 參數指定認證)。 叢集也有基本的叢集系統管理安裝前端的外掛程式 (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head)。|
|Kibana 服務                    |http://myBigCluster.westus.cloudapp.azure.com <br /><br />若要顯示資料，建立的 ElasticSearch 叢集; 設定 Kibana 服務它會受到相同驗證認證，叢集本身。|

## 同處理序與跨處理序追蹤擷取
在簡介中，我們曾提過收集診斷資料的兩種基本方法：同處理序和跨處理序。 各有優缺點。

優點 **同處理序追蹤擷取** 包括 ︰

1. *輕鬆設定及部署*

    * 診斷資料收集組態只是應用程式組態的一部分，保持它與應用程式的其餘部分永遠「同步」很簡單。

    * 輕鬆就可達成個別應用程式或個別服務的組態。

    * 跨處理序追蹤擷取通常需要另外部署和設定診斷代理程式，這是額外的系統管理工作，也是錯誤的來源。 通常，特定代理程式技術只允許每個虛擬機器 (節點) 有一個代理程式執行個體，這表示該節點上執行的所有應用程式和服務之間會共用診斷收集組態。

2. *彈性*

    * 只要用戶端程式庫支援目標資料儲存系統，應用程式可以將資料傳送至任何需要的地方。 可以依需要加入新的來源。

    * 可以實作複雜的擷取、篩選和資料彙總規則。

    * 跨處理序追蹤擷取通常受限於代理程式支援的資料來源。 有些代理程式是可擴充的。

3. *存取內部應用程式資料與內容*

    * 應用程式/服務處理序內執行的診斷子系統可以輕鬆地隨著內容資訊而擴大追蹤。

    * 在跨處理序方法中，必須透過 Windows 事件追蹤 (ETW) 之類的處理序間通訊機制，將資料傳送至代理程式。 這可能會造成額外的限制。

優點 **跨處理序追蹤擷取** 包括 ︰

1. *能夠監視應用程式並收集損毀傾印*

    * 如果應用程式啟動失敗或損毀，同處理序追蹤擷取可能不會成功。 獨立代理程式有太多更好的機會，擷取重要的疑難排解資訊。<br /><br />

2. *成熟度、穩健性和已證實的效能*

    * 平台廠商所開發的代理程式 (例如 Microsoft Azure 診斷代理程式) 都經過嚴格的測試和實戰鍛鍊。

    * 執行同處理序追蹤擷取時，必須確保從應用程式程序傳送診斷資料的活動不會干擾應用程式的主要工作，也不會引起計時或效能問題。 獨立執行的代理程式比較不容易發生這些問題，通常也特別設計為限制它對系統的影響。

當然可以結合並充分利用這兩種方法。事實上這可能是許多應用程式的最佳解決方案。

在本文中我們將使用 **Microsoft.Diagnostic.Listeners 程式庫** 和同處理序追蹤擷取到從 ElasticSearch 叢集讓 Service Fabric 應用程式傳送資料。

## 使用 Listeners 程式庫將診斷資料傳送至 ElasticSearch
Microsoft.Diagnostic.Listeners 程式庫是合作對象叢集範例 Fabric 應用程式的一部分。 使用方式：

1. 下載 [合作對象叢集範例](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) 從 GitHub。

2. 從合作對象叢集範例目錄中，將 Microsoft.Diagnostics.Listeners 和 Microsoft.Diagnostics.Listeners.Fabric 專案 (整個資料夾)，複製到應該會將資料傳送到 ElasticSearch 的應用程式的方案資料夾。

3. 開啟目標方案，在 [方案總管] 中的方案節點上按一下滑鼠右鍵，然後選擇 [加入現有專案]。 將 Microsoft.Diagnostics.Listeners 專案加入至方案。 針對 Microsoft.Diagnostics.Listeners.Fabric 專案重複相同的步驟。

4. 新增從服務專案至兩個已加入之專案的專案參考 (每個應該會將資料傳送至 ElasticSearch 的服務，都應該參考 Microsoft.Diagnostics.EventListeners 和 Microsoft.Diagnostics.EventListeners.Fabric)。

    ![Microsoft.Diagnostics.EventListeners 和 Microsoft.Diagnostics.EventListeners.Fabric 程式庫的專案參考][1]

### Service Fabric 2015 年 11 月預覽版和 Microsoft.Diagnostics.Tracing NuGet 封裝
2015 年 11 月的預覽服務網狀架構目標的應用程式 **.NET Framework 4.5.1** 因為這是 Azure 時的預覽版本支援的.NET Framework 的最新版本。 可惜，這個版本的架構缺少 Microsoft.Diagnostics.Listeners 程式庫所需的某些 EventListener API。 緊密結合 EventSource （在 [網狀架構應用程式中記錄 Api 的基礎元件） 和 EventListener，因為每個使用 Microsoft.Diagnostics.Listeners 程式庫的專案必須使用 EventSource，其中一個所提供的替代實作 **Microsoft.Diagnostics.Tracing nuget**, 、 由 Microsoft 所撰寫。 此封裝完全與架構中包含的舊版 EventSource 相容，除了變更參考的命名空間，應該不需要變更任何程式碼。

若要開始使用 Microsoft.Diagnostics.Tracing 的 EventSource 類別實作，請針對需要將資料傳送至 ElasticSearch 的每個服務專案，執行下列步驟：

1. 以滑鼠右鍵按一下服務專案，然後選擇 [管理 NuGet 封裝]。

2. 切換到 nuget.org 封裝來源 (如果尚未選取)，搜尋 "Microsoft.Diagnostics.Tracing"

3. 安裝 `Microsoft.Diagnostics.Tracing.EventSource` 封裝 (及其相依項目)

4. 開啟服務專案中的 ServiceEventSource.cs 或 ActorEventSource.cs 檔案，並將檔案頂端的 `using System.Diagnostics.Tracing` 指示詞取代為 `using Microsoft.Diagnostics.Tracing` 指示詞。

這些步驟不是必要的一次 **.NET Framework 4.6** 支援的 Microsoft Azure。

### ElasticSearch 接聽程式具現化和組態
將診斷資料傳送至 ElasticSearch 所需的最後一個步驟，就是建立 `ElasticSearchListener` 的執行個體，並以 ElasticSearch 連線資料來設定它。 接聽程式會自動擷取所有透過服務專案中定義的 EventSource 類別所引發的事件。 它必須在服務的存留期間運作，所以服務初始化程式碼中是建立它的最佳位置。 以下是無狀態服務的初始化程式碼在完成必要變更後的樣子 (以 `****` 開頭的註解指出新增的部分)：

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
                        Process.GetCurrentProcess().Id,
                        typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

ElasticSearch 連接資料應該放在服務組態檔 (PackageRoot\Config\Settings.xml) 中的個別區段。 區段的名稱必須對應至傳給 `FabricConfigurationProvider` 建構函式的值，例如：

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
值 `serviceUri`, ，`userName` 和 `password` 分別對應到 ElasticSearch 叢集端點位址、 ElasticSearch 使用者名稱和密碼。 `indexNamePrefix` 是 ElasticSearch 索引; 的前置詞Microsoft.Diagnostics.Listeners 程式庫建立新的索引，其資料的每日。

### 驗證
就這麼簡單！ 現在每當服務執行時，就會開始將追蹤傳送至組態中指定的 ElasticSearch 服務。 若要開啟 Kibana 目標 ElasticSearch 執行個體 （在本例中的頁面位址會是 http://myBigCluster.westus.cloudapp.azure.com/） 相關聯的 UI，並檢查驗證為選擇的名稱前置詞與該索引 `ElasticSearchListener` 執行個體會確實建立和填入資料。

![顯示 PartyCluster 應用程式事件的 Kibana][2]

## 後續步驟
- [深入了解診斷和監視 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png


