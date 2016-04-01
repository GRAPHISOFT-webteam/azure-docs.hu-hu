<properties
    pageTitle="Azure Mobile Engagement iOS SDK 概觀"
    description="Azure Mobile Engagement iOS SDK 的最新更新與程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="08/05/2015"
    ms.author="MehrdadMzfr" />

#Azure Mobile Engagement iOS SDK

從這裡開始取得有關如何在 iOS 應用程式中整合 Azure Mobile Engagement 的所有詳細資料。 如果您想要試試看第一次，請務必先完成我們 [15 分鐘教學課程](mobile-engagement-ios-get-started.md)。

按一下以查看 [SDK 內容](mobile-engagement-ios-sdk-content.md)

##整合程序
1. 從這裡開始 ︰ [如何在 iOS 應用程式中整合 Mobile Engagement](mobile-engagement-ios-integrate-engagement.md)

2. 通知 ︰ [如何在 iOS 應用程式中整合 Reach （通知）](mobile-engagement-ios-integrate-engagement-reach.md)

3. 標記計劃實作 ︰ [如何使用進階的 Mobile Engagement 標記 API 在 iOS 應用程式](mobile-engagement-ios-use-engagement-api.md)


##版本資訊

###3.2.1 (12/11/2015)

-   修正當新應用程式執行個體由具有深度連結的通知觸發時造成的延遲。 

如先前的版本，請參閱 [完成版本資訊](mobile-engagement-ios-release-notes.md)

##升級程序

如果您已經整合舊版 Engagement 到您的應用程式，在升級 SDK 時您必須考慮以下幾點。

您可能必須遵循幾個步驟，如果您錯過數個版本的 SDK，請參閱完整 [升級程序](mobile-engagement-ios-upgrade-procedure.md)。

針對每個新版 SDK，您必須先取代 (在 xcode 中移除並重新匯入) EngagementSDK 與 EngagementReach 資料夾。

###從 2.0.0 到 3.0.0
停止支援 iOS 4.X。 從此版本開始，您的應用程式部署目標必須至少為 iOS 6。

如果您在應用程式中使用 Reach，必須將`remote-notification` 值新增至 Info.plist 檔案中的 `UIBackgroundModes` 陣列，以接收遠端通知。

在您的應用程式委派中，方法 `application:didReceiveRemoteNotification:` 需由 `application:didReceiveRemoteNotification:fetchCompletionHandler:` 取代。

"AEPushDelegate.h" 是已被取代的介面，且您必須移除所有參考。 這包括從您的應用程式委派移除 `[[EngagementAgent shared] setPushDelegate:self]` 以及委派方法：

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;


