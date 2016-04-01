<properties
    pageTitle="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
    description="如何使用適用於 Azure Mobile Apps 的 iOS SDK"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="11/30/2015"
    ms.author="krisragh"/>

# 如何使用適用於 Azure Mobile Apps 的 iOS 用戶端程式庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]
&nbsp; 
 
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本指南說明如何使用最新版執行一般案例 [Azure 行動應用程式 iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)。 如果您不熟悉 Azure 行動應用程式，先完成 [Azure Mobile Apps Quick Start] 建立後端，建立資料表，並下載預先建置的 iOS Xcode 專案。 在本指南中，我們會著重於用戶端 iOS SDK。 若要深入了解.NET 伺服器端 SDK 後端，請參閱 [使用.NET 後端](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## 參考文件

IOS 用戶端 SDK 參考文件位於此處 ︰ [Azure 行動應用程式 iOS 用戶端參考](http://azure.github.io/azure-mobile-services/iOS/v3/)。

##<a name="Setup"></a>設定和必要條件

本指南假設您已建立包含資料表的後端。 本指南假設資料表的結構描述與這些教學課程中的資料表相同。 本指南也假設您在程式碼中，參考了 `WindowsAzureMobileServices.framework` 並匯入了 `WindowsAzureMobileServices/WindowsAzureMobileServices.h`。

##<a name="create-client"></a>作法：建立用戶端

若要在專案中存取 Azure Mobile Apps 後端，請建立 `MSClient`。 以應用程式 URL 取代 `AppUrl`。 您可以將 `gatewayURLString` 和 `applicationKey` 留白。 如果您設定驗證的閘道器，請將 `gatewayURLString` 填入閘道器 URL。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>作法：建立資料表參考

若要存取或更新資料，請建立後端資料表的參考。 將 `TodoItem` 取代為您的資料表名稱。

```
    MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>作法：查詢資料

若要建立資料庫查詢，請查詢 `MSTable` 物件。 下列查詢會取得 `TodoItem` 中的所有項目並記錄每個項目的文字。

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

##<a name="filtering"></a>作法：篩選傳回的資料

若要篩選結果，有許多可用的選項。

若要使用述詞篩選，請使用 `NSPredicate` 和 `readWithPredicate`。 下列篩選器傳回的資料只尋找未完成的待辦事項。

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

##<a name="query-object"></a>作法：使用 MSQuery

若要執行複雜的查詢 (包括排序和分頁)，請使用述詞直接建立 `MSQuery` 物件：

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` 可讓您控制下列幾種查詢行為。 執行 `MSQuery` 查詢藉由呼叫 `readWithCompletion` 它下, 一個範例所示。
* 指定結果的順序
* 限制要傳回的欄位
* 限制要傳回的記錄數
* 指定回應中的總計數
* 在要求中指定自訂查詢字串參數
* 套用其他函式


## <a name="sorting"></a>做法：使用 MSQuery 排序資料

我們來看一下範例如何排序結果。 若要先按照 `text` 欄位遞增排序，然後按照 `completion` 欄位遞減排序，請如下叫用 `MSQuery`：

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```


## <a name="selecting"></a><a name="parameters"></a>作法：使用 MSQuery 限制欄位和展開查詢字串參數

若要限制查詢中傳回的欄位，指定在欄位名稱 **selectFields** 屬性。 這僅會傳回文字和已完成欄位：

```
    query.selectFields = @[@"text", @"completed"];
```

若要在伺服器要求中包含額外的查詢字串參數 (例如有某個自訂的伺服器端指令碼使用這些參數)，請如下填入 `query.parameters`：

```
    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };
```

##<a name="inserting"></a>作法：插入資料

若要插入新的資料表資料列，請建立新的 `NSDictionary` 並叫用 `table insert`。 行動服務會自動產生新資料行是根據 `NSDictionary` 如果 [Dynamic Schema] 未停用。

如果未提供 `id`，則後端會自動產生新的唯一識別碼。 提供您自己的 `id`，以使用電子郵件地址、使用者名稱或您自己自訂的值作為識別碼。 提供您自己的識別碼可以讓聯結和商務導向的資料庫邏輯變得更容易。

```
    NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
    [self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified
        // data compared to what was passed to the server.
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
        }
    }];
```

##<a name="modifying"></a>作法：修改資料

若要更新現有的資料列，請修改項目並呼叫 `update`：

```
    NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
    [newItem setValue:@"Updated text" forKey:@"text"];
    [self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

或者，提供資料列識別碼和更新的欄位：

```
    [self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

進行更新時，至少必須設定 `id` 屬性。

##<a name="deleting"></a>作法：刪除資料

若要刪除項目，請叫用 `delete` 搭配項目：

```
    [self.table delete:item completion:^(id itemId, NSError *error) {
        // Handle error or perform additional logic as needed
    }];
```

或者，提供資料列識別碼來進行刪除：

```
    [self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
        // Handle error or perform additional logic as needed
    }];   
```

進行刪除時，至少必須設定 `id` 屬性。

##<a name="templates"></a>作法：註冊推送範本以傳送跨平台通知

若要註冊的範本，只要傳遞範本與您 **client.push registerDeviceToken** 用戶端應用程式中的方法。

        [client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
            ...
        }];

您的範本類型將為 NSDictionary，並且可能包含多個下列格式的範本：

        NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };

請注意，所有的標記都將因安全性而移除。 若要將標記加入至安裝或安裝的範本，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)。

若要傳送通知，利用這些註冊的範本，使用 [通知中樞 Api](https://msdn.microsoft.com/library/azure/dn495101.aspx)

##<a name="errors"></a>作法：處理錯誤

呼叫行動服務時，completion 區塊會包含 `NSError *error` 參數。 發生錯誤時，此參數便會傳回非 Nil。 您應檢查程式碼中的此參數，並視需要處理錯誤。

檔案 [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) 會定義常數 `MSErrorResponseKey`, ，`MSErrorRequestKey`, ，和 `MSErrorServerItemKey` 以取得更多有關錯誤的資料。 此外，檔案也定義每個錯誤代碼的常數。 如需如何使用這些常數的範例，請參閱 [Conflict-Handler] 其使用量的 `MSErrorServerItemKey` 和 `MSErrorPreconditionFailed`。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps Quick Start]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamic Schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling


