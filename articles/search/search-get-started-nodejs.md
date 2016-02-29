<properties
    pageTitle="開始以 NodeJS 使用 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
    description="逐步了解如何使用 NodeJS 做為程式設計語言，在 Azure 的雲端託管搜尋服務上建置搜尋應用程式。"
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

# 開始在 NodeJS 中使用 Azure 搜尋服務

瞭解如何建置使用 Azure 搜尋服務提供搜尋體驗的自訂 NodeJS 搜尋應用程式。 本教學課程使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/dn798935.aspx) 建構的物件和這個練習中所用的作業。

我們使用 [NodeJS](https://nodejs.org) 、 NPM、 [Sublime Text 3](http://www.sublimetext.com/3), ，並以開發並測試這段程式碼的 Windows 8.1 上的 Windows PowerShell。

若要執行此範例中，您需要 Azure 搜尋服務，您可以註冊在 [Azure 傳統入口網站](https://portal.azure.com)。

> [AZURE.TIP] 在本教學課程下載來源程式碼 [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)。

## 關於資料

此範例應用程式使用的資料 [美國地理服務中心 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), 上羅德島州來縮小資料集進行篩選。 我們將使用此資料，建置可傳回地標建築物 (例如醫院和學校) 及地理特徵 (例如河流、湖泊和山峰) 等相關資料的搜尋應用程式。

在此應用程式， **DataIndexer** 程式建置及載入索引使用 [索引子](https://msdn.microsoft.com/library/azure/dn798918.aspx) 建構，從公用 Azure SQL Database 擷取篩選過的 USGS 資料集。 認證和連接的程式碼中有提供線上資料來源的資訊。 不需要進一步設定。

> [AZURE.NOTE] 我們在此資料集，以維持免費定價層的 10000 份文件限制不套用篩選器。 如果使用標準版定價層，就不會套用此限制。 如需各個定價層的容量的詳細資訊，請參閱 [限制和條件約束](search-limits-quotas-capacity.md)。

## 建立服務

1. 登入 [Azure 傳統入口網站](https://portal.azure.com)。

2. 在 Jumpbar 中按一下 [ **新增** > **資料 + 儲存體** > **搜尋**。

     ![][1]

3. 設定服務名稱、定價層、資源群組、訂用帳戶及位置。 這些設定都是必須設定的項目，且在佈建服務後就不能變更。

     ![][2]

    - **服務名稱** 必須是唯一的、 小寫、 少於 15 個字元，且不含空格。 此名稱會成為您 Azure 搜尋服務端點的一部分。 請參閱 [命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx) 如需命名慣例的詳細資訊。

    - **定價層** 決定可用容量和計費。 以下兩個定價層提供相同的功能，但所處的資源層級不同。

        - **免費**  與其他訂戶共用的叢集上執行。 此版本所提供的容量，已足夠用來試驗教學課程以及撰寫用於證明概念的程式碼，但並不適用於生產應用程式。 部署免費版服務通常只需要幾分鐘時間。
        - **標準** 專用資源上執行，而且具備高度可擴充性。 標準版服務一開始是以一個複本和一個資料分割進行佈建，不過您可以在建立服務後調整容量。 部署標準版服務需要較長的時間，通常需要大約 15 分鐘時間。

    - **資源群組** 服務和資源用於一般用途的容器。 例如，如果您以 Azure 搜尋服務、Azure 網站和 Azure BLOB 儲存體為基礎建立自訂搜尋應用程式，那麼您可以建立資源群組，來將這些服務一起放在入口網站的管理頁面中。

    - **訂閱** 可讓您選擇使用多個訂閱，如果您有一個以上的訂閱。

    - **位置** 是資料中心區域。 目前，所有資源都必須在同一個資料中心內執行。 不支援將資源分配到多個資料中心內。

4. 按一下 [ **建立** 佈建服務。

注意 Jumpbar 中的通知。 服務一旦準備就緒可供使用時就會顯示通知。

<a id="sub-2"></a>
## 尋找 Azure 搜尋服務的服務名稱和 API 金鑰

建立服務之後，請返回入口網站取得 URL 或 `api-key`。 如果想要連接至搜尋服務，您必須同時擁有 URL 和 `api-key` 才能驗證呼叫。

1. 在 Jumpbar 中按一下 [ **首頁** 然後按一下 [搜尋服務以開啟服務儀表板。

2. 您會在服務儀表板上看到基本資訊磚，以及存取系統管理金鑰的鑰匙圖示。

    ![][3]

3. 複製服務 URL、系統管理金鑰和查詢金鑰。 稍後您會需要將這三個項目加到 config.js 檔案中。

## 下載範例檔案

使用以下其中一種方法下載範例。

1. 移至 [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)。
2. 按一下 [ **下載 ZIP**, ，並儲存.zip 檔案，然後解壓縮其中所含的所有檔案。

所有後續的檔案修改及執行陳述式均會用到此資料夾的檔案。

或者如果您在路徑陳述式中有 GIT，可以開啟 PowerShell 視窗並輸入 `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`

## 更新 config.js。 使用您的搜尋服務 URL 和 api 金鑰

使用先前複製的 URL 和 API 金鑰，在組態檔案中指定 URL、系統管理金鑰和查詢金鑰。

系統管理金鑰可將服務作業的完整控制權限授與給您，包括建立或刪除索引，以及載入文件。 相較之下，查詢金鑰僅用於唯讀作業，通常由連接到 Azure 搜尋服務的用戶端應用程式所用。

在此範例中，我們包含查詢金鑰，協助您在客戶端應用程式中確實熟悉使用查詢金鑰的最佳做法。

下列螢幕擷取畫面顯示 **config.js** 開啟文字編輯器中，以區分，讓您可以看到搜尋服務有效的值更新檔案的位置，哪些相關的項目。

![][5]


## 為範例託管執行階段環境

此範例需要 HTTP 伺服器，讓您安裝全球使用的 NPM。

使用 PowerShell 視窗以執行以下命令。

1. 瀏覽至包含的資料夾 **package.json** 檔案。
2. 輸入 `npm install`。
2. 輸入 `npm install -g http-server`。

## 建置索引並執行應用程式

1. 輸入 `npm run indexDocuments`。
2. 輸入 `npm run build`。
3. 輸入 `npm run start_server`。
4. 將您的瀏覽器導向至 `http://localhost:8080/index.html`

## 搜尋 USGS 資料

USGS 資料集包含與羅德島州相關的記錄。 如果您按一下 **搜尋** 在空白的搜尋方塊，就會前 50 個項目，這是預設值。

輸入搜尋詞彙，讓搜尋引擎運作一下。 試著輸入區域名稱。 "Roger Williams" 是羅德島州的第一任州長。 許多公園、建築物和學校都是以他的姓名命名。

![][9]

此外，您也可以試著使用這些字詞：

- Pawtucket
- Pembroke
- goose +cape


## 後續步驟

這是第一個以 NodeJS 和 USGS 資料集為基礎的 Azure 搜尋服務教學課程。 我們會漸漸擴充本教學課程，以示範其他您可能會想用在自訂方案中的搜尋功能。

如果您已有一些 Azure 搜尋服務的背景知識，可以利用此範例做為試用建議工具 (預先輸入或自動完成查詢)、篩選及多面向導覽的跳板。 您也可以新增計數和批次處理文件，讓使用者可以逐頁查看結果，藉此改進搜尋結果頁面。

不熟悉 Azure 搜尋服務嗎？ 建議您嘗試學習其他教學課程，深入了解您還可以建立哪些東西。 請造訪我們 [文件頁面](http://azure.microsoft.com/documentation/services/search/) 以尋找更多資源。 您也可以檢視中的連結，我們 [影片和教學課程清單](search-video-demo-tutorial-list.md) 以存取更多資訊。

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

