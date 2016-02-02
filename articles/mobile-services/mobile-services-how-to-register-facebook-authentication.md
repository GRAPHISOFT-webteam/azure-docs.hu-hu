<properties
    pageTitle="註冊 Facebook 驗證 | Azure 行動服務"
    description="了解如何在 Azure 行動服務應用程式中使用 Facebook 驗證。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/15/2015"
    ms.author="glenga"/>


# 在行動服務中註冊應用程式以採用 Facebook 驗證

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Facebook 驗證。
>[AZURE.NOTE] 本教學課程是有關 [Azure 行動服務]，此方案可協助您建置用於任何平台的可擴充行動應用程式。 行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。 此頁面支援 [開始使用驗證](mobile-services-ios-get-started-users.md) 教學課程，說明如何將使用者登入您的應用程式。 如果這是您第一次接觸行動服務，請完成本教學課程 [開始使用行動服務](mobile-services-ios-get-started.md)。

若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請移至 [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285)。

1. 瀏覽至 [Facebook 開發人員](http://go.microsoft.com/fwlink/p/?LinkId=268285) 網站，並使用您的 Facebook 登入帳戶的認證。

2. (選用) 按一下 [我的應用程式]****，然後按一下 [註冊為開發人員]****，接受政策並遵循註冊步驟 (若您尚未註冊)。

3. 按一下 [我的應用程式]**** > [加入新的應用程式]**** > [進階設定]****。

4. 輸入應用程式的唯一 [顯示名稱]****，選擇 [類別]**** 下的 [頁面應用程式]****，然後按一下 [建立應用程式識別碼]**** 並完成安全性練習。

    這會建立新的 Facebook 應用程式識別碼。

5. 按一下 [設定]****，在 [應用程式網域]**** 中輸入您行動服務的網域，輸入選用 [連絡人電子郵件]****，然後按一下 [新增平台]**** 並選取 [網站]****。

    ![][3]

6. 在 [網站 URL]**** 中輸入您行動服務的 URL，然後按一下 [儲存變更]****。

7. 按一下 [顯示]****，提供您的密碼 (如有要求)，然後記下 [應用程式 ID]**** 和 [應用程式密鑰]**** 的值。

    ![][5]
    &nbsp;
    >[AZURE.IMPORTANT] 應用程式密鑰是重要的安全性認證。 請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。
    &nbsp;

8. 按一下 [進階]**** 索引標籤，在 [有效的 OAuth 重新導向 URl]**** 中輸入下列其中一個 URL，然後按一下 [儲存變更]****：

    + **.NET 後端**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
    + **JavaScript 後端**: `https://<mobile_service>.azure-mobile.net/login/facebook`
     >[AZURE.NOTE]請確定針對您的行動服務後端類型使用正確的重新導向 URL 路徑格式。 若格式不正確，驗證將不會成功。

9. 按一下 [狀態與檢閱]**** > [是]****，以啟用應用程式的一般公用存取權。

    您用來註冊新應用程式的 Facebook 帳戶是應用程式的管理員，具有該應用程式的管理員存取權。 此步驟會授與一般公用存取權，讓應用程式能夠使用其他 Facebook 帳戶進行驗證。


現在您已準備好提供應用程式 ID 和應用程式密碼值給行動服務，以在您的應用程式中採用 Facebook 登入驗證。






[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png 
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png 
[facebook developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286 
[get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/ 
[azure mobile services]: http://azure.microsoft.com/services/mobile-services/ 

