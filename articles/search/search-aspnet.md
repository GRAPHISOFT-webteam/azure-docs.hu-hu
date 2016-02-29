<properties
    pageTitle="連接 Azure 搜尋服務與 ASP.NET MVC Web 應用程式 |Microsoft Azure |託管的雲端搜尋服務"
    description="將 ASP.NET MVC Web 應用程式與 Azure 搜尋服務 (雲端託管搜尋服務) 連結。 了解如何透過 .Net 程式庫或 REST API 連接、查詢和轉譯結果。"
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="11/04/2015"
    ms.author="heidist"/>

#如何整合 Azure 搜尋服務與 ASP.NET MVC Web Apps

ASP.NET MVC 是自訂方案中與 Azure 搜尋服務整合的主流 Web 應用程式架構。 在本文中，您將學習如何將 ASP.NET Web 應用程式連接到 Azure 搜尋服務，以及增加一般作業的設計模式，並檢閱幾個撰寫程式碼的做法，讓您的開發經驗更順利。 

##使用 ASP.NET 和 Azure 搜尋服務的範例和示範

已經有數個程式碼範例示範了「搜尋」如何與 ASP.NET 整合。 點選以下連結，即可直接瀏覽程式碼或示範應用程式：

- [紐約市 (NYC) 工作示範網站](http://aka.ms/azjobsdemo)
- [試用 App Service + Azure 搜尋服務](search-tryappservice.md)
- [影片、教學課程、示範及程式碼範例的完整清單](earch-video-demo-tutorial-list.md)

##連接至服務

若要與服務建立連接，並發出要求，您的 Web 應用程式只需要三個項目： 

- 您已佈建，Azure 搜尋服務的 URL 格式為 https://<service-name>.search.windows.net
- 驗證 Azure 搜尋服務的連線的 API 金鑰 (字串)
- 構成連接要求的 HTTPClient 或 SearchServiceClient

####URL 和 API 金鑰

您可以找到中的 URL 和 API 金鑰 [入口網站](search-create-service-portal.md) 或以程式設計方式使用取出 [管理 REST API](https://msdn.microsoft.com/library/dn832684.aspx)。 

一般而言，URL 和金鑰都會放在使用者互動程式的 web.config 檔案中：

      <appSettings>
        <add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
        <add key="SearchServiceApiKey" value="[API KEY]" />
        . . .
      </appSettings>

搜尋服務名稱可以是您在佈建，只要新增網域 (.search.windows.net) 連線時，指定簡短名稱，或者您也可以指定完整格式的名稱 (< 服務名稱 >。.search.windows.net) 在 web.config 中，如果沒有 HTTPS 前置詞。

API 金鑰是服務佈建期間所產生的驗證權杖 (此情況僅限管理金鑰)，或是您在入口網站中建立查詢金鑰時，以手動方式產生的驗證權杖。 金鑰的類型可決定您的應用程式能使用哪些搜尋作業：

- 管理金鑰 (具有讀寫權限，每個服務 2 個)
- 查詢金鑰 (唯讀，每個服務最多 50 個)

API 金鑰是 32 個字元的字串。 管理和查詢金鑰之間，在外觀上並無差別。 如果您遺失您在程式碼中指定採取的某種類型金鑰，您需要檢查入口網站或使用管理 REST API 來傳回金鑰類型。 若要深入了解金鑰，請造訪 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

> [AZURE.TIP] 查詢索引鍵給用戶端提供唯讀的經驗。 請參閱 [TryAppService + Azure 搜尋](search-tryappservice.md) 可以測試可用於唯讀服務的 Azure 搜尋服務作業。 請注意，在 TryAppService 中，Web 應用程式程式碼完全可以修改，您可以變更 ASP.NET 專案中的所有 C# 程式碼，以修改網頁配置、搜尋查詢建構或搜尋結果，這些都只是 Azure 搜尋的服務索引和文件載入作業，而且均為唯讀 (依據服務連接上查詢 api-key 的包含項目)。

####用戶端連接

接下來的兩個程式碼片段會使用 URL 和 API 金鑰來設定「搜尋」服務的連接。 提醒您，服務名稱和 API 金鑰均是在 web.config 檔案中指定的。 若為 REST 呼叫，必須在要求標頭中傳入管理金鑰，並將查詢金鑰傳入標頭，或直接放在 URL 中。

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) 與 REST API 呼叫**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) 與.NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##設計模式

