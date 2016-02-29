<properties
    pageTitle="Node.js 入門指南 | Microsoft Azure"
    description="了解如何建立簡單的 Node.js Web 應用程式，並將它部署至 Azure 雲端服務。"
    services="cloud-services"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.date="11/20/2015"
    ms.author="robmcm"/>


# 建立 Node.js 應用程式並部署到 Azure 雲端服務

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

本教學課程示範如何建立簡單的 Node.js 應用程式執行
。 雲端服務是 Azure 中可擴充之雲端應用程式的建置區塊
。 它們可個別和獨立
管理和向外延展應用程式的前端與後端元件。  雲端服務
提供強固的專屬虛擬機器來穩定代管各個角色。

如需雲端服務及其相較於 Azure 網站和虛擬機器的詳細資訊，請參閱 [Azure 網站、 雲端服務和虛擬機器的比較](../choose-web-site-cloud-service-vm.md)。

>[AZURE.TIP] 尋求建置簡單的網站? 如果您只需要簡單的網站前端，請考慮使用<a href="../app-service-web/web-sites-nodejs-develop-deploy-mac.md">輕量型 Web 應用程式。</a>隨著 Web 應用程式擴大以及需求改變，您可以輕易地升級到雲端服務。


按照本教學課程進行，您將建立在 Web 角色內代管的簡單 Web 應用程式。 您
將使用計算模擬器測試應用程式在本機，然後將它部署
使用 PowerShell 命令列工具部署它。

應用程式是簡單的 "hello world" 應用程式：

