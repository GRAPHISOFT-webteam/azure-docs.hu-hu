<properties
    pageTitle="註冊 Microsoft 驗證 | Microsoft Azure"
    description="了解如何在 Azure Mobile Services 應用程式中註冊 Microsoft 驗證。"
    authors="ggailey777"
    services="mobile-services"
    documentationCenter="Mobile"
    manager="dwrede"
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="glenga"/>


# 註冊應用程式來使用 Microsoft 帳戶進行驗證

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## 概觀

本主題說明如何註冊行動應用程式，以使用 Microsoft 帳戶做為 Azure 行動服務的身分驗證提供者。 以下步驟同時適用於使用 Live SDK 之服務導向的驗證以及用戶端導向的驗證。

## 前往 Windows 開發人員中心註冊 Windows 市集應用程式

務必先在 Windows 開發人員中心註冊 Windows 市集應用程式。
>[AZURE.NOTE]Windows Phone 8、Windows Phone 8.1 Silverlight 以及非 Windows 的應用程式可跳過本節。

1. 如果您尚未註冊您的應用程式，瀏覽至 [Windows 開發人員中心](https://dev.windows.com/dashboard/Application/New), ，使用您的 Microsoft 帳戶登入，輸入您的應用程式的名稱然後按一下 **保留應用程式名稱**。

3. 在 Visual Studio 開啟您的 Windows App 專案，然後在方案總管中以滑鼠右鍵按一下 Windows 市集 App 專案，按一下 [市集]**** > [將應用程式與市集建立關聯]****。

    ![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

5. 在精靈中，按一下 [登入]****，然後以您的 Microsoft 帳戶登入，選取您保留的應用程式名稱，按 [下一步]**** > [關聯]****。

6. (選擇性) 若為 Windows 8.1 通用 App，請針對 Windows Phone 市集專案重複執行步驟 4 與 5。

6. 回到新 App 的 Windows 開發人員中心頁面，按一下 [服務]**** > [推播通知]****。

7. 在 [推播通知]**** 頁面上，按一下 [Windows 推播通知服務 (WNS) 和 Microsoft Azure 行動服務]**** 下的 [線上服務網站]****。

您應用程式的 Microsoft 帳戶頁面會隨即顯示。 接下來，您會收到 Azure 需要搭配 App 使用 Microsoft 驗證的驗證認證。

## 設定您的 Microsoft 帳戶註冊，並連結至行動服務

本節的第一個步驟只適用於 Windows Phone 8、Windows Phone 8.1 Silverlight 以及非 Windows 市集應用程式。 若是上述應用程式，您也可以忽略封裝安全性識別碼 (SID)，這只有 Windows 市集應用程式可使用。

1. 對於非 Windows 市集應用程式中，瀏覽至 [我的應用程式](http://go.microsoft.com/fwlink/p/?LinkId=262039) (如有必要)，Microsoft 帳戶開發人員中心，使用您的 Microsoft 帳戶登入頁面上，按一下 **建立應用程式**, ，型別 **應用程式名稱**, ，然後按一下 [ **我接受**。

    這將透過 Microsoft 帳戶為您保留應用程式名稱，並顯示您應用程式的 Microsoft 帳戶頁面。

2. 在 App 的 Microsoft 帳戶頁面上，按一下 [API 設定]****，啟用 [行動或桌面用戶端應用程式]****，將行動服務 URL 設為 [目標網域]****，然後在 [重新導向 URL]**** 中提供下列 URL 格式之一，然後按一下 [儲存]****：

    + **.NET 後端**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
    + **JavaScript 後端**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`
     >[AZURE.NOTE]請確定針對您的行動服務後端類型使用正確的重新導向 URL 路徑格式。 若這不正確，驗證也將不會成功。  **根網域** 應該自動填寫。
&nbsp;

    ![Microsoft 帳戶 API 設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)

4. 按一下 [應用程式設定]****，記下 [用戶端識別碼]****、[用戶端密碼]**** 以及 [套件 SID]**** 中的值。

    ![Microsoft 帳戶應用程式設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] 用戶端密碼是重要的安全性認證。 請勿將用戶端密碼與任何人分享，或與您的應用程式一起散發。 只有註冊 Windows 市集應用程式才會顯示 [套件 SID] 欄位。

4. 在 [Azure 傳統入口網站]，按一下 [ **識別** 行動服務] 索引標籤上，輸入用戶端識別碼、 用戶端密碼以及套件 SID 是從您的身分識別提供者取得，然後按一下 [ **儲存**。
    >[AZURE.NOTE]若是 Windows Phone 8、Windows Phone Store 8.1 Silverlight 或非 Windows 應用程式，則不需提供套件 SID 值。

現在您的行動服務和應用程式都已設定成使用 Microsoft 帳戶。








[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[my applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[azure classic portal]: https://manage.windowsazure.com/ 

