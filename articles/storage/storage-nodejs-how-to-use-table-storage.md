<properties
    pageTitle="如何使用 Node.js 中的 Azure 資料表儲存體 | Microsoft Azure"
    description="了解如何使用 Azure 資料表儲存體。 程式碼範例以 Node.js API 撰寫。"
    services="storage"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="robmcm"/>


# 如何使用 Node.js 的 Azure 資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]


## 概觀

本主題示範如何使用 Node.js 應用程式中的 Azure 表格服務執行一般案例。

本主題中的程式碼範例假設您已經有 Node.js 應用程式。 如需如何在 Azure 中建立 Node.js 應用程式的資訊，請參閱下列主題：

- [建置 Node.js 網站並部署至 Azure](建立 Node.js 應用程式並部署至 Azure 網站)
- [使用 WebMatrix 建置 Node.js 網站並部署至 Azure](使用 WebMatrix 建立並部署 Node.js 應用程式)
- [建置 Node.js 應用程式並部署至 Azure 雲端服務](Node.js 雲端服務) (使用 Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## 設定您的應用程式以存取 Azure 儲存體

若要使用 Azure 儲存體，您需要 Azure 儲存體 SDK Node.js，這包含一組便利程式庫，
能與儲存體 REST 服務通訊。

### 使用 Node Package Manager (NPM) 安裝封裝

1.  使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)，並瀏覽至您用來建立您的應用程式的資料夾。

2.  型別 **npm 安裝 azure 儲存體** 命令視窗中。 此命令的輸出類似下列範例。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  您可以手動執行 **ls** 命令，確認 **node\_modules** 建立資料夾。 您可以在該資料夾內找到 **azure 儲存體** 封裝，其中包含您要存取儲存體的程式庫。

### 匯入封裝

將下列程式碼加入至頂端 **server.js** 應用程式中的檔案 ︰

    var azure = require('azure-storage');

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 AZURE\_STORAGE\_ACCOUNT 和 AZURE\_STORAGE\_ACCESS\_KEY 或 AZURE\_STORAGE\_CONNECTION\_STRING，以取得連接到 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，呼叫時，您必須指定帳戶資訊 **TableService**。

如需範例中設定環境變數的 [Azure 入口網站](portal.azure.com) Azure 網站，請參閱 [Node.js web application with Storage]

## 建立資料表

下列程式碼會建立 **TableService** 物件，並使用它來建立新的資料表。 加入下列的頂端附近 **server.js**。

    var tableSvc = azure.createTableService();

若要呼叫 **createTableIfNotExists** 將具有指定名稱建立新的資料表，如果不存在。 如果名為 'mytable' 的資料表尚不存在，下列範例便會建立這個新資料表：

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
        if(!error){
            // Table exists or created
        }
    });

如果建立新資料表，`result` 將是 `true`，如果資料表已存在，則為 `false`。 `response` 會包含要求的相關資訊。

### 篩選器

選用的篩選作業可以套用至使用執行的作業 **TableService**。 篩選作業可包含記錄、自動重試等。篩選器是使用簽章實作方法的物件：

        function handle (requestOptions, next)

在對要求選項進行前處理之後，方法需要呼叫 "next" 並傳遞具有下列簽章的回呼：

        function (returnObject, finalCallback, next)

在此回呼中，以及處理 returnObject (來自對伺服器之要求的回應) 之後，回呼需要叫用 next (如果存在) 以繼續處理其他篩選，或是就改為叫用 finalCallback 結束服務叫用。

實作重試邏輯的兩個篩選器隨附於 Azure SDK for Node.js **ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 下列範例會建立 **TableService** 物件，使用 **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## 將實體加入至資料表

若要新增實體，請先建立一個定義實體屬性的物件。 所有實體必須都包含 **PartitionKey** 和 **RowKey**, ，這些是實體的唯一識別碼。

* **PartitionKey** -決定儲存實體的資料分割

* **RowKey** -唯一識別資料分割內的實體

