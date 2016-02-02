<properties 
    pageTitle="使用 Socket.io 的 Node.js 應用程式 | Microsoft Azure" 
    description="學習如何在裝載於 Azure 的 node.js 應用程式中使用 socket.io。" 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="TomArcher" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="09/01/2015" 
    ms.author="tarcher"/>






# 在 Azure 雲端服務上使用 Socket.IO 建立 Node.js 交談應用程式

Socket.IO 提供 node.js
伺服器和用戶端之間的即時通訊。 本教學課程將逐步引導您在 Azure 上裝載
socket.IO 型交談應用程式。 如需 Blob 的詳細資訊，


完成之應用程式的螢幕擷取畫面如下：

![A browser window displaying the service hosted on Azure][completed-app]

## 必要條件

請確定已安裝下列產品及版本，以順利完成本文中的範例：

* 
* 
* 

## 建立雲端服務專案

下列步驟會建立雲端服務專案來裝載 Socket.IO 應用程式。

1. 從 [開始] 功能表**** 或 [開始畫面]**** 中，搜尋 **Azure PowerShell**。 最後，以滑鼠右鍵按一下 [Azure PowerShell]****，然後選取 [以系統管理員身分執行]****。

    ![Azure PowerShell icon][powershell-menu]

2. 

        PS C:\> md node

3. 

        PS C:\> cd node

4. 然後輸入下列命令來建立名為 **chatapp** 的新方案和名為 **WorkerRole1** 的背景工作角色：

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    您將看見下列回應：

    ![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## 下載交談範例


執行下列步驟以下載範例
並將它加入至您先前建立的專案。

1.  使用 [Clone]**** 按鈕來建立儲存機制的本機複本。 您也可以使用 [ZIP]**** 按鈕來下載專案。

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  
目錄。 將此目錄的內容複製到稍早建立的


![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]



4.  這樣會移除先前由 **Add-AzureNodeWorkerRole** Cmdlet 建立的預設 **server.js** 檔案，並取代為交談範例中的應用程式檔案。

### 修改 Server.js 和安裝模組

在 Azure 模擬器中測試應用程式之前，我們必須
稍做一些修改。 請對 server.js 檔案執行下列步驟
：

1.  在 Visual Studio 或其他文字編輯器中開啟 **server.js** 檔案。

2.  在 server.js 開頭找出 **Module dependencies** 區段，將含有 **sio = require('..//..//lib//socket.io')** 的那一行變更為 **sio = require('socket.io')**，如下所示：

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  若要確保應用程式在正確的連接埠上接聽，請
在 [記事本] 或您最愛的編輯器開啟 server.js，然後變更


    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });



安裝必要的模組，然後在 Azure 模擬器中測試應用程式
：

1.  

        PS C:\node\chatapp\WorkerRole1> npm install

    這樣會安裝 package.json 檔案中列出的模組。 在
    命令完成後，您應該會看到類似這樣的
    輸出：

    ![The output of the npm install command][the-output-of-the-npm-install-command]

4.  因為此範例原本是 Socket.IO GitHub 儲存機制的一部分，
    並以相對路徑直接參考 Socket.IO 程式庫，
    而且 Socket.IO 並未在 package.json 檔案中
    參考，所以我們必須發出下列命令來安裝它：

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save


### 測試和部署

1.  發出下列命令來啟動模擬器：

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  

3.  當瀏覽器視窗開啟時，請輸入暱稱，然後按 Enter 鍵。
    這樣可讓您以特定的暱稱來張貼訊息。 若要測試
    多使用者功能，請使用相同的 URL 開啟其他瀏覽器視窗
    ，並輸入不同的暱稱。

    ![兩個瀏覽器視窗顯示 User1 和 User2 的交談訊息](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  測試應用程式之後，發出下列命令來停止模擬器
    ：

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  若要將應用程式部署至 Azure，請使用
    例如：

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] 務必使用唯一的名稱，否則發行程序會失敗。 部署完成之後，瀏覽器會開啟並瀏覽至已部署的服務。
    > 
    > 如果出現錯誤指出匯入的發行設定檔中沒有您所提供的訂閱名稱，則在部署至 Azure 之前，您必須下載並匯入訂閱的發行設定檔。

    ![A browser window displaying the service hosted on Azure][completed-app]
    > [AZURE.NOTE] 如果出現錯誤指出匯入的發行設定檔中沒有您所提供的訂閱名稱，則在部署至 Azure 之前，您必須下載並匯入訂閱的發行設定檔。

您的應用程式現在已在 Azure 上執行，並且可以使用
Socket.IO 在不同的用戶端之間轉送訊息。
> [AZURE.NOTE] 為了簡化起見，此範例只能讓連線至相同執行個體的使用者聊天。 這表示如果雲端服務建立兩個背景工作角色執行個體，則使用者只能夠與連線至相同背景工作角色執行個體的其他使用者交談。 若要擴充應用程式來處理多個角色執行個體，您可以使用服務匯流排之類的技術，以便跨執行個體來共用 Socket.IO 儲存狀態。

## 後續步驟

在本教學課程中，您學到如何建立裝載於 Azure 雲端服務的基本交談應用程式。




[chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/ 
[azure sla]: http://www.windowsazure.com/support/sla/ 
[azure sdk for node.js github repository]: https://github.com/WindowsAzure/azure-sdk-for-node 
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png 
[azure sdk for node.js]: https://www.windowsazure.com/develop/nodejs/ 
[node.js web application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/ 
[socket.io github repository]: https://github.com/LearnBoost/socket.io/tree/0.9.14 
[azure considerations]: #windowsazureconsiderations 
[hosting the chat example in a worker role]: #hostingthechatexampleinawebrole 
[summary and next steps]: #summary 
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png 
[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat 
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png 
[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png 
[the-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png 
[the output of the publish-azureservice command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png 

