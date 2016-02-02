<properties
   pageTitle ="Service Fabric 叢集安全性: 用戶端角色 |Microsoft Azure 「
   描述 = 「 本文說明兩個用戶端角色和角色提供的權限 」。 
   服務 = 「 服務架構 」
   documentationCenter=".net"
   作者 ="mani 范 」
   管理員 ="coreysa"
   editor=""/>

<tags
   ms.service= 「 服務架構 」
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA 」
   ms.date="11/13/2015 」
   ms.author="subramar"/ >



# 角色型存取控制 (適用於 Service Fabric 用戶端)

Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定叢集作業的存取權，讓叢集更加安全。 系統管理員可以完整存取管理功能 (包括讀取/寫入功能)，而使用者預設只具有對管理的讀取存取權 (例如查詢)，以及解析應用程式和服務的能力。

這兩個用戶端角色 (系統管理員或用戶端) 的指定，是在建立叢集時，透過為每個角色提供個別的憑證來指定。 請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 如需有關設定安全的 Service Fabric 叢集。


## 預設存取控制設定

系統管理員存取控制類型可以完整存取所有的 FabricClient API。 它可以對 Service Fabric 叢集執行任何讀取和寫入作業，包括下列作業：

### 應用程式和服務作業

* **CreateService**：建立服務
* **CreateServiceFromTemplate**：從範本建立服務
* **UpdateService**：更新服務
* **DeleteService**：刪除服務
* **ProvisionApplicationType**：佈建應用程式類型
* **CreateApplication**：建立應用程式
* **DeleteApplication**：刪除應用程式
* **UpgradeApplication**：啟動或中斷應用程式升級
* **UnprovisionApplicationType**：解除應用程式類型佈建
* **MoveNextUpgradeDomain**：以明確的「升級網域」繼續進行應用程式升級
* **ReportUpgradeHealth**：以目前的升級進度繼續進行應用程式升級
* **ReportHealth**：回報健康情況
* **PredeployPackageToNode**：預先佈署 API
* **CodePackageControl**：重新啟動程式碼封裝
* **RecoverPartition**：復原某個資料分割
* **RecoverPartitions**：復原多個資料分割
* **RecoverServicePartitions**：復原服務資料分割
* **RecoverSystemPartitions**：復原系統服務資料分割


### 叢集作業

* **ProvisionFabric**：佈建 MSI 和 (或)「叢集資訊清單」
* **UpgradeFabric**：啟動叢集升級
* **UnprovisionFabric**：解除 MSI 和 (或)「叢集資訊清單」佈建
* **MoveNextFabricUpgradeDomain**：以明確的「升級網域」繼續進行叢集升級
* **ReportFabricUpgradeHealth**：以目前的升級進度繼續進行叢集升級
* **StartInfrastructureTask**：啟動基礎結構工作
* **FinishInfrastructureTask**：完成基礎結構工作
* **InvokeInfrastructureCommand**：基礎結構工作管理命令
* **ActivateNode**：啟用節點
* **DeactivateNode**：停用某個節點
* **DeactivateNodesBatch**：停用多個節點
* **RemoveNodeDeactivations**：還原多個節點上的停用
* **GetNodeDeactivationStatus**：檢查停用狀態
* **NodeStateRemoved**：回報已移除的節點狀態
* **ReportFault**：回報錯誤
* **FileContent**：映像存放區用戶端檔案傳輸 (叢集外部)
* **FileDownload**：起始映像存放區用戶端檔案下載 (叢集外部)
* **InternalList**：映像存放區用戶端檔案清單作業 (內部)
* **Delete**：映像存放區用戶端刪除作業
* **Upload**：映像存放區用戶端上傳作業
* **NodeControl**：啟動、停止及重新啟動節點
* **MoveReplicaControl**：將複本從一個節點移至另一個節點

### 其他作業

* **Ping**：用戶端 Ping
* **Query**：所有允許的查詢
* **NameExists**：檢查命名 URI 是否存在



使用者存取控制類型預設限制為下列作業 (系統管理員存取控制也允許存取這些作業)：

* **EnumerateSubnames**：列舉命名 URI
* **EnumerateProperties**：列舉命名屬性
* **PropertyReadBatch**：命名屬性讀取作業
* * * GetServiceDescription: 長時間輪詢服務通知和讀取服務描述
* **ResolveService**：抱怨型服務解析
* **ResolveNameOwner**：解析命名 URI 擁有者
* **ResolvePartition**：解析系統服務
* **ServiceNotifications**：事件型服務通知
* **GetUpgradeStatus**：輪詢應用程式升級狀態
* **GetUpgradeStatus**：輪詢叢集升級狀態
* **InvokeInfrastructureQuery**：查詢基礎結構工作
* **List**：映像存放區用戶端檔案清單作業
* **ResetPartitionLoad**：重設 failoverUnit 的載入
* **ToggleVerboseServicePlacementHealthReporting：切換詳細服務位置健康情況報告

## 變更用戶端角色的預設設定

在叢集資訊清單檔案中，可以視需要提供系統管理員功能給用戶端。 您可以變更預設值，請前往 ** 網狀架構設定* 選項， [叢集建立](service-fabric-cluster-creation-via-portal.md) 提供名為依照上述方式中的設定和 **名稱** 欄位，和 **系統管理員，使用者** 值欄位中。

## 後續步驟

[Service Fabric 叢集安全性](service-fabric-cluster-security.md)

[建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)





