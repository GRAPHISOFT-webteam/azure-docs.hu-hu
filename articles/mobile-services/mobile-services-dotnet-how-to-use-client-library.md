<properties
    pageTitle="使用行動服務的受管理用戶端程式庫 (Windows | Xamarin) | Microsoft Azure"
    description="了解如何搭配 Windows 和 Xamarin 應用程式針對 Azure 行動服務使用 .NET 用戶端。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/02/2015"
    ms.author="glenga"/>


# 如何針對 Azure 行動服務使用受管理的用戶端程式庫

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

## 概觀

本指南將示範如何在 Windows 和 Xamarin 應用程式中針對 Azure 行動服務使用受管理的用戶端程式庫，來執行一般案例。 所涵蓋的案例包括查詢資料、插入、更新及刪除資料、驗證使用者以及處理錯誤。 如果您不熟悉行動服務，您應考慮首先完成 [行動服務快速入門](mobile-services-dotnet-backend-xamarin-ios-get-started.md) 教學課程。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="setup"></a>設定和必要條件

我們假設您已建立行動服務和資料表。 如需詳細資訊，請參閱 [建立資料表](http://go.microsoft.com/fwlink/?LinkId=298592)。 使用本主題中的程式碼，在資料表的名稱為 `TodoItem` ，就會出現下列資料行: `識別碼`, ，`文字`, ，和 `完成`。

其對應的具類型用戶端 .NET 類型如下：


    public class TodoItem
    {
        public string Id { get; set; }
    
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
    
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

請注意， [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) 用來定義 PropertyName 之間的對應的用戶端類型和資料表之間的對應。

當 JavaScript 後端行動服務中啟用動態結構描述時，Azure 行動服務會根據插入或更新要求中的物件為基礎，自動產生新的資料行。 如需詳細資訊，請參閱 [動態結構描述](http://go.microsoft.com/fwlink/?LinkId=296271)。 在 .NET 後端行動服務中，資料表會在專案的資料模型中定義。

## <a name="create-client"></a>如何: 建立行動服務用戶端

下列程式碼將建立用來存取行動服務的 `MobileServiceClient` 物件。


    MobileServiceClient client = new MobileServiceClient(
        "AppUrl",
        "AppKey"
    );

在上面的程式碼中，使用行動服務 URL 和應用程式金鑰 (依此順序) 來取代 `AppUrl` 和 `AppKey`。 您可在 Azure 傳統入口網站上找到這兩項資訊，方法是選取您的行動服務，然後按一下 [儀表板]。
>[AZURE.IMPORTANT]應用程式金鑰是用來針對行動服務篩選出隨機要求，它會隨應用程式一起發送。 因為此金鑰並未加密，所以並不安全。 若要真正保護行動服務資料的安全，您必須改在允許存取之前驗證使用者。 如需詳細資訊，請參閱 [How to: 驗證使用者](#authentication)。

## <a name="instantiating"></a>如何: 建立資料表參考

可存取或修改行動服務資料表中資料的所有程式碼會呼叫 `MobileServiceTable` 物件上的函數。 您取得資料表的參考，藉由呼叫 [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) 方法的執行個體上 `MobileServiceClient`, 、，如下所示:

    IMobileServiceTable<TodoItem> todoTable =
        client.GetTable<TodoItem>();

此為具類型的序列化模型。請參閱討論 [不具類型的序列化模型](#untyped) 下方。

## <a name="querying"></a>如何: 查詢行動服務中的資料

本節將說明如何對行動服務發出查詢，包括下列功能：

- [篩選傳回的資料]
- [排序傳回的資料]
- [以分頁方式傳回資料]
- [選取特定資料行]
- [查詢識別碼的資料]

>[AZURE.NOTE] 系統會強制使用伺服器控制的頁面大小，以防止傳回所有資料列。 這可避免預設的大型資料集要求對服務造成負面影響。 若要傳回 50 個以上的資料列，請使用 `採取` 方法中，[以分頁方式傳回資料] 中所述。

### <a name="filtering"></a>如何: 篩選傳回的資料

下列程式碼將說明如何在查詢中包含 `Where` 子句來篩選資料。 它會傳回其 `Complete` 屬性等於 `false` 的所有 `todoTable` 項目。 `Where` 函數會套用資料列篩選述語來查詢資料表。

    // This query filters out completed TodoItems and
    // items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

您可以檢視使用訊息檢查軟體，例如瀏覽器開發人員工具或 Fiddler 傳送至行動服務之要求的 URI。 如果您查看下面的要求 URI 時，會注意到我們打算修改查詢字串本身:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

此要求通常會被概略轉譯成下列伺服器端上的 SQL 查詢：

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

傳遞至 `Where` 方法的函數可以有任意數目的條件。 以下面的程式碼行為例：

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
       .ToListAsync();

會被概略轉譯 (針對先前顯示的相同要求) 成

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

上面的 `where` 陳述式將找到 `Complete` 狀態設為 false，且 `Text` 不是 Null 的項目。

我們也可以將它改寫成多行程式碼：

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

這兩種方法是相同的，而且可以交換使用。 在一個查詢中串連多個述語的舊有選項較為精簡，也是我們給您的建議。

`where` 子句支援會被轉譯成行動服務 OData 子集的操作。這包括關係運算子 (= =、! =、 <, <=, >, ，> =)，算術運算子 (+、-、 /、 *，%)、 數字的有效位數 (Math.Floor、 Math.Ceiling)、 字串函數 (長度、 子字串、 Replace、 IndexOf、 StartsWith、 EndsWith)、 日期屬性 (年、 月、 日、 時、 分、 秒)、 存取的物件及結合上述所有這些運算式的屬性。

### <a name="sorting"></a>如何: 排序傳回的資料

下列程式碼將說明如何透過在查詢中加上 `OrderBy` 或 `OrderByDescending` 函數來排序資料。 它會從 `todoTable` 中傳回按 `Text` 欄位遞增排序的項目。

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();
    
    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>如何: 以分頁方式傳回資料

依預設，伺服器僅會傳回前 50 筆資料列。 您可以藉由呼叫 [取得] 方法增加傳回的資料列數目。 使用 `採取` 搭配 [Skip] 方法來要求特定 「 頁面 」 的總資料集查詢所傳回。 執行下列查詢時，會傳回資料表中的前三個項目。

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

下列已修訂查詢會略過前三個結果，並傳回接下來的後三個結果。 實際上這就是第二「頁」資料，頁面大小為三個項目。

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

您也可以使用 [IncludeTotalCount] 方法，以確保查詢會取得的總計數 <i>所有</i> 已傳回的記錄，忽略任何採取分頁/限制子句指定:

    query = query.IncludeTotalCount();

這是傳遞硬式編碼分頁值至 `Take` 和 `Skip` 方法的簡化案例。 在實際的應用程式中，您可以搭配頁面巡覽區控制項或類似的 UI 來使用類似上述的查詢，讓使用者導覽至上一頁和下一頁。

#### .NET 後端行動服務的分頁注意事項

若要覆寫.NET 後端行動服務中的 50 個資料列限制，您也必須套用 [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) 到公用的 GET 方法並指定分頁行為。 套用到方法時，下列設定最多傳回 1000 個資料列：

    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>如何: 選取特定資料行

您可以指定結果中要包含的屬性集，方法是在查詢中加上 `Select` 子句。 例如，下列程式碼將示範如何只選取一個欄位以及如何選取及格式化多個欄位：

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();
    
    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

到目前為止，上述的所有函數都是加法，因此我們可以持續呼叫這些函數，每一次查詢受到的影響就會越大。 再提供一個範例：

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>如何: 按識別碼查詢資料

`LookupAsync` 函數可用來查詢具有特定 ID 的資料庫物件。

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

## <a name="inserting"></a>如何: 將資料插入行動服務

> [AZURE.NOTE] 如果您想要執行某類型的插入、查詢、刪除或更新操作，則您必須建立名為 **Id** 的成員。 這是此範例類別 **TodoItem** 具有成員名稱為 **Id** 的原因。 有效的 ID 值必須存在於更新與刪除操作中。

下列程式碼將說明如何將新的資料列插入資料表中。 參數包含要作為 .NET 物件插入的資料。

    await todoTable.InsertAsync(todoItem);

如果唯一自訂 ID 值不會包含在 `todoItem` 傳遞至 `todoTable.InsertAsync` 呼叫時，值的識別碼由伺服器產生，且設定在 `todoItem` 物件傳回至用戶端。

若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

以下是使用電子郵件地址作為唯一字串 id 的範例。

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### 使用識別碼值

行動服務支援資料表 **id** 資料欄使用唯一自訂字串值。 這可讓應用程式使用自訂的值，例如電子郵件地址或使用者名稱作為識別碼。

字串識別碼為您提供下列優點：

+ 不需要往返存取資料庫就能產生識別碼。
+ 輕鬆合併不同資料表或資料庫的記錄。
+ 識別碼值與應用程式邏輯的整合更理想。

當插入的記錄上未設定字串識別碼值時，行動服務會產生唯一值做為識別碼。 您可以使用 `Guid.NewGuid()` 方法，以產生您自己的識別碼值，用戶端上或在.NET 行動後端服務。 若要深入了解在 JavaScript 後端行動服務中產生 Guid，請參閱 [How to: 產生唯一的識別碼值](mobile-services-how-to-use-server-scripts.md#generate-guids)。

您也可以在資料表中使用整數識別碼。 若要使用整數識別碼，您必須建立您的資料表與 `mobile create` 命令使用 `--integerId` 選項。 此命令需要在 Azure 的命令列介面 (CLI) 中執行。 如需有關如何使用 CLI 的詳細資訊，請參閱 [CLI 管理行動服務資料表](../virtual-machines-command-line-tools.md#Mobile_Tables)。

## <a name="modifying"></a>如何: 修改行動服務中的資料

下列程式碼將說明如何使用含有新資訊的相同 ID 來更新現有的執行個體。 參數包含要作為 .NET 物件更新的資料。

    await todoTable.UpdateAsync(todoItem);

若要插入不具類型的資料，您可以充份利用 Json.NET，如下所示。 請注意，進行更新時必須指定 ID，因為那是行動服務識別要更新哪個執行個體的方式。 您可以從 `InsertAsync` 呼叫的結果中取得此 ID。

    JObject jo = new JObject();
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

如果您嘗試在沒有提供 [Id] 值的情況下更新項目，則服務沒有辦法分辨要更新哪個執行個體，因此 Mobile Services SDK 將會發出 `ArgumentException`。


## <a name="deleting"></a>如何: 刪除行動服務中的資料

下列程式碼將說明如何刪除現有的執行個體。 您可以透過 `todoItem` 上所設定的 [Id] 欄位來識別執行個體。

    await todoTable.DeleteAsync(todoItem);

若要刪除不具類型的資料，您可以充份利用 Json.NET，如下所示。 請注意，進行刪除要求時必須指定 ID，因為那是行動服務識別要刪除哪個執行個體的方式。 刪除要求只需要 ID 即可；其他屬性不會傳遞至服務，如果有傳遞其他屬性，服務也會將他們忽略。 `DeleteAsync` 呼叫的結果通常也會是 `null`。 您可以從 `InsertAsync` 呼叫的結果中取得輸入的 ID。

    JObject jo = new JObject();
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

如果您嘗試在未設定 [Id] 欄位的情況下刪除項目，則服務沒有辦法分辨要刪除哪個執行個體，因此您將收到來自服務的 `MobileServiceInvalidOperationException`。 同樣地，如果您嘗試在未設定 [Id] 欄位的情況下刪除不具類型的項目，您將會再次收到來自服務的 `MobileServiceInvalidOperationException`。

## <a name="#custom-api"></a>如何: 呼叫自訂 API

自訂 API 可讓您定義自訂端點，並用來公開無法對應插入、更新、刪除或讀取等操作的伺服器功能。 透過使用自訂 API，您可以進一步控制訊息，包括讀取與設定 HTTP 訊息標頭，並定義除了 JSON 以外的訊息內文格式。 如需如何在您的行動服務中建立自訂 API 的範例，請參閱 [How to: 定義自訂 API 端點](mobile-services-dotnet-backend-define-custom-api.md)。

您可以呼叫其中一個 [InvokeApiAsync] 方法多載在用戶端呼叫自訂 API。 例如，下列程式碼字行會傳送 POST 要求至行動服務上的 **completeAll** API：

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

請注意，這是具類型的方法呼叫，它會要求定義 **MarkAllResult** 傳回類型。 具類型的和不具類型的方法皆受支援。 這個範例幾乎沒什麼用處，因為它是具類型的、不傳送任何裝載、沒有查詢參數，而且不會變更要求標頭。 更實際的範例及更完整的討論 [InvokeApiAsync]，請參閱 [Azure 行動服務用戶端 Sdk 中的自訂 API]。

## 作法：註冊推播通知

行動服務用戶端可讓您向 Azure 通知中樞註冊推播通知。 註冊時，您會取得從特定平台「推送通知服務 (PNS)」取得的控制代碼。 然後您就可以在建立註冊時提供此值以及任何標記。 下列程式碼會為您的 Windows 應用程式向 Windows 通知服務 (WNS) 註冊推播通知：

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =
            await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
    
        // Register for notifications using the new channel and a tag collection.
        var tags = new List<string>{ "mytag1", "mytag2"};
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
    }

請注意，在此範例中，註冊中會包含兩個標記： 如需有關 Windows 應用程式的詳細資訊，請參閱 [將推播通知新增至您的應用程式](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)。

Xamarin 應用程式需要一些額外的程式碼，才能將執行於 iOS 或 Android app 上的 Xamarin 應用程式，個別與 Apple Push Notification Service (APNS) 和 Google 雲端通訊 (GCM) 服務註冊。 如需詳細資訊，請參閱 **將推播通知新增至您的應用程式** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).
>[AZURE.NOTE]當您必須傳送通知給特定的已註冊使用者時，務必在註冊之前要求驗證，然後確認已授權該使用者註冊特定標記。 例如，您必須檢查以確保使用者註冊的標記，不是其他人的使用者識別碼。 如需詳細資訊，請參閱 [將推播通知傳送給已驗證的使用者](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md)。

## <a name="pull-notifications"></a>如何: 在 Windows 應用程式中使用的定期通知

Windows 支援定期通知 (提取通知) 更新動態磚。 透過啟用定期通知，Windows 將定期存取自訂 API 端點來更新 [開始] 功能表上的應用程式磚。 若要使用的定期通知，您必須 [定義自訂 API](mobile-services-javascript-backend-define-custom-api.md) 磚的特定格式傳回 XML 資料。 如需詳細資訊，請參閱 [定期通知](https://msdn.microsoft.com/library/windows/apps/hh761461.aspx)。

下列範例會開啟定期通知，以向*磚*自訂端點要求磚範本資料：

    TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
        new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
        PeriodicUpdateRecurrence.Hour
    );

選取 [PeriodicUpdateRecurrance](https://msdn.microsoft.com/library/windows/apps/windows.ui.notifications.periodicupdaterecurrence.aspx) 最符合您的資料更新頻率的值。

## <a name="optimisticconcurrency"></a>如何: 使用開放式並行存取

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。 在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。 開放式並行存取控制項會假設每筆交易都可以認可，因此不會使用任何資源鎖定。 在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。 如果資料已修改，則會復原認可的交易。

行動服務支援開放式並行存取控制項，方法是使用 `__version` 系統屬性資料欄來追蹤對每個項目的變更，該資料欄是針對行動服務建立的每個資料表所定義的。每當更新記錄時，行動服務會將該筆記錄的 `__version` 屬性設定為新值。在每次更新要求期間，要求所提供的該筆記錄 `__version` 屬性會與伺服器上該筆記錄的相同屬性進行比對。如果傳遞的版本與具有要求不符合伺服器上，則行動服務.NET 用戶端程式庫會擲回 `MobileServicePreconditionFailedException < T >`。例外狀況所提供的類型是來自包含該筆記錄伺服器版本的伺服器記錄。接著應用程式可以使用此資訊，來判斷是否要針對伺服器的正確 `__version` 值來執行更新要求以認可變更。

為了要啟用開放式並行存取，應用程式在 `__version` 系統屬性的資料表類別上定義了資料欄。 下列定義提供了範例。

    public class TodoItem
    {
        public string Id { get; set; }
    
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
    
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    
        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }

使用不具類型的資料表的應用程式可啟用開放式並行存取，方法是將資料表 `SystemProperties` 上的 `Version` 旗標設定如下：

    //Enable optimistic concurrency by retrieving __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

下列程式碼將示範如何在一偵測到寫入衝突時立即解決。 正確的 `__version` 值必須包含在 `UpdateAsync()` 呼叫中才能認可決定。

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;
    
        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }
    
        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }
    
    
    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
                                                    serverItem.Text, localItem.Text),
                                                    "CONFLICT DETECTED - Select a resolution:");
    
        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);
    
        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the
            // item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;
    
            // Updating recursively here just in case another
            // change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };
    
        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };
    
        await msgDialog.ShowAsync();
    }

