<properties 
    pageTitle="使用 Azure App Service 將推播通知新增至 Xamarin.Forms 應用程式" 
    description="了解如何使用 Azure App Service 將推播通知傳送至 Xamarin.Forms 應用程式" 
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="wesmc7777"
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-xamarin" 
    ms.devlang="dotnet" 
    ms.topic="article"
    ms.date="11/25/2015" 
    ms.author="wesmc"/>


# 將推播通知新增至 Xamarin.Forms 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本教學課程根據 [Xamarin.Forms 快速入門教學課程](app-service-mobile-xamarin-forms-get-started.md), ，您必須先完成。 對於您在 Xamarin.Forms 快速入門專案中想要支援的每個專案，您將會加入推播通知支援。 每次插入記錄時，就會傳送推播通知。

如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

[IOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), ，所以您必須使用實體 iOS 裝置。 您也需要註冊 [Apple Developer Program 成員資格](https://developer.apple.com/programs/ios/)。

## 必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。  
如果您還沒有帳戶，註冊 Azure 試用版並取得最多 10 個免費的行動應用程式。 試用結束之後您仍可以繼續使用它們。 請參閱 [Azure 免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

* Mac 的 [Xamarin Studio] 和 [Xcode] v4.4 或更新版本安裝它。 如果想要，您可以在 Windows 電腦上使用 Visual Studio 執行 Xamarin.Forms 應用程式，但這樣會比較複雜，因為您必須連線到已加入網路並正在執行 Xamarin.iOS 組建主機的 Mac。 如果您想要這樣做，請參閱 [在 Windows 上的安裝 Xamarin.iOS]。

* 實體的 iOS 裝置。 iOS 模擬器不支援推播通知。

* 完成 [Xamarin.Forms 快速入門教學課程](app-service-mobile-xamarin-forms-get-started.md)。

## 建立行動應用程式的通知中樞

若要設定應用程式以傳送通知，請建立新的中樞，並在其中設定您要使用的平台通知服務。

這些步驟會引導您建立新的通知中樞。 如果您已經建立通知中樞，直接選擇它即可。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 按一下 [瀏覽]**** > [Mobile Apps]**** > 您的後端 > [設定]**** > [行動]**** > [推送]**** > [通知中樞]**** > [+通知中樞]****，提供新通知中樞的名稱和命名空間，然後按一下 [確定]**** 按鈕。

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. 在 [建立通知中樞] 刀鋒視窗中，按一下 [建立]****。


## 更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## 將已更新的伺服器專案部署至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


## (選擇性) 設定和執行 Android 專案

這一節適用於對 Android 裝置執行 Xamarin Adroid 專案。 如果未使用 Android 裝置，可以略過這一節。


#### 啟用 Google 雲端通訊 (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


#### 設定 GCM 的通知中樞

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 按一下 [瀏覽]**** > [Mobile Apps]**** > 您的行動應用程式 > [設定]**** > [推送]**** > [Google (GCM)]****。 貼上您先前建立的伺服器 API 金鑰，然後按一下 [儲存]****。 您的服務現在已設定為對 Android 使用推播通知。

    ![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


#### 將推播通知新增至 droid 專案

1. 在 [元件] 資料夾上按一下滑鼠右鍵，按一下 [取得更多元件...]，搜尋 [Google 雲端通訊用戶端]**** 元件，然後將它新增至專案。 此元件可協助簡化在 Xamarrin Android 專案中使用推播通知。

2. 開啟 MainActivity.cs 專案檔案，然後將下列 using 陳述式加入至檔案頂端：

        using Gcm.Client;

3. 加入下列程式碼以 `OnCreate` 方法的呼叫之後 `Oncreate`。

     try
     {
         // Check to ensure everything's setup right
         GcmClient.CheckDevice(this);
         GcmClient.CheckManifest(this);
    
         // Register for push notifications
         System.Diagnostics.Debug.WriteLine("Registering...");
         GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
     }
     catch (Java.Net.MalformedURLException)
     {
         CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
     }
     catch (Exception e)
     {
         CreateAndShowDialog(e.Message, "Error");
     }

4. 加入下列程式碼 `CreateAndShowDialog` helper 方法。

     private void CreateAndShowDialog(String message, String title) 
     {
         AlertDialog.Builder builder = new AlertDialog.Builder(this);
    
         builder.SetMessage (message);
         builder.SetTitle (title);
         builder.Create().Show ();
     }

5. 在 `MainActivity` 類別中，新增下列程式碼公開 (expose) 目前 `MainActivity` 讓我們可以在主要 UI 執行緒上執行某些 UI:

     // Create a new instance field for this activity.
     static MainActivity instance = null;
    
     // Return the current activity instance.
     public static MainActivity CurrentActivity
     {
         get
         {
             return instance;
         }
     }

6. 初始化 `執行個體`, 變數的開頭、 `MainActivity.OnCreate` 方法。

        // Set the current instance of MainActivity.
        instance = this;

7. 將新的類別檔案加入至 **Droid** 專案。 將新的類別檔案命名為 **GcmService**。

8. 請確定下列 `使用` 陳述式會包含在檔案頂端。

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Android.App;
        using Android.Content;
        using System.Collections.Generic;
        using System.Diagnostics;
        using Android.Util;
        using Newtonsoft.Json.Linq;
        using System.Text;
        using System.Linq;

9. 後面新增下列權限要求在檔案頂端 `使用` 陳述式之前 `命名空間` 宣告。

     [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
     [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
     [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
    
     //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
     [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
     [assembly: UsesPermission(Name = "android.permission.INTERNET")]
     [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. 將下列類別定義加入至命名空間。取代 * *<PROJECT_NUMBER>* * 使用您先前記下您的專案號碼。

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]     
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {       
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };        
        }

11. 更新您 `GcmService` 類別，以使用新的廣播的接收器。

     [Service]
     public class GcmService : GcmServiceBase
     {
         public static string RegistrationID { get; private set; }
    
         public GcmService()
             : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
     }

12. 將下列程式碼加入至 GcmService 類別會覆寫 OnRegistered 事件處理常式，而且會實作 `註冊` 方法。

此程式碼將會註冊範本使用接收通知的範本主體 `messageParam` 參數。 範本通知可讓您跨平台傳送通知。 如需詳細資訊，請參閱 [範本](https://msdn.microsoft.com/library/azure/dn530748.aspx)。

    protected override void OnRegistered(Context context, string registrationId)
    {
        Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
        RegistrationID = registrationId;
    
        createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
    
        var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    
        MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
    
    }
    
    public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
    {
        try
        {
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM}
            };
    
            await push.RegisterAsync(RegistrationID, templates);
            Log.Info("Push Installation Id", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
            Debugger.Break();
        }
    }

13. 您必須實作 `OnMessage` 來處理內送的推播通知。 此程式碼中，我們將會處理通知，並將它傳送至通知管理員。

    protected override void OnMessage(Context context, Intent intent)
    {
        Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");
    
        var msg = new StringBuilder();
    
        if (intent != null && intent.Extras != null)
        {
            foreach (var key in intent.Extras.KeySet())
                msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
        }
    
        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();
    
        string message = intent.Extras.GetString("message");
        if (!string.IsNullOrEmpty(message))
        {
            createNotification("New todo item!", "Todo item: " + message);
            return;
        }
    
        string msg2 = intent.Extras.GetString("msg");
        if (!string.IsNullOrEmpty(msg2))
        {
            createNotification("New hub message!", msg2);
            return;
        }
    
        createNotification("Unknown message details", msg.ToString());
    }
    
    void createNotification(string title, string desc)
    {
        //Create notification
        var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
        //Create an intent to show ui
        var uiIntent = new Intent(this, typeof(MainActivity));
    
        //Create the notification
        var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
        //Auto cancel will remove the notification once the user touches it
        notification.Flags = NotificationFlags.AutoCancel;
    
        //Set the notification info
        //we use the pending intent, passing our ui intent over which will get called
        //when the notification is tapped.
        notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
        //Show the notification
        notificationManager.Notify(1, notification);
    }

14. 您也必須實作 `OnUnRegistered` 和 `OnError` 收件者的處理常式。

    protected override void OnUnRegistered(Context context, string registrationId)
    {
        Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
    }
    
    protected override void OnError(Context context, string errorId)
    {
        Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
    }




#### 在 Android 應用程式中測試推播通知

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下 **droid** 專案，然後按一下 [設定為啟始專案]****。

2. 按 [執行]**** 按鈕以建置專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定]**** 以接受推播通知。
    > [AZURE.NOTE] 您必須明確地接受來自應用程式的推播通知。 只有在應用程式第一次執行時，才會發生此要求。

2. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

3. 確認您已接收到通知，然後按一下 [確定]**** 以關閉通知。





## (選擇性) 設定和執行 iOS 專案

這一節適用於對 iOS 裝置執行 Xamarin iOS 專案。 如果未使用 iOS 裝置，可以略過這一節。

[AZURE.INCLUDE [Notification Hubs Xamarin Enable Apple Push Notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


#### 設定 APNS 的通知中樞

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 按一下 [瀏覽]**** > [Mobile Apps]**** > 您的行動應用程式 > [設定]**** > [推送]**** > [Apple (APNS)]**** > [上傳憑證]****。 上傳您稍早匯出的 .p12 推播憑證檔案。 如果您建立開發推播憑證是為了開發和測試，請務必選取 [**沙箱**]。 否則，請選擇 [**生產**]。 您的服務現在已設定為對 iOS 使用推播通知。

    ![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

    接下來，您將在 Xamarin Studio 或 Visual Studio 中設定 iOS 專案設定。

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


#### 將推播通知加入至 iOS 應用程式

1. 新增下列 `使用` 陳述式的頂端 **AppDelegate.cs** 檔案。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. 在 iOS 專案中，開啟 AppDelegate.cs 和更新`FinishedLaunching` 支援遠端通知，如下所示。

     public override bool FinishedLaunching (UIApplication app, NSDictionary options)
     {
         global::Xamarin.Forms.Forms.Init ();
    
         Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
    
         // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
         // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
         //SQLitePCL.CurrentPlatform.Init();
    
         // registers for push for iOS8
         var settings = UIUserNotificationSettings.GetSettingsForTypes(
             UIUserNotificationType.Alert
             | UIUserNotificationType.Badge
             | UIUserNotificationType.Sound,
             new NSSet());
    
         UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
         UIApplication.SharedApplication.RegisterForRemoteNotifications();
    
         LoadApplication (new App ());
    
         return base.FinishedLaunching (app, options);
     }

4. 在 AppDelegate.cs 中，也新增 **RegisteredForRemoteNotifications** 事件的覆寫以註冊通知：

     public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
     {
         const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
    
         JObject templates = new JObject();
         templates["genericMessage"] = new JObject
             {
               {"body", templateBodyAPNS}
             };
    
         // Register for push with your mobile app
         Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
         push.RegisterAsync(deviceToken, templates);
     }

5. 在 AppDelegate.cs 中，也新增 **DidReceivedRemoteNotification** 事件的覆寫，以便在應用程式執行時處理內送通知：

     public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
     {
         NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;
    
         string alert = string.Empty;
         if (aps.ContainsKey(new NSString("alert")))
             alert = (aps[new NSString("alert")] as NSString).ToString();
    
         //show alert
         if (!string.IsNullOrEmpty(alert))
         {
             UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
             avAlert.Show();
         }
     }


您的應用程式現在已更新為支援推播通知。

#### 在 iOS 應用程式中測試推播通知

1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [設定為啟始專案]****。

2. 在 Visual Studio 中按下 [執行]**** 按鈕或 **F5** 以建置專案，並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定]**** 以接受推播通知。
    > [AZURE.NOTE] 您必須明確地接受來自應用程式的推播通知。 只有在應用程式第一次執行時，才會發生此要求。

3. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

4. 確認您已接收到通知，然後按一下 [確定]**** 以關閉通知。




## (選擇性) 設定和執行 Windows 專案

這一節適用於對 Windows 裝置執行 WinApp 專案。 如果未使用 Windows 裝置，可以略過這一節。


#### 向 WNS 註冊 Windows 應用程式以使用推播通知

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


#### 設定 WNS 的通知中樞

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


#### 將推播通知加入至 Windows 應用程式

1. 在 Visual Studio 中，開啟 **WinApp** 專案中的 **App.xaml.cs**， 新增下列 `使用` 陳述式。

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using WesmcMobileAppGaTest;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. 在 App.xaml.cs 中，新增下列 `InitNotificationsAsync` 方法。 這個方法會取得推播通知通道，並註冊範本以接收來自通知中樞的範本通知。 支援的範本通知 `messageParam` 會傳送到此用戶端。

     private async Task InitNotificationsAsync()
     {
         var channel = await PushNotificationChannelManager
             .CreatePushNotificationChannelForApplicationAsync();
    
         const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
    
         JObject headers = new JObject();
         headers["X-WNS-Type"] = "wns/toast";
    
         JObject templates = new JObject();
         templates["genericMessage"] = new JObject
             {
               {"body", templateBodyWNS},
               {"headers", headers} // Only needed for WNS & MPNS
             };
    
         await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
     }

3. 在 App.xaml.cs 更新 `OnLaunched` 事件處理常式與 `非同步` 屬性並呼叫 `InitNotificationsAsync`

     protected async override void OnLaunched(LaunchActivatedEventArgs e)
     {
         Frame rootFrame = Window.Current.Content as Frame;
    
         // Do not repeat app initialization when the Window already has content,
         // just ensure that the window is active
         if (rootFrame == null)
         {
             // Create a Frame to act as the navigation context and navigate to the first page
             rootFrame = new Frame();
             // Set the default language
             rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
             rootFrame.NavigationFailed += OnNavigationFailed;
             Xamarin.Forms.Forms.Init(e); 
             if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
             {
                 //TODO: Load state from previously suspended application
             }
             // Place the frame in the current Window
             Window.Current.Content = rootFrame;
         }
    
         if (rootFrame.Content == null)
         {
             // When the navigation stack isn't restored navigate to the first page,
             // configuring the new page by passing required information as a navigation
             // parameter
             rootFrame.Navigate(typeof(MainPage), e.Arguments);
         }
         // Ensure the current window is active
         Window.Current.Activate();
    
         await InitNotificationsAsync();
     }

4. 在 Visual Studio 的 [方案總管] 中，開啟 **Package.appxmanifest** 檔案，然後把 [通知]**** 下方的 [支援快顯通知]**** 設為 [是]****。

5. 建置應用程式並確認沒有錯誤。 您用戶端應用程式現在應該註冊行動應用程式後端的範本通知。


#### 在 Windows 應用程式中測試推播通知

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **WinApp** 專案，然後按一下 [設定為啟始專案]****。

2. 按 [執行]**** 按鈕以建置專案並在可執行 iOS 的裝置上啟動應用程式，然後按一下 [確定]**** 以接受推播通知。
    > [AZURE.NOTE] 您必須明確地接受來自應用程式的推播通知。 只有在應用程式第一次執行時，才會發生此要求。

3. 在應用程式中輸入一項工作，然後按一下加號 (**+**) 圖示。

4. 確認您已接收到通知，然後按一下 [確定]**** 以關閉通知。







[xamarin studio]: http://xamarin.com/platform 
[install xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[xcode]: https://go.microsoft.com/fwLink/?LinkID=266532 
[installing xamarin.ios on windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/ 
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333 

