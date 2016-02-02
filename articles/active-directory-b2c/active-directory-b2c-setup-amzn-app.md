<properties
    pageTitle="Azure Active Directory B2C 預覽：Amazon 組態 | Microsoft Azure"
    description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 LinkedIn 帳戶的取用者提供註冊和登入"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="swkrish"/>


# Azure Active Directory B2C 預覽：針對具有 Amazon 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Amazon 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Amazon 做為身分識別提供者，您必須先建立 Amazon 應用程式，然後再對其提供正確參數。 您將需要 Amazon 帳戶來執行此動作;如果您沒有帳戶，您可以取得在 [http://www.amazon.com/](http://www.amazon.com/)。

1. 移至 [Amazon 開發人員中心](https://login.amazon.com/) 並以您的 Amazon 帳戶認證登入。
2. 若您尚未執行此動作，請按一下 [註冊]****，遵循開發人員註冊步驟並接受原則。
3. 按一下 [註冊新應用程式]****。

    ![Amazon - 新的應用程式](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. 提供應用程式資訊 (**名稱**、**說明**和**隱私權注意事項 URL**)，然後按一下 [儲存]****。

    ![Amazon - 註冊應用程式](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. 在 [Web 設定]**** 區段中，複製 [用戶端識別碼]**** 和 [用戶端密碼]**** 的值 (您必須按一下 [顯示密碼]**** 按鈕才會顯示該值)。 您必須使用這兩個值，將 Amazon 設為目錄中的身分識別提供者。 按一下位於區段底部的 [編輯]****。
    > [AZURE.NOTE]
    **用戶端密碼**是重要的安全性認證。

    ![Amazon - 用戶端密碼](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. 輸入 [https://login.microsoftonline.com](https://login.microsoftonline.com) 中 **允許 JavaScript 來源** 欄位和 [https://login.microsoftonline.com/te/ {目錄} / oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) 中 **允許傳回 Url** ] 欄位中，其中 **{目錄}** 是要取代為您的目錄名稱 (例如 contoso.onmicrosoft.com)。 按一下 [儲存]****。
    > [AZURE.NOTE]
    **{directory}** 值區分大小寫。

    ![Amazon - URL](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## 將 Amazon 設為您目錄中的身分識別提供者

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能刀鋒視窗中，按一下 [**身分識別提供者**]。
3. 按一下刀鋒視窗頂端的 [新增]****。
4. 針對身分識別提供者組態，提供容易辨識的**名稱**。 例如，輸入 "Amzn"。
5. 按一下 [身分識別提供者類型]****、選取 [Amazon]****，然後按一下 [確定]****。
6. 按一下 [設定此身分識別提供者]****，然後輸入您先前建立之 Amazon 應用程式的 [用戶端識別碼]**** 與 [用戶端密碼]****。
7. 依序按一下 [確定]****、[建立]****，以儲存您的 Amazon 組態。





