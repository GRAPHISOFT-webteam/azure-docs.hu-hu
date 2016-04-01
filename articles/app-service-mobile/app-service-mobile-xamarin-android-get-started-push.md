<properties
    pageTitle="使用 Azure App Service 將推播通知新增至 Xamarin.Android 應用程式"
    description="了解如何使用 Azure App Service 及 Azure 通知中樞將推播通知傳送到 Xamarin.Android 應用程式"
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/02/2015" 
    ms.author="glenga"/>

# 將推播通知新增至 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概觀

在本教學課程中，您可以新增推播通知給 [Xamarin.Android quickstart] 專案，所以每次插入記錄時，會傳送推播通知。 本教學課程根據 [Xamarin.Android quickstart] 教學課程，您必須先完成。 如果您不要使用下載的快速入門伺服器專案，必須將推播通知擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##必要條件

本教學課程需要下列各項：

+ 有效的 Google 帳戶。 您可以在 Google 帳戶註冊 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。
   
+ [Google Cloud Messaging 用戶端元件](http://components.xamarin.com/view/GCMClient/)。 您在教學課程中會新增此元件。

+ 完成 [Xamarin.Android quickstart] 教學課程。


##<a name="create-hub"></a>建立通知中樞

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a id="register"></a>啟用 Google Cloud Messaging

[AZURE.INCLUDE [Enable GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##設定行動應用程式後端以傳送推送要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-server"></a>更新伺服器專案以傳送推播通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>設定用於推播通知的用戶端專案

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>將推播通知程式碼新增至應用程式

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>在應用程式中測試推播通知

您可以在模擬器中使用虛擬裝置來測試應用程式。 在模擬器上執行時，您需要採取其他設定步驟。

1. 針對您要部署的目的地虛擬裝置或偵錯的所在虛擬裝置，請確認該裝置具有已設定為目標的 Google API，如以下 Android 虛擬裝置管理員 (AVD) 所示。 

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. 按一下加入 Android 裝置的 Google 帳戶 **應用程式** > **設定** > **將帳戶加入**, ，然後依照提示來使用現有的 Google 帳戶加入要建立一個新的裝置。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 按照先前的方法執行 todolist 應用程式，然後插入新的 todo 項目。 這次，通知圖示會顯示在通知區域中。 您可以開啟通知抽屜來檢視通知的完整內容。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quickstart]: app-service-mobile-xamarin-android-get-started.md

[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/


