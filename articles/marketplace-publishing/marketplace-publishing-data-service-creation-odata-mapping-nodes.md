<properties
   pageTitle="建立資料服務的市場指南 |Microsoft Azure"
   description="如何建立、認證和部署資料服務供他人在 Azure Marketplace 上購買的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="11/30/2015"
      ms.author="hascipio; avikova" />

# 了解透過 CSDL 將現有的 Web 服務對應至 OData 的節點結構描述
本文件將協助釐清將 OData 通訊協定對應至 CSDL 的節點結構。 請務必注意，節點結構是格式正確的 XML。 因此，設計 OData 對應時，根、父和子結構描述皆適用。

## 忽略的元素
以下是 Azure Marketplace 後端將不會在匯入 Web 服務的中繼資料期間使用的高階 CSDL 元素 (XML 節點)。 它們可以存在，但會被忽略。

| 元素 | Scope |
|----|----|
| 使用元素 | 節點、子節點和所有屬性 |
| 文件元素 | 節點、子節點和所有屬性 |
| ComplexType | 節點、子節點和所有屬性 |
| 關聯元素 | 節點、子節點和所有屬性 |
| 擴充的屬性 | 節點、子節點和所有屬性 |
| EntityContainer | 下列的屬性會被忽略: *擴充* 和 *AssociationSet* |
| 結構描述 | 下列的屬性會被忽略: *命名空間* |
| FunctionImport | 下列的屬性會被忽略: *模式* (假設預設值是 ln) |
| EntityType | 系統會忽略下列子節點: *金鑰* 和 *PropertyRef* |

以下詳細說明各種 CSDL XML 節點的變更 (新增和忽略的元素)。

## FunctionImport 節點
FunctionImport 節點代表一個將服務公開給使用者的 URL (進入點) 此節點允許描述 URL 的定址方式、使用者可用的參數，以及如何提供這些參數。

