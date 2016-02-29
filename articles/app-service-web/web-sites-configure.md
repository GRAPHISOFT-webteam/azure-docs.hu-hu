<properties 
    pageTitle="在 Azure App Service 中設定 Web 應用程式" 
    description="如何在 Azure App Service 中設定 Web 應用程式" 
    services="app-service" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="erikre"/>


# 在 Azure App Service 中設定 Web 應用程式 #

本主題說明如何設定 web 應用程式使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## 應用程式設定

1. 在 [Azure 入口網站](https://portal.azure.com), ，開啟 web 應用程式的刀鋒視窗。
2. 按一下 [ **所有設定**。
3. 按一下 [ **應用程式設定**。

![](./media/web-sites-configure/configure01.png)

 **應用程式設定** 刀鋒視窗中的設定以數種類別來分組。

### 一般設定

**Framework 版本**。 如果您的應用程式使用下列任何 Framework，請設定下列選項： 

- **.NET framework**: 設定.NET framework 版本。 
- **PHP**: 設定 PHP 版本，或 * * OFF * * 以停用 PHP。 
- **Java**: 選取 Java 版本或 **OFF** 以停用 Java。 使用 **Web 容器** 選項來選擇 Tomcat 或 Jetty 版本。
- **Python**: 選取 Python 版本，或 **OFF** 以停用 Python。

在技術上，針對您的應用程式啟用 Java 就會停用 .NET、PHP 與 Python 選項。

<a name="platform"></a>
**平台**。 選取 Web 應用程式是否會在 32 位元或 64 位元環境中執行。 64 位元環境需要 [基本] 或 [標準] 模式。 [免費] 與 [共用] 模式一律於 32 位元環境中執行。

**Web 通訊端**。 設定 **ON** 啟用 WebSocket 通訊協定; 例如，如果您的 web 應用程式使用 [ASP.NET SignalR](http://www.asp.net/signalr) 或 [socket.io](web-sites-nodejs-chat-app-socketio.md)。

<a name="alwayson"></a>
**永遠開啟**。 根據預設，Web 應用程式如果閒置一段時間，就會卸載。 此舉有助於系統保留資源。 在基本或標準模式中，您可以啟用 **Always On** ，讓應用程式都能載入所有的時間。 如果您的應用程式執行連續的 web 工作，您應該啟用 **Always On**, ，或 web 工作可能無法可靠地執行。

**受管理的管線版本**。 設定 IIS [管線模式](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application)。 除非您擁有的舊版應用程式需要舊版 IIS，否則請保留 [整合 (預設值)] 的設定。

**自動交換**。 如果您針對部署位置啟用「自動交換」，當您將更新推送到該位置時，App Service 就會將 Web 應用程式自動交換至生產位置。 如需詳細資訊，請參閱 [部署至預備位置的 Azure App Service 中的 web 應用程式] (web-站台-預備-publishing.md)。


### Debugging

**遠端偵錯**。 啟用遠端偵錯。 一經啟用，您就可以使用 Visual Studio 中的遠端偵錯工具，直接連接到您的 Web 應用程式。 遠端偵錯將保持啟用達 48 小時。 


### 應用程式設定

本區段包含您的 Web 應用程式將在啟動時載入的名稱/值組。 

- 如果是 .NET 應用程式，這些設定就會在執行階段插入 .NET 設定 `AppSettings`，並覆寫現有的設定。 

- PHP、Python、Java 和 Node 應用程式可以在執行階段以環境變數的形式存取這些設定。 系統會為每個應用程式設定建立兩個環境變數，一個變數具有由應用程式設定項目指定的名稱，另一個則具有 APPSETTING_ 前置詞。 這兩個變數都包含相同的值。

### 連接字串

連結資源的連接字串。 

如果是 .NET 應用程式，這些連接字串就會在執行階段插入 .NET 設定 `connectionStrings` 的設定，並覆寫索引鍵相當於所連結資料庫名稱的現有項目。 

如果是 PHP、Python、Java 及 Node 應用程式，您可以在執行階段將這些設定視為環境變數使用，並加上連線類型前置詞。 環境變數首碼如以下所示： 

- SQL Server：SQLCONNSTR_
- MySQL：MYSQLCONNSTR_
- SQL 資料庫：SQLAZURECONNSTR_
- 自訂：CUSTOMCONNSTR_

例如，如果 MySql 連接字串命名為 `connectionstring1`，則可透過環境變數 `MYSQLCONNSTR_connectionString1` 加以存取。

### 預設文件

預設文件是顯示於網站根 URL 上的網頁。  系統會使用清單中第一個相符的檔案。 

Web 應用程式可能會使用根據 URL 路由傳送的模組，而非處理靜態內容，若是如此，就不會有這類預設文件。    

### 處理常式對應

使用此區域可新增自訂指令碼處理器，以處理特定副檔名的要求。 

- **延伸模組**。 要處理的副檔名，例如 *.php 或 handler.fcgi。 
- **指令碼處理器路徑**。 指令碼處理器的絕對路徑。 符合該副檔名的檔案要求，將由指令碼處理器來處理。 使用路徑 `D:\home\site\wwwroot` 以指出應用程式的根目錄。
- **其他引數**。 指令碼處理器選用的命令列引數 


### 虛擬應用程式與目錄 
 
若要設定虛擬應用程式與目錄，請指定個別虛擬目錄及其相對於網站根目錄的對應實體路徑。 或者，您可以選取 **應用程式** 核取方塊，勾選虛擬目錄做為應用程式。


## 啟用診斷記錄

啟用診斷記錄：

1. 在 web 應用程式的刀鋒視窗中，按一下 [ **所有設定**。
2. 按一下 [ **診斷記錄**。 

從支援記錄功能的 Web 應用程式寫入診斷記錄的選項： 

- **應用程式記錄**。 將應用程式記錄寫入檔案系統。 記錄功能會持續運作 12 小時。 

**層級**。 應用程式記錄功能一經啟用，此選項便會指定要記錄的資訊數量 (錯誤、警告、資訊或詳細資訊)。

**Web 伺服器記錄**。 記錄會儲存為 W3C 延伸的記錄檔格式。 

**詳細的錯誤訊息**。 將詳細的錯誤訊息儲存為 .htm 檔案。 檔案會儲存在 /LogFiles/DetailedErrors 下方。 

**失敗要求追蹤**。 將失敗的要求記錄於 XML 檔案中。 檔案會儲存在 /logfiles/w3svc*xxx*, ，其中 xxx 是唯一的識別碼。 此資料夾內含有一個 XSL 檔案和一或多個 XML 檔案。 請務必下載 XSL 檔案，因為 XSL 檔案可提供格式化和篩選 XML 檔案內容的功能。

若要檢視記錄檔，您必須建立 FTP 認證，如下所示：

1. 在 web 應用程式的刀鋒視窗中，按一下 [ **所有設定**。
2. 按一下 [ **部署認證**。
3. 請輸入使用者名稱和密碼。
4. 按一下 [ **儲存**。

![](./media/web-sites-configure/configure03.png)


完整的 FTP 使用者名稱為 [app\username 」， *應用程式* 是 web 應用程式的名稱。 在使用者名稱列在 web 應用程式分頁中， **Essentials**。  

![](./media/web-sites-configure/configure02.png)

## 其他配置作業

### SSL 

在 [基本] 或 [標準] 模式中，您可以上傳自訂網域的 SSL 憑證。 如需詳細資訊，請參閱 [啟用 web 應用程式的 HTTPS](web-sites-configure-ssl-certificate.md)。 

若要檢視您上傳的憑證，請按一下 [ **所有設定** > **自訂網域和 SSL**。

### 網域名稱

新增 Web 應用程式的自訂網域名稱。 如需詳細資訊，請參閱 [Azure App Service 中設定 web 應用程式的自訂網域名稱](web-sites-custom-domain-name.md)。

若要檢視您的網域名稱，請按一下 [ **所有設定** > **自訂網域和 SSL**。

### 部署

- 設定連續部署。 請參閱 [使用 Git 來部署 Azure App Service 中的 Web 應用程式](web-sites-publish-source-control.md)
- 部署位置。 請參閱 [Azure App Service 中的 Web 應用程式部署至預備環境](web-sites-staged-publishing.md)

若要檢視您的部署位置，請按一下 [ **所有設定** > **部署位置**。


### 監視

在基本或標準模式中，您可以測試 HTTP 或 HTTPS 端點，最多三個地理位置的可用性。 如果 HTTP 回應碼為錯誤 (4xx 或 5xx)，或是當回應時間超過 30 秒時，監視測試即告失敗。 如果所有指定位置上的端點監視測試全都成功，表示該端點可用。 

如需詳細資訊，請參閱 [How to: 監視 web 端點狀態](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 後續步驟

- [設定自訂網域名稱](web-sites-custom-domain-name.md)
- [啟用 HTTPS](web-sites-configure-ssl-certificate.md)
- [在 Azure App Service 中調整 Web 應用程式規模](web-sites-scale.md)
- [在 Azure App Service 中監視 Web 應用程式的基本概念](web-sites-monitor.md)

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 

