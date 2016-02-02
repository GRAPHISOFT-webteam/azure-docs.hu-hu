<properties 
   pageTitle="服務匯流排和 .NET 與 AMQP 1.0 |Microsoft Azure"
   description="搭配使用 .NET 的服務匯流排與 AMQP。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/15/2015"
   ms.author="sethm" />


# 搭配使用 .NET 的服務匯流排與 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## 下載服務匯流排 SDK

服務匯流排 SDK 2.1 版或更新版本提供 AMQP 1.0 支援。 您可以下載最新的 SDK，從 [[NuGet]][]。

## 設定 .NET 應用程式以使用 AMQP 1.0

依預設，服務匯流排 .NET 用戶端程式庫能使用專屬的 SOAP 型通訊協定與服務匯流排服務通訊。 若要使用 AMQP 1.0 (而非預設的通訊協定)，您需要明確地設定服務匯流排連接字串，如下節內容所述。 除了這項變更之外，在使用 AMQP 1.0 時，應用程式程式碼基本上會維持不變。

目前的版本中有幾項在使用 AMQP 時不支援的 API 功能。 這些不支援的功能列示於後續一節中 [不支援的功能、 限制和行為的差異](#unsupported-features-restrictions-and-behavioral-differences)。 在使用 AMQP 時，某些進階組態設定亦有不同的意義。

### 使用 App.config 進行設定

讓應用程式使用 App.config 組態檔案來儲存設定是不錯的作法。 對於服務匯流排應用程式，您可以使用 App.config 來儲存服務匯流排設定 **ConnectionString** 值。 範例 App.config 檔案如下所示：

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

`Microsoft.ServiceBus.ConnectionString` 設定的值是用來設定服務匯流排連線的服務匯流排連接字串。 其格式如下所示：

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

其中 `[namespace]` 和 `SharedAccessKey` 取自 [Azure 傳統入口網站 []][]。 如需詳細資訊，請參閱 [如何使用服務匯流排佇列]][]。

使用 AMQP 時，附加的連接字串具有 `;TransportType = Amqp`。 此標記法會通知用戶端程式庫使用 AMQP 1.0 連線到服務匯流排。

## 訊息序列化

當使用預設的通訊協定，是使用.NET 用戶端程式庫的預設序列化行為 [DataContractSerializer []][] 序列化的型別 [BrokeredMessage []][] 用戶端程式庫和服務匯流排服務之間傳輸的執行個體。 用戶端程式庫使用 AMQP 傳輸模式時，使用 AMQP 類型系統，用於序列化 [代理的訊息 ][brokeredmessage] 至 AMQP 訊息。 此序列化作業讓可能在不同平台上執行的接收應用程式 (例如使用 JMS API 來存取服務匯流排的 Java 應用程式) 能夠接收和解譯此訊息。

當您建構 [BrokeredMessage []][] 執行個體，您可以提供.NET 物件做為參數的建構函式做為訊息的本文。 如果是可以對應至 AMQP 基本類型的物件，本文會序列化為 AMQP 資料類型。 如果物件不能直接對應至 AMQP 基本類型。也就是應用程式定義的自訂類型，然後將物件序列化使用 [DataContractSerializer []][], ，並在 AMQP 資料訊息中傳送序列化的位元組。

若要促進與非 .NET 用戶端的互通性，在訊息本文中僅使用可直接序列化為 AMQP 類型的 .NET 類型。 下表詳細說明這些類型及對應的 AMQP 類型系統。

| .NET 主體物件類型| 對應的 AMQP 類型| AMQP 主體區段類型|
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 布林| 布林值| AMQP 值|
| byte| ubyte| AMQP 值|
| ushort| ushort| AMQP 值|
| uint| uint| AMQP 值|
| ulong| ulong| AMQP 值|
| sbyte| byte| AMQP 值|
| short| short| AMQP 值|
| int| int| AMQP 值|
| long| long| AMQP 值|
| float| float| AMQP 值|
| double| double| AMQP 值|
| decimal| decimal128| AMQP 值|
| char| char| AMQP 值|
| DateTime| timestamp| AMQP 值|
| Guid| uuid| AMQP 值|
| byte]| binary| AMQP 值|
| 字串| 字串| AMQP 值|
| System.Collections.IList| list| AMQP 值︰集合中包含的項目只能是在此表格中定義的項目。|
| System.Array| array| AMQP 值︰集合中包含的項目只能是在此表格中定義的項目。|
| System.Collections.IDictionary| map| AMQP 值︰集合中包含的項目只能是在此表格中定義的項目。注意︰僅支援 String 索引鍵。|
| Uri| 描述的 string (請參閱下表)| AMQP 值|
| DateTimeOffset| 描述的 long (請參閱下表)| AMQP 值|
| TimeSpan| 描述的 long (請參閱下表)| AMQP 值|
| Stream| binary| AMQP 資料 (可能有多個)。Data 區段包含從 Stream 物件讀取的原始位元組。|
| 其他物件| binary| AMQP 資料 (可能有多個)。包含使用 DataContractSerializer 或應用程式所提供序列化程式之物件的序列化二進位資料。|