同時 **PartitionKey** 和 **RowKey** 必須是字串值。 如需詳細資訊，請參閱 [了解表格服務資料模型](http://msdn.microsoft.com/library/azure/dd179338.aspx)。

以下是定義實體的範例。 請注意， **dueDate** 定義為一種 **Edm.DateTime**。 可選擇是否指定型別，若未指定，則會推斷型別。

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] 另外還有 **時間戳記** 欄位插入或更新實體時，由 Azure 設定每一筆記錄。

您也可以使用 **entityGenerator** 來建立實體。 下列範例會建立相同的工作實體使用 **entityGenerator**。

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

若要將實體加入至資料表，將傳遞的實體物件 **insertEntity** 方法。

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
        if(!error){
            // Entity inserted
        }
    });

如果作業成功， `result` 將包含 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 插入記錄和 `response` 將包含作業的相關資訊。

範例回應：

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] 根據預設， **insertEntity** 不會傳回已插入的實體的一部分 `response` 資訊。 若您打算對此實體執行其他作業，或想要快取資訊，將此實體包含在 `result` 中傳回會是個不錯的方式。 您可以藉由啟用 **echoContent** ，如下所示 ︰
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## 更新實體

有多種方法可以用來更新現有的實體：

* **updateEntity** -藉由取代來更新現有實體

* **mergeEntity** -藉由將新的屬性值合併到現有實體來更新現有的實體

* **insertOrReplaceEntity** -藉由取代來更新現有實體。 如果實體不存在，將會插入新的實體。

* **insertOrMergeEntity** -藉由將新的屬性值合併到現有更新現有實體。 如果實體不存在，將會插入新的實體。

下列範例示範如何更新實體使用 **updateEntity**:

    tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] 根據預設，更新實體不會檢查正在更新的資料如果先前由其他處理序修改。 若要支援並行更新：
>
> 1. 取得正在更新之物件的 ETag。 系統會針對實體相關的作業將 ETag 包含在 `response` 中傳回，且可透過 `response['.metadata'].etag` 擷取 ETag。
>
> 2. 對實體執行更新操作時，請將先前擷取的 ETag 資訊新增至新的實體。 例如：
>
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. 執行更新操作。 如果擷取 ETag 值之後，實體 (例如您應用程式的其他執行個體) 進行了修改，系統會傳回 `error`，表示不符合要求中指定的更新條件。

使用 **updateEntity** 和 **mergeEntity**, ，如果正在更新的實體不存在，則更新操作會失敗。 因此如果您想要儲存一個實體，而不管它是否已存在，請使用 **insertOrReplaceEntity** 或 **insertOrMergeEntity**。

 `result` 成功的更新作業將包含 **Etag** 已更新之實體。

## 使用實體群組

有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。 若要達到此目的，使用 **TableBatch** 類別建立批次，然後再使用 **executeBatch** 方法 **TableService** 執行批次的作業。

 下列範例示範在批次中提交兩個實體：

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

成功的批次作業的 `result` 將包含批次中每個作業的相關資訊。

### 處理批次作業

若要檢查新增至批次的操作，您可以檢視 `operations` 屬性。 您也可以使用下列方法來處理操作：

* **清除** -清除批次中的所有操作

* **getOperations** -從批次取得操作

* **hasOperations** -如果批次包含操作會傳回 true

* **removeOperations** -移除操作

* **大小** -傳回批次中的作業數目

## 依索引鍵擷取實體

若要傳回特定的實體基礎 **PartitionKey** 和 **RowKey**, ，使用 **retrieveEntity** 方法。

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

此作業完成時，`result` 將包含實體。

## 查詢實體集合

若要查詢資料表，請使用 **TableQuery** 物件來建置查詢運算式，使用以下子句 ︰

* **選取** -要從查詢傳回的欄位

* **其中** -where 子句

    * **和** - `and` where 條件

    * **或** - `or` where 條件

* **top** -要提取的項目數


下列範例建立的查詢會傳回 PartitionKey 為 'hometasks' 的前 5 個項目。

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

由於 **選取** 未使用，會傳回所有的欄位。 若要執行對資料表執行查詢，使用 **queryEntities**。 下列範例使用此查詢從 'mytable' 傳回實體。

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