適用於行動服務使用開放式並行存取的更完整範例，請參閱 [開放式並行存取教學課程]。


## <a name="binding"></a>如何: 將行動服務資料繫結至 Windows 使用者介面

本節說明如何在 Windows app 中使用 UI 元素來顯示傳回的資料物件。 若要查詢 `todoTable` 中的未完成項目，並將它顯示在非常簡單的清單中，您可以執行下列範例程式碼來將清單來源與查詢繫結。 使用 `MobileServiceCollection` 建立行動服務感知繫結集合。

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();
    
    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;
    
    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

受管理的執行階段中的部分控制項支援名為介面 [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916)。 此介面允許控制項在使用者捲動時要求額外資料。 通用 Windows 8.1 應用程式可透過此介面的內建支援 `MobileServiceIncrementalLoadingCollection`, ，以自動處理控制項呼叫。 若要使用 `MobileServiceIncrementalLoadingCollection` 在 Windows 應用程式，執行下列動作:

            MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();
    
        ListBox lb = new ListBox();
        lb.ItemsSource = items;

若要在 Windows Phone 8 和"Silverlight"應用程式上使用新的集合，使用 `ToCollection` 的擴充方法 `Imobileservicetable<t < T >` 和 `IMobileServiceTable < T >`。若要實際載入資料，請呼叫 `LoadMoreItemsAsync()`。

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

