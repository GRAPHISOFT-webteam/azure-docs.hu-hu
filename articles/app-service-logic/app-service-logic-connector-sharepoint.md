<properties
   pageTitle="在 Logic Apps 中使用 SharePoint 連接器 | Microsoft Azure App Service"
   description="如何建立並設定 SharePoint 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="sameerch"/>

# 開始使用 SharePoint 連接器並將它加入您的邏輯應用程式
連線至 SharePoint Server 或 SharePoint Online 來管理文件和清單項目。 您可以執行各種動作，例如建立、更新、取得和刪除文件和清單項目。 使用內部部署 SharePoint Server 時，您可以輸入服務匯流排連接字串做為連接器組態的一部分，並安裝內部部署接聽程式代理程式以連線到伺服器。

SharePoint Online 連接器和 SharePoint Server 連接器資源庫應用程式可提供觸發程序和動作做為與 SharePoint 互動的機制。

邏輯應用程式可以根據各種資料來源觸發，並提供連接器以取得及處理屬於流程一部分的資料。 您可以將 SharePoint 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。 

## 建立 SharePoint Online 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 選取 **API 應用程式** ，並搜尋 「 SharePoint Online 連接器 」。
3. 輸入名稱、App Service 方案和其他屬性。
4. 輸入下列封裝設定：

    名稱 |需要 |描述
