<properties
    pageTitle="使用行動服務 (Windows 市集) 中的虛刪除 | Microsoft Azure"
    description="了解如何在您的應用程式中使用 Azure 行動服務的虛刪除功能"
    documentationCenter=""
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="wesmc"/>


# 使用行動服務中的虛刪除

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## 概觀

以 JavaScript 或 .NET 後端建立的資料表，可以選擇性地啟用虛刪除功能。 使用虛刪除時，新的資料行稱為 *\__deleted* [SQL 位元類型] 加入至資料庫。 啟用虛刪除時，刪除作業將不會從資料庫中實際刪除資料列，而會將已刪除資料行的值設為 TRUE。

在對已啟用虛刪除的資料表查詢記錄時，依預設將不會在查詢中傳回刪除的資料列。 若要要求這些資料列，您必須傳入查詢參數 *\__includeDeleted=true* 中您 [REST 查詢作業](http://msdn.microsoft.com/library/azure/jj677199.aspx)。 在.NET 用戶端 SDK，您也可以使用的 helper 方法 `IMobileServiceTable.IncludeDeleted()`。

.NET 後端的虛刪除支援最初是隨 Microsoft Azure 行動服務 .NET 後端的 1.0.402 版而發行的。 最新的 NuGet 套件可從這裡， [Microsoft Azure 行動服務.NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165)。


使用虛刪除可能有下列好處：

* 使用 [離線資料同步處理行動服務] 功能時，用戶端 SDK 會自動查詢已刪除的記錄並從本機資料庫中移除。 在未啟用虛刪除的情況下，您必須在後端另行撰寫程式碼，讓用戶端 SDK 得知要從本機存放區中移除哪些記錄。 否則，用戶端本機存放區和後端將會不一致這些已刪除的記錄與用戶端方法 `PurgeAsync()` 必須呼叫才能清除本機存放區。
* 有些應用程式會有一律不實體刪除資料、或僅在稽核後刪除資料的商業需求。 在此情況下，虛刪除功能就可派上用場。
* 虛刪除可用來實作「取消刪除」功能，讓意外刪除的資料可以復原。
不過，虛刪除的記錄會佔用資料庫中的空間，因此您應考慮建立排程的作業來定期實刪除已虛刪除的記錄。 如需相關範例，請參閱 [對 .NET 後端使用虛刪除] 和 [對 JavaScript 後端使用虛刪除]。 用戶端程式碼也應定期呼叫 `PurgeAsync()` 使這些實刪除的記錄不會保留在裝置的本機資料存放區。





## 為 .NET 後端啟用虛刪除

.NET 後端的虛刪除支援最初是隨 Microsoft Azure 行動服務 .NET 後端的 1.0.402 版而發行的。 最新的 NuGet 套件可從這裡， [Microsoft Azure 行動服務.NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165)。

下列步驟將引導您為 .NET 後端行動服務啟用虛刪除。

1. 在 Visual Studio 中，開啟您的 .NET 後端行動服務專案。
2. 以滑鼠右鍵按一下.NET 後端專案，然後按一下 [管理 NuGet 套件]****。
3. 在 [封裝管理員] 對話方塊中，按一下 [ **Nuget.org** 更新以及安裝 1.0.402 版或更新版本的 [Microsoft Azure 行動服務.NET 後端](http://go.microsoft.com/fwlink/?LinkId=513165) NuGet 封裝。
3. 在 Visual Studio 的 [方案總管] 中，展開您 .NET 後端專案下方的 [控制器]**** 節點，然後開啟您的控制器來源。 例如 *TodoItemController.cs*。
4. 在 `initialize ()` 方法將您的控制器 `enableSoftDelete: true` EntityDomainManager 建構函式參數。

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }



## 為 JavaScript 後端啟用虛刪除

如果您要為行動服務建立新的資料表，您可以在資料表建立頁面上啟用虛刪除。

![][2]

若要在 JavaScript 後端中的現有資料表上啟用虛刪除：

1. 在 [Azure 傳統入口網站]，按一下您的行動服務。 然後按一下 [資料] 索引標籤。
2. 在資料頁面上，點選所需的資料表。 然後，在命令列中按一下 [啟用虛刪除]**** 按鈕。 如果資料表已啟用虛刪除時，此按鈕將不會出現，但您將能夠看到 *\__deleted* 資料行時按一下 **瀏覽** 或 **資料行** 的資料表索引標籤。

    ![][0]

    若要停用虛刪除資料表，請按一下 [ **資料行** 標籤，然後按一下 *\__deleted* 資料行和 **刪除** ] 按鈕。

    ![][1]

## <a name="using-with-dotnet"></a>對.NET 後端使用虛刪除

下列排程工作會清除存留期超過一個月的虛刪除記錄：

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
    
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
    
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
    
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
    
            return Task.FromResult(true);
        }
    }

若要深入了解.NET 後端行動服務中排程工作，請參閱: [使用 JavaScript 後端行動服務排程週期性工作](mobile-services-dotnet-backend-schedule-recurring-tasks.md)




## 對 JavaScript 後端使用虛刪除

您可以使用資料表指令碼，為 JavaScript 後端行動服務的虛刪除功能新增邏輯。

若要偵測取消刪除要求，請在您的更新資料表指令碼中使用 "undelete" 屬性：

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }

若要在指令碼的查詢結果中納入已刪除的記錄，請將 "includeDeleted" 參數設為 true：

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

若要透過 HTTP 要求擷取已刪除的記錄，請新增查詢參數 "__includedeleted=true"：

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### 對虛刪除的記錄進行清除的範例排程工作。

此範例排程工作會刪除在特定日期之前更新的記錄：

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

若要深入了解 JavaScript 後端行動服務的排程工作，請參閱: [使用 JavaScript 後端行動服務排程週期性工作](mobile-services-schedule-recurring-tasks.md)。








[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png 
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png 
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png 
[sql bit type]: http://msdn.microsoft.com/library/ms177603.aspx 
[offline data sync for mobile services]: mobile-services-windows-store-dotnet-get-started-offline-data.md 
[azure classic portal]: https://manage.windowsazure.com/ 

