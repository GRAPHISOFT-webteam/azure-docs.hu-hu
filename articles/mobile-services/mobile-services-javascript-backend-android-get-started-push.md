
<properties
    pageTitle="開始使用推播通知 (Android JavaScript) | Microsoft Azure"
    description="了解如何使用 Azure 行動服務傳送推播通知至 Android JavaScript 應用程式。"
    services="mobile-services, notification-hubs"
    documentationCenter="android"
    authors="RickSaling"
    writer="ricksal"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/06/2015"
    ms.author="ricksal"/>


# 將推播通知新增至行動服務 Android 應用程式

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 摘要

本主題說明如何使用 Azure 行動服務，透過 Google 雲端通訊 ("GCM") 將推播通知傳送至 Android 應用程式。 您會將推播通知新增至快速入門專案 (本教學課程的先決條件)。 使用您的行動服務中內含的 Azure 通知中樞來啟用推播通知。 完成後，行動服務就會在每次插入記錄時傳送推播通知。

## 先決條件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## 範例程式碼
若要查看已完成的原始程式碼，請移 [這裡](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPush)。

## 啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## 設定行動服務傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## 將推播通知新增至應用程式



下一個步驟是安裝 Google Play 服務。 Google Cloud Messaging 具有某些 API 的最低層級需求進行開發和測試方面 **minSdkVersion** 資訊清單中的屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考 [設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [Add Play Services](../../includes/mobile-services-add-google-play-services.md)]

###新增程式碼

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


## 在 Azure 傳統入口網站中更新已註冊的插入指令碼

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## 在應用程式中測試推播通知

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

###設定用於測試的 Android 模擬器

當您在模擬器中執行此應用程式時，請務必使用支援 Google API 的 Android 虛擬裝置 (AVD)。

1. 從工具列的右端，選取 [Android 虛擬裝置管理員]，選取您的裝置，按一下右邊的編輯圖示。

    ![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. 選取 **變更** 裝置描述行，在選取 **Google APIs**,  ，然後按一下 [確定]。

    ![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

    如此會使 AVD 變成使用 Google API。

###執行測試

1. 從 **執行** 功能表項目，按一下 [ **執行應用程式** 啟動應用程式。

2. 在應用程式中，輸入有意義的文字，例如 _新的行動服務工作_ 然後按一下 [ **新增** ] 按鈕。

    ![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. 從螢幕頂端向下撥動將裝置的通知中樞開啟，以檢視通知。


您已成功完成此教學課程。

## 疑難排解

### 驗證 Android SDK 版本

[AZURE.INCLUDE [Verify SDK](../../includes/mobile-services-verify-android-sdk-version.md)]

## 後續步驟

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
    <br/>了解使用者如何註冊和接收他們感興趣的推播通知的類別。

+ [Send template-based notifications to subscribers]
    <br/>了解如何使用範本從行動服務時，傳送推播通知，而不必在後端製作平台特定裝載。
-->

在下列主題中深入了解行動服務和通知中心：

* [開始使用驗證]
  <br/>了解如何使用行動服務以不同帳戶類型驗證應用程式的使用者。

* [What are Notification Hubs?]
  <br/>進一步了解通知中心將通知傳遞到您的應用程式跨所有主要用平台的運作方式。

* [偵錯通知中樞應用程式](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>取得指引疑難排解和偵錯通知中樞解決方案。

* [如何使用行動服務 Android 用戶端程式庫]
  <br/>深入了解如何搭配使用行動服務與 Android。

* [Mobile Services server script reference]
  <br/>進一步了解如何在您的行動服務中實作商務邏輯。


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure classic portal]: https://manage.windowsazure.com/
[How to use the Android client library for Mobile Services]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[What are Notification Hubs?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

