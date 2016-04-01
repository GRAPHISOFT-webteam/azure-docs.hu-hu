<properties
    pageTitle="Azure 通知中心豐富內容推播"
    description="了解如何從 Azure 將各種推播通知傳送至 iOS 應用程式。 程式碼範例是以 Objective-C 及 C# 撰寫。"
    documentationCenter="ios"
    services="notification-hubs"
    authors="wesmc7777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="wesmc"/>

#Azure 通知中心豐富內容推播


##概觀

為了與使用者進行即時豐富內容交流，應用程式可能會想要推播純文字以外的內容。 這些通知可提高使用者互動，並呈現如 url、 音效、 影像/優待券，及其他等內容。 本教學課程以 [通知使用者](notification-hubs-aspnet-backend-ios-notify-users.md) 主題，並示範如何傳送包含裝載 （例如影像） 的推播通知。


本教學課程適用於 iOS 7 和 8。

  ![][IOS1]

概括而言：

1. 應用程式後端：
    - 在後端資料庫/本機儲存體儲存豐富的裝載 (在此案例中為影像)
    - 將此豐富通知的識別碼傳送至裝置
2. 裝置上的應用程式：
    - 連絡後端，並要求收到識別碼的豐富裝載
    - 完成資料擷取時，在裝置上傳送使用者通知，並在使用者點選深入了解時立即顯示裝載


## WebAPI 專案

1. 在 Visual Studio 中開啟 **AppBackend** 建立的專案 [通知使用者](notification-hubs-aspnet-backend-ios-notify-users.md) 教學課程。
2. 取得您想要使用，來通知使用者，並將它放在影像 **i m g** 專案目錄資料夾中的。
3. 按一下 [ **顯示所有檔案** 在 [方案總管] 中，以滑鼠右鍵按一下資料夾以 **加入至專案**。
4. 選取影像後，變更其建置動作屬性] 視窗中 **內嵌資源**。

    ![][IOS2]

5. 在 **Notifications.cs**, ，新增下列 using 陳述式 ︰

        using System.Reflection;

6. 更新整個 **通知** 類別取代下列程式碼。 請務必使用您的通知中樞認證和影像檔名稱來取代預留位置。

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

> [AZURE.NOTE]  （選擇性）請參閱 [如何內嵌和存取資源，使用 Visual C#](http://support.microsoft.com/kb/319292) 如需有關如何新增和取得專案資源。

7. 在 **NotificationsController.cs**, ，重新定義 **NotificationsController**  使用下列程式碼片段。 這會將初始無訊息豐富內容通知識別碼傳送到裝置，並可讓用戶端擷取影像：

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. 為了可以從所有裝置存取此應用程式，我們現在可以將它重新部署到 Azure 網站。 以滑鼠右鍵按一下 **AppBackend** 專案，然後選取 **發行**。

9. 選取 Azure 網站作為您的發行目標。 登入您的 Azure 帳戶並選取現有或新的網站，並記下的 **目的地 URL** 屬性 **連接** ] 索引標籤。 我們將此 URL 作為您 *後端端點* 稍後在本教學課程。 按一下 [ **發行**。

## 修改 iOS 專案

既然您已修改應用程式後端傳送只 *識別碼* 的通知，您將變更 iOS 應用程式來處理該識別碼，並從後端擷取豐富內容的訊息。

1. 開啟您的 iOS 專案，並移至主要應用程式目標中啟用遠端通知 **目標** 一節。

2. 按一下 [ **功能**, ，開啟 **背景模式**, ，並檢查 **遠端通知** 核取方塊。

    ![][IOS3]

3. 移至 **Main.storyboard**, ，並確定您有檢視控制器 （在稱為首頁檢視控制器在本教學課程） 從 [通知使用者](notification-hubs-aspnet-backend-ios-notify-users.md) 教學課程。

4. 新增 **導覽控制器** 腳本及控制項拖曳到 [首頁檢視控制器設 **根檢視** 的導覽。 請確定 **是初始檢視控制器** 在屬性偵測器選取只針對 [導覽控制器。

5. 新增 **檢視控制器** 腳本，並新增至 **影像檢視**。 這是使用者選擇要深入了解，在按一下通知之後會看到的頁面。 您的腳本應如下所示：

    ![][IOS4]

6. 按一下 [ **首頁檢視控制器** 在腳本，並確定它有 **homeViewController** 做為其 **自訂類別** 和 **腳本識別碼** 下身分識別檢查程式。

7. 做為影像檢視控制器] 執行相同 **imageViewController**。

8. 接著，建立新的檢視控制器類別，名為 **imageViewController** 以處理您剛建立的 UI。

9. 在 **imageViewController.h**, ，將下列內容加入控制器的介面宣告。 請務必按住 Ctrl 再從腳本影像檢視拖曳到這些屬性，以連結兩者：

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. 在 **imageViewController.m**, ，結尾處新增下列 **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. 在 **AppDelegate.m**, ，匯入您所建立的影像控制器 ︰

        #import "imageViewController.h"

12. 使用下列宣告新增介面區段：

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. 在 **AppDelegate**, ，請確定您的應用程式註冊中的無訊息通知 **application: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. 替代的下列實作中，以反映 **application: didRegisterForRemoteNotificationsWithDeviceToken** 以反應的腳本 UI 的變更 ︰

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. 然後，新增下列方法來 **AppDelegate.m** 擷取您的端點影像，並擷取完成時傳送本機通知。 請務必以後端端點取代預留位置 `{backend endpoint}`：

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. 藉由開啟中的影像檢視控制器處理上述本機通知 **AppDelegate.m** 使用下列方法 ︰

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## 執行應用程式

1. 在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。

2. 在 iOS 應用程式 UI，輸入使用者名稱和密碼相同值的驗證，然後按一下 [ **登入**。

3. 按一下 [ **傳送推播** ，您應該會看到應用程式內通知。 如果您按一下 **詳細**, ，您將會顯示您選擇要包含在應用程式後端中的影像。

4. 您也可以按一下 **傳送推播** 並立即按下裝置的 [首頁] 按鈕。 在幾分鐘的時間內，您就會收到推播通知。 如果您點選該通知或按一下 [詳細說明]，則會顯示您的應用程式以及豐富的影像內容。


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png


