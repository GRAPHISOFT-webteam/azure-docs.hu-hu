<properties
    pageTitle="開始使用以 C# 撰寫的事件中樞 | Microsoft Azure"
    description="遵循此教學課程，以開始使用具備 C# 的 Azure 事件中樞並使用 EventProcessorHost。"
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="11/05/2015"
    ms.author="sethm"/>


# 開始使用事件中心

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## 簡介

「事件中樞」是一種服務，可處理來自連接裝置和應用程式的大量事件資料。 收集資料至「事件中樞」之後，可以使用存放裝置叢集來儲存資料，或使用即時分析提供者進行轉換。 此大規模事件收集和處理功能是新型應用程式架構 (包括物聯網 (IoT)) 的重要元件。

本教學課程示範如何使用 Azure 傳統入口網站來建立事件中樞。 它還會說明如何收集到事件中心使用以 C# 撰寫的主控台應用程式的訊息，以及如何使用 C# [事件處理器主機] 程式庫平行擷取它們。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2013，或 Microsoft Visual Studio Express 2013 for Windows。

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立免費試用帳戶，只需要幾分鐘的時間。 如需詳細資訊，請參閱 [Azure 免費試用] (http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02 和 returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F 目標 ="_blank")。

## 建立事件中心

1. 登入 [Azure 傳統入口網站 []][], ，然後按一下 **新增** 螢幕的底部。

2. 按一下 [**應用程式服務**]、[**服務匯流排**]、[**事件中樞**]、[**快速建立**]。

    ![][1]

3. 為您的事件中樞輸入名稱、選取所需的區域，然後按一下 [**建立新的事件中樞**]。

    ![][2]

4. 按一下您剛才建立的命名空間 (通常是 *** 事件中樞名稱 *-ns**)。

    ![][3]

5. 按一下頁面頂端的 [事件中心]**** 索引標籤，然後按一下您剛建立的事件中心。

    ![][4]

6. 按一下頂端的 [設定]**** 索引標籤，新增名為 **SendRule** 且具有「傳送」**權限的規則，以及另一個名為 **ReceiveRule** 且具有「管理、傳送、接聽」**權限的規則，然後按一下 [儲存]****。

    ![][5]

7. 按一下頁面頂端的 [**儀表板**] 索引標籤，然後按一下 [**連接資訊**]。 記下兩個連接字串，或複製到別處，以供本教學課程後續使用。

    ![][6]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  從 Visual Studio 內部執行 **Receiver** 專案，然後等它啟動所有資料分割的接收器。

    ![][21]

2.  執行 **Sender** 專案，按主控台視窗中的 **Enter** 鍵，並查看出現在接收器視窗中的事件。

    ![][22]

## 後續步驟

您已經建置工作應用程式，可建立「事件中樞」和傳送及接收資料，接下來可進行下列案例：

- 完整 [範例會使用 ][]。
- [相應放大事件處理 ][] 範例。
- A [佇列訊息解決方案 []][] 使用服務匯流排佇列。
- [事件中樞概觀]][]




[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png 
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png 
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png 
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png 
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png 
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png 
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png 
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png 
[azure classic portal]: https://manage.windowsazure.com/ 
[event processor host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost 
[event hubs overview]: event-hubs-overview.md 
[sample application that uses event hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097 
[scale out event processing with event hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3 
[queued messaging solution]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md 