與 Azure 搜尋服務整合的 Web 應用程式必須編寫查詢並轉譯結果。 本節將指引您如何針對在程式 (包含使用者互動程式碼) 中執行的工作建構程式碼。 我們已刻意排除結構描述定義、索引產生和資料擷取。 如何撰寫程式碼的作業上的指引，請參閱逐步解說和範例中所列 [影片、 範例和教學課程，在 Azure 搜尋服務](search-video-demo-tutorial-list.md)。 

###查詢編寫

標示的欄位上執行全文檢索搜尋索引透過 **isSearchable** 結構描述中定義索引。 指定輸入的搜尋詞彙 (由下列字串"q")，搜尋引擎尋找相符項目內所有可搜尋的欄位，並傳回結果的欄位標記為 **isRetrievable**。 

> [AZURE.NOTE] 雖然大部分的欄位可能是可搜尋，索引可以包含只能用在篩選條件運算式，會將它們標示為不可搜尋排除全文檢索搜尋和非擷取排除搜尋的情況下產生的欄位。 

搜尋查詢會將使用者提供的輸入詞彙，包裝到「搜尋」要求中，以指定目標索引，然後再加上用來篩選或縮小要求範圍的參數。 程式會自動處理搜尋字串中內嵌的運算子，例如 +、- 或 |，這表示不需要剖析搜尋詞彙的程式碼。 搜尋引擎會以內部作業的方式完成所有剖析。 您可以假設傳入的字串會由引擎進行剖析和分析。

搜尋查詢有兩種類別: **搜尋** 或 **建議**。 您需要為各個查詢類型定義不同的方法。 **搜尋** 是全文檢索搜尋索引中的欄位。 **建議** 是預先輸入或自動完成查詢功能，在 Azure 搜尋服務，會建立根據使用者輸入的前三個字元的潛在搜尋詞彙的清單。 在大部分情況下，您可以限制 **建議** 只包含相對唯一或特殊的值 (例如產品或發行集名稱)，而不是欄位，其中包含無的資料的欄位。

以下程式碼片段會在使用 REST API 的程式中擷取搜尋詞彙輸入。 輸入詞彙是由字串 q 做為表示，其餘參數則是用來從同一搜尋頁面上的多面向導覽結構傳入篩選值。 「搜尋」方法中會同時使用輸入詞彙和篩選參數。

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
 **搜尋** 接受這項查詢的方法定義，如下所示。 請注意，此方法會定義查詢字串上的參數、多面向導覽結構 (透過篩選條件支援，是幫忙縮小搜尋結果範圍的主力)，以及排序順序。

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

