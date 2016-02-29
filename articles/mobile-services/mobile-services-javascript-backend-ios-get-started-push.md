<properties
    pageTitle="將推播通知新增至應用程式 (iOS) | JavaScript 後端"
    description="了解如何使用 Azure 行動服務傳送推播通知至 iOS 應用程式。"
    services="mobile-services,notification-hubs"
    documentationCenter="ios"
    manager="dwrede"
    editor=""
    authors="krisragh"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/28/2015"
    ms.author="krisragh"/>

# 將推播通知新增至 iOS 應用程式和 JavaScript 後端

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

本主題說明如何新增推播通知給 [快速入門專案](mobile-services-ios-get-started.md), ，以便您的行動服務推播通知，每次一筆記錄插入時傳送。 您必須完成 [開始使用行動服務] 第一次。

> [AZURE.NOTE]  [IOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), ，所以您必須使用實體 iOS 裝置。 您也需要付費註冊 [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/)。

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>設定 Azure 來傳送推播通知

[AZURE.INCLUDE [Configure Push Notifications in Azure Mobile Services](../../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>更新後端指令碼以傳送推播通知

* 在 [Azure 傳統入口網站]，按一下 [ **資料** 標籤，然後按一下 **TodoItem**。 在 **TodoItem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。 這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

* 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**。  This registers a new insert script, which uses the [apns object] to send a push notification (the inserted text) to the device provided in the insert request. 此指令碼會延遲通知的傳送，讓您有時間關閉應用程式來接收推播通知。


```
        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }
```

[AZURE.INCLUDE [Add Push Notifications to App](../../includes/add-push-notifications-to-app.md)]

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]


<!-- Anchors. -->


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Azure classic portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-ios-push-notifications-app-users.md
[What are Notification Hubs?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-ios-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-ios-send-localized-breaking-news.md
[Mobile Services Objective-C how-to conceptual reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

