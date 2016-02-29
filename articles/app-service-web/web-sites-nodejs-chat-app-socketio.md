<properties
    pageTitle="在 Azure App Service 中使用 Socket.IO 建立 Node.js 聊天應用程式"
    description="示範在裝載於 Azure 的 node.js Web 應用程式中使用 socket.io 的教學課程。"
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




# 在 Azure App Service 中使用 Socket.IO 建立 Node.js 聊天應用程式

Socket.IO 使用 WebSocket 提供 node.js 伺服器與用戶端之間的即時通訊。 此外，它還能支援遞補其他適用於舊版瀏覽器的傳輸 (例如長輪詢)。 本教學課程將逐步引導您裝載 Socket.IO 型交談應用程式為 Azure web 應用程式，並示範如何以 [調整](#scale-out) 應用程式使用 [Azure Redis 快取](/documentation/services/cache)。 如需 Socket.IO 的詳細資訊，請參閱 [http://socket.io/][socketio]。

> [AZURE.NOTE] 這項工作中的程序套用至 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714); 對於雲端服務，請參閱 <a href="http://www.windowsazure.com/develop/nodejs/tutorials/app-using-socketio/">Azure 雲端服務上建立 Node.js 聊天應用程式使用 Socket.IO</a>。


## 下載聊天範例

此專案中，我們將使用的聊天範例 [Socket.IO
GitHub 儲存機制]。 執行下列步驟以下載範例
並將它加入至您先前建立的專案。

1.  下載 [ZIP 或 GZ 封存版本][release] Socket.IO 專案 (版本 1.3.5 已用於此文件)


3.  解壓縮封存，並將 **examples\\chat**
    目錄複製到新位置。 例如，
    **\\node\\chat**。

## 修改 App.js 和安裝模組

1.  重新命名 **index.js** 檔案重新命名為 **app.js**。 如此能讓 Azure 偵測到這是 Node.js 應用程式。

1.  開啟 **app.js** 在文字編輯器中的檔案。 變更包含 `var io = require('../..')(server);` 的字行，如下所示：

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;


3. 開啟 **package.json** 檔案，並加入下 socket.io 的參考 `dependencies`, ，如下所示:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

4. 從命令列中，切換至 **\\node\\chat** 目錄，然後使用 npm 來安裝此應用程式所需的模組:

        npm install

    這會將模組安裝至名為的子資料夾 **node_modules**。

## 建立 Azure Web 應用程式

遵循以下步驟來建立 Azure Web 應用程式、啟用 Git 發行，然後針對 Web 應用程式啟用 WebSocket 支援。

> [AZURE.NOTE] 若要完成本教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure 免費試用</a>。

1. 安裝 Azure 命令列介面 (Azure CLI)，並連線到您的 Azure 訂用帳戶。 請參閱 [安裝和設定 Azure CLI](../xplat-cli)。

2. 如果這是您第一次在 Azure 中設定儲存機制，就需要建立登入認證。 從 Azure CLI 中，輸入下列命令：

        azure site deployment user set [username] [password]


3. 若要變更 **\\node\chat** 目錄，然後使用下列命令來建立新的 Azure web 應用程式和本機 Git 儲存機制。 這個命令也會建立名為 'azure' 的 Git 遠端。

        azure site create mysitename --git

    您必須將 'mysitename' 改成您的 Web 應用程式的唯一名稱。

2. 使用下列命令，將現有的檔案認可到本機儲存機制：

        git add .
        git commit -m "Initial commit"

3. 使用下列命令，將檔案推送至 Azure Web Apps 儲存機制：

        git push azure master

    系統出現提示時，請輸入步驟 2 中的認證。 在伺服器上匯入模組時會顯示狀態訊息。 此程序完成之後，應用程式將裝載於 Azure Web 應用程式上。

    > [AZURE.NOTE] 在模組安裝期間，您可能會發現錯誤，' 匯入的專案...找不到 '。 請放心忽略這項錯誤訊息。

