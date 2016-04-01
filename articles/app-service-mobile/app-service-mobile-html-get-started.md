<properties
    pageTitle="開始使用 HTML/JavaScript 應用程式的行動應用程式後端 | Azure App Service Mobile Apps"
    description="遵循此教學課程，開始使用 Azure 行動應用程式後端進行 HTML5 和 JavaScript 的 Web 應用程式開發。"
    services="app-service\mobile"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article"
    ms.date="11/18/2015"
    ms.author="glenga"/>


#建立 HTML 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)] 
&nbsp;  
<!--- [AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]-->

>[AZURE.IMPORTANT] 本主題是目前不支援行動應用程式因為 HTML/JavaScript 應用程式快速入門暫時移除從 Azure 入口網站。 我們計劃在不久的將來將它加回來。 感謝您耐心配合。

##概觀

本教學課程說明如何將雲端型後端服務加入到 HTML5/JavaScript Web 應用程式。 如需詳細資訊，請參閱 [什麼是行動應用程式](app-service-mobile-value-prop.md)。 

以下是完成應用程式的螢幕擷取畫面：

![已完成的應用程式的螢幕擷取畫面](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

完成本教學課程是 HTML 應用程式的所有其他 Mobile Apps 教學課程的必要條件。 

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

* [Visual Studio Community 2013] 或更新版本。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://tryappservice.azure.com/?appServiceName=mobile), ，您可以立即建立短期入門行動應用程式的應用程式服務中。 不需要信用卡；無需承諾。

##建立新的行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。 接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

## 下載伺服器專案

1. 在 [Azure Portal], ，按一下 [ **全部瀏覽** > **Web 應用程式**, ，然後按一下您剛才建立的行動應用程式後端。 

2. 在行動應用程式後端中，按一下 [ **所有設定** 歲以下 **行動應用程式** 按一下 **快速入門** > **HTML/JavaScript**。

3. 在 **下載並執行您的伺服器專案** 中 **建立新的應用程式**, ，按一下 [ **下載**, 、 壓縮的專案將檔案解壓縮至本機電腦，並在 Visual Studio 中開啟方案。

4. 建置專案以還原 NuGet 封裝。

##在伺服器專案中啟用 CORS

跨原始資源共用 (CORS) 可讓您的 Web 架構應用程式指出來自哪些網域的要求是安全的且瀏覽器應可允許。 您必須為將存取您的行動應用程式後端的每個網站新增一個 CORS 項目。 您可以使用標準 ASP.NET Web API 行為控制 CORS 設定。 如需詳細資訊，請參閱 [ASP.NET Web API 中啟用跨來源要求](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors)。

根據預設，您將從入口網站下載的用戶端快速入門專案會在連接埠 8000 的 localhost 上執行。 因此，您接下來會在伺服器專案中為 `http://localhost:8000` 啟用 CORS。  

1. 在 Visual Studio [工具] 功能表中，按一下 [ **NuGet 封裝管理員** > **Package Manager Console**, ，選取為 Nuget.org **套件來源** 並在主控台視窗中執行下列命令 ︰
 
        Install-Package Microsoft.AspNet.WebApi.Cors  

2. 開啟 App_Start/Startup.MobileApp.cs 專案檔案，然後新增下列 using 陳述式：

        using System.Web.Http.Cors;

3. 接下來，加入下列程式碼以 **Startup.ConfigureMobileApp** 方法之後 **HttpConfiguration** (*config*) 建立 ︰

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. 儲存您的更新。

接下來，您會將啟用 CORS 的專案部署到 Azure。

##將伺服器專案發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##下載並執行用戶端專案

1. 在 [行動應用程式後端的分頁中，按一下 [ **所有設定** 歲以下 **行動應用程式** 按一下 **快速入門** > **HTML/JavaScript**。 

2.  在 **下載並執行您的 HTML/Javascript 專案** 中 **建立新的應用程式**, ，按一下 [ **下載** ，並將此壓縮的專案檔案儲存到本機電腦。

3. 瀏覽至儲存壓縮的專案檔案的位置、 您的電腦上將檔案解壓縮並啟動一個從下列的命令檔 **伺服器** 子資料夾。

    + **啟動 windows** （Windows 電腦）
    + **launch-mac.command** （Mac OS X 電腦）
    + **launch-linux.sh** （Linux 電腦）

    > [AZURE.NOTE] 在 Windows 電腦上，輸入 `R` PowerShell 要求您確認您想要執行指令碼時。 因為指令碼是從網際網路中下載，所以您的網頁瀏覽器可能會警告您不要執行指令碼。 發生此情況時，您必須要求瀏覽器繼續載入指令碼。

    如此會在本機電腦上啟動網頁伺服器來裝載新的應用程式。

4. 在網頁瀏覽器中開啟 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> 以啟動應用程式。 用戶端應用程式會設定為連接到 Azure 中您的行動應用程式後端。

5. 在應用程式中，輸入有意義的文字，例如 _完成教學課程_, 中 **輸入新工作**, ，然後按一下 [ **新增**。

    ![執行應用程式](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。 要求中的資料會插入行動應用程式結構描述的 TodoItem 資料表中。 服務會傳回資料表中儲存的項目，而該資料會顯示在應用程式的第二欄。

    > [AZURE.TIP] 您可以檢閱存取行動服務來查詢和插入資料，在 app.js 檔案中找到的程式碼。 

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 