使用透過呼叫 `ToCollectionAsync` 或 `ToCollection` 所建立的集合時，您會取得可繫結至 UI 控制項的集合。 此集合有分頁感知功能，例如，控制項可要求集合「載入更多項目」，集合便會為此控制項執行此動作。 此時，控制項會在未涉及使用者程式碼的情況下啟動流程。 不過，因為集合會從網路中載入資料，因此載入有時候可能會失敗。 若要處理此類失敗，您可以覆寫 `MobileServiceIncrementalLoadingCollection` 上的 `OnException` 方法，以便處理呼叫控制項所執行的 `LoadMoreItemsAsync` 時所造成的例外狀況。

最後，想像您的資料表有許多欄位，但您只想要在控制項中顯示其中部分欄位。 您可能使用的指引 > 一節 「[選取特定資料欄](#selecting)"上述選取要顯示在 UI 中的特定資料行。

## <a name="authentication"></a>如何: 驗證使用者

行動服務支援使用各種外部識別提供者 (Facebook、Google、Microsoft 帳戶、Twitter 以及 Azure Active Directory) 來驗證與授權應用程式使用者。 您可以在資料表上設定權限，以限制僅有通過驗證使用者可以存取特定操作。 您也可以使用通過驗證使用者的身分識別來實作伺服器指令碼中的授權規則。 如需詳細資訊，請參閱教學課程 [將驗證新增至您的應用程式]。

支援兩個驗證流程：a _server flow_ 和 a _client flow_。 由於伺服器流程採用提供者的 Web 驗證介面，因此所提供的驗證體驗也最為簡單。 因為用戶端流程採用提供者特定的裝置特定 SDK，因此可允許與裝置特定功能的深入整合。

### 伺服器流程

若要讓行動服務管理驗證程序，在 Windows 應用程式，
您必須向身分識別提供者註冊您的應用程式。 接著在您的行動服務中，您必須設定提供者所提供的應用程式 ID 和密碼。 如需詳細資訊，請參閱教學課程 [將驗證新增至您的應用程式]。

註冊身分識別提供者之後，請直接以提供者的 MobileServiceAuthenticationProvider 值來呼叫 LoginAsync 方法。 例如，下列程式碼將透過使用 Facebook 來初始化伺服器流程登入。

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
    
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

如果您使用除了 Facebook 以外的識別提供者，將變更上方的 MobileServiceAuthenticationProvider 值的值為提供者。

在此案例中，行動服務透過顯示所選提供者的登入頁面，並在使用識別提供者成功登入後產生行動服務驗證權杖的方式，來管理 OAuth 2.0 驗證流程。 LoginAsync 方法傳回 [MobileServiceUser]，也提供這兩個 [userId] 的已驗證的使用者和 [MobileServiceAuthenticationToken]，以作為 JSON web 權杖 (JWT)。 您可以快取並重複使用此權杖，直到它到期為止。 如需詳細資訊，請參閱「快取驗證權杖」。

### 用戶端流程

您的應用程式也可以個別連絡識別提供者，然後將傳回的權杖提供給行動服務進行驗證。 此用戶端流程可讓您為使用者提供單一登入體驗，或從識別提供者擷取其他使用者資料。

#### 使用來自 Facebook 或 Google 的權杖單一登入

您可以最簡化形式來使用用戶端流程，如下列 Facebook 或 Google 的程式碼片段中所示。

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");
    
    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
    
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

#### 使用含有 Live SDK 的 Microsoft 帳戶單一登入

若要能夠驗證使用者，您必須在 Microsoft 帳戶開發人員中心註冊您的應用程式。 然後您必須將此註冊連接到您的行動服務。 完成的步驟 [註冊您的應用程式使用 Microsoft 帳戶登入](mobile-services-how-to-register-microsoft-authentication.md) 建立 Microsoft 帳戶註冊，並將它連接到您的行動服務。 如果您的 app 同時有 Windows 市集與 Windows Phone 8/Silverlight 版本，請先註冊 Windows 市集版本。

以下程式碼會使用 Live SDK 驗證，並使用傳回的權杖登入您的行動服務。

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {
    
        // Get the URL the mobile service.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;
    
        // Create the authentication client for Windows Store using the mobile service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);
    
        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;
    
                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");
    
                // Use the Microsoft account auth token to sign in to Mobile Services.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);
    
                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

### <a name="caching"></a>快取驗證權杖

在某些情況下，在使用者首次驗證之後就可以避免呼叫登入方法。 您可以使用 [PasswordVault] 適用於 Windows 市集應用程式第一次登入時快取目前的使用者身分識別，之後每次您查看是否已有使用者身分識別快取中。 當沒有快取時，您仍需透過登入程序傳送使用者。

    // After logging in
    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));
    
    // Log in
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        user = new MobileServiceUser(creds.UserName);
        user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // Replace access_token_value with actual value of your access token
        token.Add("access_token", "access_token_value");
    }
    
     // Log out
    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", user.UserId));

