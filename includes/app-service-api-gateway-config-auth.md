4. 瀏覽至您的 API 應用程式的閘道刀鋒視窗。

    ![按一下 [閘道]](./media/app-service-api-gateway-config-auth/gateway.png)

7. 在 **閘道** 刀鋒視窗中，按一下 [ **設定**, ，然後按一下 [ **識別**。

    ![按一下 [設定]](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

    ![按一下 [身分識別]](./media/app-service-api-gateway-config-auth/clickidentity.png)

    從 **識別** ] 刀鋒視窗中，您可以瀏覽到不同的分頁，以使用 Azure Active Directory 和數個其他提供者設定驗證。

    ![身分識別分頁](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. 選擇您要使用的身分識別提供者，並遵循對應文章中的步驟，透過該提供者來設定您的 API 應用程式。 這些文章是針對行動應用程式所撰寫的，但與 API 應用程式所用的程序相同。 有些程序需要您使用 [Azure 入口網站]。 

 - [Microsoft 帳戶](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md)
 - [Facebook 登入](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md)
 - [Twitter 登入](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md)
 - [Google 登入](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)

例如，下列螢幕擷取畫面顯示您在設定 Azure Active Directory 驗證之後，應在 [Azure 入口網站] 頁面和 [Azure Preview 入口網站] 刀鋒視窗中看到的內容。

在 Azure 預覽入口網站中， **Azure Active Directory** 分頁具有 **用戶端識別碼** 從您的 Azure 入口網站中，Azure Active Directory] 索引標籤中建立的應用程式和 **允許的租用戶** 擁有您的 Azure Active Directory 租用戶 (例如，"contoso.onmicrosoft.com")。

![Azure Active Directory 分頁](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

在 Azure 網站中， **設定** 您建立的應用程式] 索引標籤 **Azure Active Directory** ] 索引標籤有 **登入 URL**, ，**應用程式識別碼 URI**, ，和 **回覆 URL** 從 **Azure Active Directory** Azure 預覽入口網站中的分頁。

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(影像中的回覆 URL 會顯示相同 URL 兩次，一次搭配 `http:`，一次搭配 `https:`。)

