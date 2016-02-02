<properties
    pageTitle="通知中樞重大消息教學課程 - iOS"
    description="了解如何使用 Azure 服務匯流排通知中樞將本地化重大新聞通知傳送至 iOS 裝置。"
    services="notification-hubs"
    documentationCenter="ios"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="wesmc"/>


# 使用通知中心傳送即時新聞

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


## 概觀

本主題將說明如何使用 Azure 通知中心，將即時新聞通知廣播至 iOS 應用程式。 完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。 此情況是許多應用程式的共同模式，這些應用程式必須將通知傳送給先前宣告對通知有興趣的使用者群組，例如，RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中心內建立註冊時，您可以透過包含一或多個 _tags_ 來啟用廣播案例。 當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。 由於標籤只是簡單的字串而已，您無需預先佈建標籤。 如需標記的詳細資訊，請參閱 [通知中樞路由和標記運算式](notification-hubs-routing-tag-expressions.md)。


## 必要條件

本主題會在您建立的應用程式以 [開始使用通知中樞 ][get-started]。 在開始本教學課程之前, 您必須已完成 [開始使用通知中樞 ][get-started]。

## 在應用程式中新增類別選項

第一個步驟是在您現有的腳本上新增 UI 元素，以便使用者選取要註冊的類別。 使用者所選取的類別會儲存在裝置上。 啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1. 在您的 MainStoryboard_iPhone.storyboard 中，從物件程式庫新增下列元件：
    + 具有「即時新聞」文字的標籤，
    + 具有「世界」、「政治」、「商業」、「技術」、「科學」、「體育」等類別文字的標籤，
    + 六個參數 (一個類別一個)，預設會將每個參數 [狀態]**** 設為 [關閉]****。
    + 一個標示為「訂閱」的按鈕

    您的腳本應如下所示：

    ![][3]

2. 在輔助編輯器中，建立所有參數的出口，並將其命名為 "WorldSwitch"、"PoliticsSwitch"、"BusinessSwitch"、"TechnologySwitch"、"ScienceSwitch"、"SportsSwitch"

3. 為名為「訂閱」的按鈕建立「動作」。 您的 ViewController.h 應該包含下列內容：

     @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;
    
     - (IBAction)subscribe:(id)sender;

4. 建立新 **Cocoa Touch 類別** 呼叫 `通知`。 在 Notifications.h 的介面區段中複製下列程式碼：

     @property NSData* deviceToken;
    
     - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;
    
     - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                 completion:(void (^)(NSError* error))completion;
    
     - (NSSet*)retrieveCategories;
    
     - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. 在 Notifications.m 中新增下列 import 指示詞：

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. 複製 Notifications.m 檔案實作區段中的下列程式碼：

     SBNotificationHub* hub;
    
     - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{
    
         hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                     notificationHubPath:hubName];
    
         return self;
     }
    
     - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
         NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
         [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
    
         [self subscribeWithCategories:categories completion:completion];
     }
    
    
     - (NSSet*)retrieveCategories {
         NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
    
         NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];
    
         if (!categories) return [[NSSet alloc] init];
         return [[NSSet alloc] initWithArray:categories];
     }
    
    
     - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
     {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
    
         NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";
    
         [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
             jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
     }

 此類別會使用本機儲存體來儲存和擷取此裝置將接收的新聞類別。 此外，它也包含註冊這些類別使用的方法 [範本](notification-hubs-templates.md) 註冊。

7. 在 AppDelegate.h 檔案中，新增 Notifications.h 的匯入陳述式，並新增 Notifications 類別執行個體的屬性：

     #import "Notifications.h"
    
     @property (nonatomic) Notifications* notifications;

8. 在 AppDelegate.m 的 **didFinishLaunchingWithOptions** 方法中，於方法的開頭處加入程式碼來初始化通知執行個體。

    `HUBNAME` 和 `HUBLISTENACCESS` (定義於 hubinfo.h) 應該已經 `< 中樞名稱 >` 和 `< 連接字串具有接聽權限 >` 的通知中心名稱和連接字串預留位置取代 *DefaultListenSharedAccessSignature* 您稍早取得

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] 因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。 您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。 在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

9. 在 AppDelegate.m 的 **didRegisterForRemoteNotificationsWithDeviceToken** 方法中，使用下列程式碼來取代方法中的程式碼，以將裝置權杖傳遞給 notifications 類別： Notifications 類別將會執行使用類別註冊通知。 如果使用者變更類別選取項目，我們會呼叫 `subscribeWithCategories` 方法以回應 **訂閱** 按鈕來進行更新。
 > [AZURE.NOTE] 由於 Apple 推播通知服務 (APNS) 所指派的裝置權杖可能隨時會變更，因此您應經常註冊通知以避免通知失敗。 此範例會在應用程式每次啟動時註冊通知。 若是經常執行 (一天多次) 的應用程式，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。

     self.notifications.deviceToken = deviceToken;
    
     // Retrieves the categories from local storage and requests a registration for these categories
     // each time the app starts and performs a registration.
    
     NSSet* categories = [self.notifications retrieveCategories];
     [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
         if (error != nil) {
             NSLog(@"Error registering for notifications: %@", error);
         }
     }];

 請注意，**didRegisterForRemoteNotificationsWithDeviceToken** 方法中此時不應有其他程式碼。

