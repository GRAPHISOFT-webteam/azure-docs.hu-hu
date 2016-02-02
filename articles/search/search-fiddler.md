<properties
    pageTitle="如何使用 Fiddler 評估及測試 Azure 搜尋服務 REST API | Microsoft Azure | 雲端託管搜尋服務"
    description="使用 Fiddler 以無程式碼的方式驗證 Azure 搜尋服務的可用性，並試用 REST API。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/10/2015"
    ms.author="heidist"/>


# 使用 Fiddler 評估及測試 Azure 搜尋 REST API

> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)


這篇文章說明如何使用 Fiddler，您可以 [透過 Telerik 免費下載](http://www.telerik.com/fiddler), ，來發出 HTTP 要求，並檢視使用 Azure 搜尋服務 REST API，而不需要撰寫任何程式碼的回應。 Azure 搜尋服務是 Microsoft Azure 上完整管理的雲端託管搜尋服務，可輕鬆地透過 .NET 和 REST API 程式化。 Azure 搜尋服務 REST Api 記載 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

在下列步驟中，您將建立索引、上傳文件、查詢索引，然後查詢系統以取得服務資訊。

若要完成這些步驟，您必須在 Azure 搜尋服務和 `api 金鑰`。 請參閱 [入口網站中建立 Azure 搜尋服務](search-create-service-portal.md) 如需如何開始著手。

## 建立索引

1. 啟動 Fiddler。 在 [檔案]**** 功能表上，關閉 [擷取流量]**** 以隱藏與目前工作無關的 HTTP 活動。

3. 在 [編寫器]**** 索引標籤上，您可以制訂如下列螢幕擷取畫面所示的要求：

    ![][1]

2. 選取 [PUT]****。

3. 輸入可指定服務 URL、要求屬性和 API 版本的 URL。 請留意以下幾點：
   + 使用 HTTPS 做為首碼。
   + 要求屬性為 "/indexes/hotels"。 這可告知「搜尋」建立名為 'hotels' 的索引。
   + API 版本為小寫，並指定為 "?api-version=2015-02-28"。 API 版本十分重要，因為 Azure 搜尋服務會定期部署更新。 在極少數情況下，更新服務可能會對 API 造成中斷變更。 使用 API 版本時，您可以先繼續使用現有版本，方便時再升級到較新的版本。

    完整 URL 應該會類似下列範例。

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28


4.  指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.  在 [Request Body (要求本文)] 中，貼上構成索引定義的欄位。

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false,},
          {"name": "hotelName", "type": "Edm.String"},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ]
        }

6.  按一下 [Execute (執行)]****。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 201 的回應，指出已成功建立索引。

如果您收到 HTTP 504，請確認 URL 所指定的是 HTTPS。 如果您看見 HTTP 400 或 404，請查看要求本文以確認並沒有「複製-貼上」錯誤。 HTTP 403 通常表示 API 金鑰有問題 (可能是金鑰無效或是用來指定 API 金鑰的語法有問題)。

## 載入文件

在 [編寫器]**** 索引標籤上，張貼文件的要求會類似如下。 要求本文包含 4 間飯店的搜尋資料。

   ![][2]

1. 選取 [POST]****。

2.  輸入的 URL 以 HTTPS 服務 URL、 開頭，後面加上"/indexes/<<'indexname'>/docs/index? api 版本 = 2015年-02-28"。完整 URL 應該會類似下列範例。

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  要求標頭應與之前的相同。 請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  [Request Body (要求本文)] 包含 4 個要新增到飯店索引的文件。

        {
        "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
          },
          {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
            "@search.action": "upload",
            "hotelId": "3",
            "baseRate": 279.99,
            "description": "Surprisingly expensive",
            "hotelName": "Dew Drop Inn",
            "category": "Bed and Breakfast",
            "tags": ["charming", "quaint"],
            "parkingIncluded": true,
            "smokingAllowed": false,
            "lastRenovationDate": null,
            "rating": 4,
            "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
            "@search.action": "upload",
            "hotelId": "4",
            "baseRate": 220.00,
            "description": "This could be the one",
            "hotelName": "A Hotel for Everyone",
            "category": "Basic hotel",
            "tags": ["pool", "wifi"],
            "parkingIncluded": true,
            "smokingAllowed": false,
            "lastRenovationDate": null,
            "rating": 4,
            "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.  按一下 [Execute (執行)]****。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 200 的回應。 這表示已成功建立文件。 如果您收到 207，表示至少有一個文件上傳失敗。 如果您收到 404，則表示要求的標頭或本文有語法錯誤。

## 查詢索引

現在已載入索引和文件，您可以對其發出查詢。 在 [編寫器]**** 索引標籤上，查詢服務的 **GET** 命令會類似下列螢幕擷取畫面。

   ![][3]

1.  選取 [GET]****。

2.  輸入的 URL 以 HTTPS 服務 URL、 開頭，後面加上"/indexes/<<'indexname'>/docs?"，後面接著查詢參數。舉例來說，使用下列 URL，並以服務的有效值取代範例主機名稱。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    此查詢會搜尋「motel」一字，並擷取評等的 Facet 類別。

3.  要求標頭應與之前的相同。 請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444


回應碼應為 200，而回應輸出應該會類似下列螢幕擷取畫面。

   ![][4]

下列範例查詢是從 [搜尋索引作業 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/dn798927.aspx) MSDN 上。 此主題中有許多範例查詢包含空格，這在 Fiddler 中是不允許的。 因此，請先使用 + 字元取代空格，再貼到查詢字串中，然後再於 Fiddler 中嘗試該查詢：

**取代空格之前：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**以 + 取代空格之後：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## 查詢系統

您也可以查詢系統以取得文件計數和儲存體用量。 在 [編寫器]**** 索引標籤上，您的要求會類似如下，且回應會傳回文件計數和空間使用量。

 ![][5]

1.  選取 [GET]****。

2.  輸入含有服務 URL 的 URL，並於後面加上 "/indexes/hotels/stats?api-version=2015-02-28"：

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  讓要求本文保持空白。

5.  按一下 [Execute (執行)]****。 您應該會在工作階段清單中看到 HTTP 200 的狀態碼。 選取為命令張貼的項目。

6.  依序按一下 [檢測器]**** 索引標籤和 [標頭]**** 索引標籤，然後選取 JSON 格式。 您應該會看到文件計數和儲存體大小 (以 KB計算)。

## 後續步驟

以下連結提供一些額外資訊，說明如何以無程式碼的方式管理及使用 Azure 搜尋服務。

-  [管理 azure 搜尋服務](search-manage.md)
-  [如何使用 Azure 搜尋服務使用 Chrome Postman](search-chrome-postman.md)



[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png 
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png 
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png 
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png 
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png 

