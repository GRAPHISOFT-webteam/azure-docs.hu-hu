<properties
    pageTitle="Azure 通知中樞透過 .NET 後端通知 iOS 使用者"
    description="了解如何在 Azure 中將推播通知傳送給使用者。程式碼範例是以 Objective-C 撰寫並以 .NET API 作為後端。"
    documentationCenter="ios"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="wesmc"/>


# Azure 通知中樞透過 .NET 後端通知 iOS 使用者

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## 概觀

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。 本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。 ASP.NET WebAPI 後端可用來驗證用戶端並產生通知，如指引主題所顯示的 [從應用程式後端註冊](notification-hubs-registration-management.md#registration-management-from-a-backend)。
> [AZURE.NOTE] 本教學課程假設您已建立並設定通知中心中所述 [開始使用通知中樞 (iOS)](notification-hubs-ios-get-started.md)。 本教學課程也是必要條件 [安全推播 (iOS)](notification-hubs-aspnet-backend-ios-secure-push.md) 教學課程。
> 如果您使用行動服務作為後端服務，請參閱 [行動服務版本](../mobile-services-javascript-backend-ios-push-notifications-app-users.md) 本教學課程。



[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 修改您的 iOS 應用程式

1. 開啟您在單一頁面檢視應用程式 [開始使用通知中樞 (iOS)](notification-hubs-ios-get-started.md) 教學課程。
    > [AZURE.NOTE] 在本節中，我們假設您已使用空白組織名稱設定您的專案。 否則，您需要針對所有類別名稱預先考量您的組織名稱。

2. 在您的 Main.storyboard 中從物件程式庫新增下面的螢幕擷取畫面中顯示的元件。

    ![][1]

    + **使用者名稱**：含有預留位置文字 (*輸入使用者名稱*) 的 UITextField，位於傳送結果標籤正下方且受到左右邊界限制並位於傳送結果標籤正下方。
    + **密碼**：含有預留位置文字 (*輸入密碼*) 的 UITextField，位於使用者名稱文字欄位正下方且受到左右邊界限制並位於使用者文字欄位正下方。 勾選 [*傳回金鑰*] 底下屬性偵測器中的 [**安全文字輸入**] 選項。
    + **登入**：密碼文字欄位正下方標記的 UIButton，並取消勾選 [*控制項內容*] 底下屬性偵測器中的 [**啟用**] 選項。
    + **WNS**：當中樞中已設定 Windows 通知服務時，用來啟用傳送通知功能的標籤與開關。 請參閱 [Windows 入門](notification-hubs-windows-store-dotnet-get-started.md) 教學課程。
    + **GCM**：當中樞中已設定 Google Cloud Messaging 時，用來啟用傳送通知功能的標籤與開關。 請參閱 [Android 入門](notification-hubs-android-get-started.md) 教學課程。
    + **APNS**：啟用傳送通知給 Apple 平台通知服務之功能的標籤與開關。
    + **收件者使用者名稱**：含有預留位置文字 (*收件者使用者名稱標記*) 的 UITextField，位於 GCM 標籤正下方且受到左右邊界限制並位於 GCM 正下方。

    某些元件已加入 [開始使用通知中樞 (iOS)](notification-hubs-ios-get-started.md) 教學課程。

3. **Ctrl** 可拖曳檢視中的元件到 ViewController.h，並新增這些新的輸出。

     @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
     @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
     @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
     @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
    
     // Used to enable the buttons on the UI
     @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
     @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
    
     // Used to enabled sending notifications across platforms
     @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
     @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
    
     - (IBAction)LogInAction:(id)sender;

4. 在 ViewController.h 中，新增下列 `#define` 您匯入陳述式正下方。替代 *<Enter Your Backend Endpoint\>* 用來部署您的應用程式後端上一節中的目的地 url 的預留位置。例如， *http://you_backend.azurewebsites.net*。

        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"

4. 在您的專案中，建立一個名為 **RegisterClient** 的新 **Cocoa Touch class** 作為與您建立之 ASP.NET 後端互動的介面。 建立繼承自類別 `NSObject`。 然後在 RegisterClient.h 中新增下列程式碼。

     @interface RegisterClient : NSObject
    
     @property (strong, nonatomic) NSString* authenticationHeader;
    
     -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
         andCompletion:(void(^)(NSError*))completion;
    
     -(instancetype) initWithEndpoint:(NSString*)Endpoint;
    
     @end

5. 在 registerclient.m 中，更新 `@interface` 區段:

     @interface RegisterClient ()
    
     @property (strong, nonatomic) NSURLSession* session;
     @property (strong, nonatomic) NSURLSession* endpoint;
    
     -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                 andCompletion:(void(^)(NSError*))completion;
     -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                 completion:(void(^)(NSString*, NSError*))completion;
     -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                 tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
    
     @end

6. 取代 `@implementation` 在 registerclient.m 中的下列程式碼區段。

     @implementation RegisterClient
    
     // Globals used by RegisterClient
     NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
    
     -(instancetype) initWithEndpoint:(NSString*)Endpoint
     {
         self = [super init];
         if (self) {
             NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
             _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
             _endpoint = Endpoint;
         }
         return self;
     }
    
     -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                 andCompletion:(void(^)(NSError*))completion
     {
         [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
     }
    
     -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                 andCompletion:(void(^)(NSError*))completion
     {
         NSSet* tagsSet = tags?tags:[[NSSet alloc] init];
    
         NSString *deviceTokenString = [[token description]
             stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
         deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                 uppercaseString];
    
         [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
             completion:^(NSString* registrationId, NSError *error) {
             NSLog(@"regId: %@", registrationId);
             if (error) {
                 completion(error);
                 return;
             }
    
             [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                 tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                 if (error) {
                     completion(error);
                     return;
                 }
    
                 NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                 if (httpResponse.statusCode == 200) {
                     completion(nil);
                 } else if (httpResponse.statusCode == 410 && retry) {
                     [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                 } else {
                     NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);
    
                     completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                 userInfo:nil]);
                 }
    
             }];
         }];
     }
    
     -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                 tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
     {
         NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                 @"Tags": [tags allObjects]};
         NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                             options:NSJSONWritingPrettyPrinted error:nil];
    
         NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                             encoding:NSUTF8StringEncoding]);
    
         NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                 registrationId];
         NSURL* requestURL = [NSURL URLWithString:endpoint];
         NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
         [request setHTTPMethod:@"PUT"];
         [request setHTTPBody:jsonData];
         NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                 self.authenticationHeader];
         [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
         [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    
         NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
             completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
         {
             if (!error)
             {
                 completion(response, error);
             }
             else
             {
                 NSLog(@"Error request: %@", error);
                 completion(nil, error);
             }
         }];
         [dataTask resume];
     }
    
     -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                 completion:(void(^)(NSString*, NSError*))completion
     {
         NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                     objectForKey:RegistrationIdLocalStorageKey];
    
         if (registrationId)
         {
             completion(registrationId, nil);
             return;
         }
    
         // request new one & save
         NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                 _endpoint, token]];
         NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
         [request setHTTPMethod:@"POST"];
         NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                 self.authenticationHeader];
         [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
    
         NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
             completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
         {
             NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
             if (!error && httpResponse.statusCode == 200)
             {
                 NSString* registrationId = [[NSString alloc] initWithData:data
                     encoding:NSUTF8StringEncoding];
    
                 // remove quotes
                 registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                     [registrationId length]-2)];
    
                 [[NSUserDefaults standardUserDefaults] setObject:registrationId
                     forKey:RegistrationIdLocalStorageKey];
                 [[NSUserDefaults standardUserDefaults] synchronize];
    
                 completion(registrationId, nil);
             }
             else
             {
                 NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                 if (error)
                     completion(nil, error);
                 else {
                     completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                 userInfo:nil]);
                 }
             }
         }];
         [dataTask resume];
     }
    
     @end

 上述程式碼會實作指引文章所述的邏輯 [從應用程式後端註冊](notification-hubs-registration-management.md#registration-management-from-a-backend) 方法使用執行 REST 呼叫應用程式後端，並使用 NSUserDefaults 來本機儲存 registrationId 傳回的通知中心。

 請注意，此類別需要設定 **authorizationHeader** 屬性，才能正常運作。 您可以在登入後，透過 **ViewController** 類別設定此屬性。

7. 在 ViewController.h 中，加入 `#import` RegisterClient.h 陳述式。 然後新增的裝置權杖的宣告，並參照 `RegisterClient` 執行個體中 `@interface` 區段:

     #import "RegisterClient.h"
    
     @property (strong, nonatomic) NSData* deviceToken;
     @property (strong, nonatomic) RegisterClient* registerClient;

8. 在 ViewController.m 中，將私用方法宣告中的加入 `@interface` 區段:

     @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
    
     // create the Authorization header to perform Basic authentication with your app back-end
     -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                     AndPassword:(NSString*)password;
    
     @end


> [AZURE.NOTE] 下列片段不是安全驗證結構描述，您應將 **createAndSetAuthenticationHeaderWithUsername:AndPassword:** 的實作替代成特定的驗證機制，使該機制產生註冊用戶端類別所利用的驗證權杖，例如 OAuth、Active Directory。

9. 接著在 `@implementation` ViewController.m 的區段中新增下列程式碼新增實作以設定裝置權杖與驗證標頭。

     -(void) setDeviceToken: (NSData*) deviceToken
     {
         _deviceToken = deviceToken;
         self.LogInButton.enabled = YES;
     }
    
     -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                     AndPassword:(NSString*)password;
     {
         NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
    
         NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
    
         self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                     encoding:NSUTF8StringEncoding];
     }
    
     -(BOOL)textFieldShouldReturn:(UITextField *)textField
     {
         [textField resignFirstResponder];
         return YES;
     }

 請留意設定裝置權杖如何啟用登入按鈕。 這是因為檢視控制器會向應用程式後端註冊推播通知 (作為登入動作的一部分)。 因此，在裝置權杖已正確設定之前，我們不希望有人能夠存取登入動作。 只要登入在推播註冊之前發生，您可能就會想要將前者與後者分開。

