<properties
    pageTitle="在 Azure App Service 中建立 .NET WebJob | Microsoft Azure"
    description="使用 ASP.NET MVC 和 Azure 建立多層式應用程式。 前端是在 Azure App Service 的 Web 應用程式中執行，後端則是以 WebJob 形式執行。 該應用程式使用 Entity Framework、SQL Database 及 Azure 儲存體佇列和 Blob。"
    services="app-service"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/14/2015"
    ms.author="tdykstra"/>

# 在 Azure App Service 中建立 .NET WebJob

本教學課程示範如何撰寫簡單的多層式 ASP.NET MVC 5 應用程式使用的程式碼 [WebJobs SDK](websites-dotnet-webjobs-sdk.md)。

目的 [WebJobs SDK](websites-webjobs-resources.md) 是為了簡化您撰寫的一般工作的可執行的 web 工作，例如映像處理、 佇列處理、 RSS 彙總檔案維護的程式碼，並傳送電子郵件。 WebJobs SDK 具有內建的功能，可與「Azure 儲存體」和「服務匯流排」搭配運作、用來排定工作和處理錯誤，以及處理許多其他常見的案例。 此外，它是設計為具有擴充性，而且沒有 [擴充功能的開放原始碼儲存機制](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。

此範例應用程式是廣告看板。 使用者可以上傳廣告的影像，然後後端程序會將影像轉換成縮圖。 廣告清單頁面會顯示縮圖，而廣告詳細資料頁面則會顯示完整大小的影像。 以下為螢幕擷取畫面：

![Ad list](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

此範例應用程式的運作方式與 [Azure 佇列](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) 和 [Azure blob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)。 本教學課程示範如何部署應用程式以 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 和 [Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336279)。

## <a id="prerequisites"></a>必要條件

此教學課程假設您知道如何使用 [ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) Visual Studio 中的專案。

本教學課程是針對 Visual Studio 2013 所撰寫。 如果您尚未有 Visual Studio，則在安裝 Azure SDK for .NET 時，將自動為您安裝它。

本教學課程可以搭配 Visual Studio 2015 使用，但在本機執行應用程式之前，您必須將 Web.config 和 App.config 檔案中 SQL Server LocalDB 連接字串的 `Data Source` 部分，從 `Data Source=(localdb)\v11.0` 變更為 `Data Source=(LocalDb)\MSSQLLocalDB`。 

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note2.md)]

## <a id="learn"></a>您將學到什麼

本教學課程說明如何執行下列工作：

* 安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
* 建立會在您部署相關的 Web 專案時，自動部署成為 Azure WebJob 的主控台應用程式專案。
* 在開發電腦上本機測試 WebJobs SDK 後端。
* 將具有 WebJob 後端的應用程式發佈至 App Service 中的 Web 應用程式。
* 上傳檔案，並將檔案儲存在 Azure Blob 服務。
* 運用 Azure WebJobs SDK 來使用 Azure 儲存體佇列和 Blob。

## <a id="contosoads"></a>應用程式架構

範例應用程式使用 [佇列為中心的工作模式](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) ，將建立縮圖後, 端程序的 CPU 運算密集工作轉變。

本應用程式會將廣告儲存在 SQL 資料庫中，使用 Entity Framework Code First 來建立表格和存取資料。 針對每個廣告，資料庫會儲存兩個 URL：一個用於完整大小的影像，而另一個用於縮圖。

![Ad table](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

當使用者上傳映像時，web 應用程式會將影像儲存在 [Azure blob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage), ，並將廣告資訊儲存在資料庫中，以指向該 blob 的 URL。 同時會將訊息寫入 Azure 佇列。 在以 Azure WebJob 形式執行的後端處理中，WebJobs SDK 會輪詢佇列以尋找新訊息。 出現新訊息時，WebJob 便會建立該影像的縮圖，並更新該廣告的縮圖 URL 資料庫欄位。 以下圖表顯示應用程式的這些部分的互動情況：

![Contoso Ads architecture](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk](../../includes/install-sdk-2015-2013.md)]

本教學課程的指示適用於 Azure SDK for.NET 2.7.1 或更新版本。

## <a id="storage"></a>建立 Azure 儲存體帳戶

Azure 儲存體帳戶可提供在雲端中儲存佇列和 Blob 資料的資源。 WebJobs SDK 也會使用該帳戶來儲存儀表板的記錄資料。

在真實世界應用程式中，您通常會為應用程式資料與記錄資料建立不同的帳戶，並為測試資料與生產資料建立不同的帳戶。 針對本教學課程，您將只會使用一個帳戶。

1. 開啟 **伺服器總管** Visual Studio 視窗中的。

2. 以滑鼠右鍵按一下 **Azure** 節點，然後再按一下 **連線到 Microsoft Azure**。
![連接到 Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. 使用您的 Azure 認證登入。

5. 以滑鼠右鍵按一下 **儲存體** Azure 節點，然後按下 **建立儲存體帳戶**。
![建立儲存體帳戶](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)

3. 在 **建立儲存體帳戶** ] 對話方塊中，輸入儲存體帳戶的名稱。

    這個名稱必須是唯一的 (其他 Azure 儲存體帳戶不可以有相同的名稱)。 如果您輸入的名稱已在使用中，您可以變更此名稱。

    若要存取儲存體帳戶的 URL 會是 *{name}*。.core.windows.net。

