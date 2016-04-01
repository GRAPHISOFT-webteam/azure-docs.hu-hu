<properties
    pageTitle="路由與標記運算式"
    description="本主題說明 Azure 通知中樞的路由與標記運算式。"
    services="notification-hubs"
    documentationCenter=".net"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/25/2015"
    ms.author="wesmc"/>

# 路由與標記運算式

##概觀

標記運算式可讓您在透過通知中樞傳送推播通知時，指定特定的目標裝置或更明確的註冊。


## 指定特定的註冊

指定特定通知註冊的唯一方法，就是關聯標記與註冊，然後再指定這些標記。 中所述 [註冊管理](notification-hubs-registration-management.md), ，以接收推播通知註冊裝置的應用程式有處理通知中心。 當在通知中樞建立註冊之後，應用程式後端便能傳送推播通知。
應用程式後端可以下列方式，選擇特定通知的目標註冊：

1. **廣播**︰ 通知中心內的所有註冊都收到通知。
2. **標記**︰ 包含指定之的標記的所有註冊都收到通知。
3. **標記運算式**︰ 標記集的比對指定之的運算式的所有註冊都收到通知。

## 標記

標記可以是任何字串，包括英數字元及下列非英數字元，且長度不得超過 120 個字元：_、@、#、.、:、-。 下列範例示範的應用程式可以讓您從中接收有關特定音樂群組的快顯通知。 在此案例中，一個簡單路由通知的方法是為註冊加上標記，以指出不同的樂團，如下列圖片所示。

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

在此圖中，將訊息標記 **披頭四** 到達註冊的標記 tablet **Beatles**。

如需建立標記的註冊的詳細資訊，請參閱 [註冊管理](notification-hubs-registration-management.md)。

您可以傳送通知給標記的方式傳送通知的 `Microsoft.Azure.NotificationHubs.NotificationHubClient` 類別 [Microsoft Azure 通知中樞](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK。 您也可以使用 Node.js 或推播通知 REST API。  以下是 SDK 的使用範例。


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




標記無須事先佈建，而且可以參考多個應用程式特有的概念。 例如，此範例應用程式的使用者可能想要對所有樂團發表評論，但不想只收到他們評論及喜愛之樂團的快顯通知，也想要收到來自他們朋友之所有評論的快顯通知。 下圖顯示此案例的範例：



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

在此圖片中，Alice 關注的是 Beatles 的最新動態，Bob 關注的是 Wailers 的最新動態。 Bob 也會關注 Charlie 的評論，而 Charlie 則會關注 Wailers。 當 Charlie 對 Beatles 發表評論時，Alice 與 Bob 都會收到該通知。

雖然您可以在標記中編寫多項您所關注的事物 (例如 "band_Beatles" 或 "follows_Charlie")，標記仍屬於簡單字串，而不是具有屬性的值。 只有在指定有特定標記或未指定特定標記時，才會比對註冊。

如何使用標籤將傳送至相關群組的完整逐步教學課程，請參閱 [即時新聞](notification-hubs-windows-store-dotnet-send-breaking-news.md)。


## 使用標記指定使用者

另一種使用標記的方法，是指定特定使用者的所有裝置。 註冊可以加上包含使用者識別碼的特殊標記，如圖所示：


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

在此圖片中，標記 uid:Alice 的訊息會傳送給所有標記 uid:Alice 的註冊，也就是 Alice 的所有裝置。


##標記運算式

在有些情況下，通知的目標是一組註冊，而這些註冊又不是以單一標記識別，而須透過標記上的布林運算式。

假設有一支運動應用程式會將提醒傳送給波士頓的每個人，告知他們有關於紅襪隊與紅雀隊之間的賽事訊息。 若用戶端應用程式註冊關於球隊與地點的標記，則通知的目標應為波士頓中位關注紅襪隊或紅雀隊的每個人。 此條件可以下列布林運算式表示：

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

標記運算式可以包含所有布林運算子，例如 AND (&&)、OR (||) 及 NOT (!)。 其也可包含括號。 標記運算式若只含 OR，最多可有 20 個標記，否則只可有 6 個標記。

以下是使用 SDK 傳送採用標記運算式之通知的範例。


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


