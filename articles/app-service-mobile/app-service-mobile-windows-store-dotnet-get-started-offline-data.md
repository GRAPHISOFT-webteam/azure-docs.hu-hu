<properties
    pageTitle="啟用 Azure 行動應用程式的離線同步處理 (Windows 8.1) | Microsoft Azure"
    description="了解如何使用 Azure 行動服務快取和同步 Windows 市集應用程式中的離線資料"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/22/2015"
    ms.author="wesmc"/>


# 啟用 Windows 應用程式離線同步處理

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本教學課程會示範如何使用 Azure 行動應用程式後端新增 Windows 8.1 市集或 Windows 8.1 Phone 應用程式的離線支援。 離線同步處理可讓使用者與行動應用程式進行互動--檢視、新增或修改資料--即使沒有網路連線進也可行。 變更會儲存在本機資料庫中︰裝置上線後，這些變更就會與遠端後端進行同步處理。

在本教學課程中，您將更新 Windows 8.1 應用程式專案的教學課程以支援 Azure 行動應用程式的離線功能的 [建立 Windows 應用程式]。 如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要深入了解離線同步功能，請參閱主題 [離線資料同步處理在 Azure 行動應用程式]。

## 需求

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成 [建立 Windows 應用程式 ][create a windows app]。
* [Azure 行動服務 SQLite Store ][sqlite store nuget]
* [SQLite for Windows 8.1](http://www.sqlite.org/downloads)

## 更新用戶端應用程式以支援離線功能

Azure 行動應用程式的離線功能可讓您在離線狀態時，仍可與本機資料庫互動。 若要在應用程式中使用這些功能，您初始化 `MobileServiceClient.SyncContext` 本機存放區。 接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。 在本教學課程中，我們會使用 SQLite 作為本機存放區。

1. 安裝適用於 Windows 8.1 和 Windows Phone 8.1 的 SQLite Runtime。

    * **Windows 8.1 執行階段:** 安裝 [適用於 Windows 8.1 的 SQLite]。
    * **Windows Phone 8.1:** 安裝 [SQLite for Windows Phone 8.1]。
    >[AZURE.NOTE] 這些指示也適用於 Windows 10 UAP 專案，但您應改為安裝 [SQLite for Windows 的 10]。

2. 在 Visual Studio 中開啟您完成 [建立 Windows 應用程式] 教學課程中的專案。 安裝適用於 Windows 8.1 執行階段和 Windows Phone 8.1 專案的 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 封裝。 將 NuGet 參考同時新增至 Windows Store 8.1 和 Windows Phone 8.1 專案。
    >[AZURE.NOTE] 如果安裝在您已安裝的 SQLite 版本以外建立了其他的參考，您會收到編譯錯誤。 您應該透過移除您專案中 [參考]**** 節點內的重複項目，來解決這個錯誤。

3. 在方案總管中，以滑鼠右鍵按一下 Windows 8.1 執行階段和 Windows Phone 8.1 平台專案的 [參考]****，並確定有 SQLite 的參考位於 [擴充功能]**** 區段中。

    ![][1]
    </br>

    **Windows 8.1 執行階段**

    ![][11]
    </br>

    **Windows Phone 8.1**

4. SQLite 是原生程式庫，需要您選擇特定平台架構 (例如 **x86**、**x64** 或 **ARM**)。 [任何 CPU]**** 不受支援。 在方案總管中，按一下上方的 [方案]，將處理器架構下拉式方塊變更為您想測試的其中一項支援的設定。

    ![][13]

5. 在方案總管中，開啟共用專案中的 MainPage.cs 檔案。 在檔案頂端取消註解下列 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. 在 MainPage.cs 中初始化的程式碼行進行註 `todoTable` 為 `IMobileServiceTable`。 取消註解的初始化程式碼行 `todoTable` 為 `IMobileServiceSyncTable`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. 在 MainPage.cs 中標示為的區域 `離線同步處理`, ，取消註解方法 `InitLocalStoreAsync` 和 `SyncAsync`。 此方法 `InitLocalStoreAsync` 初始化與 SQLite 存放區的用戶端同步處理內容。 您可以在 Visual Studio 中，選取所有加上註解的行，並使用鍵盤快速鍵 **Ctrl**+**K**+**U** 來取消註解。

 請注意，在 `SyncAsync` 推送作業會從執行 `MobileServiceClient.SyncContext` 而不是 `IMobileServicesSyncTable`。 這是因為內容會追蹤用戶端針對所有資料表所做的變更。 這是為了解說資料表之間有所關聯的情況。 如需有關此行為的詳細資訊，請參閱 [離線 Azure 行動應用程式中的資料同步]。

     private async Task InitLocalStoreAsync()
     {
         if (!App.MobileService.SyncContext.IsInitialized)
         {
             var store = new MobileServiceSQLiteStore("localstore.db");
             store.DefineTable<TodoItem>();
             await App.MobileService.SyncContext.InitializeAsync(store);
         }
    
         await SyncAsync();
     }
    
     private async Task SyncAsync()
     {
         await App.MobileService.SyncContext.PushAsync();
         await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
     }

8. 在 `OnNavigatedTo` 事件處理常式中，取消註解來呼叫 `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 取消註解 3 呼叫 `SyncAsync` 方法中 `InsertTodoItem`, ，`UpdateCheckedTodoItem`, ，和 `ButtonRefresh_Click`:

     private async Task InsertTodoItem(TodoItem todoItem)
     {
         await todoTable.InsertAsync(todoItem);
         items.Add(todoItem);
    
         await SyncAsync(); // offline sync
     }
    
     private async Task UpdateCheckedTodoItem(TodoItem item)
     {
         await todoTable.UpdateAsync(item);
         items.Remove(item);
         ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);
    
         await SyncAsync(); // offline sync
     }
    
     private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
     {
         ButtonRefresh.IsEnabled = false;
    
         await SyncAsync(); // offline sync
         await RefreshTodoItems();
    
         ButtonRefresh.IsEnabled = true;
     }

10. 新增例外狀況處理常式中的 `SyncAsync` 方法。 在離線的情況下 `MobileServicePushFailedException` 則將擲回 `PushResult.Status = = CancelledByNetworkError`。

    private async Task SyncAsync()
    {
        String errorString = null;
    
        try
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
        }
    
        catch (MobileServicePushFailedException ex)
        {
            errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
              ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
        }
        catch (Exception ex)
        {
            errorString = "Pull failed: " + ex.Message +
              "\n\nIf you are still in an offline scenario, " +
              "you can try your Pull again when connected with your Mobile Serice.";
        }
    
        if (errorString != null)
        {
            MessageDialog d = new MessageDialog(errorString);
            await d.ShowAsync();
        }
    }

在此 `PullAsync` 範例中，我們擷取遠端中的所有記錄 `todoTable`, ，但是它也可以傳遞查詢來篩選記錄。 第一個參數 `PullAsync` 用於增量同步處理，會使用的查詢識別碼 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。 對您用戶端應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 若選擇不要增量同步處理，傳遞 `null` 做為查詢識別碼。 這會擷取每個提取作業的所有記錄，而可能降低效能。

請注意， `MobileServicePushFailedException` 推送和提取作業可能會發生。 它可能發生於提取，因為提取作業會在內部執行推入，以確定所有資料表及任何關聯性都一致。

11. 在 Visual Studio 中按 **F5** 鍵，以重新建置並執行用戶端應用程式。 應用程式的行為會與離線同步變更之前相同，因為它對插入、更新和重新整理作業執行了同步處理作業。 不過，它將會填入在離線狀態時可以使用的本機資料庫。 在填入本機資料庫之後，我們將在下節中建立離線狀態。

## <a name="update-sync"></a>更新用戶端應用程式的同步處理行為

在本節中，您將透過中斷 Azure 行動應用程式後端的連線，修改用戶端應用程式來模擬離線狀態。 當您新增資料項目時，您的例外狀況處理常式會通知您應用程式正以離線模式與 `PushResult.Status = = CancelledByNetworkError`。 新增項目會保留在本機存放區中，但不會同步處理到行動應用程式後端，直到您再度上線並 成功推送到 Azure 行動應用程式後端為止。

1. 編輯共用專案中的 App.xaml.cs。 註解化 **MobileServiceClient** 的初始化，並新增使用無效行動應用程式 URL 的以下幾行：

         public static MobileServiceClient MobileService = 
                new MobileServiceClient("https://your-service.azurewebsites.fail");

    請注意，當應用程式也使用驗證時，這會導致登入失敗。 您也可以透過停用裝置的 WiFi 和行動電話通訊網路，或使用飛航模式來示範離線行為。

2. 按 **F5** 以建置並執行應用程式。 請注意，應用程式啟動後同步處理無法重新整理。
3. 輸入一些新的 todo 項目，然後為每個項目按一下 [儲存]****。 對每一個失敗的推播 `PushResult.Status=CancelledByNetworkError`。 新的 todo 項目在可推送至行動應用程式後端之前，都只會存留在本機存放區中。

    您可以隱藏的例外狀況對話方塊 `PushResult.Status=CancelledByNetworkError`, ，然後用戶端應用程式會如同它已連接到行動裝置應用程式後端支援所有建立、 讀取、 更新、 順暢地刪除 (CRUD) 作業。

4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. (選擇性) 在 Visual Studio 中，開啟 [伺服器總管]****。 瀏覽至 **Azure**->**SQL Database** 中您的資料庫。 在資料庫上按一下滑鼠右鍵，並選取 [在 SQL Server 物件總管中開啟]****。 現在您可以瀏覽至您的 SQL Database 資料表和其內容。 確認後端資料庫中的資料沒有變更。

6. (選擇性)使用 Fiddler 或 Postman 等其他工具來查詢您的行動後端，在表單中使用 GET 查詢 `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`。

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動應用程式後端

在本節中，您會將應用程式重新連接至行動應用程式後端。 您將藉此模擬應用程式在行動應用程式後端中，從離線狀態恢復為線上狀態的情境。 當您第一次執行應用程式， `OnNavigatedTo` 事件處理常式會呼叫 `InitLocalStoreAsync`。 它接著會呼叫 `SyncAsync` 同步處理本機存放區與後端資料庫。 因此，應用程式會嘗試於開機時同步處理。

1. 開啟共用專案中的 App.xaml.cs。 取消註解您先前初始化的 `MobileServiceClient` 使用正確的行動應用程式 URL 和閘道 URL。

2. 按 **F5** 鍵，以重新建置與執行應用程式。 與使用發送和提取作業的 Azure 行動應用程式後端應用程式進行同步的本機變更儘速 `OnNavigatedTo` 事件處理常式會執行。

3. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。 請注意，Azure 行動應用程式後端資料庫與本機存放區之間的資料已同步處理。

4. 在應用程式中，按一下幾個項目旁邊的核取方塊，以在本機存放區中完成它們。

  `UpdateCheckedTodoItem` 呼叫 `SyncAsync` 同步完成行動應用程式後端的每個項目。 `SyncAsync` 呼叫推送和提取。 不過，您應該注意**每當您針對用戶端進行變更的資料表執行提取時，一律會先自動執行用戶端同步處理內容推送**。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。 因此在此情況下，我們可能已移除的呼叫 `PushAsync` 因為它在執行提取時會自動執行。 如果您不注意，此行為會導致非預期的推送。 如需有關此行為的詳細資訊，請參閱 [離線 Azure 行動應用程式中的資料同步]。


## 摘要

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。 在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們想要與伺服器同步處理本機存放區中時，我們使用 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。 這個方法所屬 `IMobileServicesSyncContext` 而不是同步資料表，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    如果用戶端同步處理內容已追蹤該資料表上的變更，則提取一律會先發出同步處理內容推送。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    另外還有的多載 `pullasync ()` 可允許指定以篩選儲存在用戶端的資料查詢。 如果未傳遞查詢， `pullasync ()` 將對應的資料表 (或查詢) 中提取所有資料列。 您可以傳遞查詢，只篩選應用程式需要同步處理的變更。

* 若要啟用增量同步處理，將查詢識別碼，來傳遞 `pullasync ()`。 查詢識別碼用來儲存上次提取作業所產生的上次更新時間戳記。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 如果查詢有一個參數，則相同的參數值可以是查詢識別碼的一部分。

    例如，如果您篩選使用者識別碼，您的唯一查詢識別碼可能是：

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    如果您想選擇不要增量同步處理，則傳遞 `null` 做為查詢識別碼。 在此情況下，擷取所有的記錄在每次呼叫 `PullAsync`, ，這可能會沒有效率。

* 您的應用程式應定期呼叫 `IMobileServiceSyncTable.PurgeAsync()` 才能清除本機存放區。


## 其他資源

* [Azure 行動應用程式中的離線資料同步]

* [雲端報導: Azure 行動服務中的離線同步處理] \ (附註: 的視訊會在行動服務，但是離線同步處理的運作方式類似的方式，在 Azure 行動應用程式)

* [Azure Friday: Azure 行動服務中的離線應用程式]





[update the app to support offline features]: #enable-offline-app 
[update the sync behavior of the app]: #update-sync 
[update the app to reconnect your mobile apps backend]: #update-online-app 
[next steps]: #next-steps 
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png 
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png 
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png 
[offline data sync in azure mobile apps]: ../app-service-mobile-offline-data-sync.md 
[create a windows app]: ../app-service-mobile-windows-store-dotnet-get-started.md 
[sqlite for windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919 
[sqlite for windows phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920 
[sqlite for windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921 
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/ 
[cloud cover: offline sync in azure mobile services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri 
[azure friday: offline-enabled apps in azure mobile services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/ 