10. 下列方法應該已經出現在 AppDelegate.m 中完成 [開始使用通知中樞 ][get-started] 教學課程。 否則，請予以新增。

    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }
    
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }

此方法會顯示簡易 **UIAlert**，以處理應用程式執行時接收到的通知。

11. 在 ViewController.m 中，新增 AppDelegate.h 的匯入陳述式，並將下列程式碼複製到 XCode 產生的**訂閱**方法中。 此程式碼將更新通知註冊，以使用使用者在使用者介面中選擇的新類別標記。

    ```
    #import "Notifications.h"
    ```
    
    NSMutableArray* categories = [[NSMutableArray alloc] init];
    
    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];
    
    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];

此方法會建立類別的 **NSMutableArray**，並使用 **Notifications** 類別在本機儲存體中儲存清單，並在您的通知中心註冊對應標籤。 變更類別時，系統會使用新類別重新建立註冊。

12. 在 ViewController.m 中，於 **viewDidLoad** 方法中新增下列程式碼，以根據先前儲存的類別來設定使用者介面。

    // This updates the UI on startup based on the status of previously saved categories.
    
    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];
    
    NSSet* categories = [notifications retrieveCategories];
    
    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;




應用程式現在可以在裝置本機儲存體中儲存一組類別，以用來在每次應用程式啟動時於通知中樞註冊。 使用者可以在執行階段變更選取的類別，然後按一下 [訂閱]**** 方法來更新裝置的註冊。 接下來，您將更新應用程式，以直接在應用程式本身傳送即時新聞通知。


## (選擇性) 傳送加註標記的通知

如果您無法存取 Visual Studio，可以跳到下一節，並從應用程式本身傳送通知。 您也可以從 [Azure 傳統入口網站] 來傳送適當的範本通知使用通知中心的 [偵錯] 索引標籤。

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


## (選擇性) 從裝置傳送通知

通知通常會由後端服務傳送，但您可直接從應用程式傳送即時新聞通知。 若要這樣做，我們會更新 `SendNotificationRESTAPI` 中所定義的方法 [開始使用通知中樞 ][get-started] 教學課程。


1. 在 ViewController.m 更新 `SendNotificationRESTAPI` 方法，遵循，使其可接受參數，類別標記，並會傳送適當 [範本](notification-hubs-templates.md) 通知。

     - (void)SendNotificationRESTAPI:(NSString*)categoryTag
     {
         NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                  defaultSessionConfiguration] delegate:nil delegateQueue:nil];
    
         NSString *json;
    
         // Construct the messages REST endpoint
         NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];
    
         // Generated the token to be used in the authorization header.
         NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
    
         //Create the request to add the template notification message to the hub
         NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
         [request setHTTPMethod:@"POST"];
    
         // Add the category as a tag
         [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];
    
         // Template notification
         json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                 categoryTag, self.notificationMessage.text];
    
         // Signify template notification format
         [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];
    
         // JSON Content-Type
         [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
    
         //Authenticate the notification message POST request with the SaS token
         [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
    
         //Add the notification message body
         [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    
         // Send the REST request
         NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];
    
         [dataTask resume];
     }

2. 在 ViewController.m 中，更新 [傳送通知]**** 動作 (如下列程式碼所示)。 因此，它將使用每個標記個別傳送通知，並傳送至多個平台。


     - (IBAction)SendNotificationMessage:(id)sender
     {
         self.sendResults.text = @"";
    
         NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                 @"Technology", @"Science", @"Sports", nil];
    
         // Lets send the message as breaking news for each category to WNS, GCM, and APNS
         // using a template.
         for(NSString* category in categories)
         {
             [self SendNotificationRESTAPI:category];
         }
     }

3. 重新建置專案，並確定您沒有建置錯誤。


## 執行應用程式並產生通知

1. 按 [執行] 按鈕，以建置專案並啟動應用程式。 選取要訂閱的一些即時新聞選項，然後按 [訂閱]**** 按鈕。 您應該會看到一個對話方塊，表示已訂閱通知。

    ![][1]

    當您選擇 [訂閱]**** 時，應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。

2. 輸入要以即時新聞形式傳送的訊息，然後按下 [傳送通知]**** 按鈕。 或者，執行.NET 主控台應用程式來產生通知。

    ![][2]

3. 每個訂閱即時新聞的裝置都會收到您剛剛傳送的即時新聞通知。



## 後續步驟

在本教學課程中，我們了解到如何按類別廣播即時新聞。 請考慮完成下列其中一個強調其他進階通知中心案例的教學課程：

+ **[使用通知中心廣播已當地語系化的即時新聞]**

    了解如何擴充即時新聞應用程式，以啟用傳送已當地語系化的通知。








[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png 
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png 
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png 
[how to: service bus notification hubs (ios apps)]: http://msdn.microsoft.com/library/jj927168.aspx 
[use notification hubs to broadcast localized breaking news]: notification-hubs-ios-send-localized-breaking-news.md 
[mobile service]: /develop/mobile/tutorials/get-started 
[notify users with notification hubs]: notification-hubs-aspnet-backend-ios-notify-users.md 
[notification hubs guidance]: http://msdn.microsoft.com/library/dn530749.aspx 
[notification hubs how-to for ios]: http://msdn.microsoft.com/library/jj927168.aspx 
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/ 
[azure classic portal]: https://manage.windowsazure.com 

