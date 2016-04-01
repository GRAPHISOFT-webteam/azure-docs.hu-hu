<properties
   pageTitle="Service Fabric 上的 Reliable Actor | Microsoft Azure"
   description="描述 Reliable Actors 如何使用 Service Fabric 平台的功能，且涵蓋動作項目開發人員觀點的概念。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="abhisram"/>

# Reliable Acto 如何使用 Service Fabric 平台

動作項目會使用 Azure Service Fabric 應用程式模型來管理應用程式的生命週期。 每個動作項目型別會對應至一個 Service Fabric [服務類型](service-fabric-application-model.md#describe-a-service)。 動作項目程式碼是 [封裝](service-fabric-application-model.md#package-an-application) 做為 Service Fabric 應用程式和 [部署](service-fabric-deploy-remove-applications.md#deploy-an-application) 到叢集。

## 動作項目的應用程式模型概念範例

讓我們採取的動作項目專案範例 [透過 Visual Studio 建立](service-fabric-reliable-actors-get-started.md), ，來說明上述概念。

在 Visual Studio 中建立解決方案後，應用程式資訊清單、服務資訊清單和 Settings.xml 組態檔將包含在動作項目服務專案。 如以下螢幕擷取畫面所示。

![透過 Visual Studio 建立專案][1]

您可以藉由查看包含在動作項目服務專案中的應用程式資訊清單，找到動作項目所封裝的應用程式類型及其版本。 下列程式碼片段便是來自應用程式資訊清單的範例。

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

您可以藉由查看包含在動作項目服務專案中的服務資訊清單，找到動作項目類型所對應的服務類型。 下列程式碼片段便是來自服務資訊清單的範例。

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

透過 Visual Studio 建立應用程式封裝時，[組建輸出] 視窗中的記錄檔會指出應用程式封裝的位置。 例如：

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

以下是上述位置的部分清單 (為了簡潔故省略完整清單)。

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───en-us
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───en-us
                    System.Fabric.Common.Internal.Strings.resources.dll

上述清單中顯示實作 VoicemailBox 動作項目的組件，其包含在應用程式封裝之服務封裝內的程式碼封裝中。  

後續的管理 （也就是 升級和最終刪除） 透過 Service Fabric 應用程式管理機制也會執行的應用程式。 如需詳細資訊，請參閱主題 [應用程式模型](service-fabric-application-model.md), ，[應用程式部署和移除](service-fabric-deploy-remove-applications.md), ，和 [應用程式升級](service-fabric-application-upgrade.md)。

## 動作項目服務的可調整性
叢集系統管理員可以在叢集中為每個服務類型建立一個或多個動作項目服務。 每個動作項目服務可以有一個或多個資料分割 (類似任何其他 Service Fabric 服務)。 為多個服務建立服務類型的能力 (這會對應到動作項目類型) 和為服務建立多個資料分割的能力，可讓動作項目應用程式進行調整。 請參閱 [延展性](service-fabric-concepts-scalability.md) 如需詳細資訊。

> [AZURE.NOTE] 無狀態動作項目服務都需要有 [執行個體](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) 計數為 1。 不支援資料分割中的一個無狀態動作項目服務有多個執行個體。 因此，無狀態動作項目服務無法選擇增加執行個體計數來達成延展性。 他們必須使用如下所述的延展性選項 [延展性文章](service-fabric-concepts-scalability.md)。

## 動作項目的 Service Fabric 資料分割概念
動作項目的識別碼會對應至動作項目服務的資料分割。 動作項目建立於動作項目識別碼所對應的資料分割中。 動作項目執行階段時建立動作項目時，寫入 [EventSource 事件](service-fabric-reliable-actors-diagnostics.md#eventsource-events) ，指出動作項目建立在哪個資料分割。 以下是此事件的範例，將指出有識別碼 `-5349766044453424161` 的動作項目建立於服務 `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService` 與應用程式 `fabric:/VoicemailBoxAdvancedApplication` 的資料分割 `b6afef61-be9a-4492-8358-8f473e5d2487`。

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

另一個有識別碼 `-4952641569324299627` 的動作項目則建立於相同服務但不同的資料分割 (`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) 內，如下列事件指出。

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE] 為求簡潔而省略上述事件的一些欄位。

資料分割識別碼可用來取得資料分割的其他資訊。 例如， [Service Fabric 總管](service-fabric-visualizing-your-cluster.md) 工具可以用來檢視資料分割以及服務和其所屬的應用程式的相關資訊。 以下螢幕擷取畫面顯示資料分割的相關資訊 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`，其中包含有識別碼 `-4952641569324299627` 的動作項目，如上述範例中所示。  

![Service Fabric 總管中資料分割的相關資訊][3]

動作項目可以由程式設計方式取得資料分割識別碼、服務名稱、應用程式名稱和其他 Service Fabric 的平台特定資訊，透過 `Host.ActivationContext` 和 `Host.StatelessServiceInitialization` 或 `Host.StatefulServiceInitializationParameters` 動作項目類型衍生自基底類別的成員。 下列程式碼片段將顯示一個範例。

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### 無狀態動作項目的 Service Fabric 資料分割概念
無狀態動作項目是由 Service Fabric 無狀態服務的資料分割所建立。 動作項目識別碼可以判斷動作項目會建立在哪個資料分割底下。
 [執行個體](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) 計數的無狀態動作項目服務必須是 1。 不支援將執行個體計數變更為其他任何值。 因此，動作項目會在資料分割內部的單一服務執行個體中建立。

> [AZURE.TIP] Fabric Actor 執行階段會發出某些 [無狀態動作項目執行個體的相關事件](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances)。 這些項目對於診斷與效能監視很有幫助。

動作項目執行階段建立無狀態動作項目時，寫入 [EventSource 事件](service-fabric-reliable-actors-diagnostics.md#eventsource-events) ，並指出哪些資料分割和執行個體中建立動作項目。 以下是此事件的範例。 它指出有識別碼 `abc` 的動作項目建立於服務 `fabric:/HelloWorldApplication/HelloWorldActorService` 與應用程式 `fabric:/HelloWorldApplication` 的資料分割 `8c828833-ccf1-4e21-b99d-03b14d4face3` 的執行個體 `130745709600495974` 中

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE] 為求簡潔而省略上述事件的一些欄位。

### 可設定狀態的動作項目的 Service Fabric 資料分割概念
可設定狀態的動作項目是由 Service Fabric 具狀態服務的資料分割所建立。 動作項目識別碼可以判斷動作項目會建立在哪個資料分割底下。 服務的每個資料分割可以有一或多個 [複本](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) ，放置在叢集中不同節點上。 擁有多個複本可為動作項目狀態提供可靠性。 Azure 資源管理員會根據可用的錯誤最佳化放置的位置，並升級叢集中的網域。 相同資料分割的兩個複本永遠不會放在相同節點上。 動作項目一律會建立在它們的動作項目識別碼對應的資料分割主要複本中。

> [AZURE.TIP] Fabric Actor 執行階段會發出某些 [可設定狀態的動作項目相關的事件](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas)。 這些項目對於診斷與效能監視很有幫助。

您還記得，在 [稍早所呈現的 VoiceMailBoxActor 範例](#service-fabric-partition-concepts-for-actors), ，動作項目識別碼 `-4952641569324299627` 磁碟分割內建立 `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`。 該範例的 EventSource 事件也指出動作項目建立在該資料分割的複本 `130745418574851853`。 這是建立動作項目的時候，該資料分割的主要複本。 下列 Service Fabric Explore 螢幕擷取畫面進一步確認這一點。

![Service Fabric 總管中的主要複本][4]

## 動作項目狀態提供者選項
動作項目執行階段中包含一些預設的動作項目狀態提供者。 若要為動作項目服務選擇適當的狀態提供者，就必須了解狀態提供者如何使用基礎服務網狀架構平台功能，讓動作項目狀態高度可用。

根據預設，可設定狀態的動作項目會從索引鍵值存放區使用動作項目狀態提供者。 此狀態提供者建置在由 Service Fabric 平台提供的分散式索引鍵值存放區之基礎上。 狀態永久儲存在裝載主要節點的本機磁碟 [複本](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)。 狀態也會複寫並永久儲存在裝載次要複本之節點的本機磁碟上。 只有複本仲裁將其狀態認可至本機磁碟時，狀態才完成儲存。 索引鍵值存放區具有進階功能，可以偵測到不一致，例如錯誤的進度並自動加以更正。

動作項目執行階段也包含 `VolatileActorStateProvider`。 此狀態提供者會將狀態複寫到複本 (狀態的複本)，但狀態會保留在每個複本上的記憶體中。 如果一個複本中斷和恢復運作，其狀態會從另一個複本重建。 但是，如果所有的複本同時當機，狀態資料將會遺失。 因此，此狀態提供者是適用於應用程式資料可以安然度過幾個複本的失敗，並且可以安然度過計劃的容錯移轉，例如升級。 如果所有複本都遺失，則資料需要透過非 Service Fabric 的外部機制來重建。 您可以透過加入 `VolatileActorStateProvider` 屬性到動作項目類別或組件層級屬性，將可設定狀態的動作項目設為使用揮發性的動作項目狀態提供者。

下列程式碼片段會示範如何變更組件中沒有明確狀態提供者屬性的所有動作項目，以使用 `VolatileActorStateProvider`。

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

下列程式碼片段示範如何將特定動作項目類型的狀態提供者，在此例中為 `VoicemailBox`，變更為 `VolatileActorStateProvider`。

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

請注意，變更狀態提供者需要重新建立動作項目服務。 狀態提供者不能作為應用程式升級的一部分進行變更。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png


