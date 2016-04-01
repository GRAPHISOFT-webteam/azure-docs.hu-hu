<properties 
    pageTitle="如何使用 AMQP 1.0 與 Java 服務匯流排 API 搭配 | Microsoft Azure" 
    description="如何搭配 Azure 服務匯流排和 Advanced Message Queuing Protodol (AMQP) 1.0 使用 Java Message Service (JMS)。" 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    writer="sethm" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/06/2015" 
    ms.author="sethm"/>

# 如何搭配使用 Java 訊息服務 (JMS) API 與服務匯流排和 AMQP 1.0

進階訊息佇列通訊協定 (AMQP) 1.0 是一個有效率且可靠的有線等級訊息通訊協定，可以用來建置強大的跨平台訊息應用程式。

服務匯流排的 AMQP 1.0 支援代表您能夠從一組平台中使用有效率的二進位通訊協定，來運用佇列與發佈/訂閱代理訊息功能。 此外，您還可以建置使用混合語言、架構及作業系統所建置之元件所組成的應用程式。

本文章說明如何以常用的 Java 訊息服務 (JMS) API 標準從 Java 應用程式使用服務匯流排代理訊息功能 (佇列和發佈/訂閱主題)。 這是說明如何使用服務匯流排 .NET API 達到相同效用的隨附作法指南。 您可以同時使用這兩個指南了解使用 AMQP 1.0 的跨平台訊息。

## 開始使用服務匯流排

