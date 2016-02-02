<properties
   pageTitle="Service Fabric Reliable Actors 概觀 | Microsoft Azure"
   description="Service Fabric Reliable Actor 程式設計模型簡介"
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>


# Service Fabric Reliable Actor 簡介

Reliable Actors API 是兩個所提供的高階架構的其中一個 [Service Fabric](service-fabric-technical-overview.md), ，連同 [可靠服務 API](service-fabric-reliable-services-introduction.md)。

Reliable Actors API 以動作項目模式為基礎，提供非同步、單一執行緒的程式設計模型以簡化您的程式碼，同時還利用 Service Fabric 所提供的延展性和可靠性保證。

## 動作項目

動作項目是被隔離的單一執行緒元件，能夠封裝狀態和行為。 動作項目與 .NET 物件類似，因此能提供自然的程式設計模型。 每個動作項目都是動作項目類型的執行個體，就像是 .NET 物件是 .NET 類型的執行個體。 例如，有的動作項目類型會實作計算機的功能，而該類型會有許多動作項目分散到叢集的各種節點上。 每一個這類的動作項目都有唯一的動作項目識別碼識別。

## 定義和實作動作項目介面

動作項目會使用要求-回應模式傳遞非同步訊息，藉此與系統的其餘部分互動，包括其他動作項目。 這些互動在介面中定義為非同步方法。 例如，實作計算器功能的動作項目類型介面可定義如下。

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

動作項目類型可實作此介面，如下所示：

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

