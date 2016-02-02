<properties
    pageTitle="什麼是 Azure 事件中樞？| Microsoft Azure"
    description="Azure 事件中樞的概觀"
    services="event-hubs"
    documentationCenter=".net"
    authors="nberdy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/05/2015"
    ms.author="sethm"/>


# Azure 事件中樞是什麼？

事件中樞是可高度調整的資料輸入服務，每秒可擷取數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。 事件中樞能做為事件管線的「大門」，一旦收集的資料進入事件中樞，它可以使用任何即時分析提供者或批次/儲存配接器轉換及儲存資料。 事件中樞能分隔事件串流的生產與這些事件的使用，讓事件消費者依照自己的排程存取事件。

## 事件中樞功能

事件中樞是事件處理服務，它能提供大規模進入雲端的事件和遙測入口，並具備低延遲和高可靠性等特性。 這項服務特別適合：

* 應用程式檢測
* 使用者經驗或工作流程處理
* 物聯網 (IoT) 案例

一些其他重要的事件中樞功能包括行動應用程式中的行為追蹤、來自 Web 伺服陣列的流量資訊、遊戲機遊戲中的遊戲內部事件擷取，或是從產業用機器或連接之車輛收集而來的遙測資料。

不同於 [服務匯流排佇列和主題](../service-bus/service-bus-messaging-overview.md), ，事件中樞著重於提供大規模的訊息資料流處理。 事件中樞功能與服務匯流排主題的相異之處在於，這些功能乃極度偏向高輸送量和事件處理案例。 因此，事件中樞不會實作一些可供的傳訊功能 [主題](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics)。 如果您需要這些功能，主題仍是最佳選擇。

## 後續步驟

如需事件中樞的詳細資訊，請參閱下列主題。

- [事件中心概觀](event-hubs-overview.md)
- [事件中心程式設計指南](event-hubs-programming-guide.md)
- [事件中樞可用性和支援常見問題集](event-hubs-availability-and-support-faq.md)
- 開始使用 [事件中樞教學課程 []][]
- 完成 [範例會使用 ][]


[event hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md 
[sample application that uses event hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097 