本指南假設您已經有服務匯流排命名空間，其中包含名稱為「queue1」的佇列。 如果沒有，則您可以建立命名空間和佇列使用 [Azure 傳統入口網站](http://manage.windowsazure.com)。 如需如何建立服務匯流排命名空間和佇列的詳細資訊，請參閱 [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)。

> [AZURE.NOTE] 資料分割的佇列和主題也支援 AMQP。 如需詳細資訊，請參閱 [分割訊息實體](service-bus-partitioning.md) 和 [服務匯流排的 AMQP 1.0 支援分割佇列和主題](service-bus-partitioned-queues-and-topics-amqp-overview.md)。

## 下載 AMQP 1.0 JMS 用戶端程式庫

如需哪裡下載最新版 Apache Qpid JMS AMQP 1.0 用戶端程式庫的詳細資訊，請瀏覽 [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)。

您使用服務匯流排建立和執行 JMS 應用程式時，必須從 Apache Qpid JMS AMQP 1.0 散發封裝將下列 4 個 JAR 檔加入 Java CLASSPATH：

*    geronimo-jms\_1.1\_spec-1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## 編寫 Java 應用程式

### Java 命名及目錄介面 (JNDI)

JMS 使用 Java 命名及目錄介面 (JNDI) 建立邏輯名稱與實際名稱之間的區別。 使用 JNDI 可以解析兩種 JMS 物件：ConnectionFactory 和 Destination。 JNDI 使用提供者模型，您可以在其中插入不同的目錄服務處理名稱解析作業。 Apache Qpid JMS AMQP 1.0 程式庫提供使用下列格式的內容檔案設定的簡單內容檔案型 JNDI 提供者：

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### 設定 ConnectionFactory

用來定義的項目 **ConnectionFactory** 在 Qpid 內容檔案 JNDI 提供者是下列格式 ︰

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

其中 **[jndi_name]** 和 **[ConnectionURL]** 具有下列意義 ︰

- **[jndi_name]**: ConnectionFactory 的邏輯名稱。 這是使用 JNDI IntialContext.lookup() 方法在 Java 應用程式中解析的名稱。
- **[ConnectionURL]**︰ 將包含所需資訊的 JMS 程式庫提供給 AMQP 代理人的 URL。

格式 **ConnectionURL** 如下 ︰

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```
其中 **[namespace]**, ，**[username]** 和 **[密碼]** 具有下列意義 ︰

- **[namespace]**︰ 服務匯流排命名空間。
- **[username]**︰ 服務匯流排簽發者名稱。
- **[密碼]**︰ 服務匯流排發行者金鑰 URL 編碼形式。

> [AZURE.NOTE] 您必須進行 URL 編碼手動密碼。 實用的 URL 編碼公用程式將會位於 [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp)。

#### 設定目的地

在 Qpid 內容檔案 JNDI 提供者中用來定義目的地的項目使用下列格式：

```
queue.[jndi_name] = [physical_name]
```

或

```
topic.[jndi_name] = [physical_name]
```

其中 **[jndi\_name]** 和 **[physical\_name]** 具有下列意義 ︰

- **[jndi_name]**︰ 目的地的邏輯名稱。 這是使用 JNDI IntialContext.lookup() 方法在 Java 應用程式中解析的名稱。
- **[physical_name]**︰ 應用程式傳送或接收訊息的服務匯流排實體的名稱。

> [AZURE.NOTE] 從服務匯流排主題訂閱收到時，在 JNDI 中指定的實體名稱應該是主題的名稱。 以 JMS 應用程式程式碼建立持續性訂用帳戶時，將建立訂用帳戶名稱。  [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md) 提供處理 JMS 服務匯流排主題訂閱的詳細資訊。

### 撰寫 JMS 應用程式

對於服務匯流排使用 JMS 時，不需要特別 API 或選項。 不過，後續將說明一些限制。 和任何 JMS 應用程式一樣，首先需要設定 JNDI 環境，能夠解決 **ConnectionFactory** 和目的地。

#### 設定 JNDI InitialContext

將組態資訊的雜湊表傳遞到 javax.naming.InitialContext 類別的建構函式，將設定 JNDI 環境。 雜湊表中的兩個所需項目是 Initial Context Factory 和 Provider URL 的類別名稱。 下列程式碼示範如何設定 JNDI 環境使用的 Qpid 內容檔案型 JNDI 提供者的內容檔案，名為 **servicebus.properties**。

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### 使用服務匯流排佇列的簡單 JMS 應用程式

下列範例程式將 JMS TextMessages 傳送到 JNDI 邏輯名稱為 QUEUE 的服務匯流排佇列，並收到傳回的訊息。

    // SimpleSenderReceiver.java
    
    import javax.jms.*;
    import javax.naming.Context;
    import javax.naming.InitialContext;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.util.Hashtable;
    import java.util.Random;
    
    public class SimpleSenderReceiver implements MessageListener {
        private static boolean runReceiver = true;
        private Connection connection;
        private Session sendSession;
        private Session receiveSession;
        private MessageProducer sender;
        private MessageConsumer receiver;
        private static Random randomGenerator = new Random();
    
        public SimpleSenderReceiver() throws Exception {
            // Configure JNDI environment
            Hashtable<String, String> env = new Hashtable<String, String>();
            env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
            env.put(Context.PROVIDER_URL, "servicebus.properties");
            Context context = new InitialContext(env);
    
            // Lookup ConnectionFactory and Queue
            ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
            Destination queue = (Destination) context.lookup("QUEUE");
    
            // Create Connection
            connection = cf.createConnection();
    
            // Create sender-side Session and MessageProducer
            sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
            sender = sendSession.createProducer(queue);
    
            if (runReceiver) {
                // Create receiver-side Session, MessageConsumer,and MessageListener
                receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
                receiver = receiveSession.createConsumer(queue);
                receiver.setMessageListener(this);
                connection.start();
            }
        }
    
        public static void main(String[] args) {
            try {
    
                if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                    runReceiver = false;
                }
    
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
                System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
                BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
                while (true) {
                    String s = commandLine.readLine();
                    if (s.equalsIgnoreCase("exit")) {
                        simpleSenderReceiver.close();
                        System.exit(0);
                    } else {
                        simpleSenderReceiver.sendMessage();
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    
        private void sendMessage() throws JMSException {
            TextMessage message = sendSession.createTextMessage();
            message.setText("Test AMQP message from JMS");
            long randomMessageID = randomGenerator.nextLong() >>>1;
            message.setJMSMessageID("ID:" + randomMessageID);
            sender.send(message);
            System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
        }
    
        public void close() throws JMSException {
            connection.close();
        }
    
        public void onMessage(Message message) {
            try {
                System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
                message.acknowledge();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }   

### 執行應用程式

執行應用程式將產生表單的輸出：

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## JMS 與 .NET 之間的跨平台訊息

本指南說明使用 JMS 傳送和接收服務匯流排的訊息。 不過，AMQP 1.0 的其中一個主要優點是能夠從不同語言撰寫的元件建立應用程式，並確實完整交換訊息。

使用上述的範例 JMS 應用程式和取自隨附指南的類似.NET 應用程式 [如何透過.NET 服務匯流排.NET API 使用 AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md), ，即可交換.NET 與 Java 之間的訊息。 

如需有關跨平台訊息的詳細資料使用服務匯流排和 AMQP 1.0，請參閱 [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)。

### JMS 到 .NET

示範 JMS 到 .NET 的訊息：

* 不使用任何命令列引數啟動 .NET 範例應用程式。
* 使用「sendonly」命令列引數啟動 Java 範例應用程式。 在此模式中，應用程式不會收到來自佇列的訊息，而只會傳送。
* 按下 **Enter** 幾次在 Java 應用程式主控台中，這會導致要傳送的訊息。
* 這些訊息將由 .NET 應用程式所接收。

#### JMS 應用程式的輸出

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### .NET 應用程式的輸出

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### .NET 到 JMS

示範 .NET 到 JMS 的訊息：

* 使用「sendonly」命令列引數啟動 .NET 範例應用程式。 在此模式中，應用程式不會收到來自佇列的訊息，而只會傳送。
* 不使用任何命令列引數啟動 Java 範例應用程式。
* 按下 **Enter** 幾次在.NET 應用程式主控台中，這會導致要傳送的訊息。
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

對於服務匯流排使用 JMS 而不使用 AMQP 1.0 會有下列限制：

* 只有一個 **對於** 或 **Messageproducer** 允許每個 **工作階段**。 如果您需要建立多個 **MessageProducers** 或 **MessageConsumers** 應用程式中，建立專用 **工作階段** 每個訊息。
* 目前不支援可變更的主題訂用帳戶。
* **MessageSelectors** 目前不支援。
* 暫時目的地，亦即， **TemporaryQueue**, ，**TemporaryTopic** 目前不支援，以及與 **QueueRequestor** 和 **TopicRequestor** 使用它們的 Api。
* 不支援交易式工作階段和分散式交易。

## 摘要

本作法指南說明如何以常用的 JMS API 和 AMQP 1.0 從 Java 使用服務匯流排代理訊息功能 (佇列和發佈/訂閱主題)。

您也可以使用包括 .NET、C、Python 和 PHP 在內的其他語言所撰寫的 Service Bus AMQP 1.0。 使用這些不同的語言撰寫的元件可使用服務匯流排中的 AMQP 1.0 支援確實完整交換訊息。 如需詳細資訊，請參閱 [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)。

## 後續步驟

* [Azure 服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [如何透過服務匯流排 .NET API 使用 AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)
* [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)
* [Java 開發人員中心](/develop/java/)。



 


