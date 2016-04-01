<properties 
   pageTitle="在 Logic Apps 中使用 Azure 儲存體 Blob 連接器 | Microsoft Azure App Service" 
   description="如何建立並設定 Azure 儲存體 Blob 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它" 
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
   ms.author="rajram"/>
   
# 開始使用 Azure 儲存體 Blob 連接器並將它加入您的邏輯應用程式 
連接到 Azure 儲存體 Blob，以便從 Blob 容器上傳、下載及刪除 Blob。 連接器在 Logic Apps 中是用作「工作流程」的一部分。 

## 觸發程序和動作
*觸發程序* 所發生的事件。 例如，訂單更新時或加入新客戶時。  *動作* 是觸發程序的結果。 例如，當訂單更新時，傳送警示給銷售人員。 或者，當加入新客戶時，傳送歡迎電子郵件給新客戶。 

儲存體 Blob 連接器可以在邏輯應用程式中用作動作，且支援 JSON 和 XML 格式的資料。 儲存體 Blob 連接器目前沒有觸發程序。 

儲存體 Blob 連接器提供下列觸發程序和動作： 

觸發程序 | 動作
--- | ---
None | <ul><li>取得 Blob ︰ 從容器取得特定的 Blob</li><li>上傳 Blob ︰ 上傳新的 Blob 或更新現有的 Blob</li><li>刪除 Blob ︰ 從容器中刪除特定的 Blob</li><li>列出 Blob ︰ 列出目錄中的所有 blob</li><li>快照 Blob ︰ 建立特定 Blob 的唯讀快照集</li><li>複製 Blob ︰ 從另一個 Blob 複製，以建立新的 Blob。  來源 Blob 可能位於相同的帳戶或另一個帳戶中。</li></ul>


## 建立 Azure 儲存體 Blob 連接器

連接器可以在邏輯應用程式內建立，或直接從 Azure Marketplace 建立。 從 Marketplace 建立連接器：  

1. 在 Azure 開始面板中，選取 **Marketplace**。
2. 搜尋"Blob":  
    ![選取 Azure 儲存體 Blob 連接器][2]

3. 選取它，然後選取 **建立**。
4. 輸入名稱、App Service 方案和其他屬性。
5. 輸入下列封裝設定：

    名稱 |需要 | 描述
--- | --- | ---
容器 /SAS URI |[是 |輸入 Blob 容器的 URI。 URI 也可能包括 SAS 權杖。 例如，輸入 http://*storageaccountname*.blob.core.windows.net/containername 或 http://*storageaccountname*.blob.core.windows.net/containername?sr=c 和 si = mypolicy sig = signatureblah
存取金鑰 |否 |輸入有效的主要或次要儲存體帳戶存取金鑰。 如果您使用 SAS Token 進行驗證，請保留此欄位空白。

    ![建立 Azure 儲存體 Blob 連接器][3]

6. 按一下 [ **建立**。

## 在邏輯應用程式中使用 Azure 儲存體 Blob 連接器
建立 Azure 儲存體 Blob 連接器後，即可立即加入工作流程。

1. 建立新的邏輯應用程式：[新增] -> [Web + 行動] -> LogicApp。 輸入邏輯應用程式的屬性：  
    ![建立邏輯應用程式][4]

2. 按一下 [ **觸發程序和動作**。 工作流程設計工具隨即開啟：  
    ![邏輯應用程式空白流程設計工具][5]

3. 選取右窗格中的 Azure 儲存體 Blob 連接器。 連接器會列出可用的動作：  
    ![Azure 儲存體 Blob 動作的清單][10]

4. 在此案例中，我們將使用 **上傳 Blob** 動作 ︰  
    ![上傳 Blob 動作的輸入][11]

5. 輸入輸入值，並選取核取記號以完成設定：

    輸入 |描述
--- | ---
Blob 路徑 |決定要上傳 Blob 的路徑。 此路徑相對於已設定的容器路徑。
Blob 寫入內容 |輸入要上傳內容和 Blob 的屬性。
內容轉移編碼 |輸入 none 或 Base64。
覆寫 |當設定為 true，現有的 Blob 會覆寫。 設定為 false 時，會在 Blob 已存在於相同路徑時傳回錯誤。

請注意，已設定的 [Azure 儲存體 Blob 上傳 Blob] 動作會顯示輸入參數以及輸出參數。

#### 使用先前動作的輸出做為 Azure 儲存體 Blob 動作的輸入
在上一個螢幕擷取畫面， **內容** 值可以是運算式 ︰

    @triggers().outputs.body.Content

您可以將它設定為任何您想要的值。 運算式會採用邏輯應用程式觸發程序的輸出，並將它做為要上傳的檔案內容。 例如，您想要使用轉換的輸出。 在此情況下，運算式將：

    @actions('transformservice').outputs.body.OutputXML

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；無需承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!-- Image reference -->
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG
 


