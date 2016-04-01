<properties 
    pageTitle="在使用 Visual Studio 連接服務加入 Azure 行動服務之後，開始使用 Javascript Mobile App | Microsoft Azure " 
    description="如何在 Visual Studio 中的 JavaScript 專案開始使用行動服務" 
    services="mobile-services" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="JavaScript" 
    ms.topic="article" 
    ms.date="09/17/2015" 
    ms.author="tarcher"/>

# 在使用 Visual Studio 連接服務加入 Azure 行動服務之後，開始使用 Javascript Mobile App

> [AZURE.SELECTOR]
> - [開始使用](vs-mobile-services-javascript-getting-started.md)
> - [發生什麼情形](vs-mobile-services-javascript-what-happened.md)

依照這些範例中之程式碼執行的第一個步驟取決於您要連線的行動服務類型。

 - 對於 JavaScript 後端行動服務，請建立名為 TodoItem 的資料表。  若要建立資料表，尋找 [Azure] 節點下的行動服務在伺服器總管] 中，以滑鼠右鍵按一下行動服務的節點，以開啟內容功能表，然後選擇 [ **Create Table**。 輸入 "TodoItem" 做為資料表名稱。

 - 若使用 .NET 後端行動服務，則 Visual Studio 為您建立的預設專案範本中已有 TodoItem 資料表，但您必須將它發行到 Azure。 若要發行它，請在 [方案總管中開啟行動服務專案的內容功能表，然後選擇 **發行 Web**。 接受預設值，然後選擇 **發行** ] 按鈕。

##取得資料表的參考

用戶端物件已加入至專案。  它的名稱就是行動服務的名稱再加上 "Client"。 下列程式碼可取得資料表的參考，而該資料表包含 TodoItem 的資料，可在後續操作中用來讀取和更新資料表。

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

##新增項目 

將新的項目插入至資料表。 自動會建立 id (字串類型的 GUID) 作為新資料列的主要索引鍵。 請勿變更 id 欄的類型，因為行動服務基礎結構會用到它。

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##讀取/查詢資料表

下列程式碼會查詢資料表的所有項目、更新本機集合，然後將結果繫結至 UI 元素 listItems。

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

您可以使用 where 方法來修改查詢。 以下範例是濾除已完成的項目。

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

如需您可以使用的查詢範例，請參閱 [查詢物件](http://msdn.microsoft.com/library/azure/jj613353.aspx)。

##更新實體

更新資料表中的資料列。 在此範例中， *todoItem* 是更新的項目，以及 *項目* 行動服務所傳回的是相同的項目。 當行動服務回應時，在本機 todoItems 清單中使用更新的項目 [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx) 方法。 呼叫 **完成** 方法傳回 [承諾](https://msdn.microsoft.com/library/dn802826.aspx) 物件，以取得所插入物件的複本，並處理任何錯誤。

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####刪除實體

刪除資料表中的資料列。 呼叫 [完成]() 方法傳回 [承諾](https://msdn.microsoft.com/library/dn802826.aspx) 物件，以取得所插入物件的複本，並處理任何錯誤。

    todoTable.del(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }



[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/) 


