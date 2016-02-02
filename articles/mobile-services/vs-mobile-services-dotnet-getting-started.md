<properties
    pageTitle="開始使用 Visual Studio.NET 行動服務專案 (連接服務) | Microsoft Azure"
    description="如何在 Visual Studio .NET 專案中開始使用 Azure 行動服務"
    services="mobile-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/17/2015"
    ms.author="tarcher"/>


# 開始使用行動服務 (.NET 專案)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
> - 
> - 

依照這些範例中之程式碼執行的第一個步驟取決於您要連線的行動服務類型。

- 對於 JavaScript 後端行動服務，請建立名為 TodoItem 的資料表。  輸入 "TodoItem" 做為資料表名稱。

- 若要使用 .NET 後端行動服務，在 Visual Studio 為您建立的預設專案範本中已有 TodoItem 資料表，但您必須將它發行到 Azure。 若要發行它，請開啟行動服務專案的內容功能表，然後選擇 [發行 Web]****。 接受預設值，然後選擇 [發行]**** 按鈕。

##### 取得資料表的參考

您需要 TodoItem 類別，並設定屬性來解譯行動服務為了回應查詢而傳送的 JSON。

    public class TodoItem
    {
        public string Id { get; set; }
    
        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }
    
        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }
    
    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

資料表的權限必須設為「具有應用程式金鑰的任何人」****，此程式碼才能運作。 如果您變更權限來保護行動服務，則需要加入使用者驗證支援。

##### 加入資料表項目

將新的項目插入至資料表。

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

##### 讀取或查詢資料表

下列程式碼會查詢資料表的所有項目。 請注意，只會傳回第一頁的資料，依預設有 50 個項目。 您可以傳遞想要的頁面大小 (這是選用參數)。

    List<TodoItem> items;
    try
    {
        // Query that returns all items.
        items = await todoTable.ToListAsync();
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

##### 更新資料表項目

更新資料表中的資料列。 參數項目是要更新的 TodoItem 物件。

    await todoTable.UpdateAsync(item);

##### 刪除資料表項目

刪除資料庫中的資料列。 參數項目是要刪除的 TodoItem 物件。

    await todoTable.DeleteAsync(item);







