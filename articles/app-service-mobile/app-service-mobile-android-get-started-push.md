<properties
    pageTitle="新增推播通知至 Android 應用程式使用 Azure 行動應用程式"
    description="了解如何使用 Azure 行動應用程式將推播通知傳送至 Android 應用程式。"
    services="app-service\mobile"
    documentationCenter="android"
    manager="dwrede"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="12/02/2015"
    ms.author="yuaxu"/>

# 將推播通知新增至 Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀
在本教學課程中，您可以新增推播通知給 [Android quick start] 專案，所以每次插入記錄時，會傳送推播通知。 本教學課程根據 [Android quick start] 教學課程，您必須先完成。 如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。 

##必要條件

若要完成此教學課程，您需要下列項目：

* [Google 帳戶](http://go.microsoft.com/fwlink/p/?LinkId=268302) 已驗證的電子郵件地址。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)& mdash; 不需要使用 Node.js 後端專案。
* 完成 [快速入門教學課程](../app-service-mobile-android-get-started.md)。

##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## 啟用 Google 雲端通訊

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##設定行動應用程式後端以傳送推送要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="publish-the-service"></a>將行動後端發佈至 Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## 將推播通知新增至應用程式

###驗證 Android SDK 版本

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

下一個步驟是安裝 Google Play 服務。 Google Cloud Messaging 具有某些 API 的最低層級需求進行開發和測試方面 **minSdkVersion** 資訊清單中的屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考 [設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

###新增 Google Play 服務至專案

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

###新增程式碼

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## 對已發佈的行動服務進行應用程式測試

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

##<a id="more"></a>更多資訊

* 標記可讓您使用推播鎖定區隔的客戶。 [使用 Azure 行動應用程式的.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 示範如何將標記加入至 [裝置安裝。

<!-- URLs -->
[Android quick start]: app-service-mobile-android-get-started.md

