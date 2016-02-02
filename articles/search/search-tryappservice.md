<properties 
   pageTitle="使用 Azure App Service 免費試用 Azure Search | Microsoft Azure"
   description="使用 Azure App Service 範本免費試用 Azure Search 長達一小時。"
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>


# 使用 Azure App Service 免費試用 Azure Search

[Azure App Service](https://tryappservice.azure.com/) 是新的、 完全免費的方式可以測試選取的 Azure 服務，包括 Azure 搜尋服務，提供最多一個小時，且沒有 Azure 訂用帳戶註冊所需。

此網站可讓您從數個範本中選擇。 當您選取包含 Azure 搜尋服務的 ASP.NET 範本時，您就能獲得一個小時存取功能完整的網站，由您所選取的服務所支援。 您將無法更新或刪除任何由 Azure 搜尋服務管理的資料 – 但是您可以執行查詢，並進行任意數目的程式碼變更，以重塑使用者體驗。 如果工作階段在您完成探索之前到期，您一律可以重新開始另一個工作階段，或者如果您的目標是要直接建立或載入索引，請移至試用版或完整的訂用帳戶。

在 [Azure App Service](https://tryappservice.azure.com/) Azure 搜尋服務的網站是 Azure 平台上提供豐富、 全文檢索搜尋體驗，以及搜尋中心功能僅適用於此服務的許多 Web 應用程式範本的一部分。

雖然其他 Azure 服務，例如 SQL Database，提供全文檢索搜尋，但是類似 Azure 搜尋服務的服務可提供您調整控制項、分頁和計數；點閱數醒目提示、自動完成查詢建議、自然語言支援、多面向導覽、篩選以及更多功能。如同幾個我們 [範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search) 示範，就可以開發的功能完整的搜尋應用程式只使用 Azure 搜尋服務和 ASP.NET。

一部分 [Azure App Service](https://tryappservice.azure.com/) 供應項目，您將使用 Azure 搜尋服務已建立的程式，並準備好接收搜尋查詢。 您無法上傳或使用您自己的索引或資料。 您將使用的資料是來自 [美國地質調查局 (USG)](http://geonames.usgs.gov/domestic/download_data.htm), ，包含橫跨美國大約 3 百萬個資料列的地標、 歷史、 建築物和其他地標特色。

為了協助您充分運用一個小時的工作階段，下列指示將逐步引導您了解查詢和程式碼。

繼續進行之前，您可能要花幾分鐘的時間檢閱一些關於程式碼、服務和可搜尋資料的重點。 如果您還不熟悉 Azure 搜尋服務，了解一些背景相當實用。

## 關於程式碼與 Azure 搜尋服務的事實

Azure 搜尋服務是完全受管理的平台即服務 [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) 服務，以簡化開發人員整合在 web 和行動應用程式的絕佳搜尋經驗。 在搜尋作業中使用的資料會利用搜尋服務儲存在 Azure 中，其中資料和作業的鄰近性可確保低延遲性和一致的搜尋行為。 深入探索這一點：

- 可搜尋的資料會儲存在受 Azure Search 管理的索引中
- 定義索引的結構描述是由使用者定義，並會指定可搜尋的欄位，在篩選運算式中有用的非可搜尋欄位以及建構，例如微調結果的評分設定檔。
- 搜尋索引包含一或多個既可搜尋也可擷取的文件 (類似於資料表中的資料列)
- 大部分的索引都會從單一資料集載入，由您預先準備來包含僅在搜尋作業的內容中才有用的欄位。
- 資料可以由索引子 (僅支援 Azure SQL Database 或 Azure DocumentDB) 自動載入或透過其中一個 Azure 搜尋服務 API 推送至搜尋索引。 當您使用 API 時，您可以從任何資料來源推送資料，只要它是 JSON 格式即可。

在 [Azure App Service](https://tryappservice.azure.com/) 選項時，「 ASP.NET + Azure 搜尋網站 」 範本，提供原始程式碼的 Web 應用程式，可修改在 Visual Studio Team Services (可為一小時工作階段的一部分)。 檢視或變更程式碼不需要個別的開發工具。

在 C# 中撰寫程式碼使用 [Azure 搜尋服務.NET 用戶端程式庫](https://msdn.microsoft.com/library/dn951165.aspx) 針對索引執行查詢，提供多面向導覽，並顯示計數和搜尋結果網頁中。

其他程式碼 (不包含在範本中) 用來建置和載入 USGS 搜尋索引。 因為服務是唯讀的，所以需要寫入存取權的所有作業都必須事先完成。 您可以看到 [的結構描述複本](#schema) 用來建置結構描述，這篇文章的結尾。

## 開始使用

如果您尚未開始您的 1 小時工作階段，請遵循下列步驟開始。

1. 移至 [https://tryappservice.azure.com](https://tryappservice.azure.com/) 向下選取捲動 **Web 應用程式**。
2. 按 [下一步]****。
3. 選擇 [ASP.NET + Azure Search 網站]**** 範本。

    ![][1]

4. 按一下 [建立]****。
5. 選擇登入方法並提供使用者名稱和密碼。

    ![][2]

6. 請等候佈建網站。 準備好時，您會看到類似以下的頁面。 每個頁面會顯示正在執行的時鐘，好讓您隨時知道還剩下多少時間。

    ![][3]

7. 選擇 [使用 Visual Studio Team Services 進行編輯]**** 以檢視解決方案並瀏覽網站。
9. 在 Visual Studio Team Services 中，展開頁面頂端的工作階段選項，然後按一下 [瀏覽網站]****。

    ![][4]

10. 您應該會看到 Azure 搜尋服務網站的開始使用頁面。 按一下 [開始使用]**** 按鈕以開啟網站。

    ![][5]

11. ASP.NET 網站會在瀏覽器中開啟，提供搜尋方塊。 輸入要搜尋的熟悉詞彙 (例如 *Yellowstone*) 或是知名的山 (例如 *Mount Rainier*)。 從熟悉的地標開始以輕鬆評估結果。

    ![][6]


## 如何開始

由於搜尋索引可完全運作，良好的第一個步驟就是嘗試幾個查詢。 Azure 
搜尋服務支援所有標準搜尋運算子 (+、-、 |)，常值的相符項目、 萬用字元 (*) 和優先順序運算子加上引號。 您可以檢閱運算子完整清單的查詢語法參考。

- 開始萬用字元搜尋，藉由加入星號 (`*`)。 這會告訴您在索引中找到多少文件：2,262,578。
- 接下來，請輸入"Yellowstone"，然後加入"+ center"，"+ building"和"-ND"，漸進式搜尋結果縮小成只 Yellowstone 訪客中心，排除那些在北達科他: `Yellowstone + center + 建置-ND`。
- 請嘗試搜尋片語，結合優先順序運算子和字串比對: `statue+(park+MT)`。 您應該會看到類似下列螢幕擷取畫面的結果。 請注意，facet 類別會出現在功能類別之下，透過多面向導覽提供自我引導篩選，這是在大部分搜尋應用程式中經常發現的功能。

    ![][7]

準備好繼續進行了嗎？ 讓我們變更幾行程式碼，查看對全文檢索搜尋作業的影響。

## 變更 searchMode.All

Azure Search 具有可設定的 **searchMode** 屬性，可用來控制搜尋運算子行為。 這個屬性的有效值為 `任何` (預設值) 或 `所有`。 請參閱 [簡單的查詢語法](https://msdn.microsoft.com/library/dn798920.aspx) 如需設定這些選項的詳細指引。

- **searchMode.Any** 規定與搜尋詞彙相符的任何項目都足以在搜尋結果中包含一個項目。 如果您的搜尋片語是 `Yellowstone 訪客中心`, ，則包含這些字詞的任何文件包含在搜尋結果。 這種模式偏向「重新叫用」**。
- 此範例中使用的 **searchModel.All** 會要求文件中必須要有所有指定的詞彙。 這個模式比 **searchMode.Any** 更嚴苛，但是如果您偏好「精確度」**勝過重新叫用，它可能會是您應用程式的理想選擇。

> [AZURE.NOTE] **searchMode.Any** 在查詢建構大部分包含片語且使用最少量運算子時效果最佳。 根據一般的經驗法則，搜尋消費者應用程式 (例如電子商務網站) 的人員傾向於只使用詞彙，而搜尋內容或資料的人員更有可能在搜尋片語中包含運算子。 如果您認為搜尋可能會包括運算子，尤其是 `不 (-)` 運算子，開始 **searchModel.All**。 相較之下，其他選擇， **searchMode.Any** 將 `或者`  `不` 與其他搜尋詞彙，而不是去除結果可大幅擴充的運算子。 下列範例可協助您了解差異。

在這個工作中，您將變更 **searchMode**，並根據模式比較搜尋結果。

1. 開啟包含範例應用程式的瀏覽器視窗，選擇 [連接至 Visual Studio Team Services]****。

    ![][8]

2. 開啟 **Search.cshtml**, ，尋找 `searchMode.All` 上第 39 行，並將它變更為 `searchMode.Any`。

    ![][9]

3. 在右邊的導向列中，按一下 [執行]****。

    ![][10]

在重建應用程式視窗中，輸入搜尋詞彙，曾用過，例如 `Yellowstone + center + 建置-ND`, ，並比較之前和之後的結果變更 **searchMode**。

這是個很大的差異。 您不會取得七個搜尋結果，而是取得超過兩百萬個。

   ![][11]

您正在觀察的行為是因為包含 `不` 運算子 (在此情況下，"-ND")，也就是 *或有* 而 *AND 有* 時 **searchMode** 設為 `任何`。

指定這個設定，搜尋結果包含叫用的搜尋詞彙 `Yellowstone`, ，`center`, ，和 `建置`, ，但也是每個文件 `不北達科他`。 由於只含 13081 文件包含片語 `北達科他`, ，幾乎所有的資料集傳回。

無可否認地，這可能是罕見的案例，但它也說明了的效果 **searchMode** 上搜尋片語包含 `不` 運算子，因此有助於了解為什麼會發生的行為，並變更它，如果這是不是您想要的。

若要繼續本教學課程中，還原 **searchMode** 回其原始值 (設為 `所有` 第 39 行)、 執行程式，並且將重建應用程式用於其餘的工作。

## 新增華盛頓州的全域篩選器

一般來說，如果您想要搜尋可用的資料子集，您會在匯入資料時，在資料來源設定篩選器。 基於學習的目的，使用唯讀資料時，我們會在應用程式中設定篩選器，只傳回包含華盛頓州的文件。

1. 開啟 Search.cshtml，尋找 **SearchParameters** 程式碼區塊 (從第 36 行開始)，然後新增註解行再加上篩選。

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };



篩選器可以使用 OData 語法來指定，並經常搭配多面向導覽使用或包含在限制查詢的查詢字串中。 請參閱 [OData 篩選語法](https://msdn.microsoft.com/library/azure/dn798921.aspx) 如需詳細資訊。

2. 按一下 **[執行]**。

3. 開啟應用程式。

4. 輸入萬用字元 (*) 以傳回計數。 請注意，結果現在會限制為 42,411 個項目，也就是華盛頓州中所有地理功能的所有文件。

   ![][12]

## 新增命中結果醒目提示

既然您進行了一連串的單行程式碼變更，您可能會想要嘗試更深層的修改，需要在多個位置中的程式碼變更。 下列版本的 **Search.cshtml** 可以直接貼在您目前工作階段中的 Search.cshtml 檔案上。

下列程式碼會新增點閱數醒目提示。 請注意在中宣告的新屬性 [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx)。 另外還有一個新的功能，可以逐一查看結果的集合，以取得需要醒目提示的文件，加上呈現結果的 HTML。

當您執行此程式碼範例時，在指定的欄位中有對應相符項目的搜尋詞彙輸入會以粗體字醒目提示。

   ![][14]

您可能會想要儲存一份原始 **Search.cshtml** 檔案的複本來查看這兩種版本的比較。
> [AZURE.NOTE] 已除去註解以減少檔案大小。

    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## 後續步驟

使用中提供唯讀服務 [Try Azure App Service](https://tryappservice.azure.com/) 站台，您已看過的查詢語法和全文檢索搜尋，了解 searchMode 和篩選器，並加入至搜尋應用程式的 [醒目。 在下一個步驟中，請考慮繼續建立並更新索引。 這樣可以新增下列功能：

- [定義評分設定檔](https://msdn.microsoft.com/library/dn798928.aspx) 來進行微調搜尋分數，以便先顯示高價值的項目。
- [定義建議工具](https://msdn.microsoft.com/library/mt131377.aspx) ，加入自動完成或預先輸入查詢建議以回應使用者輸入。
- [定義索引子](https://msdn.microsoft.com/library/dn946891.aspx) ，您的索引時，自動更新資料來源是 Azure SQL Database 或 Azure DocumentDB。

若要執行所有工作，您需要 Azure 訂用帳戶，讓您可以在服務中建立並填入索引。 如需如何註冊免費試用版的詳細資訊，請瀏覽 [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/)。

若要深入了解 Azure 搜尋服務，請造訪我們 [文件頁面](http://azure.microsoft.com/documentation/services/search/) 上 [http://azure.microsoft.com](http://azure.microsoft.com) 或簽出任何數目的 [範例和影片](search-video-demo-tutorial-list.md) 以探索完整範圍的 Azure 搜尋服務功能。

<a name="Schema"></a>
## 關於結構描述

下列螢幕擷取畫面顯示用來建立此範本中所使用之索引的結構描述。

   ![][13]

### Schema.json 檔案

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }


[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png 
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png 
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png 
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png 
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png 
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png 
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png 
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png 
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png 
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png 
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png 
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png 
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png 
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png 

