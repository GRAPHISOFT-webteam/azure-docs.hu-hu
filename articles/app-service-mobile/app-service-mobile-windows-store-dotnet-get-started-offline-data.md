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

在本教學課程中，您將會更新 Windows 8.1 應用程式專案的教學課程 [Create a Windows app] 以支援 Azure 行動應用程式的離線功能。 如果您不要使用下載的快速入門伺服器專案，必須將資料存取擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。 

若要深入了解離線同步功能，請參閱本主題 [Offline Data Sync in Azure Mobile Apps]。

## 需求

本教學課程需要下列各項：

* 執行於 Windows 8.1 的 Visual Studio 2013。
* 完成 [建立 Windows 應用程式][create a windows app]。
* [Azure 行動服務 SQLite Store][sqlite store nuget]
* [SQLite for Windows 8.1](http://www.sqlite.org/downloads)

## 更新用戶端應用程式以支援離線功能

Azure 行動應用程式的離線功能可讓您在離線狀態時，仍可與本機資料庫互動。 若要在您的應用程式中使用這些功能，您必須將 `MobileServiceClient.SyncContext` 初始化至本機存放區。 接著，請透過 `IMobileServiceSyncTable` 介面參考您的資料表。 在本教學課程中，我們會在本機存放區使用 SQLite。

1. 安裝適用於 Windows 8.1 和 Windows Phone 8.1 的 SQLite 執行階段。

    * **Windows 8.1 Runtime:** 安裝 [SQLite for Windows 8.1]。
    * **Windows Phone 8.1:** 安裝 [SQLite for Windows Phone 8.1]。

    >[AZURE.NOTE] 這些指示也適用於 Windows 10 UAP 專案，但您應改為安裝 [SQLite for Windows 10]。

2. 在 Visual Studio 中開啟您在完成專案 [Create a Windows app] 教學課程。 安裝 **Microsoft.Azure.Mobile.Client.SQLiteStore** Windows 8.1 runtime 和 Windows Phone 8.1 專案的 NuGet 封裝。 將 NuGet 參考同時新增至 Windows Store 8.1 和 Windows Phone 8.1 專案。

    >[AZURE.NOTE] 如果安裝建立的 SQLite 不同版本的額外參考不是您已安裝，您會收到編譯錯誤。 您應該移除重複項目中的，以解決此錯誤 **參考** 在專案中的節點。

3. 在 [方案總管] 中，以滑鼠右鍵按一下 **參考** 用於 Windows 8.1 執行階段和 Windows Phone 8.1 平台專案，並確定有 SQLite 中的參考 **延伸** 一節。

    ![][1]
    </br>

    **Windows 8.1 Runtime**

    ![][11]
    </br>

    **Windows Phone 8.1**

4. SQLite 是原生程式庫，而且需要您選擇特定平台架構例如 **x86**, ，**x64**, ，或 **ARM**。 **任何 CPU** 不支援。 在方案總管中，按一下上方的 [方案]，將處理器架構下拉式方塊變更為您想測試的其中一項支援的設定。

    ![][13]

5. 在方案總管中，開啟共用專案中的 MainPage.cs 檔案。 在檔案頂端取消註解下列 using 陳述式：

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. 在 MainPage.cs 中，把將 `todoTable` 初始化為 `IMobileServiceTable` 的程式碼行註解化。 取消註解將 `todoTable` 初始化為 `IMobileServiceSyncTable` 的程式碼行：

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. 在 MainPage.cs 中標示為 `Offline sync` 的區域內，取消註解 `InitLocalStoreAsync` 和 `SyncAsync` 方法。 `InitLocalStoreAsync` 方法會初始化與 SQLite 存放區的用戶端同步處理內容。 在 Visual Studio 中，您可以選取所有加上註解的程式碼行，並使用 **Ctrl**+**K**+**U** 鍵盤快速鍵來取消註解。

    請注意，在 `SyncAsync` 中推送作業是透過 `MobileServiceClient.SyncContext` 執行而非 `IMobileServicesSyncTable`。 這是因為內容會追蹤用戶端針對所有資料表所做的變更。 這是為了解說資料表之間有所關聯的情況。 如需有關此行為的詳細資訊，請參閱 [Offline Data Sync in Azure Mobile Apps]。

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

8. 在 `OnNavigatedTo` 事件處理常式中，取消註解對 `InitLocalStoreAsync` 的呼叫：

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 取消註解 `InsertTodoItem`、`UpdateCheckedTodoItem` 和 `ButtonRefresh_Click` 方法中對 `SyncAsync` 的 3 個呼叫：

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

10. 在 `SyncAsync` 方法中新增例外狀況處理常式。 在離線情況下，會一起擲回 `MobileServicePushFailedException` 與 `PushResult.Status == CancelledByNetworkError`。

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

    在此 `PullAsync` 範例中，我們擷取遠端 `todoTable` 中的所有記錄，但也可以藉由傳遞查詢來篩選記錄。 `PullAsync` 的第一個參數是用於增量同步處理的查詢識別碼，會使用 `UpdatedAt` 時間戳記取得自從上次同步後修改過的記錄。 對您用戶端應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 若選擇不要增量同步處理，請傳遞 `null` 做為查詢識別碼。 這會擷取每個提取作業的所有記錄，而可能降低效能。

    請注意，推送和提取作業可能會發生 `MobileServicePushFailedException`。 它可能發生於提取，因為提取作業會在內部執行推入，以確定所有資料表及任何關聯性都一致。

11. 在 Visual Studio 中按 **F5** 鍵以重新建置並執行用戶端應用程式。 應用程式的行為會與離線同步變更之前相同，因為它對插入、更新和重新整理作業執行了同步處理作業。 不過，它將會填入在離線狀態時可以使用的本機資料庫。  在填入本機資料庫之後，我們將在下節中建立離線狀態。  

## <a name="update-sync"></a>更新用戶端應用程式的同步處理行為

在本節中，您將透過中斷 Azure 行動應用程式後端的連線，修改用戶端應用程式來模擬離線狀態。 當您新增資料項目時，您的例外狀況處理常式會通知您應用程式正在離線模式中運行 (`PushResult.Status == CancelledByNetworkError`)。 新增項目會保留在本機存放區中，但不會同步處理到行動應用程式後端，直到您再度上線並 成功推送到 Azure 行動應用程式後端為止。

1. 編輯共用專案中的 App.xaml.cs。 標記為註解的初始化 **MobileServiceClient** ，並新增使用無效行動應用程式 URL 的以下幾行 ︰

         public static MobileServiceClient MobileService = 
                new MobileServiceClient("https://your-service.azurewebsites.fail");

    請注意，當應用程式也使用驗證時，這會導致登入失敗。 您也可以透過停用裝置的 WiFi 和行動電話通訊網路，或使用飛航模式來示範離線行為。

2. 按下 **F5** 以建置並執行應用程式。 請注意，應用程式啟動後同步處理無法重新整理。
3. 輸入一些新的 todo 項目，然後按一下 **儲存** 針對每個。 對於包含 `PushResult.Status=CancelledByNetworkError` 的每個項目，推送都會失敗。 新的 todo 項目在可推送至行動應用程式後端之前，都只會存留在本機存放區中。 
 
    您可以隱藏 `PushResult.Status=CancelledByNetworkError` 的例外狀況對話方塊，用戶端應用程式會如同連線到行動應用程式後端的情況運作，順暢地支援所有建立、讀取、更新、刪除 (CRUD) 作業。 

4. 關閉應用程式並重新加以開啟，以驗證您所建立的新項目持續存留於本機存放區中。

5. （選擇性）在 Visual Studio 中開啟 **伺服器總管**。 瀏覽至您的資料庫 **Azure**]-> [**SQL 資料庫**。 以滑鼠右鍵按一下您的資料庫，然後選取 **在 SQL Server 物件總管中開啟**。 現在您可以瀏覽至您的 SQL Database 資料表和其內容。 確認後端資料庫中的資料沒有變更。

6. (選擇性) 使用 REST 工具 (例如 Fiddler 或 Postman) 來查詢您的行動後端 (使用表單 `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` 中的 GET 查詢)。 

## <a name="update-online-app"></a>更新應用程式以重新連接您的行動應用程式後端

在本節中，您會將應用程式重新連接至行動應用程式後端。 您將藉此模擬應用程式在行動應用程式後端中，從離線狀態恢復為線上狀態的情境。 當您第一次執行應用程式時，`OnNavigatedTo` 事件處理常式會呼叫 `InitLocalStoreAsync`。 接著會呼叫 `SyncAsync` 來同步處理您的本機存放區與後端資料庫。 因此，應用程式會嘗試於開機時同步處理。

1. 開啟共用專案中的 App.xaml.cs。 取消註解先前的 `MobileServiceClient` 初始化，以使用正確的行動應用程式 URL 與閘道器 URL。

2. 按下 **F5** 鍵重新建置並執行應用程式。 該應用程式會在 `OnNavigatedTo` 事件處理常式執行時使用推送與提取作業，同步處理您的本機變更與 Azure 行動應用程式後端。

3. (選擇性) 使用 SQL Server 物件總管或 REST 工具 (例如 Fiddler) 檢視已更新的資料。 請注意，Azure 行動應用程式後端資料庫與本機存放區之間的資料已同步處理。

4. 在應用程式中，按一下幾個項目旁邊的核取方塊，以在本機存放區中完成它們。

  `UpdateCheckedTodoItem` 呼叫 `SyncAsync` 同步完成行動應用程式後端的每個項目。 `SyncAsync` 呼叫推送和提取。 不過，您應該注意到 **每當您執行用戶端所做的變更的資料表提取時，用戶端同步處理內容上的推入一定會執行第一次自動**。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。 因此在此情況下，我們可以先移除對 `PushAsync` 的呼叫，因為執行提取時它會自動執行。 如果您不注意，此行為會導致非預期的推送。 如需有關此行為的詳細資訊，請參閱 [Offline Data Sync in Azure Mobile Apps]。


##摘要

為了支援行動服務的離線功能，我們使用了 `IMobileServiceSyncTable` 介面，並對本機存放區初始化 `MobileServiceClient.SyncContext`。 在此案例中，我們以本機存放區做為 SQLite 資料庫。

正常情況下，在行動服務的 CRUD 作業執行時，應用程式會如同仍處於連接狀態，但所有的作業都會對本機存放區執行。

當我們要同步處理本機存放區與伺服器時，我們使用 `IMobileServiceSyncTable.PullAsync` 和 `MobileServiceClient.SyncContext.PushAsync` 方法。

*  為了將變更推送至伺服器，我們呼叫了 `IMobileServiceSyncContext.PushAsync()`。 此方法是 `IMobileServicesSyncContext` 的成員之一 (而不是同步資料表)，因為它會在所有資料表之間推送變更。

    只有以某種方式在本機上修改過的記錄 (透過 CUD 作業)，才會傳送至伺服器。

* 為了將資料從伺服器上的資料表提取至應用程式，我們呼叫了 `IMobileServiceSyncTable.PullAsync`。

    如果用戶端同步處理內容已追蹤該資料表上的變更，則提取一律會先發出同步處理內容推送。 這是為了確保本機存放區中的所有資料表和關聯性都保持一致。

    此外還有 `PullAsync()` 的多載，可指定查詢以篩選儲存在用戶端上的資料。 如果未傳遞查詢，`PullAsync()` 將會在對應的資料表 (或查詢) 中提取所有資料列。 您可以傳遞查詢，只篩選應用程式需要同步處理的變更。

* 若要啟用增量同步處理，請將查詢識別碼傳遞至 `PullAsync()`。 查詢識別碼用來儲存上次提取作業所產生的上次更新時間戳記。 對您應用程式中的每個邏輯查詢而言，查詢識別碼應該是唯一的描述性字串。 如果查詢有一個參數，則相同的參數值可以是查詢識別碼的一部分。

    例如，如果您篩選使用者識別碼，您的唯一查詢識別碼可能是：

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    如果您想選擇不要增量同步處理，則傳遞 `null` 做為查詢識別碼。 在此情況下，每次呼叫 `PullAsync` 時都會擷取所有的記錄可能會沒有效率。

* 您的應用程式應該定期呼叫 `IMobileServiceSyncTable.PurgeAsync()` 以清除本機存放區。


## 其他資源

* [Azure 行動應用程式中的離線資料同步處理]

* [Cloud Cover: Offline Sync in Azure Mobile Services] \ (附註 ︰ 的視訊會在行動服務，但是離線同步處理的運作方式類似的方式，在 Azure 行動應用程式)

* [Azure Friday：Azure 行動服務中離線啟用的應用程式]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Offline Data Sync in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md
[create a windows app]: ../app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921

[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
 
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/


