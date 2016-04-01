<properties
   pageTitle="開始使用可靠的服務 | Microsoft Azure"
   description="概述使用無狀態與具狀態服務來建立 Microsoft Azure Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# 開始使用 Microsoft Azure Service Fabric 可靠的服務

Service Fabric 應用程式包含一個或多個執行您的程式碼的服務。 本指南說明如何建立無狀態與具狀態服務網狀架構應用程式，使用 [可靠的服務](service-fabric-reliable-services-introduction.md)。

## 建立無狀態服務

無狀態服務是目前大部分存在於雲端應用程式的服務類型。 服務會被視為無狀態，因為服務本身不包含需要可靠地儲存或設為高度可用的資料；也就是說，如果無狀態服務的執行個體關閉，其所有內部狀態都會遺失。 在這些類型的服務中，狀態必須保存到外部存放區，例如 Azure 資料表或 SQL 資料庫中，才能成為高度可用且可靠。

啟動 Visual Studio 2015 RC 為 **管理員**, ，並建立新 **Service Fabric 應用程式** 專案名為 *HelloWorld*:

![使用新增專案對話方塊來建立新的 Service Fabric 應用程式](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

然後建立 **無狀態服務** 專案名為 *HelloWorldStateless*:

![在第二個對話方塊中，建立無狀態服務](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

您的方案現在包含 2 個專案：

 + **HelloWorld**
    這是 *應用程式* 專案，其中包含您 *服務*。 它也包含描述應用程式的應用程式資訊清單，以及一些幫助您部署應用程式的 PowerShell 指令碼。
 + **HelloWorldStateless**  
    這是服務專案，其中包含無狀態服務實作。


## 實作服務

開啟 **HelloWorldStateless.cs** 服務專案中的檔案。 在 Service Fabric 中，服務可以執行任何商務邏輯。 服務 API 為您的程式碼提供兩個進入點：

 - 開放式的進入點方法，稱為 *RunAsync* 其中您可以開始執行任何工作的負載，例如長時間執行的運算工作負載。

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - 通訊進入點，您可以將其中插入您選擇的通訊堆疊，例如 Web API，在這裡您可以開始接收來自使用者或其他服務的要求。

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

在本教學課程中，我們將著重在 `RunAsync()` 進入點方法，您可以在這裡立即開始執行您的程式碼。
專案範本包括 `RunAsync()` 的範例實作，它會遞增一個循環計數。

> [AZURE.NOTE] 如需使用通訊堆疊的詳細資訊，請參閱 [開始使用 Microsoft Azure Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

當您的服務執行個體已放置並且可以執行時，平台會呼叫這個方法。 針對無狀態服務，這僅表示開啟服務執行個體的時間。 會提供取消語彙基元以協調服務執行個體何時需要關閉。 在 Service Fabric 中，基於各種原因，服務執行個體的這個開啟關閉循環可能會在您整個服務存留期中發生許多次，包括：

- 系統可能為了資源平衡四處移動您的服務執行個體。
- 程式碼中發生錯誤。
- 在應用程式或系統升級期間。
- 當基礎硬體發生中斷。

此協調流程是由系統管理，為了讓您的服務維持高度可用且正確平衡。

`RunAsync()` 執行它自己在 **工作**。 請注意，上述程式碼片段中我們直接跳到 **時** 迴圈，來排定個別的工作，您的工作負載不需要。 取消您的工作負載是由所提供的取消語彙基元協調的協同努力。 系統會先等待您工作結束 (成功完成、取消或錯誤) 再繼續。 它是 **重要** 接受取消語彙基元、 完成任何工作，並結束 `RunAsync()` 儘快系統要求取消時。

在這個無狀態服務範例中，計數會儲存在本機變數。 但是，因為這是無狀態服務，所儲存的值只針對它所在服務執行個體的目前生命週期而存在。 當服務移動或重新啟動時，值將會遺失。

## 建立具狀態服務

Service Fabric 還導入一種新的具狀態服務：可以在服務本身內可靠地維護狀態的服務，並且與使用該服務的程式碼共存。 您的狀態是由 Service Fabric 設為高度可用，而不需要將狀態保存到外部存放區。

若要將我們的計數器值從無狀態轉換成高度可用且持續，即使服務移動或重新啟動亦然，我們需要具狀態服務。

在同一個 **HelloWorld** 應用程式中，加入新的服務應用程式專案上按一下滑鼠右鍵並選取 **新網狀架構服務**。

![將服務加入 Service Fabric 應用程式](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

選取 **Service Fabric 具狀態服務** ，並將它"命名為 HelloWorldStateful"。 按一下 [ **新增**。

![使用新增專案對話方塊來建立新的 Service Fabric 具狀態服務](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

您的應用程式現在應該有兩個服務 ︰ 無狀態服務 *HelloWorld* 和具狀態服務 *HelloWorldStateful*。

開啟 **Helloworldstateful** 中 *HelloWorldStateful* 其中包含下列 `RunAsync` 方法 ︰

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

具狀態服務與無狀態服務具有相同的進入點。 主要差異在於的可用性 *可靠的集合* 和 *狀態管理員*。 `RunAsync()` 可設定狀態服務中運作方式類似於無狀態服務，只不過在具狀態服務平台之前，執行額外的工作代替您執行 `RunAsync()`, ，例如確保 *狀態管理員* 和 *可靠的集合* 備妥使用。

### 可靠的集合與狀態管理員

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** 是一個字典實作，可讓您可靠地儲存在服務中的狀態。 這是 Service Fabric 內建一部分 [可靠的集合](service-fabric-reliable-services-reliable-collections.md)。 有了 Service Fabric 和可靠的集合，您現在可以直接在服務中儲存資料，而不需要外部的持續性存放區，就能擁有高度可用的資料。 Service Fabric 完成此作業所建立和管理多個 *複本* 為您的服務，同時提供 API，管理這些複本和其狀態轉換的複雜性抽。

可靠的集合可以儲存任何 .NET 類型 (包括您的自訂類型)，不過有幾個需要注意的事項：

 1. Service Fabric 可讓您的狀態高度可用的 *複寫* 跨節點狀態，並將它儲存到本機磁碟。 這表示儲存在可靠的集合中的所有項目必須是 *序列化*。 根據預設，可靠的集合使用 [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) 進行序列化，因此請務必確定您的型別都 [資料合約序列化程式支援](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) 時使用預設的序列化程式。

 2. 當您在可靠的集合上認可交易時，物件會複寫以獲得高可用性。 可靠的集合中儲存的物件會保留在服務的本機記憶體中，這表示您具有物件的本機參考。

    很重要的一點是，您不要改變那些物件的本機執行個體而不在交易中的可靠的集合上執行更新作業，因為那些變更不會自動複寫。

 *StateManager* 負責為您管理可靠的集合。 在您的服務中隨時隨地，只需要以名稱向 StateManager 要求可靠的集合，它都會確保您取回參考。 我們不建議將可靠的集合執行個體的參考儲存在類別成員變數或屬性中，因為必須特別小心，以確保參考在服務生命週期中的所有時間都設定為執行個體。 StateManager 會為您處理這項工作，並且針對重複造訪最佳化。

### 交易式和非同步

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

可靠的集合與其 `System.Collections.Generic` 和 `System.Collections.Concurrent` 對應項目具有許多相同的作業，包括 LINQ。 不過，可靠的集合上的作業是非同步的。 可靠的集合的寫入作業，因為 *複寫*, ，也就是說，作業會傳送到其他複本的不同節點上高可用性的服務。

它們也支援 *交易式* 作業，因此您可以維持狀態一致的多個可靠的集合之間。 比方說，您可能會從可靠佇列取出一個工作項目、對它執行作業，然後將結果儲存在可靠字典中，全都在單一交易中完成。 這會被視為不可部分完成的作業，以確保整個作業將會成功，或是都不成功；因此如果您從佇列取消項目之後，但在可以儲存結果之前發生錯誤，那麼會回復整個交易，且項目會保持在佇列中進行處理。

## 執行應用程式

回到 *HelloWorld* 應用程式。 您現在可以建置並部署您的服務。 按下 **F5**, ，和您的應用程式會建置並部署到本機叢集。

一旦服務在執行，您可以看到在產生的 ETW 事件 **診斷事件** 視窗。 請注意，應用程式中會同時顯示無狀態服務和具狀態服務的事件。 您可以按一下來暫停資料流 *暫停* ] 按鈕，然後再展開訊息來檢查訊息的詳細資料。

>[AZURE.NOTE] 之前執行的應用程式，請確定您有一個執行的本機開發叢集。 簽出 [入門指南](service-fabric-get-started.md) 以取得設定本機環境。

![在 Visual Studio 中檢視診斷事件](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 後續步驟

[在 Visual Studio 中偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)

[開始使用 Microsoft Azure Service Fabric Web API 服務與 OWIN 自我裝載](service-fabric-reliable-services-communication-webapi.md)

[深入了解可靠的集合](service-fabric-reliable-services-reliable-collections.md)

[部署應用程式](service-fabric-deploy-remove-applications.md)

[應用程式升級](service-fabric-application-upgrade.md)

[可靠的服務的開發人員參考資料](https://msdn.microsoft.com/library/azure/dn706529.aspx)