4. Socket.IO 使用 WebSockets (在 Azure 上依預設不會啟用)。 若要啟用 Web Sockets，請使用下列命令：

        azure site set -w

    如果出現提示，請輸入 Web 應用程式的名稱。

    >[AZURE.NOTE]
    >'azure site set -w' 命令僅適用於 Azure 命令列介面 0.7.4 版或更新版本。 您也可以啟用 WebSocket 支援使用 [Azure 入口網站](https://portal.azure.com)。
    >
    >若要使用 Azure 入口網站啟用 Websocket，按一下 [web 應用程式在 Web 應用程式刀鋒視窗中的，按一下 **所有設定** > **應用程式設定**。 在 **Web 通訊端**, ，按一下 [ **上**。 然後按一下 [ **儲存**。

5. 若要在 Azure 上檢視 Web 應用程式，請使用下列命令來啟動網頁瀏覽器，並瀏覽至裝載的 Web 應用程式：

        azure site browse

您的應用程式現在已在 Azure 上執行，而且可以使用 Socket.IO 在不同用戶端之間轉送聊天訊息。

##橫向擴充

Socket.IO 應用程式可以向外延展使用 __配接器__ 至多個應用程式執行個體之間散佈訊息和事件。 有數種配接器使用時， [socket.io-redis](https://github.com/automattic/socket.io-redis) 配接器可以輕鬆地搭配 Azure Redis 快取功能。

> [AZURE.NOTE] 橫向擴充 Socket.IO 解決方案的額外需求是支援黏性工作階段。 Azure Web Apps 的黏性工作階段預設是透過 Azure 要求路由來啟用。 如需詳細資訊，請參閱 [Azure 網站的執行個體同質性](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###建立 Redis 快取

執行中的步驟 [Azure Redis 快取中建立快取](/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-a-cache) 來建立新的快取。

> [AZURE.NOTE] 儲存 __主機名稱__ 和 __主索引鍵__ 快取，因為將會需要這些接下來的步驟。

###新增 redis 和 socket.io-redis 模組

1. 從命令列中，切換至 __\\node\\chat__ 目錄，然後使用下列命令。

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] 此命令中指定的版本是測試本文章的版本。

2. 修改 __app.js__ 檔案，加入下列程式碼行後面 `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    取代 __redishostname__ 和 __rediskey__ 使用主機名稱和 Redis 快取索引鍵。

    如此將會為先前建立的 Redis 快取建立一個發佈和訂閱用戶端。 之後該用戶端會搭配配接器使用，以將 Socket.IO 設定為使用 Redis 快取在應用程式的執行個體之間傳遞訊息和事件。

    > [AZURE.NOTE] 雖然 __socket.io-redis__ 配接器可以直接與 Redis 通訊，目前的版本不支援 Azure Redis 快取所需的驗證。 因此會使用建立初始連接 __redis__ 模組，然後將用戶端傳遞至 __socket.io-redis__ 配接器。
    >
    > 雖然 Azure Redis 快取使用連接埠 6380 來支援安全的連線，但是此範例中使用的模組截至 2014/7/14 為止並不支援安全的連線。 以上程式碼使用的是預設的 6379 連接埠 (不安全)。

3. 儲存已修改 __app.js__

###認可變更並重新部署

從命令列中 __\\node\\chat__ 目錄中，使用下列命令來認可變更並重新部署應用程式。

    git add .
    git commit -m "implementing scale out"
    git push azure master

一旦將變更推送至伺服器，您就可以使用下列命令在多個執行個體間擴充網站。

    azure site scale instances --instances #

其中 __#__ 是要建立執行個體的數目。

您可以從多個瀏覽器或多部電腦連接至 Web 應用程式，以確認該訊息已正確傳送至所有用戶端。

## 疑難排解

###連線限制

Azure Web Apps 可用於多個 SKU，SKU 可以決定您網站適用的資源。 這包含允許 WebSocket 連線的數目。 如需詳細資訊，請參閱 [Web Apps 定價頁面][pricing]。

###使用 WebSockets 而未傳送的訊息

如果用戶端瀏覽器持續遞補長輪詢，而非使用 WebSockets，可能是下列原因所致。

* **試圖將傳輸僅限制到 WebSockets**

    為了讓 Socket.IO 使用 WebSockets 作為訊息傳輸，伺服器和用戶端都必須支援 WebSockets。 如果其中一方不支援 WebSockets，則 Socket.IO 將會交涉其他傳輸，例如長輪詢。 Socket.IO 使用的預設傳輸清單為 ` websocket, htmlfile, xhr-polling, jsonp-polling`。 您也可以將它僅使用 WebSockets 加入下列程式碼，以強制 **app.js** 檔案中的，列包含 `, nicknames = {};`。

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] 請注意，不支援 WebSockets 的舊版瀏覽器將無法連線到站台，上述程式碼作用中時，因為它通訊僅限制到 WebSockets。

* **使用 SSL**

    WebSockets 依賴某些較少使用 HTTP 標頭，例如 **升級** 標頭。 某些中繼網路裝置，例如 Web Proxy，可能會移除這些標頭。 為了避免發生這種問題，您可以建立經由 SSL 的 WebSocket 連線。

    若要這麼做，最簡單的方式就是將 Socket.IO 設定為 `match origin protocol`。 此一代碼會將 Socket.IO 指引至安全的 WebSockets 通訊，如同網頁原始的 HTTP/HTTPS 要求一般。 如果瀏覽器使用 HTTPS URL 造訪您的網站，則後續通過 Socket.IO 的 WebSocket 通訊將會在經由 SSL 時受到保護。

    若要修改本例以啟用這項設定，加入下列程式碼 **app.js** 列包含檔案 `, nicknames = {};`。

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **確認 web.config 設定**

    裝載 Node.js 應用程式使用的 azure web 應用程式 **web.config** 檔案將連入要求路由至 Node.js 應用程式。 函式，正確地使用 Node.js 應用程式，要讓 websocket 能 **web.config** 必須包含下列項目。

        <webSocket enabled="false"/>

    這會停用 IIS WebSockets 模組，其中包含它自己的 WebSockets 實作，而且會與 Node.js 特定 WebSocket 模組 (例如 Socket.IO) 衝突。 如果此行不存在，或設定為 `true`，這可能是您應用程式的 WebSocket 傳輸無法運作的原因。

    通常 Node.js 應用程式並不包含 **web.config** 檔案，因此 Azure 網站會自動產生的 Node.js 應用程式時部署。 由於此檔案是在伺服器上自動產生的，因此您必須在網站使用 FTP 或 FTPS URL 才能檢視此檔案。 您可以找到 FTP 和 FTPS Url 您在傳統入口網站的網站選取 web 應用程式，然後 **儀表板** 連結。 Url 會顯示在 **快速瀏覽** 一節。

    > [AZURE.NOTE]  **Web.config** 檔案才會產生 Azure 網站的應用程式不提供。 如果您提供 **web.config** 檔案在應用程式專案的根目錄中，它將使用由 Azure Web 應用程式。

    如果項目不存在，或設定值為 `true`, ，則您應該建立 **web.config** Node.js 應用程式的根目錄中並指定其值為 `false`。  如需參考，以下是預設值 **web.config** 使用的應用程式的 **app.js** 做為進入點。

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    如果您的應用程式使用的進入點以外的其他 **app.js**, ，您必須將所有出現的 **app.js** 與正確的進入點。 例如，取代 **app.js** 與 **server.js**。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

##後續步驟

在本教學課程中，您學到如何建立裝載於 Azure Web 應用程式中的聊天應用程式。 您也可以將此應用程式交由 Azure 雲端服務託管。 如需有關如何完成這項作業的步驟，請參閱 [Azure 雲端服務上建立 Node.js 聊天應用程式使用 Socket.IO][cloudservice]。

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需舊入口網站變更為新入口網站的指南，請參閱: [瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Socket.IO GitHub repository]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /pricing/details/web-sites/
 

