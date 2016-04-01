<properties 
   pageTitle="可測試性：服務通訊 | Microsoft Azure" 
   description="服務之間的通訊是整合 Service Fabric 應用程式的重要環節。 本文討論設計考量及測試技巧。" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="vturecek" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="08/25/2015"
   ms.author="vturecek"/>

# Service Fabric Testability 案例 ︰ 服務通訊

微服務及服務導向的架構樣式會在 Service Fabric 中自然出現。 在這些類型的分散式架構中，元件化的微服務應用程式通常是由需要彼此通訊的多個服務所組成。 即使在最簡單的情況下，您通常至少會有一個無狀態網路服務及一個可設定狀態的資料儲存服務需要相互通訊。

服務之間的通訊是整合應用程式的重要環節，因為各服務會向其他服務公開遠端 API。 與 I/O 相關的一組 API 界限通常需要謹慎處理，且需經過大量測試和驗證。 

若要在分散式系統中，將這些服務界限連接起來，有許多方面都需要謹慎考量：

 - **傳輸通訊協定**。 要使用 HTTP 以提供更優異的互通性，還是自訂的二進位通訊協定，以應付最大的輸送量？
 - **錯誤處理**。 如何處理永久性和暫時性錯誤？當服務移動至另一個節點時，會發生什麼事？
 - **逾時和延遲**。 在多層式應用程式中，各服務層如何透過堆疊處理延遲，為使用者順暢提供服務？

不論您使用 Service Fabric 提供的其中一種內建服務通訊元件，或者選擇自行建立，測試服務之間的互動永遠是確保應用程式復原能力的重要部分。

## 我的服務位於何處？

服務執行個體經過一段時間後可能會移動，尤其在設有負載度量，以自訂最佳資源平衡時更是如此。 升級、容錯移轉、向外延展及其他分散式系統存留時間內發生的各種情況，都是 Service Fabric 可以移動服務執行個體以最大化可用性的時機。

由於服務會在叢集中移動，您的用戶端和其他服務應在與服務通訊時準備就緒，以因應兩種案例：

 + 在您最後一次與服務執行個體或分割區複本通訊後，其中一項即已移動過。 正常情況下，這是服務生命週期的一部分，而且應該會在應用程式的存留期間發生。
 + 服務執行個體或分割區複本正在移動。 在 Service Fabric 中，雖然服務在節點間容錯移轉的速度相當快速，但若服務通訊元件太慢而無法啟動，可能會對可用性造成延遲。

妥善處理這些案例，對於系統流暢運作至關重要。 為了達成此目標，需要考量一些事項：

+ 可以連接到每個服務 *位址* 可接聽 （HTTP、 WebSockets 等）。 若服務執行個體或分割區已移動，其位址端點將會變更 (已移至其他具有不同的 IP 位址的節點)。 如果使用內建的通訊元件，這些元件會為您處理重新解析服務位址。 
+ 當服務執行個體再次開啟接聽程式時，服務延遲時間可能會短暫增加，這需取決於服務在移動後開啟的速度。
+ 必須先關閉任何現有的連線，然後等服務於新的節點上開啟時再重新開啟。 妥善關閉節點或重新開啟，可讓現有連線擁有足夠時間正常關閉。

### 測試：移動服務執行個體

我們可以使用 Service Fabric 的 Testability 工具，撰寫測試案例，以不同方式測試這些情況。

1. 移動具狀態服務的主要複本。
 
    具狀態服務分割區的主要複本會因為諸多原因而移動。 以此鎖定特定分割區的主要複本，以便透過高度受控制的方式，查看服務回應移動的情形。

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. 停止節點。

    節點停止後，Service Fabric 會將該節點上所有服務執行個體或分割區，移動至叢集中其他可用的其中一個節點。 以此測試節點從叢集中遺失，且該節點上的所有服務執行個體及複本都必須移動的情況。

    使用 Stop-ServiceFabricNode PowerShell cmdlet 可停止節點：

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### 服務可用性

Service Fabric 是專為提供服務高可用性的平台。 不過，在極端情況下，基礎結構的基礎問題仍可能導致無法使用服務。 因此，請務必一併測試這種案例。

具狀態服務會使用仲裁式系統來複寫狀態，藉以達到高可用性。 換句話說，必須要能使用複本仲裁，才能執行寫入作業。 在極罕見情況下，例如大規模的硬體故障，有可能無法使用複本仲裁。 在此情況下，您將無法執行寫入作業，但仍能執行讀取作業。

### 測試：撰寫作業無法使用

Service Fabric 的 Testability 工具可讓您插入引發仲裁遺失的錯誤，以測試這類案例。 雖然這極為罕見，但仰賴具狀態狀態服務的用戶端和服務務必做好準備，以處理無法要求具狀態服務執行寫入作業的各種情況。 同時，具狀態服務本身也應了解發生這種情況的可能性，並能依正常程序與呼叫者通訊。 

使用 Invoke-ServiceFabricPartitionQuorumLoss PowerShell Cmdlet 會引發仲裁遺失：

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

在此範例中，我們將 `QuorumLossMode` 設為 `PartialQuorumLoss`，以便在引發仲裁遺失時，不會關閉所有複本，如此才能正常執行讀取作業。 若要測試整個分割區都無法使用的案例，您可將此參數設定為 `FullQuorumLoss`。

## 後續步驟

[深入了解 Testability 動作](service-fabric-testability-actions.md)

[深入了解 Testability 案例](service-fabric-testability-scenarios.md) 


