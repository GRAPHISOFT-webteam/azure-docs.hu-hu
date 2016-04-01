<properties 
    pageTitle="使用 Azure 搜尋服務建立地理空間搜尋應用程式 | Microsoft Azure | 雲端託管搜尋服務" 
    description="使用 Bing 和 Azure 搜尋服務 (Microsoft Azure 上之託管的雲端搜尋服務) 建立地理空間搜尋應用程式。" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="heidist"/>

# 使用 Azure 搜尋服務建立地理空間搜尋應用程式

本教學課程示範如何使用 Azure 搜尋服務與 Bing 地圖將地理空間搜尋新增至 Web 應用程式。 透過地理搜尋功能，您可以找到某個地點特定距離範圍內的搜尋目標 (例如，尋找距離我目前位置 5 公理範圍內的所有餐廳)。 Azure 搜尋服務中的地理空間功能支援常用的地圖技術。 舉例來說，當您想要在不動產應用程式中使用多邊形來展示多邊形邊界內的待售屋，您可以透過 OData 或是我們的簡易搜尋語法輕鬆地辦到。

如需詳細的概觀，此影片 Channel 9 有關 [Azure 搜尋與地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

![][7]

為了建立應用程式，我們將使用 Bing 地圖服務來對應從 CSV 檔案載入的地理編碼地址，然後將得出的資料儲存到搜尋索引中。 

本教學課程以 [Azure 搜尋 – Adventure Works 示範](http://azuresearchadventureworksdemo.codeplex.com)。 如果您尚未看過這項示範，請從該示範開始了解如何建立索引並從 Web 應用程式呼叫 Azure 搜尋服務 API。  

<a id="sub-1"></a>
## 必要條件

+   Visual Studio 2012 或更高版本，並安裝 ASP.NET MVC 4 與 SQL Server。 如果您還沒有安裝的軟體，您可以下載免費的 Express 版 ︰ [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 和 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx)。
+   Azure 搜尋服務。 您需要搜尋服務名稱，以及系統管理金鑰。 請參閱 [入口網站中建立 Azure 搜尋服務](search-create-service-portal.md) 如需詳細資訊。
+   Bing 地圖服務與其存取金鑰。 下一節會提供說明
+   [CodePlex 上的 azure 搜尋 GeoSearch 範例](https://azuresearchgeospatial.codeplex.com/)。 在 [來源] 索引標籤中，按一下 [ **下載** 以取得解決方案的壓縮檔。 

    ![][12]

此解決方案內含兩個專案：

+   **StoreIndexer** 會建立 Azure 搜尋索引並載入資料。
+   **AdventureWorksWebGeo** 是查詢 Azure 搜尋索引的 mvc4 應用程式，並在 Bing 地圖上顯示商店位置。

[AZURE。包含 [需要 Azure 帳戶才能完成此教學課程 ︰](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing 地圖

我們將使用 Bing 地圖 API 來做兩件事情。

+ **地理編碼地址 ︰** 在資料中，我們有地址的縣 （市）、 狀態 （zip），但是我們還要取得該地址的經度和緯度座標以便進行地理空間搜尋。 為取得座標，我們將使用 Bing 地圖 DataFlow API 來傳送一批地址以利地址編碼。 透過 Bing 試用帳戶，我們一次只能使用 50 組地址，但是這已經足夠本教學課程使用。

+ **Bing 地圖 ︰** 應用程式執行時，我們將使用 Bing 地圖來顯示商店位置，Bing 地圖上。

### 建立 Bing 地圖帳戶

1. 移至 [Bing 地圖入口網站](https://www.bingmapsportal.com/) 並建立新的帳戶。 輸入詳細資料以建立帳戶。

2. 建立帳戶之後，請選擇 **建立或檢視表的索引鍵** 輸入來建立金鑰的詳細資料。  為方便本示範中，您可以選擇 **Trial Key**。

3. 按一下 [ **提交**。 您的 Bing 地圖應用程式，您應該看到的重要細節。 請記下此金鑰，以便稍後使用。

<a id="sub-3"></a>
## C# 中使用 Bing 地圖 DataFlow API 的地理編碼地址

在此步驟中，我們會針對全球各地的不同自行車商店，使用 Bing 地圖 DataFlow API 來建立一些地理編碼地址。 

此項資料來自名為 store_locations.csv (位於您剛下載的來源) 的 CSV 檔案。 如果您在文字編輯器或是 Excel 中開啟此檔案，會看到每一間商店、商店名稱及其地址都會有對應的 ID 欄位。

讓我們逐一檢視這些代碼，了解其運作原理。

1. 在 Visual Studio 中開啟 AdventureWorksGeo 方案中，展開專案 **StoreIndexer** 方案總管] 中開啟 Program.cs。 由於我們已在索引建立 [Azure 搜尋 – Adventure Works 示範](http://azuresearchadventureworksdemo.codeplex.com/), ，我們將略過討論的 Program.cs 中的運作方式。

2. 移至 **Main** 函式，並注意它會呼叫 **ApplyStoreData**。  移至此函數，並詳細了解此程式碼。  

3. **ApplyStoreData** System.Data.DataTable 名"為 store_locations.csv"的 CSV 檔案載入資料。  

    此檔案內含所有商店，包括我們想要載入 Azure 搜尋服務的地址。  逐一查看這個檔案中的每個資料列，我們可以建立一組 **indexOperations** ，然後插入 Azure 搜尋索引 (先前 **createstoresindex （)** 函式)。  

    如果您之後仔細查看索引，您會發現 **GeoPt** 經度和緯度的每個存放區會包含的欄位是空的。 這就把我們帶到下一個步驟 **Main** 函式。

5. 移至函數 **extractaddressinfotoxml （)**。 此函數會從 store_locations.csv 檔案擷取地址資訊，並使用以 Bing 地圖可以接受並進行地理編碼的格式來載入 XML 檔案。 檔案建立之後，就會傳送 Bing 地圖 DataFlow 進行處理呼叫函式 **GeoCoding.CreateJob**。

6. 由於地理編碼程序可能需要一些時間，因此會呼叫的迴圈 **GeoCoding.CheckStatus** 每隔 10 秒，以查看該工作是否完成。 藉由呼叫下載完成後，結果 **GeoCoding.DownloadResults** 至地址類別。

7. 最後步驟是將這些地理編碼的地址傳送至 Azure 搜尋服務。 讓我們仔細看看如何做到這點開啟 **UpdateStoreData** 函式。

  **UpdateStoreData** 使用動作 **@search.action ︰ 合併** 座標來更新 Edm.GeographyPoint 類型的位置欄位縱向與橫向剛從 Bing 地圖下載。 此函數會查詢 "stores" 索引中屬於該文件唯一金鑰的 StoreId，然後將此新資料與現有文件合併。

8. 在執行應用程式之前，請開啟 App.config 並針對您的 Azure 搜尋服務與 Bing 地圖 API 更新其 "SearchServiceName"、"SearchServiceApiKey" 與 "BingMapsAPI" 的值，以新增您的 Azure 搜尋服務與 Bing 地圖 API 資訊。 在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

9. 以滑鼠右鍵按一下 **StoreIndexer** 專案，並選擇 **偵錯** | **開始新執行個體** 執行它。

<a id="sub-4"></a>
## 使用 Azure 搜尋服務與 Bing 地圖為 MVC4 應用程式新增地圖

在此步驟中，您將在網頁瀏覽器中建立並執行搜尋應用程式，以便載入商店搜尋並繪製在 Bing 地圖上。

1.  在 Visual Studio 中，開啟名為 AdventureWorksGeo.sln 的 Azure 搜尋服務示範解決方案。 
    
2.  以滑鼠右鍵按一下 **AdventureWorksWebGeo** 在方案總管] 中，選取 **設定為啟始專案**。
    
3.  開啟此專案中的 Web.config，並更新您的 Azure 搜尋服務與 Bing 地圖 API 裡的 "SearchServiceName"、"SearchServiceApiKey" 與 "BingMapsAPI" 值。 在搜尋服務名稱上，如果您的服務是 "mysearch.search.windows.net"，則您需要輸入 "mysearch"。

4.  儲存 Web.config。
    
5.  按下 **F5** 啟動專案。 請遵循下列 [疑難排解](#err-mvc) 步驟，如果您收到建置錯誤。

請注意，這些商店會以點狀方式呈現在地圖上。 按一下其中一間商店，會看到快顯視窗，內含該商店的詳細資料。 這裡的所有資訊都來自先前步驟中建立，名為 "stores" 的 Azure 搜尋索引。 

<a id="sub-5"></a>
## 探索 AdventureWorksWebGeo

專案 **AdventureWorksWebGeo** 示範如何使用 ASP.NET MVC 4 與 Azure 搜尋來建置使用地理搜尋功能的地圖應用程式進行互動。 在本小節裡，我們將檢視個別的應用程式碼部分，了解他們做了哪些事情。

1.  在 [方案總管] 中，展開 **AdventureWorksWebGeo** | **控制器** 並開啟 HomeController.cs。  **Index （)** 應用程式啟動和索引頁面載入時，會呼叫函式。 在此函數中，Bing 地圖 API 會從 Web.config 載入並傳遞至「索引」檢視作為 ViewBag.BingAPI 使用。

2.  開啟 Index.cshtml 從 **檢視** | **首頁**。

3.  此檔案會遵循您通常用來將 Bing 地圖新增至 Web 應用程式的方式進行，不過幾個重點如下：

+   控制器裡的 ViewBag 會用來搭配以下項目載入地圖認證：'@ViewBag.BingAPI' 

+   一旦在地圖載入，JQuery $.post 會加入 HomeController **搜尋** 函數: / home/搜尋

+    **搜尋** 函式會擷取商店位置，然後再接收並且新增為 PushPins Bing 地圖。 

4.  開啟下方的 HomeController.cs **控制器** 並查看 **搜尋** 函式。 請注意此函數如何呼叫 _storeSearch.Search(lat, lon, 10000)。 此舉會導致執行查詢，以尋找距離指定的橫向 (lat) 與縱向 (lon) 座標 10,000 公里以內的所有商店。 查詢的結果會進行處理，然後傳回「索引」檢視以便處理為 PushPins 並顯示在 Bing 地圖上。

示範就到此為止。 現在您已經逐一檢視過所有需要知道的主要作業流程，可以開始依據 ASP.NET MVC4 應用程式並搭配 Azure 搜尋來打造出自己的地圖。


<a id="err-mvc"></a>
## 如何解決「無法載入檔案或是組件 'System.Web.Mvc」

在打造 AdventureWorksWeb 時，如果出現「無法載入檔案或是組件 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 或其中一項相依性」的訊息時，請嘗試以下步驟來解決這個錯誤。

1. 開啟 [封裝管理員主控台 ︰ **工具** | **NuGet 封裝管理員** | **封裝管理員主控台**
2. 在 PM> 提示字元底下，輸入「Update-package -reinstall Microsoft.AspNet.Mvc」
3. 當系統要求您重新載入檔案，選擇 [ **全部**。
4. 重建方案，然後再試 **F5** 一次。

<a id="next-steps"></a>
## 後續步驟

為力求精進，請考慮在此地圖應用程式加入更多功能。 例如，您可以新增：

+ 搜尋方塊，以便讓使用者搜尋特定商店。

+ Facet 類別，以讓使用者依國家/地區或州/省篩選。  

+ 使用者繪製的選擇區域，讓使用者藉由拖曳地圖上的區域來指定要搜尋的區域。 該區域可以接著由 Azure 搜尋服務透過地理互動 API 進行篩選，然後繪製在地圖上。


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG 

