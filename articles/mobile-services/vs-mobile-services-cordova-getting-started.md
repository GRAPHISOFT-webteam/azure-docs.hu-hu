<properties
    pageTitle="開始使用 Cordova 行動服務專案 (Visual Studio 連接服務)"
    description="說明在使用 Visual Studio 連接服務將 Cordova 專案連接至 Azure 行動服務後，您可以採用的第一個步驟。"
    services="mobile-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/17/2015"
    ms.author="tarcher"/>


# 開始使用行動服務 (Cordova 專案)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
> - 
> - 

## 第一個步驟

依照這些範例中之程式碼執行的第一個步驟取決於您要連線的行動服務類型。

- 對於 JavaScript 後端行動服務，請建立名為 TodoItem 的資料表。  輸入 "TodoItem" 做為資料表名稱。

- 若要使用 .NET 後端行動服務，在 Visual Studio 為您建立的預設專案範本中已有 TodoItem 資料表，但您必須將它發行到 Azure。 若要發行它，請開啟行動服務專案的內容功能表，然後選擇 [發行 Web]****。 接受預設值，然後選擇 [發行]**** 按鈕。



## 建立資料表的參考

下列程式碼可取得資料表的參考，而該資料表包含 TodoItem 的資料，可在後續操作中用來讀取和更新資料表。 建立行動服務時會自動建立 TodoItem 資料表。

    var todoTable = mobileServiceClient.getTable('TodoItem');

資料表的權限必須設為「具有應用程式金鑰的任何人」****，這些範例才能運作。 稍後您可以設定驗證。

## 將項目加入資料表

將新的項目插入至資料表。 自動會建立 id (字串類型的 GUID) 作為新資料列的主要索引鍵。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }
    
    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

## 讀取或查詢資料表

下列程式碼會查詢資料表的所有項目，並依文字欄位排序。 您可以在成功處理常式中新增程式碼來處理查詢結果。 在此案例中是更新項目的本機陣列。

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

您可以使用 where 方法來修改查詢。 以下範例是濾除已完成的項目。

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });



## 更新資料表項目

更新資料表中的資料列。 在此程式碼中，當行動服務回應時，項目就從清單中移除。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

## 刪除資料表項目

使用 **del** 方法刪除資料表中的資料列。

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });







