<properties
    pageTitle="調整由 Azure SQL Database 支援的行動服務 | Microsoft Azure"
    description="了解如何診斷和修正 SQL Database 支援的行動服務中出現的延展性問題"
    services="mobile-services"
    documentationCenter=""
    authors="lindydonna"
    manager="dwrede"
    editor="mollybos"/>


<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="donnam;ricksal"/>

# 調整由 Azure SQL Database 支援的行動服務

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Azure 行動服務可讓您輕鬆地開始使用及建置連接到雲端主控後端的應用程式，將資料儲存在 SQL 資料庫中。 隨著應用程式的成長，您只需在入口網站中修改調整設定以增加更多計算和網路容量，即可調整您的服務執行個體。 但是，要對支援服務的 SQL 資料庫進行調整，必須仰賴主動式規劃和監視，因為服務將會承受更多負載。 本文將逐步引導您執行一組最佳作法，以確保 SQL 支援的行動服務能夠維持絕佳效能。

本主題將逐步引導您完成下列基本章節：

1. [診斷問題](#Diagnosing)
2. [編製索引](#Indexing)
3. [結構描述設計](#Schema)
4. [查詢設計](#Query)
5. [服務架構](#Architecture)
6. [進階疑難排解](#Advanced)

<a name="Diagnosing"></a>
## 診斷問題

如果您懷疑您的行動服務發生問題，在負載下的，若要檢查的第一個位置是 **儀表板** ] 索引標籤中為服務 [Azure classic portal]。 應確認的事項如下：

- 您的使用計量包括 **API 呼叫** 和 **的使用中裝置** 公尺未超出配額
- **端點監視** 狀態指出服務已啟動 (只有在服務使用標準層且 [端點監視已啟用)

如果上述任一種不成立，請考慮上修改調整設定 *延展* ] 索引標籤。 如果仍無法解決問題，您可以進一步調查問題是否來自於 Azure SQL Database。 後續幾節將說明幾種診斷可能錯誤的不同方法。

### 選擇正確的 SQL Database 層

請務必了解可供您使用的不同資料庫層，以確保您已根據應用程式的需求選擇了正確的層。 Azure SQL Database 提供三種不同的服務層：

- 基本
- 標準
- 高級


以下是您為資料庫選取適當的層時可參考的意見：

- **基本** -用於開發時期，或是小型生產服務在您只想要執行的單一資料庫查詢一次
- **標準** -用於生產服務預計多個並行資料庫查詢
- **高階** -用於具有許多並行查詢、 高度負載的大規模生產服務，且需有低延遲為每個要求。

如需何時使用每一層的詳細資訊，請參閱 [Reasons to Use the New Service Tiers]

### 分析資料庫度量

在您熟悉不同的資料庫層之後，我們即可探索資料庫效能度量，以利分析各層以內和之間的調整。

1. 啟動 [Azure classic portal]。
2. 在 [行動服務] 索引標籤上，選取您想要使用的服務。
3. 選取 **設定** ] 索引標籤。
4. 選取 **SQL Database** 名稱 **資料庫設定** 一節。 這會導覽至入口網站中的 [Azure SQL Database] 索引標籤。
5. 瀏覽至 **監視** ] 索引標籤
6. 確定相關度量皆會顯示使用 **加入度量** ] 按鈕。 請納入下列項目
    - *CPU 百分比* (僅適用於 Basic/Standard/Premium 層)

    - *資料 IO 百分比* (僅適用於 Basic/Standard/Premium 層) 
    - *記錄 IO 百分比* (僅適用於 Basic/Standard/Premium 層)
    - *儲存體* 
7. 在您的服務發生問題的期間查看度量。 

    ![Azure 傳統入口網站 - SQL Database 度量][PortalSqlMetrics]

如有任何度量長時間超過 80% 使用率，則可能表示效能有問題。 如需詳細了解資料庫使用率的詳細資訊，請參閱 [了解資源使用](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource)。

如果度量指出您的資料庫造成高使用率，請考慮 **向上擴充至更高的服務層資料庫** 作為第一個因應步驟。 若要立即解決問題，請考慮使用 **調整** 您向上擴充您資料庫的資料庫] 索引標籤。 這樣會增加您的費用。
![Azure 傳統入口網站-SQL Database Scale][PortalSqlScale]

請儘快考慮使用下列進一步的因應步驟：

- **調整您的資料庫。**
  通常在完成資料庫最佳化後即可降低資料庫使用率，而無須再調整至更高層。
- **請考慮您的服務架構。**
  您的服務負載長期來看很可能未平均分佈，而包含高度需求的「尖峰」。 與其向上擴充資料庫以處理尖峰，而使其在低需求期間變得使用率偏低，一般常會選擇調整服務架構以避免此類尖峰，或在不產生資料庫叫用的情況下加以處理。

本文的其他小節包含可協助您實作這些因應措施的適當指引。


### 設定警示

將關鍵資料庫度量的警示設定為主動步驟，以確保您有充裕的時間可因應資源耗盡的狀況，是很有用的。

1. 瀏覽至 **監視** 資料庫，您想要設定的警示] 索引標籤
2. 確定相關度量皆已顯示，如上一節所說明
3. 選取您想要設定警示，然後選取的度量 **新增規則**

    ![Azure Management Portal - SQL Alert][PortalSqlAddAlert]

