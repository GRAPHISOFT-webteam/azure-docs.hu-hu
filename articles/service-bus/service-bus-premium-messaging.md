<properties
    pageTitle="服務匯流排進階和標準傳訊定價層概觀 | Microsoft Azure"
    description="服務匯流排進階和標準傳訊"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="darosa"/>

# 服務匯流排進階和標準傳訊層級 

服務匯流排代理傳訊 (其包含佇列和主題等傳訊實體) 在雲端規模結合了企業傳訊功能與豐富的發佈-訂閱語意。 服務匯流排代理傳訊作為許多縝密雲端解決方案的通訊骨幹。

藉由引進 *高階* 層的服務匯流排訊息，在討論周圍規模、 效能和可用性的關鍵任務應用程式的常見客戶要求。 雖然功能組幾乎完全相同，但這兩層級的 Azure 服務匯流排傳訊設計用來服務不同的使用案例。

下表強調大致的一些差異。

| 高級                               | 標準                       |
|---------------------------------------|--------------------------------|
| 高輸送量                       | 變動輸送量            |
| 可預測的效能               | 變動延遲               |
| 可預測的價格                   | 隨用隨付變動價格 |
| 相應增加和相應減少工作負載的能力 | N/A                            |

**Azure 服務匯流排高階訊息** 提供資源隔離層的 CPU 和記憶體，以便在隔離狀態執行的每個客戶工作負載。 此資源的容器會呼叫 *傳訊單元*。 每個進階命名空間都會被配置至少一個傳訊單位。 您可以為每個服務匯流排進階命名空間購買 1、2 或 4 個傳訊單位。 單一工作負載或實體可以跨越多個傳訊單位，而傳訊單位數目可以隨意變更，雖然計費是依 24 小時或每日費率收費。 結果是您的服務匯流排方案的效能可預測並可重複。

此效能不僅更可預測並可取得，而且還更快速。 在儲存引擎的傳訊組建中導入的 azure 服務匯流排高階 [Azure 事件中樞](http://azure.microsoft.com/services/event-hubs/)。 使用進階訊息，尖峰效能的速度比標準層級的速度快很多。

## 進階傳訊技術差異

以下是進階和標準傳訊層之間的一些差異。

### 資料分割實體

資料分割實體可在進階訊息中提供支援，但是其運作方式與在服務匯流排傳訊的標準和基本層級中不同。 進階傳訊不會使用 SQL 做為資料存放區，而且不可能再有與共用平台相關聯的資源競爭。 因此，不一定需要資料分割。 此外，資料分割計數已從標準傳訊中的 16 個資料分割變更為進階傳訊中的兩個資料分割。 擁有 2 個資料分割可確保可用性，而且是比較適合進階執行階段環境的數字。 如需詳細資訊，請參閱 [分割訊息實體](service-bus-partitioning.md)。

### 快速實體

因為在完全隔離的執行階段環境中執行，所以不再需要進階訊息中的快速實體。 因此，進階命名空間不支援快速實體。 如需 express 功能的詳細資訊，請參閱 [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) 屬性。

## 後續步驟

若要深入了解服務匯流排訊息，請參閱下列主題。

- [Azure 服務匯流排進階傳訊簡介 (部落格文章)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Azure 服務匯流排進階傳訊簡介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [Azure 服務匯流排架構概觀](service-bus-fundamentals-hybrid-solutions.md)
- [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)

