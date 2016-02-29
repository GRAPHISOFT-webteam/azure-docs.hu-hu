<properties
    pageTitle="開始使用 PhoneGap/cordova App 的 Azure 行動服務 | Microsoft Azure"
    description="遵循此教學課程，可開始使用 Azure 行動服務進行 iOS、Android 和 Windows Phone 的 PhoneGap 開發。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="multiple"
    ms.topic="get-started-article" 
    ms.date="11/06/2015"
    ms.author="ggailey777"/>

# 開始使用行動服務

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

本教學課程說明如何使用 Azure 行動服務，將雲端型後端服務新增至應用程式。 在本教學課程中，您將建立新的行動服務，並建立可在新的行動服務中儲存應用程式資料的簡單_待辦事項清單_應用程式。

以下是完成應用程式的螢幕擷取畫面：

![][3]

### 其他需求

完成本教學課程需要下列各項：

+ PhoneGap 工具 (Windows Phone 8 專案需要 v3.2 以上版本)。

+ 使用中的 Microsoft Azure 帳戶。

+ PhoneGap 支援多個平台的開發。 除了 PhoneGap 工具本身以外，您還必須為所要使用的各個平台安裝下列工具：

    - Windows Phone: 安裝 [Visual Studio 2012 Express for Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
    - iOS: 安裝 [Xcode] (必須是 4.4 以上)
    - Android: 安裝 [Android Developer Tools] [Android SDK]
        <br/>(Mobile Services SDK for Android 支援適用於 Android 2.2 或更新版本的應用程式。 若要執行快速入門應用程式，則必須使用 Android 4.2 或更新版本。)

## 建立新的行動服務

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 建立新的 PhoneGap 應用程式

在本節中，您將建立連接到行動服務的新 PhoneGap 應用程式。

1.  在 [Azure 傳統入口網站]，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 在 [快速入門] 索引標籤中，按一下 [ **PhoneGap** 下 **選擇平台** 展開 **建立新的 PhoneGap 應用程式**。

    ![][0]

    This displays the three easy steps to create a PhoneGap app connected to your mobile service.

    ![][1]

3. 如果您尚未下載並安裝 PhoneGap 和至少其中一項平台開發工具 (Windows Phone、iOS 或 Android)，請於此時執行。

4. 按一下 [ **Create TodoItem table** 來建立儲存應用程式資料的資料表。

5. 在 **下載並執行您的應用程式**, ，按一下 [ **下載**。

    這會下載範例專案 _待辦事項清單_ 連線到您的行動服務，以及行動服務 JavaScript SDK 的應用程式。 請將壓縮的專案檔案儲存至本機電腦，並記下儲存位置。

## 執行新的 PhoneGap 應用程式

本教學課程的最後階段是建立並執行新的應用程式。

1.  瀏覽至您儲存此壓縮專案檔案的位置，並在您的電腦上展開檔案。

2.  根據以下各個平台的指示，開啟並執行對應的專案。

    + **Windows Phone 8**

        1. Windows Phone 8: 開啟中的.sln 檔案 **platforms\wp8** 在 Visual Studio 2012 Express for Windows Phone 的資料夾。

        2. 按下 **F5** 鍵，以重建專案並啟動應用程式。

        ![][2]

    + **iOS**

        1. 在中開啟專案 **平台/ios** 在 Xcode 中的資料夾。

        2. 按下 **執行** ] 按鈕以建置專案並啟動應用程式在 iPhone 模擬器中，這是此專案的預設值。

        ![][3]

    + **Android**

        1. 在 Eclipse 中，按一下 [ **檔案** 然後 **匯入**, ，依序展開 **Android**, ，按一下 [ **現有的 Android 程式碼至工作區**, ，然後按一下 [ **下一步。**

        2. 按一下 [ **瀏覽**, ，瀏覽到展開的專案檔案的位置，按一下 **確定**, ，確定已勾選 TodoActivity 專案，然後按一下 [ **完成**。 <p>這會將專案檔案匯入目前的工作區中。</p>

        3. 從 **執行** ] 功能表上，按一下 [ **執行** Android 模擬器中啟動專案。

            ![][4]

        >[AZURE.NOTE]若要能夠在 Android 模擬器中執行專案，您必須定義至少一個 Android 虛擬裝置 (AVD)。 請使用 AVD 管理員來建立和管理這些裝置。


3. 之後啟動前述其中一個行動模擬器中的應用程式，請在文字方塊中鍵入一些文字，然後按一下 [ **新增**。

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料插入至 **TodoItem** 資料表。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

    > [AZURE.IMPORTANT] 如果使用 PhoneGap 工具重新建置主要專案，就會覆寫此平台專案的變更。 因此，請依照下一節中的說明，在專案的根 www 目錄中進行變更。

4. 回到 [Azure 傳統入口網站]，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## 為各個平台進行應用程式更新及重新建置專案

1. 變更 ´www´ 目錄中的程式碼檔案，在此案例中為 ´todolist/www´。

2. 驗證所有的目標平台工具皆可從系統路徑存取。

2. 在根專案目錄中開啟命令提示字元，然後執行下列其中一個平台特定命令：

    + **Windows Phone**

        從 Visual Studio 開發人員命令提示字元執行下列命令：

            phonegap local build wp8

    + **iOS**

        開啟終端機，然後執行下列命令：

            phonegap local build ios

    + **Android**

        開啟命令提示字元或終端機視窗，然後執行下列命令：

            phonegap local build android

4. 根據下一節中的說明，在適當的開發環境中開啟每個專案。

>[AZURE.NOTE]您可以檢閱存取行動服務來查詢和插入資料，在 js/index.js 檔案中找到的程式碼。

## 後續步驟
請注意，您已完成快速入門，並了解如何執行行動服務中的其他重要工作：

* **[將驗證新增至您的應用程式]**  
  了解如何透過身分識別提供者來驗證您的應用程式使用者。  

* **[將推播通知新增至您的應用程式](https://msdn.microsoft.com/magazine/dn879353.aspx)**  
  了解如何註冊及傳送推播通知至應用程式。

* **[行動服務 HTML/JavaScript 作法概念性參考](mobile-services-html-how-to-use-client-library.md)**  
  進一步了解如何使用 JavaScript 用戶端程式庫來存取資料、 呼叫自訂 Api，以及執行驗證。

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Add authentication to your app]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Azure classic portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 