Windows Phone 應用程式，可能會加密和快取資料，使用 [ProtectedData] 類別，而將機密資訊儲存在隔離儲存區。

## <a name="errors"></a>如何: 處理錯誤

在行動服務中遇到、驗證及解決錯誤的方式有數種。

例如，您可在行動服務中註冊伺服器指令碼，並使用該指令碼來針對插入和更新資料執行各種操作，包括驗證與資料修改。 想像定義與註冊可用來驗證與修改資料的伺服器指令碼，如下所示：

    function insert(item, user, request)
    {
       if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
       } else {
          request.execute();
       }
    }

此伺服器端指令碼可驗證傳送至行動服務之字串資料長度，並拒絕太長的字串，在此案例中為不得超過 10 個字元。

現在，行動服務正在伺服器端上驗證資料並傳送錯誤回應，您可以更新您的 .NET 應用程式以便處理驗證的錯誤回應。

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and Mobile Services has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

## <a name="untyped"></a>如何: 使用不具型別的資料

.NET 用戶端是專為強型別案例所設計的。 不過，較弱型別體驗有時候非常方便；例如，當處理具有開放式結構描述的物件時便是如此。 已依下列方式啟用該案例。 在查詢中，您可以放棄 LINQ 並使用電傳格式。

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
    
    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

