<properties
   pageTitle="教學課程：整合 Salesforce 與 Azure Active Directory | Microsoft Azure"
   description="了解如何使用 Salesforce 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="TerryLanfear"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="liviodlc"/>

#教學課程：如何整合 Salesforce 與 Azure Active Directory

本教學課程將示範如何將 Salesforce 環境與 Azure Active Directory 連接。 您將學習如何設定單一登入 Salesforce、如何啟用自動的使用者佈建，以及如何指派使用者存取 Salesforce。

##先決條件

1. 若要存取 Azure Active Directory 透過 [Azure 管理入口網站](https://manage.windowsazure.com), ，您必須先具備有效的 Azure 訂用帳戶。

2. 您必須擁有有效的租用戶 [Salesforce.com](https://www.salesforce.com/)。

> [AZURE.IMPORTANT] 如果您使用 Salesforce.com **試用** 帳戶，您將無法設定自動化的使用者佈建。 試用帳戶沒有必要的 API 存取權，必須購買之後才會擁有。
> 
> 您可以使用克服這項限制 [免費的開發人員帳戶](https://developer.salesforce.com/signup) 完成本教學課程。

如果您使用 Salesforce 沙箱環境，請參閱 [Salesforce 沙箱整合教學課程](https://go.microsoft.com/fwLink/?LinkID=521879)。

##影片教學課程

您可以依照此教學課程使用下列影片。

**影片教學課程第一部：如何啟用單一登入**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**影片教學課程第二部：如何自動化使用者佈建**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##步驟 1：將 Salesforce 加入您的目錄

1. 在 [Azure 管理入口網站](https://manage.windowsazure.com), ，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![從左方的導覽窗格中選取 [Active Directory]。][0]

2. 從 **目錄** 清單中，選取您想要將 Salesforce 加入的目錄。

3. 按一下 [ **應用程式** 上方功能表中。

    ![按一下 [應用程式]。][1]

4. 按一下 [ **新增** 頁面的底部。

    ![按一下 [新增] 以加入新的應用程式。][2]

5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![按一下 [從資源庫新增應用程式]。][3]

6. 在 **搜尋方塊**, ，型別 **Salesforce**。 然後選取 **Salesforce** 從結果中，然後按一下 **完成** 加入應用程式。

    ![新增 [Salesforce]。][4]

7. 您現在應該會看到 Salesforce 的 [快速入門] 頁面：

    ![Azure AD 中 Salesforce 的 [快速入門] 頁面][5]

##步驟 2：啟用單一登入

1. 在設定單一登入之前，您必須先為您的 Salesforce 環境設定並部署自訂網域。 如需如何進行這項指示，請參閱 [設定網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US)。

2. 在 Salesforce 的快速入門] 頁面在 Azure AD 中，按一下 [ **設定單一登入** ] 按鈕。

    ![[設定單一登入] 按鈕][6]

3. 此時會開啟一個對話方塊，您會看到一個畫面，詢問「您要讓使用者如何登入 Salesforce?」 選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![選取 [Azure AD 單一登入]][7]

    > [AZURE.NOTE] 若要深入了解有關不同單一登入的選項， [按一下這裡](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

4. 在 **設定應用程式設定** 頁面上，填寫 **登入 URL** 只要您使用下列格式的 Salesforce 網域 url:
 - 企業帳戶：`https://<domain>.my.salesforce.com`
 - 開發人員帳戶：`https://<domain>-dev-ed.my.salesforce.com` 

    ![輸入您的 [登入 URL]][8]

5. 在 **在 Saleforce 設定單一登入** 頁面上，按一下 **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![下載憑證][9]

6. 在瀏覽器中開啟新索引標籤，登入您的 Salesforce 系統管理員帳戶。

7. 在 **管理員** 瀏覽窗格中，按一下 [ **安全性控制** 展開相關的區段。 然後按一下 [ **單一登入設定**。

    ![在 [安全性控制] 之下按一下 [單一登入設定]][10]

8. 在 **單一登入設定** 頁面上，按一下 **編輯** ] 按鈕。

    ![按一下 [編輯] 按鈕][11]

    > [AZURE.NOTE] 如果您無法啟用單一登入您的 Salesforce 帳戶的設定，您可能需要連絡 Salesforce 的支援人員為您啟用的功能。

9. 選取 **SAML 啟用**, ，然後按一下 [ **儲存**。

    ![選取 [啟用 SAML]][12]

10. 若要設定 SAML 單一登入設定，請按一下 [ **新增**。

    ![選取 [啟用 SAML]][13]

11. 在 **SAML 單一登入設定編輯** 頁面上，進行下列設定:

    ![您應進行的組態的螢幕擷取畫面][14]

 - 如 **名稱** 欄位中，輸入易記的名稱，此組態。 提供值 **名稱** 自動填入 **API 名稱** 文字方塊。

 - 在 Azure AD 中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者** 在 Salesforce 中的欄位。

 - 在 **實體識別碼文字方塊**, ，輸入您的 Salesforce 網域名稱使用以下模式:
     - 企業帳戶：`https://<domain>.my.salesforce.com`
     - 開發人員帳戶：`https://<domain>-dev-ed.my.salesforce.com`

 - 按一下 [ **瀏覽** 或 **選擇檔案** 開啟 **選擇要上傳的檔案** ] 對話方塊中，選取您的 Salesforce 憑證，然後按一下 **開啟** 以上傳憑證。

 - 如 **SAML 身分識別類型**, ，請選取 **判斷提示包含使用者的 salesforce.com 使用者名稱**。

 - 如 **SAML 身分識別位置**, ，請選取 **身分識別位於 Subject 陳述式的 NameIdentifier 元素中**

 - 在 Azure AD 中，複製 **遠端登入 URL** 值，並接著將它貼入 **身分識別提供者登入 URL** 在 Salesforce 中的欄位。

 - 如 **服務提供者起始的要求繫結**, ，請選取 **HTTP 重新導向**。

 - 最後，按一下 [ **儲存** 套用 SAML 單一登入設定。

12. 在 Salesforce 中的左瀏覽窗格，按一下 [ **定義域管理** 以展開相關的區段，然後按一下 [ **我網域**。

    ![按一下 [我的網域]][15]

13. 向下捲動至 **驗證設定** 區段，然後按一下 **編輯** ] 按鈕。

    ![按一下 [編輯] 按鈕][16]

14. 在 **驗證服務** 區段中，選取您的 SAML SSO 組態的易記名稱，然後按一下 **儲存**。

    ![選取您的 SSO 組態][17]

    > [AZURE.NOTE] 如果選取一個以上的驗證服務，然後當使用者嘗試啟動單一登入您的 Salesforce 環境，它們將會提示您選取要用來登入的驗證服務。 如果您不想要這種情形，則您應該 **核取所有其他驗證服務**。

15. 在 Azure AD 中，選取單一登入組態確認核取方塊以啟用您上傳到 Salesforce 的憑證。 然後按一下 [ **下一步**。

    ![核取確認核取方塊][18]

16. 在對話方塊的最後一頁，如果您想要收到電子郵件通知與此單一登入組態相關的錯誤和警告，請輸入電子郵件地址。 

    ![輸入您的電子郵件地址。][19]

17. 按一下 [ **完成** 以關閉對話方塊。 若要測試您的組態，請參閱下面的一節 [指派使用者至 Salesforce](#step-4-assign-users-to-salesforce)。

##步驟 3：啟用自動的使用者佈建

1. 在 salesforce 的 Azure AD 快速入門] 頁面上，按一下 **設定使用者佈建** ] 按鈕。

    ![按一下 [設定使用者佈建] 按鈕][20]

2. 在 **設定使用者佈建** 對話方塊中，輸入您的 Salesforce 系統管理員使用者名稱及密碼。

    ![輸入您的系統管理員使用者名稱或密碼][21]

    > [AZURE.NOTE] 如果您要設定生產環境，最佳作法是特別針對此步驟在 Salesforce 中建立新的系統管理員帳戶。 此帳戶必須具有 **系統管理員** 在 Salesforce 中指派給它的設定檔。

3. 若要取得您的 Salesforce 安全性權杖，請開啟新索引標籤並登入相同的 Salesforce 系統管理員帳戶。 在頁面右上角，按一下您的名稱，然後按一下 **我的設定**。

    ![按一下您的名稱，然後按一下 [我的設定]][22]

4. 在左的導覽窗格中，按一下 **個人** 以展開相關的區段，然後按一下 [ **重設我的安全性權杖**。

    ![按一下您的名稱，然後按一下 [我的設定]][23]

5. 在 **重設我的安全性權杖** 頁面上，按一下 **重設安全性權杖** ] 按鈕。

    ![閱讀警告。][24]

6. 檢查與此系統管理員帳戶相關聯的電子郵件收件匣。 尋找來自 Salesforce.com，包含新安全性權杖的電子郵件。

7. 複製權杖，移至您 Azure AD 的視窗，並將它貼到 **使用者安全性語彙基元** 欄位。 然後按一下 [ **下一步**。

    ![貼上安全性權杖][25]

8. 在確認頁面上，您可以選擇在發生佈建失敗時接收電子郵件通知。 按一下 [ **完成** 以關閉對話方塊。

    ![輸入您的電子郵件地址以接收通知][26]

##步驟 4：指派使用者至 Salesforce

1. 若要測試您的組態，請先在目錄中建立新的測試帳戶。

2. 在 Salesforce [快速入門] 頁面上，按一下 **指派使用者** ] 按鈕。

    ![按一下 [指派使用者]][27]

3. 選取測試使用者，然後按一下 [ **指派** 在畫面底部的按鈕:

 - 如果還沒有啟用自動的使用者佈建，您會看到下列確認提示：

        ![Confirm the assignment.][28]

 - 如果已啟用自動的使用者佈建，您會看到提示，定義使用者應具備何種類型的 Salesforce 設定檔。 幾分鐘之後，新佈建的使用者應該就會出現在 Salesforce 環境中。

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] If you are provisioning to a Salesforce **developer** environment, you will have a very limited number of licenses available for each profile. Therefore, it's best to provision users to the **Chatter Free User** profile, which has 4,999 licenses available.

4. 若要測試您的單一登入設定，請開啟 [存取面板] [https://myapps.microsoft.com](https://myapps.microsoft.com/), 、 登入測試帳戶，然後按一下 **Salesforce**。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