--- | --- | ---
網站 URL |[是 |輸入 SharePoint 網站的完整 URL。 例如，輸入 ︰ *https://microsoft.sharepoint.com/teams/wabstest*。
文件庫 / 清單相對 URL | 是 | 輸入相對於 SharePoint 網站 URL、且可由連接器修改的文件庫/清單 URL。 例如，輸入 ︰ *清單/工作、 共用文件*。

5. 完成時，[封裝設定看起來如下所示 ︰  
![][1]

完成後，您現在可以在相同的資源群組中建立邏輯應用程式，以便使用 SharePoint Online 連接器。

## 建立 SharePoint Server 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 選取 **API 應用程式** ，並搜尋 「 SharePoint Server 連接器 」。
3. 輸入名稱、App Service 方案和其他屬性。
4. 輸入下列封裝設定：

    名稱 |需要 |描述
--- | --- | ---
網站 URL |[是 |輸入 SharePoint 網站的完整 URL。 例如，輸入 ︰ *https://microsoft.sharepoint.com/teams/wabstest*。
驗證模式 | 是 | 指定連線到 SharePoint 網站時的驗證模式。 選項包括：<ul><li>預設值</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>如果您選擇預設值，會使用執行 SharePoint 連接器的認證。不需要使用者名稱/密碼。 使用者名稱和密碼所需的其他驗證類型。<br/><br/>**請注意** 不支援匿名驗證。
使用者名稱 |否 |如果驗證模式不是預設值，請輸入有效的使用者名稱連接到 SharePoint 網站。
密碼 |否 |如果驗證模式不是預設值，請輸入有效的密碼來連線到 SharePoint 網站。
文件庫 / 清單相對 URL | 是 | 輸入相對於 SharePoint 網站 URL、且可由連接器修改的文件庫/清單 URL。 例如，輸入 ︰ *清單/工作、 共用文件*。
服務匯流排連接字串 |否 |如果您要連線至內部部署上，輸入服務匯流排轉送連接字串。<br/><br/>[使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)<br/>[服務匯流排定價](http://azure.microsoft.com/pricing/details/service-bus/)

5. 完成時，[封裝設定看起來如下所示 ︰  
![][2]

完成後，您現在可以在相同的資源群組中建立邏輯應用程式，以便使用 SharePoint Server 連接器。


## 在邏輯應用程式中使用 SharePoint 連接器

建立 API 應用程式之後，您現在可以使用 SharePoint 連接器做為邏輯應用程式的觸發程序或動作。 若要這樣做，您需要：

1. 建立新的邏輯應用程式，並選擇具有 SharePoint 連接器的相同資源群組。

2. 開啟 **觸發程序和動作** 以開啟邏輯應用程式設計工具並設定您的工作流程。 SharePoint 連接器會出現在右側資源庫中的 [最近使用的] 區段。 請選取它。

3. 如果在啟動邏輯應用程式時就選取 SharePoint 連接器，它就會像觸發程序般運作。 否則，可以使用連接器在 SharePoint 帳戶上採取動作。

4. 使用 SharePoint Online 連接器時，您必須驗證和授權邏輯應用程式來代表您執行作業。 若要開始授權，請按一下 [ **授權** SharePoint 連接器上 ︰  
![][3]

5. 按一下 [授權] 便會開啟 SharePoint 的 [驗證] 對話方塊。 輸入符號，您要執行作業的 SharePoint 帳戶的詳細資料 ︰  
![][4]

6. 授與您的帳戶，以代表您執行作業的邏輯應用程式存取權限 ︰  
![][5]

7. 如果 SharePoint 連接器設定為觸發程序，則會顯示觸發程序。 否則，會顯示動作清單，您可以選擇您想要執行的適當作業 ︰  
![][6]
  
**設定文件庫的相對 URL**  
![][7]

**已設定文件清單的相對 URL**

> [AZURE.NOTE]下列觸發程序中，假設您在連接器封裝設定，其中 「 共用文件 」 是文件庫，而 「 清單/工作 」 則是清單中，輸入 「 共用文件、 清單/工作 」。 

##  觸發程序
如果您要起始邏輯應用程式，請使用觸發程序

> [AZURE.NOTE] 觸發程序閱讀後刪除的檔案。 若要保留這些檔案，請輸入封存位置的值。

### 1.共用文件中的新文件 (JSON)
當「共用文件」中有可用的新文件時，便會啟動此觸發程序。 

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
檢視名稱 | 否 | 輸入有效檢視用來篩選要挑選的文件。 例如，輸入「核准的訂單」。 若要處理所有的現有文件，請將此欄位留空。
封存位置 | 否 | 輸入相對於 SharePoint 網站的有效資料夾 URL，用來封存已處理的文件。
在封存中覆寫 | 否 | 勾選此選項即可覆寫封存路徑中的檔案 (如果已存在)。
CAML 查詢 | 否，進階 | 輸入有效的 CAML 查詢來篩選文件。 例如，輸入 ︰  `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### 輸出

名稱 | 說明
--- | ---
名稱 | 文件的名稱。
內容 | 文件的內容。
ContentTransferEncoding | 訊息的內容傳送編碼。 ("none"或"base64")

**請注意** 所有的資料行的文件項目會顯示在 [進階] 輸出屬性。


### 2.工作中的新項目 (JSON)
在「工作」清單中新增項目時，即會啟動此觸發程序。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
檢視名稱 | 否 | 輸入用於清單中篩選項目的有效檢視。 例如，輸入「核准的訂單」。 若要處理所有新的項目，請將此欄位留空。
封存位置 | 否 | 輸入相對於 SharePoint 網站的有效資料夾 URL，用來封存已處理的清單項目。
CAML 查詢 | 否，進階 | 輸入有效的 CAML 查詢來篩選文件。 例如，輸入 ︰  `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### 輸出

名稱 | 說明
--- | ---
系統會以動態方式填入清單中的資料行，並顯示在輸出參數中。 | &nbsp;


### 3.共用文件中的新文件 (XML)

當「共用文件」中有可用的新文件時，便會啟動此觸發程序。 新文件會以 XML 訊息的方式傳回。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
檢視名稱 | 否 | 輸入有效檢視用來篩選要挑選的文件。 例如，輸入「核准的訂單」。 若要處理所有的現有文件，請將此欄位留空。
封存位置 | 否 | 輸入相對於 SharePoint 網站的有效資料夾 URL，用來封存已處理的清單文件。
在封存中覆寫 | 否 | 勾選此選項即可覆寫封存路徑中的檔案 (如果已存在)。
CAML 查詢 | 否，進階 | 輸入有效的 CAML 查詢來篩選文件。 例如，輸入 ︰  `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### 輸出

名稱 | 說明
--- | ---
內容 | 文件的內容。
ContentTransferEncoding | 訊息的內容傳送編碼。 ("none"或"base64")


### 4.工作中的新項目 (XML)

在「工作」清單中新增項目時，即會啟動此觸發程序。 新的清單項目會以 XML 訊息的方式傳回。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
檢視名稱 | 否 | 輸入用於清單中篩選項目的有效檢視。 範例：「核准的訂單」。 若要處理新的項目，請將此欄位留空。
封存位置 | 否 | 輸入相對於 SharePoint 網站的有效資料夾 URL，用來封存已處理的清單項目。
CAML 查詢 | 否，進階 | 輸入有效的 CAML 查詢來篩選清單項目。 例如，輸入：`<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### 輸出

名稱 | 說明
--- | ---
內容 | 文件的內容。
ContentTransferEncoding | 訊息的內容傳送編碼。 ("none"或"base64")


##  動作
對於下列動作，假設您在連接器封裝設定中輸入「共用文件、清單/工作」，其中「共用文件」是文件庫，而「清單/工作」則是清單。 

### 1.上傳到共用文件 (JSON)

這個動作會將新文件上載到「共用文件」。 輸入為包含文件庫中所有資料行欄位的強型別 JSON 物件。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
名稱 | 是 | 文件的名稱。
內容 | 是 | 文件的內容。
ContentTransferEncoding | 是 | 訊息的內容傳送編碼。 ("none"或"base64")
強制覆寫 | 是 | 如果設定為 TRUE，且指定名稱的文件存在的情況下，文件將會被覆寫。
ReqParam1* | 是 | 這是要在文件庫中加入文件的其中一個必要參數。
ReqParam2* | 是 | 這是要在文件庫中加入文件的其中一個必要參數。
OptionalParam1* | 編號 進階 | 這是要在文件庫中加入文件的其中一個選擇性參數。
OptionalParam2* | 編號 進階 | 這是要在文件庫中加入文件的其中一個選擇性參數。

**請注意** 會以動態方式填入文件庫中的所有參數。 可以看見必要參數，而選擇性參數則在進階區段中。

#### 輸出

名稱 | 說明
--- | ---
ItemId | 加入文件庫的文件項目識別碼。
狀態 | 成功上傳文件會傳回狀態碼 200 (確定)。


 

### 2.從共用文件取得 (JSON)
這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中取得文件。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
文件相對 URI | 否 | 輸入相對於「共用文件」的文件 URL。 例如，輸入 ︰ *myspec1、 myfolder/orders*。

#### 輸出

名稱 | 說明
--- | ---
內容 | 文件內容
ContentTransferEncoding | 訊息的內容傳送編碼。 ("none"或"base64")
狀態 | 成功執行動作會傳回狀態碼 200 (確定)。
Param1* | 這是位於文件庫中文件的其中一個參數。
Param2* | 這是位於文件庫中文件的其中一個參數。

**請注意** 會以動態方式填入文件庫中的所有參數。 此外，它們位於進階設定中。

 

### 3.從共用文件中刪除

這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中刪除文件。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
文件相對 URI | 否 | 輸入相對於「共用文件」的文件 URL。 例如，輸入 ︰ *myspec1、 myfolder/orders*。

#### 輸出

名稱 | 說明
--- | ---
狀態 | 成功執行動作會傳回狀態碼 200 (確定)。


### 4.在工作中插入 (JSON)

這個動作會在項目清單中加入項目。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ReqParam1* | 是 | 這是要在清單中加入項目的其中一個必要參數。
ReqParam2* | 是 | 這是要在清單中加入項目的其中一個必要參數。
OptionalParam1* | 編號 進階 | 這是要在清單中加入項目的其中一個必要參數。
OptionalParam2* | 編號 進階 | 這是要在清單中加入項目的其中一個必要參數。

**請注意** 會以動態方式填入 「 清單 」 中的所有參數。 可以看見必要參數，而選擇性參數則在進階區段中。

#### 輸出

名稱 | 說明
--- | ---
ItemId | 加入清單項目的項目識別碼。
狀態 | 成功插入清單項目會傳回狀態碼 200 (確定)。


### 5.更新工作 (JSON)

這個動作會在項目清單中更新項目。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ItemId | 是 | 清單項目的項目識別碼。
ReqParam1* | 否 | 這是要在清單中加入項目的其中一個必要參數。
ReqParam2* | 否 | 這是要在清單中加入項目的其中一個必要參數。
OptionalParam1* | 編號 進階 | 這是要在清單中加入項目的其中一個必要參數。
OptionalParam2* | 編號 進階 | 這是要在清單中加入項目的其中一個必要參數。

**請注意**  會以動態方式填入 「 清單 」 中的所有參數。 可以看見必要參數，而選擇性參數則在進階區段中。

#### 輸出

名稱 | 說明
--- | ---
狀態 | 成功更新清單項目會傳回狀態碼 200 (確定)。


### 6.從工作取得項目 (JSON)

這個動作會從項目清單中取得項目。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ItemId | 是 | 清單項目的項目識別碼。

#### 輸出

名稱 | 說明
--- | ---
Column1* | 這是清單中的其中一個參數。
Column2* | 這是清單中的其中一個參數。
狀態 | 成功執行動作會傳回狀態碼 200 (確定)。

**請注意** 以動態方式填入並顯示在輸出參數清單中的資料行。


### 7.從工作中刪除項目

這個動作會從項目清單中刪除項目。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ItemId | 是 | 清單項目的項目識別碼。

#### 輸出

名稱 | 說明
--- | ---
狀態 | 成功刪除清單項目會傳回狀態碼 200 (確定)。


### 8.列出共用文件 (JSON)。

這個動作會列出文件庫中的所有文件。 您可以使用 [檢視] 或 CAML 查詢來篩選文件。  

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
檢視名稱 | 否 | 輸入有效檢視用來篩選要挑選的文件。 例如，輸入「核准的訂單」。 若要處理所有的現有文件，請將此欄位留空。
CAML 查詢 | 否 | 輸入有效的 CAML 查詢來篩選文件。 例如，輸入 ︰  `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### 輸出

名稱 | 說明
--- | ---
文件 | 所有文件的陣列。 每份文件具有下列欄位 ︰ <ul><li>文件 []</li><li>名稱</li><li>項目識別碼</li><li>項目完整 URL</li><li>進階</li><li>項目編輯 URL</li><li>清單名稱</li><li>清單完整 URL</li></ul>
狀態 | 成功插入清單項目會傳回狀態碼 200 (確定)。


### 9.上傳到共用文件 (XML)

這個動作會將新文件上載到「共用文件」。 輸入文件應該是 XML 裝載。 動作的回應會是 XML 裝載。
 
#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
名稱 | 是 | 文件的名稱。
內容 | 是 | 文件的內容。
ContentTransferEncoding | 是 | 訊息的內容傳送編碼。 ("none"或"base64")
強制覆寫 | 是 | 如果設定為 TRUE，且指定名稱的文件存在，則會覆寫文件。
 
#### 輸出

名稱 | 說明
--- | ---
輸出 XML | 使用 XML 格式的上傳動作回應。
狀態 | 成功上傳文件會傳回狀態碼 200 (確定)。

### 10.從共用文件取得 (XML)

這個動作會從指定文件相對 URL (資料夾結構) 的文件庫中取得文件。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
文件相對 URI | 否 | 輸入相對於「共用文件」的文件 URL。 例如，輸入 ︰ *myspec1、 myfolder/orders*。
檔案類型 | 是 | 輸入檔案是二進位檔案或文字檔案。

#### 輸出

名稱 | 說明
--- | ---
輸出 XML | 文件內容
ContentTransferEncoding | 訊息的內容傳送編碼。 ("none"或"base64")
狀態 | 成功執行動作會傳回狀態碼 200 (確定)。

### 11.在工作中插入 (XML)

這個動作會在項目清單中加入項目。 輸入應是 XML 裝載。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
輸入 XML | 是 | XML 訊息，其中包含要插入之清單項目的欄位值。 您可以使用轉換 API 應用程式來產生 XML 訊息。

**請注意** 會以動態方式填入 「 清單 」 中的所有參數。 可以看見必要參數，而選擇性參數則在進階區段中。

#### 輸出

名稱 | 說明
--- | ---
ItemId | 加入清單項目的項目識別碼。
狀態 | 成功插入清單項目會傳回狀態碼 200 (確定)。


### 12.更新工作 (XML)

這個動作會在項目清單中更新項目。 輸入應是 XML 裝載。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ItemID | 是 | 清單項目的項目識別碼。
輸入 XML | 是 | XML 訊息，其中包含要插入之清單項目的欄位值。 您可以使用轉換 API 應用程式來產生 XML 訊息。

**請注意** 會以動態方式填入 「 清單 」 中的所有參數。 可以看見必要參數，而選擇性參數則在進階區段中。

#### 輸出

名稱 | 說明
--- | ---
狀態 | 成功更新清單項目會傳回狀態碼 200 (確定)。


### 13.從工作取得項目 (XML)

這個動作會從項目清單中取得項目。

#### 輸入

名稱 | 必要 | 說明
--- | --- | ---
ItemID | 是 | 清單項目的項目識別碼。

#### 輸出

名稱 | 說明
--- | ---
輸出 XML | XML 訊息，其中包含選取清單項目的欄位值。
狀態 | 成功執行動作會傳回狀態碼 200 (確定)。


## 混合式組態 (選用)

> [AZURE.NOTE] 在防火牆後方使用 SharePoint 內部時，才需要此步驟。

App Service 使用混合式組態管理員來安全地連線到內部部署系統。 如果您的連接器使用內部部署 SharePoint Server，則需要混合式連線管理員。

請參閱 [使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)。

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視 API 應用程式和連接器](app-service-api-manage-in-portal.md)。


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png