| .NET 類型| 對應的 AMQP 描述類型| 注意事項|
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri| `< 類型名稱 ="uri"class = 限制的來源 ="string">< 描述元 name="com.microsoft:uri"/ >< / 類型 >`| Uri.AbsoluteUri|
| DateTimeOffset| `< 類型名稱 = 「 日期時間位移"class = 限制的來源 ="long">< 描述元名稱 ="com.microsoft:datetime 位移 」 / >< / 類型 >`| DateTimeOffset.UtcTicks|
| TimeSpan| `< 類型名稱 ="timespan"class = 限制的來源 ="long">< 描述元 name="com.microsoft:timespan"/ >< / 類型 > `| TimeSpan.Ticks|

## 不支援的功能、限制和行為差異

使用 AMQP 時，目前不支援服務匯流排 .NET API 的以下功能：

-   交易。

-   透過傳輸目的地傳送。

-   依照訊息序號接收。

-   訊息和工作階段瀏覽。

-   工作階段狀態。

-   批次型 API。

-   擴充接收。

-   訂用帳戶規則的執行階段操作。

-   工作階段鎖定更新。

具體而言，使用 AMQP 時目前不支援以下 API 功能：

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]][]
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]][]

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]][]

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch]][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]][]

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek]][]
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]][]

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]][]

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]][]

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState]][]
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]][]
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]][]

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]][]

相較於預設通訊協定，使用 AMQP 時，服務匯流排 .NET API 也有一些細微的行為差異：

-   [OperationTimeout []][] 屬性會被忽略。

-   `MessageReceiver.Receive(TimeSpan.Zero)` 實作為 `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`。

## 控制 AMQP 通訊協定設定

.NET API 會公開數個設定，以控制 AMQP 通訊協定的行為：

-   **MessageReceiver.PrefetchCount**：控制已套用至連結的初始信用額度。 預設值為 0。

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**︰控制在交涉連線開啟時間期間所提供的 AMQP 框架大小上限。 預設值為 65,536 個位元組。

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**︰如果傳輸可分批進行，這個值會決定用於傳送配置的延遲上限。 預設由傳送者/接收者繼承。 個別的傳送者/接收者可以覆寫預設值 (20 毫秒)。

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**︰控制是否透過 SSL 連線建立 AMQP 連線。 預設值為 **true**。

## 後續步驟

準備好進行深入了解嗎？ 請造訪下列連結：

- [服務匯流排 AMQP 概觀]
- [服務匯流排的 AMQP 1.0 支援分割佇列和主題]
- [Windows Server 適用的服務匯流排中的 AMQP]


[how to use service bus queues]: service-bus-dotnet-how-to-use-queues.md 
[datacontractserializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx 
[brokeredmessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx 
[microsoft.servicebus.messaging.messagingfactory.acceptmessagesession]: https://msdn.microsoft.com/library/azure/jj657638.aspx 
[microsoft.servicebus.messaging.messagingfactory.createmessagesender(system.string,system.string)]: https://msdn.microsoft.com/library/azure/jj657703.aspx 
[microsoft.servicebus.messaging.messagesender.sendbatch(system.collections.generic.ienumerable{microsoft.servicebus.messaging.brokeredmessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx 
[microsoft.servicebus.messaging.messagereceiver.receive(system.int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx 
[microsoft.servicebus.messaging.messagereceiver.receivebatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx 
[microsoft.servicebus.messaging.messagereceiver.completebatch(system.collections.generic.ienumerable{system.guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx 
[microsoft.servicebus.messaging.messagereceiver.peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx 
[microsoft.servicebus.messaging.messagereceiver.peekbatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx 
[microsoft.servicebus.messaging.queueclient.peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx 
[microsoft.servicebus.messaging.queueclient.peekbatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx 
[microsoft.servicebus.messaging.topicclient.sendbatch(system.collections.generic.ienumerable{microsoft.servicebus.messaging.brokeredmessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx 
[microsoft.servicebus.messaging.subscriptionclient.receive(system.int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx 
[microsoft.servicebus.messaging.subscriptionclient.receivebatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx 
[microsoft.servicebus.messaging.subscriptionclient.completebatch(system.collections.generic.ienumerable{system.guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx 
[microsoft.servicebus.messaging.subscriptionclient.peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx 
[microsoft.servicebus.messaging.subscriptionclient.peekbatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx 
[microsoft.servicebus.messaging.subscriptionclient.addrule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx 
[microsoft.servicebus.messaging.subscriptionclient.removerule(system.string)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx 
[microsoft.servicebus.messaging.messagesession.getstate]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx 
[microsoft.servicebus.messaging.messagesession.setstate(system.io.stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx 
[microsoft.servicebus.messaging.messagesession.renewlock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx 
[microsoft.servicebus.messaging.brokeredmessage.renewlock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx 
[operationtimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx 
[nuget]: http://nuget.org/packages/WindowsAzure.ServiceBus/ 
[azure classic portal]: http://manage.windowsazure.com 
[service bus amqp overview]: service-bus-amqp-overview.md 
[amqp 1.0 support for service bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md 
[amqp in service bus for windows server]: https://msdn.microsoft.com/library/dn574799.aspx 

