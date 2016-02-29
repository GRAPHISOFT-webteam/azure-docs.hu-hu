<properties
    pageTitle="如何搭配使用 Azure Redis 快取與 Node.js | Microsoft Azure"
    description="開始搭配使用 Azure Redis 快取與 Node.js 和 node_redis。"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/03/2015"
    ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取 Microsoft 所管理的專用安全 Redis 快取。 從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何開始搭配使用 Azure Redis 快取與 Node.js。 使用 Azure Redis 快取與 Node.js 的另一個範例，請參閱 [建置 Node.js 聊天應用程式在 Azure 網站上使用 Socket.IO][]。


## 先決條件

安裝 [node_redis](https://github.com/mranney/node_redis):

    npm install redis

本教學課程使用 [node_redis](https://github.com/mranney/node_redis), ，但是您可以使用列在任何 Node.js 用戶端 [http://redis.io/clients](http://redis.io/clients)。

## 在 Azure 上建立 Redis 快取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=398536), ，按一下 [ **新增**, ，**資料 + 儲存體**, ，然後選取 **Redis 快取**。

  ![][1]

輸入 DNS 主機名稱。 它的形式為 `<name>
  .redis.cache.windows.net`。 按一下 [ **建立**。

  ![][2]


  一旦您建立快取中， [瀏覽至該](cache-configure.md#configure-redis-cache-settings) 若要檢視快取設定。 按一下下方的連結 **金鑰** ，並將複製的主索引鍵。 您需要有此金鑰才能驗證要求。

  ![][4]


  ## 啟用非 SSL 端點


  按一下下方的連結 **連接埠**, ，然後按一下 **否** 表示 「 僅限透過 SSL 的允許存取 」。 這會啟用快取的非 SSL 連接埠。 node_redis 用戶端目前不支援 SSL。

  ![][3]


  ## 在快取中加入項目並擷取該項目

  var redis = require("redis");

  新增快取名稱和存取金鑰。
  var client = redis.createClient(6379,'<name>。.redis.cache.windows.net '，{auth_pass: '<key>' });

    client.set("foo", "bar", function(err, reply) {
        console.log(reply);
    });

    client.get("foo",  function(err, reply) {
        console.log(reply);
    });


Output:

    OK
    bar


## 後續步驟

- [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics) 讓您可以 [監視](cache-how-to-monitor.md) 快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Build a Node.js Chat Application with Socket.IO on an Azure Website]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

