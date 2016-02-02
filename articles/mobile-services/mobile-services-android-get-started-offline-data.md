<properties
    pageTitle="在 Android 行動服務應用程式中加入離線資料同步 | Microsoft Azure"
    description="了解如何使用 Azure 行動服務快取和同步 Android 應用程式中的離線資料"
    documentationCenter="android"
    authors="RickSaling"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="12/06/2015"
    ms.author="ricksal"/>


# 在 Android 行動服務應用程式中加入離線資料同步

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## 摘要

行動應用程式可能會在移至沒有服務的區域，或因為網路問題而失去網路連線。 例如，用於遠端營造地點的營造產業應用程式可能需要輸入稍後會同步至 Azure 的排程資料。 使用 Azure 行動服務離線同步，您可以在失去許多行動應用程式所需的網路連線時持續作業。 透過離線同步，您可以使用 Azure SQL Server 資料表的本機副本，並定期重新同步處理兩個版本。

在本教學課程中，您將更新應用程式的 [行動服務快速入門教學課程] 以啟用離線同步處理，並加入離線資料，以測試應用程式同步處理這些項目到線上資料庫，並確認 Azure 傳統入口網站中的變更。

不論您是離線或連線狀態，對資料進行多項變更隨時可能會發生衝突。 未來的教學課程將探索同步衝突處理，供您選擇要接受哪個版本的變更。 在此教學課程中，我們假設沒有同步衝突，而您對現有資料進行的變更將直接套用到 Azure SQL Server。


## 若要開始，您需要：

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## 更新應用程式以支援離線同步

利用離線同步讀取和寫入*同步資料表* (使用 *IMobileServiceSyncTable* 介面)，這是您裝置上 **SQLite** 資料庫的一部分。

若要推送和提取裝置與 Azure 行動服務之間的變更，您可使用「同步處理內容」**(*MobileServiceClient.SyncContext*)，這是您用來在本機儲存資料的本機資料庫所初始化。

1. 在 *AndroidManifest.xml* 檔案中加入此程式碼，以新增檢查網路連接的權限：

        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

2. 將下列 **import** 陳述式加入 *ToDoActivity.java*：

     import java.util.Map;
    
     import android.widget.Toast;
    
     import com.microsoft.windowsazure.mobileservices.table.query.Query;
     import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
     import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
     import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
     import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. 頂端附近的 `ToDoActivity` 類別中，變更的宣告 `mToDoTable` 變數從 `MobileServiceTable < ToDoItem >` 類別 `MobileServiceSyncTable < ToDoItem >` 類別。

         private MobileServiceSyncTable<ToDoItem> mToDoTable;

    這是您定義同步資料表的位置。

4. 在處理本機存放區中，初始化 `onCreate` 方法，在建立後新增下列程式碼行 `MobileServiceClient` 執行個體:

         // Saves the query which will be used for pulling data
         mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);
    
         SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
         SimpleSyncHandler handler = new SimpleSyncHandler();
         MobileServiceSyncContext syncContext = mClient.getSyncContext();
    
         Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
         tableDefinition.put("id", ColumnDataType.String);
         tableDefinition.put("text", ColumnDataType.String);
         tableDefinition.put("complete", ColumnDataType.Boolean);
         tableDefinition.put("__version", ColumnDataType.String);
    
         localStore.defineTable("ToDoItem", tableDefinition);
         syncContext.initialize(localStore, handler).get();
    
         // Get the Mobile Service Table instance to use
         mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. 遵循上述的程式碼，位於 `嘗試` 區塊中，加入額外 `攔截` 區塊之後 `MalformedURLException` 其中一個:

        } catch (Exception e) {
            Throwable t = e;
            while (t.getCause() != null) {
                    t = t.getCause();
                }
            createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
        }

6. 加入這個方法，以驗證網路連線：

        private boolean isNetworkAvailable() {
            ConnectivityManager connectivityManager
                    = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
            NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
            return activeNetworkInfo != null && activeNetworkInfo.isConnected();
        }

7. 加入這個方法，以同步處理本機 *SQL Light* 存放區與 Azure SQL Server：

     public void syncAsync(){
         if (isNetworkAvailable()) {
             new AsyncTask<Void, Void, Void>() {
    
                 @Override
                 protected Void doInBackground(Void... params) {
                     try {
                         mClient.getSyncContext().push().get();
                         mToDoTable.pull(mPullQuery).get();
    
                     } catch (Exception exception) {
                         createAndShowDialog(exception, "Error");
                     }
                     return null;
                 }
             }.execute();
         } else {
             Toast.makeText(this, "You are not online, re-sync later!" +
                     "", Toast.LENGTH_LONG).show();
         }
     }