您將收到可用作屬性包的 JSON 值。 如需有關 JToken 和 Json.NET 的詳細資訊，請參閱 [Json.NET](http://json.codeplex.com/)

## <a name="unit-testing"></a>如何: 設計單位測試

`MobileServiceClient.GetTable` 所傳回的值和查詢均為介面。這可讓它們輕鬆"mockable"基於測試目的，因此您可以建立 `MyMockTable: IMobileServiceTable < TodoItem >` 來實作您的測試邏輯。

## <a name="customizing"></a>如何: 自訂用戶端

本節說明您可以自訂要求標頭，以及在回應中自訂 JSON 物件序列化的方式。

### <a name="headers"></a>如何: 自訂要求標頭

若要支援您的特定應用程式案例，您可能需要自訂與行動服務的通訊。 例如，您可能會想要在每封連出要求上新增自訂標頭，或甚至變更回應狀態碼。 您可以提供自訂的 DelegatingHandler 來進行，如下列範例所示：

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl",
            "AppKey" ,
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await table.InsertAsync(newItem);
    }
    
    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

此程式碼在要求中新增新的 **x-my-header** 標頭，並且任意設定回應代碼為無法使用。 在實際案例中，您會根據應用程式所需的一些自訂邏輯來設定回應狀態碼。

