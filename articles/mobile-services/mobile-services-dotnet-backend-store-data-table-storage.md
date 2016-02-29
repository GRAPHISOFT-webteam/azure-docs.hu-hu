<properties
    pageTitle="建置使用資料表儲存體的 .NET 後端行動服務 | Azure 行動服務"
    description="了解如何搭配 .NET 後端行動服務使用 Azure 資料表儲存體。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/11/2015"
    ms.author="glenga"/>

# 建置使用資料表儲存體的 .NET 後端行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


本主題說明如何在 .NET 後端行動服務中使用非關聯式資料存放區。 在本教學課程中，您將修改 Azure 行動服務快速入門專案，以使用 Azure 資料表儲存體而非預設 Azure SQL Database 資料存放區。

本教學課程，必須先完成 [開始使用行動服務] 教學課程。 您還需要一個 Azure 儲存體帳戶。

##在 .NET 後端行動服務中設定 Azure 資料表儲存體

首先，您必須設定您的行動服務和 .NET 後端程式碼專案以連接到 Azure 儲存體。

1. 在 **方案總管] 中** 在 Visual Studio 中，以滑鼠右鍵按一下.NET 後端專案，然後 **管理 NuGet 封裝**。

2. 在左窗格中，選取 **線上** 類別目錄中，選取 **及 [Stabile Only**, ，搜尋 **MobileServices**, ，按一下 [ **安裝** 上 **Microsoft Azure 行動服務.NET 後端 Azure 儲存體延伸** 套件，然後接受授權合約。

    ![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

    這會將 Azure 儲存體服務支援新增至 .NET 後端行動服務專案。

3. 如果您尚未建立儲存體帳戶，請參閱 [如何建立儲存體帳戶](../storage-create-storage-account.md)。

4. 在 [Azure 傳統入口網站]，按一下 [ **儲存體**, 、 儲存體帳戶，然後按一下 [ **管理金鑰**。

5. 請記下的 **儲存體帳戶名稱** 和 **便捷鍵**。

6. 在您的行動服務中，按一下 [ **設定** 索引標籤上，向下捲動至 **連接字串** 並輸入新的連接字串與 **名稱** 的 `StorageConnectionString` 和 **值** 也就是儲存體帳戶連接字串格式如下。

        DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

    ![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. 在上述字串中，會取代的值 `<ACCOUNT_NAME>` 和 `<ACCESS_KEY>` 入口網站中的值，然後按一下 [ **儲存**。

    儲存體帳戶連接字串會以加密方式儲存在應用程式設定中。 您可以在執行階段在任何資料表控制器中存取此字串。

8. 在 Visual Studio 的 [方案總管] 中，為行動服務專案開啟 Web.config 檔案，並新增下列新的連接字串：

        <add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. 使用步驟 6 的連接字串來取代 `<STORAGE_CONNECTION_STRING>` 預留位置。

    在本機電腦上執行行動服務時，該服務會使用此連接字串，讓您先測試程式碼再發佈。 在 Azure 中執行時，行動服務會改用入口網站中設定的連接字串值，並忽略專案中的連接字串。

## <a name="modify-service"></a>修改資料類型和資料表控制器

由於 TodoList 快速入門專案設計用於處理使用 Entity Framework 的 SQL Database，所以您必須在專案中進行一些更新才能使用資料表儲存體。

1. 修改 **TodoItem** 資料類型以衍生自 **StorageData** 而不是 **EntityData**, 、，如下所示。

        public class TodoItem : StorageData
        {
            public string Text { get; set; }
            public bool Complete { get; set; }
        }

    >[AZURE.NOTE] **StorageData** 類型具有需要的格式字串的組合索引鍵的 Id 屬性 *partitionId*,，*rowValue*。

2. 在 **TodoItemController**, ，新增下列 using 陳述式。

        using System.Web.Http.OData.Query;
        using System.Collections.Generic;

3. 取代 **初始化** 方法 **TodoItemController** 下使用。

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName,
                tableName, Request, Services);
        }

    這會使用儲存體帳戶連接字串，為要求的控制站建立新的儲存體網域管理員。

3. 取代現有 **GetAllTodoItems** 為下列程式碼的方法。

        public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        }

    不同於 SQL 資料庫，此版本不會傳回 IQueryable<TEntity>因此可以繫結至結果但未進一步編寫的查詢中。

## 更新用戶端應用程式

您需要在用戶端上進行一項變更，以便快速入門應用程式處理使用資料表儲存體的 .NET 後端。 這是由資料表儲存體提供者所預期的複合索引鍵造成。

1. 開啟包含資料存取碼的用戶端程式碼檔案，並尋找執行插入作業的方法。

2. 更新正在新增的 TodoItem 執行個體，使用字串格式 `<partitionID>,<rowValue>` 明確設定 Id 欄位。

    這是如何在 C# 應用程式中設定此 ID 的範例，其中 partition 部分是固定的，而 row 部分是以 GUID 為基礎。

         todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

您現在已可測試應用程式。

## <a name="test-application"></a>測試應用程式

1. (選用) 重新發佈您的行動服務 .NET 後端專案。

    您也可先在本機測試您的行動服務，再將 .NET 後端專案發佈至 Azure。 不論在本機或在 Azure 中測試，行動服務都會使用 Azure 資料表儲存體。

4. 執行連接到您的行動服務的快速入門用戶端應用程式。

    請注意，您看不見您先前使用快速入門教學課程加入的項目。 這是因為資料表存放區目前是空的。

5. 加入新項目以產生資料庫變更。

    除了您的資料現在儲存在非關聯式存放區而不是 SQL Database 中，應用程式和行動服務的行為應如同以往。

##後續步驟

您現在已看到搭配使用資料表儲存體與 .NET 後端是多麼簡單，請考慮瀏覽某些其他後端儲存體選項：

+ [連接到內部部署 SQL Server 使用混合式連線](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>混合式連線可讓您安全地連接到內部部署資產的行動服務。 因此，您可以藉由混合式連線讓行動用戶端使用 Azure 存取內部部署資料。 支援的資產包括任何可在靜態 TCP 連接埠上執行的資源，例如 Microsoft SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

+ [將影像上傳至 Azure 儲存體使用行動服務](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>示範如何擴充 TodoList 範例專案，可讓您上傳至 Azure Blob 儲存體的應用程式的映像。

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Azure classic portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab

