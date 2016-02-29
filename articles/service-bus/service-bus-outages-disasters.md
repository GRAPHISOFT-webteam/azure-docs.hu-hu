<properties 
   pageTitle="將服務匯流排應用程式與服務匯流排中斷和災害隔絕 | Microsoft Azure"
   description="說明您可用來保護應用程式，避免潛在服務匯流排中斷的技巧。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# 將應用程式與服務匯流排中斷和災難隔絕的最佳作法

關鍵任務應用程式必須持續作業，即使發生非預期的中斷或災害亦然。 本主題說明您可用來保護服務匯流排應用程式，避免潛在的服務中斷或災害的技巧。

中斷的定義是暫時無法使用 Azure 服務匯流排。 中斷可能會影響服務匯流排的否些元件，例如訊息存放區或甚至整個資料中心。 修正問題之後，服務匯流排可再次使用。 中斷通常不會導致訊息或其他資料遺失。 元件失敗的範例是特定的訊息存放區無法使用。 資料中心全面中斷的範例是資料中心電源中斷、或錯誤的資料中心網路交換器。 中斷可能持續數分鐘到數天。

災害的定義是服務匯流排縮放單位或資料中心永久遺失。 資料中心不一定能再次使用。 災害通常會造成部分或所有訊息或其他資料遺失。 災害的範例包括火災、水災或地震。

## 目前架構

服務匯流排使用多個訊息存放區來儲存傳送至佇列或主題的訊息。 非分割的佇列或主題會指派給一個訊息存放區。 如果此訊息存放區無法使用，該佇列或主題上的所有作業都會失敗。

所有服務匯流排訊息實體 (佇列、主題、轉送) 都位於附屬於資料中心的服務命名空間中。 服務匯流排不會啟用自動的資料異地複寫，也不會允許跨多個資料中心的服務命名空間。

## 保護 ACS 免於中斷

如果您使用 ACS 認證，ACS 會無法使用，用戶端也無法再取得權杖。 在 ACS 停機時擁有權杖的用戶端可以繼續使用服務匯流排直到權杖到期。 預設權杖存留期為 3 小時。

若要保護 ACS 免於中斷，請使用共用存取簽章 (SAS) 權杖。 在此情況下，用戶端會藉由使用秘密金鑰簽署自行決策權杖的方式，直接使用服務匯流排進行驗證。 不再需要呼叫 ACS。 如需有關 SAS 權杖的詳細資訊，請參閱 [服務匯流排驗證][]。

## 保護佇列和主題免於發生訊息存放區失敗

非分割的佇列或主題會指派給一個訊息存放區。 如果此訊息存放區無法使用，該佇列或主題上的所有作業都會失敗。 另一方面，分割佇列包含多個片段。 每個片段都儲存在不同的訊息存放區。 當訊息傳送至分割的佇列或主題時，服務匯流排會指派訊息到其中一個片段。 如果對應的訊息存放區無法使用，服務匯流排會盡可能將訊息寫入至不同的片段。 如需分割實體的詳細資訊，請參閱 [分割訊息實體][]。

## 保護資料中心免於中斷或災害

