<properties
    pageTitle="傳送推播通知給 iOS 中的已驗證使用者 (JavaScript 後端)"
    description="了解如何將推播通知傳送給特定使用者"
    services="mobile-services,notification-hubs"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>


<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2015"
    ms.author="krisragh"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

在本主題中，您將了解如何將推播通知傳送給 iOS 上已驗證的使用者。 開始本教學課程之前, 完成 [Get started with authentication] 和 [Get started with push notifications] 第一次。

在本教學課程中，您會要求使用者先進行驗證、向通知中心註冊進行推播通知，以及更新伺服器指令碼以傳送這些通知給已驗證的使用者。


##<a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

取代 `insert` 函式使用下列程式碼，然後按一下 [ **儲存**。 此插入指令碼使用已登入使用者的使用者識別碼標記來傳送推播通知給所有 iOS 應用程式註冊：

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]:#next-steps


<!-- URLs. -->
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md


