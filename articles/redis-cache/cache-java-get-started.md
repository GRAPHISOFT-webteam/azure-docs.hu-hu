<properties
   pageTitle="如何搭配使用 Azure Redis 快取與 Java | Microsoft Azure"
    description="開始搭配使用 Azure Redis 快取與 Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/03/2015"
    ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Java

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取 Microsoft 所管理的專用 Redis 快取。 從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何搭配使用 Azure Redis 快取與 Java。


## 必要條件

[Jedis](https://github.com/xetorthio/jedis) -Redis 的 Java 用戶端

本教學課程使用 Jedis，但是您可以使用列在任何 Java 用戶端 [http://redis.io/clients](http://redis.io/clients)。


## 在 Azure 上建立 Redis 快取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=398536), ，按一下 [ **新增**, ，**資料 + 儲存體**, ，然後選取 **Redis 快取**。

  ![][1]

輸入 DNS 主機名稱。 它的形式為 `<name>.redis.cache.windows.net`。 按一下 [ **建立**。

  ![][2]


建立快取之後，請在 Azure 入口網站中按一下它，以檢視快取設定。 按一下下方的連結 **金鑰** ，並將複製的主索引鍵。 您需要有此金鑰才能驗證要求。

  ![][4]


## 啟用非 SSL 端點


按一下下方的連結 **連接埠**, ，然後按一下 **否** 表示 「 僅限透過 SSL 的允許存取 」。 這會啟用快取的非 SSL 連接埠。 Jedis 用戶端目前不支援 SSL。

  ![][3]


## 在快取中加入項目並擷取該項目

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## 後續步驟

- [啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) 讓您可以 [監視](https://msdn.microsoft.com/library/azure/dn763945.aspx) 快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