![顯示 Hello World 網頁的網頁瀏覽器](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

## 先決條件

> [AZURE.NOTE] 本教學課程使用 Azure PowerShell，需要有 Windows。

- 安裝和設定 [Powershell](../install-configure-powershell.md)。
- 下載並安裝 [Azure SDK for.NET 2.7](http://www.microsoft.com/en-us/download/details.aspx?id=48178)。 在安裝過程中，選取：
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## 建立 Azure 雲端服務專案

執行下列工作，建立新的 Azure 雲端服務專案以及基本的 Node.js 樣板：


1. 執行 **Azure PowerShell** 系統管理員身分。 (從 **[開始] 功能表** 或 **[開始] 畫面**, ，搜尋 **PowerShell**。)

2.  [連接 PowerShell](powershell-install-configure.md#how-to-connect-to-your-subscription) 至您的訂閱。
3.  輸入下列 PowerShell Cmdlet 來建立專案：

        New-AzureServiceProject helloworld

    ![New-AzureService helloworld 命令的結果](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

     **New-azureserviceproject** 指令程式會產生發佈 Node.js 應用程式的雲端服務的基本結構。 其中包含發佈到 Azure 所需的組態檔。 該 Cmdlet 也會將您的工作目錄變更為服務的目錄。

    Cmdlet 會建立下列檔案：

    -   **ServiceConfiguration.Cloud.cscfg**,，
        **ServiceConfiguration.Local.cscfg** 和 **ServiceDefinition.csdef**:
        發行應用程式所需的 Azure 特定檔案
        重新接收。 如需詳細資訊，請參閱
        [建立 Azure 託管的服務概觀][]。

    -   **deploymentSettings.json**: 儲存所使用的本機設定
        Azure PowerShell 部署 Cmdlet 所使用的本機設定。

4.  輸入下列命令以新增 Web 角色：

        Add-AzureNodeWebRole

    ![Add-AzureNodeWebRole 命令的輸出。](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

     **Add-azurenodewebrole** 指令程式會建立基本的 Node.js 應用程式。 它也會修改 **.csfg** 和 **.csdef** 檔案以加入新角色的組態項目。

    > [AZURE.NOTE] 如果您未指定角色名稱，則會使用預設名稱。 您可以提供一個名稱做為第一個 Cmdlet 參數：`Add-AzureNodeWebRole MyRole`


Node.js 應用程式定義檔中 **server.js**, ，位於 web 角色的目錄 (**WebRole1** 預設情況下)。 程式碼如下：

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

此程式碼基本上是 「 Hello World 」 範例一樣在 [nodejs.org][] 網站，但它使用雲端環境指派的連接埠號碼。

## 將應用程式部署至 Azure

    [AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]


### 下載 Azure 發佈設定

若要將應用程式部署到 Azure，您必須先下載您 Azure 訂閱的發佈設定。

1.  執行下列 Azure PowerShell Cmdlet：

        Get-AzurePublishSettingsFile

    這將使用瀏覽器瀏覽到發佈設定下載頁面。 可能提示您使用 Microsoft 帳戶登入。 若是如此，請使用與您的 Azure 訂閱相關聯的帳戶。

    將下載的設定檔儲存到您可以輕鬆存取的檔案位置。

2.  執行下列 Cmdlet 來匯入您所下載的發佈設定檔：

        Import-AzurePublishSettingsFile [path to file]


    > [AZURE.NOTE] 匯入後的發行設定，請考慮刪除下載的.publishSettings 檔案，因為它包含了可能讓他人存取您的帳戶資訊。


### 發佈應用程式

若要發佈，請執行下列命令：

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** 指定部署的名稱。 這必須是唯一名稱，否則發佈程序將失敗。  **Get-date** tacks 上應該讓名稱成為唯一的日期/時間字串的命令。

- **位置** 指定將裝載應用程式中的資料中心。 若要查看可用資料中心的清單，請使用 **Get-azurelocation** 指令程式。

- **-啟動** 開啟瀏覽器視窗並部署完成後，瀏覽至裝載的服務。

發佈成功後，您將看見如下的回應：

![Publish-AzureService 命令的輸出](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> 第一次發佈應用程式時，從部署到可以使用需要數分鐘的時間。

部署完成後，瀏覽器視窗將開啟，並瀏覽到雲端服務。


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

您的應用程式現在成功在 Azure 上執行了！

 **Publish-azureserviceproject** 指令程式會執行下列步驟:

1.  建立部署的封裝。 該封裝包含 node.js 應用程式資料夾中所有的檔案。

2.  建立新 **儲存體帳戶** 如果不存在。 Azure 儲存體帳戶將用來在部署期間儲存應用程式封裝。 部署完成後，即可安全刪除儲存體帳戶。

3.  建立新 **雲端服務** 如果不存在。 A **雲端服務** 是部署至 Azure 時，會裝載您的應用程式的容器。 如需詳細資訊，請參閱 [建立 Azure 託管服務概觀][]。

4.  將部署封裝發佈到 Azure。



## 停止並刪除您的應用程式

部署您的應用程式後，您可能會想要將它停用，以便避免額外的成本。 Azure 會對於 Web 角色執行個體的伺服器使用時間時數計費。 部署應用程式之後，即使執行個體未執行而在停止狀態中，也會使用伺服器時間。

1.  在 Windows PowerShell 視窗中，使用下列 Cmdlet 停止上一個小節中建立的服務部署：

        Stop-AzureService

    停止服務可能需要幾分鐘的時間。 服務停止時，您將收到表示停止的訊息。

    ![Stop-AzureService 命令的狀態](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  若要刪除服務，請呼叫下列 Cmdlet：

        Remove-AzureService

    出現提示時，輸入 **Y** 以刪除服務。

    刪除服務可能需要幾分鐘的時間。 刪除服務後，您將收到表示已刪除服務的訊息。

    ![Remove-AzureService 命令的狀態](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

    > [AZURE.NOTE] 刪除服務不會刪除初次發佈服務時, 所建立的儲存體帳戶，而且將持續對使用的儲存體計費。 如需有關刪除儲存體帳戶的詳細資訊，請參閱 [如何從 Azure 訂用帳戶刪除儲存體帳戶](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx)。

## 後續步驟

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Overview of Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[How to Delete a Storage Account from an Azure Subscription]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
 