建構搜尋字串的 .NET 方法可置於 MVC 檢視或控制器中。 此函數會將字串傳遞至主控制器。 此外它也會定義搜尋結果的資料結構。 

    function Search() {

        var q = $("#q").val();
        
        $.post('/home/search',
        {
            q: q
        },
        function (data) {
            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
            searchResultsHTML += "<td>DATE EDITED</td></tr>";
            for (var i = 0; i < data.length; i++) {
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
            }

            $("#searchResults").html(searchResultsHTML);

        });

叫用的.NET 方法 **搜尋** 看起來可能如下所示，包含主要 C# 程式中提供的連接，並搜尋作業:

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###處理搜尋結果

搜尋結果會以欄位所組成的資料列集形式傳回，這些欄位在索引結構描述中會標示為 isRetrievable。 若要轉譯結果集，較為簡單的一種方式是在 MVC 中使用 ViewBag 系統物件。 下列程式碼片段取自中 Index.cshtml [CodePlex 上的 AdventureWorksDemo 專案](https://azuresearchadventureworksdemo.codeplex.com/)。

    @model dynamic
    
    @{
        ViewBag.Title = "Search";
    }
    
    <h2>Product search</h2>
    
    @if (@ViewBag.errorMessage != null) {
        @ViewBag.errorMessage
    } else {
        <div class="container">
            <form action="/Home/Search" method="get">
                <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
                <input type="hidden" name="color" id="color" value="@ViewBag.color" />
                <input type="hidden" name="category" id="category" value="@ViewBag.category" />
                <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
                <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
                <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
            </form>
        </div>

###多面向導覽

在同一個 Index.cshmtl 檔案中，您可以找到用來建立多面向導覽結構的 HTML，其可為自我引導的篩選提供分類，並依色彩、價格或類別逐漸縮小搜尋結果的範圍。 

        if (@Model != null)
        {
            <div class="container">
                <div class="row">
                    <div class="col-md-4">
                        Colors:
                        <ul>
                            @foreach (var colorFacet in Model["@search.facets"].color)
                            {
                                <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
    document.forms[0].submit(); 
    return false;">@colorFacet.value</a> (@colorFacet.count)</li>
                            }
                        </ul>
                        Categories:
                        <ul>
                            @foreach (var categoryFacet in Model["@search.facets"].categoryName)
                            {
                                <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
                            }
                        </ul>
                        Prices:
                        <ul>
                            @foreach (var priceFacet in Model["@search.facets"].listPrice)
                            {
                                if (priceFacet.count > 0)
                                {
                           <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
    document.forms[0].submit(); return false;
    ">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
                                }
                            }
                        </ul>
                    </div>
                    <div class="col-md-8">
                        <p>
                            Sort -
                            <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
                            <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
                            <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
                        </p>
                        <p>Found @Model["@odata.count"] products in the catalog</p>
    
                        <ul>
                            @foreach (var product in Model.value)
                            {
                                <li>
                                    <h3><b>@product.name</b></h3>
                                    price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
                                </li>
                            }
                        </ul>
                    </div>
                </div>
            </div>
        }
    }


###搜尋結果醒目提示

將樣式套用到搜尋結果中的搜尋詞彙例項，稱為「搜尋結果醒目提示」。 在 Azure 搜尋服務中，會於查詢中指定搜尋結果醒目提示，指定方法為透過醒目提示搜尋參數，提供以逗號分隔的欄位清單，以便掃描相符詞彙。 您所套用的實際樣式是由您決定。 下列三個程式碼片段會從 [TryAppService + Azure 搜尋服務教學課程](search-tryappservice.md)。

首先，請指定搜尋結果醒目提示做為搜尋參數，並列出要檢查相符詞彙的欄位。 指定要用於搜尋結果醒目提示的 HTML 樣式。

    // Set the Search parameters used when executing the search request
         var sp = new SearchParameters
    {
    // Include a count of results in the query result
         IncludeTotalResultCount = true,
    // Limit the results to 20 documents
         Top = 20,
    // Enable hit-highlighting
         HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
         HighlightPreTag = "<b>",
         HighlightPostTag = "</b>",
    };

接下來，逐一查看要尋找的字串必須以反白顯示搜尋結果。
私用 HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

      {
         if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
          {
          string highlightedResult = string.Join("...", item.Highlights[fieldName]);
          return new HtmlString(highlightedResult);
          }
          return new HtmlString(item.Document[fieldName].ToString());
       }

最後，提供搜尋結果的配置，藉此指定在先前的程式碼片段中已評估的結果集。

    <div class="col-xs-12 col-sm-6 col-md-10">
      <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
      <ul class="list-unstyled">
        <!-- Cycle through the search results -->
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


##常見的程式碼撰寫慣例

初次接觸 MVC、.NET 程式設計、或 REST API？  以下各節提供一些程式碼撰寫慣例，可協助您快速掌握到訣竅。

###MVC 範本

下表摘要說明在包含 Azure 搜尋服務的應用程式中，使用 MVC 範本元件的方式。 如果您使用 MVC 4 或 MVC 5，則整合 Azure 搜尋服務的程式碼會通常會加入至這些模組

檔案|說明
----|-----------
Web.config|提供服務 URL 和 API 金鑰。 將 System.Configuration 的參考加入主要程式模組，以讀取值。
Program.cs|在主程式中，設定 HttpClient 或 SearchServiceClient，以建立服務的連接。 將「搜尋」方法加入此程式。
DataModel|不使用。 假設索引建立和資料載入作業分別在不同的程式中， 則 Web 應用程式中的 Azure 搜尋服務就不需要資料模型。
Views|包含應用程式網頁 HTML 的檢視，從搜尋方塊輸入到處理搜尋結果的動態 HTML 均可顯示。
Controllers|在 HomeContoller.cs 中通常可以找到查詢建構和錯誤處理。 至少控制器應該包含搜尋方法，以從 Azure 搜尋服務擷取結果，並將結果集轉送至檢視。 

(選擇性) 如果要讓自動完成查詢使用建議，您應包含會傳回建議查詢的方法 (視您的索引是否包含與使用者所提供之搜尋詞彙輸入相符的值而定)。

###使用時機：.NET 用戶端程式庫與REST API

若為 ASP.NET 應用程式，您可以將 .NET 用戶端程式庫視為較好的選擇，因為它會設定 HTTP 連接，並為您處理 JSON 序列化和還原序列化，以簡化您的程式碼。

在某些情況下，可能會由兩種方法之間的功能同位指定您選擇的 API。 一般而言， [.NET 用戶端程式庫](https://msdn.microsoft.com/library/azure/dn951165.aspx) 和 [服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，只要您要求作業的實作中都是可互換。 不過，有時候在預覽版本中，會先在 REST API 中顯示新功能，且這些功能只會在數個月後加入 .NET 程式庫。 例如，用來將特定資料來源類型的資料載入作業自動化的索引子，會在預覽版 REST API 中先顯示，然後在幾個月後才出現在用戶端程式庫中。 在功能文件中，會標註所有功能實作的限制。

###在 REST API 中包含 JSON 序列化與還原序列化的 AzureSearchHelper.cs

與 .NET 程式庫不同的是，.NET 程式庫會為您執行此步驟，而服務 REST API 則必須使用服務在要求-回應交換中，將 JSON 文件序列化和還原序列化。 JSON 是載入或重新整理索引中的文件時，資料傳送的承載格式。 

JSON 序列化程式碼可以在幾個範例中，找到名為的檔案 **AzureSearchHelper.cs**:

    using System;
    using System.Net.Http;
    using System.Text;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Converters;
    using Newtonsoft.Json.Serialization;
    
    namespace CatalogCommon
    {
        public class AzureSearchHelper
        {
            public const string ApiVersionString = "api-version=2014-07-31-Preview";
    
            private static readonly JsonSerializerSettings _jsonSettings;
    
            static AzureSearchHelper()
            {
                _jsonSettings = new JsonSerializerSettings
                {
                    Formatting = Formatting.Indented, // for readability, change to None for compactness
                    ContractResolver = new CamelCasePropertyNamesContractResolver(),
                    DateTimeZoneHandling = DateTimeZoneHandling.Utc
                };
    
                _jsonSettings.Converters.Add(new StringEnumConverter());
            }
    
            public static string SerializeJson(object value)
            {
                return JsonConvert.SerializeObject(value, _jsonSettings);
            }
    
            public static T DeserializeJson<T>(string json)
            {
                return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
            }
    
            public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
            {
                UriBuilder builder = new UriBuilder(uri);
                string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
                builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;
    
                var request = new HttpRequestMessage(method, builder.Uri);
    
                if (json != null)
                {
                    request.Content = new StringContent(json, Encoding.UTF8, "application/json");
                }
    
                return client.SendAsync(request).Result;
            }
    
            public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
            {
                if (!response.IsSuccessStatusCode)
                {
                    string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
                    throw new Exception("Search request failed: " + error);
                }
            }
        }
    }

###組織程式碼

將工作負載分割成同一 Visual Studio 方案內的多個獨立專案，讓您可以更具彈性的方式設計、維護和執行每個程式。 我們推薦使用下列三種方式：

- 索引建立程式碼
- 資料擷取程式碼
- 使用者互動程式碼

在 Azure 搜尋服務中，索引編製作業和文件作業 (例如新增或更新文件，或執行查詢) 是彼此完全獨立的作業。 這表示您可以從構成搜尋要求並轉譯結果的 ASP.NET 使用者互動程式碼，將索引管理分離出來。

在我們大部分的程式碼範例中，會在一個專案 (在不同範例中可稱為 DataIndexer、CatalogIndexer 或 DataCatalog) 中建立並載入索引，同時將處理搜尋要求和回應的程式碼，放在 ASP.NET MVC 應用程式專案中。 在程式碼範例中，雖然會在一個專案中組合索引建立和文件上傳，但實際執行程式碼中卻可能會隔離這些作業。 一旦建立索引後，就很少會變更它 (而且根據變更狀況，可能需要重建)，然而文件可能會重複地重新整理。

隔離工作負載可帶來其他優點，包括不同層級的 Azure 搜尋服務權限 (完整管理權限與僅查詢權限)、使用不同的程式設計語言、每個程式具有更特定的相依性，以及能夠獨立修改程式，或建立多個前端應用程式，讓它們都依中央索引編製應用程式所建立與維護的索引來運作。

##後續步驟

若要進一步了解 Azure 搜尋服務與 ASP.NET 整合，請造訪下列連結：

- [如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md) 
- [Azure 搜尋開發人員案例研究](search-dev-case-study-whattopedia.md)
- [Azure 搜尋開發的一般工作流程](search-workflow.md) 


