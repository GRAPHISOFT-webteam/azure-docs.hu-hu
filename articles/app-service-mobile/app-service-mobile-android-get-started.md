<properties
    pageTitle="在 Azure App Service Mobile Apps 上建立 Android 應用程式 | Microsoft Azure"
    description="請遵循此教學課程，以開始使用 Azure 行動應用程式後端進行 Android 開發"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#建立 Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 概觀

本教學課程將示範如何使用 Azure 行動應用程式後端，將雲端式後端服務加入 Android 行動應用程式。  您將建立新的行動裝置應用程式後端以及一個簡單 _待辦事項清單_ Android 應用程式在 Azure 中儲存應用程式資料。

對於在 Azure App Service 中使用 Mobile Apps 功能的所有其他 Android 教學課程，完成本教學課程是必要條件。

## 必要條件

若要完成此教學課程，您需要下列項目：

* [Android 開發人員工具](https://developer.android.com/sdk/index.html), ，其中包括 Android Studio 整合式開發環境，以及最新版 Android 平台。
* Azure Mobile Android SDK 會自動參考您下載的快速入門專案之 ss 部分。
* [Visual Studio Community 2013] 的 PC 或更新版本 (& s) 出來; 不需要 Node.js 後端。
*  [有效的 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## 建立新的 Azure 行動應用程式後端

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 設定伺服器專案

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## 下載並執行 Android 應用程式

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

