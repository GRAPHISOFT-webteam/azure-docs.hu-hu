<properties
    pageTitle="搭配 Azure 搜尋服務使用 Chrome Postman |Microsoft Azure |託管的雲端搜尋服務"
    description="搭配 Azure 搜尋服務 (雲端託管搜尋服務) 使用 Chrome Postman。安裝並設定 Postman。建立 Azure 搜尋服務索引。使用 Postman 張貼文件和查詢索引。"
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
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="11/10/2015"
    ms.author="heidist"/>


# 如何搭配 Azure 搜尋服務使用 Chrome Postman

> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)


[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") 是提供可讓開發人員有效地使用 REST API 的 Google Chrome 的服務，例如 Azure 搜尋服務的工具。 您可透過 Postman 傳送 API 呼叫，不需撰寫任何程式碼，即可使用 Postman 快速建立與查詢搜尋索引。 這個方法可讓您有效了解 API 並嘗試新功能。

![][1]

## 需求

您必須具備 Azure 搜尋服務。 使用 Azure 搜尋服務的自訂應用程式，您將需要 URL，您的服務，與管理員 `api 金鑰` ，因此您可以建立索引。 如需有關如何取得搜尋服務的值的指示，請參閱 [入口網站中建立服務](search-create-service-portal.md)。

## 安裝 Postman

若要下載 Postman，請瀏覽 [Google Chrome 商店](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)。 此頁面連結可讓您下載並安裝 Postman 的 REST 用戶端。 安裝之後，您即可使用 Chrome 應用程式啟動器來啟動 Postman。

![][2]

## 設定 Postman 以查詢 Azure 搜尋服務

若要設定 Postman，請遵循下列步驟：

1. 請在「於此處輸入要求 URL」的位置，輸入 Azure 搜尋服務的 URL。
2. 附加至 URL: `? api-version = 2015年-02-28`。 您還可以指定不同的 API 版本。 請參閱 [Azure 搜尋服務版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx) 如需詳細資訊。
3. 請確認 `取得` 選擇。
4. 按一下 [標頭]**** 按鈕。
5. 輸入下列項目的值：
    - `api 金鑰`: [系統管理金鑰]
    - `Content-type`: `application/json; charset = utf-8`
6. 按一下 [傳送]****，對 Azure 搜尋服務發出 REST 呼叫並顯示 JSON 回應。

![][3]

## 使用 Postman 建立 Azure 搜尋服務索引

接下來，我們可以發出 REST 呼叫來新建 Azure 搜尋服務的索引，並擴充在上一個步驟完成的項目。 索引建立與上一個呼叫不同，該作業需使用 HTTP PUT 以及含有索引結構描述定義的 JSON 文件。 在這個範例中，我們將建立一個索引來存放登山路線清單。 作法：

1. 變更 URL，以: `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` 使用您的搜尋服務名稱。
2. 變更要求類型由 `取得` 至 `放`。
3. 在原始本文內容中，輸入下列 JSON：

        {
        "name": "trails",
        "fields": [
        {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "name", "type": "Edm.String"},
        {"name": "county", "type": "Edm.String"},
        {"name": "elevation", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"} ]
        }

4. 按一下 [傳送]****。

![][4]

## 使用 Postman 將文件張貼至 Azure 搜尋服務索引

建立好索引後，即可將文件載入其中。 為了進行這項作業，我們會使用來自美國地質調查局 (USGS) 資料集的五個路線資料，批次張貼一組文件：

1. 變更 URL，以: `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` 使用您的搜尋服務名稱。 請注意，這個 URL 應包含您剛建立的索引路徑。
2. 將 HTTP 類型變更為 `POST`。
3. 在原始本文內容中，輸入下列 JSON：

        {
          "value": [
            {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
            {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
            {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
            {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
            {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
          ]
        }

4. 按一下 [傳送]****。

![][5]

## 使用 Postman 查詢索引

最後一個步驟是查詢索引，並針對 *trail* (路線) 一詞發出簡單的全文檢索搜尋要求。

1. 輸入下列 url: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28 與搜尋 = 軌跡` 使用您的搜尋服務名稱。請注意，該 URL 應包含 `搜尋` 查詢參數和搜尋詞彙的 *軌跡*。
2. 將 HTTP 要求類型變更為 `取得`。
3. 按一下 [傳送]****。

在回應中，您應可看到 Azure 搜尋服務傳回的 JSON 搜尋結果。

![][6]

## 後續步驟

現在，我們已逐步說明使用 Azure 搜尋服務與 Postman 的基本步驟，下列幾項重點有助您進行接下來的步驟：

1. Postman 支援 `集合`, ，可讓您輕鬆儲存經常發出的要求。 您可和其他人共用集合；個別使用者可在自己的 Postman 複本中發出。
2. 在 Azure 搜尋服務文件中，請務必記下的 HTTP 要求類型 (`取得`, ，`放`, 等等) 與每個呼叫相關聯，並在 Postman 中做適當變更。

REST API 文件，請參閱 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)。

您也可以造訪 [影片和教學課程清單](search-video-demo-tutorial-list.md) 如需詳細資訊。



[1]: ./media/search-chrome-postman/full_postman_client.png 
[2]: ./media/search-chrome-postman/postman.png 
[3]: ./media/search-chrome-postman/configure.png 
[4]: ./media/search-chrome-postman/create_index.png 
[5]: ./media/search-chrome-postman/upload_documents.png 
[6]: ./media/search-chrome-postman/query.png 

