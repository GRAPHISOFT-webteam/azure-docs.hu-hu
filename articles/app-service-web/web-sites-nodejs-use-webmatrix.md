<properties 
    pageTitle="使用 WebMatrix 來建立 Node.js Web 應用程式並部署至 Azure。" 
    description="指導如何使用 WebMatrix 開發 Node.js 應用程式並部署至 Azure App Service Web Apps 的教學課程。" 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/20/2015"
    ms.author="robmcm"/>


# 使用 WebMatrix 來建立 Node.js Web 應用程式並部署至 Azure。

本教學課程會示範如何使用 WebMatrix 來開發 Node.js 應用程式，並將它部署到 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式。 WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含網站或 Web 應用程式開發所需的一切。 WebMatrix 包含一些功能讓您輕鬆地使用 Node.js，包括程式碼自動完成、預先建立的範本，以及 Jade、LESS 和 CoffeeScript 的編輯器支援。 深入了解 [WebMatrix](https://www.microsoft.com/web/webmatrix/)。

完成本指南後，您將會有一個在 Azure App Service 中執行的 Node.js Web 應用程式。
 
完成之應用程式的螢幕擷取畫面如下：

![Azure node Web site][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## Sign into Azure

執行下列步驟，即可在 Azure App Service 中建立 Web 應用程式。

1. 啟動 WebMatrix
2. 如果這是您第一次使用 WebMatrix，則會提示您登入 Azure。  否則，您可以按一下 **登入** 按鈕，然後選擇 **新增帳戶**。  選取此選項可 **登入** 使用 Microsoft 帳戶。

    ![Add Account][addaccount]

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

    ![Sign into Azure][signin]  


## 使用 Azure 內建的範本建立網站

1. 在 [開始] 畫面中，按一下 [ **新增** 按鈕，然後選擇 **範本庫** 從範本庫建立新的網站 ︰

    ![New site from Template Gallery][sitefromtemplate]

2. 在 **範本的站台** 對話方塊中，選取 **節點** ，然後選取 **Express 網站**。 最後，按一下 [ **下一步**。 如果您遺漏任何必要條件 **Express 網站** 範本，將會提示您安裝。

    ![select express template][webmatrix-templates]

3. 如果您已登入 Azure，則現在可選擇建立 App Service Web 應用程式做為您的本機網站。  請選擇唯一名稱，並選取您想在其中建立 App Service Web 應用程式的資料中心： 

    ![Create site on Azure][nodesitefromtemplateazure]
    
4. 當 WebMatrix 完成建立本機網站與 App Service Web 應用程式之後，就會顯示 WebMatrix IDE。

    ![webmatrix ide][webmatrix-ide]

##將應用程式發行至 Azure

1. 在 WebMatrix 中，按一下 [ **發行** 從 **首頁** 要顯示功能區 **發行預覽** 站台] 對話方塊。

    ![publish preview][webmatrix-node-publishpreview]

2. 按一下 [ **繼續**。 發佈完成時，App Service Web 應用程式的 URL 會顯示在 WebMatrix IDE 底部

    ![publish complete][webmatrix-publish-complete]

3. 按一下連結在瀏覽器中開啟 App Service Web 應用程式。

    ![Express 網站][webmatrix-node-express-site]

##修改和重新發行應用程式

您可以輕鬆地修改和重新發行應用程式。 在這裡，您將簡單地變更標題中，在 **index.jade** 檔案，並重新發行應用程式。

1. 在 WebMatrix 中，選取 **檔案**, ，然後展開 **檢視** 資料夾。 開啟 **index.jade** 按兩下檔案。

    ![webmatrix viewing index.jade][webmatrix-modify-index]

2. 將段落行變更為：

        p Welcome to #{title} with WebMatrix on Azure!

3. 儲存您所做的變更，然後按一下發行圖示。 最後，按一下 [ **繼續** 中 **發行預覽** 對話方塊中，等待發行更新。

    ![publish preview][webmatrix-republish]

4. 發佈完成時，使用發行程序完成時所傳回的連結，以查看已更新的 App Service Web 應用程式。

    ![Azure 節點 Web 應用程式][webmatrix-node-completed]

##後續步驟

若要深入了解 Azure，以及如何指定要搭配您的應用程式的版本所隨附的 Node.js 版本，請參閱 [Azure 應用程式中指定 Node.js 版本](../nodejs-specify-node-version-azure-apps.md)。

如果您遇到問題的應用程式部署至 Azure 之後，請參閱 [如何偵錯 Node.js web 應用程式在 Azure App Service 中的](web-sites-nodejs-debug.md) 獲得診斷問題的資訊。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 

