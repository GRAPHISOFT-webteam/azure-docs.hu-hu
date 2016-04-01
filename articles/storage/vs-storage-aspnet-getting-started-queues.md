<properties
    pageTitle="開始使用佇列儲存體和 Visual Studio 已連接服務 (ASP.NET) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何在 Visual Studio ASP.NET 專案中開始使用 Azure 佇列儲存體"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>

# 開始使用 Azure 佇列儲存體和 Visual Studio 連線的服務

## 概觀

本文將說明如何開始建立或使用 Visual Studio 中參考了 ASP.NET 專案中的 Azure 儲存體帳戶之後，Visual Studio 中使用 Azure 佇列儲存體 **新增連接的服務** ] 對話方塊。

我們將會示範如何在儲存體帳戶中建立及存取 Azure 佇列。 我們也將顯示如何執行基本的佇列作業，例如新增、修改、讀取和讀取佇列訊息。 範例均以 C# 程式碼並使用 [Azure Storage Client Library for.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。 如需 ASP.NET 的詳細資訊，請參閱 [ASP.NET](http://www.asp.net)。

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。

## 在程式碼中存取佇列

若要存取 ASP.NET 專案中的佇列，您需要將下列項目併入至存取 Azure 佇列儲存體的任何 C# 來源檔案。

1. 確定 C# 檔案頂端的命名空間宣告包含這些 **使用** 陳述式。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得 **CloudStorageAccount** 物件，代表儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得您的儲存體連接字串和儲存體帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得 **CloudQueueClient** 物件以參考儲存體帳戶中的 queueobjects。  

        // Create the queueclient.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得 **CloudQueue** 物件以參考特定的佇列。

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**請注意** 在下列範例使用上述程式碼中的程式碼開頭。

## 在程式碼中建立佇列

若要在程式碼中建立 Azure 佇列，請新增呼叫至 **CreateIfNotExists** 上述程式碼。

    // Create the CloudQuecClient  if it does not exist
    messageQueue.CreateIfNotExists();

## 將訊息新增至佇列

若要將訊息插入現有佇列，建立新 **CloudQueueMessage** 物件，然後呼叫 **AddMessage** 方法。

A **CloudQueueMessage** 可以從字串 （採用 utf-8 格式） 或位元組陣列建立物件。

以下是插入訊息 'Hello, World' 的範例。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## 讀取佇列中的訊息

透過呼叫 PeekMessage() 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## 讀取並移除佇列中的訊息

您的程式碼可以使用兩個步驟將訊息從佇列中移除 (清除佇列)。
1. 呼叫 GetMessage() 取得佇列中的下一個訊息。 對於從此佇列讀取訊息的任何其他程式碼而言，將無法看到從 GetMessage() 傳回的訊息。 依預設，此訊息會維持 30 秒的不可見狀態。
2.  若要完成從佇列移除訊息，請呼叫 **DeleteMessage**。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 下列程式碼會呼叫 **DeleteMessage** 處理完訊息之後。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## 使用其他將訊息移出佇列的選項

自訂從佇列中擷取訊息的方法有兩種。
首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定
較長或較短的可見度逾時，讓您的程式碼有較長或較短的
時間可以完全處理每個訊息。 下列程式碼範例將使用
**GetMessages** 方法一次呼叫中取得 20 個訊息。 接著，它會
每個訊息使用 **foreach** 迴圈。 它也會將可見度
逾時設定為每個訊息五分鐘。 請注意，系統會針對所有訊息
同時開始計時 5 分鐘，所以從呼叫
若要呼叫 **GetMessages**, ，任何尚未刪除的訊息
訊息都會重新出現。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## 取得佇列長度

您可以取得佇列中的估計訊息數目。 Auch die Eigenschaften
**FetchAttributes** 方法會要求以 queueservice
佇列屬性，其中包含訊息計數。  **ApproximateMethodCount**
屬性會傳回
**FetchAttributes** 方法，而無需呼叫 queueservice。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## 搭配使用 Async-Await 模式和通用佇列 API

這個範例示範如何搭配使用 Async-Await 模式和通用佇列 API。 此範例會呼叫每個指定方法的非同步版本，這可透過每個方法的非同步 Postfix 來查看。 使用非同步方法時，async-await 模式會暫停本機執行，直到呼叫完成為止。 這種行為可讓目前的執行緒執行其他工作，有助於避免發生效能瓶頸並提升應用程式的整體回應。 如需在.NET 中使用 Async-await 模式的詳細資訊，請參閱 [Async 和 Await （C# 和 Visual Basic）] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## 刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫
**刪除** 佇列物件上的方法。

    // Get a reference to the CloudQueue object named 'messageQueue' as described in "Access a queue in code"

    // Delete the queue.
    messageQueue.Delete();



## 後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]


