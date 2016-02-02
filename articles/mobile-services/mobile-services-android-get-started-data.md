<properties
    pageTitle="開始使用資料 android (JavaScript 後端) |Microsoft Azure"
    description="了解如何開始使用行動服務使用 Android 應用程式 (JavaScript 後端) 中的資料。"
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
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="ricksal"/>


# 將行動服務新增至現有的 Android 應用程式(JavaScript 後端)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## 摘要

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本主題說明如何使用 Azure 行動服務，將持續性資料新增至 Android 應用程式。在本教學課程中，您將下載可在記憶體中儲存資料的應用程式、建立新的行動服務、整合此應用程式與行動服務，以便在 Azure 行動服務中 (而非在本機) 儲存和更新資料，然後使用 Azure 傳統入口網站來檢視執行應用程式所產生的資料變更。</p>
</div>
<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a><span class="time">15:32</span></div>
</div>


<p>
            本教學課程可協助您更詳細了解 Azure 行動服務從 Android 應用程式存放和擷取資料的方式。因此會逐步說明已在行動服務快速入門教學課程中完成的許多步驟。如果這是您第一次接觸行動服務，請考慮先完成教學課程 
            <a href="/en-us/develop/mobile/tutorials/get-started-android">開始使用行動服務</a>
            。
          </p>

## 必要條件

若要完成此教學課程，您需要下列項目：

- 一個 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure 免費試用</a>。

- [Azure 行動服務 Android SDK]
- <a  href="https://developer.android.com/sdk/index.html" target="_blank">Android Studio 整合式的開發環境</a>, ，其中包括 Android SDK 以及 Android 4.2 或更新版本。 下載的 GetStartedWithData 專案需要 Android 4.2 或更新版本。 不過，Mobile Services SDK 只需要 Android 2.2 或更新版本。

## 範例程式碼

若要檢視已完成的原始程式碼，請 <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio">這裡</a>。

## 下載 GetStartedWithData 專案

### 取得範例程式碼

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### 檢查和執行範例程式碼

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## 在 Azure 傳統入口網站中，建立新的行動服務。

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## 將新資料表新增至行動服務

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## 更新應用程式以使用行動服務進行資料存取

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## 對新的行動服務進行應用程式測試

現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 在 [執行]**** 功能表中，按一下 [執行應用程式]**** 來啟動專案。

    這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [加入]****。

    這會傳送新項目以插入至行動服務。

3. 在 [Azure 傳統入口網站]，按一下 [ **行動電話服務**, ，然後按一下您的行動服務。

4. 按一下 [資料]**** 索引標籤，然後按一下 [瀏覽]****。

    ![][9]

    請注意，**TodoItem** 資料表現在包含資料和行動服務產生的一些值，且資料行已自動加入至資料表，以符合應用程式中的 TodoItem 類別。

Android 的**開始使用資料**教學課程到此結束。

## 疑難排解

### 驗證 Android SDK 版本

[AZURE.INCLUDE [Verify SDK](../../includes/mobile-services-verify-android-sdk-version.md)]



## 後續步驟

本教學課程示範讓 Android 應用程式在行動服務中使用資料的基礎。

接下來，可考慮進入下列其中一個教學課程，這些教學課程都是以您在本教學課程中建立的 GetStartedWithData 應用程式為基礎：

* [驗證與修改資料的指令碼]
  <br/>進一步了解在行動服務中使用伺服器指令碼來驗證並變更從應用程式傳送的資料。

* [使用分頁縮小查詢]
  <br/>了解如何使用分頁，查詢中用來控制單一要求中所處理的資料量。

完成資料系列後，可試著閱讀這些另外的 Android 教學課程：

* [開始使用驗證]
    <br/>了解如何驗證應用程式的使用者。

* [開始使用推播通知]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式使用行動服務。





[download the android app project]: #download-app 
[create the mobile service]: #create-service 
[add a data table for storage]: #add-table 
[update the app to use mobile services]: #update-app 
[test the app against mobile services]: #test-app 
[next steps]: #next-steps 
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png 
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png 
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png 
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png 
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png 
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png 
[validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet 
[refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-android 
[get started with mobile services]: /develop/mobile/tutorials/get-started-android 
[get started with data]: /develop/mobile/tutorials/get-started-with-data-android 
[get started with authentication]: /develop/mobile/tutorials/get-started-with-users-android 
[get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-android 
[azure classic portal]: https://manage.windowsazure.com/ 
[azure mobile services android sdk]: http://aka.ms/Iajk6q 
[github]: http://go.microsoft.com/fwlink/p/?LinkID=282122 
[android sdk]: https://go.microsoft.com/fwLink/p/?LinkID=280125 