如果作業成功，`result.entries` 將包含符合查詢的實體陣列。 如果查詢無法傳回所有實體， `result.continuationToken` 將為非*null* 做的第三個參數 **queryEntities** 來擷取更多結果。 初始查詢中，使用 *null* 第三個參數。

### 查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數欄位。
這可以減少頻寬並提高查詢效能 (尤其是對大型實體而言)。 使用 **選取** 子句並傳遞要傳回的欄位名稱。 例如，下列查詢將只傳回 **描述** 和 **dueDate** 欄位。

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## 刪除實體

您可以使用實體的資料分割和資料列索引鍵來刪除實體。 在此範例中， **task1** 物件包含 **RowKey** 和 **PartitionKey** 要刪除之實體的值。 然後將該物件傳遞至 **deleteEntity** 方法。

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] 考慮使用 Etag 時刪除項目，以確保項目未修改過其他處理程序。 請參閱 [更新實體](#update-an-entity) 如需有關使用 Etag 資訊。

## 刪除資料表

下列程式碼會從儲存體帳戶刪除資料表。

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

如果您不確定資料表是否存在，使用 **deleteTableIfExists**。

## 使用接續 Token

當您查詢大量結果的資料表時，請尋找接續 Token。 可能有大量的資料可供您的查詢可能不知道是否您未建立時辨識
接續語彙基元會出現。

當此類權杖存在時，在查詢實體設定 `continuationToken` 屬性期間，系統便會傳回結果物件。 您可以在執行查詢使用此方法以繼續在分割和資料表實體之間移動。

查詢時，系統可能會將 continuationToken 參數放在查詢物件執行個體和回呼函數之間：

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

如果您檢查 `continuationToken` 物件，您會找到像是 `nextPartitionKey`、`nextRowKey` 和 `targetLocation` 的屬性，這些屬性可以用來逐一查看所有結果。

GitHub 上的 Azure 儲存體 Node.js 儲存機制中也有接續範例。 尋找 `examples/samples/continuationsample.js`。

## 使用共用存取簽章

共用存取簽章 (SAS) 可安全地提供對資料表的精確存取，而不必提供您的儲存體帳戶名稱或金鑰。 SAS 通常用來提供對資料的有限存取，例如允許行動應用程式查詢記錄。

信任的應用程式，例如雲端服務產生 SAS，使用 **generateSharedAccessSignature** 的 **TableService**, ，並提供它給不受信任或不完全信任的應用程式等行動裝置應用程式。 SAS 是使用原則來產生，該原則描述 SAS 有效期間的開始和結束日期，以及授與 SAS 持有者的存取等級。

下列範例會產生新的共用存取原則，讓 SAS 持有者查詢 ('r') 資料表，並於建立它之後的 100 分鐘過期。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

請注意，也必須提供主機資訊，因為 SAS 持有者嘗試存取資料表時需要此資訊。

然後，用戶端應用程式會使用 SAS 加上 **TableServiceWithSAS** 對資料表執行作業。 下列範例會連線到資料表並執行查詢。

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

因為產生的 SAS 只有查詢權限，若嘗試插入、更新或刪除實體，則會傳回錯誤。

### 存取控制清單

您也可以使用存取控制清單 (ACL) 來設定 SAS 的存取原則。 若您要允許用戶端存取資料表，但對每個用戶端提供不同的存取原則，則這會很有用。

ACL 是使用存取原則陣列來實作，每個原則有相關聯的識別碼。 下列範例定義兩個原則，其中一個用於 'user1'，另一個用於 'user2'：

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

下列範例會取得的目前 ACL **hometasks** 資料表，並將新的原則使用 **setTableAcl**。 此方法允許：

    tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

設定 ACL 之後，您可以根據原則的識別碼來建立 SAS。 下列範例會建立 'user2' 的新 SAS：

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## 後續步驟

如需詳細資訊，請參閱下列資源：

-   [Azure 儲存體團隊部落格][]。
-   [Azure Storage SDK for Node][] GitHub 上的儲存機制。
-   [Node.js 開發人員中心](/develop/nodejs/)

  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js web application with Storage]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md


