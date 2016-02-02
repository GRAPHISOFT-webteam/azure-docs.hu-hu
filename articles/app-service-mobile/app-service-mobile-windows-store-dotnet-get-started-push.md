<properties 
    pageTitle="新增推播通知至 Windows 執行階段 8.1 通用 app | Azure 行動應用程式" 
    description="了解如何使用 Azure App Service 行動應用程式與 Azure 通知中樞傳送推播通知至 Windows 應用程式。" 
    services="app-service\mobile,notification-hubs" 
    documentationCenter="windows" 
    authors="ggailey777" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/25/2015" 
    ms.author="glenga"/>


# 新增推播通知至 Windows 執行階段 8.1 通用 app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本主題將示範如何使用 Azure App Service 行動應用程式與 Azure 通知中樞將推播通知傳送至 Windows 執行階段 8.1 通用 app。 在此案例中，加入新項目時，您的行動應用程式後端會傳送通知至所有註冊 Windows 通知服務 (WNS) 的 Windows 應用程式

本教學課程以應用程式服務行動應用程式快速入門為基礎。 在開始本教學課程之前，您必須先完成快速入門教學課程 [建立 Windows 應用程式](../app-service-mobile-windows-store-dotnet-get-started.md)。 如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## 必要條件

若要完成此教學課程，您需要下列項目：

* 使用中 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* 完成 [快速入門教學課程](../app-service-mobile-windows-store-dotnet-get-started.md)。


## <a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## 針對推播通知註冊應用程式

在您能從 Azure 傳送推播通知到您的 Windows 應用程式之前，您必須將應用程式提交至 Windows 市集。 接著您可以設定您的伺服器專案與 WNS 整合。

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


## 設定後端來傳送推播通知

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


## <a id="update-service"></a>更新伺服器以傳送推播通知

現在應用程式已經啟用了推播通知，您必須更新應用程式後端以傳送推播通知。 使用下列程序符合您的後端專案類型與出來，兩者皆 [.NET 後端](#dotnet) 或 [Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [管理 NuGet 封裝]****，搜尋 Microsoft.Azure.NotificationHubs，然後按一下 [安裝]****。 這會安裝通知中樞用戶端程式庫。

2. 展開 [Controllers]****，開啟 [TodoItemController.cs]，然後新增下列 using 陳述式：

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. 在 **PostTodoItem** 方法中，於呼叫 **InsertAsync** 之後新增下列程式碼：

     // Get the settings for the server project.
     HttpConfiguration config = this.Configuration;
     MobileAppSettingsDictionary settings =
         this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
    
     // Get the Notification Hubs credentials for the Mobile App.
     string notificationHubName = settings.NotificationHubName;
     string notificationHubConnection = settings
         .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
    
     // Create the notification hub client.
     NotificationHubClient hub = NotificationHubClient
         .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
    
     // Define a WNS payload
     var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                             + item.Text + @"</text></binding></visual></toast>";
     try
     {
         // Send the push notification.
         var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
    
         // Write the success result to the logs.
         config.Services.GetTraceWriter().Info(result.State.ToString());
     }
     catch (System.Exception ex)
     {
         // Write the failure result to the logs.
         config.Services.GetTraceWriter()
             .Error(ex.Message, null, "Push.SendAsync Error");
     }

 此程式碼會告訴通知中樞在插入新項目之後傳送推播通知。

### <a name="nodejs"></a>Node.js 後端專案

1. 如果您還沒有這麼做， [下載快速入門專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) 或其他使用 [Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。

2. 在 todoitem.js 檔案中，以下列程式碼取代現有的程式碼：

     var azureMobileApps = require('azure-mobile-apps'),
     promises = require('azure-mobile-apps/src/utilities/promises'),
     logger = require('azure-mobile-apps/src/logger');
    
     var table = azureMobileApps.table();
    
     table.insert(function (context) {
     // For more information about the Notification Hubs JavaScript SDK,  
     // see http://aka.ms/nodejshubs
     logger.info('Running TodoItem.insert');
    
     // Define the WNS payload that contains the new item Text.
     var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                 + context.item.text + "</text></binding></visual></toast>";
    
     // Execute the insert.  The insert returns the results as a Promise,
     // Do the push as a post-execute action within the promise flow.
     return context.execute()
         .then(function (results) {
             // Only do the push if configured
             if (context.push) {
                 // Send a WNS native toast notification.
                 context.push.wns.sendToast(null, payload, function (error) {
                     if (error) {
                         logger.error('Error while sending push notification: ', error);
                     } else {
                         logger.info('Push notification sent successfully!');
                     }
                 });
             }
             // Don't forget to return the results from the context.execute()
             return results;
         })
         .catch(function (error) {
             logger.error('Error while running context.execute: ', error);
         });
     });
    
     module.exports = table;  

 插入新的 todo 項目時，這會傳送包含 item.text 的 WNS 快顯通知。

2. 在本機電腦中編輯檔案時，重新發布伺服器專案。

## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a id="update-service"></a>將推播通知新增至您的應用程式

1. 開啟共用 **App.xaml.cs** 專案檔案，並新增下列 `使用` 陳述式:

        using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. 在相同檔案中，將下列 **InitNotificationsAsync** 方法定義新增至 [**應用程式**] 類別：

     private async Task InitNotificationsAsync()
     {
         // Get a channel URI from WNS.
         var channel = await PushNotificationChannelManager
             .CreatePushNotificationChannelForApplicationAsync();
    
         // Register the channel URI with Notification Hubs.
         await App.MobileService.GetPush().RegisterAsync(channel.Uri);
     }

 此程式碼會從 WNS 中擷取應用程式的 ChannelURI，然後向您的應用程式服務行動應用程式註冊該 ChannelURI。

3. 在 **App.xaml.cs** 中 **OnLaunched** 事件處理常式的頂端，將 **async** 修飾詞新增到方法定義中，並將下列呼叫新增到新的 **InitNotificationsAsync** 方法中，如下列範例所示：

     protected async override void OnLaunched(LaunchActivatedEventArgs e)
     {
         await InitNotificationsAsync();
    
         // ...
     }

 這樣可保證在每次啟動應用程式時都會註冊存留期較短的 ChannelURI。

4. 在 [方案總管] 中，按兩下 Windows 市集應用程式的 **Package.appxmanifest**，並將 [**通知**] 中的 [**支援快顯通知**] 設為 [**是**]：

    從 [檔案]**** 功能表中，按一下 [全部儲存]****。

5. 針對 Windows Phone 市集應用程式專案重複上一個步驟。

您的應用程式現在已能夠接收快顯通知。

## <a id="test"></a>在您的應用程式中測試推播通知

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


## <a id="more"></a>更

* 範本可讓您彈性地傳送跨平台推播和當地語系化推播。 [如何使用 Azure 行動應用程式的受管理的用戶端](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) 說明如何註冊的範本。
* 標記可讓您使用推播鎖定區隔的客戶。 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) 示範如何將標記加入至 [裝置安裝。








[azure portal]: https://portal.azure.com/ 

