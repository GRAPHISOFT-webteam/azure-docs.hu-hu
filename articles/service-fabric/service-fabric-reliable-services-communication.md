<properties
   pageTitle="Reliable Service 通訊概觀 | Microsoft Azure"
   description="Reliable Service 通訊模型概觀，其中包括開啟服務的接聽程式、解析端點和服務間通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Reliable Service 通訊模型

「Service Fabric 即平台」完全不受服務間的通訊影響。 任何及所有通訊協定和堆疊 (從 UDP 到 HTTP) 都具有平等地位。 它是由服務開發人員選擇服務應有的通訊方式。 Reliable Services 應用程式架構提供幾個預先建置的通訊堆疊及工具來運作您的自訂通訊堆疊，例如可供用戶端用來探索服務端點並與其進行通訊的抽象概念。

## 設定服務通訊

Reliable Services API 使用一個簡單的服務通訊介面。 若要開啟服務的端點，只要實作此介面即可：

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

然後，在服務基底類別方法覆寫項中傳回您的通訊接聽程式實作來新增該實作。 

針對具狀態的情況：

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

針對無狀態的情況：

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

在這兩種情況下，您都會傳回一個可讓您的服務輕鬆使用多個接聽程式的接聽程式集合 - 例如，您可能會有一個 HTTP 接聽程式和一個個別的 WebSocket 接聽程式。 每個接聽程式都會獲得一個名稱及所產生的名稱集合：當用戶端要求服務執行個體或資料分割的接聽位址時，系統會以 JSON 物件的形式呈現位址配對。

在無狀態服務中，覆寫項會傳回 ServiceInstanceListeners 的集合。 ServiceInstanceListener 只包含一個用來建立 ICommunicationListener 並賦予其名稱的函式。 就具狀態服務而言，覆寫項則會傳回 ServiceReplicaListeners 集合。 這與其無狀態的對應項稍有不同，因為 ServiceReplicaListener 可以選擇在次要複本上開啟 ICommunicationListener。 這讓您不僅可以在服務中使用多個通訊接聽程式，也可以指定哪些接聽程式要在次要複本上接受要求，以及哪些接聽程式只在主要複本上進行接聽。 

例如，我們可以有一個只在主要複本上接受 RPC 呼叫的 ServiceRemotingListener，以及一個在次要複本上接受讀取要求的第二、自訂接聽程式：

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

最後，描述所需的服務端點 [服務資訊清單](service-fabric-application-model.md) [端點] 區段下。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通訊接聽程式可以從 `ServiceInitializationParameters` 中的 `CodePackageActivationContext` 存取配置予其的端點資源，並在開啟時開始接聽要求。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] 「 端點 」 資源通用於整個服務封裝和服務封裝啟動時，由 Service Fabric 配置。 因此，所有裝載於相同 ServiceHost 的複本都共用相同的連接埠。 這表示通訊接聽程式應該支援連接埠共用。 建議做法是讓通訊接聽程式在產生接聽位址時，使用資料分割識別碼和複本/執行個體識別碼。

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

撰寫的完整逐步解說的 `ICommunicationListener`, ，請參閱 [開始使用 Microsoft Azure Service Fabric Web API 與 OWIN 自我裝載服務](service-fabric-reliable-services-communication-webapi.md)

## 用戶端對服務間的通訊
可靠服務 API提供下列抽象概念讓撰寫用於與服務進行通訊的用戶端更簡單。

### ServicePartitionResolver
此公用程式類別可協助用戶端在執行階段判斷 Service Fabric 服務的端點。 判斷服務端點的程序在 Service Fabric 術語中稱為「服務端點解析」。

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE] FabricClient 是用來與 Service Fabric 叢集上的各種管理操作的 Service Fabric 叢集的物件。

通常用戶端程式碼不需要直接搭配 `ServicePartitionResolver`。 其在可靠服務 API中建立並傳遞給其他協助程式類別，並在內部使用解析程式及協助用戶端與服務進行通訊。

### CommunicationClientFactory
`ICommunicationClientFactory` 定義通訊用戶端處理站所實作的基底介面，並產生可以與 ServiceFabric 服務通訊的用戶端。 CommunicationClientFactory 的實作將取決於用戶端想要通訊的 Service Fabric 服務所使用的通訊堆疊。 可靠服務 API 提供 CommunicationClientFactoryBase<TCommunicationClient> 會提供這個基底實作 `ICommunicationClientFactory` 介面，並執行常見的所有通訊堆疊的工作。 (就像使用 `ServicePartitionResolver` 來判斷服務端點)。 用戶端通常會實作 CommunicationClientFactoryBase 抽象類別來處理通訊堆疊的特定邏輯。

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
透過處理一般通訊和 Service Fabric 暫時性例外狀況的重試，`ServicePartitionClient` 使用的 CommunicationClientFactory (內部的 ServicePartitionResolver) 將有助於與服務通訊。

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

一般使用模式會看起來如下：

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## 後續步驟
* [使用 Reliable Services 遠端服務進行遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)

* [在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)

* [使用 Reliable Services 的 WCF 通訊](service-fabric-reliable-services-communication-wcf.md)

 