因為方法引動過程及其回應最終會導致叢集中的網路要求，引數與其所傳回之工作的結果型別必須可由平台序列化。 特別是，它們必須 [資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。
> [AZURE.TIP] Fabric Actor 執行階段會發出某些 [動作項目方法相關的事件與效能計數器](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)。 這些項目對於診斷與效能監視很有幫助。

值得一提，是屬於動作項目介面方法的下列規則:
- 動作項目介面方法無法多載。
- 動作項目介面方法不能有輸出、 參照或選擇性參數。

## 動作項目通訊

### 動作項目 Proxy

動作項目用戶端 API 能夠在動作項目執行個體與動作項目用戶端之間提供通訊。 為了與動作項目通訊，用戶端會建立一個動作項目 Proxy 物件來實作動作項目介面。 用戶端會叫用 Proxy 物件上的方法來與動作項目互動。 動作項目 Proxy 可用於用戶端對動作項目，及動作項目對動作項目的通訊。 繼續我們的計算機範例，接下來會為計算機動作項目撰寫如下所示的用戶端程式碼：

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

請注意，有兩段資訊用於建立動作項目 Proxy 物件 - 動作項目 ID 與應用程式名稱。 動作項目識別碼是可唯一識別動作項目，而應用程式名稱會識別識別碼 [Service Fabric 應用程式](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) 中所部署的動作項目。

### 動作項目生命週期

Service Fabric 動作項目是虛擬的，也就是說，其生命週期不會繫結至其記憶體內部表示法。 因此，不需要明確地進行建立或終結。 動作項目執行階段會在第一次收到動作項目的要求時自動啟動動作項目。 如果動作項目不用於特定時間，則動作項目執行階段將會回收記憶體內部物件，同時維護稍後重新啟動動作項目所需的存在知識。 如需詳細資訊，請參閱 [動作項目生命週期和記憶體回收](service-fabric-reliable-actors-lifecycle.md)。

### 位置透明度和自動容錯移轉

為了提供高延展性和可靠性，Service Fabric 將動作項目分散於整個叢集，並且讓動作項目視需要自動從失敗的節點移轉到狀況良好的節點。  `ActorProxy` 類別，用戶端上的執行必要的解決方案，來尋找動作項目 id [分割](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) 並開啟與它通訊通道。  `ActorProxy` 也會在通訊失敗和容錯移轉的情況下重試。 這可確保即使存在錯誤也會可靠地傳遞訊息，但這也表示動作項目實作能夠從相同的用戶端取得重複的訊息。

## 並行

### 回合式存取

動作項目執行階段為存取動作項目方法提供簡單的回合式模型。 這表示動作項目代碼內永遠只能有一個執行緒為使用中。

一個回合包含完整執行動作項目方法以回應要求其他的動作項目或用戶端或完整執行 [計時器/提醒](service-fabric-reliable-actors-timers-reminders.md) 回呼。 即使這些方法和回呼是非同步的執行者執行階段不交錯它們 - 必須完整完成一個回合，才能允許進行下一回合。 換句話說，計時器/提醒回撥目前正在執行的動作項目方法或計時器/提醒回撥必須完整完成，才能對方法或回撥進行新的呼叫。 如果已從方法或回撥傳回執行，且方法或回撥傳回的工作已完成，則方法或回撥視為已完成。 值得強調的是，即使是在不同的方法、計時器與回撥之間，仍會遵守回合式並行。

動作項目執行階段會藉由在某回合的開頭取得一個各動作項目鎖定，然後在回合結束時釋放該鎖定，來強制回合式並行。 因此，回合式並行會依各個動作項目強制執行，不會在動作項目之間強制執行。 動作項目方法和計時器/提醒回撥可代表不同的動作項目同時執行。

以下範例說明上述概念。 如果有一個動作項目類型實作兩個非同步方法 (假設為 *Method1* 與 *Method2*)，也就是計時器與提醒。 下圖顯示代表這兩個屬於此動作項目類型的動作項目 - *ActorId1* 與 *ActorId2* - 執行這些方法與回撥的時間軸範例。

![][1]

上圖遵循的慣例為：

- 每一個垂直線顯示代表特定動作項目執行方法或回撥時的邏輯流程。
- 每個垂直線上標示的事件依時間順序發生，新發生的事件排在舊事件下方。
- 時間軸使用不同的顏色來對應不同的動作項目。
- 反白顯示用於指出代表方法或回撥保留各動作項目鎖定的持續期間。

在上圖中得注意以下幾點：

- 當 *Method1* 代表 *ActorId2* 執行以回應用戶端要求 *xyz789* 時，另一個抵達的用戶端要求 *abc123* 也需要 *Method1* 由 *ActorId2* 執行。 不過，*Method1* 的後者執行會在前者執行完成後才會開始。 同樣的，由 *ActorId2* 註冊的提醒會在 *Method1* 正在執行時引發，以回應用戶端要求 *xyz789*。 提醒回撥只會在 *Method1* 的這兩個執行都完成後才執行。 所有的一切都是因為對 *ActorId2* 強制執行回合式並行。
- 同樣的，也會對 *ActorId1* 強制執行回合式並行，如代表依序發生的 *ActorId1* 執行 *Method1*、*Method2* 和計時器回撥所示。
- 代表*ActorId1* 執行 *Method1* 與代表 *ActorId2* 執行重疊。 這是因為回合式並行只會在動作項目內強制執行，不會在動作項目間強制執行。
- 在某些方法/回撥執行中， `工作` 由方法傳回後，完成方法/回撥。 在某些其他 `工作` 已經依照方法/回撥傳回的時間完成。 在任一情況下，每個動作項目釋放鎖定為止，才能在上述兩種會發生此問題，亦即方法/回撥傳回之後， `工作` 完成。

### 重新進入

動作項目執行階段依預設允許重新進入。 這表示如果動作項目 A 的動作項目方法在動作項目 B 上呼叫方法，然後反過來在動作項目 A 上呼叫另一個方法，則當該方法若是同一個邏輯呼叫鏈結內容的一部分，則允許執行該方法。 所有的計時器與提醒呼叫的開頭都是新的邏輯呼叫內容。 請參閱 [重新進入](service-fabric-reliable-actors-reentrancy.md) 一節以取得詳細資料。

### 並行保證的範圍

動作項目執行階段在控制叫用這些方法的狀況下，提供這些並行保證。 例如，動作項目執行階段會對為回應接收用戶端要求而進行的方法叫用，以及對計時器與提醒回撥，提供這些保證。 然而，如果動作項目程式碼在動作項目執行階段提供的機制之外直接叫用這些方法，則執行階段無法提供任何並行保證。 例如，如果在某個與動作項目方法所傳回的工作不相關聯的工作內容中叫用方法，或如果從動作項目自行建立的執行緒叫用方法，則執行階段無法提供並行保證。 因此，若要執行背景作業，動作項目應該使用 [動作項目計時器或動作項目提醒](service-fabric-reliable-actors-timers-reminders.md) 遵守回合式並行。
> [AZURE.TIP] Fabric Actor 執行階段會發出某些 [與並行相關的事件與效能計數器](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters)。 這些項目對於診斷與效能監視很有幫助。

## 動作項目狀態管理

Fabric Actor 可讓您建立無狀態或可設定狀態的動作項目。

### 無狀態的動作項目

無狀態動作項目，衍生自 `StatelessActor` 基底類別，不需要任何動作項目執行階段管理的狀態。 其成員變數會在其整個記憶體內部生命週期中存留，正如同任何其他的 .NET 類型一樣。 不過，如果在一段無活動期間後回收記憶體，則會遺失其狀態。 同樣的，狀態會因為容錯移轉而失去，這發生在升級、資源平衡作業期間，或是動作項目處理序或其裝載節點失敗的結果。

以下是無狀態動作項目的範例。

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### 可設定狀態的動作項目

可設定狀態動作項目具有需要的記憶體回收與容錯移轉之間保留的狀態。它們衍生自 `StatefulActor < TState >`, ，其中 `TState` 是必須保留的狀態的型別。可以透過動作項目方法中存取狀態 `狀態` 基底類別的屬性。

以下為存取狀態之可設定狀態的動作項目範例。

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

動作項目狀態會保存在磁碟上並複寫到叢集中的多個節點，以在記憶體回收和容錯移轉時保留。 這表示，之類的方法引數和傳回值，動作項目狀態的型別必須是  [資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。
> [AZURE.NOTE] 請參閱 [序列化上的可靠動作項目附註](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) 應該如何定義介面和 Actor State 類型的詳細資訊的文章。  

#### 動作項目狀態提供者

動作項目狀態提供者會儲存與擷取狀態。 可在組件內依狀態提供者特定的屬性，對各動作項目或所有動作項目設定狀態提供者。 動作項目啟動時，其狀態會載入到記憶體中。 動作項目方法完成時，動作項目執行階段會呼叫狀態提供者的方法自動儲存修改的狀態。 如果在儲存作業期間發生失敗，則動作項目執行階段會建立新的動作項目執行個體，並從狀態提供者載入最後一個一致的狀態。

根據預設，可設定狀態的動作項目會使用索引鍵值存放區動作項目狀態提供者，其建置在由 Service Fabric 平台提供的分散式索引鍵值存放區之基礎上。 如需詳細資訊，請參閱主題 」 上 [狀態提供者選擇](service-fabric-reliable-actors-platform.md#actor-state-provider-choices)。
> [AZURE.TIP] 動作項目執行階段會發出某些 [事件與效能計數器與動作項目狀態管理相關](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters)。 這些項目對於診斷與效能監視很有幫助。

#### 唯讀方法

依預設，動作項目執行階段會在動作項目方法呼叫、計時器回撥或提醒回撥完成時儲存動作項目狀態。 儲存狀態完成前不允許其他的動作項目呼叫。

有一些動作項目方法不會修改狀態，在此狀況下，需要花費額外的時間儲存狀態，這會影響系統的整體輸送量。 若要避免此狀況發生，您可以將不修改狀態的方法和計時器回撥標示為唯讀。

下列範例示範如何在動作項目將方法標示為唯讀模式使用 `Readonly` 屬性。

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

可以標示計時器回撥 `Readonly` 屬性類似的方式。 對於提醒，唯讀旗標會傳入的引數為 `RegisterReminder` 來註冊提醒叫用方法。

## 後續步驟

### 概念

[動作項目生命週期和記憶體回收](service-fabric-reliable-actors-lifecycle.md)

[動作項目計時器與提醒](service-fabric-reliable-actors-timers-reminders.md)

[動作項目事件](service-fabric-reliable-actors-events.md)

[動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)

[Fabric Actor 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)

[設定 KVSActorStateProvider 動作項目](service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[動作項目診斷與效能監視](service-fabric-reliable-actors-diagnostics.md)



[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png 

