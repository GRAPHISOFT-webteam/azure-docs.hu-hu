<properties
    pageTitle="在 Azure 應用程式服務中建立 WordPress Web 應用程式 | Microsoft Azure"
    description="了解如何使用 Azure 入口網站為 WordPress 部落格建立新的 Azure Web 應用程式。"
    services="app-service\web"
    documentationCenter="php"
    authors="tfitzmac"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="hero-article"
    ms.date="11/13/2015"
    ms.author="tomfitz"/>

# 在 Azure App Service 中建立 WordPress Web 應用程式

本教學課程顯示如何在 Azure Marketplace 尋找應用程式，並將它安裝在 App Service Web 應用程式中。 本教學課程使用 WordPress 部落格網站做為範例，但對於許多其他 Marketplace 應用程式，此程序類似。

當您完成本教學課程時，您的專屬 WordPress 部落格網站將在雲端中啟動並執行中。

![WordPress 網站](./media/web-sites-php-web-site-gallery/wpdashboard.png)

您將了解：

* 如何在 Azure Marketplace 尋找應用程式範本。
* 如何在 Azure App Service 建立以範本為基礎的 Web 應用程式。
* 如何為新的 Web 應用程式和 MySQL 資料庫設定 Azure App Service 設定。

Azure Marketplace 提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。 Web 應用程式上建立各種受歡迎的架構，例如 [PHP](/develop/nodejs/) WordPress 在本例中， [.NET](/develop/net/), ，[Node.js](/develop/nodejs/), ，[Java](/develop/java/), ，和 [Python](/develop/python/), 、 等等。 從 Azure Marketplace 所需的唯一軟體建立的 web 應用程式是您使用瀏覽器 [Azure 入口網站](https://portal.azure.com/)。 

> [AZURE.NOTE]
> 若要完成此教學課程，您需要 Microsoft Azure 帳戶。 如果您沒有這類帳戶，可以[啟用自己的 Visual Studio 訂閱者權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)，或是[申請免費試用](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)。
>
> 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)。 您可以於該處，在 App Service 中立即建立短期的入門 Web app - 不需信用卡，不需任何承諾。

## 選取 WordPress 和設定 Azure App Service

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [ **新**。
    
    ![建立新的][5]
    
3. 搜尋 **WordPress**, ，然後按一下 [ **WordPress**。

    ![WordPress 來源清單][7]
    
5. 讀取 WordPress 應用程式的描述之後, 按一下 **建立**。

    ![建立](./media/web-sites-php-web-site-gallery/create.png)

4. 輸入中的 web 應用程式的名稱 **Web 應用程式** 方塊。

    此名稱在 azurewebsites.net 網域中必須是唯一的，因為 Web 應用程式的 URL 將是 {name}.azurewebsites.net。 如果您輸入的名稱不是唯一的，紅色驚嘆號會出現在文字方塊中。

8. 如果您有多個訂用帳戶，請選擇您想要使用的訂用帳戶。 

5. 選取 **資源群組** 或建立新的帳戶。

    如需資源群組的詳細資訊，請參閱 [使用 Azure 入口網站來管理您的 Azure 資源](../resource-group-portal.md)。

5. 選取 **App Service 方案/位置** 或建立新的帳戶。

    如需 App Service 方案的詳細資訊，請參閱 [Azure App Service 方案概觀](../azure-web-sites-web-hosting-plans-in-depth-overview.md) 

7. 按一下 [ **資料庫**, ，然後在 **新的 MySQL 資料庫** 刀鋒視窗提供設定 MySQL 資料庫所需的值。

    a. 輸入新名稱或保留預設名稱。

    b. 保留 **資料庫類型** 設 **共用**。

    c. 選擇與您選擇 Web 應用程式的同一位置。

    d. 選擇定價層。 對本教學課程來說，Mercury (允許免費使用的連線數和可用磁碟空間下限) 夠用。

8. 在 **新的 MySQL 資料庫** 刀鋒視窗中，按一下 [ **確定**。 

8. 在 **WordPress** 刀鋒視窗中，接受法律條款，然後再按一下 **建立**。 

    ![設定 Web 應用程式](./media/web-sites-php-web-site-gallery/configure.png)

    Azure App Service 即會建立 Web 應用程式，通常不到一分鐘。 您可以按一下入口網站頁面頂端的鐘圖示查看進度。

    ![進度指示器](./media/web-sites-php-web-site-gallery/progress.png)

## 啟動和管理 WordPress Web 應用程式
    
7. 當您完成建立 Web 應用程式時，請在 Azure 入口網站中瀏覽至您在其中建立應用程式的資源群組，然後您就能看到 Web 應用程式和資料庫。

    燈泡圖示的額外資源 [Application Insights](/services/application-insights/), ，它會提供 web 應用程式監視的服務。

1. 在 **資源群組** 刀鋒視窗中，按一下 [web 應用程式列。

    ![設定 Web 應用程式](./media/web-sites-php-web-site-gallery/resourcegroup.png)

2. 在 Web 應用程式刀鋒視窗中，按一下 [ **瀏覽**。

    ![網站 URL][browse]

3. 在 WordPress 中 **歡迎** 頁面上，輸入 WordPress 所需的組態資訊，然後按一下 **安裝 WordPress**。

    ![設定 WordPress](./media/web-sites-php-web-site-gallery/wpconfigure.png)

4. 使用您在建立的認證登入 **歡迎** 頁面。  

5. 您的網站 [儀表板] 頁面隨即開啟。    

    ![WordPress 網站](./media/web-sites-php-web-site-gallery/wpdashboard.png)

## 後續步驟

您已了解如何從資源庫建立及部署 PHP Web 應用程式。 如需在 Azure 中使用 PHP 的詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。

如需如何使用 App Service Web Apps 的詳細資訊，請參閱頁面左邊上的連結 (適用於寬瀏覽器視窗) 或頁面頂端的連結 (適用於窄瀏覽器視窗)。 

## 變更的項目
* 變更從應用程式服務的網站的指南，請參閱 [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)。

[5]: ./media/web-sites-php-web-site-gallery/startmarketplace.png
[7]: ./media/web-sites-php-web-site-gallery/search-web-app.png
[browse]: ./media/web-sites-php-web-site-gallery/browse-web.png


