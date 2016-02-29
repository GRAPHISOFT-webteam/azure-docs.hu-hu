<properties 
    pageTitle="傳送推播通知給已驗證的通用 Windows 應用程式使用者。" 
    description="了解如何從 Azure 行動服務傳送推播通知給您通用 Windows C# 應用程式的特定使用者。" 
    services="mobile-services,notification-hubs" 
    documentationCenter="windows" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/11/2015" 
    ms.author="glenga"/>

# 傳送推播通知給已驗證的使用者

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##概觀
本主題說明如何將推播通知傳送給任何已註冊之 iOS 裝置上的驗證使用者。 不同於先前的 [新增推播通知給您的應用程式] 教學課程，本教學課程會變更您的行動服務要求用戶端才能向通知中心註冊推播通知之前，必須驗證使用者。 此外也會修改註冊，以根據指派的使用者識別碼新增標記。 最後會更新伺服器指令碼，而僅將通知傳送給已驗證的使用者，而不是所有的註冊。

本教學課程將逐步引導您完成下列程序：

1. [更新服務以要求註冊的驗證]
2. [更新以在註冊前先登入應用程式]
3. [測試應用程式]
 
本教學課程支援 Windows 市集和 Windows Phone 市集應用程式。

##必要條件 

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [將驗證新增至您的應用程式]<br/>將登入需求加入至 TodoList 範例應用程式。

+ [新增推播通知至您的應用程式]<br/>使用通知中心設定推播通知的 TodoList 範例應用程式。 

在您完成這兩個教學課程後，您即可防止未經授權的使用者從您的行動服務註冊推播通知。

##<a name="register"></a>更新服務以要求註冊所需的驗證

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

&nbsp;&nbsp;5. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

    function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;       

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
                push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
                    request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
    }

&nbsp;&nbsp;此 insert 指令碼會使用「使用者識別碼標記」傳送推播通知 (包含插入項目的文字) 給已登入的使用者建立的所有 Windows 市集應用程式註冊。

##<a name="update-app"></a>更新應用程式以在註冊前先登入

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)] 

##<a name="test"></a>測試應用程式

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)] 

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]:#next-steps


<!-- URLs. -->
[Add authentication to your app]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Add push notifications to your app]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md 