這個節點之詳細資料會在找到 [http://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx][MSDNFunctionImportLink]

[MSDNFunctionImportLink]:'http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx'

以下是 FunctionImport 節點所公開的其他屬性 (或屬性的新增項目)：

**d:BaseUri** -
公開至 Marketplace 的其他資源的 URI 範本。 Marketplace 使用範本，針對 REST Web 服務建構查詢。 URI 範本包含 {parameterName} 格式中參數的預留位置，其中 parameterName 是參數的名稱。 例如 apiVersion = {apiVersion}。
允許參數以 URI 參數出現，或做為 URI 路徑的一部分。 如果出現在路徑中，它們永遠都是必要參數 (不能標示為可為 Null)。 *範例:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**名稱** -匯入函式的名稱。  不能與 CSDL 中其他定義的名稱相同。  例如 Name="GetModelUsageFile"

**EntitySet** *(選擇性)* -如果函數傳回的實體類型的值集合 **EntitySet** 必須是實體集合所屬集。 否則， **EntitySet** 不能使用屬性。 *範例:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(選擇性)* -指定的 URI 所傳回的項目類型。  如果函數沒有傳回值，請勿使用這個屬性。 以下是支援的類型：

 - **集合 (<Entity type name>)**: 指定定義的實體類型的集合。 名稱存在於 EntityType 節點的 Name 屬性中。 範例為 Collection(WXC.HourlyResult)。
 - **未經處理 (<mime type>)**: 指定未經處理的文件/blob，傳回給使用者。 範例為 Raw(image/jpeg)。其他範例：

  - ReturnType="Raw(text/plain)"
  - ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** -指定如何處理分頁的其他資源。 參數值是以大括弧括住，例如 page={$page}&itemsperpage={$size}。可用的選項如下：

- **None:** 沒有分頁可用
- **略過:** 分頁都會透過邏輯 [略過 」 和 「 接受 」 (上方) 表示。 Skip 跳過 M 個元素，然後 Take 傳回下 N 個元素。 參數值：$skip
- **Take:** Take 傳回下一步的 n 個項目。 參數值：$take
- **PageSize:** 分頁都會透過邏輯分頁和大小 (每頁的項目) 表示。 Page 代表目前傳回的頁面。 參數值：$page
- **大小:** 大小就代表傳回的每一頁的項目數目。 參數值：$size

**d:AllowedHttpMethods** *(選擇性)* -指定的動詞命令由其他資源。 此外，將接受的動詞限制為指定的值。  預設值 = POST。  *範例:* `d:AllowedHttpMethods="GET"` 可用的選項有:

- **GET:** 通常用來傳回資料
- **POST:** 通常用來插入新資料
- **PUT:** 通常用來更新資料
- **DELETE:** 用來刪除資料

FunctionImport 節點內的其他子節點 (未被 CSDL 文件涵蓋) 如下：

**d:RequestBody** *(選擇性)* -要求主體用來表示要求必須要有的本文傳送。 可在要求本文內提供參數。 它們是以大括號括住，例如 {parameterName}。 這些參數從使用者輸入對應至傳送至內容提供者服務的本文。 RequestBody 元素具有名稱為 httpMethod 的屬性。 屬性允許兩個值：

- **POST:** HTTP POST 要求時使用
- **GET:** 如果要求的 HTTP GET 使用

    範例：

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** 和 **d:Namespace** -此節點描述傳回的函式匯入 (URI 端點) 的 XML 中定義的命名空間。 後端服務所傳回的 XML 可能包含任意數目的命名空間，以區別所傳回的內容。 **所有這些命名空間，如果使用 d:Map 或 d:Match XPath 查詢中必須列。**d:Namespaces 節點包含 d:Namespace 節點集/清單。 其中每一個列出一個用於後端服務回應的命名空間。 以下是 d:Namespace 節點的屬性：

-   **d:Prefix:** 看出 XML 結果中傳回的服務，例如 f:FirstName、 f:LastName，其中 f 是前置詞命名空間的前置詞。
- **d:Uri:** 完整的結果文件中使用的命名空間 URI。 前置詞會在執行階段解析為其代表的值。

**d:ErrorHandling** *(選擇性)* -這個節點包含錯誤處理與條件。 針對內容提供者服務所傳回的結果驗證每個條件。 如果條件符合建議的 HTTP 錯誤碼，則錯誤訊息會傳回給使用者。

**d:ErrorHandling** *(選擇性)* 和 **d:Condition** *(選擇性)* -條件節點所擁有的簽入內容提供者的服務所傳回的結果。 以下是 **必要** 屬性:

- **d:Match:** 驗證是否存在於內容提供者指定的節點/值的 XPath 運算式的輸出 XML。 XPath 是針對輸出執行，因此如果符合條件，應該傳回 true，否則傳回 false。
- **d:HttpStatusCode:** HTTP 狀態碼應傳回的市場情況條件比對。 Marketplace 透過 HTTP 狀態碼向使用者發出錯誤信號。 HTTP 狀態碼的清單位於 http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** 會傳回到 – 具有 HTTP 狀態碼 – 使用者的錯誤訊息。 這應該是易懂的錯誤訊息，其中沒有包含任何機密資料。

**d:Title** *(選擇性)* -可讓您描述函式的標題。 標題的值來自

- 選用的對應屬性 (xpath)，其會指定是否要在服務要求傳回的回應中尋找標題。
- - 或者 - 指定為節點值的標題。

**d:Rights** *(選擇性)* -函式相關聯的權限 (例如著作權)。 權限的值來自

- 選用的對應屬性 (xpath)，其會指定是否要在服務要求傳回的回應中尋找權限。
-   - 或者 - 指定為節點值的權限。

**d:Description** *(選擇性)* -簡短描述函式。 說明的值來自

- 選用的對應屬性 (xpath)，其會指定是否要在服務要求傳回的回應中尋找說明。
- - 或者 - 指定為節點值的說明。

**d:EmitSelfLink** - *請參閱上述範例 」 FunctionImport '分頁' 的傳回資料 」*

**d:EncodeParameterValue** -OData 的選用擴充功能

**d:QueryResourceCost** -OData 的選用擴充功能

**d:Map** -OData 的選用擴充功能

**d:Headers** -OData 的選用擴充功能

**d:Headers** -OData 的選用擴充功能

**d:Value** -OData 的選用擴充功能

**d:HttpStatusCode** -OData 的選用擴充功能

**d:ErrorMessage** -OData 的選用擴充功能

## 參數節點

此節點代表一個公開為 URI 範本 / 要求本文 (已在 FunctionImport 節點中指定) 一部分的參數。

「 參數項目 」 節點的相關詳細資料很有幫助文件頁面會在找到 [http://msdn.microsoft.com/library/ee473431.aspx](http://msdn.microsoft.com/library/ee473431.aspx)  (使用 **其他版本** 下拉式清單中選取不同的版本，如有必要檢視文件)。 *範例:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| 參數屬性 | 必要 | 值 |
|----|----|----|
| 名稱 | 是 | 參數名稱。 區分大小寫！  BaseUri 大小寫須相符。 **範例:** `<Property Name="IsDormant" Type="Byte" />` |
| 類型 | 是 | 參數類型。 此值必須是 **EDMSimpleType** 或模型範圍內的複雜類型。 如需詳細資訊，請參閱「6 種支援的參數/屬性類型」。  (區分大小寫！ 第一個字元是大寫，其他都是小寫)。另請參閱  [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][MSDNParameterLink]. **範例:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| 模式 | 否 | **在**, 、，或 InOut 參數輸入、 輸出或輸入/輸出參數而定。 (只有 “IN” 適用於 Azure Marketplace)。**範例:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | 否 | 允許的參數長度上限。 **範例:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision | 否 | 參數的精確度。 **範例:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| 調整 | 否 | 參數的小數位數。 **範例:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]:'http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx'

以下是已加入至 CSDL 規格的屬性：

| 參數屬性 | 說明 |
|----|----|
| **d:Regex** *(選擇性)* | 用來驗證輸入參數值的 regex 陳述式。 如果輸入值不符合陳述式，則會拒絕此值。 這允許也可指定一組可能值，例如 ^[0-9]+?$，以僅允許數字。 **範例:** ' < 參數名稱 ="名稱"模式 ="In"類型 ="String"d: 可為 Null ="false"d:Regex ="^ [a A-ZA-Z] * $"d:Description ="A 不能包含任何空格或非字母的非英文字元的名稱"d:SampleValues ="George|John|Thomas|James"/ >' |
| **d:Enum** *(選擇性)* | 以直立線區隔的有效參數值清單。 這些值的類型必須符合已定義的參數類型。 範例: ' 英文|度量|原始`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< 參數名稱 = 「 持續時間 」 Type ="String"模式 ="In"可為 Null ="true"d:Enum ="1 年|5years|10years"/ >' |
| **d: 可為 Null** *(選擇性)* | 允許定義參數是否可為 null。 預設值為：true。 不過，公開為 URI 範本中路徑一部分的參數不可為 null。 當這些參數的屬性設為 false 時，使用者輸入會被覆寫。 允許定義參數是否可為 null。 預設值為：true。 不過，公開為 URI 範本中路徑一部分的參數不可為 null。 當這些參數的屬性設為 false 時，使用者輸入會被覆寫。 **範例:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(選擇性)* | 要在 UI 中顯示為用戶端之附註的範例值。  可以加入多個值，以直立線分隔，|。 **範例:** ' < 參數名稱 ="BikeOwner"Type ="String"模式 ="In"d:SampleValues ="George|John|Thomas|James"/ >' |
| **d:UriTemplate** | |

## EntityType 節點

這個節點代表從 Marketplace 傳回給使用者的其中一個類型。 它也包含從內容提供者服務所傳回之輸出到傳回給使用者之值的對應。

這個節點之詳細資料會在找到 [http://msdn.microsoft.com/library/bb399206.aspx](http://msdn.microsoft.com/library/bb399206.aspx) (使用 **其他版本** 下拉式清單中選取不同的版本，如有必要檢視文件。)

| 屬性名稱 | 必要 | 值 |
|----|----|----|
| 名稱 | 是 | 實體類型的名稱。 **範例:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | 否 | 另一個實體類型的名稱，而此實體類型是要定義之實體類型的基底類型。 **範例:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

以下是已加入至 CSDL 規格的屬性：

**d:Map** -針對服務的輸出執行 XPath 運算式。 這裡的假設，就是服務輸出包含一組重複的項目，例如 ATOM 摘要，其中有一組重複的項目。 其中每一個重複節點都包含一筆記錄。 然後，指定 XPath，以指向提供者服務結果中保留個別記錄值的個別重複節點。 來自服務的範例輸出：

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath 運算式將是 /foo/bar，因為每一個列節點就是輸出中的重複節點，並包含傳回給使用者的實際內容。

**索引鍵** -服務商場會忽略這個屬性。 REST 型 Web 服務通常不會公開主要金鑰。


## 屬性節點

這個節點包含記錄的屬性。

這個節點之詳細資料會在找到 [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (使用 **其他版本** 下拉式清單中選取不同的版本，如有必要檢視文件。)*範例:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | 必要 | 值 |
|----|----|----|
| 名稱 | 是 | 屬性的名稱。 |
| 類型 | 是 | 屬性值的類型。 屬性值類型必須是 **EDMSimpleType** 或複雜類型 (以完整限定的名稱) 的模型的範圍內。 如需詳細資訊，請參閱概念模型類型 (CSDL)。 |
| Nullable | 否 | **True** (預設值) 或 **False** 根據屬性是否有 null 值。 注意: 在所指的 CSDL 版本 [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) 命名空間、 複雜型別屬性必須具有可為 Null ="False"。 |
| DefaultValue | 否 | 屬性的預設值。 |
|MaxLength | 否 | 屬性值的長度上限 |
| FixedLength | 否 | **True** 或 **False** 根據屬性值是否會儲存為 fiexed 長度的字串。 |
| Precision | 否 | 指的是要在數值中保留的位數上限。 |
| 調整 | 否 | 要在數值中保留的小數位數上限。 |
| Unicode | 否 | **True** 或 **False** 根據屬性值是否為儲存為 Unicode 字串。 |
| Collation | 否 | 指定要在資料來源使用之定序的字串。 |
| ConcurrencyMode | 否 | **無** (預設值) 或 **固定**。 如果值設定為 **固定**, ，屬性值會用於開放式並行存取檢查。 |

以下是已加入至 CSDL 規格的其他屬性：

**d:Map** -對服務執行的 XPath 運算式輸出，並擷取輸出的一個屬性。 指定的 XPath 相對於已在 EntityType 節點的 XPath 中選取的重複節點。 此外，也可以指定絕對 XPath，以允許在每一個輸出節點中包括靜態資源，例如只在原始服務輸出中找到一次，但應該出現在 OData 輸出中每一個資料列的著作權陳述式。 來自服務的輸出：

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

這裡的 XPath 運算式將是./bar/baz0，可從內容提供者服務取得 baz0 節點。

**d:CharMaxLength** -字串型別，您可以指定最大長度。 請參閱 DataService CSDL 範例

**d:IsPrimaryKey** -指出資料行是否為資料表/檢視表的主索引鍵。 請參閱 DataService CSDL 範例。

**d:isExposed** -決定是否要公開資料表的結構描述 (通常為 true)。 請參閱 DataService CSDL 範例

**d:IsView** *(選擇性)* -如果這根據檢視，而不是資料表，則為 true。  請參閱 DataService CSDL 範例

**d:Tableschema** -請參閱 DataService CSDL 範例

**d:ColumnName** -資料表/檢視表中的資料行的名稱。  請參閱 DataService CSDL 範例

**d:IsReturned** -是布林值，是否此服務會公開給用戶端的這個值會決定。  請參閱 DataService CSDL 範例

**d:IsQueryable** -是布林值，該值決定資料行是否可以使用資料庫查詢中。   請參閱 DataService CSDL 範例

**d:OrdinalPosition** -這是資料行的數值位置的外觀，x，在資料表或檢視，其中 x 是從 1 到資料表中的資料行數目。  請參閱 DataService CSDL 範例

**d:DatabaseDataType** -會在資料庫中，也就是 SQL 資料型別資料行的資料型別。 請參閱 DataService CSDL 範例

## 支援的參數/屬性類型
以下是支援的參數和屬性類型。 (區分大小寫)

| 基本類型 | 說明 |
|----|----|
| Null | 表示缺少值 |
| Boolean | 代表二進位值邏輯的數學概念|
| 位元組 | 不帶正負號的 8 位元整數值|
|DateTime| 代表日期和時間，值的範圍從西元年 1 月 1，1753年午夜 12:00:00 到 透過下午 11:59:59，年 12 月到西元 9999|
|十進位 | 代表精確度和小數位數固定的數值。 此類型可以描述範圍從負 10 ^255 + 1 到正 10 ^255 - 1 的數值|
| 兩倍 | 代表具有 15 位數精確度的浮點數，可以代表近似範圍從 ± 2.23e -308 到 ± 1.79e +308 的值。 **由於 Exel 匯出問題使用 Decimal**|
| 單一 | 代表具有 7 位數精確度的浮點數，可以代表近似範圍從 ± 1.18e -38 到 ± 3.40e +38 的值。|
|Guid |代表 16 位元組 (128 位元) 的唯一識別碼值 |
|Int16|代表帶正負號的 16 位元整數值 |
|Int32|代表帶正負號的 32 位元整數值 |
|Int64|代表帶正負號的 64 位元整數值 |
|String | 代表固定或可變長度的字元資料 |


## 另請參閱
- 如果您有興趣了解整體的 OData 對應程序和用途，請閱讀這篇文章 [資料服務的 OData 對應](marketplace-publishing-data-service-creation-odata-mapping.md) 檢閱定義、 結構和指示。
- 如果您有興趣檢閱範例，請閱讀這篇文章 [資料服務 OData 對應範例](marketplace-publishing-data-service-creation-odata-mapping-examples.md) 查看範例程式碼，並了解程式碼語法與內容。
- 若要返回發佈至 Azure Marketplace 資料服務的指定路徑，請閱讀本文 [資料服務的發行指導](marketplace-publishing-data-service-creation.md)。

