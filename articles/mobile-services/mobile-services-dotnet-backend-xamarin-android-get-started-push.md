<properties
    pageTitle="開始使用 Xamarin.Android 應用程式的行動服務 | Microsoft Azure"
    description="了解如何使用 Azure 行動服務及通知中心傳送推播通知至 Xamarin Android 應用程式"
    services="mobile-services"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="dwrede"
    editor="mollybos"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/11/2015"
    ms.author="glenga"/>

# 將推播通知新增至行動服務應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概觀

本主題說明如何使用 Azure 行動服務傳送推播通知至 Xamarin.Android 應用程式。 在本教學課程中，您新增使用 Google 雲端通訊 (GCM) 服務的推播通知 [Get started with Mobile Services] 專案。 完成後，行動服務就會在每次插入記錄時傳送推播通知。

本教學課程需要下列各項：

+ 有效的 Google 帳戶。
+ [Google Cloud Messaging Client Component]. 您在教學課程中會新增此元件。

您應該已經有 [Xamarin.Android] 和 [Azure 行動服務][Azure Mobile Services Component] 元件安裝在您的專案，當您完成從 [Get started with Mobile Services]。

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [Enable GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>設定行動服務以傳送推播要求

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>更新行動服務以傳送推播通知

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>設定用於推播通知的現有專案

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>將推播通知程式碼新增至應用程式

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>對已發佈的行動服務進行應用程式測試

您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

###<a id="local-testing"></a>啟用推播通知以進行本機測試

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/


