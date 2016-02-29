<properties 
    pageTitle="開始使用 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務" 
    description="開始使用 Azure 搜尋服務 (Microsoft Azure 上的雲端託管搜尋服務)。" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""
    tags="azure-portal"/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="heidist"/>

# 開始使用 Azure Search

Microsoft Azure 搜尋服務是託管的雲端搜尋服務，可讓您將搜尋功能內嵌到自訂用程式。 它可為您的資料提供搜尋引擎和儲存體，並讓您透過 .NET 檔案庫或 REST API 加以存取和管理。

本文章可讓您開始使用 Azure 搜尋服務 REST API。 

.NET 開發人員的替代方式就是使用 Azure 搜尋服務 .NET SDK。 請參閱 [開始使用 Azure 搜尋服務.net](search-get-started-dotnet.md) 或 [如何使用 Azure 搜尋服務.NET SDK](search-howto-dotnet-sdk.md) 如需詳細資訊。


> [AZURE.NOTE] 完成本教學課程，您需要 [Azure 訂用帳戶](../includes/free-trial-note.md)。 如果您不準備要註冊試用訂閱，您可以略過本教學課程中，選擇 [Try Azure App Service](https://tryappservice.azure.com/) 改。 這個替代選項免費提供您 Azure 搜尋服務與 ASP.NET Web 應用程式 - 每個工作階段一小時 - 不需有訂用帳戶。
 
<a id="sub-1"></a>
## 開始使用免費服務

身為管理員，當您選擇共用服務時，可以在現有訂用帳戶中免費新增搜尋服務，或是如果您選擇的是專用資源，則可以用較低的費率新增搜尋服務。 

訂戶可自動且免費地存取共用的多租用戶搜尋服務，此服務可用於學習、概念證明測試或是小型開發搜尋專案。 請使用下列步驟註冊免費版本。

1. 登入 [Azure 傳統入口網站](https://portal.azure.com) 使用現有的訂閱。 請注意，此 URL 會引導您前往入口網站。 您必須使用入口網站。 

2. 按一下 [ **新增** 頁面的頂端。
 
    ![][6]

3. 按一下 [ **資料 + 儲存體** | **搜尋**。

    - 輸入要在服務 URL 中使用的小寫服務名稱，請避免使用空格，並且不要超過 15 個字元的字串限制。

    - 按一下箭號在 **定價層** 來選擇收費選項。 選擇 **免費** 然後按一下 [ **選取** 頁面的底部。 免費版本所提供的容量，已足夠用來試驗教學課程以及撰寫概念證明程式碼，但並不適用於生產應用程式。 

    - 按一下箭號在 **資源群組** 可以挑選現有的群組，或建立一個新。 資源群組是用於一般用途之服務和資源的容器。 例如，如果您以 Azure 搜尋服務、Azure 網站和 Azure BLOB 儲存體為基礎建立自訂搜尋應用程式，那麼您可以建立資源群組，來將這些服務一起放在入口網站的管理頁面中。

    - 按一下箭號在 **訂閱** 如果有多個訂閱，而且您想要針對此搜尋服務使用不同的訂閱。

    - 按一下箭號在 **位置** 選擇資料中心的區域。 在此預覽中，您可以選擇美國西部、美國東部、北歐及東南亞。 之後有其他區域上線時，請選擇您要建立服務的區域。 公開預覽並不支援將資源分散在多個資料中心的設定。

4. 按一下 [ **建立** 佈建服務。 請注意， **建立** 填入所有必要值之後，才會啟用。 

幾分鐘後，服務即建立完成。 您可以回到組態設定以取得 URL 或 API 金鑰。 若想要連接至 [搜尋服務]，您必須同時擁有 URL 和 API 金鑰以驗證呼叫。 下面會說明如何快速找到這些值：

14. 移至 **首頁** 開啟儀表板。 按一下搜尋服務以開啟服務儀表板。 

    ![][13]

15. 在服務儀表板，您會看到磚 **屬性** 和 **金鑰**, ，以及在一眼就能清楚知道資源使用量的使用量資訊。 

繼續 [測試服務作業](#sub-3) 如需如何使用這些值與服務連接的指示。

<a id="sub-2"></a>
## 升級至標準搜尋

標準搜尋會為您取得 Azure 資料中心內只有您可使用的專用資源。 搜尋的工作負載需要用到儲存體和服務複本。 當您註冊標準搜尋時，您可以將服務設定最佳化，以使用更多對您的狀況來說最重要的任何資源。

具有專用資源可以帶給您更大的規模和更好的效能，但不會帶來其他功能。 共用和標準搜尋提供的功能皆相同。

若要使用標準搜尋，請建立新的搜尋服務，並選擇 [標準] 價格層。 請注意，升級指的並不是將免費版本就地升級。 切換至標準版 (透過其調整規模的潛能) 需要用到新的服務。 您將需要重新載入搜尋應用程式所使用的索引和文件。

設定專用資源可能需要一些時間 (15分鐘或更久)。 

**步驟 1 - 建立價格層設為標準的新服務**

1. 登入 [Azure 傳統入口網站](https://portal.azure.com) 使用現有的訂閱。 

2. 按一下 [ **新增** 頁面的底部。

4. 從組件庫中，按一下 [ **資料 + 儲存體** | **搜尋**。

7. 填寫服務組態設定，然後再按一下 **建立**。

8. 在 **定價層** 來選擇收費選項。 選擇 **標準** 然後按一下 [ **選取** 頁面的底部。

**步驟 2 - 依據規模需求調整搜尋單位**

每個標準搜尋一開始會有一個複本和資料分割，但可輕鬆地在更高的資源階層中重新調整。

1.  服務建立之後，請回到服務儀表板，按一下 [ **延展** 並排顯示。

2.  使用滑桿新增副本和 (或) 資料分割。 

額外的複本和資料分割會以搜尋單位計費。 當您新增資源時，頁面上會顯示要支援任何特定資源設定所需要的搜尋單位總數。 

您可以檢查 [定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkID=509792) 以取得每單位的計費資訊。 請參閱 [限制和條件約束](search-limits-quotas-capacity.md) 以協助您決定如何設定資料分割和複本組合。

 ![][15]

<a id="sub-3"></a>
## 測試服務作業

設定搜尋服務的最後一個步驟是，確認您的服務可否從用戶端應用程式上操作和存取。 此程序使用 Fiddler，您可以 [透過 Telerik 免費下載](http://www.telerik.com/fiddler), ，來發出 HTTP 要求和檢視回應。 藉由使用 Fiddler，您無須撰寫任何程式碼就可立即測試 API。 

下列程序在共用及標準搜尋中皆適用。 在以下步驟中，您將建立索引、上傳文件、查詢索引，然後查詢系統以取得服務資訊。

### 建立索引

1. 啟動 Fiddler。 在 [檔案] 功能表中，關閉 **擷取流量** 隱藏與目前工作無關的 HTTP 活動。 在 [Composer (編寫器)] 索引標籤上，您可以制定如下所示的要求： 

    ![][16]

2. 選取 **放**。

3. 輸入可指出服務 URL (可在 [Properties (屬性)] 頁面上找到)、要求屬性和 API 版本的完整 URL。 請留意以下幾點：
   + 使用 HTTPS 作為首碼
   + 要求屬性為 "/indexes/hotels"。 這可告知「搜尋」建立名為 'hotels' 的索引。
   + API 版本為小寫，並指定為 "?api-version=2015-02-28"。 API 版本十分重要，因為 Azure 搜尋服務會定期部署更新。 在極少數情況下，更新服務可能會對 API 造成中斷變更。 使用 API 版本時，您可以先繼續使用現有版本，方便時再升級到較新的版本。

    完整 URL 應該會類似下列範例：

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
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.  按一下 [ **執行**。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 201 的回應，指出已成功建立索引。 

如果您收到 HTTP 504，請確認 URL 所指定的是 HTTPS。 如果您看見 HTTP 400 或 404，請查看要求本文以確認並沒有「複製-貼上」錯誤。 HTTP 403 通常表示 API 金鑰有問題 (可能是金鑰無效或是用來指定 API 金鑰的語法有問題)。

### 載入文件

在 [Composer (編寫器)] 索引標籤上，張貼文件的要求看起來會像下面這樣。 要求本文包含 4 間飯店的搜尋資料。

   ![][17]

1. 選取 **POST**。

2.  輸入以 HTTPS 開頭的 URL，並於後面依序加上服務 URL 和 "/indexes/<'indexname'>/docs/index?api-version=2015-02-28"。 完整 URL 應該會類似下列範例：

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

8.  按一下 [ **執行**。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 200 的回應。 這表示已成功建立文件。 如果您收到 207，表示至少有一個文件上傳失敗。 如果您收到 404，則表示要求的標頭或本文有語法錯誤。

### 查詢索引

現在已載入索引和文件，您可以對其發出查詢。  在 [Composer (編寫器)] 索引標籤上，查詢服務的 GET 命令看起來會像下面這樣：

   ![][18]

1.  選取 **取得**。

2.  輸入以 HTTPS 開頭，並於後面依序加上服務 URL、"/indexes/<'indexname'>/docs?" 和查詢參數的完整 URL。 舉例來說，使用下列 URL，並以服務的有效值取代範例主機名稱。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    此查詢會搜尋「motel」一字，並擷取評等的 Facet 類別。

3.  要求標頭應與之前的相同。 請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

回應碼應為 200，而回應的輸出看起來應該會類似下圖。
 
   ![][19]

下列範例查詢是從 [搜尋索引作業 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/dn798927.aspx) MSDN 上。 此主題中有許多範例查詢包含空格，這在 Fiddler 中是不允許的。 因此，請先使用 + 字元取代空格，再貼到查詢字串中，然後再於 Fiddler 中嘗試該查詢： 

**取代空格之前：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**以 + 取代空格之後：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### 查詢系統

您也可以查詢系統以取得文件計數和儲存體用量。 在 [Composer (編寫器)] 索引標籤上，您的要求看起來會像下面這樣，而回應會傳回文件計數和空間的使用量。

   ![][20]

1.  選取 **取得**。

2.  輸入含有服務 URL 的 URL，並於後面加上 "/indexes/hotels/stats?api-version=2015-02-28"：

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28 

3.  指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  讓要求本文保持空白。

5.  按一下 [ **執行**。 您應該會在工作階段清單中看到 HTTP 200 的狀態碼。 選取為命令張貼的項目。

6.  按一下 [Inspectors (檢測器)] 索引標籤 | [標頭]，然後選取 [JSON] 格式。 您應該會看到文件計數和儲存體大小 (以 KB計算)。

    ![][21]

<a id="sub-4"></a>
## 瀏覽搜尋服務儀表板

如果您需要複習如何尋找設定頁面，請依循以下步驟尋找服務儀表板。

1.  登入 [Azure 傳統入口網站](https://portal.azure.com) 使用現有的訂閱。 
2.  按一下 [ **首頁** 然後按一下 [搜尋服務的磚。

    ![][22]

4.  按一下磚會開啟服務儀表板。 請注意， **啟動**, ，**停止**, ，和 **刪除** 命令皆在頂端。 

5.  請注意，服務 URL 是在頁面頂端附近。 您需要此 URL 來連接到 Azure 搜尋服務。
    
7.  按一下 [ **金鑰** 圖示來檢視 api 金鑰。 您需要系統管理金鑰來驗證服務。 您可以使用主要或次要金鑰。 (選擇性) 您可以建立查詢金鑰以進行服務的唯讀存取。


<!--Next steps and links -->
<a id="next-steps"></a>
## 立即試用

準備好前往下一個步驟了嗎？ 下列連結會引導您前往其他資料頁面，這些頁面會說明如何建置和管理使用 Azure 搜尋服務的搜尋應用程式。

- [建立 Azure Search GeoSearch 範例](search-create-geospatial.md)

- [在 Microsoft Azure 中管理搜尋方案](search-manage.md) 

- [何謂 Azure 搜尋服務？](search-what-is-azure-search.md)

- [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/dn798935.aspx)

- [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [第 9 頻道影片：Azure 搜尋服務簡介](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [第 9 頻道影片：Azure 搜尋服務和地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [雲端報導第 152 集：在 Azure 搜尋服務中產生索引](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

