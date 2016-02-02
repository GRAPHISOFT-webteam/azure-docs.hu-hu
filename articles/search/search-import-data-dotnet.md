<properties
    pageTitle="將資料匯入使用.NET 的 Azure 搜尋服務 |Microsoft Azure |託管的雲端搜尋服務"
    description="如何使用 .NET SDK 或 .NET 程式庫將資料上傳至 Azure 搜尋服務中的索引"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/09/2015"
    ms.author="heidist"/>


# 使用 .NET 將資料匯入至 Azure 搜尋服務

> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)


本文將說明如何以填入索引使用 [Azure 搜尋服務.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)。 下列內容是子集 [如何使用.NET 應用程式的 Azure 搜尋服務](search-howto-dotnet-sdk.md)。 請參閱上層文章中的端對端步驟。

將資料匯入到索引的先決元件包括具有先前建立的索引。

假設名為 'hotels' 的索引，您可以建構匯入資料，如下所示的方法。

下一步 `Main` 是填入新建立的索引。 執行方法如下：

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };
    
        try
        {
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }
    
        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

此方法分四個部分。 第一個建立的陣列 `旅館` 將做為我們上傳至索引的輸入資料的物件。 為簡單起見，此資料採硬式編碼。 在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二個部分會建立 `IndexAction` 每個 `旅館`, ，然後在新一起群組那些 `IndexBatch`。 批次然後上傳至 Azure 搜尋服務索引 `Documents.Index` 方法。
> [AZURE.NOTE] 在此範例中，我們只上傳文件。 如果您想要變更合併至現有的文件或刪除文件，您可以建立 `IndexAction` 對應 `IndexActionType`。 我們不需要指定 `IndexActionType` 在此範例中因為預設值是 `上載`。

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果您的 Azure 搜尋服務失敗的批次中的文件的一些索引 `IndexBatchException` 所擲回 `Documents.Index`。 如果您在服務負載過重時編制文件的索引，就會發生此情況。 **我們強烈建議您在程式碼中明確處理此情況。**您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

最後，方法會延遲兩秒。 您的 Azure 搜尋服務中會發生非同步索引編製，因此範例應用程式必須稍待一會，才能確定文件已準備好可供搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。





