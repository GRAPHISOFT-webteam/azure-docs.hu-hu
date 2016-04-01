<properties 
    pageTitle="使用 Azure App Service 將推播通知新增至 Xamarin.iOS 應用程式" 
    description="了解如何使用 Azure App Service 將推播通知傳送至 Xamarin.iOS 應用程式。" 
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="wesmc7777"
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-xamarin-ios" 
    ms.devlang="dotnet" 
    ms.topic="article"
    ms.date="12/01/2015" 
    ms.author="wesmc"/>

# 將推播通知新增至 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

本教學課程根據 [Xamarin.iOS 快速入門教學課程](app-service-mobile-xamarin-ios-get-started.md), ，您必須先完成。 您將會新增推播通知至 Xamarin.iOS 快速入門專案，以便在每次插入一筆記錄時傳送推播通知。 如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。 

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。  
如果您還沒有帳戶，請註冊 Azure 試用版，還可獲得最多 10 個免費的行動應用程式後端。 試用結束之後您仍可以繼續使用它們。 請參閱 [Azure 免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

* 使用 Mac [Xamarin Studio] 和 [Xcode] v4.4 或更新版本安裝它。 如果想要，您可以在 Windows 電腦上使用 Visual Studio 執行 Xamarin.iOS 應用程式，但這樣會比較複雜，因為您必須連線到已加入網路並正在執行 Xamarin.iOS 組建主機 的 Mac。 如果您想這麼做，請參閱 [Installing Xamarin.iOS on Windows]。

* 實體的 iOS 裝置。 iOS 模擬器不支援推播通知。

*  [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/), ，才能註冊使用 Apple 推播通知服務 (APNS)。

* 完成 [Xamarin.iOS 快速入門教學課程](app-service-mobile-xamarin-ios-get-started.md)。


##在 Apple 的開發人員入口網站註冊應用程式以取得推播通知

[AZURE.INCLUDE [Notification Hubs Xamarin Enable Apple Push Notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##設定您的行動應用程式以傳送推播通知

若要設定應用程式以傳送通知，請建立新的中樞，並針對您要使用的平台通知服務加以設定。  

1. 在 [Azure 入口網站](https://portal.azure.com/), ，按一下 **瀏覽** > **行動應用程式** > 行動應用程式 > **設定** > **行動** > **推播** > **通知中心** > **+ 通知中心**, ，並提供新的通知中心名稱和命名空間，然後按一下 **確定** ] 按鈕。

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. 在 [建立通知中心] 分頁中，按一下 [ **建立**。

3. 按一下 [ **推送** > **Apple (APN)** > **上傳憑證**。 上傳您稍早匯出的 .p12 推播憑證檔案。  請務必選取 **沙箱** 如果您建立開發和測試開發推播憑證。  否則，請選擇 **生產**。 您的服務現在已設定成在 iOS 上使用推播通知！

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

##更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##將伺服器專案部署至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##設定您的 Xamarin.iOS 專案

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##將推播通知新增至應用程式

1. 在 **QSTodoService**, ，新增下列屬性，讓 **AppDelegate** 可以取得行動用戶端 ︰
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. 新增下列 `using` 陳述式的頂端 **AppDelegate.cs** 檔案。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. 在 **AppDelegate**, ，覆寫 **FinishedLaunching** 事件 ︰ 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 在相同檔案中，覆寫 **RegisteredForRemoteNotifications** 事件。 透過此程式碼，您將註冊能夠在伺服器所支援的所有平台間傳送的簡單範本通知。 
 
    如需有關使用通知中樞範本的詳細資訊，請參閱 [範本](../notification-hubs/notification-hubs-templates.md)。 


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. 然後，覆寫 **DidReceivedRemoteNotification** 事件 ︰

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

您的應用程式現在已更新為支援推播通知。

## <a name="test"></a>在應用程式中測試推播通知

1. 按下 **執行** 按鈕以建置專案並啟動應用程式在執行 iOS 的裝置，然後按一下 [ **確定** 以接受推播通知。
    
    > [AZURE.NOTE] 您必須明確地接受推播通知，從您的應用程式。 只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中，輸入一項工作，然後按一下加號 (**+**) 圖示。

3. 確認已收到通知，然後按一下 [ **確定** 以關閉通知。

4. 重複執行步驟 2 並立即關閉應用程式，接著確認通知已顯示。

您已成功完成此教學課程。

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


 

