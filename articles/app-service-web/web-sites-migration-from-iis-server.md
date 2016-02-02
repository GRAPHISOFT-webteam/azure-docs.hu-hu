<properties 
    pageTitle="將企業 Web 應用程式移轉至 Azure App Service" 
    description="示範如何使用 Web Apps 移轉小幫手，將現有的 IIS 網站快速移轉至 Azure App Service Web Apps" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    writer="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/10/2015" 
    ms.author="cephalin"/>


# 將企業 Web 應用程式移轉至 Azure App Service

您可以輕鬆地移轉現有網站執行在網際網路資訊服務 (IIS) 6 或更新版本才能 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。
>[AZURE.IMPORTANT] 我們將在 2015 年 7 月 14 日終止對 Windows Server 2003 的支援。 如果您的網站目前裝載於採用 Windows Server 2003 的 IIS 伺服器上，則 Web Apps 是一種讓您的網站保持連線的低風險、低成本和低摩擦方式，而 Web Apps 移轉小幫手可以協助您自動進行移轉程序。 

[Web Apps 移轉小幫手](https://www.movemetothecloud.net/) 可以分析您的 IIS 伺服器安裝、 識別哪些網站可以移轉至 App Service、 反白顯示任何無法移轉或平台上不支援的項目，然後將您的網站和相關聯的資料庫移轉至 Azure。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 在相容性分析期間驗證的元素

移轉小幫手會建立整備報表，來識別導致無法從內部部署 IIS 成功移轉至 Azure App Service Web Apps 之相關問題或封鎖問題的任何潛在原因。 一些需要留意的重要項目：

-   連接埠繫結 - Web Apps 僅支援連接埠 80 (適用於 HTTP) 和連接埠 443 (適用於 HTTPS 流量)。 系統將會忽略不同的連接埠組態，並將流量路由傳送至 80 或 443。
-   驗證 - Web Apps 預設支援匿名驗證以及應用程式所指定的表單驗證。 只有與 Azure Active Directory 和 ADFS 整合，才能使用 Windows 驗證。 目前不支援所有其他形式的驗證 (例如基本驗證)。
-   全域組件快取 (GAC) - Web Apps 不支援 GAC。 如果您的應用程式會參考您通常部署至 GAC 的組件，就必須部署至 Web Apps 上的應用程式 bin 資料夾。
-   IIS5 相容性模式 - Web Apps 不支援此模式。
-   應用程式集區 - 在 Web Apps 中，每個網站及其子應用程式都在相同的應用程式集區中執行。 如果您的網站上有多個利用多個應用程式集區的子應用程式，請將它們彙總到具有通用設定的單一應用程式集區，或將每個應用程式移轉至個別的 Web 應用程式。
-   COM 元件 - Web Apps 不允許在平台上註冊 COM 元件。 如果您的網站或應用程式使用任何 COM 元件，您必須以 Managed 程式碼予以重新撰寫，並與網站或應用程式一起部署這些元件。
-   ISAPI 篩選器 - Web Apps 可支援使用 ISAPI 篩選器。 您需要執行下列動作：
    -   使用您的 Web 應用程式部署 DLL
    -   註冊 Dll 使用 [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters)
    -   將 applicationHost.xdt 檔案置於網站根目錄中，包含下列內容：

            <?xml version="1.0"?>
            <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
            <configSections>
                <sectionGroup name="system.webServer">
                  <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
                </sectionGroup>
              </configSections>
            </configuration>

        如需如何與您的網站使用的 XML 文件轉換的範例，請參閱 [轉換您的 Microsoft Azure 網站](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx)。

-   不會移轉其他元件，例如 SharePoint、Front Page Server Extensions (FPSE)、FTP、SSL 憑證。

## 如何使用 Web Apps 移轉小幫手

這一節逐步說明如何移轉一些使用 SQL Server 資料庫並在內部部署 Windows Server 2003 R2 (IIS 6.0) 機器上執行之網站的範例：

1.  在 IIS 伺服器或用戶端電腦瀏覽至 [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/)

    ![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.  按一下 [專用 IIS 伺服器]**** 按鈕，以安裝 Web Apps 移轉小幫手。 在不久的將來會有更多的選項。
4.  按一下 [安裝工具]**** 按鈕，在電腦上安裝 Web Apps 移轉小幫手。

    ![](./media/web-sites-migration-from-iis-server/install-page.png)
    >[AZURE.NOTE] 您也可以按一下 [下載以進行離線安裝]**** 下載 ZIP 檔案，以便在未連線到網際網路的伺服器上安裝。 或者，您可以按一下 [上傳現有的移轉整備報告]****，這是一個進階選項，可處理您先前產生的現有移轉整備報告 (稍後說明)。

5.  在 [應用程式安裝]**** 畫面中，按一下 [安裝]**** 即可安裝在電腦上。 必要時也會安裝對應的相依項目，如 Web Deploy、DacFX 和 IIS。

    ![](./media/web-sites-migration-from-iis-server/install-progress.png)

    安裝後，Web Apps 移轉小幫手會自動啟動。

6.  選擇 [將網站與資料庫從遠端伺服器移轉到 Azure]****。 輸入遠端伺服器的管理認證，並按一下 [繼續]****。

    ![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

    您當然可以選擇從本機伺服器移轉。 當您想要從生產 IIS 伺服器移轉網站時，遠端選項很有用。

    此時移轉工具會檢查 IIS 伺服器的組態，例如網站、應用程式、應用程式集區和相依性，以識別可供移轉的候選網站。

8.  以下螢幕擷取畫面顯示三個網站 – **預設的網站**、**TimeTracker** 與 **CommerceNet4**。 全部都有我們想要移轉的相關資料庫。 選取您想評估的所有網站，然後按 [下一步]****。

    ![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)

9.  按一下 [上傳]**** 以上傳整備報表。 如果您按一下 [在本機儲存檔案]****，您可以稍後再執行移轉工具，並如先前所述上傳儲存的整備報表。

    ![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)

    一旦上傳整備報表，Azure 就會執行整備分析並顯示結果。 讀取每個網站的評估詳細資料，並確定您在您繼續之前已了解或已處理所有的問題。

    ![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12. 按一下 [開始移轉]**** 開始進行移轉。您現在會被重新導向至 Azure 來登入您的帳戶。 請務必以具有使用中 Azure 訂閱的帳戶進行登入。 如果您沒有 Azure 帳戶，可以在此註冊免費試用版。

13. 選取要針對移轉的 Azure Web 應用程式和資料庫使用的租用戶帳戶、Azure 訂用帳戶和區域，然後按一下 [開始移轉]****。 您可以選取稍後要移轉的網站。

    ![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14. 在下一個畫面上，您可以變更預設移轉設定，例如：

    - 使用現有的 Azure SQL Database 或建立新的 Azure SQL Database，並設定其認證
    - 選取要移轉的網站
    - 定義 Azure Web 應用程式及其連結的 SQL 資料庫的名稱
    - 自訂全域設定和網站層級設定

    以下螢幕擷取畫面顯示針對以預設設定移轉而選取的所有網站。

    ![](./media/web-sites-migration-from-iis-server/migration-settings.png)
    >[AZURE.NOTE] **啟用 Azure Active Directory** 自訂設定中的核取方塊可整合 Azure web 應用程式與 [Azure Active Directory](active-directory-whatis.md) ( **預設目錄**)。 如需有關同步處理 Azure Active Directory 與內部部署 Active Directory 的詳細資訊，請參閱 [目錄整合](http://msdn.microsoft.com/library/jj573653)。

16.  進行所有必要的變更後，按一下 [建立]**** 即可啟動移轉程序。 移轉工具會建立 Azure SQL Database 和 Azure Web 應用程式，然後發行網站內容和資料庫。 移轉進度會清楚地顯示在移轉工具中，而您最後將看到摘要畫面，詳述移轉的網站、移轉是否成功、新建立的 Azure Web 應用程式連結。

    If any error occurs during migration, the migration tool will clearly indicate the failure and rollback the changes. You will also be able to send the error report directly to the engineering team by clicking the **Send Error Report** button, with the captured failure call stack and build message body. 
    
    ![](./media/web-sites-migration-from-iis-server/migration-error-report.png)
    
    If migrate succeeds without errors, you can also click the **Give Feedback** button to provide any feedback directly. 

20. 按一下 Azure Web 應用程式的連結，確認已成功移轉。

21. 您現在可以管理 Azure App Service 中移轉的 Web 應用程式。 若要這樣做，請登入 [Azure 入口網站](https://portal.azure.com)。

22. 在 Azure 入口網站中，開啟 Web Apps 分頁，以查看您移轉的網站 (顯示為 Web 應用程式)，然後按一下任一個網站，以便開始管理 Web 應用程式，例如，設定連續發行、建立備份、自動調整，以及監視使用量或效能。

    ![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)