### <a name="serialization"></a>如何: 自訂序列化

行動服務用戶端程式庫使用 Json.NET 在用戶端上將 JSON 回應轉換為 .NET 物件。 您可以在訊息中設定此序列化在 .NET 類型和 JSON 之間的行為。  [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 類別會公開 `Jsonserializersettings` 型別的屬性 [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

使用此屬性，您可以設定其中一個 Json.NET 屬性，如下所示：

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

此屬性會在序列化期間將所有屬性轉換為小寫。








[what is mobile services]: #what-is 
[concepts]: #concepts 
[how to: create the mobile services client]: #create-client 
[how to: create a table reference]: #instantiating 
[how to: query data from a mobile service]: #querying 
[filter returned data]: #filtering 
[sort returned data]: #sorting 
[return data in pages]: #paging 
[select specific columns]: #selecting 
[look up data by id]: #lookingup 
[how to: bind data to user interface in a mobile service]: #binding 
[how to: insert data into a mobile service]: #inserting 
[how to: modify data in a mobile service]: #modifying 
[how to: delete data in a mobile service]: #deleting 
[how to: use optimistic concurrency]: #optimisticconcurrency 
[how to: authenticate users]: #authentication 
[how to: handle errors]: #errors 
[how to: design unit tests]: #unit-testing 
[how to: query data from a mobile service]: #querying 
[how to: customize the client]: #customizing 
[how to: work with untyped data]: #untyped 
[customize request headers]: #headers 
[customize serialization]: #serialization 
[next steps]: #nextsteps 
[caching the authentication token]: #caching 
[how to: call a custom api]: #custom-api 
[add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md 
[passwordvault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx 
[protecteddata]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx 
[loginasync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx 
[mobileserviceauthenticationprovider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx 
[mobileserviceuser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx 
[userid]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx 
[mobileserviceauthenticationtoken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx 
[ascii control codes c0 and c1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set 
[cli to manage mobile services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services 
[optimistic concurrency tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md 
[mobileserviceclient]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx 
[includetotalcount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx 
[skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx 
[take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx 
[fiddler]: http://www.telerik.com/fiddler 
[custom api in azure mobile services client sdks]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx 
[invokeapiasync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx 

