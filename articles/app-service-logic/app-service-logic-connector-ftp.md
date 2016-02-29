<properties
    pageTitle="在 Logic Apps 中使用 FTP 連接器 | Microsoft Azure App Service"
    description="如何建立並設定 FTP 連接器或 API 應用程式，並在 Azure App Service 的邏輯應用程式中使用它"
    authors="rajram"
    manager="dwrede"
    editor=""
    services="app-service\logic"
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/30/2015"
    ms.author="rajram"/>

# 開始使用 FTP 連接器並將它加入您的邏輯應用程式
連線至 FTP 伺服器以移動資料或檔案。 FTP 連接器的主要功能如下：

- 依照需求從 FTP 伺服器提取檔案
- 根據可設定的排程執行輪詢
- 根據 FTP 伺服器中的新文件輪詢 FTP 伺服器並觸發邏輯流程
- 指定 FTP 伺服器做為 IP 位址、連接埠、密碼和主機名稱
- 依照需求執行傳送的能力
- 依照需求在 FTP 伺服器上刪除檔案的能力

您可以將 FTP 連接器加入您的商務工作流程，就能在邏輯應用程式的該工作流程中處理資料。 

## 建立新的 FTP 連接器
若要建立新的 FTP 連接器，請遵循下面所述的步驟。
- 啟動 Azure 入口網站
- 開啟使用 [+ 新增] (位於頁面底部)-> [Web + 行動--> [Azure Marketplace:  
![啟動 Azure Marketplace][] 1

- 按一下 API 應用程式
- 搜尋 FTP，然後選取 FTP 連接器:  
![選取 FTP 連接器][] 2

- 按一下 [建立]
- 在開啟的 FTP 連接器刀鋒視窗，提供下列資料:  
![建立 FTP 連接器][] 3

- **位置** -選擇您要部署連接器的地理位置
- **訂閱** -選擇您要建立此連接器的訂閱
- **資源群組** -選取或建立連接器所在的資源群組
- **Web 主控方案** -選取或建立 web 主控方案
- **定價層** -選擇連接器的定價層
- **名稱** -提供 FTP 連接器的名稱
- **套件設定**
    - **伺服器位址** -指定 FTP 伺服器名稱或 IP 位址
    - **使用者名稱** -指定要連接到 FTP 伺服器的使用者名稱
    - **密碼** -指定要連接到 FTP 伺服器的密碼
    - **根資料夾** -指定根資料夾路徑
    - **使用二進位** -指定 true 會使用二進位傳輸模式，不適用於 ASCII
    - **使用 SSL** -指定 true 以使用 FTP over SSL/TLS 安全通道
    - **伺服器連接埠** -指定 FTP 伺服器連接埠號碼
- 按一下 [建立]。 將建立新的 FTP 連接器。

## 在邏輯應用程式中使用 FTP 連接器
一旦建立 FTP 連接器，即可從流程中取用。

透過 [+ 新增] -> [Web + 行動] -> [LogicApp] 建立新流程。 提供的中繼資料，包括資源群組的流程:  
![建立邏輯應用程式][] 4

按一下 [ *觸發程序和動作*。 流程設計工具隨即開啟:  
![邏輯應用程式空白流程設計工具][] 5

可以使用 FTP 連接器做為觸發程序和動作。

### 觸發程序
在空的流程設計工具中，按一下右邊組件庫] 窗格中的 FTP 連接器:  
![選擇 FTP 觸發程序][] 6

FTP 連接器具有一個觸發程序 -「檔案可用 (讀取然後刪除)」。 此觸發程序︰

- 輪詢新檔案的資料夾路徑
- 具現化每個新檔案的邏輯流程
- 在具現化邏輯流程之後，從資料夾路徑中刪除檔案

按一下 [檔案可用 (讀取然後刪除)' 觸發程序:  
![基本輸入 FTP 觸發程序][] 7

輸入可協助您設定依排定的頻率輪詢特定資料夾路徑。 基本輸入如下：
- 頻率 - 指定 FTP 輪詢的頻率
- 間隔 - 指定排定頻率的間隔
- 資料夾路徑 - 在 FTP 伺服器中指定資料夾路徑
- 類型檔案 - 指定檔案類型是文字或二進位檔

按一下省略符號 [...] 顯示進階的輸入:  
![基本輸入 FTP 觸發程序][] 8

進階的輸入包括:
- 檔案遮罩 - 指定輪詢時的檔案遮罩
- 排除檔案遮罩 - 指定輪詢時排除的檔案遮罩

提供輸入，然後按一下核取記號以完成輸入的設定:  
![基本輸入 FTP 觸發程序][] 9

請注意，設定的 FTP 觸發程序會顯示已設定的輸入參數及輸出。

#### 在後續動作中使用 FTP 觸發程序的輸出
FTP 觸發程序的輸出可做為流程中其他某些動作的輸入。

您可以按一下 [...] 輸入對話方塊中的動作，然後選取下拉式方塊，直接從 FTP 的輸出。

您也可以直接在動作的輸入方塊中撰寫運算式。 要參考 FTP 觸發程序輸出的流程運算式如下所示︰

    @triggers('ftpconnector').outputs.body.Content

### 動作
按一下右窗格中的 FTP 連接器。 FTP 連接器列出支援的動作:  
!FTP 動作清單[] 10

FTP 連接器支援下列動作︰

- **取得檔案** -取得特定檔案的內容
- **將檔案上傳** -將檔案上傳至 FTP 資料夾路徑
- **刪除檔案** -從 FTP 資料夾路徑刪除檔案
- **列出檔案** -列出 FTP 資料夾路徑中的所有檔案

舉例來說 - 上傳檔案。 按一下 [上傳檔案]。

會先顯示基本輸入:  
![上傳檔案動作的基本輸入][] 11


- **內容** -指定要上傳檔案的內容
- **內容轉移編碼** -指定 none 或 base64
- **檔案路徑** -指定要上傳之檔案的檔案路徑

按一下 [...] 可顯示進階輸入:  
![上傳檔案動作的基本輸入][] 12


- **如果附加存在** -True 或 False ' 時附加存在 '。 啟用時，資料會附加至檔案 (如果存在的話)。 停用時，則會覆寫檔案 (如果存在的話)。
- **暫存資料夾** -選擇性。 如果有提供，配接器會將檔案上傳到 [暫存資料夾路徑]，上傳完成之後檔案會移至 [資料夾路徑]。 [暫存資料夾路徑] 應和 [資料夾路徑] 在相同的實體磁碟上，以確保移動作業自動進行。 只在啟用 [存在時附加] 屬性，才能使用暫存資料夾。

提供輸入，然後按一下核取記號以完成輸入的設定:  
![設定上傳檔案動作][] 13

「檔案路徑」參數設定為︰

    @concat('/Output/',triggers().outputs.body.FileName)

請注意，已設定的 [FTP 上傳檔案] 動作會顯示輸入參數，以及輸出參數。

#### 使用先前動作的輸出做為 FTP 動作的輸入
請注意，在已設定的螢幕擷取畫面中，[內容] 值會設定為運算式。

    @triggers().outputs.body.Content


您可以將它設定為任何您想要的值。 這只是一個範例。 運算式會採用邏輯應用程式觸發程序的輸出，並將它做為要上傳的檔案內容。 假設您要使用前一個動作的輸出，像是轉換。 在此情況下，運算式會是

    @actions('transformservice').outputs.body.OutputXML

## 進一步運用您的連接器
現在已建立連接器，您可以將它加入到使用邏輯應用程式的商務工作流程。 請參閱 [什麼是邏輯應用程式?](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure 邏輯應用程式，請移至 [試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic), ，您可以立即建立短期入門邏輯應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

檢視在 Swagger REST API 參考 [連接器和 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

您也可以檢閱連接器的效能統計資料及控制安全性。 請參閱 [管理和監視內建 API 應用程式和連接器](app-service-logic-monitor-your-connectors.md)。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

