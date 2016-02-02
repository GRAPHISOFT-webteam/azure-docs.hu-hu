
1. 如果您尚未註冊您的應用程式，請瀏覽 [提交應用程式頁面]，Windows 市集應用程式之開發人員中心，使用您的 Microsoft 帳戶登入，然後按一下 **應用程式名稱**。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 在 **[應用程式名稱]** 中為您的應用程式輸入名稱，然後依序按一下 **[保留應用程式名稱]** 和 **[儲存]**。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

    This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，開啟您完成**開始使用行動服務**教學課程時所建立的 Windows 市集應用程式專案。

4. 在 [方案總管] 中，以滑鼠右鍵按一下此專案，然後依序按一下 [市集]**** 和 [將應用程式與市集建立關聯...]****。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

    這將會顯示 **[將您的應用程式與 Windows 市集建立關聯]** 精靈。

5. 在此精靈中，按一下 [登入]****，然後使用您的 Microsoft 帳戶登入。

6. 選取您在步驟 2 中註冊的應用程式，按 **[下一步]**，然後按一下 **[關聯]**。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

    這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。

7. (選用) 重複執行步驟 4-6，也會註冊通用 Windows 應用程式的 Windows Phone 市集專案。

8. 回到新應用程式的 Windows 開發人員中心頁面，按一下 **[服務]**。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. 在 [服務] 頁面中，按一下 [Azure 行動服務]**** 下的 [Live Services site]****。

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. 按一下 **[驗證您的服務]**，並記下 **[用戶端密碼]** 和 **[封裝安全性識別碼 (SID)]** 的值。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)
    > [AZURE.NOTE] 用戶端密碼和封裝 SID 是重要的安全性認證。 請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。 

11. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後按一下您的應用程式。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. 按一下 [推播]**** 索引標籤，輸入您在步驟 4 中從 WNS 取得的 [用戶端密碼]**** 和 [封裝 SID]**** 值，然後按一下 [儲存]****。

    ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)
    >[AZURE.NOTE]當您在入口網站的 [推播]**** 索引標籤中設定進階推播通知的 WNS 認證時，這些認證將會與通知中樞共用，以設定您應用程式適用的通知中樞。



[submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582 

