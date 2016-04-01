## <a name="register-mobile-service-aad"></a>向 Azure Active Directory 註冊您的行動服務


在本節中，您將向 Azure Active Directory 註冊您的行動服務，並設定權限以允許單一登入模擬。

1. 向 Azure Active Directory 註冊應用程式，依照 [How to Register with the Azure Active Directory] 主題。

2. 在 [Azure clasic 入口網站](https://manage.windowsazure.com/), 回到 Azure Active Directory 擴充功能，按一下您的 active directory

3. 按一下 [ **應用程式** 標籤，然後按一下您的應用程式。

4. 按一下 [ **管理資訊清單**。 然後按一下 [ **下載資訊清單** ，並將應用程式資訊清單儲存到本機目錄。

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. 在 Visual Studio 中開啟應用程式資訊清單。 在檔案的頂端尋找應用程式權限行，如下所示：

        "oauth2Permissions": [],

    使用下列應用程式權限來取代該行，並儲存檔案。

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **管理資訊清單** 做為應用程式會再次按一下 **上傳資訊清單**。  瀏覽到您剛剛更新的應用程式資訊清單位置，並上傳此資訊清單。

<!-- URLs. -->
[How to Register with the Azure Active Directory]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md