8. 在 `onCreate` 方法，做為要最後下一步一行加入下列程式碼中，以滑鼠右鍵，再呼叫 `refreshItemsFromTable`:

            syncAsync();

    這會使裝置在啟動時與 Azure 資料表同步處理。 否則，您會顯示本機存放區的上次離線內容。

9. 更新中的程式碼 `refreshItemsFromTable` 方法來使用這項查詢 (第一行程式碼內 `嘗試` 區塊):

        final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. 在 `onOptionsItemSelected` 方法的內容取代 `如果` 這個程式碼區塊:

            syncAsync();
            refreshItemsFromTable();

    此程式碼會在您按右上角的 [重新整理]**** 按鈕時執行。 除了在啟動時同步處理以外，這是您將本機存放區同步至 Azure 的主要方式。 這促使了同步變更的批次處理且具有效率 (有鑑於從 Azure 提取是相當耗成本的作業)。 您也可以設計此應用程式，在每次變更同步處理，藉由新增呼叫 `syncAsync` 至 `addItem` 和 `checkItem` 方法，如果您的應用程式需要這。


## 測試應用程式

在本節中，您會在開啟 WiFi 的情況下測試行為，然後關閉 WiFi 以建立離線案例。

當您新增資料項目時，這些項目會存放在本機 SQL Light 存放區中，但直到您按 [重新整理]**** 按鈕時才會同步處理至行動服務。 對於何時需要同步處理資料，其他應用程式可能會有不同的需求，但是為了示範目的，本教學課程讓使用者明確要求。

當您按下該按鈕時，新的背景工作會啟動，並使用同步處理內容先推送對本機存放區做的所有變更，然後將所有變更的資料從 Azure 提取至本機資料表。


### 線上測試

讓我們測試下列案例。

1. 在您的裝置上增加一些新項目；
2. 確認項目不會顯示在入口網站中；
3. 接下來按 [重新整理]**** 並確認這些項目會接著顯示。
4. 在入口網站中變更或新增項目，然後按 [重新整理]**** 並確認變更顯示在您的裝置上。

### 離線測試

1. 讓裝置或模擬器處於「飛航模式」**。 這會建立離線案例。

2. 新增一些 *ToDo* 項目，或將某些項目標示為完成。 結束裝置或模擬器 (或強制關閉應用程式)，然後重新啟動。 請確認您的變更已保存在裝置上，因為它們會保留在本機 SQL Light 存放區中。

3. 檢視 Azure *TodoItem* 資料表的內容。 請確認新項目_尚未_ 同步處理到伺服器：

   - 為 JavaScript 後端，移至 Azure 傳統入口網站，然後按一下 [資料] 索引標籤來檢視內容 `TodoItem` 資料表。
   - 若為 .NET 後端，請使用 SQL 工具 (例如 *SQL Server Management Studio*) 或 REST 用戶端 (如 *Fiddler* 或 *Postman*) 檢視資料表內容。

4. 在裝置或模擬器中開啟 WiFi。 接著，按 [重新整理]**** 按鈕。

5. 在 Azure 傳統入口網站中，再次檢視 TodoItem 資料。 新的和變更的 TodoItems 現在應該會出現。


## 後續步驟

* [使用 ][soft delete]

## 其他資源

* [雲端報導: Azure 行動服務中的離線同步處理]

* [Azure Friday: Azure 行動服務中的離線應用程式] \ (附註: 示範適用於 Windows，但功能討論適用於所有平台)





[get the sample app]: #get-app 
[review the core data model]: #review-core-data 
[review the mobile services sync code]: #review-sync 
[change the sync behavior of the app]: #setup-sync 
[test the app]: #test-app 
[mobile services sample repository on github]: https://github.com/Azure/mobile-services-samples 
[get started with mobile services]: mobile-services-android-get-started.md 
[handling conflicts with offline support for mobile services]: mobile-services-android-handling-conflicts-offline-data.md 
[soft delete]: mobile-services-using-soft-delete.md 
[cloud cover: offline sync in azure mobile services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri 
[azure friday: offline-enabled apps in azure mobile services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/ 
[mobile services quick start tutorial]: mobile-services-android-get-started.md 