若要允許兩個資料中心之間的容錯移轉，您可以在每個資料中心建立服務匯流排服務命名空間。 例如，服務匯流排服務命名空間 **} {3>contosoprimary.servicebus.** 可能位於美國 (北/中部) 的區域，和 **contosoSecondary.servicebus.windows.net** 可能位於美國 (南部/中部) 的區域。 如果服務匯流排訊息實體必須在資料中心發生中斷時保持可存取狀態，您可以在這兩個命名空間建立該實體。

如需詳細資訊，請參閱 「 Azure 資料中心內的服務匯流排失敗 」 一節 [非同步傳訊模式和高可用性][]。

## 保護轉送端點免於發生資料中心中斷或災害

轉送端點的異地複寫會讓公開轉送端點的服務可在服務匯流排中斷時使用。 若要達到異地複寫，服務必須在不同的命名空間中建立兩個轉送端點。 命名空間必須位於不同的資料中心而兩個端點必須具有不同的名稱。 例如，可以找到主要端點下 **contosoPrimary.servicebus.windows.net/myPrimaryService**, ，而第二個對應項目可以達到下 **contosoSecondary.servicebus.windows.net/mySecondaryService**。

然後服務會接聽這兩個端點，且用戶端可以透過任一端點叫用服務。 用戶端應用程式會隨機挑選其中一個轉送做為主要端點，並將其要求傳送至作用中的端點。 如果作業失敗並出現錯誤代碼，此失敗代表轉送端點無法使用。 應用程式會開啟備份端點的通道並重新發出要求。 作用中和備份端點會在這一點交換角色：用戶端應用程式會將舊的使用中端點視為新的備份端點，而將舊的備份端點視為新的作用中端點。 如果這兩個傳送作業都失敗，兩個實體的角色會保持不變並傳回錯誤。

 [與服務匯流排轉送訊息的地理區域複寫][] 範例會示範如何進行複寫轉送。

## 保護佇列和主題免於發生資料中心中斷或災害

若要達到針對資料中心中斷的彈性，使用代理訊息時，服務匯流排支援兩種方法: *active* 和 *被動* 複寫。 無論使用哪個方法，如果特定佇列或主題必須在資料中心發生中斷時保持可存取狀態，您可以在這兩個命名空間建立該佇列或主題。 這兩個實體可以具有相同的名稱。 比方說，主要佇列可以達到下 **contosoPrimary.servicebus.windows.net/myQueue**, ，而第二個對應項目可以達到下 **contosoSecondary.servicebus.windows.net/myQueue**。

如果應用程式不需要永久的傳送者至接收者通訊，應用程式可以實作持續的用戶端佇列，以防止訊息遺失並避免傳送者發生任何暫時性服務匯流排錯誤。

## 主動複寫

主動複寫會針對每個作業使用兩個命名空間中的實體。 傳送訊息的任何用戶端會傳送相同訊息的兩個複本。 第一個複本傳送至主要實體 (例如， **contosoPrimary.servicebus.windows.net/sales**)，和第二份訊息複本傳送至次要實體 (比方說， **contosoSecondary.servicebus.windows.net/sales**)。

用戶端會從這兩個佇列接收訊息。 接收者會處理訊息的第一個複本，並隱藏第二個複本。 若要隱藏重複的訊息，傳送者必須利用唯一的識別碼標記每個訊息。 訊息的兩個複本必須以相同的識別碼標記。 您可以使用 [BrokeredMessage.MessageId][] 或 [BrokeredMessage.Label][] 屬性或自訂屬性來標記訊息。 接收者必須維護其已收到的訊息清單。

 [與服務匯流排代理訊息的地理區域複寫][] 範例將示範如何使用中複寫的訊息實體。

> [AZURE.NOTE] 主動複寫方法會讓作業數目加倍，因此這個方法可能會造成更高的成本。

## 被動複寫

在沒有錯誤的情況下，被動複寫只會使用兩個訊息實體的其中之一。 用戶端會傳送訊息至作用中的實體。 如果作用中實體上的作業失敗，並出現錯誤碼指出裝載作用中實體的資料中心可能會無法使用，用戶端會傳送訊息的複本至備份實體。 作用中和備份實體會在這一點交換角色：傳送用戶端會將舊的使用中實體視為新的備份實體，而將舊的備份實體視為新的作用中實體。 如果這兩個傳送作業都失敗，兩個實體的角色會保持不變並傳回錯誤。

用戶端會從這兩個佇列接收訊息。 因為接收者可能會收到相同訊息的兩個複本，所以接收者必須隱藏重複的訊息。 您可以主動複寫所描述的相同方式隱藏重複訊息。

一般而言，被動複寫比主動複寫更具經濟效益，因為大部分的情形只會執行一項作業。 延遲、輸送量和成本與非複寫案例相同。

使用被動複寫時，訊息可能會在下列案例中遺失或收到兩次：

-   **訊息延遲或遺失**: 假設寄件者成功傳送訊息 m1 至主要佇列，然後佇列會在接收端收到 m1。 傳送者會將後續的訊息 m2 傳送至次要佇列。 如果主要佇列暫時無法使用，接收者會在佇列可再次使用之後收到 m1。 若發生災害，接收者可能永遠無法收到 m1。

-   **重複接收**: 假設寄件者傳送到主要佇列的訊息 m。 服務匯流排已成功處理 m 但無法傳送回應。 傳送作業逾時之後，傳送者會將 m 的相同複本傳送至次要佇列。 如果接收者可以在主要佇列無法使用之前收到 m 的第一個複本，接收者會幾乎同時接收 m 的兩個複本。 如果接收者無法在主要佇列無法使用之前收到 m 的第一個複本，接收者一開始只會收到 m 的第二個複本，但會接著在主要佇列可以使用時會收到 m 的第二個複本。

 [與服務匯流排代理訊息的地理區域複寫][] 範例會示範被動複寫訊息實體。

## 持續用戶端佇列

如果應用程式可以容忍服務匯流排實體無法使用，但不能遺失訊息，傳送者可以採用持續用戶端佇列，此佇列會在本機儲存所有無法傳送至服務匯流排的訊息。 一旦服務匯流排實體再度可以使用，所有緩衝的訊息都會傳送至該實體。  [持久訊息寄件者][] 範例會實作這類的 MSMQ 佇列。 或者，訊息可以寫入至本機磁碟。

持續用戶端佇列會保留訊息順序，並在服務匯流排實體無法使用時保護用戶端應用程式免於發生例外狀況。 它可以搭配簡單和分散式交易使用。

## 後續步驟

若要深入了解災害復原，請參閱這些文章：

- [Azure SQL Database 商務持續性][]
- [Azure 業務持續性技術指引][]

  [Service Bus Authentication]: service-bus-authentication-and-authorization.md
  [Partitioning Messaging Entities]: service-bus-partitioning.md
  [Asynchronous Messaging Patterns and High Availability]: https://msdn.microsoft.com/library/azure/dn292562.aspx
  [Geo-replication with Service Bus Relayed Messages]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Geo-replication with Service Bus Brokered Messages]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Durable Message Sender]: http://code.msdn.microsoft.com/Service-Bus-Durable-Sender-0763230d
  [Azure SQL Database Business Continuity]: https://msdn.microsoft.com/library/azure/hh852669.aspx
  [Azure Business Continuity Technical Guidance]: https://msdn.microsoft.com/library/azure/hh873027.aspx

