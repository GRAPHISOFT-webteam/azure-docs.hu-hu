<properties 
    pageTitle="如何使用 Node.js 中的服務匯流排佇列 | Microsoft Azure" 
    description="了解如何從 Node.js 應用程式，在 Azure 中使用服務匯流排佇列。" 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/06/2015" 
    ms.author="sethm"/>


# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 這些範例均以 JavaScript 撰寫並使用 Node.js Azure 模組。 本文說明的案例包括**建立佇列**、**傳送並接收訊息**，以及**刪除佇列**。 如需佇列的詳細資訊，請參閱 [後續步驟 []][] 一節。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## 建立 Node.js 應用程式

建立空白的 Node.js 應用程式。 如需有關如何建立 Node.js 應用程式的指示，請參閱 [建立並部署 Node.js 應用程式到 Azure 網站 []][], ，或 [Node.js 雲端服務 []][] (使用 Windows PowerShell)。

## 設定應用程式以使用服務匯流排

若要使用 Azure 服務匯流排，請下載及使用 Node.js Azure 套件。 此封裝含有一組能與服務匯流排 REST 服務通訊的便利程式庫。

### 使用 Node Package Manager (NPM) 取得封裝

1. 使用 **Windows PowerShell for Node.js** 命令視窗瀏覽至 **c:\\node\\sbqueues\\WebRole1** 您建立範例應用程式檔案的資料夾。

2. 在命令視窗中輸入 **npm install azure**，這應該會產生類似下列內容的輸出：

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3. 您可以手動執行 **ls** 命令，確認 **node\_modules** 建立資料夾。 在該資料夾內找到 **azure** 封裝，其中包含您存取儲存體所需的服務匯流排佇列。

### 匯入模組

使用記事本或其他文字編輯器將以下內容新增至應用程式 **server.js** 檔案的頂端：

```
var azure = require('azure');
```

### 設定 Azure 服務匯流排連接

Azure 模組會讀取環境變數 AZURE\_SERVICEBUS\_NAMESPACE 和 AZURE\_SERVICEBUS\_ACCESS\_KEY，以取得連接到服務匯流排所需的資訊。 如果您未設定這些環境變數，必須在呼叫 **createServiceBusService** 時指定帳戶資訊。

如需針對 Azure 雲端服務組態檔中設定環境變數的範例，請參閱 [Node.js 雲端服務與儲存體 []][]。

如需範例中設定環境變數的 [Azure 傳統入口網站 []][] Azure 網站，請參閱 [Node.js Web 應用程式與儲存體 []][]。

## 建立佇列

**ServiceBusService** 物件可讓您使用服務匯流排佇列。 下列程式碼將建立 **ServiceBusService** 物件。 請將程式碼新增至 **server.js** 檔案的頂端附近，放置在匯入 Azure 模型的陳述式後方：

```
var serviceBusService = azure.createServiceBusService();
```

呼叫 **ServiceBusService** 物件上的 **createQueueIfNotExists** 之後，會傳回指定的佇列 (如果存在)，或以指定的名稱建立新的佇列。 下列程式碼會使用 **createQueueIfNotExists** 來建立或連接至名為的佇列 `myqueue`:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** 也支援其他選項，讓您覆寫預設佇列設定，例如訊息存留時間或佇列大小上限。 下列範例會將佇列大小上限設為 5 GB，並將存留時間 (TTL) 值設為 1 分鐘：

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### 篩選器

您可以將選用的篩選作業套用至使用 **ServiceBusService** 執行的作業。 篩選作業可包括記錄、自動重試等等。篩選器是使用簽章實作方法的物件：

```
function handle (requestOptions, next)
```

方法必須呼叫之後進行前的處理對要求選項， `下一步`, ，並傳遞具有下列簽章的回呼:

```
function (returnObject, finalCallback, next)
```

在此回呼中，以及處理後 **returnObject** (回應要求到伺服器)，回呼必須叫用 `下一步` 它是否存在繼續處理其他篩選，或是直接叫用 `finalCallback`, ，做為結束服務叫用。

Azure SDK for Node.js 包含了實作重試邏輯的兩個篩選器：**ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 下列程式碼將建立使用 **ExponentialRetryPolicyFilter** 的 **ServiceBusService** 物件：

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## 傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式將呼叫 **ServiceBusService** 物件上的 **sendQueueMessage** 方法。 傳送至服務匯排流 (以及服務匯流排接收) 的佇列是 **BrokeredMessage** 物件，而且具有一組標準屬性 (例如 **Label** 和 **TimeToLive**)、一個用來保存自訂應用程式特定屬性的目錄，以及一堆任意的應用程式資料。 應用程式可將字串做為訊息傳遞來設定訊息的內文。 任何必要的標準屬性都會填入預設值。

下列範例示範如何將測試訊息傳送至佇列，名為 `myqueue` 使用 **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

服務匯流排佇列最多可支援 256 KB 的訊息大小 (包含標準和自訂應用程式屬性的標頭可以容納 64 KB 的大小上限)。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 此佇列大小會在建立時定義，上限是 5 GB。 如需配額的詳細資訊，請參閱 [Azure 佇列和服務匯流排佇列]][]。

## 從佇列接收訊息

對於 **ServiceBusService** 物件使用 **receiveQueueMessage** 方法即可從佇列接收訊息。 預設會從佇列刪除唯讀的訊息，不過，您可以將 **isPeekLock** 設定為 **true**，不需要從佇列刪除訊息，即可讀取 (查看) 並鎖定訊息。

在接收作業中讀取和刪除訊息的預設行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果您將 **isPeekLock** 參數設定為 **true**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可呼叫 **deleteMessage** 方法，並提供將刪除的訊息做為參數，來完成接收程序的第二個階段。 **deleteMessage** 方法會將訊息標示為已取用，並將它從佇列中移除。

下列範例示範如何使用 **receiveQueueMessage** 來接收和處理訊息。 範例首先會接收並刪除訊息，然後使用設定為 **true** 的 **isPeekLock** 接收訊息，接著使用 **deleteMessage** 刪除訊息：

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因無法處理訊息，它可以呼叫 **ServiceBusService** 物件上的 **unlockMessage** 方法。 這將導致服務匯流排將佇列中的
訊息佇列內，並讓訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **deleteMessage** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 後續步驟

若要深入了解，請參閱下列資源。

-   [佇列、 主題和訂用帳戶的]][]
-   [節點 [] 的 azure SDK][] GitHub 上的儲存機制
-   [Node.js 開發人員中心](/develop/nodejs/)


[azure sdk for node]: https://github.com/Azure/azure-sdk-for-node 
[azure classic portal]: http://manage.windowsazure.com 
[node.js cloud service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md 
[queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md 
[create and deploy a node.js application to an azure website]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md 
[node.js cloud service with storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md 
[node.js web application with storage]: ../storage/storage-nodejs-how-to-use-table-storage.md 
[azure queues and service bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas 

