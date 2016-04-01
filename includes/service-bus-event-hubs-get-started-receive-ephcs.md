## 使用 EventProcessorHost 接收訊息

[EventProcessorHost][] 是管理持續檢查點，藉以簡化來自事件中心之事件的接收.NET 類別，以及來自事件中心的平行接收。 使用 [EventProcessorHost][], ，您也可以將事件分割到多個接收者，即使裝載於不同的節點。 這個範例示範如何使用 [EventProcessorHost][] 單一接收者。  [向外延展的事件處理][] 範例示範如何使用 [EventProcessorHost][] 與多個接收者。

若要使用 [EventProcessorHost][], ，您必須擁有 [Azure 儲存體帳戶][]:

1. 登入 [Azure 傳統入口網站][], ，然後按一下 **新增** 螢幕的底部。

2. 按一下 [ **Data Services**, ，然後 **儲存體**, ，然後 **快速建立**, ，然後輸入儲存體帳戶的名稱。 選取您所需的區域，然後按一下 **建立儲存體帳戶**。

    ![][11]

3. 按一下新建立的儲存體帳戶，然後按一下 **管理存取金鑰**:

    ![][12]

    複製存取金鑰以便稍後在此教學課程中使用。

4. 在 Visual Studio 中建立新的 Visual C# 桌面應用程式專案使用 **主控台應用程式** 專案範本。 將專案命名為 **接收者**。

    ![][14]

5. 在 [方案總管] 中，以滑鼠右鍵按一下方案，以及 [ **管理 NuGet 封裝**。

     **管理 NuGet 封裝** ] 對話方塊隨即出現。

6. 搜尋 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, ，按一下 [ **安裝**, ，並接受使用規定。

    ![][13]

    這會下載、安裝並新增 [Azure Service Bus Event Hub - EventProcessorHost NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)的參考與其所有相依性。

7. 以滑鼠右鍵按一下 **接收者** 專案，然後按一下 **新增**, ，然後按一下 [ **類別**。 將新類別 **SimpleEventProcessor**, ，然後按一下 [ **確定** 建立類別。

8. 在 SimpleEventProcessor.cs 檔案開頭處新增下列陳述式：

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

    接著，將該類別的主體取代為下列程式碼：

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ````

    這個類別會呼叫 **EventProcessorHost** 來處理接收自事件中樞的事件。 請注意， `SimpleEventProcessor` 類別會使用馬錶定期上呼叫檢查點方法 **EventProcessorHost** 內容。 這可確保重新啟動接收者時，遺失的處理工作不超過五分鐘。

9. 在 **程式** 類別中，新增下列 `using` 陳述式，在檔案頂端 ︰

    ```
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.Threading;
    using System.Threading.Tasks;
    ```

    然後，如下所示修改 `Program` 類別中的 `Main` 方法，並替代事件中心名稱和連接字串，以及您在先前各節中複製的儲存體帳戶和金鑰：

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{event hub connection string}";
      string eventHubName = "{event hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ````

> [AZURE.NOTE] 本教學課程使用的單一執行個體 [EventProcessorHost][]。 若要增加輸送量，建議您執行多個執行個體 [EventProcessorHost][], ，如下所示 [向外延展的事件處理][] 範例。 在這些情況下，各種執行個體會自動彼此協調以對已接收的事件進行負載平衡。 如果您想要多個接收者都處理 *所有* 事件，您必須使用 **ConsumerGroup** 概念。 當收到來自不同電腦的事件，可能適合用來指定名稱 [EventProcessorHost][] 執行個體根據電腦 （或角色） 中所部署。 如需這些主題的詳細資訊，請參閱 [事件中心概觀][] 和 [事件中樞程式設計指南][] 主題。

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Scaled out event processing]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Azure classic portal]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png