4. 提供警示的名稱和描述
    ![Azure Management Portal - SQL Alert Name and Description][PortalSqlAddAlert2]

5. 指定要作為警示臨界值的值。 請考慮使用 **80%** 以便進行一些反應時間。 也請務必指定主動監視的電子郵件地址。
 
    ![Azure Management Portal - SQL Alert Threshold and Email][PortalSqlAddAlert3]


如需有關如何診斷 SQL 問題的詳細資訊，請參閱 [進階診斷](#AdvancedDiagnosing) 底部的 [此文件。

<a name="Indexing"></a>
## 編製索引

當您發現查詢效能出現問題時，您首先要調查的，應該是索引的設計。 索引會直接影響到 SQL 引擎執行查詢的情形，因此是很重要的。 

例如，如果您經常需要以特定欄位查閱某個元素，則應考慮為該資料行新增索引。 否則，SQL 引擎將會強迫執行資料表掃描並讀取每一筆實體記錄 (或至少讀取查詢資料行)，而記錄可能會蔓延到磁碟各處。

因此，如果您經常在特定資料行上執行 WHERE 或 JOIN 陳述式，則務必要編製其索引。 請參閱章節 [建立索引](#CreatingIndexes) 如需詳細資訊。

如果索引很好用，而資料表掃描很糟，這是否表示您應該為資料表中的每個資料行編製索引，以求保險？  簡言之，答案是「應該不用」。 索引會佔用空間，且本身會有額外負荷：每次在資料表中進行插入時，每個索引資料行的索引結構就必須更新一次。 請參閱以下有關於如何選擇資料行索引的指引。

### 索引設計指引

如前所述，在資料表中新增更多索引不見得有用，因為索引本身可能就是需要代價的，無論是在效能上，還是在儲存額外負荷上。

#### 查詢考量

- 考慮在經常用於述詞 (例如 WHERE 子句) 和聯結條件中的資料行內新增索引，同時權衡下列資料庫考量事項。
- 撰寫可在單一陳述式中盡可能插入或修改最多資料列的查詢，而不使用多個查詢來更新相同資料列。 在只有一個陳述式時，資料庫引擎較能最佳化它對索引的維護情形。

#### 資料庫考量

資料表上若有大量的索引，將會影響 INSERT、UPDATE、DELETE 和 MERGE 陳述式的效能，因為當資料表中的資料變更時，所有索引都必須適當地調整。

- 如 **頻繁更新** 資料表，請避免頻繁更新的資料行建立索引。
- 表格 **非經常更新** 但含有大量資料，請使用多個索引。 不會修改資料的查詢 (例如 SELECT 陳述式) 將可因此而改善效能，因為查詢最佳化工具將會有較多選項可尋找最理想的存取方法。

為小型資料表編製索引可能不是最佳作法，因為與執行簡易資料表掃描相較，查詢最佳化工具在周遊索引以搜尋資料時將花費更多時間。 因此，小型資料表上的索引可能永遠不會用到，但仍必須在資料表中的資料變更時進行維護。


<a name="CreatingIndexes"></a>
### 建立索引

#### JavaScript 後端

若要為 JavaScript 後端中的資料行設定索引，請執行下列動作：

1. 開啟您的行動服務中 [Azure classic portal]。
2. 按一下 [ **資料** ] 索引標籤。
3. 選取您要修改的資料表。
4. 按一下 [ **資料行** ] 索引標籤。
5. 選取資料行。 在命令列中，按一下 [ **設定索引**:

    ![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

您也可以移除此檢視的索引。

#### .NET 後端

若要定義 Entity Framework 中的索引，請在您要編製索引的欄位上使用屬性 `[Index]`。 例如：

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

        [Index]
        public bool Complete { get; set; }
    }

如需有關索引的詳細資訊，請參閱 [Entity Framework 中的索引註解] []。 如需最佳化索引的相關秘訣，請參閱 [進階索引](#AdvancedIndexing) 底部的 [此文件。

<a name="Schema"></a>
## 結構描述設計

以下是為您的物件選擇資料類型 (接著會轉譯為 SQL 資料庫的結構描述) 時應注意的幾個問題。 調整結構描述通常可以大幅提升效能，因為 SQL 具有自訂的最佳化方式，可為不同的資料類型處理索引和儲存工作：

- **使用提供的識別碼資料行**。 每個行動服務資料表都附有設定為主要索引鍵的預設識別碼資料行，且資料行上都設有索引。 您無須建立其他識別碼資料行。
- **在模型中使用正確的資料類型。**如果您知道模型的特定屬性將是數值或布林值，請務必在您的模型中將其定義為該形式，而不要定義為字串。 在 JavaScript 後端中，請使用 `true` (不要使用`"true"`) 和 `5` (不要使用 `"5"`) 之類的文字。 在 .NET 後端中，當您宣告模型的屬性時請使用 `int` 和 `bool` 類型。 這會讓 SQL 為這些類型建立正確的結構描述，而使查詢更具效益。

<a name="Query"></a>
## 查詢設計

以下是在查詢資料庫時所應考量的某些準則：

- **一律在資料庫中執行聯結作業。**您經常都會需要結合兩個或多個資料表合併的記錄位置共用相同的欄位的資料 (也稱為 *聯結*)。 這項作業牽涉到同時從兩個資料表中提取所有實體繼而逐一查看所有實體，因此若未正確執行，可能會缺乏效率。 此類作業最好留在資料庫中執行，但有時候卻很容易誤由用戶端執行，或是以行動服務程式碼執行。
    - 請勿在應用程式的程式碼中執行聯結
    - 請勿在您的行動服務程式碼中執行聯結。 當使用 JavaScript 後端，請留意， [table 物件](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) 不會處理聯結。 請務必使用 [mssql 物件](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx) 直接以確保聯結會在資料庫中。 如需詳細資訊，請參閱 [聯結關聯式資料表](mobile-services-how-to-use-server-scripts.md#joins)。 透過 LINQ 使用 .NET 後端和查詢時，Entity Framework 會自動在資料庫層級上處理節。
- **實作分頁。**查詢資料庫有時可能會導致大量記錄傳回至用戶端。 若要盡可能減少作業的大小和延遲，請考慮實作分頁。

    - 根據預設，您的行動服務會將任何傳入查詢的頁面大小限定為 50，而您可以手動要求提高到 1,000 筆記錄。 如需詳細資訊，請參閱 「 以分頁方式傳回資料 」 [Windows 市集](mobile-services-windows-dotnet-how-to-use-client-library.md#paging), ，[iOS](mobile-services-ios-how-to-use-client-library.md#paging), ，[Android](mobile-services-android-how-to-use-client-library.md#paging), ，[HTML/JavaScript](mobile-services-html-how-to-use-client-library#paging), ，和 [Xamarin](partner-xamarin-mobile-services-how-to-use-client-library.md#paging)。
    - 從行動服務程式碼發出的查詢並沒有預設頁面大小。 如果您的應用程式未實作分頁，或將其視為防護措施，請考慮將預設限制套用至您的查詢。 在 JavaScript 後端使用 **採取** 運算子 [查詢物件](http://msdn.microsoft.com/library/azure/jj613353.aspx)。 如果使用.NET 後端，請考慮使用 [Take method] LINQ 查詢的一部分。  


如需改善查詢設計，包括如何分析查詢計劃，請參閱 [進階查詢設計](#AdvancedQuery) 底部的 [此文件。

<a name="Architecture"></a>
## 服務架構

設想您將要傳送推播通知給您所有的客戶，以查看您應用程式中的某項新內容。 當他們點選通知時，應用程式將會啟動，而可能觸發對您行動服務的呼叫，以及對您的 SQL 資料庫的查詢執行。 由於在短短的幾分鐘內就可能有數百萬個客戶執行此動作，因此這將會產生 SQL 負載的暴衝，且比起應用程式的穩定狀態負載，可能會高出數倍。 在暴衝期間將您的應用程式調整至較高的 SQL 層，然後再重新將其向下調整，即可解決此問題，但此解決方案將需要手動操作，且會導致成本提高。 經常稍微查看您的行動服務架構，可有效地將用戶端產生的負載平衡到您的 SQL 資料庫，並且消除需求上有問題的暴衝。 這些修改通常可輕易地在盡可能不影響客戶使用經驗的情況下實作。 這裡有一些範例：

- **經過一段時間分散負載。**如果您對特定事件 (例如廣播推播通知) 的執行時機進行控制，並預期這些事件會產生需求上的暴衝，且這些事件的執行時機並不重要，請考慮將其分散到不同時間。 在前述範例中，或許您的應用程式客戶可以在一天的不同時間分批取得新應用程式內容的通知，而無需在幾乎相同的時間取得。 請考慮將您的客戶分成允許交錯傳遞至各個批次的群組。 在使用通知中心時，套用附加標記以追蹤批次，然後將推播通知傳遞至該標記，將可提供實作此策略的簡單途徑。 如需標記的詳細資訊，請參閱 [使用通知中心傳送即時新聞](../notification-hubs-windows-store-dotnet-send-breaking-news.md)。
- **Blob 和資料表儲存體在情況允許時使用。**通常，客戶在暴衝期間所將檢視的內容是較為靜態的，且不需要儲存在 SQL 資料庫中，因為您不可能需要對該項內容的關聯式查詢功能。 在此情況下，請考慮將內容儲存在 Blob 或資料表儲存體中。 您可以直接從裝置存取 Blob 儲存體中的公用 Blob。 若要以安全的方式存取 Blob 或使用資料表儲存體，您必須透過行動服務自訂 API 以保護您的儲存體存取金鑰。 如需詳細資訊，請參閱 [影像上傳至 Azure 儲存體使用行動服務](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)。
- **使用記憶體中快取**。 另一個替代方式是儲存資料，通常衝期間存取記憶體中快取在流量暴例如 [Azure 快取](http://azure.microsoft.com/services/cache/)。 這表示傳入的要求將可從記憶體中提取其所需的資訊，而無須重複查詢資料庫。

<a name="Advanced"></a>
## 進階疑難排解
本節說明一些進階的診斷工作，或許有助於處理目前的步驟無法完全解決的問題。

### 必要條件
若要執行本節中的某些診斷工作，您需要存取的管理工具 SQL 資料庫的這類 **SQL Server Management Studio** 或內建的管理功能 **Azure 傳統入口網站**。

SQL Server Management Studio 是一項免費的 Windows 應用程式，可提供最進階的功能。 如果您無法存取 Windows 機器 (例如，如果您使用 Mac)，請考慮佈建虛擬機器在 Azure 中所示 [建立虛擬機器執行 Windows Server](../virtual-machines-windows-tutorial.md) ，然後從遠端連接。 如果您想要使用 VM 的主要目的是執行 SQL Server Management Studio， **基本 A0** (先前稱為 「 超小型 」) 執行個體應已足夠。

Azure 傳統入口網站提供內建的管理功能，雖然功能有限，但不需本機安裝即可使用。

下列步驟將引導您針對支援行動服務的 SQL 資料庫取得連線資訊，然後使用前述兩項工具之一來連接。 您可以選擇您想用的任一工具。

#### 取得 SQL 連線資訊
1. 啟動 [Azure classic portal]。
2. 在 [行動服務] 索引標籤上，選取您想要使用的服務。
3. 選取 **設定** ] 索引標籤。
4. 選取 **SQL Database** 名稱 **資料庫設定** 一節。 這會導覽至入口網站中的 [Azure SQL Database] 索引標籤。
5. 選取 **設定 Azure 防火牆規則，此 IP 位址的**。
6. 請記下中的伺服器位址 **連接到您的資料庫** 區段，例如: *mcml4otbb9.database.windows.net*。

#### SQL Server Management Studio
1. 瀏覽至 [SQL Server 版本-Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. 尋找 **SQL Server Management Studio** 區段，然後選取 **下載** 按鈕下方。
3. 執行設定步驟，直到您可以成功執行應用程式為止：

    ![SQL Server Management Studio][SSMS]

4. 在 **連接到伺服器** 對話方塊中，輸入下列值
    - 伺服器名稱: *您稍早取得的伺服器位址*
    - 驗證: *SQL Server 驗證*
    - 登入: *您在建立伺服器時選擇的登入*
    - 密碼: *您在建立伺服器時選擇的密碼*
5. 您現在應已連線。

#### SQL Database 管理入口網站
1. 在 Azure SQL 資料庫] 索引標籤，為您的資料庫，選取 [ **管理** 按鈕
2. 使用下列值來設定連線
    - 伺服器: *應該預先設定為正確值*
    - 資料庫: *留白*
    - 使用者名稱: *您在建立伺服器時選擇的登入*
    - 密碼: *您在建立伺服器時選擇的密碼*
3. 您現在應已連線。

    ![Azure 傳統入口網站 - SQL Database][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### 進階診斷

您可以輕鬆地直接完成許多診斷工作中 **Azure 傳統入口網站**, ，但有些進階診斷工作則只能透過 **SQL Server Management Studio** 或 **SQL Database 管理入口網站**。  我們將利用動態管理檢視的功能，這是一組會以資料庫的相關診斷資訊自動填入的檢視。 本節將提供一組可用來對這些檢視執行以檢查各種度量的查詢。 如需詳細資訊，請參閱 [資料庫使用動態管理檢視監視 SQL][]。

完成之後連線到 SQL Server Management Studio 中的資料庫上一節中的步驟，選取您的資料庫 **物件總管] 中**。 展開 **檢視** 然後 **系統檢視表** 會顯現管理檢視清單。 若要執行下方的查詢，選取 **新查詢**, ，而您已選取您的資料庫 **物件總管] 中**, ，然後貼上查詢，並選取 **Execute**。

![SQL Server management Studio - dynamic management views][SSMSDMVs]

或者如果您使用 SQL Database 管理入口網站，先選取您的資料庫，然後選擇 **新查詢**。

![SQL Database Management Portal - new query][PortalSqlManagementNewQuery]

若要執行的任何下列查詢，將它貼到視窗，並選取 **執行**。

![SQL Database Management Portal - run query][PortalSqlManagementRunQuery]

#### 進階度量


如果您使用 Basic、Standard 和 Premium 等層，則管理入口網站會使特定度量可供使用。 很容易取得這些度量和其他度量使用 **[sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)** 管理] 檢視中的，不論您層使用。 請考量下列查詢：

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'todoitem_db'
    ORDER BY start_time DESC

> [AZURE.NOTE]
> 請執行這個查詢 **主要** 在伺服器上的資料庫 **sys.resource\_stats** 檢視只會出現在該資料庫。

結果將會包含下列好用的度量：CPU (層限制百分比)、儲存體 (MB)、實體資料讀取 (層限制百分比)、記錄寫入 (層限制百分比)、記憶體 (層限制百分比)、背景工作計數、工作階段計數等。

#### SQL 連線事件

 **[Sys.event\_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** 檢視包含連線相關事件的詳細資料。

    select * from sys.event_log
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE]
> 請執行這個查詢 **主要** 在伺服器上的資料庫 **sys.event\_log** 檢視只會出現在該資料庫。

<a name="AdvancedIndexing" ></a>
### 進階索引

資料表或檢視可包含下列類型的索引：

- **叢集**。 叢集索引會指定記錄實際儲存在磁碟上的方式。 每個資料表只能有一個叢集索引，因為資料列本身只能以一種順序來排序。

- **非叢集**。 非叢集索引會與資料列分開儲存，可用來根據索引值執行查詢。 資料表上的所有非叢集索引都會以叢集索引中的索引鍵值作為查閱索引鍵。

提供實際比喻：試想書籍或技術手冊。 每一頁的內容都是一筆記錄，頁碼是叢集索引，而書後的主題索引則是非叢集索引。 主題索引中的每個項目都會指向叢集索引，即頁碼。

> [AZURE.NOTE]
> 根據預設，Azure 行動服務的 JavaScript 後端設定 **\_createdAt** 與叢集索引。 如果您移除此資料行，或您想要不同的叢集的索引，請務必遵循 [叢集索引設計指導方針](#ClusteredIndexes) 下方。 在 .NET 後端中，類別 `EntityData` 會使用註解 `[Index(IsClustered = true)]` 將 `CreatedAt` 定義為叢集索引。

<a name="ClusteredIndexes"></a>
#### 叢集索引設計指引

每個資料表都應在具有下列屬性的一個資料行 (如果是複合索引鍵，則為多個資料行) 上具有叢集索引：

- 縮窄-使用小型資料類型，或屬於 [複合索引鍵][Primary and Foreign Key Constraints] 縮窄資料行的小的數字
- 唯一，或接近唯一
- 靜態 - 值不常變更
- 持續增加
- (選用) 固定寬度
- (選用) 非 null

原因 **縮小** 屬性，是所有其他索引的資料表上使用叢集索引中的索引鍵值作為查閱索引鍵。 在書後主題索引的範例中，叢集索引是頁碼，是一個小數字。 如果改將章節標題納入叢集索引中，則主題索引本身將會長得多，因為此時的索引鍵值將是 (章節名稱, 頁碼)。

這個金鑰應該為 **靜態** 和 **不斷增加** 以避免需要維護記錄的實體位置 (意指實際移動記錄，或可能因為儲存體分隔記錄的儲存位置的頁面而分散)。

叢集索引對於執行下列工作的查詢最具效益：

- 使用 BETWEEN、>、>=、< 和 <= 等運算子，傳回某個範圍內的值。
    - 在使用叢集索引找到具有第一個值的資料列後，具有後續索引值的資料列必定會在相鄰的位置。
- 使用 JOIN 子句；這些通常是外部索引鍵資料行。
- 使用 ORDER BY 或 GROUP BY 子句。
    - 在資料行上以 ORDER BY 或 GROUP BY 子句指定的索引可讓 Database Engine 無須排序資料，因為資料列已有排序。 這可以改善查詢效能。

#### 在 Entity Framework 中建立叢集索引

若要使用 Entity Framework 在 .NET 後端中設定叢集索引，請設定註解的 `IsClustered` 屬性。 例如，以下是 `Microsoft.WindowsAzure.Mobile.Service.EntityData` 中的 `CreatedAt` 定義：

    [Index(IsClustered = true)]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    [TableColumnAttribute(TableColumnType.CreatedAt)]
    public DateTimeOffset? CreatedAt { get; set; }

#### 在資料庫結構描述中建立索引

對於 JavaScript 後端，您只能透過 SQL Server Management Studio 或 Azure SQL 資料庫入口網站直接變更資料庫結構描述，以修改資料表的叢集索引。

下列指南說明如何藉由直接修改資料庫結構描述，來設定叢集或非叢集索引：

- [建立和修改 PRIMARY KEY 條件約束][]
- [建立非叢集索引][]
- [建立叢集的索引][]
- [建立唯一索引][]

#### 尋找前 N 個遺漏索引
您可以在動態管理檢視上撰寫 SQL 查詢，以通知您與個別查詢的資源使用情形有關的詳細資訊，或引導您找出所應新增的索引。 下列查詢會決定哪 10 個遺漏的索引會為使用者查詢產生最高的預期累積改進 (採用遞減順序)。

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

下列範例查詢會在這些資料表間執行聯結，以取得應屬於各個遺漏索引的資料行清單，並計算「索引效益」以判斷是否應考慮使用給定的索引：

    SELECT * from
    (
        SELECT
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

如需詳細資訊，請參閱 [資料庫使用動態管理檢視監視 SQL][] 和 [遺漏索引動態管理檢視][]。

<a name="AdvancedQuery" ></a>
### 進階查詢設計 

一般而言，要診斷出哪些查詢對資料庫而言的成本最高，並不是容易的事。 


#### 尋找前 N 個查詢

下列範例會傳回以平均 CPU 時間排名的前五個查詢的相關資訊。 此範例會根據查詢雜湊來彙總查詢，使在邏輯上等同的查詢依其累積資源耗用量進行分組。

    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
        SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
        MIN(query_stats.statement_text) AS "Statement Text"
    FROM
        (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
            WHEN -1 THEN DATALENGTH(st.text)
            ELSE QS.statement_end_offset END
                - QS.statement_start_offset)/2) + 1) AS statement_text
         FROM sys.dm_exec_query_stats AS QS
         CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;

如需詳細資訊，請參閱 [資料庫使用動態管理檢視監視 SQL][]。 除了執行查詢， **SQL Database 管理入口網站** 可讓您選取檢視這項資料的理想捷徑 **摘要** 為您的資料庫，然後選取 **查詢效能**:

![SQL Database Management Portal - query performance][PortalSqlManagementQueryPerformance]

#### 分析查詢計劃

一旦您已識別出且費時的查詢或是您即將部署程式碼使用新查詢，而且您想要調查其效能，這項工具會提供絕佳支援分析 **查詢計劃**。 查詢計劃可讓您確認哪些作業在給定的 SQL 查詢執行時耗用了大量 CPU 時間和 IO 資源。 若要分析查詢計劃中的 **SQL Server Management Studio**, ，請使用強調顯示的工具列按鈕。

![SQL Server Management Studio - query plan][SSMSQueryPlan]

若要分析查詢計劃中的 **SQL Database 管理入口網站**, ，您可以使用反白顯示的工具列按鈕。

![SQL Database Management Portal - query plan][PortalSqlManagementQueryPlan]

## 另請參閱

- [Azure SQL Database 文件][]
- [Azure SQL Database 效能和調整][]
- [Azure SQL Database 的疑難排解][]

### 編製索引

- [索引基本概念][]
- [一般索引設計指引][]
- [唯一索引設計指引][]
- [叢集索引設計指引][]
- [主索引鍵和外部索引鍵條件約束][]
- [為何該索引鍵的成本?][]

### Entity Framework
- [Entity Framework 5 的效能考量][]
- [Code First 資料註解][]

<!-- IMAGES -->

[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[Azure classic portal]: http://manage.windowsazure.com

[Azure SQL Database Documentation]: http://azure.microsoft.com/documentation/services/sql-database/
[Managing SQL Database using SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Monitoring SQL Database Using Dynamic Management Views]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Azure SQL Database performance and scaling]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Troubleshooting Azure SQL Database]: http://msdn.microsoft.com/library/azure/ee730906.aspx
[Reasons to Use the New Service Tiers]:http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons

[Take method]:http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx

<!-- MSDN -->
[Creating and Modifying PRIMARY KEY Constraints]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[Create Clustered Indexes]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[Create Unique Indexes]: http://technet.microsoft.com/library/ms187019.aspx
[Create Nonclustered Indexes]: http://technet.microsoft.com/library/ms189280.aspx

[Primary and Foreign Key Constraints]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Index Basics]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[General Index Design Guidelines]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx
[Unique Index Design Guidelines]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[Clustered Index Design Guidelines]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[Missing Index Dynamic Management Views]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Performance Considerations for Entity Framework 5]: http://msdn.microsoft.com/data/hh949853
[Code First Data Annotations]: http://msdn.microsoft.com/data/jj591583.aspx
[Index Annotations in Entity Framework]:http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[How much does that key cost?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/