10. 在 ViewController.m 中，使用以下程式碼片段為您的 [**登入**] 按鈕實作動作方法，以及實作一個方法來使用 ASP.NET 後端傳送通知訊息。

    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;
    
        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
    
        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }
    
    
    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];
    
        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];
    
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
    
        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
    
        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];
    
        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }
    
            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }

11. 更新 [**傳送通知**] 按鈕的動作來使用 ASP.NET 後端，並傳送至由任何開關啟用的任何 PNS。

    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }
    
    
    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];
    
        [self.sendResults setText:@""];
    
        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];
    
        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];
    
        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }

11. 在 **ViewDidLoad** 函數中，新增下列內容以具現化 RegisterClient 執行個體，並設定文字欄位的委派。

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.RecipientField.delegate = self;
        self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];

12. 現在於 **AppDelegate.m** 中，移除 **application:didRegisterForPushNotificationWithDeviceToken:** 方法的所有內容，並使用下列內容取代它，確定檢視控制器包含從 APN 擷取的最新裝置權杖：

    // Add import to the top of the file
    #import "ViewController.h"
    
    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }

13. 最後，在 **AppDelegate.m** 中，確定您有下列方法：

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }


## 測試應用程式

1. 在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。

2. 在 iOS 應用程式 UI 中，輸入使用者名稱和密碼。 這些可以是任何字串，但兩個必須是相同的字串值。 然後按一下 [**登入**]。

    ![][2]

3. 您應該會看到註冊成功的快顯通知。 按一下 [確定]****。

    ![][3]

4. 在 ** 收件者使用者名稱標記* 文字欄位中，輸入從其他裝置註冊時搭配使用的使用者名稱標記。
5. 輸入通知訊息並按一下 [**傳送通知**]。 只有已經使用收件者使用者名稱標記註冊的裝置才會收到通知訊息。 通知訊息只會傳送給那些使用者。

    ![][4]



[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png 
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png 
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png 
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png 

