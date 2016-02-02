<properties 
    pageTitle="在 Azure App Service 中管理 Web 應用程式" 
    description="適用於在 Azure App Service 中管理 Web 應用程式之資源的連結。" 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2015" 
    ms.author="erikre"/>


# 在 Azure App Service 中管理 Web 應用程式

本主題包含管理 web 應用程式中的資源連結 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。 所謂的管理包括所有維持 Web 應用程式順暢運作的工作。

在 Web 應用程式的生命週期內，從初始部署到正常運作、維護及更新，都需要執行不同的管理工作。

您可以在 Azure 入口網站中執行許多 Web 應用程式管理工作。

## 將 Web 應用程式部署到生產環境之前

### 選擇階層。

Azure App Service 提供了五種階層：免費、共用、基本、標準和高階。 如需功能以及每一層的定價資訊，請參閱 [定價詳細資料](/pricing/details/app-service/)。

- [應用程式服務計劃](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 可以讓您將同一個階層下的多個 web 應用程式。
- 您可以隨時 [切換階層](web-sites-scale.md) 建立 web 應用程式之後。

### 組態

使用 [Azure 入口網站](https://portal.azure.com/) 來設定各種組態選項。 如需詳細資訊，請參閱 [Azure App Service 中設定 web 應用程式](web-sites-configure.md)。 以下是簡短的檢查清單：

- 視需要選取 .NET、PHP、Java 或 Python 的**執行階段版本**。
- 如果您的 Web 應用程式使用 WebSocket 通訊協定，請啟用 **WebSockets**。 (這包括使用的應用程式 [ASP.NET SignalR](http://www.asp.net/signalr) 或 [socket.io](web-sites-nodejs-chat-app-socketio.md).)
- 您是否執行連續性的 Web 工作？ 如果是的話，請啟用 [Always On]****。
- 設定**預設文件** (如 index.html)。

除了以上基本組態設定之外，您也許還想要設定以下各項：

- **安全通訊端層 (SSL)** 加密。 若要使用 SSL 搭配自訂網域名稱，您必須取得 SSL 憑證並設定 Web 應用程式來加以使用。 請參閱 [Azure App Service 中的 web 應用程式啟用 HTTPS](web-sites-configure-ssl-certificate.md)。
- **自訂網域名稱。**您的 Web 應用程式會自動取得 azurewebsites.net 下的子網域。 您可以使其與自訂網域名稱 (如 contoso.com) 相關聯。 請參閱 [Azure App Service 中設定自訂網域名稱](web-sites-custom-domain-name.md)。

語言特有組態：

- **PHP**: [在 Azure App Service Web Apps 中設定 PHP](web-sites-php-configure.md)。
- **Python**: [設定 Python Azure 應用程式服務 Web 應用程式](web-sites-python-configure.md)


## 當 Web 應用程式執行時

當 Web 應用程式執行時，您會想要確認 Web 應用程式是否可供使用，以及是否能根據使用者流量而調整。 您可能也需要疑難排解錯誤。

### 監控

- 您可以透過 Azure 入口網站中， [新增效能度量](web-sites-monitor.md) 例如 CPU 使用率和用戶端要求數目。
- 如需深入探索，請使用 New Relic 來監視及管理效能。 請參閱 [.NET web 應用程式在 Azure App Service 中的使用 New Relic 應用程式效能管理](store-new-relic-web-sites-dotnet-application-performance-management.md)。
- [調整 web 應用程式](web-sites-scale.md) 中因應流量變化。 您可以根據階層來調整 VM 的數目和/或 VM 執行個體的大小。 在標準和高階階層中，您還可以設定自動調整，使 Web 應用程式得以按照固定排程或根據負載自動調整。

### 備份

- 設定 [自動備份](web-sites-backup.md) 的 web 應用程式。 深入了解備份 [這段影片](http://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)。
- 了解選項 [資料庫復原](../sql-database-business-continuity.md) Azure SQL 資料庫中。

### 疑難排解

- 如果發生錯誤，您可以 [在 Visual Studio 中疑難排解](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), 、 使用診斷記錄和即時偵錯在雲端中。
- 在 Visual Studio 之外，還有各種方法可用來收集診斷記錄。 請參閱 [啟用診斷記錄功能，在 Azure App Service web 應用程式的](web-sites-enable-diagnostic-log.md)。
- 若是 Node.js 應用程式，請參閱 [如何偵錯 Node.js web 應用程式在 Azure App Service 中的](web-sites-nodejs-debug.md)。

### 還原資料

- [還原](web-sites-restore.md) 先前已備份的 web 應用程式。


## 更新 Web 應用程式時

如果您尚未啟用自動備份，您可以建立 [手動備份](web-sites-backup.md)。

請考慮使用 [預備部署](web-sites-staged-publishing.md)。 此選項可讓您將更新發佈到與生產部署並行運作的預備部署。

如果您使用 Visual Studio Team Services，可以從原始檔控制設定連續部署：

- [使用 Team Foundation 版本控制 (TFVC)](../cloud-services-continuous-delivery-use-vso.md)
- [使用 Git](../cloud-services-continuous-delivery-use-vso-git.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]





[before you deploy your site to production]: #before-you-deploy-your-site-to-production 
[while your website is running]: #while-your-website-is-running 
[when you update your website]: #when-you-update-your-website 