5. 設定 **區域或同質群組** 到最接近您區域的下拉式清單。

    此設定會指定哪個 Azure 資料中心將會主控您的儲存體帳戶。 對於本教學課程，您的選擇將不會造成顯著的差異。 不過，對於生產 Web 應用程式，您會希望 Web 伺服器和儲存體帳戶都位於相同區域，以減少延遲和資料輸出費用。 您稍後將建立的 Web 應用程式資料中心應該盡可能接近可存取您 Web 應用程式的瀏覽器，以便將延遲降至最低。

6. 設定 **複寫** 下拉式清單來 **本機備援**。

    對儲存體帳戶啟用地理區域複寫時，儲存內容會複寫至次要資料中心，以便能在主要位置發生嚴重災難時容錯移轉至該位置。 地理區域複寫會引發額外成本。 對於測試和開發帳戶，您通常不會想要付費使用地理區域複寫功能。 如需詳細資訊，請參閱 [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

5. 按一下 [ **建立**。

    ![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

## <a id="download"></a>下載應用程式

1. 下載並解壓縮 [已完成的方案](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)。

2. 啟動 Visual Studio。

3. 從 **檔案** ] 功能表中選擇 **開啟 > 專案/方案**, 、 瀏覽至方案的下載位置，然後再開啟方案檔。

4. 按 CTRL+SHIFT+B 建置解決方案。

    根據預設，Visual Studio 會自動還原未包含在 NuGet 封裝內容 *.zip* 檔案。 如果封裝未還原，手動進行安裝，請前往 **管理方案的 NuGet 封裝** 對話方塊，然後按一下 **還原** 右上方的按鈕。

5. 在 **方案總管] 中**, ，請確定 **ContosoAdsWeb** 選取為啟動專案。

## <a id="configurestorage"></a>設定應用程式以使用儲存體帳戶

1. 開啟應用程式 *Web.config* 在 ContosoAdsWeb 專案中的檔案。

    此檔案包含 SQL 連接字串和用來使用 Blob 和佇列的 Azure 儲存體連接字串。

    SQL 連接字串指向 [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) 資料庫。

    儲存體連接字串是具有儲存體帳戶名稱和存取金鑰預留位置的範例。 您將會使用具有您儲存體帳戶名稱和金鑰的連接字串來進行取代。  

    <pre class="prettyprint">& l t; connectionStrings & gt;
      & lt; 新增名稱 ="ContosoAdsContext"connectionString ="資料來源 = (localdb) \v11.0;Initial Catalog = ContosoAds;整合式安全性 = True;MultipleActiveResultSets = True;"providerName="System.Data.SqlClient"/ & gt;
      & l t; 新增名稱 ="AzureWebJobsStorage"connectionString ="DefaultEndpointsProtocol = https;AccountName =<mark>[] accountname</mark>;AccountKey =<mark>[] accesskey</mark>"/ & gt;
    & l t; / connectionStrings & gt;</pre>

    儲存體連接字串的名稱是 AzureWebJobsStorage，因為這是 WebJobs SDK 預設使用的名稱。 這裡會使用相同的名稱，因此在 Azure 環境中您只需要設定一個連接字串值。

2. 在 **伺服器總管**, ，以滑鼠右鍵按一下您的儲存體帳戶 **儲存體** 節點，然後再按一下 **屬性**。

    ![按一下 [儲存體帳戶] 屬性](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)

3. 在 **屬性** ] 視窗中，按一下 [ **儲存體帳戶金鑰**, ，然後按一下省略符號。

    ![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

4. 複製 **連接字串**。

    ![ [儲存體帳戶金鑰] 對話方塊](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)

5. 中的儲存體連接字串來取代 *Web.config* 您剛才複製的連接字串的檔案。 在貼上之前請確定您選取引號內的所有項目，但不包括引號。

6. 開啟 *App.config* 在 ContosoAdsWebJob 專案中的檔案。

    此檔案有兩個儲存體連接字串，一個供應用程式使用，另一個供記錄使用。 您可以使用不同的儲存體帳戶的應用程式資料和記錄，以及您可以使用 [資料的多個儲存體帳戶](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)。 在本教學課程中，您將使用單一儲存體帳戶。 連接字串具有儲存體帳戶金鑰的預留位置。 
    <pre class="prettyprint">& l t; 組態 & gt;
    & l t; connectionStrings & gt;
        & l t; 新增名稱 ="AzureWebJobsDashboard"connectionString ="DefaultEndpointsProtocol = https;AccountName =<mark>[] accountname</mark>;AccountKey =<mark>[] accesskey</mark>"/ & gt;
        & l t; 新增名稱 ="AzureWebJobsStorage"connectionString ="DefaultEndpointsProtocol = https;AccountName =<mark>[] accountname</mark>;AccountKey =<mark>[] accesskey</mark>"/ & gt;
        & lt; 新增名稱 ="ContosoAdsContext"connectionString ="資料來源 = (localdb) \v11.0;Initial Catalog = ContosoAds;整合式安全性 = True;MultipleActiveResultSets = True;"/ （& s) gt;
    & l t; / connectionStrings & gt;
        & l t; 啟動 & gt;
            & l t; U 版本 ="v4.0"sku ="。NETFramework，Version = 為 4.5 版 」 / & gt;
    & l t; / 啟動 & gt;
& l t; / 組態 & gt;</pre>

    依預設，WebJobs SDK 會尋找名為 AzureWebJobsStorage 和 AzureWebJobsDashboard 的連接字串。 或者，您可以 [儲存連接字串，不過您想要並明確地將它傳入 `JobHost` 物件](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#config)。

7. 使用您先前複製的連接字串來取代這兩個儲存體連接字串。

8. 儲存您的變更。

## <a id="run"></a>在本機執行應用程式

1. 若要啟動應用程式的 Web 前端，請按 CTRL+F5。

    預設瀏覽器便會開啟到首頁。 (系統即會執行 Web 專案，這是因為您已將它設定為啟始專案。)

    ![Contoso Ads home page](./media/websites-dotnet-webjobs-sdk-get-started/home.png)

2. 若要啟動應用程式的 WebJob 後端，以滑鼠右鍵按一下 ContosoAdsWebJob 專案中的 **方案總管] 中**, ，然後按一下 [ **偵錯** > **開始新執行個體**。

    主控台應用程式視窗隨即開啟，並顯示指出 WebJobs SDK JobHost 物件已開始執行的記錄訊息。

    ![Console application window showing that the backend is running](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)

3. 在瀏覽器中，按一下 [  **建立廣告**。

4. 輸入部分測試資料，然後選取要上傳，然後按一下 [映像 **建立**。

    ![Create page](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

    應用程式會進入索引頁面，但不會顯示新廣告的縮圖，因為處理尚未發生。

    同時，不久之後，主控台應用程式視窗中的記錄訊息會顯示已收到佇列訊息並已開始處理。

    ![Console application window showing that a queue message has been processed](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)

5. 在看到主控台應用程式視窗中的記錄訊息後，您可以重新整理索引頁面以查看縮圖。

    ![索引頁面](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

6. 按一下 [ **詳細資料** 廣告以查看完整大小的影像。

    ![Details page](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

您一直在本機電腦上執行應用程式，並使用位於電腦上的 SQL Server 資料庫，但它會使用雲端中的佇列和 Blob。 在下一節中，您將會使用雲端資料庫以及雲端 Blob 和佇列，在雲端中執行應用程式。  

## <a id="runincloud"></a>在雲端中執行應用程式

您將執行下列步驟，在雲端中執行應用程式：

* 部署至 Web Apps。 Visual Studio 會在 App Service 和 SQL 資料庫執行個體中自動建立新的 Web 應用程式。
* 設定 Web 應用程式來使用您的 Azure SQL Database 和儲存體帳戶。

在建立一些廣告並在雲端中執行後，您將檢視 WebJobs SDK 儀表板，以查看儀表板所提供的豐富監視功能。

### 部署至 Web Apps

1. 關閉瀏覽器和主控台應用程式視窗。

2. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 ContosoAdsWeb 專案，然後按 **發行**。

3. 在 **設定檔** 步驟 **發行 Web** 精靈] 中，按一下 [ **Microsoft Azure web 應用程式**。

    ![選取 Azure 網站發佈目標](./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png)

4. 如果您仍未登入 Azure，請登入。

5. 按一下 [ **新**。

    對話方塊看起來可能會稍有不同，視您已安裝的 Azure SDK for.NET 版本而定。

    ![Click New](./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png)

6. 在 **Microsoft Azure 上的建立 web 應用程式** ] 對話方塊中，輸入中的唯一名稱 **Web 應用程式名稱** 方塊。

    完整的 URL 將包含您在此處輸入加上。 azurewebsites.net (旁邊所示 **Web 應用程式名稱** 文字方塊)。 例如，如果 Web 應用程式名稱是 ContosoAds，則 URL 會是 ContosoAds.azurewebsites.net。

7. 在 [應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 下拉式清單中，選擇 **建立新的應用程式服務方案**。 輸入 App Service 方案名稱 (例如 ContosoAdsPlan)。

8. 在 [資源群組](../resource-group-overview.md) 下拉式清單中，選擇 **建立新的資源群組**。

9. 輸入資源群組名稱 (例如 ContosoAdsGroup)。

10. 在 **區域** 下拉式清單中，選擇您所選擇的儲存體帳戶的相同區域。

    這個設定會指定 Web 應用程式將執行所在的 Azure 資料中心。 將 Web 應用程式和儲存體帳戶維持在相同資料中心內，可將延遲和資料輸出費用降到最低。

11. 在 **資料庫伺服器** 下拉式清單中，選擇 **建立新的伺服器**。

12. 輸入資料庫伺服器的名稱 (例如 contosoadsserver + 數字) 或您的名稱，使伺服器名稱成為唯一的名稱。 

    伺服器名稱必須是唯一的。 它可以包含小寫字母、數字和連字號。 不能包含結尾連字號。 

    或者，如果您的訂閱已有伺服器，您可以從下拉式清單選取該伺服器。

12. 輸入系統管理員 **資料庫使用者名稱** 和 **資料庫密碼**。

    如果您選取 **新的 SQL 資料庫伺服器** 不輸入現有的名稱和密碼，而輸入新名稱和密碼，您現在定義以未來存取資料庫時使用。 如果選取先前建立的伺服器，系統會提示您提供先前建立之管理使用者帳戶的密碼。

13. 按一下 [ **建立**。

    ![在 Microsoft Azure 對話方塊上建立 Web 應用程式](./media/websites-dotnet-webjobs-sdk-get-started/newdb.png)

    Visual Studio 便會建立方案、Web 專案、Azure 中的 Web 應用程式，以及 Azure SQL Database 執行個體。

14. 在 **連接** 步驟 **發行 Web** 精靈] 中，按一下 [ **下一步**。

    ![Connection step](./media/websites-dotnet-webjobs-sdk-get-started/connstep.png)

15. 在 **設定** 步驟中，清除 **在執行階段使用此連接字串** 核取方塊，，然後按一下 [ **下一步**。

    ![Settings step](./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png)

    您無需使用發行對話方塊來設定 SQL 連接字串，因為您稍後將在 Azure 環境中設定該值。

    您可以忽略此頁面上的警告。

    * 通常，您在 Azure 中執行時所用的儲存體帳戶會不同於您在本機執行時所用的儲存體帳戶，但在本教學課程中，您將在這兩個環境中使用相同的儲存體帳戶。 因此您無需轉換 AzureWebJobsStorage 連接字串。 即使您想在雲端中使用不同的儲存體帳戶，因為應用程式在 Azure 中執行時會使用 Azure 環境設定，所以您無需轉換連接字串。 您稍後會在教學課程中看到此狀況。

    * 在本教學課程中，您不打算變更 ContosoAdsContext 資料庫所用的資料模型，因此無需使用 Entity Framework Code First Migrations 來進行部署。 Code First 會在應用程式首次嘗試存取 SQL 資料時，自動建立新的資料庫。

    本教學課程中，預設值下的選項 **檔案發行選項** 就很適合。

16. 在 **預覽** 步驟中，按一下 **開始預覽**。

    ![Click Start Preview](./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png)

    您可以忽略有關未發行任何資料庫的警告。 Entity Framework Code First 會建立資料庫；因此無需發佈資料庫。

    預覽視窗會顯示二進位和 WebJob 專案的組態檔會複製到 *app_data\jobs\continuous* 的 web 應用程式的資料夾。

    ![WebJobs files in preview window](./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png)

17. 按一下 [ **發行**。

    Visual Studio 會部署應用程式，並在瀏覽器中開啟首頁 URL。

    您將無法使用該 Web 應用程式，直到您在下一節中設定 Azure 環境中的連接字串為止。 您將會看到一個錯誤頁面或首頁，視您稍早所選擇的 Web 應用程式和資料庫建立選項而定。

### 設定 Web 應用程式來使用您的 Azure SQL Database 和儲存體帳戶。

它是安全性最佳作法 [避免將敏感資訊，例如連接字串放在檔案儲存於原始程式碼儲存機制中](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets)。 Azure 提供實作上述最佳做法的方式：您可以在 Azure 環境中設定連接字串和其他設定值，當應用程式在 Azure 中執行時，ASP.NET 組態 API 便會自動挑選這些值。 您也可以使用在 Azure 中設定這些值 **伺服器總管**, ，Azure 入口網站、 Windows PowerShell 或跨平台命令列介面。 如需詳細資訊，請參閱 [應用程式字串與連接字串的運作](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)。

本節中使用 **伺服器總管** 在 Azure 中設定連接字串值。

7. 在 **伺服器總管**, ，以滑鼠右鍵按一下 web 應用程式在 **Azure > {您的資源群組}**, ，然後按一下 [ **檢視設定**。

     **Azure Web 應用程式** 視窗會開啟 **組態** ] 索引標籤。

9. 將 DefaultConnection 連接字串的名稱變更為 ContosoAdsContext。

    Azure 便會在您建立 Web 應用程式和相關資料庫時自動建立此連接字串，因此它已包含正確的連接字串值。 您只是單純將名稱變更為程式碼要尋找的名稱。

9. 新增兩個新的連接字串，將他們命名為 AzureWebJobsStorage 和 AzureWebJobsDashboard。 將類型設定為 [自訂]，並將連接字串值設為您稍早所用的相同值 *Web.config* 和 *App.config* 檔案。 (請確定您包含整個連接字串，而不只是存取金鑰而已，並且不要包含引號。)

    WebJobs SDK 會使用這些連接字串，一個供應用程式資料使用，一個供記錄使用。 如稍早所看到的，供應用程式資料使用的連接字串也會提供給 Web 前端程式碼使用。

9. 按一下 [ **儲存**。

    ![Azure 入口網站中的連接字串](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)

10. 在 **伺服器總管**, ，以滑鼠右鍵按一下 web 應用程式，然後按一下 **停止**。

12. Web 應用程式停止之後，再次以滑鼠右鍵按一下 web 應用程式，然後按一下 **啟動**。

    發行時，WebJob 便會自動啟動，但在您進行組態變更時便會停止。 重新啟動，您可以重新啟動 web 應用程式，或重新啟動在 WebJob [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。 通常建議您在進行設定變更之後重新啟動 Web 應用程式。

9. 重新整理網址列中具有 Web 應用程式 URL 的瀏覽器視窗。

    首頁便會隨即出現。

10. 建立廣告，就像您在本機執行應用程式時所做的一樣。

    一開始，顯示的索引頁面沒有縮圖。

11. 在幾秒後重新整理頁面，縮圖便會隨即出現。

    如果未出現縮圖，您可能必須等候一分鐘左右，讓 WebJob 重新啟動。 如果在一段時間後當您重新整理頁面時仍未看到縮圖，WebJob 可能未自動啟動。 在此情況下，請移至 WebJobs 索引標籤中 [傳統入口網站](https://manage.windowsazure.com) web 應用程式頁面，然後按一下 **啟動**。

### 檢視 WebJobs SDK 儀表板

1. 在 [傳統入口網站](https://manage.windowsazure.com), ，選取您的 web 應用程式。

2. 按一下 [ **WebJobs** ] 索引標籤。

3. 在您的 WebJob 中，按一下記錄欄中的 URL。

    ![WebJobs 索引標籤](./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png)

    新的瀏覽器索引標籤即會開啟到 WebJobs SDK 儀表板。 儀表板會顯示 WebJob 正在執行中，並顯示在 WebJobs SDK 所觸發之程式碼中的函數清單。

4. 按一下其中一個函式，以查看其執行的詳細資料

    ![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)

    ![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)

     **轉送函數** 本頁] 按鈕會造成 WebJobs SDK 架構再次呼叫函數，並可提供您一個機會來變更首先傳送至函式的資料。

>[AZURE.NOTE] 當您完成測試時，刪除 web 應用程式和 SQL Database 執行個體。 Web 應用程式是免費提供的，但 SQL Database 執行個體和儲存體帳戶則會累算費用 (由於是小規模，因此將收取基本費用)。 另外，如果您持續執行 Web 應用程式，那麼，找到您 URL 的任何人都可以建立和檢視廣告。 在傳統的入口網站，請移至 **儀表板** web 應用程式索引標籤，然後按一下 **刪除** 在頁面底部的按鈕。 您可以接著勾選核取方塊，以同時刪除 SQL Database 執行個體。 如果您只想暫時避免他人存取 web 應用程式，請按一下 [ **停止** 改。 在此情況下，將會繼續累算 SQL Database 和儲存體帳戶的費用。 當您不再需要 SQL 資料庫和儲存體帳戶時，可以遵循類似程序來加以刪除。

## <a id="create"></a>從頭開始建立應用程式

您將在本節執行下列工作：

* 使用 Web 專案建立 Visual Studio 方案。
* 在前端與後端共用的資料存取層中加入類別庫專案。
* 在啟用 WebJobs 部署的後端中新增主控台應用程式專案。
* 新增 NuGet 封裝。
* 設定專案參考。
* 從您在前一節的教學課程中使用的所下載應用程式，複製其應用程式程式碼和組態檔。
* 針對使用 Azure Blob 和佇列及 WebJobs SDK 該部分的程式碼進行審查。

### 使用 Web 專案和類別庫專案建立 Visual Studio 方案

1. 在 Visual Studio 中，選擇 [ **新增** > **專案** 從 **檔案** 功能表。

2. 在 **新的專案** ] 對話方塊中，選擇 [ **Visual C#** > **Web** > **ASP.NET Web 應用程式**。

3. 將專案命名為 ContosoAdsWeb，將方案命名為 ContosoAdsWebJobsSDK （變更方案名稱，如果您要將它所下載方案相同的資料夾中），然後按一下 **確定**。

    ![新增專案](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)

5. 在 **新增 ASP.NET 專案** ] 對話方塊中，選擇 MVC 範本，並清除 **定域機組中的主機** 底下的核取方塊 **Microsoft Azure**。

    選取 **定域機組中的主機** 可讓 Visual Studio 會自動建立新的 Azure web 應用程式與 SQL Database。 由於您稍早已建立這些項目，您現在無需在建立專案時執行此作業。 如果您要建立一個新的，請勾選此核取方塊。 您接下來可以使用稍早部署應用程式的相同方法，來設定新的 Web 應用程式和 SQL Database。

5. 按一下 [ **變更驗證**。

    ![Change Authentication](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)

7. 在 **變更驗證** ] 對話方塊中，選擇 [ **不需要驗證**, ，然後按一下 [ **確定**。

    ![不需要驗證](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)

8. 在 **新增 ASP.NET 專案** ] 對話方塊中，按一下 [ **確定**。

    Visual Studio 便會建立方案和 Web 專案。

9. 在 **方案總管] 中**, ，以滑鼠右鍵按一下方案 （不是專案），然後選擇 [ **新增** > **新的專案**。

11. 在 **加入新的專案** ] 對話方塊中，選擇 [ **Visual C#** > **Windows 桌面** > **類別庫** 範本。  

10. 將專案命名為 *ContosoAdsCommon*, ，然後按一下 [ **確定**。

    此專案將包含由前端與後端使用的 Entity Framework 內容和資料模型。 作為替代方式，您可以在 Web 專案中定義 EF 相關的類別，並從 WebJob 專案參考該專案。 但之後您的 WebJob 專案會有不需要的 Web 組件參考。

### 新增已啟用 WebJobs 部署的主控台應用程式專案

1. 以滑鼠右鍵按一下 web 專案 （不方案或類別庫專案），然後按一下 **新增** > **New Azure WebJob Project**。

    ![New Azure WebJob Project menu selection](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)

2. 在 **Add Azure WebJob** ] 對話方塊中，輸入 ContosoAdsWebJob **專案名稱** 和 **WebJob 名稱**。 保留 **模式執行的 WebJob** 設 **連續執行**。

3.  按一下 [ **確定**。

    Visual Studio 便會建立設定為每次部署 Web 專案時，便會部署成為 WebJob 的主控台應用程式。 若要執行此動作，應用程式在建立專案後執行了下列工作：

    * 加入 *webjob 發行 settings.json* WebJob 專案的 Properties 資料夾中的檔案。
    * 加入 *webjobs-list.json* web 專案的 Properties 資料夾中的檔案。
    * 在 WebJob 專案中安裝了 Microsoft.Web.WebJobs.Publish NuGet 封裝。

    如需有關這些變更的詳細資訊，請參閱 [如何使用 Visual Studio 部署 WebJobs](websites-dotnet-deploy-webjobs.md)。

### 新增 NuGet 封裝

WebJob 專案的新專案範本會自動安裝 WebJobs SDK NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 及其相依性。

在 WebJob 專案自動安裝的 WebJobs SDK 相依性中，其中一個相依性是 Azure 儲存體用戶端程式庫 (SCL)。 不過，您需要將它新增至 Web 專案才能使用 Blob 和佇列。

1. 開啟 **管理 NuGet 封裝** 解決方案的對話方塊。

2. 在左窗格中，選取 **已安裝的套件**。

3. 尋找 *Azure 儲存體* 封裝，並按 [ **管理**。

4. 在 **選取專案** 方塊中，選取 **ContosoAdsWeb** 核取方塊，，然後按一下 [ **確定**。

    這三個專案都會透過 Entity Framework 來使用 SQL Database 中的資料。

5. 在左窗格中，選取 **線上**。

6. 尋找 *EntityFramework* NuGet 封裝，並將它安裝在這三個專案。


### 設定專案參考

Web 和 WebJob 專案都將使用 SQL Database，因此兩者都會需要 ContosoAdsCommon 專案的參考。

1. 在 ContosoAdsWeb 專案中，設定 ContosoAdsCommon 專案的參考。 (以滑鼠右鍵按一下 ContosoAdsWeb 專案，然後按一下 **新增** > **參考**。 在 **參考管理員** 對話方塊中，選取 **方案** > **專案** > **ContosoAdsCommon**, ，然後按一下 [ **確定**。)

1. 在 ContosoAdsWebJob 專案中，設定 ContosAdsCommon 專案的參考。

    WebJob 專案需要參考，才能使用映像及存取連接字串。

3. 在 ContosoAdsWebJob 專案中，設定 `System.Drawing` 和 `System.Configuration` 的參考。

### 新增程式碼和組態檔

本教學課程不會顯示如何 [使用 scaffolding 建立 MVC 控制器和檢視](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started), 、 如何以 [撰寫適用於 SQL Server 資料庫的 Entity Framework 程式碼](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc), ，或 [ASP.NET 4.5 中非同步程式設計的基本概念](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async)。 因此剩下要進行的作業就是，從所下載的方案複製程式碼和組態檔到新方案。 在執行該作業後，下一節將示範和說明程式碼的重要部分。

若要加入檔案到專案或資料夾，以滑鼠右鍵按一下專案或資料夾，然後按一下 **新增** > **現有項目**。 選取的檔案，再按一下 **新增**。 如果詢問您是否要取代現有的檔案，按一下 [ **是**。

1. 在 ContosoAdsCommon 專案中，刪除 *Class1.cs* 檔案，並在其位置加入下列檔案從下載的專案。

    - *Ad.cs*
    - *ContosoAdscontext.cs*
    - *BlobInformation.cs*<br/><br/>

2. 在 ContosoAdsWeb 專案中，從所下載的專案加入下列檔案。

    - *Web.config*
    - *Global.asax.cs*  
    - 在 *控制器* 資料夾 ︰ *AdController.cs*
    - 在 *Views\Shared* 資料夾 ︰ *_Layout.cshtml* 檔案
    - 在 *Views\Home* 資料夾 ︰ *Index.cshtml*
    - 在 *Views\Ad* 資料夾 （先建立資料夾） ︰ 五個 *.cshtml* 檔案<br/><br/>

3. 在 ContosoAdsWebJob 專案中，從所下載的專案加入下列檔案。

    - *App.config* (變更檔案類型篩選器來 **所有檔案**)
    - *Program.cs*
    - *Functions.cs*

您現在可以如本教學課程中稍早所指示般建置、執行及部署應用程式。 不過，在您開始執行此動作之前，請先將仍在您部署的第一個 Web 應用程式中執行的 WebJob 停止。 否則，WebJob 將會處理在本機建立或由在新 Web 應用程式中執行之應用程式所建立的佇列訊息，因為它們全都使用相同的儲存體帳戶。

## <a id="code"></a>檢閱應用程式程式碼

以下小節將說明 WebJobs SDK、Azure 儲存體 Blob 和佇列相關的程式碼。

> [AZURE.NOTE] WebJobs SDK 特定的程式碼，請移至 [Program.cs 和 Functions.cs](#programcs) 區段。

### ContosoAdsCommon - Ad.cs

Ad.cs 檔案可定義廣告類別列舉，以及廣告資訊的 POCO 實體類別。

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### ContosoAdsCommon - ContosoAdsContext.cs

ContosoAdsContext 類別可指定廣告類別用於 DbSet 集合，Entity Framework 會儲存在 SQL 資料庫中。

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

類別含兩個建構函式。 第一個是由 Web 專案所使用，指定儲存在 Web.config 檔案或 Azure 執行階段環境中的連接字串名稱。 第二個建構函式可讓您傳入實際的連接字串。 WebJob 專案會需要該資訊，因為它沒有 Web.config 檔案。 您稍早已看見儲存此連接字串的位置，之後您會看到程式碼如何在具現化 DbContext 類別時擷取連接字串。

### ContosoAdsCommon - BlobInformation.cs

`BlobInformation` 類別可用來在佇列訊息中儲存影像 Blob 的相關資訊。

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }

            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }


### ContosoAdsWeb - Global.asax.cs

從呼叫程式碼 `Application_Start` 方法會建立 *映像* blob 容器和 *映像* 佇列已經存在。 這可確保每當使用新的儲存體帳戶啟動時，系統便會自動建立必要的 Blob 容器和佇列。

此程式碼使用的儲存體連接字串來取得儲存體帳戶的存取權 *Web.config* 檔案或 Azure 執行階段環境。

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

然後，它會取得參考 *映像* blob 容器、 建立容器，如果它不存在，並設定存取權限的新容器。 依預設，新的容器只允許具有儲存體帳戶認證的用戶端存取 Blob。 Web 應用程式需要 Blob 處於公用狀態，才能使用指向影像 Blob 的 URL 來顯示影像。

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

類似的程式碼取得參考 *blobnamerequest* 排入佇列，並建立新的佇列。 在此情況下，即不需要變更權限。  [ResolveBlobName](#resolveblobname) 教學課程稍後的章節將說明為何 web 應用程式所寫入的佇列用只是用來取得 blob 名稱但不用來產生縮圖。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - _Layout.cshtml

 *_Layout.cshtml* 檔案可設定頁首與頁尾中的應用程式名稱並建立 [廣告] 功能表項目。

### ContosoAdsWeb - Views\Home\Index.cshtml

 *Views\Home\Index.cshtml* 檔案在首頁上顯示類別連結。 連結會將查詢字串變數中 `Category` 列舉的整數值傳遞至 [廣告索引] 頁面。

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

在 *AdController.cs* 檔案建構函式呼叫 `InitializeStorage` 方法來建立 Azure 儲存體用戶端程式庫物件，提供用於處理 blob 和佇列的 API。

此程式碼取得的參考，然後 *映像* blob 容器，如您稍早在 *Global.asax.cs*。 在這麼做，它會設定預設值 [重試原則](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) 適用於 web 應用程式。 預設指數輪詢重試原則，可能會因為對暫時性的錯誤進行反覆重試，使得 Web 應用程式停止回應超過一分鐘。 此處指定的重試原則會在每次嘗試後等候 3 秒，最多嘗試 3 次。

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

類似的程式碼取得參考 *映像* 佇列。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

多數的控制器程式碼通常用於使用 DbContext 類別來處理 Entity Framework 資料模型。 例外狀況為 HttpPost `Create` 方法，它會上傳檔案，並將檔案儲存在 Blob 儲存體。 模型繫結提供 [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) 方法的物件。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

如果使用者選取要上傳的檔案，程式碼會上傳檔案、將檔案儲存在 Blob，並以指向 Blob 的 URL 更新廣告資料庫記錄。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

執行上傳的程式碼位於 `UploadAndSaveBlobAsync` 方法。 它會為 Blob 建立 GUID 名稱、上傳並儲存檔案，然後傳回參考至儲存的 Blob。

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

在 HttpPost `Create` 方法建立 Blob 並更新資料庫之後，它會建立佇列訊息，以通知後端程序，有一個影像已備妥可供轉換成縮圖。

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

HttpPost `Edit` 方法的程式碼也是類似的，例外情況是如果使用者選取一個新影像檔案，則必須將此廣告的任何現有 Blob 刪除。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

以下是當您刪除廣告時會刪除 Blob 的程式碼：

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### ContosoAdsWeb - Views\Ad\Index.cshtml 和 Details.cshtml

 *Index.cshtml* 檔案會顯示縮圖與其他廣告資料 ︰

        <img  src="@Html.Raw(item.ThumbnailURL)" />

 *Details.cshtml* 檔案會顯示完整大小的影像 ︰

        <img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\Ad\Create.cshtml 和 Edit.cshtml

 *Create.cshtml* 和 *Edit.cshtml* 檔案可指定表單編碼，供控制器取得 `HttpPostedFileBase` 物件。

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

`<input>` 元素告知瀏覽器提供檔案選取對話方塊。

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob - Program.cs

當 WebJob 啟動時，`Main` 方法會呼叫 WebJobs SDK `JobHost.RunAndBlock` 方法，開始執行目前執行緒上所觸發的函數。

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

### <a id="generatethumbnail"></a>ContosoAdsWebJob - Functions.cs - GenerateThumbnail 方法

WebJobs SDK 會在收到佇列訊息時呼叫此方法。 此方法會建立縮圖，並將縮圖 URL 放入資料庫。

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }

            // Entity Framework context class is not thread-safe, so it must
            // be instantiated and disposed within the function.
            using (ContosoAdsContext db = new ContosoAdsContext())
            {
                var id = blobInfo.AdId;
                Ad ad = db.Ads.Find(id);
                if (ad == null)
                {
                    throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
                }
                ad.ThumbnailURL = outputBlob.Uri.ToString();
                db.SaveChanges();
            }
        }

* 當 thumbnailrequest 佇列收到新訊息時，`QueueTrigger` 屬性便會指示 WebJobs SDK 呼叫此方法。

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    佇列訊息中的 `BlobInformation` 物件會自動還原序列化成 `blobInfo` 參數。 方法完成時，佇列訊息便會被刪除。 如果方法在完成前失敗，則佇列訊息不會被刪除；在 10 分鐘的租用到期後，訊息便會被釋放並等待被再次挑選與處理。 如果某個訊息總是導致例外狀況，則不會無限制地重複此順序。 在嘗試處理訊息失敗 5 次之後，訊息便會被移至名為 {queuename}-poison 的佇列。 您可以設定嘗試的數目上限。

* 這兩個 `Blob` 屬性提供繫結到 Blob 的物件：一個繫結到現有影像的 Blob，一個繫結到方法建立的新縮圖 Blob。

        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Blob 名稱取自佇列訊息 (`BlobName` 和 `BlobNameWithoutExtension`) 中所收到 `BlobInformation` 物件的屬性。 若要取得儲存體用戶端程式庫的完整功能，您可以利用 `CloudBlockBlob` 類別來使用 Blob。 如果您想要重複使用為使用 `Stream` 物件所撰寫的程式碼，您可以使用 `Stream` 類別。

如需有關如何撰寫使用 WebJobs SDK 屬性的函式的詳細資訊，請參閱下列資源 ︰

* [如何透過 WebJobs SDK 使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure Blob 儲存體](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure 資料表儲存體](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure 服務匯流排](websites-dotnet-webjobs-sdk-service-bus.md)

> [AZURE.NOTE]
>
> * 如果您的 Web 應用程式在多個 VM 上執行，則系統將同時執行多個 WebJobs，且在某些情況下，這會導致相同的資料多次進行處理。 如果您使用內建佇列、blob 和服務匯流排觸發程序，這不會造成問題。 SDK 可確保您的函式針對每個訊息或 blob 僅會處理一次。
>
> * 如需如何實作正常關機的資訊，請參閱 [正常關機](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)。
>
> * 為求簡化，`ConvertImageToThumbnailJPG` 方法 (未顯示) 中的程式碼會使用 `System.Drawing` 命名空間中的類別。 不過，此命名空間中類別的設計原意是要與 Windows Form 搭配使用。 不支援將它們用於 Windows 或 ASP.NET 服務。 如需影像處理選項的詳細資訊，請參閱 [動態影像產生](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) 和 [深入調整影像大小](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na)。

## 後續步驟

在本教學課程中，您看到使用 WebJobs SDK 進行後端處理的簡單多層次應用程式。 如要進一步了解 ASP.NET 多層式應用程式和 WebJobs，請參考本節所提供的一些建議。

### 遺漏的功能

此應用程式設計得很簡單，以做為入門的教學課程。 在真實世界應用程式中，您會實作 [相依性插入](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) 和 [儲存機制和單位運作模式](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), ，使用 [介面來記錄](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), ，使用 [EF Code First 移轉](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) 管理資料模型變更，並使用 [EF 連線復原](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) 來管理暫時性網路錯誤。

### 調整 WebJob 的規模

WebJob 會在 Web 應用程式的內容中執行，且無法單獨調整。 例如，如果您擁有一個標準的 Web 應用程式執行個體，則您的背景程序只有一個執行中的執行個體，而且它會使用亦可用來提供 Web 內容的部分伺服器資源 (CPU、記憶體等)。

如果流量會因為一天中的某個時段或一週中的某天而有所不同，而且如果必須執行的後端處理可以暫緩的話，您可以排程 WebJobs 在低流量時段中執行。 如果該解決方案的負載仍然太高，您可以在針對該用途專用的 Web 應用程式中以 WebJob 形式執行後端。 接著您可以擴充後端 Web 應用程式，而不會影響到前端 Web 應用程式。

如需詳細資訊，請參閱 [調整 WebJobs](websites-webjobs-resources.md#scale)。

### 避免關機時 Web 應用程式逾時

若要確定一律執行 WebJobs，而且在您的 web 應用程式的所有執行個體上執行，您必須啟用 [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx) 功能。

### 在 WebJobs 外部使用 WebJobs SDK

使用 WebJobs SDK 的程式無需在 Azure 的 WebJob 中執行。 它可以在本機執行，也可以在如雲端服務背景工作角色或 Windows 服務等其他環境中執行。 不過，您僅能透過 Azure Web 應用程式來存取 WebJobs SDK 儀表板。 若要使用您有 web 應用程式連接到您正在使用上設定 AzureWebJobsDashboard 連接字串儲存體帳戶儀表板 **設定** 傳統入口網站] 索引標籤。 然後，您可以使用下列 URL 來進入儀表板：

https://{webappname}.scm.azurewebsites.net/azurejobs/#/functions

如需詳細資訊，請參閱 [取得使用 WebJobs SDK 的本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), ，但請注意，它會顯示舊的連接字串名稱。

### 其他 WebJobs 文件

如需詳細資訊，請參閱 [Azure WebJobs 文件資源](http://go.microsoft.com/fwlink/?LinkId=390226)。


