<properties
    pageTitle="將行動服務新增至現有的通用 Windows 市集應用程式 | Microsoft Azure"
    description="了解如何開始使用行動服務在 Windows Store 應用程式中使用資料。"
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="glenga"/>

# 將行動服務新增至現有的應用程式

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##概觀

本主題將說明如何以 Azure 行動服務作為 Windows 市集應用程式的後端資料來源。 在本教學課程中，您將下載 Visual Studio 2013 專案，以取得在記憶體中儲存資料的應用程式、建立新的行動服務、整合行動服務與該應用程式，然後檢視執行應用程式時所做的資料變更。

您在此教學課程中建立的行動服務，是一項 .NET 後端行動服務。 .NET 後端服務讓您得以在行動服務中運用 .NET 語言與 Visual Studio 建立伺服器端商業邏輯，並在本機電腦上執行與偵錯行動服務。 若要建立可讓您以 JavaScript 撰寫伺服器端商務邏輯的行動服務，請參閱本主題的 JavaScript 後端版本。

>[AZURE.NOTE]本主題說明如何使用 Visual Studio Professional 2013 with Update 3 的工具，新的行動服務連接到通用 Windows 應用程式。 要將行動服務連接到 Windows 市集或 Windows Phone 市集 8.1 應用程式時，也可使用相同的步驟進行。 若要將行動服務連接至 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 應用程式，請參閱 [開始使用資料適用於 Windows Phone](mobile-services-dotnet-backend-windows-phone-get-started-data.md)。

> 如果您無法升級至 Visual Studio Professional 2013 Update 3 或是您偏好手動將您的行動服務專案新增至 Windows 市集應用程式解決方案，請參閱 [這一版](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) 的主題。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 或更新版本)。

##下載 GetStartedWithData 專案

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##從 Visual Studio 建立新的行動服務

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. 在 [方案總管] 中，[getstartedwithdata.shared] 專案資料夾中，開啟 App.xaml.cs 程式碼檔案，並注意已加入的全新靜態欄位 **應用程式** 類別在 Windows 市集應用程式條件式編譯區塊，如下列範例所示 ︰

    public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient
        todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


（& s) nbsp; & nbsp;此程式碼提供存取您的應用程式中新的行動服務所使用的執行個體 [MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030) 類別。 用戶端是利用提供新行動服務的 URI 和應用程式金鑰來建立。 應用程式中的所有頁面都可以使用此靜態資料行。

&nbsp;&nbsp;8. 以滑鼠右鍵按一下 Windows Phone 應用程式專案中，按一下 **新增**, ，按一下 [ **已連接服務]**, ，選取的行動服務，您剛建立，然後按一下 **確定**。 相同的程式碼會新增到共用 App.xaml.cs 檔案，但是這次是新增到 Windows Phone 應用程式條件式編譯區塊。

這時，Windows 市集與 Windows Phone 市集應用程式都會同時連接到新的行動服務。 下個步驟是測試新的行動服務專案。


##在本機測試行動服務專案

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##更新應用程式以使用行動服務

在本節中，您將會更新通用 Windows 應用程式，以使用行動服務作為應用程式的後端服務。 您只需要變更 [GetStartedWithData.Shared] 專案資料夾的 MainPage.cs 專案檔案。

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##將行動服務發佈至 Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##測試 Azure 代管的行動服務

現在我們可以將兩個通用的 Windows 應用程式同時與 Azure 代管的行動服務一起測試。

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##檢視儲存在 SQL Database 裡的資料

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]

本教學課程到此結束。

##後續步驟

本教學課程示範讓通用 Windows 應用程式專案能夠在行動服務中處理資料的基本概念。 接著，請考慮閱讀下列其他主題：

* [Get started with authentication]
  <br/>了解如何驗證應用程式的使用者。

* [Get started with push notifications]
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務 C# 做法概念性參考](mobile-services-windows-dotnet-how-to-use-client-library.md)
  <br/>深入了解如何搭配使用行動服務與 .NET。


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030


