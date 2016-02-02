<properties
    pageTitle="開始使用適用於 Android 應用程式的 Azure 行動服務 (JavaScript 後端)"
    description="遵循此教學課程，開始使用 Azure 行動服務進行 Android 開發 (JavaScript 後端)。"
    services="mobile-services"
    documentationCenter="android"
    authors="RickSaling"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="11/05/2015"
    ms.author="ricksal"/>


# 開始使用行動服務 android (JavaScript 後端)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程顯示如何使用 Azure 行動服務，將雲端型後端服務新增到 Android 應用程式。 在本教學課程中，您將建立新的行動服務和簡單的**待辦事項清單**應用程式，後者會在前者儲存應用程式資料。

> [AZURE.VIDEO mobile-get-started-android]

以下是完成應用程式的螢幕擷取畫面：

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## 必要條件

完成本教學課程，您需要 [Android Developer Tools](https://developer.android.com/sdk/index.html)), ，其中包括 Android Studio 整合式開發環境，以及最新版 Android 平台。 需要 Android 4.2 或以上的版本。

下載的快速入門專案包含 Azure Mobile Services SDK for Android。
> [AZURE.IMPORTANT] 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28)。


## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 Android 應用程式

當您建立自己的行動服務之後，就可以依照 Azure 傳統入口網站中簡單的快速入門，來建立新的應用程式或修改現有的應用程式，以便連線到您的行動服務。

在本節中，您將建立與行動服務連線的新 Android 應用程式。

1.  在 Azure 傳統入口網站中，按一下 [行動服務]****，然後按一下您剛建立的行動服務。

2. 在快速入門索引標籤中，按一下 [Choose platform]**** 下的 [Android]****，並展開 [Create a new Android app]****。

    ![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

    此處顯示建立與行動服務連線的 Android 應用程式的 3 個簡單步驟。

    ![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. 如果您尚未這樣做，請下載並安裝 [Android Developer Tools](https://go.microsoft.com/fwLink/p/?LinkID=280125) 在您本機電腦或虛擬機器。

4. 按一下 [Create TodoItem table]**** 以建立儲存應用程式資料的資料表。

5. 現在，按下 [下載]**** 按鈕來下載您的應用程式。

## 執行您的 Android 應用程式

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>後續步驟

請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* [開始使用資料]
  <br/>進一步了解儲存與查詢使用行動服務的資料。

* [開始使用驗證]
  <br/>了解如何驗證應用程式的身分識別提供者的使用者。

* [開始使用推播通知]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]




[get started (eclipse)]: mobile-services-android-get-started-ec.md 
[get started with data]: mobile-services-android-get-started-data.md 
[get started with authentication]: mobile-services-android-get-started-users.md 
[get started with push notifications]: mobile-services-javascript-backend-android-get-started-push.md 
[mobile services android sdk]: https://go.microsoft.com/fwLink/p/?LinkID=266533 

