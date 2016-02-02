<properties 
    pageTitle="如何使用 AMQP 1.0 與 .NET 服務匯流排 API 搭配 | Microsoft Azure" 
    description="了解如何搭配使用 Advanced Message Queuing Protodol (AMQP) 1.0 和 Azure .NET 服務匯流排 API。" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="sethm"/>


# 如何透過服務匯流排 .NET API 使用 AMQP 1.0

進階訊息佇列通訊協定 (AMQP) 1.0 是一個有效率且可靠的有線等級傳訊通訊協定，可以用來建置強大的跨平台傳訊應用程式。

在服務匯流排中支援 AMQP 1.0 代表您現在能夠從一組平台中使用有效率的二進位通訊協定，來運用佇列與發佈/訂閱代理傳訊功能。 此外，您還可以建置使用混合語言、架構及作業系統所建置之元件所組成的應用程式。

本文說明如何使用服務匯流排 .NET API，從 .NET 應用程式使用服務匯流排代理傳訊功能 (佇列和發佈/訂閱主題)。 我們另備有說明如何使用標準 Java 訊息服務 (JMS) API 來進行相同操作的附屬文章。 您可以同時使用這兩個指南了解使用 AMQP 1.0 的跨平台訊息。

## 開始使用服務匯流排

本文假設您已經有服務匯流排命名空間，其中包含名稱為「queue1」的佇列。 如果沒有，則您可以建立命名空間和佇列使用 [Azure 傳統入口網站](http://manage.windowsazure.com)。 如需如何建立服務匯流排命名空間和佇列的詳細資訊，請參閱 [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)。

## 下載服務匯流排 SDK

服務匯流排 SDK 2.1 或更新版本提供 AMQP 1.0 支援。 您可以從在 NuGet 下載最新的 SDK [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/)。

## 程式碼 .NET 應用程式

依預設，服務匯流排 .NET 用戶端程式庫能使用專屬的 SOAP 型通訊協定與服務匯流排服務通訊。 若要使用 AMQP 1.0 (而非預設的通訊協定)，您需要明確地設定服務匯流排連接字串，如下節內容所述。 除了這項變更之外，在使用 AMQP 1.0 時，應用程式程式碼基本上會維持不變。

目前的版本中有幾項在使用 AMQP 時不支援的 API 功能。 這些不支援的功能列示於後續一節中 [不支援的功能和限制](#unsupported-features-and-restrictions)。 在使用 AMQP 時，某些進階組態設定亦有不同的意義。 在本文中，會使用任何這些設定，但更多詳細資料可用於 [服務匯流排 AMQP 概觀](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)。

### 透過 App.config 來設定

讓應用程式使用 App.config 組態檔案來儲存設定是建議的作法。 對於服務匯流排應用程式，您可以使用 App.config 來儲存服務匯流排 **ConnectionString**。 此範例應用程式也會使用 App.config 來儲存它使用的服務匯流排訊息實體的名稱。

以下是範例 App.config 檔案：

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### 設定服務匯流排連接字串

**Microsoft.ServiceBus.ConnectionString** 設定的值是用來設定服務匯流排連線的服務匯流排連接字串。 其格式如下所示：

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

其中 `[namespace]` 和 `[SAS 金鑰]` 取自 [Azure 傳統入口網站 []][]。 如需詳細資訊，請參閱 [如何使用服務匯流排佇列]][]。

使用 AMQP 時，連接字串會加 `;TransportType = Amqp`, ，它能告知用戶端程式庫來建立連線至服務匯流排使用 AMQP 1.0。

### 設定實體名稱

此範例應用程式使用 `EntityName` 中設定 **appSettings** App.config 檔案來設定與應用程式交換訊息的佇列名稱的區段。

### 使用服務匯流排佇列的簡易 .NET 應用程式

以下範例會傳送訊息給服務匯流排佇列，以及接收來自服務匯流排佇列的訊息。

```
// SimpleSenderReceiver.cs

using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;

        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;

                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();

                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");

                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }

        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);

            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }

        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }

        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }

    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }

        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }

        public void RequestStop()
        {
            _shouldStop = true;
        }

        private volatile bool _shouldStop;
    }
}
```

### 執行應用程式

執行應用程式將產生表單的輸出：

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf

Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06

Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## JMS 與 .NET 之間的跨平台訊息

本文示範如何使用 .NET 將訊息傳送到服務匯流排，以及如何使用 .NET 接收這些訊息。 不過，AMQP 1.0 的其中一個主要優點是能夠從不同語言撰寫的元件建立應用程式，並確實完整交換訊息。

使用上述的範例.NET 應用程式和取自隨附指南的類似 Java 應用程式 [如何使用 Java 訊息服務 (JMS) API 與服務匯流排和 AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), ，就可以交換.NET 與 Java 之間的訊息。

如需有關跨平台訊息的詳細資料使用服務匯流排和 AMQP 1.0，請參閱 [服務匯流排 AMQP 1.0 概觀](service-bus-amqp-overview.md)。

### JMS 到 .NET

示範 JMS 到 .NET 的訊息：

* 不使用任何命令列引數啟動 .NET 範例應用程式。
* 使用「sendonly」命令列引數啟動 Java 範例應用程式。 在此模式中，應用程式不會收到來自佇列的訊息，而只會傳送。
* 在 Java 應用程式主控台多次按下 **Enter** 鍵，這將傳送訊息。
* 這些訊息將由 .NET 應用程式所接收。

### JMS 應用程式的輸出

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### .NET 應用程式的輸出

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## .NET 到 JMS

示範從 .NET 到 JMS 的傳訊操作：

* 啟動 .NET 範例應用程式並搭配 "sendonly" 命令列引數。 在此模式中，應用程式將不會接收來自佇列的訊息，它只會傳送訊息。
* 在不搭配任何命令列引數的情況下啟動 Java 範例應用程式。
* 在 .NET 應用程式主控台內按幾次 [確定]****，如此能導致訊息傳送。
* 這些訊息將由 Java 應用程式所接收。

#### .NET 應用程式的輸出

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### JMS 應用程式的輸出

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## 不支援的功能和限制

以下 .NET 服務匯流排 API 功能是目前使用 AMQP 時無法支援的功能：

* 交易
* 透過傳輸目的地傳送
* 依照訊息序號接收
* 訊息和工作階段瀏覽
* 工作階段狀態
* Batch 型 API
* 向外延展接收
* 訂用帳戶規則的執行階段操作
* 工作階段鎖定更新
* 某些輕微的行為差異

如需詳細資訊，請參閱 [服務匯流排 AMQP 概觀](service-bus-amqp-dotnet.md)。 該主題含有不支援之 API 的詳細清單。

## 摘要

本文示範如何使用 AMQP 1.0 和服務匯流排 .NET API，從 .NET 存取服務匯流排代理傳訊功能 (佇列和發佈/訂閱主題)。

您也可以從 Java、C、Python 及 PHP 等其他語言使用服務匯流排 AMQP 1.0。 使用這些語言組建的元件可使用服務匯流排中的 AMQP 1.0 可靠而真實地交換訊息。 如需詳細資訊，請參閱 [服務匯流排 AMQP 概觀](service-bus-amqp-dotnet.md)。

## 後續步驟

既然您已閱讀服務匯流排和 AMQP 與 .NET 的概觀，請參閱下列連結取得詳細資訊。

* [Azure 服務匯流排的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [如何使用 Java 訊息服務 (JMS) API 與服務匯流排和 AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)


[azure classic portal]: http://manage.windowsazure.com 

