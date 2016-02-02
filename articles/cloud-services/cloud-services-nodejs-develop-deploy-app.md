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



。 雲端服務是 Azure 中可擴充之雲端應用程式的建置區塊
。 它們可個別和獨立
管理和向外延展應用程式的前端與後端元件。 雲端服務
提供強固的專屬虛擬機器來穩定代管各個角色。


>[AZURE.TIP] 尋求建置簡單的網站？ 隨著 Web 應用程式擴大以及需求改變，您可以輕易地升級到雲端服務。


按照本教學課程進行，您將建立在 Web 角色內代管的簡單 Web 應用程式。 您

使用 PowerShell 命令列工具部署它。

應用程式是簡單的 "hello world" 應用程式：

![顯示 Hello World 網頁的網頁瀏覽器](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

## 必要條件

> [AZURE.NOTE] 本教學課程使用 Azure PowerShell (需要 Windows)。

- 
- 在安裝過程中，選取：
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## 建立 Azure 雲端服務專案

執行下列工作，建立新的 Azure 雲端服務專案以及基本的 Node.js 樣板：


1. 以系統管理員的身分執行 **Azure PowerShell**。 (從 [**開始**] 功能表或**開始畫面** 中，搜尋 **Azure PowerShell**)。

2.  
3.  輸入下列 PowerShell Cmdlet 來建立專案：

    New-AzureServiceProject helloworld

![New-AzureService helloworld 命令的結果](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

**New-AzureServiceProject** Cmdlet 會產生可將 Node.js 應用程式發佈至雲端服務的基本結構。 其中包含發佈到 Azure 所需的組態檔。 該 Cmdlet 也會將您的工作目錄變更為服務的目錄。

Cmdlet 會建立下列檔案：

-   

發行應用程式所需的 Azure 特定檔案
重新接收。 如需詳細資訊，請參閱


-   
    Azure PowerShell 部署 Cmdlet 所使用的本機設定。

4.  輸入下列命令以新增 Web 角色：

        Add-AzureNodeWebRole

    ![Add-AzureNodeWebRole 命令的輸出。](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

    **Add-AzureNodeWebRole** Cmdlet 可建立基本的 Node.js 應用程式。 也可修改 **.csfg** 和 **.csdef** 檔案以加入新角色的組態項目。
    > [AZURE.NOTE] 如果您未指定角色名稱，系統會使用預設名稱。


Node.js app 是在 **server.js** 檔案中定義，該檔案位於 Web 角色 (預設為 **WebRole1**) 的目錄中。 程式碼如下：

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);



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

    > [AZURE.NOTE] 在匯入發佈設定之後，請考慮刪除下載的 .publishSettings 檔案，因為它包含了可能讓他人存取您帳戶的資訊。


### 發佈應用程式

若要發佈，請執行下列命令：

    $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))   
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

- **-ServiceName** 指定部署的名稱。 這必須是唯一名稱，否則發佈程序將失敗。 **Get-Date** 命令會添加到應該讓名稱唯一的日期/時間字串。

- **-Location** 指定託管應用程式的資料中心。 若要查看可用資料中心的清單，請使用 **Get-AzureLocation** Cmdlet。

- **-Launch** 可在部署完成後，開啟瀏覽器視窗並瀏覽至託管服務。

發佈成功後，您將看見如下的回應：

![Publish-AzureService 命令的輸出](./media/cloud-services-nodejs-develop-deploy-app/node19.png)
> [AZURE.NOTE]
> 第一次發佈應用程式時，從部署到可以使用需要數分鐘的時間。

部署完成後，瀏覽器視窗將開啟，並瀏覽到雲端服務。




您的應用程式現在成功在 Azure 上執行了！

**Publish-AzureServiceProject** Cmdlet 將執行下列步驟：

1.  建立部署的封裝。 該封裝包含 node.js 應用程式資料夾中所有的檔案。

2.  建立新的**儲存體帳戶** (如果不存在)。 Azure 儲存體帳戶將用來在部署期間儲存應用程式封裝。 部署完成後，即可安全刪除儲存體帳戶。

3.  建立新的**雲端服務** (如果不存在)。 **雲端服務**是應用程式部署到 Azure 時代管應用程式的容器。

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
    > [AZURE.NOTE] 刪除服務不會刪除初次發佈服務時建立的儲存體帳戶，而且將持續對使用的儲存體計費。

## 後續步驟




[the windows start menu with the azure sdk node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png 
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png 
[nodejs.org]: http://nodejs.org/ 
[a directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png 
[overview of creating a hosted service for azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx 
[a directory listing of the webrole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png 
[the menu displayed when right-clicking the azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png 
[a browser window displaying http://www.windowsazure.com/ with the free trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png 
[a browser window displaying the liveid sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png 
[internet explorer displaying the save as dialog for the publishsettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png 
[the full status output of the publish-azureservice command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png 
[how to delete a storage account from an azure subscription]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/ 
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png 

